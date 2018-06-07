---
title: VS Code를 사용하여 클라우드에 Kubernetes Node.js 개발 환경 만들기 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스가 있는 Kubernetes 개발 환경을 빠르게 만듭니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: deb651170b0fd58f8c89b591f3e42b5b629f4095
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361475"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Azure Dev Spaces에서 Node.js를 사용하여 시작

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

이제 Azure에서 Kubernetes 기반 개발 환경을 만들 준비가 되었습니다.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Azure CLI 설치
Azure Dev Spaces에는 최소한의 로컬 컴퓨터 설정이 필요합니다. 대부분의 개발 환경 구성은 클라우드에 저장되며 다른 사용자와 공유할 수 있습니다. [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 다운로드하고 실행하여 시작합니다.

> [!IMPORTANT]
> Azure CLI가 이미 설치되어 있는 경우 2.0.32 버전 이상을 사용하고 있는지 확인합니다.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

클러스터가 만들어질 때까지 기다리는 동안 코드 작성을 시작할 수 있습니다.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Kubernetes에 Node.js 컨테이너 만들기

이 섹션에서는 Node.js 웹앱을 만들어 Kubernetes의 컨테이너에서 실행합니다.

### <a name="create-a-nodejs-web-app"></a>Node.js 웹앱 만들기
https://github.com/Azure/dev-spaces로 이동하여 GitHub에서 코드를 다운로드하고, **복제 또는 다운로드**를 선택하여 GitHub 리포지토리를 로컬 환경에 다운로드합니다. 이 가이드에서 사용할 코드는 `samples/nodejs/getting-started/webfrontend`에 있습니다.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>콘텐츠 파일 업데이트
Azure Dev Spaces는 Kubernetes에서 단순히 코드만 실행하는 것이 아니라, 코드 변경 내용이 클라우드의 Kubernetes 환경에 적용되는지를 빠르고 반복적으로 확인할 수 있게 합니다.

1. `./public/index.html` 파일을 찾고 HTML로 편집합니다. 예를 들어 페이지의 배경색을 파란색 음영으로 변경합니다.

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. 파일을 저장합니다. 잠시 후 터미널 창에 실행 중인 컨테이너의 파일이 업데이트되었다는 메시지가 표시됩니다.
1. 브라우저로 이동하여 페이지를 새로 고칩니다. 업데이트된 색이 표시됩니다.

어떻게 된 건가요? HTML 및 CSS와 같은 콘텐츠 파일을 편집하면 Node.js 프로세스를 다시 시작할 필요가 없으므로, `azds up` 명령이 실행되면 수정된 콘텐츠 파일이 Azure에서 실행 중인 컨테이너에 직접 자동으로 동기화되어 콘텐츠의 편집 내용을 빠르게 확인할 수 있습니다.

### <a name="test-from-a-mobile-device"></a>모바일 장치에서 테스트
모바일 장치에서 웹앱을 열면 UI가 소형 장치에 제대로 표시되지 않습니다.

이 문제를 해결하려면 `viewport` 메타 태그를 추가합니다.
1. `./public/index.html` 파일을 엽니다.
1. 기존 `head` 요소에 `viewport` 메타 태그를 추가합니다.

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. 파일을 저장합니다.
1. 장치의 브라우저를 새로 고칩니다. 이제 렌더링된 웹앱이 제대로 표시됩니다. 

이는 앱을 사용할 장치를 테스트할 때까지 일부 문제가 발견되지 않는 경우의 예입니다. Azure Dev Spaces를 사용하면 코드를 빠르게 반복하여 대상 장치의 변경 내용에 대한 유효성을 검사할 수 있습니다.

### <a name="update-a-code-file"></a>코드 파일 업데이트
서버 쪽 코드 파일을 업데이트하려면 Node.js 앱을 다시 시작해야 하므로 약간의 작업이 더 필요합니다.

1. 터미널 창에서 `Ctrl+C`를 눌러 `azds up`을 중지합니다.
1. `server.js`라는 코드 파일을 열고, 서비스의 hello 메시지를 편집합니다. 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. 파일을 저장합니다.
1. 터미널 창에서 `azds up`을 실행합니다. 

이렇게 하면 컨테이너 이미지가 다시 빌드되고 Helm 차트가 다시 배포됩니다. 브라우저 페이지를 다시 로드하여 코드 변경 내용이 적용되는지 확인합니다.

하지만 코드를 개발하는 데 *훨씬 빠른 방법*도 있으며 다음 섹션에서 살펴보겠습니다. 

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>AZDS 디버그 구성 선택
1. [디버그] 보기를 열려면 VS Code 측면의 **작업 막대**에서 [디버그] 아이콘을 클릭합니다.
1. 활성 디버그 구성으로 **시작 프로그램(AZDS)** 을 선택합니다.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> 명령 팔레트에 Azure Dev Spaces 명령이 표시되지 않으면 [Azure Dev Spaces용 VS Code 확장이 설치되었는지](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code) 확인합니다.

### <a name="debug-the-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅
**F5** 키를 눌러 Kubernetes에서 코드를 디버그합니다!

`up` 명령과 마찬가지로 디버깅을 시작할 때 코드가 개발 환경에 동기화되고 컨테이너가 빌드되어 Kubernetes에 배포됩니다. 이번에는 디버거가 원격 컨테이너에 연결됩니다.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

`server.js`의 `app.get('/api'...` 내와 같이 서버 쪽 코드 파일에 중단점을 설정합니다. 브라우저 페이지를 새로 고치거나 '다시 말하기' 단추를 누르면, 중단점을 맞추고 코드를 단계별로 실행할 수 있습니다.

호출 스택, 지역 변수, 예외 정보 등과 같은 코드가 로컬에서 실행되는 경우와 마찬가지로 디버그 정보에 대한 모든 액세스 권한을 갖습니다.

### <a name="edit-code-and-refresh-the-debug-session"></a>코드 편집 및 디버그 세션 새로 고침
디버거를 활성화한 상태에서 코드를 편집합니다(예: hello 메시지를 다시 수정).

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

파일을 저장하고, **디버그 작업 창**에서 **새로 고침** 단추를 클릭합니다. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

코드를 편집할 때마다 상당한 시간이 걸리는 새 컨테이너 이미지를 다시 빌드 및 다시 배포하는 대신, Azure Dev Spaces는 디버그 세션 사이에서 Node.js 프로세스를 다시 시작하여 빠른 편집/디버그 루프를 제공합니다.

브라우저에서 웹앱을 새로 고치거나, *다시 말하기* 단추를 누릅니다. UI에 사용자 지정 메시지가 표시됩니다.

### <a name="use-nodemon-to-develop-even-faster"></a>NodeMon을 사용하여 더 빠르게 개발
*Nodemon*은 Node.js 개발자가 빠른 개발을 위해 사용하는 인기 있는 도구입니다. 서버 쪽 코드를 편집할 때마다 노드 프로세스를 수동으로 다시 시작하는 대신, 개발자는 종종 *nodemon*에서 파일 변경을 모니터링하고 서버 프로세스를 자동으로 다시 시작하도록 노드 프로젝트를 구성합니다. 이러한 작업 스타일에서는 개발자가 코드를 편집한 후 브라우저를 새로 고치기만 하면 됩니다.

Azure Dev Spaces를 사용하면 로컬로 개발할 때 사용하는 것과 동일한 개발 워크플로를 많이 사용할 수 있습니다. 이를 설명하기 위해 `webfrontend` 프로젝트 샘플은 *nodemon*을 사용하도록 구성되었습니다(`package.json`에서 개발 종속성으로 구성됨).

다음 단계를 수행합니다.
1. VS Code 디버거를 중지합니다.
1. VS Code 측면의 **작업 막대**에서 [디버그] 아이콘을 클릭합니다. 
1. 활성 디버그 구성으로 **연결(AZDS)** 을 선택합니다.
1. F5 키를 누릅니다.

이 구성에서 컨테이너는 *nodemon*을 시작하도록 구성됩니다. 서버 코드를 편집하면 로컬로 개발할 편집할 때와 마찬가지로 *nodemon*에서 노드 프로세스를 자동으로 다시 시작합니다. 
1. `server.js`에서 hello 메시지를 다시 편집하고 파일을 저장합니다.
1. 브라우저를 새로 고치거나 *다시 말하기* 단추를 클릭하여 변경 내용이 적용되는지 확인합니다!

**이제 코드를 빠르게 반복하고 Kubernetes에서 직접 디버그하는 방법을 사용할 수 있습니다!** 다음으로, 두 번째 컨테이너를 만들고 호출하는 방법을 살펴보겠습니다.

## <a name="call-a-service-running-in-a-separate-container"></a>별도의 컨테이너에서 실행되는 서비스 호출

이 섹션에서는 두 번째 서비스인 `mywebapi`를 만들고 `webfrontend`에서 이 서비스를 호출하도록 합니다. 각 서비스는 별도의 컨테이너에서 실행됩니다. 그런 다음, 두 컨테이너 모두에서 디버그합니다.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>*mywebapi* 샘플 코드 열기
이 가이드에서 사용할 `mywebapi` 샘플 코드가 이미 `samples` 폴더에 있어야 합니다. 그렇지 않으면 https://github.com/Azure/dev-spaces로 이동하고 **복제 또는 다운로드**를 선택하여 GitHub 리포지토리를 다운로드합니다. 이 섹션에서 사용할 코드는 `samples/nodejs/getting-started/mywebapi`에 있습니다.

### <a name="run-mywebapi"></a>*mywebapi* 실행
1. *별도의 VS Code 창*에서 `mywebapi` 폴더를 엽니다.
1. F5 키를 누르고, 서비스가 빌드되고 배포될 때까지 기다립니다. VS Code 디버그 막대가 표시되면 준비가 되었음을 알 수 있습니다.
1. 엔드포인트 URL을 적어둡니다. 이는 http://localhost:\<portnumber\>과 비슷합니다. **팁: VS Code 상태 표시줄에 클릭 가능한 URL이 표시됩니다.** 컨테이너가 로컬로 실행되는 것처럼 보이지만, 실제로는 Azure의 개발 환경에서 실행되고 있습니다. localhost 주소를 사용하는 이유는 `mywebapi`에서 공용 엔드포인트를 정의하지 않았고 이로 인해 Kubernetes 인스턴스 내에서만 액세스할 수 있기 때문입니다. 편의상 로컬 컴퓨터에서 개인 서비스와 쉽게 상호 작용할 수 있도록 Azure Dev Spaces는 Azure에서 실행되는 컨테이너에 대한 임시 SSH 터널을 만듭니다.
1. `mywebapi`에서 준비가 되면 브라우저를 localhost 주소로 엽니다. `mywebapi` 서비스의 응답("Hello from mywebapi(mywebapi에서 보낸 Hello)")이 표시됩니다.


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend*에서 *mywebapi*로 요청
이제 `mywebapi`에 요청하는 코드를 `webfrontend`에 작성해 보겠습니다.
1. `webfrontend`에 대한 VS Code 창으로 전환합니다.
1. `server.js`의 맨 위에 다음 코드 줄을 추가합니다.
    ```javascript
    var request = require('request');
    var propagateHeaders = require('./propagateHeaders');
    ```

3. *GET 처리기에 대한 코드를*바꿉니다`/api`. 요청을 처리할 때는 `mywebapi`를 차례로 호출한 다음, 두 서비스의 결과를 반환합니다.

    ```javascript
    app.get('/api', function (req, res) {
        request({
            uri: 'http://mywebapi',
            headers: propagateHeaders.from(req) // propagate headers to outgoing requests
        }, function (error, response, body) {
            res.send('Hello from webfrontend and ' + body);
        });
    });
    ```

Kubernetes의 DNS 서비스 검색을 사용하여 서비스를 `http://mywebapi`로 참조하는 방법에 주의합니다. **개발 환경의 코드는 프로덕션 환경에서 실행되는 것과 동일한 방식으로 실행됩니다**.

위의 코드 예제에서는 `propagateHeaders`라는 도우미 모듈을 사용합니다. 이 도우미는 `azds prep`을 실행했을 때 코드 폴더에 추가되었습니다. `propagateHeaders.from()` 함수는 기존 http.IncomingMessage 개체의 특정 헤더를 나가는 요청의 헤더 개체로 전파합니다. 나중에 이를 통해 공동 작업 개발 팀에 어떻게 도움이 되는지 살펴보겠습니다.

### <a name="debug-across-multiple-services"></a>여러 서비스에서 디버깅
1. 이 시점에서 `mywebapi`는 디버거가 연결된 상태로 계속 실행되고 있습니다. 그렇지 않으면 `mywebapi` 프로젝트에서 F5 키를 누릅니다.
1. 기본 `/` GET 처리기에 중단점을 설정합니다.
1. `webfrontend` 프로젝트에서 GET 요청을 `http://mywebapi`로 보내기 바로 전에 중단점을 설정합니다.
1. `webfrontend` 프로젝트에서 F5 키를 누릅니다.
1. 웹앱을 열고 두 서비스에서 코드를 단계별로 실행합니다. 웹앱에는 두 서비스로 연결된 "Hello from webfrontend and Hello from mywebapi.(webfrontend에서 보낸 Hello 및 mywebapi에서 보낸 Hello입니다.)"라는 메시지가 표시됩니다.

모두 완료되었습니다! 이제 각 컨테이너를 개별적으로 개발하고 배포할 수 있는 다중 컨테이너 응용 프로그램이 있습니다.

## <a name="learn-about-team-development"></a>팀 개발 알아보기

[!INCLUDE[](includes/team-development-1.md)]

이제 작동을 확인합니다.
1. `mywebapi`에 대한 VS Code 창으로 이동하고, 기본 `/` GET 처리기에 대한 코드를 편집합니다. 예를 들어 다음과 같습니다.

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]




