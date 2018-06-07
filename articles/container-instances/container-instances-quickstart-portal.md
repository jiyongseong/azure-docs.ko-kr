---
title: 퀵 스타트 - Azure Portal로 첫 번째 Azure Container Instances 컨테이너 만들기
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Container Instances에 컨테이너를 배포합니다
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>빠른 시작: Azure Container Instances에서 첫 번째 컨테이너 만들기

Azure Container Instances를 사용하면 가상 머신을 프로비전하거나 상위 수준 서비스를 도입하지 않고도 Azure에서 Docker 컨테이너를 쉽게 만들고 관리할 수 있습니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure에서 컨테이너를 만들고 FQDN(정규화된 도메인 이름)으로 인터넷에 노출합니다. 몇 가지 설정을 구성하면 브라우저에서 다음과 같은 내용을 볼 수 있습니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][aci-portal-07]

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure Portal에 로그인합니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정][azure-free-account]을 만듭니다.

## <a name="create-a-container-instance"></a>컨테이너 인스턴스 만들기

**리소스 만들기** > **컨테이너** > **Container Instances**를 선택합니다.

![Azure Portal에서 새 컨테이너 인스턴스를 만들기 시작][aci-portal-01]

**컨테이너 이름**, **컨테이너 이미지** 및 **리소스 그룹** 텍스트 상자에 다음 값을 입력합니다. 다른 값은 기본값으로 두고 **확인**을 선택합니다.

* 컨테이너 이름: `mycontainer`
* 컨테이너 이미지: `microsoft/aci-helloworld`
* 리소스 그룹: `myResourceGroup`

![Azure Portal에서 새 컨테이너 인스턴스의 기본 설정 구성][aci-portal-03]

Azure Container Instances에서는 Windows 및 Linux 컨테이너를 모두 만들 수 있습니다. 이 빠른 시작에서는 **Linux**의 기본 설정을 사용하여 Linux 기반 `microsoft/aci-helloworld` 이미지를 배포하겠습니다.

**구성** 아래에서 컨테이너의 **DNS 이름 레이블**을 지정합니다. 이름은 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 컨테이너는 `<dns-name-label>.<region>.azurecontainer.io`에서 공개적으로 연결할 수 있습니다.

**구성**에서 다른 설정은 기본값으로 두고 **확인**을 선택하여 구성의 유효성 검사를 실행합니다.

![Azure Portal에서 새 컨테이너 인스턴스 구성][aci-portal-04]

유효성 검사가 완료되면 컨테이너 설정의 요약 정보가 표시됩니다. **확인**을 선택하여 컨테이너 배포 요청을 제출합니다.

![Azure Portal에서 새 컨테이너 인스턴스의 설정 요약][aci-portal-05]

배포가 시작되면 포털 대시보드에 배포 진행 상황을 나타내는 타일이 표시됩니다. 배포가 완료되면 타일에 새 컨테이너 인스턴스가 표시됩니다.

![Azure Portal에서 새 컨테이너 인스턴스를 만들기 진행 상황][aci-portal-08]

**mycontainer** 컨테이너 인스턴스를 선택하여 속성을 표시합니다. 컨테이너 인스턴스의 **FQDN**(정규화된 도메인 이름) 및 **상태**를 기록해 둡니다.

![Azure Portal의 컨테이너 그룹 개요][aci-portal-06]

**상태**가 *실행 중*이면 브라우저에서 컨테이너의 FQDN으로 이동합니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][aci-portal-07]

축하합니다! 몇 가지 설정을 구성하여 공개적으로 액세스 가능한 응용 프로그램을 Azure Container Instances에 배포했습니다.

## <a name="view-container-logs"></a>컨테이너 로그 보기

컨테이너 또는 컨테이너가 실행되는 응용 프로그램 문제를 해결할 때 컨테이너 인스턴스의 로그를 살펴보면 도움이 됩니다.

컨테이너의 로그를 보려면 **설정**에서 **컨테이너**, **로그**를 차례로 선택합니다. 브라우저에서 응용 프로그램을 살펴볼 때 생성된 HTTP GET 요청이 보일 것입니다.

![Azure Portal의 컨테이너 로그][aci-portal-11]

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 작업을 마친 후에는 *mycontainer* 컨테이너 인스턴스에 대한 **개요**를 선택한 다음, **삭제**를 선택합니다.

![Azure Portal에서 컨테이너 인스턴스 삭제][aci-portal-09]

확인 대화 상자가 나타나면 **예**를 선택합니다.

![Azure Portal에서 컨테이너 인스턴스의 삭제 확인][aci-portal-10]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 공용 Docker Hub 리포지토리의 이미지로 Azure 컨테이너 인스턴스를 만들었습니다. 컨테이너 이미지를 직접 빌드하고 개인 Azure 컨테이너 레지스트리의 Azure Container Instances에 배포하려면 Azure Container Instances 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/