---
title: Azure Container Service 자습서 - 응용 프로그램 크기 조정
description: Azure Container Service 자습서 - 응용 프로그램 크기 조정
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f643c09f00b23cd14e85e83ed0cf7ab7a13c7646
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Kubernetes Pod 및 Kubernetes 인프라 크기 조정

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

자습서를 수행하고 있다면 Azure Container Service에서 작동하는 Kubernetes 클러스터가 있으며 Azure Voting 앱을 배포한 상태입니다. 

7개 중 5단계인 이 자습서에서는 앱의 Pod를 스케일 아웃하고 Pod 자동 크기 조정을 시도합니다. Azure VM 에이전트 노드 수를 조정하여 워크로드 호스팅을 위한 클러스터 용량을 변경하는 방법도 알아봅니다. 완료된 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 수동으로 Kubernetes Pod 크기 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성
> * Kubernetes Azure 에이전트 노드 크기 조정

후속 자습서에서는 Azure Vote 응용 프로그램을 업데이트하고, Kubernetes 클러스터를 모니터링하도록 Log Analytics를 구성합니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, 이 이미지를 Azure Container Registry에 업로드하고, Kubernetes 클러스터를 만들었습니다. 그런 다음 Kubernetes 클러스터에서 응용 프로그램을 실행했습니다. 

이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

## <a name="manually-scale-pods"></a>수동으로 Pod 크기 조정

따라서 Azure Vote 프런트 엔드 및 Redis 인스턴스가 각각 단일 복제본으로 배포되었습니다. 확인하려면 [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 실행합니다.

```azurecli-interactive
kubectl get pods
```

출력

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

[kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) 명령을 사용하여 수동으로 `azure-vote-front` 배포의 Pod 수를 변경합니다. 이 예제에서는 수를 5로 늘립니다.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

[kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get)를 실행하여 Kubernetes에서 Pod를 만들고 있는지 확인합니다. 1분 정도 지나면 추가 Pod가 실행되고 있습니다.

```azurecli-interactive
kubectl get pods
```

출력

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Pod 자동 크기 조정

Kubernetes는 [수평 Pod 자동 크기 조정](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)을 지원하여 CPU 사용률 또는 다른 선택 메트릭에 따라 배포에서 Pod 수를 조정할 수 있게 해줍니다. 

자동 크기 조정기를 사용하려면 Pod에 CPU 요청 및 제한이 정의되어 있어야 합니다. `azure-vote-front` 배포에서 프런트 엔드 컨테이너는 0.25 CPU를 요청하며 제한은 0.5 CPU입니다. 설정은 다음과 같습니다.

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

다음 예제에서는 [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) 명령을 사용하여 `azure-vote-front` 배포의 Pod 수를 자동으로 조정합니다. 여기서는 CPU 사용률이 50%를 초과하면 자동 크기 조정기가 Pod를 최대 10개로 늘립니다.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

자동 조정기의 상태를 확인하려면 다음 명령을 실행합니다.

```azurecli-interactive
kubectl get hpa
```

출력

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure Vote 앱에 최소 부하를 적용한 상태로 몇 분이 지나면 Pod 복제본 수가 자동으로 3개로 줄어듭니다.

## <a name="scale-the-agents"></a>에이전트 크기 조정

이전 자습서에서 기본 명령을 사용하여 Kubernetes 클러스터를 만든 경우 이 클러스터에는 세 개의 에이전트 노드가 있습니다. 클러스터에 대한 컨테이너 워크로드를 더 늘리거나 줄일 계획인 경우 수동으로 에이전트 수를 조정할 수 있습니다. [az acs scale](/cli/azure/acs#az_acs_scale) 명령을 사용하고 `--new-agent-count` 매개 변수로 에이전트 수를 지정합니다.

다음 예제에서는 *myK8sCluster*라는 Kubernetes 클러스터의 에이전트 노드 수를 4개로 늘립니다. 이 명령은 완료되는 데 2~3분이 걸립니다.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

명령 출력에는 에이전트 노드 수가 `agentPoolProfiles:count` 값으로 표시됩니다.

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Kubernetes 클러스터의 다양한 크기 조정 기능을 사용했습니다. 설명한 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 수동으로 Kubernetes Pod 크기 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성
> * Kubernetes Azure 에이전트 노드 크기 조정

다음 자습서로 이동하여 Kubernetes에서 응용 프로그램 업데이트에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes에서 응용 프로그램 업데이트](./container-service-tutorial-kubernetes-app-update.md)

