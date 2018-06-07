---
title: Azure에서 Jenkins 서버 만들기
description: Jenkins 솔루션 템플릿에서 Azure Linux 가상 머신에 Jenkins를 설치하고 샘플 Java 응용 프로그램을 빌드합니다.
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: c9f86ab2536d3c598bb8c7084524395b41f18db0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Azure Portal에서 Azure Linux VM에 Jenkins 서버 만들기

이 빠른 시작은 Azure에서 사용 가능하도록 구성된 도구와 플러그 인을 사용하여 Ubuntu Linux VM에 [Jenkins](https://jenkins.io)를 설치하는 방법을 보여줍니다. 작업을 완료하면 [GitHub](https://github.com)에서 샘플 Java 앱을 빌드하는 Jenkins 서버가 Azure에서 실행됩니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독
* 컴퓨터의 명령줄에서 SSH에 대한 액세스(Bash 셸 또는 [PuTTY](http://www.putty.org/) 등)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>솔루션 템플릿에서 Jenkins VM 만들기
Jenkins는 단일 Jenkins 설치가 많은 수의 프로젝트를 호스트하거나 빌드 또는 테스트에 필요한 다양한 환경을 제공할 수 있도록 하나 이상의 에이전트에 작업을 위임하는 모델을 지원합니다. 이 섹션의 단계는 Azure에서 Jenkins 서버를 설치 및 구성하는 과정을 안내합니다.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Jenkins에 연결

웹 브라우저에서 가상 머신(예: http://jenkins2517454.eastus.cloudapp.azure.com/))으로 이동합니다. Jenkins 콘솔은 보안되지 않은 HTTP를 통해 액세스할 수 없으므로 SSH 터널을 사용하여 컴퓨터에서 안전하게 Jenkins 콘솔에 액세스하는 지침이 이 페이지에서 제공됩니다.

![Jenkins 잠금 해제](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

명령줄 페이지에서 `ssh` 명령을 사용하여 터널을 설정하고, 솔루션 템플릿에서 가상 머신을 설정하는 경우 이전에 선택한 가상 머신 관리 사용자의 이름으로 `username`을 바꿉니다.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

터널을 시작한 후에 로컬 컴퓨터에서 http://localhost:8080/으로 이동합니다. 

Jenkins VM에 SSH를 통해 연결되어 있는 동안 명령줄에서 다음 명령을 실행하여 초기 암호를 가져옵니다.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

이 초기 암호를 사용하여 Jenkins 대시보드를 처음으로 잠금 해제합니다.

![Jenkins 잠금 해제](./media/install-jenkins-solution-template/jenkins-unlock.png)

다음 페이지에서 **Install suggested plugins**(권장 플러그 인 설치)를 선택하고, Jenkins 대시보드에 액세스하는 데 사용한 Jenkins 관리 사용자를 만듭니다.

![Jenkins가 준비되었습니다.](./media/install-jenkins-solution-template/jenkins-welcome.png)

이제 Jenkins 서버는 코드를 빌드할 준비가 되었습니다.

## <a name="create-your-first-job"></a>첫 번째 작업 만들기

Jenkins 콘솔에서 **Create new jobs**(새 작업 만들기)를 선택하고 이름은 **mySampleApp**으로 지정하고 **Freestyle project**(프리스타일 프로젝트)를 선택한 후 **OK**(확인)를 선택합니다.

![새 작업 만들기](./media/install-jenkins-solution-template/jenkins-new-job.png) 

**소스 코드 관리** 탭을 선택하고, **Git** 사용을 설정한 다음, **리포지토리 URL** 필드에 다음 URL을 입력합니다. `https://github.com/spring-guides/gs-spring-boot.git`

![Git 리포지토리 정의](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

**빌드** 탭을 선택한 후 **빌드 단계 추가**, **Gradle 스크립트 호출**을 선택합니다. **Gradle 래퍼 사용**을 선택한 후 **래퍼 위치**에 `complete`를 입력하고 **작업**에 `build`를 입력합니다.

![Gradle 래퍼를 사용하여 빌드](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

**고급..**을 선택하고 **루트 빌드 스크립트** 필드에 `complete`를 입력합니다. **저장**을 선택합니다.

![Gradle 래퍼 빌드 단계에서 고급 설정을 지정](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>코드 빌드

**지금 빌드**를 선택하여 코드를 컴파일하고 샘플 앱을 패키징합니다. 빌드가 완료되면 프로젝트에 대한 **작업 영역** 링크를 선택합니다.

![작업 영역으로 이동하여 빌드에서 JAR 파일 가져오기](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

`complete/build/libs`로 이동하여 `gs-spring-boot-0.1.0.jar`이 있으면 빌드가 성공한 것입니다. 이제 Azure에서 Jenkins 서버로 고유한 프로젝트를 빌드할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure VM을 Jenkins 에이전트로 추가](jenkins-azure-vm-agents.md)
