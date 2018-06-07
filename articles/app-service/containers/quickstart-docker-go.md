---
title: Azure Web App for Containers에 Docker/Go 앱 배포
description: Web Apps for Containers에 Go 응용 프로그램을 실행하는 Docker 이미지를 배포하는 방법입니다.
keywords: Azure App Service, 웹앱, Go, Docker, 컨테이너
services: app-service
author: msangapu
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: b8fb814aa56465f9dce51de29de37f93213bf58f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-a-dockergo-web-app-in-web-app-for-containers"></a>Web App for Containers에 Docker/Go 웹앱 배포

[App Service Linux](app-service-linux-intro.md)는 .NET, PHP, Node.js 등과 같은 언어에 대한 지원을 통해 Linux에 미리 정의된 응용 프로그램 스택을 제공합니다. 또한 사용자 지정 Docker 이미지를 사용하여 Azure에 아직 정의되지 않은 응용 프로그램 스택에 웹앱을 실행할 수도 있습니다. 이 빠른 시작에서는 웹앱을 만들고 Docker 허브에서 Go 이미지를 배포하는 방법을 설명합니다. [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 웹앱을 만듭니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>웹앱 만들기

[az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](../app-service-web-overview.md)을 만듭니다. `<app name>`을 전역적으로 고유한 앱 이름으로 바꾸어야 합니다.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

앞의 명령에서 `--deployment-container-image-name`은 [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/) 공용 Docker 허브 이미지를 가리킵니다.

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>앱으로 이동

```bash
http://<app_name>.azurewebsites.net/hello
```

![Azure에서 실행되는 샘플 앱](media/quickstart-docker-go/hello-world-in-browser.png)

**축하합니다.** Go 응용 프로그램을 실행하는 사용자 지정 Docker 이미지가 Web Apps for Containers에 배포되었습니다.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 Docker 이미지 사용](tutorial-custom-docker-image.md)
