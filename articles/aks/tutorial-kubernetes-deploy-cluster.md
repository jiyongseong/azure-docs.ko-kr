---
title: Azure의 Kubernertes 자습서 - 클러스터 배포
description: AKS 자습서 - 클러스터 배포
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e4e4e7c3956f396024513a4c48722dfb86e89151
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>자습서: AKS(Azure Kubernetes Service) 클러스터 배포

Kubernetes는 컨테이너화된 응용 프로그램용 분산 플랫폼을 제공합니다. AKS를 통해 프로덕션이 준비된 Kubernetes 클러스터를 간단하고 빠르게 프로비저닝할 수 있습니다. 총 8부 중 3부인 이 자습서에서는 Kubernetes 클러스터가 AKS에 배포됩니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Kubernetes AKS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * kubectl 구성

후속 자습서에서는 Azure 투표 응용 프로그램을 클러스터에 배포하고 확장/업데이트하며, Kubernetes 클러스터를 모니터링하도록 Log Analytics를 구성합니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 컨테이너 이미지를 만들어 Azure Container Registry 인스턴스에 업로드했습니다. 이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 돌아갑니다.

## <a name="enable-aks-preview"></a>AKS 미리 보기 사용

AKS가 미리 보기 상태인 경우 새 클러스터를 만들려면 구독에서 기능 플래그가 필요합니다. 사용하려는 구독의 수에 관계 없이 이 기능을 요청할 수 있습니다. `az provider register` 명령을 사용하여 AKS 공급자를 등록합니다.

```azurecli
az provider register -n Microsoft.ContainerService
```

등록하면 이제 AKS를 사용하여 Kubernetes 클러스터를 만들 준비가 되었습니다.

## <a name="create-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

다음 예제에서는 `myResourceGroup` 리소스 그룹에 `myAKSCluster` 클러스터를 만듭니다. 이 리소스 그룹은 [이전 자습서][aks-tutorial-prepare-acr]에서 만든 것입니다.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

몇 분 후 배포가 완료되고 json 형식의 AKS 배포 관련 정보가 반환됩니다.

## <a name="install-the-kubectl-cli"></a>kubectl CLI 설치

클라이언트 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다.

Azure Cloud Shell을 사용하는 경우 kubectl이 이미 설치되어 있습니다. 로컬로 설치하려면 다음 명령을 실행합니다.

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Kubectl로 연결

Kubernetes 클러스터에 연결하도록 kubectl을 구성하려면 다음 명령을 실행합니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get nodes][kubectl-get] 명령을 실행합니다.

```azurecli
kubectl get nodes
```

출력

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

이 자습서를 마치면 AKS 클러스터가 워크로드에 대해 준비됩니다. 이후 자습서에서는 다중 컨테이너 응용 프로그램이 이 클러스터에 배포, 규모 확장, 업데이트 및 모니터링됩니다.

## <a name="configure-acr-authentication"></a>ACR 인증 구성

인증은 AKS 클러스터와 ACR 레지스트리 간에 구성되어야 합니다. 여기에는 ACR 레지스트리에서 이미지를 가져오도록 AKS ID에 적절한 권한을 부여하는 작업이 포함됩니다.

먼저 AKS에 구성된 서비스 주체의 ID를 가져옵니다. 리소스 그룹 이름 및 AKS 클러스터 이름을 환경에 맞게 업데이트합니다.

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

ACR 레지스트리 리소스 ID를 가져옵니다. 레지스트리 이름을 ACR 레지스트리의 이름으로 업데이트하고 리소스 그룹을 ACR 레지스트리가 있는 리소스 그룹으로 업데이트합니다.

```azurecli
ACR_ID=$(az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv)
```

적절한 액세스 권한을 부여하는 역할 할당을 만듭니다.

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Kubernetes 클러스터가 AKS에 배포되었습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Kubernetes AKS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * kubectl 구성

다음 자습서로 이동하여 클러스터에서 응용 프로그램 실행에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes에서 응용 프로그램 배포][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
