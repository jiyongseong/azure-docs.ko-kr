---
title: 클라우드에서 Kubernetes 개발 환경 만들기 | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: 9c9a485a5c59342149027798e118b97b7305c640
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361535"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-nodejs"></a>빠른 시작: Azure Dev Spaces(Node.js)를 사용하여 Kubernetes 개발 환경 만들기


[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

이제 Azure에서 Kubernetes 기반 개발 환경을 만들 준비가 되었습니다.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Azure CLI 설치
Azure Dev Spaces에는 최소한의 로컬 컴퓨터 설정이 필요합니다. 개발 환경의 구성은 대부분 클라우드에 저장되며 다른 사용자와 공유할 수 있습니다. 먼저 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 다운로드하여 실행합니다. 

> [!IMPORTANT]
> Azure CLI가 이미 설치되어 있는 경우 2.0.32 이상 버전을 사용하고 있는지 확인합니다.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

환경이 생성될 때까지 기다리는 동안 코드 작성을 시작할 수 있습니다.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Kubernetes에서 Node.js 컨테이너 만들기

이 섹션에서는 Node.js 웹앱을 만들어 Kubernetes의 컨테이너에서 실행합니다.

### <a name="create-a-nodejs-web-app"></a>Node.js 웹앱 만들기
https://github.com/Azure/dev-spaces로 이동하여 GitHub에서 코드를 다운로드하고 **복제 또는 다운로드**을 선택하여 GitHub 리포지토리를 로컬 환경으로 다운로드합니다. 이 가이드의 코드는 `samples/nodejs/getting-started/webfrontend`에 있습니다.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>콘텐츠 파일 업데이트
Azure Dev Spaces는 Kubernetes에서 단순히 코드를 실행하는 것이 아니라, 클라우드의 Kubernetes 환경에서 코드 변경 내용을 신속하고 반복적으로 확인할 수 있게 해주는 것입니다.

1. `./public/index.html` 파일을 찾고 이 HTML 파일을 편집합니다. 예를 들어, 페이지의 배경색을 파란색 음영으로 변경합니다.

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. 파일을 저장합니다. 잠시 후, 터미널 창에 실행 중인 컨테이너의 파일이 업데이트되었다는 메시지가 표시됩니다.
1. 브라우저로 이동하여 페이지를 새로 고칩니다. 색상 업데이트가 표시되어야 합니다.

어떻게 된 건가요? HTML 및 CSS와 같은 콘텐츠 파일을 편집하면 Node.js 프로세스를 다시 시작할 필요가 없으므로, 활성 `azds up` 명령은 수정된 컨텐츠 파일을 Azure에서 실행 중인 컨테이너에 바로 자동으로 동기화합니다. 따라서 콘텐츠 편집 내용을 빠르게 볼 수 있습니다.

### <a name="test-from-a-mobile-device"></a>모바일 장치에서 테스트
모바일 장치에서 웹앱을 열면 소형 장치에서 UI가 제대로 표시되지 않는 것을 알 수 있습니다.

이 문제를 해결하려면 `viewport` 메타 태그를 추가합니다.
1. `./public/index.html` 파일 열기
1. 기존 `head` 요소에 `viewport` 메타 태그를 추가합니다.

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. 파일을 저장합니다.
1. 장치의 브라우저를 새로 고칩니다. 이제 올바르게 렌더링된 웹앱이 표시됩니다. 

이는 앱을 사용할 장치를 테스트할 때까지 일부 문제가 발견되지 않는 경우의 예입니다. VS Azure Dev Spaces를 사용하면 코드를 빠르게 반복하여 대상 장치의 변경 내용에 대한 유효성을 검사할 수 있습니다.

### <a name="update-a-code-file"></a>코드 파일 업데이트
Node.js 앱을 다시 시작해야 하므로 서버 쪽 코드 파일을 업데이트하려면 약간의 작업이 더 필요합니다.

1. 터미널 창에서 `Ctrl+C`(`azds up` 중지)를 누릅니다.
1. `server.js`라는 코드 파일을 열고, 서비스의 hello 메시지를 편집합니다. 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. 파일을 저장합니다.
1. 터미널 창에서 `azds up`를 실행합니다. 

이렇게 하면 컨테이너 이미지가 다시 빌드되고 Helm 차트가 다시 배포됩니다. 브라우저 페이지를 다시 로드하여 코드 변경 내용이 적용되는지 확인합니다.

하지만 코드 개발에 사용할 수 있는 *훨씬 더 빠른 방법*이 있으며, 이에 대해서는 다음 섹션에서 살펴볼 예정입니다. 

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>AZDS 디버그 구성 선택
1. 디버그 보기를 열려면 VS Code 측면의 **활동 표시줄**에서 디버그 아이콘을 클릭합니다.
1. 활성 디버그 구성으로 **시작 프로그램(AZDS)** 을 선택합니다.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> 명령 팔레트에 Azure Dev Spaces 명령이 보이지 않으면 Azure Dev Spaces용 VS Code 확장 프로그램을 설치했는지 확인합니다.

### <a name="debug-the-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅
**F5** 키를 눌러 Kubernetes에서 코드를 디버깅하세요.

`up` 명령과 마찬가지로 디버깅을 시작할 때 코드가 개발 환경에 동기화되고, 컨테이너가 빌드되어 Kubernetes에 배포됩니다. 이번에는 디버거가 원격 컨테이너에 연결됩니다.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

서버 측 코드 파일(예: `server.js`의 `app.get('/api'...`)에 중단점을 설정합니다. 브라우저 페이지를 새로 고치거나 '다시 말하기' 단추를 누르면 중단점을 맞추고 코드를 단계별로 실행할 수 있습니다.

코드가 로컬에서 실행되는 경우처럼 호출 스택, 지역 변수, 예외 정보 등과 같은 디버그 정보에 대한 전체 액세스 권한이 있습니다.

### <a name="edit-code-and-refresh-the-debug-session"></a>코드 편집 및 디버그 세션 새로 고침
디버거를 활성화한 상태에서 코드를 편집합니다. 예를 들어, hello 메시지를 다시 수정합니다.

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

파일을 저장하고, **디버그 작업 창**에서 **새로 고침** 단추를 클릭합니다. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

코드 편집이 완료될 때마다 상당한 시간이 소요되는 새 컨테이너 이미지 다시 빌드 및 다시 배포 작업을 수행하는 대신 Azure Dev Spaces는 더 빠른 편집/디버그 루프를 제공하기 위해 디버그 세션 간에 Node.js 프로세스를 다시 시작합니다.

브라우저에서 웹앱을 새로 고치거나, *다시 말하기* 단추를 누릅니다. 사용자 지정 메시지가 UI에 표시되어야 합니다.

### <a name="use-nodemon-to-develop-even-faster"></a>NodeMon을 사용하여 더 빠르게 개발
*Nodemon*은 Node.js 개발자가 신속한 개발을 위해 사용하는 인기 있는 도구입니다. 개발자는 서버 쪽 코드 편집을 수행할 때마다 노드 프로젝트를 수동으로 다시 시작하는 대신, *nodemon*이 파일 변경을 모니터링하고 서버 프로세스를 자동으로 다시 시작하도록 노드 프로젝트를 구성하는 경우가 많습니다. 이러한 작업 방식을 따르는 개발자는 코드를 편집한 후 브라우저를 새로 고치기만 하면 됩니다.

Azure Dev Spaces를 사용하면 로컬 개발 시 사용하는 것과 동일한 개발 워크플로를 많이 사용할 수 있습니다. 이를 설명하기 위해 샘플 `webfrontend` 프로젝트는 *nodemon*을 사용하도록 구성되었습니다(`package.json`에서 개발 종속성으로 구성됨).

다음 단계를 수행합니다.
1. VS Code 디버거를 중지합니다.
1. VS Code 측면의 **활동 표시줄**에서 디버그 아이콘을 클릭합니다. 
1. 활성 디버그 구성으로 **연결(AZDS)** 을 선택합니다.
1. F5 키를 누릅니다.

이 구성에서 컨테이너는 *nodemon*을 시작하도록 구성됩니다. 서버 코드를 편집하면 로컬에서 개발할 때와 마찬가지로 *nodemon*에서 노드 프로세스를 자동으로 다시 시작합니다. 
1. `server.js`에서 hello 메시지를 다시 편집하고 파일을 저장합니다.
1. 브라우저를 새로 고치거나 *다시 말하기* 단추를 클릭하여 변경 내용이 적용되는지 확인합니다.

**이제 코드를 신속하게 반복하고 Kubernetes에서 직접 디버깅할 수 있는 방법이 있습니다.**

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [여러 컨테이너 작업 및 팀 개발](get-started-nodejs.md#call-a-service-running-in-a-separate-container)