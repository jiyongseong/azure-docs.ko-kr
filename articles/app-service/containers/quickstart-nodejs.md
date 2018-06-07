---
title: Linux의 Azure App Service에서 Node.js 만들기 | Microsoft Docs
description: 몇 분 안에 Linux의 Azure App Service에서 첫 번째 Node.js Hello World를 배포합니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a2643e65b74f44ee05001d5df26c7c77a430fbb2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>Linux의 Azure App Service에서 Node.js 웹앱 만들기

> [!NOTE]
> 이 문서에서는 Linux의 App Service에 앱을 배포합니다. _Windows_의 App Service에 배포하려면 [Azure에서 Node.js 웹앱 만들기](../app-service-web-get-started-nodejs.md)를 참조하세요.
>

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 사용하여 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작은 기본 제공 이미지를 사용하여 Linux의 App Service에 Node.js 앱을 배포하는 방법을 보여줍니다. [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 기본 제공 이미지로 웹앱을 만들고 Git을 사용하여 웹앱에 Node.js 코드를 배포합니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-nodejs/hello-world-in-browser.png)

Mac, Windows 또는 Linux 컴퓨터를 사용하여 이 자습서의 단계를 수행하면 됩니다. 이 문서의 내용을 다루는 [비디오](#video)를 따라 진행할 수도 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* <a href="https://git-scm.com/" target="_blank">Git 설치</a>
* <a href="https://nodejs.org/" target="_blank">Node.js 및 NPM 설치</a>

## <a name="download-the-sample"></a>샘플 다운로드

컴퓨터의 터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

이 터미널 창을 사용하여 이 빠른 시작의 모든 명령을 실행합니다.

샘플 코드를 포함하는 디렉터리로 변경합니다.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>로컬에서 앱 실행

Node.js HTTP 서버에서 기본 제공을 시작하려면 터미널 창을 열고 `npm start` 스크립트를 사용하여 응용 프로그램을 로컬로 실행합니다.

```bash
npm start
```

웹 브라우저를 열고 `http://localhost:1337`의 샘플 앱으로 이동합니다.

이 페이지에 표시된 샘플 앱에서 **Hello World** 메시지가 표시됩니다.

![로컬로 실행되는 샘플 앱](media/quickstart-nodejs/localhost-hello-world-in-browser.png)

터미널 창에서 **Ctrl+C**를 눌러 웹 서버를 종료합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)]

새로 만든 웹앱으로 이동합니다. _&lt;앱 이름>_을 해당하는 웹앱 이름으로 바꿉니다.

```bash
http://<app name>.azurewebsites.net
```

새로운 웹앱은 다음과 같아야 합니다.

![빈 웹앱 페이지](media/quickstart-nodejs/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)]

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>앱으로 이동

웹 브라우저를 사용하여 배포된 응용 프로그램으로 이동합니다.

```bash
http://<app_name>.azurewebsites.net
```

Node.js 샘플 코드가 기본 제공 이미지가 있는 웹앱에서 실행됩니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-nodejs/hello-world-in-browser.png)

**축하합니다.** Linux의 App Service에 첫 번째 Node.js 앱을 배포했습니다.

## <a name="update-and-redeploy-the-code"></a>코드 업데이트 및 다시 배포

로컬 디렉터리에서 Node.js 앱의 `index.js` 파일을 열고 `response.end` 호출의 인자 텍스트를 다음과 같이 변경합니다.

```nodejs
response.end("Hello Azure!");
```

Git에서 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git commit -am "updated output"
git push azure master
```

배포가 완료되면 **앱으로 이동** 단계에서 열린 브라우저 창으로 다시 전환하고 새로 고침을 누릅니다.

![Azure에서 실행되는 업데이트된 샘플 앱](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>새로운 Azure 웹앱 관리

만든 웹앱을 관리하려면 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>로 이동합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

웹앱의 개요 페이지가 표시됩니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 

![Azure Portal의 App Service 페이지](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

왼쪽 메뉴는 앱 구성을 위한 서로 다른 페이지를 제공합니다. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="video"></a>비디오

>[!VIDEO https://www.youtube.com/embed/S9eqK7xPKqU]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [MongoDB를 사용하는 Node.js](tutorial-nodejs-mongodb-app.md)
