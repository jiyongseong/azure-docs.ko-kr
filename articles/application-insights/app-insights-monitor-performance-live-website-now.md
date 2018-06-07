---
title: "Azure Application Insights로 라이브 ASP.NET 웹앱 모니터링 | Microsoft Docs"
description: "다시 배포하지 않고 웹 사이트의 성능을 모니터링합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: 869ea96072b1492db929c16cfb1e22b0c96bca7d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Application Insights를 사용한 런타임 시 웹앱 계측


코드를 수정하거나 다시 배포할 필요 없이 Azure Application Insights를 사용하여 라이브 웹앱을 계측할 수 있습니다. 앱이 온-프레미스 IIS 서버에서 호스트되는 경우 상태 모니터를 설치합니다. Azure 웹앱이거나 Azure VM에서 실행되는 경우 Azure 제어판의 Application Insights 모니터링에서 전환할 수 있습니다. ([라이브 J2EE 웹앱](app-insights-java-live.md) 및 [Azure Cloud Services](app-insights-cloudservices.md)를 계측하는 방법을 설명하는 별도의 문서도 있습니다.) [Microsoft Azure](http://azure.com) 구독이 필요합니다.

![예제 차트](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Application Insights를 .NET 웹 응용 프로그램에 적용하는 세 가지 경로가 있습니다.

* **빌드 시간:** 웹앱 코드에 [Application Insights SDK를 추가][greenbrown]합니다.
* **실행 시간:** 코드를 다시 빌드하거나 다시 작성하지 않고 아래 설명된 대로 서버에서 웹앱을 계측합니다.
* **모두:** 웹앱 코드에 SDK를 빌드하고 런타임 확장도 적용합니다. 두 옵션의 좋은 점을 선택합니다.

다음은 각 루트의 장점을 요약한 것입니다.

|  | 빌드 시간 | 실행 시간 |
| --- | --- | --- |
| 요청 및 예외 |예 |예 |
| [자세한 예외 정보](app-insights-asp-net-exceptions.md) | |예 |
| [종속성 진단](app-insights-asp-net-dependencies.md) |.NET 4.6+, 간단히 |예, 전체 세부 정보: 결과 코드, SQL 명령 텍스트, HTTP 동사|
| [시스템 성능 카운터](app-insights-performance-counters.md) |예 |예 |
| [사용자 지정 원격 분석에 대 한 API][api] |예 |아니요 |
| [추적 로그 통합](app-insights-asp-net-trace-logs.md) |예 |아니요 |
| [페이지 보기 및 사용자 데이터](app-insights-javascript.md) |예 |아니요 |
| 코드를 다시 빌드해야 함 |예 | 아니요 |


## <a name="monitor-a-live-azure-web-app"></a>라이브 Azure 웹앱 모니터링

응용 프로그램을 Azure 웹 서비스로 실행 중인 경우 모니터링을 켜는 방법은 다음과 같습니다.

* Azure에서 앱 제어판에서 Application Insights를 선택합니다.

    ![Azure 웹앱의 Application Insights 설정](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Application Insights 요약 페이지가 열리면 맨 아래쪽에 있는 링크를 클릭하여 전체 Application Insights 리소스를 엽니다.

    ![Application Insights를 클릭해 갑니다.](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[클라우드 및 VM 앱 모니터링](app-insights-azure.md).

### <a name="enable-client-side-monitoring-in-azure"></a>Azure에서 클라이언트 쪽 모니터링 사용

Azure에서 Application Insights를 사용하도록 설정한 경우 페이지 보기 및 사용자 원격 분석을 추가할 수 있습니다.

1. 설정 > 응용 프로그램 설정 선택
2.  앱 설정 아래에서 새로운 키 값 쌍을 추가합니다. 
   
    키: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    값: `true`
3. 설정을 **저장**하고 앱을 **다시 시작**합니다.

Application Insights JavaScript SDK가 이제 각 웹 페이지에 삽입됩니다.

## <a name="monitor-a-live-iis-web-app"></a>라이브 IIS 웹앱 모니터링

IIS 서버에서 앱이 호스트되는 경우 상태 모니터를 사용하여 Application Insights를 사용하도록 설정합니다.

1. IIS 웹 서버에서 관리자 자격 증명으로 로그인합니다.
2. Application Insights 상태 모니터가 설치되어 있지 않으면 [상태 모니터 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=506648)를 다운로드하고 실행합니다.(또는 [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 실행하고 Application Insights 상태 모니터를 검색합니다.)
3. 상태 모니터에서 모니터링할 설치된 웹 응용 프로그램 또는 웹 사이트를 선택합니다. Azure 자격 증명으로 로그인합니다.

    Application Insights 포털에서 결과를 표시할 리소스를 구성합니다. (일반적으로 새 리소스를 만드는 것이 좋습니다. 이 앱에 대해 [웹 테스트][availability] 또는 [클라이언트 모니터링][client]이 이미 있으면 기존 리소스를 선택합니다.) 

    ![앱과 리소스를 선택합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. IIS를 다시 시작합니다.

    ![대화 상자의 위쪽에 있는 다시 시작을 선택합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    웹 서비스가 잠시 중단됩니다.

## <a name="customize-monitoring-options"></a>모니터링 옵션 사용자 지정

Application Insights를 사용하면 DLL 및 ApplicationInsights.config가 웹앱에 추가됩니다. [이 .config 파일을 편집](app-insights-configuration-with-applicationinsights-config.md)하여 일부 옵션을 변경할 수 있습니다.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>앱을 다시 게시할 때 Application Insights 다시 활성화

앱을 다시 게시하기 전에 [Application Insights를 Visual Studio에서 코드에 추가][greenbrown]하는 것이 좋습니다. 이렇게 하면 자세한 원격 분석을 얻을 수 있고 사용자 지정 원격 분석을 작성하는 기능도 얻을 수 있습니다.

Application Insights를 코드에 추가하지 않고 다시 게시하려는 경우, 배포 과정에서 게시된 웹 사이트의 DLL 및 ApplicationInsights.config가 삭제될 수 있으니 유의하세요. 따라서

1. ApplicationInsights.config를 편집한 경우 앱을 다시 게시하기 전에 복사본을 만듭니다.
2. 앱을 다시 게시합니다.
3. Application Insights 모니터링을 다시 활성화합니다. (적절한 방법 즉, Azure 웹앱 제어판 또는 IIS 호스트의 상태 모니터를 사용합니다.)
4. .config 파일에 수행했던 편집 내용을 복구합니다.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Application Insights의 런타임 구성 문제 해결

### <a name="cant-connect-no-telemetry"></a>연결할 수 없나요? 원격 분석이 없나요?

* 상태 모니터가 작동할 수 있도록 서버 방화벽에서 [필요한 송신 포트](app-insights-ip-addresses.md#outgoing-ports)를 엽니다.

* 상태 모니터를 열고 왼쪽 창에서 응용 프로그램을 선택합니다. "구성 알림" 섹션에 이 응용 프로그램에 대한 진단 메시지가 있는지 확인합니다.

  ![성능 블레이드를 열어 요청, 응답 시간, 종속성 및 기타 데이터를 확인합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* 서버에서 "권한 부족"에 대한 메시지가 표시되는 경우 다음을 시도합니다.
  * IIS 관리자에서 응용 프로그램 풀을 선택하고 **고급 설정**을 연 다음 **프로세스 모델**에서 ID를 확인합니다.
  * 컴퓨터 관리 제어판에서 성능 모니터 사용자 그룹에 이 ID를 추가합니다.
* 서버에 MMA/SCOM(Systems Center Operations Manager)이 설치된 경우 일부 버전이 충돌할 수 있습니다. SCOm과 상태 모니터를 제거한 다음 최신 버전을 다시 설치하세요.
* [문제 해결][qna]을 참조하세요.

## <a name="system-requirements"></a>시스템 요구 사항
Server에서 Application Insights 상태 모니터에 대한 OS 지원:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

최신 SP 및 .NET Framework 4.5 포함

클라이언트 쪽 Windows 7, 8, 8.1 및 10에서, 역시 .NET Framework 4.5 포함

IIS 지원: IIS 7, 7.5, 8, 8.5(IIS 필요)

## <a name="automation-with-powershell"></a>PowerShell을 사용한 Automation
IIS 서버에서 PowerShell을 사용하여 모니터링을 시작하고 중지할 수 있습니다.

먼저 Application Insights 모듈을 가져옵니다.

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

어떤 앱을 모니터링 중인지 확인합니다.

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (선택 사항) 웹앱의 이름입니다.
* 이 IIS 서버에서 각 웹앱(또는 명명된 앱)에 대한 상태를 모니터링하여 Application Insights를 표시합니다.
* 각 앱에 대해 `ApplicationInsightsApplication`을(를) 반환합니다.

  * `SdkState==EnabledAfterDeployment`: 상태 모니터 도구 또는 `Start-ApplicationInsightsMonitoring`에서 앱을 모니터링하고 런타임 시 계측했습니다.
  * `SdkState==Disabled`: 앱이 Application insights에 대해 계측되지 않습니다. 앱을 계측하지 않았거나 상태 모니터 도구 또는 `Stop-ApplicationInsightsMonitoring`을(를) 사용하여 런타임 모니터링이 비활성화되었습니다.
  * `SdkState==EnabledByCodeInstrumentation`: 소스 코드에 SDK를 추가하여 앱을 계측했습니다. 해당 SDK은 업데이트되거나 중지될 수 없습니다.
  * `SdkVersion`은(는) 이 앱을 모니터링하는 데 사용하는 버전을 나타냅니다.
  * `LatestAvailableSdkVersion`은(는) NuGet 갤러리에서 현재 사용할 수 있는 버전을 나타냅니다. 앱을 이 버전으로 업그레이드하려면 `Update-ApplicationInsightsMonitoring`을(를) 사용합니다.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` IIS에서 앱의 이름
* `-InstrumentationKey` 결과를 표시하려는 Application Insights 리소스의 ikey입니다.
* 이 cmdlet은 아직 계측되지 않은 앱에만 영향을 줍니다. 즉, SdkState==NotInstrumented입니다.

    cmdlet은 이미 계측된 앱에 영향을 주지 않습니다. 앱이 빌드 시 코드에 SDK를 추가하거나 런타임 시 이전에 이 cmdlet을 사용하여 계측되었는지 여부는 중요하지 않습니다.

    앱을 계측하는 데 사용한 SDK 버전은 가장 최근에 이 서버에 다운로드된 버전입니다.

    최신 버전을 다운로드하려면 Update-ApplicationInsightsVersion을 사용합니다.
* 성공 시 `ApplicationInsightsApplication`을(를) 반환합니다. 실패한 경우 stderr에 대한 추적을 기록합니다.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` IIS에서 앱의 이름
* `-All` `SdkState==EnabledAfterDeployment`인 이 IIS 서버에서 모든 앱에 대한 모니터링을 중지합니다.
* 지정된 앱의 모니터링을 중지하고 계측을 제거합니다. 실행 시 상태 모니터링 도구 또는 Start-ApplicationInsightsApplication을 사용하여 계측된 앱에서 작동합니다. (`SdkState==EnabledAfterDeployment`)
* ApplicationInsightsApplication을 반환합니다.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: IIS에서 웹앱의 이름
* `-InstrumentationKey`(옵션) 이를 사용하여 앱의 원격 분석이 전송되는 리소스를 변경합니다.
* 이 cmdlet은:
  * 최근에 이 컴퓨터에 다운로드된 SDK 버전으로 명명된 앱을 업그레이드합니다. (`SdkState==EnabledAfterDeployment`인 경우에만 작동)
  * 계측 키를 제공하는 경우 명명된 앱은 해당 키가 있는 리소스에 원격 분석을 전송하도록 다시 구성됩니다. ( `SdkState != Disabled`인 경우 작동)

`Update-ApplicationInsightsVersion`

* 서버에 최신 Application Insights SDK를 다운로드합니다.

## <a name="questions"></a>상태 모니터에 대한 질문

### <a name="what-is-status-monitor"></a>상태 모니터란?

IIS 웹 서버에 설치한 데스크톱 응용 프로그램입니다. 웹앱을 계측하고 구성하는 데 도움이 됩니다. 

### <a name="when-do-i-use-status-monitor"></a>언제 상태 모니터를 사용하나요?

* 이미 실행 중인 경우에라도 IIS 서버에서 실행 중인 모든 웹앱을 계측하려는 경우입니다.
* 컴파일 시 [Application Insights SDK를 사용하여 빌드한](app-insights-asp-net.md) 웹앱에 대한 추가 원격 분석을 사용하도록 설정하려는 경우입니다. 

### <a name="can-i-close-it-after-it-runs"></a>실행한 후에 닫을 수 있나요?

예. 선택한 웹 사이트를 계측한 후에 닫을 수 있습니다.

자체로 원격 분석을 수집하지 않습니다. 웹앱을 구성하고 일부 사용 권한을 설정합니다.

### <a name="what-does-status-monitor-do"></a>상태 모니터의 기능은 무엇인가요?

계측할 상태 모니터에 대한 웹앱을 선택하는 경우:

* 웹앱의 이진 파일 폴더에서 Application Insights 어셈블리 및 .config 파일을 다운로드하고 저장합니다.
* `web.config`을 수정하여 Application Insights HTTP 추적 모듈을 추가합니다.
* CLR 프로파일링을 사용하여 종속성 호출을 수집할 수 있습니다.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>앱을 업데이트할 때마다 상태 모니터를 실행해야 하나요?

증분 방식으로 다시 배포하는 경우에는 아닙니다. 

게시 프로세스에서 '기존 파일 삭제' 옵션을 선택하는 경우 상태 모니터를 다시 실행하여 Application Insights를 구성해야 합니다.

### <a name="what-telemetry-is-collected"></a>어떤 원격 분석이 수집되나요?

상태 모니터를 사용하여 런타임 시에만 계측하는 응용 프로그램의 경우:

* HTTP 요청
* 종속성에 대한 호출
* 예외
* 성능 카운터

컴파일 시 이미 계측된 응용 프로그램의 경우:

 * 프로세스 카운터
 * 종속성 호출(.NET 4.5); 종속성 호출(.NET 4.6)에 값을 반환합니다.
 * 예외 스택 추적 값

[자세히 알아보기](http://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next"></a>다음 단계

원격 분석 보기:

* [메트릭을 탐색하여](app-insights-metrics-explorer.md) 성능 및 사용량을 모니터링합니다.
* [이벤트 및 로그를 검색하여][diagnostic] 문제를 진단합니다.
* [분석](app-insights-analytics.md)을 통해 고급 쿼리를 수행합니다.
* [대시보드를 만듭니다](app-insights-dashboards.md).

원격 분석 더 추가:

* [웹 테스트를 만들어][availability] 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
* [웹 클라이언트 원격 분석을 추가][usage]하여 웹 페이지 코드에서 예외를 확인하고 추적 호출을 삽입합니다.
* [Application Insights SDK를 코드에 추가하여][greenbrown] 추적 및 로그 호출을 삽입할 수 있도록 합니다.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-javascript.md
