---
title: "Jenkins를 사용하여 Azure에 웹앱 배포 | Microsoft Docs"
description: "Jenkins 및 Docker를 사용하여 Java 웹앱에 대해 GitHub에서 Azure App Service로 지속적인 통합을 설정합니다."
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: b2606acba341d4cfbc16314048e134fa30ff8606
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Jenkins를 사용하여 Azure App Service로 지속적인 통합 및 배포 설정

이 자습서는 [Spring Boot](http://projects.spring.io/spring-boot/) 프레임워크로 개발된 샘플 Java 웹앱의 Jenkins를 사용한 [Linux에서 Azure App Service Web App](/azure/app-service/containers/app-service-linux-intro)으로의 지속적인 통합 및 배포(CI/CD)를 설정합니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure App Service로 배포하는 데 필요한 Jenkins 플러그 인을 설치합니다.
> * 새 커밋이 푸시되면 GitHub 리포지토리에서 Docker 이미지를 빌드하도록 Jenkins 작업을 정의합니다.
> * Linux용 새 Azure 웹앱을 정의하고 Azure Container Registry에 푸시된 Docker 이미지를 배포하도록 구성합니다.
> * Azure App Service Jenkins 플러그 인을 구성합니다.
> * 수동 빌드를 사용하여 샘플 앱을 Azure App Service로 배포합니다.
> * Jenkins 빌드를 트리거하고 변경 내용을 GitHub에 푸시하여 웹앱을 업데이트합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 완료하려면 다음이 필요합니다.

* JDK 및 Maven 도구가 구성되어 있는 [Jenkins](https://jenkins.io/). Jenkins 시스템에 없는 경우 [Jenkins 솔루션 템플릿](/azure/jenkins/install-jenkins-solution-template)에서 Azure에 새로 하나 만듭니다.
* [GitHub](https://github.com) 계정.
* 로컬 명령줄 또는 [Azure Cloud Shell](/azure/cloud-shell/overview)에 있는 [Azure CLI 2.0](/cli/azure)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Jenkins 플러그 인 설치

1. Jenkins 웹 콘솔에 웹 브라우저를 열고 왼쪽 메뉴에서 **Jenkins 관리**를 선택한 다음, **플러그 인 관리**를 선택합니다.
2. **가용성** 탭을 선택합니다.
3. 다음 플러그 인 옆의 확인란을 검색하여 선택합니다.   

    - [Azure App Service 플러그 인](https://plugins.jenkins.io/azure-app-service)
    - [GitHub 분기 원본 플러그 인](https://plugins.jenkins.io/github-branch-source)

    플러그 인이 나타나지 않는 경우 **설치됨** 탭을 확인하여 이미 설치되어 있지는 않은지 확인합니다.

1. **지금 다운로드하고 다시 시작한 후 설치**를 선택하여 Jenkins 구성에 플러그 인을 사용할 수 있도록 합니다.

## <a name="configure-github-and-jenkins"></a>GitHub 및 Jenkins 구성

사용자 계정의 리포지토리에 새 커밋이 푸시되는 경우 [GitHub 웹후크](https://developer.github.com/webhooks/)를 받도록 Jenkins를 설정합니다.

1. **Jenkins 관리**, **시스템 구성**을 차례로 선택합니다. **GitHub** 섹션에서 **후크 관리**가 선택되어 있는지 확인한 다음, **추가 GitHub 작업 관리**를 선택하고 **로그인 및 암호를 토큰으로 변환**을 선택합니다.
2. **로그인 및 암호에서** 라디오 단추를 선택하고 GitHub 사용자 이름 및 암호를 입력합니다. **토큰 자격 증명 만들기**를 선택하여 새로운 [GitHub 개인용 액세스 토큰](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)을 만듭니다.   
   ![로그인 및 암호에서 GitHub PAT 만들기](media/jenkins-java-quickstart/create_github_credentials.png)
3.  GitHub 서버 구성의 **자격 증명** 드롭다운에서 새로 만든 토큰을 선택합니다. **연결 테스트**를 선택하여 인증이 작동하는지 확인합니다.   
   ![PAT가 구성되면 GitHub에 연결을 확인](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> GitHub 계정에 활성화된 2단계 인증이 있는 경우 GitHub에서 토큰을 만들고 이를 사용하여 Jenkins를 구성합니다. 전체 세부 정보는 [Jenkins GitHub 플러그 인](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) 설명서를 검토하세요.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>샘플 리포지토리 포크 및 Jenkins 작업 만들기 

1. [Spring Boot 샘플 응용 프로그램 리포지토리](https://github.com/spring-guides/gs-spring-boot-docker)를 열고, 오른쪽 위 모서리에 있는 **포크**를 선택하여 사용자 고유의 GitHub 계정으로 포크합니다.   
    ![GitHub에서 포크](media/jenkins-java-quickstart/fork_github_repo.png)
1. Jenkins 웹 콘솔에서 **새 항목**을 선택하고, 이름을 **MyJavaApp**으로 지정하고, **프리스타일 프로젝트**를 선택한 다음, **확인**을 선택합니다.   
    ![새 Jenkins 프리스타일 프로젝트](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. **일반** 섹션에서 **GitHub** 프로젝트를 선택하고 https://github.com/raisa/gs-spring-boot-docker와 같은 포크된 리포지토리 URL을 입력합니다.
3. **소스 코드 관리** 섹션에서 **Git**을 선택하고, https://github.com/raisa/gs-spring-boot-docker.git와 같은 포크된 리포지토리 `.git` URL을 입력합니다.
4. **트리거 빌드**에서 **GITscm 폴링에 대한 GitHub 후크 트리거**를 선택합니다.
5. **빌드** 섹션에서 **빌드 단계 추가**를 선택하고 **최상위 Maven 대상 호출**을 선택합니다. **목표** 필드에 `package`를 입력합니다.
6. **저장**을 선택합니다. 프로젝트 페이지에서 **지금 빌드**를 선택하여 작업을 테스트할 수 있습니다.

## <a name="configure-azure-app-service"></a>Azure App Service 구성 

1. Azure CLI 또는 [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 새 [웹앱을 Linux에](/azure/app-service/containers/app-service-linux-intro) 만듭니다. 이 자습서의 웹앱 이름은 `myJavaApp`이지만, 사용자 소유 앱에는 고유한 이름을 사용해야 합니다.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. [Azure Container Registry](/azure/container-registry/container-registry-intro)를 만들어 Jenkins로 빌드된 Docker 이미지를 저장합니다. 이 자습서에 사용된 컨테이너 레지스트리 이름은 `jenkinsregistry`이지만, 사용자 소유 컨테이너 레지스트리에는 고유한 이름을 사용해야 합니다. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. 웹앱을 구성하여 컨테이너 레지스트리에 푸시되는 Docker 이미지를 실행하고, 컨테이너에서 실행 중인 앱이 포트 8080에서 요청을 수신 대기하도록 지정합니다.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Azure App Service Jenkins 플러그 인 구성

1. Jenkins 웹 콘솔에서 사용자가 만든 **MyJavaApp** 작업을 선택한 다음, 페이지의 왼쪽에서 **구성**을 선택합니다.
2. **빌드 후 작업**으로 스크롤한 다음, **빌드 후 작업 추가**를 선택하고 **Azure Wep App 게시**를 선택합니다.
3. **Azure 프로필 구성**에서 **Azure 자격 증명** 옆에 있는 **추가**를 선택하고 **Jenkins**를 선택합니다.
4. **자격 증명 추가** 대화 상자에 있는 **종류** 드롭다운에서 **Microsoft Azure 서비스 주체**를 선택합니다.
5. Azure CLI 또는 [Cloud Shell](/azure/cloud-shell/overview)에서 Active Directory Service 주체를 만듭니다.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. 서비스 주체의 자격 증명을 **자격 증명 추가** 대화 상자에 입력합니다. Azure 구독 ID를 모르는 경우 CLI에서 쿼리할 수 있습니다.
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Azure 서비스 주체 구성](media/jenkins-java-quickstart/azure_service_principal.png)
6. **서비스 주체 확인**을 선택하여 Azure를 사용하는 서비스 주체 인증을 확인합니다. 
7. **추가**를 선택하여 자격 증명을 저장합니다.
8. **Azure Web App 게시** 구성으로 돌아가면 방금 **Azure 자격 증명** 드롭다운에서 추가한 서비스 주체 자격 증명을 선택합니다.
9. **구성 추가**에서 드롭다운 목록에서 리소스 그룹 및 웹앱 이름을 선택합니다.
10. **Docker를 통해 게시** 라디오 단추를 선택합니다.
11. `complete/Dockerfile`을 **Dockerfile 경로**에 입력합니다.
12. `https://jenkinsregistry.azurecr.io`를 **Docker 레지스트리 URL** 필드에 입력합니다.
13. **레지스트리 자격 증명** 옆에 있는 **추가**를 선택합니다. 
14. **Username**에 대해 사용자가 만든 Azure Container Registry에 사용할 관리자 사용자 이름을 입력합니다.
15. Azure Container Registry에 사용할 암호를 **암호** 필드에 입력합니다. Azure Portal에서 또는 다음 CLI 명령을 통해 사용자 이름 및 암호를 얻을 수 있습니다.

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![컨테이너 레지스트리 자격 증명 추가](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. **추가**를 선택하여 자격 증명을 저장합니다.
16. **Azure Web App 게시**에 대한 **앱 구성** 패널에 있는 **레지스트리 자격 증명** 드롭다운에서 새로 만든 자격 증명을 선택합니다. 완료된 빌드 후 작업은 다음 이미지와 같이 표시됩니다.   
    ![Azure App Service 배포에 대한 빌드 후 작업 구성](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. **저장**을 클릭하여 작업 구성을 저장합니다.

## <a name="deploy-the-app-from-github"></a>GitHub에서 앱 배포

1. Jenkins 프로젝트에서 **지금 빌드**를 선택하여 샘플 앱을 Azure에 배포합니다.
2. 빌드가 완료되면 앱이 Azure에서 게시 URL(예: http://myjavaapp.azurewebsites.net )에 나타납니다.   
   ![Azure에서 배포된 앱 보기](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>변경 내용 푸시 및 재배포

1. Github 포크에서, 웹에서 `complete/src/main/java/Hello/Application.java`를 찾아봅니다. GitHub 인터페이스의 오른쪽에 있는 **이 파일 편집** 링크를 선택합니다.
2. `home()` 메서드를 다음과 같이 변경하고 리포지토리의 마스터 분기에 대한 변경 내용을 커밋합니다.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. 리포지토리의 `master` 분기의 새 커밋에 의해 트리거된 새 빌드가 Jenkins에서 시작됩니다. 완료되면 Azure에서 앱을 다시 로드합니다.     
      ![Azure에서 배포된 앱 보기](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>다음 단계

- [Azure VM을 빌드 에이전트로 사용](/azure/jenkins/jenkins-azure-vm-agents)
- [Azure CLI로 작업 및 파이프라인의 리소스 관리](/azure/jenkins/execute-cli-jenkins-pipeline)
 
