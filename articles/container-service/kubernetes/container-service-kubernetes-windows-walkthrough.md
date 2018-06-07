---
title: 빠른 시작 - Windows용 Azure Kubernetes 클러스터
description: Azure Container Service에서 Azure CLI를 사용하여 Windows 컨테이너용 Kubernetes 클러스터를 빠르게 만드는 방법에 대해 알아봅니다.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 74ce913548fbcefdc441d0d2b772c864dacd4482
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-kubernetes-cluster-for-windows-containers"></a>Windows 컨테이너용 Kubernetes 클러스터 배포

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 [Azure Container Service](../container-service-intro.md)에서 [Kubernetes](https://kubernetes.io/docs/home/) 클러스터를 배포하는 방법에 대해 자세히 설명합니다. 클러스터가 배포되면 Kubernetes `kubectl` 명령줄 도구를 사용하여 해당 클러스터에 연결하고 첫 번째 Windows 컨테이너를 배포합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

> [!NOTE]
> Azure Container Service에서 Kubernetes의 Windows 컨테이너에 대한 지원은 미리 보기로 제공됩니다. 
>

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Kubernetes 클러스터 만들기
[az acs create](/cli/azure/acs#az_acs_create) 명령을 사용하여 Azure Container Service에서 Kubernetes 클러스터를 만듭니다. 

다음 예제에서는 하나의 Linux 마스터 노드와 두 개의 Windows 에이전트 노드가 있는 *myK8sCluster* 클러스터를 만들고, Linux 마스터에 연결하는 데 필요한 SSH 키를 만듭니다. 이 예제에서는 Windows 노드에 대해 관리 사용자 이름으로 *azureuser*, 암호로 *myPassword12*를 사용합니다. 이러한 값을 사용자 환경에 적절한 값으로 업데이트합니다. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

몇 분 후 명령이 완료되면 배포에 대한 정보가 표시됩니다.

## <a name="install-kubectl"></a>kubectl 설치

클라이언트 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)을 사용합니다. 

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 로컬로 설치하려면 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 명령을 사용하면 됩니다.

다음 Azure CLI 예제에서는 `kubectl`을 시스템에 설치합니다. Windows에서 이 명령을 관리자 권한으로 실행합니다.

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>Kubectl로 연결

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 명령을 실행합니다. 다음 예제에서는 Kubernetes 클러스터에 대한 클러스터 구성을 다운로드합니다.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

시스템에서 클러스터에 대한 연결을 확인하려면 다음을 실행합니다.

```azurecli-interactive
kubectl get nodes
```

`kubectl`에서 마스터 및 에이전트 노드를 나열합니다.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Windows IIS 컨테이너 배포

하나 이상의 컨테이너가 포함된 Kubernetes *Pod* 내부에서 Docker 컨테이너를 실행할 수 있습니다. 

이 기본 예제에서는 JSON 파일을 사용하여 Microsoft IIS(Internet Information Server) 컨테이너를 지정한 다음 `kubctl apply` 명령을 사용하여 Pod를 만듭니다. 

`iis.json`이라는 로컬 파일을 만들고 다음 텍스트를 복사합니다. 이 파일은 Kubernetes에게 [Docker 허브](https://hub.docker.com/r/microsoft/iis/)의 공용 컨테이너 이미지를 사용하여 Windows Server 2016 Nano Server에서 IIS를 실행하도록 지시합니다. 컨테이너는 포트 80을 사용하지만, 처음에 클러스터 네트워크 내에서만 액세스할 수 있습니다.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Pod를 시작하려면 다음을 입력합니다.
  
```azurecli-interactive
kubectl apply -f iis.json
```  

배포를 추적하려면 다음을 입력합니다.
  
```azurecli-interactive
kubectl get pods
```

Pod를 배포하는 동안 상태는 `ContainerCreating`입니다. 컨테이너가 `Running` 상태가 되는 데 몇 분이 걸릴 수 있습니다.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

공용 IP 주소로 Pod를 전 세계에 공개하려면 다음 명령을 입력합니다.

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

이 명령을 사용하면 Kubernetes에서 해당 공용 IP 주소를 사용하여 서비스 및 Azure Load Balancer 규칙을 만듭니다. 

다음 명령을 실행하여 서비스의 상태를 확인합니다.

```azurecli-interactive
kubectl get svc
```

처음에는 IP 주소가 `pending`으로 표시됩니다. 몇 분 후에 다음과 같이 `iis` Pod의 외부 IP 주소가 설정됩니다.
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

선택한 웹 브라우저를 사용하여 외부 IP 주소의 기본 IIS 시작 페이지를 볼 수 있습니다.

![IIS로 이동하는 이미지](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>클러스터 삭제
클러스터가 더 이상 필요하지 않으면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Kubernetes 클러스터를 배포하고, `kubectl`로 연결하고, IIS 컨테이너가 있는 Pod를 배포했습니다. Azure Container Service에 대해 자세히 알아보려면 Kubernetes 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [ACS Kubernetes 클러스터 관리](container-service-tutorial-kubernetes-prepare-app.md)
