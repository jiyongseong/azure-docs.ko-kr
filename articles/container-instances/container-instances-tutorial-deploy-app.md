---
title: Azure Container Instances 자습서 - 앱 배포
description: Azure Container Instances 자습서 3/3부 - 응용 프로그램 배포
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 2438914449ff609d149fca20f2f3756576877752
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>자습서: Azure Container Instances에 컨테이너 배포

3부작 시리즈의 마지막 자습서입니다. 시리즈의 앞부분에서는 [컨테이너 이미지를 만들어](container-instances-tutorial-prepare-app.md) [Azure Container Registry에 푸시했습니다](container-instances-tutorial-prepare-acr.md). 이 문서에서는 Azure Container Instances에 컨테이너를 배포하여 이 시리즈를 완료합니다.

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Container Registry에서 Azure Container Instances에 컨테이너 배포
> * 브라우저에서 실행 중인 응용 프로그램 보기
> * 컨테이너의 로그 표시

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI를 사용하여 컨테이너 배포

이 섹션에서는 Azure CLI를 사용하여 [첫 번째 자습서](container-instances-tutorial-prepare-app.md)에서 작성되고 [두 번째 자습서](container-instances-tutorial-prepare-acr.md)에서 Azure Container Registry에 푸시된 이미지를 배포합니다. 계속 진행하려면 먼저 이러한 자습서를 완료해야 합니다.

### <a name="get-registry-credentials"></a>레지스트리 자격 증명 가져오기

[두 번째 자습서](container-instances-tutorial-prepare-acr.md) 만든 이미지처럼 개인 컨테이너 레지스트리에 호스트되는 이미지를 배포하는 경우 레지스트리 자격 증명을 제공해야 합니다.

먼저, 컨테이너 레지스트리 로그인 서버의 전체 이름을 가져옵니다(`<acrName>`을 레지스트리 이름으로 바꾸기).

```azurecli
az acr show --name <acrName> --query loginServer
```

다음으로, 컨테이너 레지스트리 암호를 가져옵니다.

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>컨테이너 배포

이제 [az container create][az-container-create] 명령을 사용하여 컨테이너를 배포합니다. `<acrLoginServer>` 및 `<acrPassword>`를 이전 두 개의 명령에서 얻은 값으로 바꿉니다. `<acrName>`을 컨테이너 레지스트리의 이름으로 바꿉니다.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. `--dns-name-label` 값은 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 명령을 실행한 결과 **DNS 이름 레이블** 오류 메시지가 표시되는 경우에는 이전 명령의 값을 수정합니다.

### <a name="verify-deployment-progress"></a>배포 진행률 확인

배포 상태를 확인하려면 [az container show][az-container-show] 명령을 사용합니다.

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

상태가 *보류 중*에서 *실행 중*으로 변경될 때까지 [az container show][az-container-show] 명령을 1분 미만으로 반복합니다. 컨테이너가 *실행 중* 상태가 되면 다음 단계를 진행합니다.

## <a name="view-the-application-and-container-logs"></a>응용 프로그램 및 컨테이너 로그 보기

배포에 성공하면 [az container show][az-container-show] 명령을 사용하여 컨테이너의 FQDN(정규화된 도메인 이름)을 표시합니다.

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

예: 
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

실행 중인 응용 프로그램을 보려면 원하는 브라우저에서 표시된 DNS 이름으로 이동합니다.

![브라우저의 Hello World 앱][aci-app-browser]

또한 컨테이너의 로그 출력을 볼 수 있습니다.

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

예제 출력:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 이 자습서 시리즈에서 만든 리소스가 필요하지 않은 경우 [az group delete][az-group-delete] 명령을 실행하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. 이 명령은 실행 중인 컨테이너뿐만 아니라 생성한 컨테이너 레지스트리 및 모든 관련 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Instances에 컨테이너를 배포하는 프로세스를 완료했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Container Registry의 컨테이너 배포
> * 브라우저에서 응용 프로그램 보기
> * 컨테이너 로그 보기

기본 사항을 알아보았으니, 컨테이너 그룹의 작동 방식 등 Azure Container Instances에 대해 자세히 알아보겠습니다.

> [!div class="nextstepaction"]
> [Azure Container Instances의 컨테이너 그룹](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
