---
title: Azure Container Service 자습서 - ACR 준비
description: Azure Container Service 자습서 - ACR 준비
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f58a8d76cc46ac25474c7b91e464974612876a06
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry 배포 및 사용

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

ACR(Azure Container Registry)은 Docker 컨테이너 이미지를 위한 Azure 기반의 개인 레지스트리입니다. 일곱 가지 중에 두 번째인 이 자습서에서는 Azure Container Registry 인스턴스를 배포하고 컨테이너 이미지를 이 인스턴스에 밀어넣는 과정을 안내합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * ACR(Azure Container Registry) 인스턴스 배포
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드

이후 자습서에서는 이 ACR 인스턴스를 Azure Container Service Kubernetes 클러스터와 통합합니다. 

## <a name="before-you-begin"></a>시작하기 전에

[이전 자습서](./container-service-tutorial-kubernetes-prepare-app.md)에서는 간단한 Azure Voting 응용 프로그램에 컨테이너 이미지를 만들었습니다. Azure Voting 앱 이미지를 만들지 않은 경우 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다.

이 자습서에는 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="deploy-azure-container-registry"></a>Azure Container Registry 배포

Azure Container Registry를 배포할 때는 먼저 리소스 그룹이 필요합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 `westeurope` 하위 지역에 `myResourceGroup`이라는 리소스 그룹이 만들어집니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

[az acr create](/cli/azure/acr#az_acr_create) 명령으로 Azure Container Registry를 만듭니다. 컨테이너 레지스트리의 이름은 **고유해야 합니다**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

이 자습서의 나머지 부분에서는 선택한 컨테이너 레지스트리 이름의 자리 표시자로 `<acrname>`을 사용합니다.

## <a name="container-registry-login"></a>컨테이너 레지스트리 로그인

[az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) 명령을 사용하여 ACR 인스턴스에 로그인합니다. 컨테이너 레지스트리가 생성될 때 지정된 고유한 이름을 제공해야 합니다.

```azurecli
az acr login --name <acrName>
```

이 명령은 완료되면 ‘로그인했습니다.’ 메시지를 반환합니다.

## <a name="tag-container-images"></a>컨테이너 이미지 태그 지정

현재 이미지 목록을 보려면 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용합니다.

```bash
docker images
```

출력

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

각 컨테이너 이미지에 레지스트리의 loginServer 이름으로 태그를 지정해야 합니다. 이 태그는 컨테이너 이미지를 이미지 레지스트리에 밀어넣을 때 라우팅에 사용됩니다.

loginServer 이름을 가져오려면 다음 명령을 실행합니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

이제 `azure-vote-front` 이미지에 컨테이너 레지스트리의 loginServer로 태그를 지정합니다. 또한 이미지 이름 끝에 `:v1`을 추가합니다. 이 태그는 이미지 버전을 나타냅니다.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

태그가 지정되면 [docker images](https://docs.docker.com/engine/reference/commandline/images/)를 실행하여 작업을 확인합니다.

```bash
docker images
```

출력

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>레지스트리에 이미지 푸시

레지스트리에 `azure-vote-front` 이미지를 푸시합니다. 

다음 예제를 사용하여 ACR loginServer 이름을 해당 환경의 loginServer로 바꿉니다.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

이 작업은 완료되는 데 2~3분이 걸립니다.

## <a name="list-images-in-registry"></a>레지스트리에서 이미지 나열

Azure Container Registry에 밀어넣은 이미지 목록을 반환하려면 [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) 명령을 사용합니다. ACR 인스턴스 이름으로 명령을 업데이트합니다.

```azurecli
az acr repository list --name <acrName> --output table
```

출력

```azurecli
Result
----------------
azure-vote-front
```

그런 다음 특정 이미지에 대한 태그를 보려면 [az acr repository show-tags](/cli/azure/acr/repository#show-tags) 명령을 사용합니다.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

출력

```azurecli
Result
--------
v1
```

자습서를 완료하면 개인 Azure Container Registry 인스턴스에 컨테이너 이미지가 저장됩니다. 이후 자습서에서 이 이미지는 ACR에서 Kubernetes 클러스터로 배포됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ACS Kubernetes 클러스터에서 사용하기 위해 Azure Container Registry를 준비했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure Container Registry 인스턴스 배포
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드

다음 자습서로 이동하여 Azure에서 Kubernetes 클러스터 배포에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes 클러스터 배포](./container-service-tutorial-kubernetes-deploy-cluster.md)