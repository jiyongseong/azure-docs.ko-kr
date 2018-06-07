---
title: 빠른 시작 - Azure Portal을 사용하여 Azure에서 개인 Docker 레지스트리 만들기
description: Azure Portal을 사용한 개인 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 55da52e0d314c353c669c56ad918c4dd6bef44c3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-container-registry-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 컨테이너 레지스트리 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하고 관리할 수 있는 Azure의 개인 Docker 레지스트리입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 컨테이너 레지스트리를 만들고, 컨테이너 이미지를 레지스트리로 푸시하고, 마지막으로 레지스트리의 컨테이너를 ACI(Azure Container Instances)로 배포합니다.

이 빠른 시작을 완료하려면 Docker를 로컬로 설치해야 합니다. Docker는 모든 [Mac][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

**리소스 만들기** > **컨테이너** > **Azure Container Registry**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-01]

**레지스트리 이름** 및 **리소스 그룹**에 대한 값을 입력합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. `myResourceGroup`이라는 새 리소스 그룹을 만들고, **SKU**에 ‘Basic’을 선택합니다. **만들기**를 선택하여 ACR 인스턴스를 배포합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-03]

이 빠른 시작에서는 *Basic* 레지스트리를 만듭니다. Azure Container Registry는 다음 표에 간략하게 설명된 몇 개의 다른 SKU에서 사용할 수 있습니다. 각각에 대해 확장된 세부 정보를 보려면 [컨테이너 레지스트리 SKU][container-registry-skus]를 참조하세요.

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

**배포 성공** 메시지가 표시되면 포털에서 컨테이너 레지스트리를 선택한 다음, **선택키**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-05]

**관리 사용자**에서 **사용**을 선택합니다. 다음 값을 기록해 둡니다.

* 로그인 서버
* 사용자 이름
* 암호

Docker CLI를 사용하여 사용자 레지스트리를 사용하는 동안 다음 단계에서 이러한 값을 사용합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-06]

## <a name="log-in-to-acr"></a>ACR에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 이렇게 하려면 [docker login][docker-login] 명령을 사용합니다. *username*, *password* 및 *login server* 값을 이전 단계에서 적어 둔 값으로 바꿉니다.

```bash
docker login --username <username> --password <password> <login server>
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다. `--password-stdin` 매개 변수를 사용하도록 권장하는 보안 경고가 표시될 수 있습니다. 이 문서의 범위 외부에서 사용하는 경우 이 모범 사례를 따르는 것이 좋습니다. 자세한 내용은 [docker login][docker-login] 명령 참조를 참조하세요.

## <a name="push-image-to-acr"></a>ACR에 이미지 푸시

Azure Container Registry에 이미지를 푸시하려면 먼저 이미지가 있어야 합니다. 필요한 경우에 다음 명령을 실행하여 기존 이미지를 Docker Hub에서 끌어옵니다.

```bash
docker pull microsoft/aci-helloworld
```

레지스트리에 이미지를 푸시하려면 먼저 ACR 로그인 서버 이름으로 이미지에 태그를 지정해야 합니다. [docker tag][docker-tag] 명령을 사용하여 이미지에 태그를 지정합니다. *login server*를 이전에 기록해 둔 로그인 서버 이름으로 바꿉니다.

```bash
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

마지막으로 [docker push][docker-push]를 사용하여 ACR 인스턴스로 이미지를 푸시합니다. *login server*를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker push <login server>/aci-helloworld:v1
```

성공적인 `docker push` 명령은 다음과 유사하게 출력됩니다.

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>컨테이너 이미지 나열

ACR 인스턴스의 이미지를 나열하려면 포털에서 레지스트리로 이동하여 **리포지토리**를 선택한 다음, `docker push`를 사용하여 만든 리포지토리를 선택합니다.

이 예제에서 **aci-helloworld** 리포지토리를 선택하면 **태그**에 `v1` 태그가 지정된 이미지가 표시됩니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-09]

## <a name="deploy-image-to-aci"></a>ACI에 이미지 배포

레지스트리의 인스턴스로 배포하기 위해 리포지토리(aci-helloworld)로 이동한 다음, v1 옆의 줄임표를 클릭해야 합니다.

![포털에서 Azure 컨테이너 인스턴스 시작][qs-portal-10]

바로 가기 메뉴가 표시되면 **인스턴스 실행**을 선택합니다.

![ACI 바로 가기 메뉴 시작][qs-portal-11]

**컨테이너 이름**을 입력하고, 올바른 구독이 선택되었는지 확인하고, 기존 **리소스 그룹**: "myResourceGroup"을 선택한 다음, **확인**을 클릭하여 Azure 컨테이너 인스턴스를 시작합니다.

![ACI 배포 옵션 시작][qs-portal-12]

배포가 시작되면 포털 대시보드에 배포 진행 상황을 나타내는 타일이 배치됩니다. 배포가 완료되면 이 타일이 업데이트되어 새 **mycontainer** 컨테이너 그룹을 표시합니다.

![ACI 배포 상태][qs-portal-13]

mycontainer 컨테이너 그룹을 선택하여 컨테이너 그룹 속성을 표시합니다. 컨테이너 그룹의 **IP 주소**와 컨테이너의 **상태**를 기록해 둡니다.

![ACI 컨테이너 세부 정보][qs-portal-14]

## <a name="view-the-application"></a>응용 프로그램 보기

컨테이너가 **실행** 상태가 되면 선호하는 브라우저를 사용하여 이전 단계에서 기록해 둔 IP 주소로 이동하여 응용 프로그램을 표시합니다.

![브라우저의 Hello World 앱][qs-portal-15]

## <a name="clean-up-resources"></a>리소스 정리

리소스를 정리하려면 포털에서 **myResourceGroup** 리소스 그룹으로 이동합니다. 리소스 그룹이 로드되면 **리소스 그룹 삭제**를 클릭하여 리소스 그룹, Azure Container Registry 및 모든 Azure Container Instances를 제거합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-08]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들고, Azure Container Instances를 통해 해당 인스턴스를 시작했습니다. ACI에 대해 자세히 알아보기 위해 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
