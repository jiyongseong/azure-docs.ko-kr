---
title: 빠른 시작 - Azure CLI를 사용하여 Azure에서 개인 Docker 레지스트리 만들기
description: Azure CLI를 사용한 개인 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다.
services: container-registry
author: neilpeterson
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: ed5c2d0b925d92f318b36b36d060ebc923499ee5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 컨테이너 레지스트리 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하는 데 사용되는 관리되는 Docker 컨테이너 레지스트리 서비스입니다. 이 가이드는 Azure CLI를 사용하여 Azure Container Registry 인스턴스 만들기, 컨테이너 이미지를 레지스트리로 푸시, 마지막으로 레지스트리의 컨테이너에서 ACI(Azure Container Instances)로 배포를 설명합니다.

이 빠른 시작에서는 Azure CLI 버전 2.0.27 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치하거나 업그레이드해야 하는 경우 [Azure CLI 2.0 설치][azure-cli]를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [Mac][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

이 빠른 시작에서는 *Basic* 레지스트리를 만듭니다. Azure Container Registry는 다음 표에 간략하게 설명된 몇 개의 다른 SKU에서 사용할 수 있습니다. 각각에 대해 확장된 세부 정보를 보려면 [컨테이너 레지스트리 SKU][container-registry-skus]를 참조하세요.

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

[az acr create][az-acr-create] 명령을 사용하여 ACR 인스턴스를 만듭니다.

레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 다음 예제에서는 *myContainerRegistry007*을 사용합니다. 이를 고유한 값으로 업데이트합니다.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

레지스트리를 만들면 출력은 다음과 비슷합니다.

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
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

이 빠른 시작의 나머지 부분에서는 컨테이너 레지스트리 이름에 자리 표시자로 `<acrName>`을 사용합니다.

## <a name="log-in-to-acr"></a>ACR에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 이렇게 하려면 [az acr login][az-acr-login] 명령을 사용합니다.

```azurecli
az acr login --name <acrName>
```

완료되면 이 명령은 `Login Succeeded` 메시지를 반환합니다.

## <a name="push-image-to-acr"></a>ACR에 이미지 푸시

Azure Container Registry에 이미지를 푸시하려면 먼저 이미지가 있어야 합니다. 로컬 컨테이너 이미지가 아직 없는 경우 Docker 허브에서 기존 이미지를 가져오는 다음 명령을 실행합니다.

```bash
docker pull microsoft/aci-helloworld
```

레지스트리에 이미지를 푸시하기 전에 ACR 로그인 서버의 정규화된 이름을 사용하여 태그를 지정해야 합니다. 다음 명령을 실행하여 ACR 인스턴스의 전체 로그인 서버 이름을 가져옵니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] 명령을 사용하여 이미지에 태그를 지정합니다. `<acrLoginServer>`를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

마지막으로 [docker push][docker-push]를 사용하여 ACR 인스턴스로 이미지를 푸시합니다. `<acrLoginServer>`를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>컨테이너 이미지 나열

다음 예제는 레지스트리의 리포지토리를 나열합니다.

```azurecli
az acr repository list --name <acrName> --output table
```

출력

```bash
Result
----------------
aci-helloworld
```

다음 예제는 **aci-helloworld** 리포지토리에서의 태그를 나열합니다.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

출력

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>ACI에 이미지 배포

만든 레지스트리에서 컨테이너 인스턴스를 배포하기 위해 배포할 때 레지스트리 자격 증명을 제공해야 합니다. 프로덕션 시나리오에서 컨테이너 레지스트리에 대한 [서비스 사용자][container-registry-auth-aci]를 사용해야 하지만 이 빠른 시작을 간단하게 하기 위해 다음 명령을 사용하여 레지스트리에서 관리 사용자를 활성화합니다.

```azurecli
az acr update --name <acrName> --admin-enabled true
```

관리자가 활성화되면 사용자 이름은 레지스트리 이름과 동일하며 이 명령을 사용하여 암호를 검색할 수 있습니다.

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

1 CPU 코어 및 1GB의 메모리를 사용하여 컨테이너 이미지를 배포하려면 다음 명령을 실행합니다. `<acrName>`, `<acrLoginServer>` 및 `<acrPassword>`를 이전 명령에서 얻은 값으로 바꿉니다.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Azure Resource Manager에서 컨테이너의 세부 정보와 함께 초기 응답을 다시 가져와야 합니다. 컨테이너의 상태를 모니터링하고 실행될 때 검사하고 확인하려면 [az container show][az-container-show]를 반복합니다. 이 설치는 1분 이내에 완료되어야 합니다.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>응용 프로그램 보기

ACI에 대한 배포가 성공하면 [az container show][az-container-show] 명령으로 컨테이너의 FQDN을 가져옵니다.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

예제 출력: `"aci-demo.eastus.azurecontainer.io"`

실행 중인 응용 프로그램을 보려면 원하는 브라우저에서 공용 IP 주소로 이동합니다.

![브라우저의 Hello World 앱][aci-app-browser]

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, ACR 인스턴스 및 모든 컨테이너 이미지를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들고, 컨테이너 이미지를 레지스트리로 푸시하고, Azure Container Instances를 통해 해당 인스턴스를 시작했습니다. ACI에 대해 자세히 알아보기 위해 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az_container_show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md