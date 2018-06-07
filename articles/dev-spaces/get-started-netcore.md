---
title: .NET Core 및 VS Code를 사용하여 클라우드에서 Kubernetes 개발 환경 만들기 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: a57118feb85a010e38d73b758ebfb84d1cc463fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361254"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Azure Dev Spaces에서 .NET Core를 사용하여 시작

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

이제 Azure에서 Kubernetes 기반 개발 환경을 만들 준비가 되었습니다.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Azure CLI 설치
Azure Dev Spaces에는 최소한의 로컬 컴퓨터 설정이 필요합니다. 개발 환경의 구성은 대부분 클라우드에 저장되며 다른 사용자와 공유할 수 있습니다. 먼저 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 다운로드하고 실행하여 시작합니다. 

> [!IMPORTANT]
> Azure CLI가 이미 설치되어 있는 경우 2.0.32 이상 버전을 사용하고 있는지 확인합니다.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

클러스터가 생성되기를 기다리는 동안 코드 개발을 시작할 수 있습니다.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기
[.NET Core](https://www.microsoft.com/net)가 설치되어 있는 경우 `webfrontend` 폴더에 ASP.NET Core 웹앱을 신속하게 만들 수 있습니다.
    
```cmd
dotnet new mvc --name webfrontend
```

또는 https://github.com/Azure/dev-spaces로 이동하여 **GitHub에서 샘플 코드를 다운로드**하고 **복제 또는 다운로드**를 선택하여 GitHub 리포지토리를 로컬 환경으로 다운로드합니다. 이 가이드의 코드는 `samples/dotnetcore/getting-started/webfrontend`에 있습니다.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>콘텐츠 파일 업데이트
Azure Dev Spaces는 Kubernetes에서 단순히 코드를 실행하는 것이 아니라, 클라우드의 Kubernetes 환경에서 코드 변경 내용을 신속하고 반복적으로 확인할 수 있게 해주는 것입니다.

1. `./Views/Home/Index.cshtml` 파일을 찾고 이 HTML 파일을 편집합니다. 예를 들어 `<h2>Application uses</h2>`를 읽는 행 70을 `<h2>Hello k8s in Azure!</h2>`로 변경합니다.
1. 파일을 저장합니다. 잠시 후, 터미널 창에 실행 중인 컨테이너의 파일이 업데이트되었다는 메시지가 표시됩니다.
1. 브라우저로 이동하여 페이지를 새로 고칩니다. 웹 페이지에 업데이트된 HTML이 표시되어야 합니다.

어떻게 된 건가요? HTML 및 CSS와 같은 콘텐츠 파일을 편집하는 경우 .NET Core 웹앱에서 다시 컴파일을 수행할 필요가 없기 때문에 활성 `azds up` 명령은 수정된 콘텐츠 파일을 Azure의 실행 중인 컨테이너에 자동으로 동기화하므로 콘텐츠 편집 내용을 바로 볼 수 있습니다.

## <a name="update-a-code-file"></a>코드 파일 업데이트
.NET Core 앱이 업데이트된 응용 프로그램 이진 파일을 다시 빌드하고 생성해야 하기 때문에 코드 파일을 업데이트하려면 작업이 좀 더 필요합니다.

1. 터미널 창에서 `Ctrl+C`(`azds up` 중지)를 누릅니다.
1. `Controllers/HomeController.cs`라는 코드 파일을 열고, 정보 페이지(`ViewData["Message"] = "Your application description page.";`)에 표시될 메시지를 편집합니다.
1. 파일을 저장합니다.
1. 터미널 창에서 `azds up`를 실행합니다. 

이 명령은 컨테이너 이미지를 다시 빌드하고 Helm 차트를 다시 배포합니다. 실행 중인 응용 프로그램에서 코드 변경 내용을 적용하려면 웹앱의 정보 메뉴로 이동합니다.


하지만 코드 개발에 사용할 수 있는 *훨씬 더 빠른 방법*이 있으며, 이에 대해서는 다음 섹션에서 살펴볼 예정입니다. 

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>AZDS 디버그 구성 선택
1. 디버그 보기를 열려면 VS Code 측면의 **활동 표시줄**에서 디버그 아이콘을 클릭합니다.
1. 활성 디버그 구성으로 **.NET Core 시작(AZDS)** 을 선택합니다.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> 명령 팔레트에 Azure Dev Spaces 명령이 보이지 않으면 Azure Dev Spaces용 VS Code 확장 프로그램을 설치했는지 확인합니다. VS Code에서 연 작업 영역이 azds.yaml을 포함하는 폴더인지 확인합니다.


### <a name="debug-the-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅
**F5** 키를 눌러 Kubernetes에서 코드를 디버깅하세요.

`up` 명령과 마찬가지로 코드가 개발 환경에 동기화되고 컨테이너가 빌드되어 Kubernetes에 배포됩니다. 물론 이번에는 디버거가 원격 컨테이너에 연결됩니다.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

예를 들어 `Controllers/HomeController.cs` 원본 파일의 `Index()` 함수 내에서 서버 측 코드 파일에 중단점을 설정합니다. 브라우저 페이지를 새로 고치면 중단점에 도달합니다.

코드가 로컬에서 실행되는 경우처럼 호출 스택, 지역 변수, 예외 정보 등과 같은 디버그 정보에 대한 전체 액세스 권한이 있습니다.

### <a name="edit-code-and-refresh"></a>코드 편집 및 새로 고침
디버거를 활성화한 상태에서 코드를 편집합니다. 예를 들어 `Controllers/HomeController.cs`에서 정보 페이지의 메시지를 수정합니다. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

파일을 저장하고, **디버그 작업 창**에서 **새로 고침** 단추를 클릭합니다. 

![](media/get-started-netcore/debug-action-refresh.png)

코드 편집이 완료될 때마다 상당한 시간이 소요되는 새 컨테이너 이미지 다시 빌드 및 다시 배포 작업을 수행하는 대신 Azure Dev Spaces는 기존 컨테이너 내에서 코드를 점진적으로 다시 컴파일하여 더 빠른 편집/디버그 루프를 제공합니다.

브라우저에서 웹앱을 새로 고치고 정보 페이지로 이동합니다. 사용자 지정 메시지가 UI에 표시되어야 합니다.

**이제 코드를 신속하게 반복하고 Kubernetes에서 직접 디버깅할 수 있는 방법이 있습니다.** 다음으로, 두 번째 컨테이너를 만들고 호출하는 방법을 살펴보겠습니다.

## <a name="call-a-service-running-in-a-separate-container"></a>별도의 컨테이너에서 실행되는 서비스 호출

이 섹션에서는 두 번째 서비스인 `mywebapi`를 만들고 `webfrontend`에서 이 서비스를 호출합니다. 각 서비스는 별도의 컨테이너에서 실행됩니다. 그런 다음, 두 컨테이너 모두에서 디버그합니다.

![여러 컨테이너](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>*mywebapi* 샘플 코드 다운로드
이제 GitHub 리포지토리에서 샘플 코드를 다운로드해 보겠습니다. https://github.com/Azure/dev-spaces로 이동하고 **복제 또는 다운로드**를 선택하여 GitHub 리포지토리를 다운로드합니다. 이 섹션에서 사용할 코드는 `samples/dotnetcore/getting-started/mywebapi`에 있습니다.

### <a name="run-mywebapi"></a>*mywebapi* 실행
1. *별도의 VS Code 창*에서 `mywebapi` 폴더를 엽니다.
1. F5 키를 누르고, 서비스가 빌드되고 배포될 때까지 기다립니다. VS Code 디버그 막대가 표시되면 준비가 되었음을 알 수 있습니다.
1. 엔드포인트 URL을 적어둡니다. 이는 http://localhost:\<portnumber\>과 비슷합니다. **팁: VS Code 상태 표시줄에 클릭 가능한 URL이 표시됩니다.** 컨테이너가 로컬에서 실행되는 것처럼 보일 수도 있지만, 실제로는 Azure의 개발 환경에서 실행됩니다. localhost 주소를 사용하는 이유는 `mywebapi`에서 공용 엔드포인트를 정의하지 않았고 이로 인해 Kubernetes 인스턴스 내에서만 액세스할 수 있기 때문입니다. 편의상 로컬 컴퓨터에서 개인 서비스와 쉽게 상호 작용할 수 있도록 Azure Dev Spaces는 Azure에서 실행되는 컨테이너에 대한 임시 SSH 터널을 만듭니다.
1. `mywebapi`에서 준비가 되면 브라우저를 localhost 주소로 엽니다. URL에 `/api/values`를 추가하여 `ValuesController`에 대한 기본 GET API를 호출합니다. 
1. 모든 단계가 성공적으로 완료되면 `mywebapi` 서비스의 응답이 표시될 수 있습니다.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend*에서 *mywebapi*로 요청
이제 `mywebapi`에 요청하는 코드를 `webfrontend`에 작성해 보겠습니다.
1. `webfrontend`에 대한 VS Code 창으로 전환합니다.
1. About 메서드에 대한 코드를 *바꿉니다*.

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
        // headers in the incoming request to any outgoing requests
        using (var client = new HeaderPropagatingHttpClient(this.Request))
        {
            // Call *mywebapi*, and display its response in the page
            var response = await client.GetAsync("http://mywebapi/api/values/1");
            ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
        }

        return View();
    }
    ```

Kubernetes의 DNS 서비스 검색을 사용하여 서비스를 `http://mywebapi`로 참조하는 방법에 주의합니다. **개발 환경의 코드는 프로덕션 환경에서 실행되는 것과 동일한 방식으로 실행됩니다**.

위의 코드 예제에서도 `HeaderPropagatingHttpClient` 클래스를 사용합니다. 이 도우미 클래스는 `azds prep`을 실행했을 때 코드 폴더에 추가되었습니다. `HeaderPropagatingHttpClient`는 잘 알려진 `HttpClient` 클래스에서 파생되었으며, 기존 ASP.NET HttpRequest 개체의 특정 헤더를 나가는 HttpRequestMessage 개체로 전파하는 기능을 추가합니다. 나중에 팀 시나리오에서 이 파생된 클래스를 사용하여 더 생산적인 개발 환경을 용이하게 하는 방법을 살펴보겠습니다.


### <a name="debug-across-multiple-services"></a>여러 서비스에서 디버깅
1. 이 시점에서 `mywebapi`는 디버거가 연결된 상태로 계속 실행되고 있습니다. 그렇지 않으면 `mywebapi` 프로젝트에서 F5 키를 누릅니다.
1. `api/values/{id}` GET 요청을 처리하는 `Get(int id)` 메서드에 중단점을 설정합니다.
1. `webfrontend` 프로젝트에서 GET 요청을 `mywebapi/api/values`로 보내기 바로 전에 중단점을 설정합니다.
1. `webfrontend` 프로젝트에서 F5 키를 누릅니다.
1. 웹앱을 호출하고, 두 서비스에서 코드를 단계별로 실행합니다.
1. 웹앱의 [정보] 페이지에는 두 서비스로 연결된 "Hello from webfrontend and Hello from mywebapi.(webfrontend에서 보낸 Hello 및 mywebapi에서 보낸 Hello입니다.)"라는 메시지가 표시됩니다.


모두 완료되었습니다! 이제 각 컨테이너를 개별적으로 개발하고 배포할 수 있는 다중 컨테이너 응용 프로그램이 있습니다.

## <a name="learn-about-team-development"></a>팀 개발 알아보기

[!INCLUDE[](includes/team-development-1.md)]

작동을 확인합니다. `mywebapi`에 대한 VS Code 창으로 이동하고, `string Get(int id)` 메서드에 대한 코드를 편집합니다. 예를 들어 다음과 같습니다.

    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
