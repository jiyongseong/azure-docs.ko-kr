---
title: 사용자 지정 이미지를 사용하여 Linux에서 함수 만들기(미리 보기) | Microsoft Docs
description: 사용자 지정 Linux 이미지에서 실행되는 Azure Functions를 만드는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 758906126b42c103853e0047bb19d2e96a84fae6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>사용자 지정 이미지를 사용하여 Linux에서 함수 만들기(미리 보기)

Azure Functions를 사용하면 사용자 지정 컨테이너에서 Linux의 함수를 호스팅할 수 있습니다. [기본 Azure App Service 컨테이너에 호스팅](functions-create-first-azure-function-azure-cli-linux.md)할 수도 있습니다. 이 기능은 현재 미리 보기 상태이므로 [Functions 2.0 런타임](functions-versions.md)(마찬가지로 미리 보기 상태)이 필요합니다.

이 자습서에서는 함수 앱을 사용자 지정 Docker 이미지로 배포하는 방법에 대해 알아봅니다. 이 패턴은 기본 제공 App Service 컨테이너 이미지를 사용자 지정해야 하는 경우에 유용합니다. 함수에 특정 언어 버전이 필요하거나 기본 제공 이미지에서 제공되지 않는 특정 종속성 또는 구성이 필요한 경우 사용자 지정 이미지를 사용할 수 있습니다.

이 자습서에서는 Azure Functions를 사용하여 사용자 지정 이미지를 만들고 Docker 허브에 푸시하는 방법을 안내합니다. 그런 다음 이 이미지를 Linux에서 실행되는 함수 앱에 대한 배포 원본으로 사용합니다. Docker를 사용하여 이미지를 빌드하고 푸시합니다. Azure CLI를 사용하여 함수 앱을 만들고 Docker 허브에서 이미지를 배포합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Docker를 사용하여 사용자 지정 이미지 빌드
> * 컨테이너 레지스트리에 사용자 지정 이미지 게시 
> * Azure Storage 계정 만들기 
> * Linux App Service 계획 만들기 
> * Docker 허브에서 함수 앱 배포
> * 함수 앱에 응용 프로그램 설정 추가 

다음 단계는 Mac, Windows 또는 Linux 컴퓨터에서 지원됩니다.  

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Git](https://git-scm.com/downloads)
* 활성 [Azure 구독](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Docker 허브 계정](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>샘플 다운로드

터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제한 후 샘플 코드를 포함하는 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Docker 파일에서 이미지 빌드

이 Git 리포지토리에서 _Dockerfile_을 살펴봅니다. 이 파일은 Linux에서 함수 앱을 실행하는 데 필요한 환경을 설명합니다. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> 개인 컨테이너 레지스트리에서 이미지를 호스팅하는 경우 Dockerfile의 **ENV** 변수를 사용하여 함수 앱에 연결 설정을 추가해야 합니다. 이 자습서에서는 개인 레지스트리를 사용하는 것을 보장할 수 없으므로 보안 모범 사례로 [Azure CLI를 사용하여 배포 후에 연결 설정을 추가](#configure-the-function-app)합니다.   

### <a name="run-the-build-command"></a>build 명령 실행
Docker 이미지를 빌드하려면 `docker build` 명령을 실행하고 이름을 `mydockerimage`로 입력하고 태그를 `v1.0.0`으로 입력합니다. `<docker-id>`를 Docker 허브 계정 ID로 바꿉니다.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

명령은 다음과 유사한 출력을 생성합니다.

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>이미지를 로컬로 테스트
로컬 컨테이너에서 Docker 이미지를 실행하여 빌드된 이미지가 작동하는지 확인합니다. [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 실행하고 이미지의 이름 및 태그를 전달합니다. `-p` 인수를 사용하여 포트를 지정해야 합니다.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

로컬 Docker 컨테이너에서 실행되는 사용자 지정 이미지를 사용하여 <http://localhost:8080>으로 이동하여 함수 앱 및 컨테이너가 제대로 작동하는지 확인합니다.

![함수 앱을 로컬로 테스트](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

컨테이너에서 함수 앱을 확인한 후 실행을 중지합니다. 이제 사용자 이미지를 Docker 허브 계정으로 푸시할 수 있습니다.

## <a name="push-the-custom-image-to-docker-hub"></a>사용자 지정 이미지를 Docker 허브에 푸시

레지스트리는 이미지를 호스트하고 서비스 이미지 및 컨테이너 서비스를 제공하는 응용 프로그램입니다. 이미지를 공유하려면 레지스트리에 푸시해야 합니다. Docker 허브는 Docker 이미지의 레지스트리이며 고유한 공개 또는 개인 리포지토리를 호스팅할 수 있습니다. 

이미지를 푸시하려면 먼저 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Docker 허브에 로그인해야 합니다. `<docker-id>`를 사용자의 계정 이름으로 바꾸고, 콘솔의 프롬프트에서 암호를 입력합니다. 다른 Docker 허브 암호 옵션은 [docker login 명령 설명서](https://docs.docker.com/engine/reference/commandline/login/)를 참조하세요.

```bash
docker login --username <docker-id> 
```

"로그인했습니다."라는 메시지는 사용자가 로그인했다고 확인합니다. 로그인했으면 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 이미지를 Docker 허브에 푸시합니다.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

명령의 출력을 검토하여 푸시가 성공했는지 확인합니다.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
이제 Azure에서 이 이미지를 새 함수 앱에 대한 배포 원본으로 사용할 수 있습니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0.21 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service 계획 만들기

Functions에 대한 Linux 호스팅은 현재 소비 계획에서 지원되지 않습니다. Linux App Service 계획에서 실행해야 합니다. 호스팅에 대한 자세한 내용은 [Azure Functions 호스팅 계획 비교](functions-scale.md)를 참조하세요. 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>사용자 지정 이미지 만들기 및 배포

함수 앱은 함수 실행을 호스팅합니다. [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령을 사용하여 Docker 허브 이미지에서 함수 앱을 만듭니다. 

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. 이전과 마찬가지로 `<docker-id>`는 사용자의 Docker 계정 이름입니다.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
함수 앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

_deployment-container-image-name_ 매개 변수는 Docker 허브에서 호스팅되는 이미지가 함수 앱을 만드는 데 사용됨을 나타냅니다. 


## <a name="configure-the-function-app"></a>함수 앱 구성

함수에는 기본 저장소 계정에 연결하기 위한 연결 문자열이 필요합니다. 사용자 지정 이미지를 개인 컨테이너 계정에 게시하는 경우 [ENV 명령](https://docs.docker.com/engine/reference/builder/#env) 또는 이와 동등한 것을 사용하여 Dockerfile에서 이러한 응용 프로그램 설정을 환경 변수로 대신 설정해야 합니다. 

이 경우 `<storage_account>`는 만든 기본 저장소 계정의 이름입니다. [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) 명령으로 연결 문자열을 가져옵니다. 함수 앱에서 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) 명령으로 이 응용 프로그램 설정을 추가합니다.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

이제 Linux에서 실행되는 함수를 Azure에서 테스트할 수 있습니다.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Docker를 사용하여 사용자 지정 이미지 빌드
> * 컨테이너 레지스트리에 사용자 지정 이미지 게시 
> * Azure Storage 계정 만들기 
> * Linux App Service 계획 만들기 
> * Docker 허브에서 함수 앱 배포
> * 함수 앱에 응용 프로그램 설정 추가

Azure Functions 핵심 도구를 사용하여 Azure Functions를 로컬로 개발하는 방법에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"] 
> [Azure Functions를 로컬에서 코딩 및 테스트](functions-run-local.md)
