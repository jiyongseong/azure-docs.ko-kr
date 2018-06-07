---
description: 포함 파일
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 552e93e9bd1b17c73fb1638fbae2ac30b051c261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
1. 브라우저에서 [Azure Marketplace image for Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)(Jenkins용 Azure Marketplace 이미지)를 엽니다.

1. **지금 가져오기**를 선택합니다.

    ![[지금 가져오기]를 선택하여 Jenkins Marketplace 이미지에 대한 설치 프로세스를 시작합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. 가격 정보와 사용 조건 정보를 검토한 후 **계속**을 선택합니다.

    ![Jenkins Marketplace 이미지 가격 및 사용 조건 정보입니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. **만들기**를 선택하여 Azure Portal에서 Jenkins 서버를 구성합니다. 

    ![Jenkins Marketplace 이미지를 설치합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. **기본 사항** 탭에서 다음 값을 지정합니다.

    - **이름** - `Jenkins`를 입력합니다.
    - **사용자 이름** - Jenkins가 실행 중인 가상 머신에 로그인할 때 사용할 사용자 이름을 입력합니다. 사용자 이름은 [특정 요구 사항](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)을 충족해야 합니다.
    - **인증 유형** - **SSH 공개 키**를 선택합니다.
    - **SSH 공개 키** - RSA 공개 키를 복사하여 단일 줄 형식(`ssh-rsa`으로 시작) 또는 여러 줄 PEM 형식으로 붙여넣습니다. Linux 및 macOS에서는 ssh-keygen을, Windows에서는 PuTTYGen을 사용하여 SSH 키를 생성할 수 있습니다. SSH 키 및 Azure에 대한 자세한 내용은 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](/azure/virtual-machines/linux/ssh-from-windows) 문서를 참조하세요.
    - **구독** - Jenkins를 설치할 Azure 구독을 선택합니다.
    - **리소스 그룹** - **새로 만들기**를 선택하고 Jenkins 설치를 구성하는 리소스 컬렉션의 논리 컨테이너 역할을 하는 리소스 그룹 이름을 입력합니다.
    - **위치** - **미국 동부**를 선택합니다.

    ![[기본] 탭에 Jenkins에 대한 인증 및 리소스 그룹 정보를 입력합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. **확인**을 선택하여 **추가 설정** 탭으로 이동합니다. 

1. **추가 설정** 탭에서 다음 값을 지정합니다.

    - **크기** - Jenkins 가상 머신에 적합한 크기 지정 옵션을 선택합니다.
    - **VM 디스크 유형** - HDD(하드 디스크 드라이브) 또는 SSD(반도체 드라이브)를 지정하여 가상 머신에 허용된 저장소 디스크 유형을 나타냅니다.
    - **가상 네트워크** - (선택 사항) **가상 네트워크**를 선택하여 기본 설정을 수정합니다.
    - **서브넷** - **서브넷**을 선택하고, 정보를 확인한 다음, **확인**을 선택합니다.
    - **공용 IP 주소** - IP 주소 이름은 기본적으로 이전 페이지에서 지정한 Jenkins에 접미사 -IP가 붙습니다. 옵션을 선택하여 이 기본값을 변경할 수 있습니다.
    - **도메인 이름 레이블** - Jenkins 가상 머신에 대한 정규화된 URL 값을 지정합니다.
    - **Jenkins 릴리스 종류** - 옵션(`LTS`, `Weekly build` 또는 `Azure Verified`)에서 원하는 릴리스 종류를 선택합니다. `LTS` 및 `Weekly build` 옵션은 [Jenkins LTS 릴리스 라인](https://jenkins.io/download/lts/) 문서에서 설명하고 있습니다. `Azure Verified` 옵션은 Azure에서 실행되도록 확인된 [Jenkins LTS 버전](https://jenkins.io/download/lts/)을 참조합니다. 

    ![[설정] 탭에서 Jenkins에 대한 가상 머신 설정을 입력합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. **확인**을 선택하여 **통합 설정** 탭으로 이동합니다.

1. **통합 설정** 탭에서 다음 값을 지정합니다.

    - **서비스 사용자** - Azure와의 인증을 위한 자격 증명으로 Jenkins에 추가됩니다. `Auto`는 MSI(관리 서비스 ID)에서 서비스 사용자를 만든다는 것을 의미합니다. `Manual`은 서비스 사용자를 직접 만들어야 함을 의미합니다. 
        - **응용 프로그램 ID** 및 **비밀** - **서비스 사용자** 옵션에 대해 `Manual` 옵션을 선택하는 경우 서비스 사용자에 대해 `Application ID` 및 `Secret`을 지정해야 합니다. [서비스 사용자를 만들](/cli/azure/create-an-azure-service-principal-azure-cli) 때 기본 역할은 Azure 리소스를 사용하는 데 충분한 **참가자**입니다.
    - **클라우드 에이전트 사용** - `ACI`가 Azure Container Instances를 참조하고 `VM`이 가상 머신을 참조하는 에이전트에 대한 기본 클라우드 템플릿을 지정합니다. 클라우드 에이전트를 사용하지 않도록 설정하려면 `No`를 지정할 수도 있습니다.

1. **확인**을 선택하여 **요약** 탭으로 이동합니다.

1. **요약** 탭이 표시되면 입력된 정보의 유효성을 검사합니다. 탭의 위쪽에서 **유효성 검사 통과** 메시지가 표시되면 **확인**을 선택합니다. 

    ![[요약] 탭이 표시되고 선택한 옵션의 유효성을 검사합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. **만들기** 탭이 표시되면 **만들기**를 선택하여 Jenkins 가상 머신을 만듭니다. 서버가 준비되면 Azure Portal에 알림이 표시됩니다.

    ![Jenkins가 준비되었음을 알리는 메시지입니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)