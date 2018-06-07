---
title: Web App for Containers로 Docker 컨테이너 레지스트리에서 연속 배포 - Azure | Microsoft Docs
description: Web App for Containers의 Docker 컨테이너 레지스트리에서 연속 배포를 설정하는 방법입니다.
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Web App for Containers를 사용한 연속 배포

이 자습서에서는 관리되는 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 리포지토리 또는 [Docker 허브](https://hub.docker.com)에서 사용자 지정 컨테이너 이미지에 대한 연속 배포를 구성합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-the-continuous-deployment-feature"></a>연속 배포 기능 사용

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 사용하고 다음 명령을 실행하여 연속 배포 기능을 사용하도록 설정할 수 있습니다.

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

[Azure Portal](https://portal.azure.com/)의 페이지 왼쪽에서 **App Service** 옵션을 선택합니다.

Docker 허브 연속 배포를 구성하려는 앱의 이름을 선택합니다.

**Docker 컨테이너** 페이지에서 **켜기**를 선택한 다음, **저장**을 선택하여 지속적인 배포를 사용하도록 설정합니다.

![앱 설정 스크린샷](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>웹후크 URL을 준비합니다.

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 사용하고 다음 명령을 실행하여 웹후크 URL을 가져옵니다.

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

웹후크 URL에는 `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook` 끝점이 필요합니다.

Azure Portal에서 웹앱 게시 프로필을 다운로드하여 `publishingusername` 및 `publishingpwd`를 구할 수 있습니다.

![웹후크 2를 추가하는 스크린샷](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>웹후크 추가

### <a name="azure-container-registry"></a>Azure Container Registry

1. 레지스트리 포털 페이지에서 **웹후크**를 선택합니다.
2. 새 웹후크를 만들려면 **추가**를 선택합니다. 
3. **웹후크 만들기** 창에서 웹후크에 이름을 지정합니다. 웹후크 URI의 경우 이전 섹션에서 가져온 URL을 입력합니다.

범위를 컨테이너 이미지를 포함하는 리포지토리로 정의해야 합니다.

![웹후크 스크린샷](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

이미지를 업데이트할 때 자동으로 웹앱을 새 이미지로 업데이트합니다.

### <a name="docker-hub"></a>Docker 허브

Docker 허브 페이지에서 **웹후크**를 선택한 다음, **웹후크 만들기**를 선택합니다.

![웹후크 1을 추가하는 스크린샷](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

웹후크 URL의 경우 이전에 가져온 URL을 입력합니다.

![웹후크 2를 추가하는 스크린샷](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

이미지를 업데이트할 때 자동으로 웹앱을 새 이미지로 업데이트합니다.

## <a name="next-steps"></a>다음 단계

* [Linux의 Azure App Service 소개](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Linux의 App Service에서 .NET Core 웹앱 만들기](quickstart-dotnetcore.md)
* [Linux의 App Service에서 Ruby 웹앱 만들기](quickstart-ruby.md)
* [Web App for Containers에 Docker/Go 웹앱 배포](quickstart-docker-go.md)
* [Linux의 Azure App Service에 대한 FAQ](./app-service-linux-faq.md)
* [Azure CLI를 사용하여 Web App for Containers 관리](./app-service-linux-cli.md)
