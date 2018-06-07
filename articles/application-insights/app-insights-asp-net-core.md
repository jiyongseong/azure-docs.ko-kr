---
title: ASP.NET Core용 Azure Application Insights | Microsoft Docs
description: 응용 프로그램의 가용성, 성능 및 사용 현황을 모니터링합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 2245fcdaa8b7e85ea37e9af9c939cd188c4d7ed9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32157145"
---
# <a name="application-insights-for-aspnet-core"></a>ASP.NET Core용 Application Insights

Azure Application Insights는 코드 수준까지 웹 응용 프로그램의 심층 모니터링을 제공합니다. 웹 응용 프로그램의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다. 또한 사용자가 보고할 때까지 기다리지 않고 응용 프로그램의 오류를 빠르게 식별하고 진단할 수 있습니다.

이 문서에서는 Visual Studio에서 샘플 ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) 응용 프로그램을 만드는 과정과 Azure Application Insights로 모니터링을 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

- NET Core 2.0.0 SDK 이상
- ASP.NET 및 웹 개발 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 버전 15.3 이상

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Visual Studio에서 ASP.NET Core 프로젝트 만들기

1. 마우스 오른쪽 단추를 클릭하고 관리자 권한으로 **Visual Studio 2017**을 시작합니다.
2. **파일** > **새로 만들기** > **프로젝트**를 선택합니다(Ctrl+Shift+N).

   ![Visual Studio 파일, 새 프로젝트 메뉴 스크린샷](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. **Visual C#** 을 확장하고 **.NET Core** > **ASP.NET Core 웹 응용 프로그램**을 선택합니다. **이름** > **솔루션 이름**을 입력하고 **새 Git 리포지토리 만들기**를 선택합니다.

   ![Visual Studio 파일, 새 프로젝트 마법사 스크린샷](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. **.NET Core** > **ASP.NET Core 2.0** **웹 응용 프로그램** > **확인**을 선택합니다.

    ![Visual Studio 파일, 새 프로젝트 선택 메뉴 스크린샷](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Application Insights 원격 분석 추가

1. **프로젝트** > **Application Insights 원격 분석 추가...** 를 선택합니다. (또는 **연결된 서비스**를 마우스 오른쪽 단추로 클릭하고 연결된 서비스 추가를 클릭합니다.)

    ![Visual Studio 파일, 새 프로젝트 선택 메뉴 스크린샷](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. **체험하기**를 선택합니다.

    ![Visual Studio 파일, 새 프로젝트 선택 메뉴 스크린샷](./media/app-insights-asp-net-core/0005-start-free.png)

3. 적절한 **구독** > **리소스** > 월별로 1GB 넘는 컬렉션을 허용할지 여부 > **등록**을 선택합니다.

    ![Visual Studio 파일, 새 프로젝트 선택 메뉴 스크린샷](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>프로젝트 변경 내용

Application Insights는 오버헤드가 매우 낮습니다. Application Insights 원격 분석을 추가하여 프로젝트에 대한 변경 내용을 검토하려면

**보기** > **팀 탐색기**(Ctrl +\, Ctrl+M) > **프로젝트** > **변경**을 선택합니다.

- 총 4개의 변경 내용:

  ![Application Insights를 추가하여 변경된 파일 스크린샷](./media/app-insights-asp-net-core/0007-changes.png)

- 다음과 같은 1개의 새 파일이 만들어집니다.

   **ConnectedService.json**

  ![Application Insights를 추가하여 변경된 파일 스크린샷](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- 다음과 같은 3개의 파일이 수정됩니다.

  **appsettings.json**

   ![Application Insights를 추가하여 변경된 파일 스크린샷](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Application Insights를 추가하여 변경된 파일 스크린샷](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Application Insights를 추가하여 변경된 파일 스크린샷](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>PowerShell 사용한 가상 트랜잭션

앱을 시작한 후, 링크를 수동으로 클릭하는 방식으로 테스트 트래픽을 생성할 수 있습니다. 그러나 PowerShell에서 간단한 가상 트랜잭션을 만드는 것이 도움이 되는 경우가 많습니다.

1. IIS Express를 클릭하여 앱 실행 ![Visual Studio IIS Express 아이콘 스크린샷](./media/app-insights-asp-net-core/0012-iis-express.png)

2. 브라우저 주소 표시줄에서 URL을 복사합니다. 형식 http://localhost:{random 포트 번호}

   ![브라우저의 URL 주소 표시줄 스크린샷](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 다음 PowerShell 루프를 실행하여 테스트 앱에 대해 100개의 가상 트랜잭션을 만듭니다. **localhost:** 다음의 포트 번호가 이전 단계에서 복사한 URL과 일치하도록 수정합니다.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Application Insights 포털 열기

이전 섹션에서 PowerShell을 실행한 후 Application Insights를 시작하여 트랜잭션을 보고 데이터가 수집되고 있는지 확인합니다. 

Visual Studio 메뉴에서 **프로젝트** > **Application Insights** > **Application Insights 포털 열기**를 선택합니다.

   ![Application Insights 개요 스크린샷](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> 위의 예제 스크린샷에서 **라이브 스트림**, **페이지 보기 로드 시간** 및 **실패한 요청**은 현재 수집되지 않습니다. 다음 섹션에서는 이러한 항목의 추가 작업을 안내합니다. **라이브 스트림** 및 **페이지 보기 로드 시간**을 이미 수집하고 있는 경우 **실패한 요청**에 대한 단계만 따릅니다.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>실패한 요청, 라이브 스트림 및 페이지 보기 로드 시간 수집

### <a name="failed-requests"></a>실패한 요청

기술적으로 **실패한 요청**은 수집되지만 아직 발생하지 않았습니다. 이 프로세스를 가속화하기 위해 기존 프로젝트에 실제 예외를 시뮬레이트하는 사용자 지정 예외를 추가할 수 있습니다. **디버깅 중지**(Shift+F5)를 계속하기 전에 Visual Studio에서 앱이 여전히 실행되고 있는 경우 다음을 수행합니다.

1. **솔루션 탐색기**에서 **페이지** > **About.cshtml**을 확장하고 **About.cshtml.cs**를 엽니다.

   ![Visual Studio 솔루션 탐색기 스크린샷](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. ``Message=`` 아래에 예외를 추가하고 파일에 변경 내용을 저장합니다.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![예외 코드 스크린샷](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>라이브 스트림

**Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet 패키지에 대한 ASP.NET Core 업데이트를 사용하여 Application Insights의 라이브 스트림 기능에 액세스하려면 다음을 수행합니다.

Visual Studio에서 **프로젝트** > **NuGet 패키지 관리** > **Microsoft.ApplicationInsights.AspNetCore** > 버전 **2.2.0** > **업데이트**를 선택합니다.

  ![NuGet 패키지 관리자 스크린샷](./media/app-insights-asp-net-core/0017-update-nuget.png)

여러 확인 메시지가 표시되므로 읽은 후 변경에 동의하면 수락합니다.

### <a name="page-view-load-time"></a>페이지 보기 로드 시간

1. Visual Studio에서 **솔루션 탐색기** > **페이지**로 이동합니다. 두 파일 **_Layout.cshtml** 및 **_ViewImports.cshtml**을 수정해야 합니다.

2. **_ViewImports.cshtml**에서 다음을 추가합니다.

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![_ViewImports.cshtml의 코드 변경 스크린샷](./media/app-insights-asp-net-core/00018-view-imports.png)

3. **_Layout.cshtml**에서 ``</head>`` 태그 앞에, 다른 스크립트 이전에 아래의 줄을 추가합니다.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![layout.cshtml의 코드 변경 스크린샷](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>실패한 요청, 페이지 보기 로드 시간, 라이브 스트림 테스트

이전 단계를 완료했으므로 테스트를 수행하고 모든 항목이 제대로 작동하는지 확인할 수 있습니다.

1. IIS Express를 클릭하여 앱 실행 ![Visual Studio IIS Express 아이콘 스크린샷](./media/app-insights-asp-net-core/0012-iis-express.png)

2. **정보** 페이지로 이동하여 테스트 예외를 트리거합니다. (디버그 모드에서 실행되는 경우 Application Insights에서 예외가 선택되기 전에 Visual Studio에서 **계속**을 클릭해야 합니다.)

3. 이전에 나온 시뮬레이트된 PowerShell 트랜잭션 스크립트를 다시 실행합니다(스크립트에서 포트 번호를 조정해야 할 수 있음).

4. Application Insights 개요가 아직 열려 있지 않으면 Visual Studio 메뉴에서 **프로젝트** > **Application Insights** > **Application Insights 포털 열기**를 선택합니다. 

   > [!TIP]
   > 새 트래픽이 아직 표시되지 않으면 **시간 범위**를 선택하고 **새로 고침**을 클릭합니다.

   ![개요 창 스크린샷](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. 라이브 스트림 선택

   ![라이브 메트릭 스트림 스크린샷](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (PowerShell 스크립트가 여전히 실행되는 경우, 라이브 메트릭이 표시되며, 중지된 경우에는 라이브 메트릭이 열린 상태에서 스크립트를 다시 실행해야 합니다.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK 비교

Application Insights 제품 그룹은 [전체 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)와 .Net Core SDK 간의 기능 격차를 최대한 줄이기 위해 최선을 다하고 있습니다. Application Insights용 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 2.2.0 릴리스는 이러한 기능 간격을 많이 좁혔습니다.

[.NET과 .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) 간의 차이점과 균형 요인에 대해 알아보세요.

   | SDK 비교 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **라이브 메트릭**      | **+** |**-** | **+** |
   | **서버 원격 분석 채널** | **+** |**-** | **+**|
   |**적응 샘플링**| **+** | **-** | **+**|
   | **SQL 종속성 호출**     | **+** |**-** | **+**|
   | **성능 카운터*** | **+** | **-**| **-**|

이 컨텍스트의 _성능 카운터_는 프로세서, 메모리 및 디스크 사용률과 같은 [서버 쪽 성능 카운터](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters)를 나타냅니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
[코드를 읽고 참여하기](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>다음 단계
* [사용자 흐름을 탐색](app-insights-usage-flows.md)하여 사용자가 앱을 탐색하는 방식을 이해합니다.
* 예외가 throw되는 시점의 소스 코드 및 변수 상태를 보려면 [스냅숏 컬렉션을 구성](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)합니다.
* [API를 사용](app-insights-api-custom-events-metrics.md) 합니다.
* [가용성 테스트](app-insights-monitor-web-app-availability.md) 는 사용자의 앱을 전 세계에서 지속적으로 확인합니다.
