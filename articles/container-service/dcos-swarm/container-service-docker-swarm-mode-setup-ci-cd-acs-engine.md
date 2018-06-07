---
title: Azure Container Service Engine 및 Swarm Mode를 사용한 CI/CD
description: Docker Swarm Mode, Azure Container Registry Engine 및 Visual Studio Team Services와 Azure Container Service를 사용하여 다중 컨테이너 .NET Core 응용 프로그램 연속 배달
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 01126f3eef988eb1787bafea92e7384aad1a703c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Visual Studio Team Services를 사용하여 ACS Engine 및 Docker Swarm Mode를 포함한 Azure Container Service에 있는 다중 컨테이너 응용 프로그램을 배포하는 전체 CI/CD 파이프라인

*이 문서는 [Visual Studio Team Services를 사용하여 Docker Swarm을 포함한 Azure Container Service에 있는 다중 컨테이너 응용 프로그램을 배포하는 전체 CI/CD 파이프라인](container-service-docker-swarm-setup-ci-cd.md) 설명서를 토대로 작성되었습니다.*

요즘 클라우드를 위한 최신 응용 프로그램을 개발할 때 어려운 문제 중 하나는 이러한 응용 프로그램을 지속적으로 전달할 수 있다는 점입니다. 이 문서에서는 다음을 사용하여 전체 CI/CD(지속적인 통합 및 배포) 파이프라인을 구현하는 방법을 알아봅니다. 
* Docker Swarm Mode의 Azure Container Service Engine
* Azure Container Registry
* Visual Studio Team Services

이 문서는 간단한 응용 프로그램을 기반으로 [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)에서 사용할 수 있으며 ASP.NET Core를 사용하여 전개됩니다. 응용 프로그램은 세 개의 웹 API 및 하나의 웹 프론트 엔드라는 네 개의 다른 서비스로 구성되어 있습니다.

