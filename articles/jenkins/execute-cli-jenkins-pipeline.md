---
title: "Jenkins로 Azure CLI 실행 | Microsoft Docs"
description: "Azure CLI를 사용하여 Jenkins 파이프라인을 통해 Azure에 Java 웹앱을 배포하는 방법을 알아봅니다."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 2b568bd22858a42178e2821e0e97a3b4ebdfccd5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Jenkins 및 Azure CLI를 사용해 Azure App Service에 배포
Azure에 Java 웹앱을 배포하기 위해 [Jenkins 파이프라인](https://jenkins.io/doc/book/pipeline/)에서 Azure CLI를 사용할 수 있습니다. 이 자습서에서는 Azure VM에서 CI/CD 파이프라인을 만들며 다음 방법이 포함됩니다.

> [!div class="checklist"]
> * Jenkins VM 만들기
> * Jenkins 구성
> * Azure에서 웹앱 만들기
> * GitHub 리포지토리 준비
> * Jenkins 파이프라인 만들기
> * 파이프라인 실행 및 웹앱 확인

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Jenkins 인스턴스 만들기 및 구성
Jenkins 마스터가 아직 없는 경우 [솔루션 템플릿](install-jenkins-solution-template.md)으로 시작합니다. 이 템플릿에는 필수 [Azure 자격 증명](https://plugins.jenkins.io/azure-credentials) 플러그 인이 기본적으로 포함되어 있습니다. 

Azure 자격 증명 플러그 인을 사용하면 Microsoft Azure 서비스 주체 자격 증명을 Jenkins에 저장할 수 있습니다. 버전 1.2에서는 Jenkins 파이프라인이 Azure 자격 증명을 얻을 수 있도록 지원이 추가되었습니다. 

버전 1.2 이상이 있는지 확인합니다.
* Jenkins 대시보드 내에서 **Jenkins 관리->플러그 인 관리자->**를 클릭하고 **Azure 자격 증명**을 검색합니다. 
* 버전이 1.2보다 이전이면 플러그 인을 업데이트합니다.

Java JDK 및 Maven도 Jenkins 마스터에 필요합니다. 설치하려면 SSH를 사용하여 Jenkins 마스터에 로그인하고 다음 명령을 실행합니다.
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Jenkins 자격 증명에 Azure 서비스 주체 추가

Azure CLI를 실행하려면 Azure 자격 증명이 필요합니다.

* Jenkins 대시보드 내에서 **자격 증명->시스템->**을 클릭합니다. **전역 자격 증명(제한 없음)**을 클릭합니다.
* **자격 증명 추가**를 클릭한 다음 구독 ID, 클라이언트 ID, 클라이언트 암호 및 OAuth 2.0 토큰 끝점을 입력하여 [Microsoft Azure 서비스 주체](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)를 추가합니다. 이후 단계에서 사용할 ID를 제공합니다.

![자격 증명 추가](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Java 웹앱을 배포하기 위한 Azure App Service 만들기

[az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) CLI 명령을 사용하여 **무료** 가격 책정 계층과 함께 Azure App Service 계획을 만듭니다. App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스를 정의합니다. App Service 계획에 할당된 모든 응용 프로그램은 이들 리소스를 공유하므로 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

계획이 준비되면 Azure CLI는 다음 예와 비슷한 출력이 표시됩니다.

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure Web App 만들기

 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) CLI 명령을 사용하여 `myAppServicePlan` App Service 계획에서 웹앱 정의를 만듭니다. 웹앱 정의는 응용 프로그램에 액세스하는 URL을 제공하고 Azure에 코드를 배포하는 몇 가지 옵션을 구성합니다. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

`<app_name>` 자리 표시자를 고유한 앱 이름으로 대체합니다. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부이므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 사용자에게 노출하기 전에 웹앱에 사용자 지정 도메인 이름 항목을 매핑할 수 있습니다.

웹앱 정의가 준비되면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Java 구성 

[az appservice web config update](/cli/azure/appservice/web/config#az_appservice_web_config_update) 명령으로 앱에 필요한 Java 런타임 구성을 설정합니다.

다음 명령은 최근 Java 8 JDK 및 [Apache Tomcat](http://tomcat.apache.org/) 8.0에서 실행되도록 웹앱을 구성합니다.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>GitHub 리포지토리 준비
[Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample) 리포지토리를 만듭니다. 사용자 고유의 GitHub 계정에 리포지토리를 분기하려면 오른쪽 위 모서리에 있는 **분기** 단추를 클릭합니다.

* GitHub 웹 UI에서 **Jenkinsfile** 파일을 엽니다. 연필 아이콘을 클릭하여 이 파일을 편집하고 줄 20과 21에서 각각 웹앱의 리소스 그룹 및 이름을 업데이트합니다.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* 줄 23을 변경하여 Jenkins 인스턴스의 자격 증명 ID를 업데이트합니다.

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Jenkins 파이프라인 만들기
웹 브라우저에서 Jenkins를 열고 **새 항목**을 클릭합니다. 

* 작업 이름을 제공하고 **파이프라인**을 선택합니다. **확인**을 클릭합니다.
* **파이프라인** 탭을 클릭합니다. 
* **정의**에서 **SCM의 파이프라인 스크립트**를 선택합니다.
* **SCM**에서 **Git**을 선택합니다.
* 분기 리포지토리의 GitHub URL을 입력합니다(https:\<분기 리포지토리\>.git).
* 페이지 맨 아래에 있는 **저장**

## <a name="test-your-pipeline"></a>파이프라인 테스트
* 만든 파이프라인으로 이동한 다음 **지금 빌드**를 클릭합니다.
* 몇 초 후에 빌드에 성공하고, 빌드로 이동한 다음 **콘솔 출력**을 클릭하여 세부 정보를 확인할 수 있습니다.

## <a name="verify-your-web-app"></a>웹앱 확인
WAR 파일이 웹앱에 성공적으로 배포되었는지 확인하려면 웹 브라우저를 엽니다.

* http://&lt;app_name>.azurewebsites.net/api/calculator/ping으로 이동합니다.  
다음이 표시됩니다.

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>(&lt;x> 및 &lt;y>를 임의의 숫자로 대체)로 이동하여 x와 y의 합계를 구합니다.

![계산기: 추가](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Linux에서 Azure Web App에 배포
이제 Jenkins 파이프라인에서 Azure CLI를 사용하는 방법을 배웠으므로 Linux에서 Azure Web App에 배포하도록 스크립트를 수정할 수 있습니다.

Linux에서 Web App은 Docker를 사용하여 배포를 수행하는 다양한 방법을 지원합니다. 배포하려면 서비스 런타임에 웹앱을 Docker 이미지로 패키지화 하는 Dockerfile을 제공해야 합니다. 그러면 플러그 인이 이미지를 빌드하고 Docker 레지스트리에 푸시하고 이미지를 웹앱에 배포합니다.

* Linux에서 실행되는 Azure Web App을 만들려면 [이 단계](../app-service/containers/quickstart-nodejs.md)를 따르세요.
* 이 [문서](https://docs.docker.com/engine/installation/linux/ubuntu/)의 지침에 따라 Jenkins 인스턴스에 Docker를 설치합니다.
* [이 단계](/azure/container-registry/container-registry-get-started-azure-cli)에 따라 Azure Portal에 컨테이너 레지스트리를 만듭니다.
* 분기한 동일한 [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample) 리포지토리에서 **Jenkinsfile2** 파일을 편집합니다.
    * 18-21줄에서 리소스 그룹, 웹앱 및 ACR의 이름으로 각각 업데이트합니다. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * 24줄에서 \<azsrvprincipal\>을 자격 증명 ID로 업데이트합니다.
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Windows에서 Azure 웹앱에 배포할 때 수행했던 것처럼 새 Jenkins 파이프라인을 만듭니다. 이번에만 대신 **Jenkinsfile2**를 사용합니다.
* 새 작업을 실행합니다.
* 확인하려면 Azure CLI에서 다음을 실행합니다.

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    다음 결과를 얻을 수 있습니다.
    
    ```
    [
    "calculator"
    ]
    ```
    
    http://&lt;app_name>.azurewebsites.net/api/calculator/ping으로 이동합니다. 메시지가 표시됩니다. 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>(&lt;x> 및 &lt;y>를 임의의 숫자로 대체)로 이동하여 x와 y의 합계를 구합니다.
    
## <a name="next-steps"></a>다음 단계
이 자습서에서는 GitHub 리포지토리에 있는 소스 코드를 확인하는 Jenkins 파이프라인을 구성했습니다. Maven을 실행하여 war 파일을 작성한 다음 Azure CLI를 사용하여 Azure App Service를 배포합니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Jenkins VM 만들기
> * Jenkins 구성
> * Azure에서 웹앱 만들기
> * GitHub 리포지토리 준비
> * Jenkins 파이프라인 만들기
> * 파이프라인 실행 및 웹앱 확인
