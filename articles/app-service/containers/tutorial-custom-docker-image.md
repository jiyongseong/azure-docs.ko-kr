---
title: Web App for Containers에 사용자 지정 Docker 이미지 사용 - Azure | Microsoft Docs
description: Web App for Containers에 사용자 지정 Docker 이미지를 사용하는 방법.
keywords: azure app service, 웹앱, linux, docker, 컨테이너
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: SyntaxC4
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 887ed316605ab423159ef0d2e07f0960c702ed8b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Web App for Containers에 사용자 지정 Docker 이미지 사용

[Containers용 Web App](app-service-linux-intro.md)은 PHP 7.0 및 Node.js 4.5와 같은 특정 버전에 대한 지원을 통해 Linux에서 기본 제공 Docker 이미지를 제공합니다. Web App for Containers는 Docker 컨테이너 기술을 사용하여 기본 제공 이미지와 사용자 지정 이미지를 모두 PaaS(Platform as a Service)로 호스팅합니다. 이 자습서에서는 사용자 지정 Docker 이미지를 빌드하고 이 이미지를 Web App for Containers에 배포하는 방법에 대해 설명합니다. 이 패턴은 기본 제공 이미지에 선택한 언어가 포함되어 있지 않거나 기본 제공 이미지에 제공되지 않는 특정 구성이 응용 프로그램에 필요한 경우에 유용합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 Docker 이미지를 Azure에 배포
> * 컨테이너를 실행하도록 환경 변수 구성
> * Docker 이미지를 업데이트하고 다시 배포
> * SSH를 사용하여 컨테이너에 연결
> * 개인 Docker 이미지를 Azure에 배포

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Docker 허브 계정](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>샘플 다운로드

터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제한 후 샘플 코드를 포함하는 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Docker 파일에서 이미지 빌드

Git 리포지토리에서 _Dockerfile_을 살펴봅니다. 이 Docker 파일은 응용 프로그램을 실행하는 데 필요한 Python 환경을 설명합니다. 또한 이미지는 컨테이너와 호스트 간에 보안 통신을 위해 [SSH](https://www.ssh.com/ssh/protocol/) 서버를 설정합니다.

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Docker 이미지를 빌드하려면 `docker build` 명령을 실행하고 이름을 _mydockerimage_로 지정하고 태그를 _v1.0.0_으로 입력합니다. _\<docker-id>_ 를 Docker 허브 계정 ID로 바꿉니다.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

명령은 다음과 유사한 출력을 생성합니다.

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Docker 컨테이너를 실행하여 빌드가 작동하는지를 테스트합니다. [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 명령을 실행하고 이미지의 이름 및 태그를 전달합니다. `-p` 인수를 사용하여 포트를 지정해야 합니다.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

`http://localhost:2222`로 이동하여 웹앱과 컨테이너가 제대로 작동하는지 확인합니다.

![로컬로 웹앱 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> SSH, SFTP 또는 Visual Studio Code를 사용하여 로컬 개발 컴퓨터에서 직접 앱 컨테이너에 연결할 수도 있습니다(Node.js 앱 라이브 디버깅을 위해). 자세한 내용은 [Linux App Service의 원격 디버깅 및 SSH](https://aka.ms/linux-debug)를 참조하세요.
>

## <a name="push-the-docker-image-to-docker-hub"></a>Docker 허브에 Docker 이미지 푸시

레지스트리는 이미지를 호스트하고 서비스 이미지 및 컨테이너 서비스를 제공하는 응용 프로그램입니다. 이미지를 공유하려면 레지스트리에 푸시해야 합니다. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> 개인 Docker 레지스트리 푸시 [개인 레지스트리에서 Docker 이미지 사용](#use-a-docker-image-from-any-private-registry-optional)에 대한 선택적 지침을 참조하세요.

<!--## [Docker Hub](#tab/docker-hub)-->

Docker 허브는 Docker 이미지의 레지스트리이며 고유한 공개 또는 개인 리포지토리를 호스팅할 수 있습니다. 사용자 지정 Docker 이미지를 공용 Docker 허브에 푸시하려면 [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 전체 이미지 이름 및 태그를 제공합니다. 전체 이미지 이름 및 태그는 다음 샘플과 같습니다.

```
<docker-id>/image-name:tag
```

이미지를 푸시하려면 먼저 [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Docker 허브에 로그인해야 합니다. _\<docker-id>_ 를 사용자의 계정 이름으로 바꾸고, 콘솔의 프롬프트에서 암호를 입력합니다.

```bash
docker login --username <docker-id>
```

"로그인했습니다."라는 메시지는 사용자가 로그인했다고 확인합니다. 일단 로그인하면 [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 Docker 허브에 이미지를 푸시할 수 있습니다.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

명령의 출력을 검토하여 푸시가 성공했는지 확인합니다.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포

Azure Web Apps를 사용하여 클라우드에서 네이티브 Linux 응용 프로그램을 호스트할 수 있습니다. 컨테이너용 Web App을 만들려면 그룹, 서비스 계획 및 웹앱 자체를 만드는 Azure CLI 명령을 실행해야 합니다. 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Linux App Service 계획 만들기

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>웹앱 만들기

Cloud Shell에서 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](app-service-linux-intro.md)을 만듭니다. _<appname>_ 을 고유한 앱 이름으로, _\<docker-ID>_ 를 Docker ID로 바꾸어야 합니다.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>환경 변수 구성

대부분의 Docker 이미지에는 환경 변수가 구성되어야 합니다. 다른 사용자가 빌드한 기존 Docker 이미지를 사용하는 경우 이미지에서 80 포트 이외의 포트를 사용할 수 있습니다. `WEBSITES_PORT` 앱 설정을 사용하여 이미지에서 사용하는 포트를 Azure에 알려줍니다. [이 자습서의 Python 샘플](https://github.com/Azure-Samples/docker-django-webapp-linux)에 대한 GitHub 페이지에서 `WEBSITES_PORT`를 _8000_으로 설정해야 함을 보여 줍니다.

앱 설정을 지정하려면 Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용합니다. 앱 설정은 대/소문자를 구분하고 공백으로 구분합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> 개인 Docker 레지스트리에서 배포 [개인 레지스트리에서 Docker 이미지 사용](#use-a-docker-image-from-any-private-registry-optional)에 대한 선택적 지침을 참조하세요.

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>웹앱 테스트

웹앱(`http://<app_name>azurewebsites.net`)을 찾아서 작동하는지 확인합니다. 

![웹앱 포트 구성 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>웹앱 변경 및 다시 배포

로컬 Git 리포지토리에서 app/templates/app/index.html을 엽니다. 첫 번째 HTML 요소를 찾아서 변경합니다.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Python 파일을 수정하고 저장하면 새 Docker 이미지를 다시 빌드하고 푸시해야 합니다. 그런 다음 변경 내용을 적용하려면 웹앱을 다시 시작합니다. 이 자습서에서는 이전에 사용한 동일한 명령을 사용합니다. [Docker 파일에서 이미지 빌드](#build-the-image-from-the-docker-file) 및 [Docker 허브에 Docker 이미지 푸시](#push-the-docker-image-to-docker-hub) 섹션을 참조할 수 있습니다. [웹앱 테스트](#test-the-web-app)의 지침에 따라 웹앱을 테스트합니다.

## <a name="connect-to-web-app-for-containers-using-ssh"></a>SSH를 사용하여 컨테이너용 Web App에 연결

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 사용자 지정 Docker 이미지가 SSH를 지원하도록 하려면 Dockerfile에 빌드해야 합니다. Docker 파일 자체에서 SSH를 사용하도록 설정합니다. SSH 지침은 이미 샘플 dockerfile에 추가되었으므로 고유한 사용자 지정 이미지를 사용하여 이러한 지침을 따를 수 있습니다.

* `apt-get`을 호출하고 루트 계정의 암호를 `"Docker!"`로 설정하는 [RUN](https://docs.docker.com/engine/reference/builder/#run) 지침입니다.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > 이 구성을 사용하면 컨테이너에 대한 외부 연결이 허용되지 않습니다. Kudu/SCM 사이트를 통해서만 SSH를 사용할 수 있습니다. Kudu/SCM 사이트는 게시 자격 증명으로 인증됩니다.

* [sshd_config](http://man.openbsd.org/sshd_config) 파일을 */etc/ssh/* 디렉터리에 복사하도록 Docker 엔진에 지시하는 [COPY](https://docs.docker.com/engine/reference/builder/#copy) 지침입니다. 구성 파일은 [이 sshd_config 파일](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config)을 기반으로 해야 합니다.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *sshd_config* 파일에 다음이 항목되어야 합니다. 
    > * `Ciphers`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`aes128-cbc,3des-cbc,aes256-cbc`
    > * `MACs`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`hmac-sha1,hmac-sha1-96`

* 컨테이너에서 2222 포트를 노출하는 [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 지침입니다. 루트 암호를 알고 있더라도 인터넷에서 포트 2222에 액세스할 수 없습니다. 개인 가상 네트워크의 브리지 네트워크 내에 있는 컨테이너에서 액세스할 수 있는 내부 전용 포트입니다. 그런 후에 명령은 SSH 구성 세부 정보를 복사하고 `ssh` 서비스를 시작합니다.

    ```docker
    EXPOSE 8000 2222
    ```

* /bin 디렉터리의 셸 스크립트를 사용하여 [ssh 서비스를 시작](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)해야 합니다.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>컨테이너에 대한 SSH 연결 열기

Web App for Containers는 컨테이너에 대한 외부 연결을 허용하지 않습니다. SSH는 `https://<app_name>.scm.azurewebsites.net`에서 액세스할 수 있는 Kudu 사이트를 통해서만 사용할 수 있습니다.

연결하려면 `https://<app_name>.scm.azurewebsites.net/webssh/host`로 이동하고 Azure 계정으로 로그인합니다.

그런 다음 대화형 콘솔을 표시하는 페이지로 리디렉션됩니다. 

특정 응용 프로그램이 컨테이너에서 실행되고 있는지를 확인하려고 합니다. 컨테이너를 검사하고 실행 중인 프로세스를 확인하려면 프롬프트에서 `top` 명령을 실행합니다.

```bash
top
```

`top` 명령은 컨테이너에서 실행 중인 모든 프로세스를 노출합니다.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

축하합니다! Web App for Containers에 사용자 지정 Docker 이미지가 구성되었습니다.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Docker 허브의 개인 이미지 사용(선택 사항)

[웹앱 만들기](#create-a-web-app)에서 Docker 허브에 대한 이미지를 `az webapp create` 명령에 지정했습니다. 공용 이미지에는 이것으로 충분합니다. 개인 이미지를 사용하려면 Azure 웹앱에서 Docker 계정 ID와 암호를 구성해야 합니다.

Cloud Shell에서 `az webapp create` 명령 뒤에 [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set)를 지정합니다. *\<app_name>*, _\<docker-id>_ 및 _\<password>_ 를 Docker ID 및 암호로 바꿉니다.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

명령은 JSON 문자열에서 다음과 같은 출력을 표시하여 구성 변경에 성공했는지 보여줍니다.

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>개인 레지스트리에서 Docker 이미지 사용(선택 사항)

이 섹션에서는 Web App for Containers의 개인 레지스트리에서 Docker 이미지를 사용하는 방법에 대해 알아보고 Azure Container Registry를 예로 사용합니다. 다른 개인 레지스트리를 사용하기 위한 단계도 비슷합니다. 

Azure Container Registry는 개인 이미지를 호스트하기 위해 Azure에서 관리되는 Docker 서비스입니다. 배포는[Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) 및 Web App for Containers를 포함한 모든 유형이 될 수 있습니다. 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

Cloud Shell에서 [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az_acr_create) 명령을 사용하여 Azure Container Registry를 만듭니다. 이름, 리소스 그룹 및 SKU의 `Basic`을 전달합니다. 사용 가능한 SKU는 `Classic`, `Basic`, `Standard`, `Premium`입니다.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

컨테이너를 만들면 다음과 같은 출력을 생성합니다.

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Azure Container Registry에 로그인

이미지를 레지스트리로 푸시하려면 레지스트리가 푸시를 수락하도록 자격 증명을 제공해야 합니다. Cloud Shell에서 [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az_acr_show) 명령을 사용하여 이러한 자격 증명을 검색할 수 있습니다. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

이 명령은 사용자 이름으로 사용할 수 있는 두 개의 암호를 드러냅니다.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

로컬 터미널 창에서 `docker login` 명령을 사용하여 Azure Container Registry에 로그인합니다. 서버 이름은 로그인하는 데 필요합니다. `{azure-container-registry-name>.azurecr.io` 양식을 사용합니다. 프롬프트에서 콘솔에 암호를 입력합니다.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

로그인이 성공했다고 확인합니다. 

### <a name="push-an-image-to-azure-container-registry"></a>Azure Container Registry에 이미지 푸시

> [!NOTE]
> 자신의 이미지를 사용하는 경우 다음과 같이 이미지에 태그를 지정합니다.
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

`docker push` 명령을 사용하여 이미지를 푸시합니다. 레지스트리의 이름 뒤에 이미지 이름과 태그를 붙여 이미지에 대한 태그를 지정합니다.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

ACR 저장소를 나열하여 푸시가 레지스트리에 대한 컨테이너에 성공적으로 추가되었는지 확인합니다. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

레지스트리에서 이미지를 나열하면 `mydockerimage`가 레지스트리에 위치한다고 확인합니다.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Azure Container Registry(또는 개인 레지스트리)에서 이미지를 사용하도록 Web App 구성

Azure Container Registry에 저장된 컨테이너를 실행하도록 Web App for Containers를 구성할 수 있습니다. Azure Container Registry를 사용하는 것이 개인 레지스트리를 사용하는 것과 같으므로, 사용자 고유의 개인 레지스트리를 사용해야 하는 경우 이 작업을 완료하는 단계도 비슷합니다.

Cloud Shell에서 [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show)를 실행하여 Azure Container Registry의 사용자 이름과 암호를 표시합니다. 다음 단계에서 웹앱을 구성하는 데 사용할 수 있도록 사용자 이름 및 하나의 암호를 복사합니다.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

Cloud Shell에서 [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) 명령을 실행하여 사용자 지정 Docker 이미지를 웹앱에 할당합니다. *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ 및 _\<password>_ 를 바꿉니다. Azure Container Registry의 경우 *\<docker-registry-server-url>* 은 `https://<azure-container-registry-name>.azurecr.io` 형식입니다. Docker 허브 외에 사용하는 레지스트리가 있을 경우 이미지 이름이 레지스트리의 FQDN(정규화된 도메인 이름)으로 시작해야 합니다. Azure Container Registry의 경우 `<azure-container-registry>.azurecr.io/mydockerimage`와 유사합니다. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://`가 *\<docker-registry-server-url>* 에 필요합니다.
>

명령은 JSON 문자열에서 다음과 같은 출력을 표시하여 구성 변경에 성공했는지 보여줍니다.

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드](tutorial-docker-python-postgresql-app.md)
