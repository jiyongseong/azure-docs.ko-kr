---
title: Azure Container Instances 자습서 - Azure Container Registry 준비
description: Azure Container Instances 자습서 2/3부 - Azure Container Registry 준비
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: a23e6af506b503a666837f01665e2cad9d1ec5b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>자습서: Azure Container Registry 배포 및 사용

세 부분으로 이루어진 자습서의 두 번째 부분입니다. 자습서의 [1부](container-instances-tutorial-prepare-app.md)에서는 Node.js 웹 응용 프로그램에 사용할 Docker 컨테이너 이미지를 만들었습니다. 이 자습서에서는 이 이미지를 Azure Container Registry에 푸시합니다. 컨테이너 이미지를 만들지 않은 경우 [자습서 1 - 컨테이너 이미지 만들기](container-instances-tutorial-prepare-app.md)로 돌아갑니다.

Azure Container Registry는 Azure의 개인 Docker 레지스트리입니다. 이 자습서에서는 구독에서 Azure Container Registry 인스턴스를 만든 후 이전에 만든 컨테이너 이미지를 여기에 푸시합니다. 시리즈의 2부에 해당하는 본 문서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure Container Registry 인스턴스 만들기
> * Azure Container Registry에 컨테이너 이미지 태그 지정
> * 레지스트리에 이미지 업로드

시리즈의 마지막 과정인 그 다음 문서에서는 Azure Container Instances에 개인 레지스트리의 컨테이너를 배포합니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Azure 컨테이너 레지스트리 만들기

컨테이너 레지스트리를 만들려면 컨테이너 레지스트리를 배포할 *리소스 그룹*이 필요합니다. 리소스 그룹은 모든 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

리소스 그룹을 만든 후에는 [az acr create][az-acr-create] 명령을 사용하여 Azure 컨테이너 레지스트리를 만듭니다. 컨테이너 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. `<acrName>`를 레지스트리의 고유한 이름으로 바꿉니다.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

다음은 *mycontainerregistry082*라는 새 Azure 컨테이너 레지스트리의 출력 예제입니다(여기에 잘려서 표시).

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

이 자습서의 나머지 부분에서는 `<acrName>`을 이 단계에서 선택하는 컨테이너 레지스트리 이름의 자리 표시자로 참조합니다.

## <a name="log-in-to-container-registry"></a>컨테이너 레지스트리에 로그인

Azure Container Registry 인스턴스에 로그인해야 이미지를 푸시할 수 있습니다. [az acr login][az-acr-login] 명령을 사용하여 작업을 완료합니다. 컨테이너 레지스트리를 만들 때 선택한 고유의 이름을 입력해야 합니다.

```azurecli
az acr login --name <acrName>
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다.

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>컨테이너 이미지 태그 지정

컨테이너 이미지를 Azure Container Registry 같은 개인 레지스트리에 푸시하려면 먼저 레지스트리 로그인 서버의 전체 이름을 사용하여 이미지에 태그를 지정해야 합니다.

먼저 Azure 컨테이너 레지스트리의 전체 로그인 서버 이름을 가져옵니다. [az acr show][az-acr-show] 명령을 실행하고, `<acrName>`을 방금 만든 레지스트리 이름으로 바꿉니다.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

예를 들어 레지스트리 이름이 *mycontainerregistry082*인 경우:

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

이제 [docker images][docker-images] 명령을 사용하여 로컬 이미지 목록을 표시합니다.

```bash
docker images
```

컴퓨터에 있는 다른 이미지와 함께, [이전 자습서](container-instances-tutorial-prepare-app.md)에서 작성한 *aci-tutorial-app* 이미지가 보일 것입니다.

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

*aci-tutorial-app* 이미지에 컨테이너 레지스트리의 loginServer로 태그를 지정합니다. 또한 이미지 이름 끝에 이미지 버전 번호를 나타내는 `:v1` 태그를 추가합니다. `<acrLoginServer>`를 앞에서 실행한 [az acr show][az-acr-show] 명령의 결과로 바꿉니다.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

`docker images` 명령을 다시 실행하여 태그 지정 작업을 확인합니다.

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry에 이미지 푸시하기

개인 레지스트리의 전체 로그인 서버 이름으로 *aci-tutorial-app* 이미지에 태그를 지정했으니, [docker push][docker-push] 명령을 사용하여 이미지를 레지스트리에 푸시할 수 있습니다. `<acrLoginServer>`를 이전 단계에서 얻은 전체 로그인 서버 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push` 작업은 인터넷 연결에 따라 몇 초에서 몇 분 정도 걸리고 출력은 다음과 유사합니다.

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Azure Container Registry에서 이미지 나열

방금 푸시한 이미지가 실제로 Azure 컨테이너 레지스트리에 있는지 확인하기 위해 [az acr repository list][az-acr-repository-list] 명령을 사용하여 레지스트리의 이미지를 나열합니다. `<acrName>`을 컨테이너 레지스트리의 이름으로 바꿉니다.

```azurecli
az acr repository list --name <acrName> --output table
```

예: 

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

특정 이미지의 *태그*를 보려면 [az acr repository show-tags][az-acr-repository-show-tags] 명령을 사용합니다.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

다음과 비슷한 결과가 나타나야 합니다.

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Instances와 함께 사용할 Azure 컨테이너 레지스트리를 준비하고, 컨테이너 이미지를 레지스트리에 푸시했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure Container Registry 인스턴스 배포
> * Azure Container Registry에 컨테이너 이미지 태그 지정
> * Azure Container Registry에 이미지 업로드

다음 자습서로 넘어가서 Azure Container Instances를 사용하여 컨테이너를 Azure에 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Instances에 컨테이너 배포](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
