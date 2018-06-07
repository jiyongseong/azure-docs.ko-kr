---
title: Windows 데스크톱 앱의 사용량 및 성능 모니터링
description: Application Insights를 사용하여 Windows 데스크톱 앱의 사용량 및 성능을 분석합니다.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: ada38fc26f2fce9251ae648302733b04fe4c82ec
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195831"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>클래식 Windows 데스크톱 앱에서 사용량 및 성능 모니터링

온-프레미스 호스팅된 응용 프로그램은 Azure 및 다른 클라우드에서 Application Insights를 충분히 활용할 수 있습니다. 유일한 제한은 Application Insights 서비스와 [통신을 허용](app-insights-ip-addresses.md)해야 한다는 것입니다. UWP(유니버설 Windows 플랫폼) 응용 프로그램 모니터링에는 [Visual Studio App Center](app-insights-mobile-center-quickstart.md)가 좋습니다.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>클래식 Windows 응용 프로그램에서 Application Insights에 원격 분석을 전송하려면
1. [Azure Portal](https://portal.azure.com)에서 [Application Insights 리소스를 만듭니다](app-insights-create-new-resource.md). 응용 프로그램 유형으로 ASP.NET 앱을 선택합니다.
2. 계측 키를 복사합니다. 방금 만든 새 리소스의 필수 드롭다운에서 키를 찾습니다. 
3. Visual Studio에서 앱 프로젝트의 NuGet 패키지를 편집하고 Microsoft.ApplicationInsights.WindowsServer를 추가합니다. (또는 표준 원격 분석 수집 모듈 없이 API를 사용하려면 Microsoft.ApplicationInsights를 선택합니다.)
4. 코드에서 계측 키를 설정합니다.
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *키* `";`
   
    또는 ApplicationInsights.config에서(표준 원격 분석 패키지 중 하나를 설치한 경우).
   
    `<InstrumentationKey>`*키*`</InstrumentationKey>` 
   
    ApplicationInsights.config를 사용하는 경우 솔루션 탐색기에서 해당 속성이 **빌드 작업 = 콘텐츠, 출력 디렉터리로 복사 = 복사**로 설정되도록 합니다.
5. [API를 사용](app-insights-api-custom-events-metrics.md) 하여 원격 분석을 전송합니다.
6. 앱을 실행하고 Azure Portal에서 만든 리소스의 원격 분석을 참조하세요.

## <a name="telemetry"></a>예제 코드
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>다음 단계
* [대시보드 만들기](app-insights-dashboards.md)
* [진단 검색](app-insights-diagnostic-search.md)
* [메트릭 탐색](app-insights-metrics-explorer.md)
* [분석 쿼리 작성](app-insights-analytics.md)