![MyShop 샘플 응용 프로그램](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Visual Studio Team Services를 사용하여 Docker Swarm Mode 클러스터에서 이 응용 프로그램을 지속적으로 제공하려고 합니다. 다음 그림에서는 이 연속 배달 파이프라인을 자세히 설명합니다.

![MyShop 샘플 응용 프로그램](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

여기에서는 단계에 대해 간략히 설명합니다.

1. 코드 변경 내용을 소스 코드 리포지토리로 커밋합니다(여기에서는 GitHub). 
2. GitHub은 Visual Studio Team Services에서 빌드를 트리거합니다. 
3. Visual Studio Team Services는 최신 버전의 원본을 가져오고 응용 프로그램을 작성하는 모든 이미지를 빌드합니다. 
4. Visual Studio Team Services는 Azure 컨테이너 레지스트리 서비스를 사용하여 만든 Docker 레지스트리에 이미지를 각각 푸시합니다. 
5. Visual Studio Team Services는 새 릴리스를 트리거합니다. 
6. 릴리스는 Azure Container Service 클러스터 노드에서 SSH를 사용하는 마스터 명령을 실행합니다. 
7. 클러스터의 Docker Swarm Mode는 이미지의 최신 버전을 가져옵니다. 
8. Docker Stack을 사용하여 새 버전의 응용 프로그램을 배포합니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 작업을 완료해야 합니다.

- [ACS Engine을 사용하여 Azure Container Service에서 Swarm Mode 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Azure 컨테이너 서비스에서 Swarm 클러스터에 연결](../container-service-connect.md)
- [Azure 컨테이너 레지스트리 만들기](../../container-registry/container-registry-get-started-portal.md)
- [Visual Studio Team Services 계정 및 팀 프로젝트 만들기](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [GitHub 계정에 GitHub 리포지토리 포크](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Azure Container Service의 Docker Swarm Orchestrator는 레거시 독립 실행형 Swarm을 사용합니다. 현재 통합 [Swarm 모드](https://docs.docker.com/engine/swarm/)(Docker 1.12 이상)는 Azure Container Service에서 지원되는 Orchestrator가 아닙니다. 이러한 이유로 [ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), 커뮤니티 제공 [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com)의 Docker 솔루션을 사용하고 있습니다.
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>1단계: Visual Studio Team Services 계정 구성 

이 섹션에서는 사용자의 Visual Studio Team Services 계정을 구성합니다. VSTS 서비스 끝점을 구성하려면 Visual Studio Team Services 프로젝트의 도구 모음에서 **설정** 아이콘을 클릭하고 **서비스**를 선택합니다.

![서비스 끝점 열기](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Visual Studio Team Services 및 Azure 계정 연결

VSTS 프로젝트와 Azure 계정 간에 연결을 설정합니다.

1. 왼쪽에서 **새 서비스 끝점** > **Azure Resource Manager**를 클릭합니다.
2. Azure 계정을 사용하도록 VSTS를 인증하려면 **구독**을 선택하고 **확인**을 클릭합니다.

    ![Visual Studio Team Services - Azure 인증](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Visual Studio Team Services 및 GitHub 연결

VSTS 프로젝트와 GitHub 계정 간에 연결을 설정합니다.

1. 왼쪽에서 **새 서비스 끝점** > **GitHub**을 클릭합니다.
2. VSTS를 인증하여 GitHub 계정으로 작업하려면 **인증**을 클릭하고 열린 창에서 절차를 따릅니다.

    ![Visual Studio Team Services - GitHub 인증](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Azure Container Service 클러스터에 VSTS 연결

CI/CD 파이프라인에 도달하기 전에 Azure의 Docker Swarm 클러스터에 대한 외부 연결을 구성하는 마지막 단계가 있습니다. 

1. Docker Swarm 클러스터의 경우 **SSH** 형식의 끝점을 추가합니다. 그런 다음 Swarm 클러스터(마스터 노드)의 SSH 연결 정보를 입력합니다.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

이제 모든 구성을 수행했습니다. 다음 단계에서는 Docker Swarm 클러스터에 응용 프로그램을 빌드하고 배포하는 CI/CD 파이프라인을 만듭니다. 

## <a name="step-2-create-the-build-definition"></a>2단계: 빌드 정의 만들기

이 단계에서는 VSTS 프로젝트의 빌드 정의를 설정하고 사용자 컨테이너 이미지에 대한 빌드 워크플로를 정의합니다.

### <a name="initial-definition-setup"></a>초기 정의 설정

1. 빌드 정의를 만들려면 Visual Studio Team Services 프로젝트에 연결하고 **빌드 및 릴리스**를 클릭합니다. **빌드 정의** 섹션에서 **+ 새로 만들기**를 클릭합니다. 

    ![Visual Studio Team Services - 새 빌드 정의](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. **시작**을 선택합니다.

    ![Visual Studio Team Services - 새 빈 빌드 정의](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. 그런 후 **변수** 탭을 클릭하고 두 개의 새로운 변수 **RegistryURL** 및 **AgentURL**을 만듭니다. 레지스트리 및 클러스터 에이전트 DNS 값을 붙여 넣습니다.

    ![Visual Studio Team Services - 빌드 변수 구성](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. **빌드 정의** 페이지에서 **트리거** 탭을 열고 필수 구성 요소에서 만든 MyShop 프로젝트 포크와의 연속 통합을 사용하도록 빌드를 구성합니다. 그런 다음 **일괄 처리 변경 사항**을 선택합니다. *docker-linux*를 **분기 사양**으로 선택합니다.

    ![Visual Studio Team Services - 빌드 리포지토리 구성](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. 마지막으로 **옵션** 탭을 클릭하고 기본 에이전트 큐를 **Hosted Linux Preview**로 구성합니다.

    ![Visual Studio Team Services - 호스트 에이전트 구성](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>빌드 워크플로 정의
다음 단계에서는 빌드 워크플로를 정의합니다. 먼저 코드의 소스를 구성해야 합니다. 이렇게 하려면 **GitHub**, **리포지토리** 및 **분기**(docker-linux)를 선택합니다.

![Visual Studio Team Services - 코드 소스 구성](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

*MyShop* 응용 프로그램에 대해 빌드되는 5개의 컨테이너 이미지가 있습니다. 각 이미지는 프로젝트 폴더에 있는 Dockerfile을 사용하여 빌드됩니다.

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

각 이미지에 대한 두 가지 Docker 단계가 필요합니다. 하나는 이미지를 빌드하고 다른 하나는 Azure 컨테이너 레지스트리에서 이미지를 푸시하는 것입니다. 

1. 빌드 워크플로에서 단계를 추가하려면 **+ 빌드 단계 추가**를 클릭하고 **Docker**를 선택합니다.

    ![Visual Studio Team Services - 빌드 단계 추가](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. 각 이미지의 경우 `docker build` 명령을 사용하는 하나의 단계를 구성합니다.

    ![Visual Studio Team Services - Docker 빌드](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    빌드 작업의 경우 **이미지 빌드** 작업인 Azure Container Registry를 선택하고 각 이미지를 정의하는 Dockerfile을 선택합니다. **작업 디렉터리**를 Dockerfile 루트 디렉터리로 설정하고, **이미지 이름**을 정의한 후 **최신 태그 포함**을 선택합니다.
    
    이미지 이름은 ```$(RegistryURL)/[NAME]:$(Build.BuildId)``` 형식이어야 합니다. **[NAME]** 을 이미지 이름으로 바꿉니다.
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. 각 이미지의 경우 `docker push` 명령을 사용하는 두 번째 단계를 구성합니다.

    ![Visual Studio Team Services - Docker 푸시](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    푸시 작업의 경우 **이미지 푸시** 작업인 Azure 컨테이너 레지스트리를 선택하고 이전 단계에서 기본 설치되어 있는 **이미지 이름**을 입력한 후 **최신 태그 포함**을 선택합니다.

4. 5개의 이미지 각각에 대한 빌드 및 푸시 단계를 구성한 후에 빌드 워크플로에서 3개의 단계를 더 추가합니다.

   ![Visual Studio Team Services - 명령줄 작업 추가](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. bash 스크립트를 사용하여 docker-compose.yml 파일에 나오는 모든 *RegistryURL*을 RegistryURL 변수로 바꾸는 명령줄 작업입니다. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - 레지스트리 URL로 작성 파일 업데이트](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. bash 스크립트를 사용하여 docker-compose.yml 파일에 나오는 모든 *AgentURL*을 AgentURL 변수로 바꾸는 명령줄 작업입니다.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. 릴리스에서 사용할 수 있도록 빌드 아티팩트인 업데이트된 작성 파일을 삭제하는 작업입니다. 자세한 내용은 다음과 같은 화면을 참조하세요.

         ![Visual Studio Team Services - 아티팩트 게시](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services - 작성 파일 게시](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. **저장 및 큐에 넣기**를 클릭하여 빌드 정의를 테스트합니다.

   ![Visual Studio Team Services - 저장 및 큐에 넣기](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services - 새 큐](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. **빌드**가 올바른 경우 다음 화면이 표시되어야 합니다.

  ![Visual Studio Team Services - 빌드 성공](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>3단계: 릴리스 정의 만들기

Visual Studio Team Services를 사용하면 [환경에서 릴리스를 관리](https://www.visualstudio.com/team-services/release-management/)할 수 있습니다. 연속 배포를 설정하여 사용자의 응용 프로그램이 다른 환경(예: 개발, 테스트, 프로덕션 전 및 프로덕션)에서 원활하게 배포되고 있는지 확인할 수 있습니다. Azure Container Service Docker Swarm Mode 클러스터를 나타내는 환경을 만들 수 있습니다.

![Visual Studio Team Services - ACS에 대한 릴리스](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>최초 릴리스 설정

1. 릴리스 정의를 만들려면 **릴리스** > **+ 릴리스**를 클릭합니다.

2. 아티팩트 원본을 구성하려면 **아티팩트** > **아티팩트 원본 연결**을 클릭합니다. 여기에서는 이전 단계에서 정의한 빌드에 이 새로운 릴리스 정의를 연결합니다. 그런 후에 docker-compose.yml 파일을 릴리스 프로세스에서 사용할 수 있습니다.

    ![Visual Studio Team Services - 릴리스 아티팩트](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. 릴리스 트리거를 구성하려면 **트리거**를 클릭하고 **연속 배포**를 선택합니다. 동일한 아티팩트 원본에 트리거를 설정합니다. 이 설정을 통해 빌드가 성공적으로 완료되면 새 릴리스가 시작될 수 있습니다.

    ![Visual Studio Team Services - 릴리스 트리거](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. 릴리스 변수를 구성하려면 **변수**를 클릭하고 **+변수**를 선택하여 레지스트리의 정보로 3개의 변수, 즉 **docker.username**, **docker.password** 및 **docker.registry**를 새로 만듭니다. 레지스트리 및 클러스터 에이전트 DNS 값을 붙여 넣습니다.

    ![Visual Studio Team Services - 빌드 리포지토리 구성](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > 이전 화면에서와 같이 docker.password에서 **잠금** 확인란을 클릭합니다. 이 설정은 암호를 제한하는 데 중요합니다.
    >

### <a name="define-the-release-workflow"></a>릴리스 워크플로 정의

릴리스 워크플로는 추가한 두 가지 작업으로 구성됩니다.

1. 이전에 구성한 SSH 연결을 사용하여 Docker Swarm 마스터 노드의 *배포* 폴더에 작성 파일을 안전하게 복사하도록 작업을 구성합니다. 자세한 내용은 다음과 같은 화면을 참조하세요.
    
    소스 폴더: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - 릴리스 SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. bash 명령을 실행하는 두 번째 작업을 구성하여 마스터 노드에서 `docker` 및 `docker stack deploy` 명령을 실행합니다. 자세한 내용은 다음과 같은 화면을 참조하세요.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services - 릴리스 Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    마스터에서 실행되는 명령은 Docker CLI 및 Docker 작성 CLI를 사용하여 다음 작업을 수행합니다.

    - Azure Container Registry에 로그인(**변수** 탭에 정의된 세 개의 빌드 변수 사용)
    - Swarm 끝점을 사용하는 **DOCKER_HOST** 변수 정의(:2375)
    - 이전 안전한 복사 작업에서 만들어지고 docker-compose.yml 파일을 포함하는 *배포* 폴더 탐색 
    - 새 이미지를 가져오고 컨테이너를 만드는 `docker stack deploy` 명령을 실행합니다.

    >[!IMPORTANT]
    > 이전 화면에 표시된 대로 **STDERR에 실패** 확인란을 취소된 상태로 둡니다. 이 설정을 사용하면 `docker-compose`이 표준 오류 출력에서 “컨테이너가 중지 또는 삭제됩니다.”와 같은 여러 진단 메시지를 인쇄하기 때문에 릴리스 프로세스를 완료할 수 있습니다. 확인란을 선택하면 모든 작업이 정상적으로 작동하는 경우에도 Visual Studio Team Services는 릴리스 중에 오류가 발생했다고 보고합니다.
    >
3. 새 릴리스 정의를 저장합니다.

## <a name="step-4-test-the-cicd-pipeline"></a>4단계: CI/CD 파이프라인 테스트

이제 구성했으면 이 새 CI/CD 파이프라인을 테스트하겠습니다. 테스트하는 가장 쉬운 방법은 소스 코드를 업데이트하고 GitHub 리포지토리에 변경 내용을 커밋하는 것입니다. 코드를 푸시한 몇 초 후에 새 빌드가 Visual Studio Team Services에서 실행되는 것을 볼 수 있습니다. 성공적으로 완료되면 새 릴리스가 트리거되고 Azure Container Service 클러스터에서 새 버전의 응용 프로그램을 배포합니다.

## <a name="next-steps"></a>다음 단계

* Visual Studio Team Services를 사용하는 CI/CD에 대한 자세한 내용은 [VSTS 빌드 개요](https://www.visualstudio.com/docs/build/overview)를 참조하세요.
* ACS Engine에 대한 자세한 내용은 [ACS Engine GitHub 리포지토리](https://github.com/Azure/acs-engine)를 참조하세요.
* Docker Swarm Mode에 대한 자세한 내용은 [Docker Swarm Mode 개요](https://docs.docker.com/engine/swarm/)를 참조하세요.
