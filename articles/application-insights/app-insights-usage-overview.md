---
title: Azure Application Insights로 사용 분석 | Microsoft Docs
description: 어떤 사용자가 앱으로 어떤 작업을 수행하는지 이해합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 79f4f77f08653c40591137268b87dc2feeb0acf6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights를 사용하여 사용량 분석

가장 인기 있는 웹 또는 모바일 앱의 기능은 무엇인가요? 사용자가 앱으로 이러한 목표를 달성하고 있나요? 특정 지점에서 나갔다가 나중에 돌아오나요?  [Azure Application Insights](app-insights-overview.md)는 사람들이 사용자의 앱을 사용하는 방식을 잘 이해할 수 있도록 도와줍니다. 앱을 업데이트할 때마다 사용자들에게 얼마나 적절한지 평가할 수 있습니다. 이러한 지식을 바탕으로 다음 개발 주기에 대해 데이터 중심의 결정을 내릴 수 있습니다.

## <a name="send-telemetry-from-your-app"></a>앱에서 원격 분석 보내기

앱 서버 코드와 웹 페이지에 모두 Application Insights를 설치하여 최상의 환경을 얻습니다. 앱의 클라이언트 및 서버 구성 요소는 분석을 위해 Azure Portal로 원격 분석을 다시 보냅니다.

1. **서버 코드:** [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) 또는 [기타](app-insights-platforms.md) 앱에 적합한 모듈을 설치합니다.

    * *서버 코드를 설치하지 않으려면 [Azure Application Insights 리소스를 만들기만](app-insights-create-new-resource.md) 하면 됩니다.*

2. **웹 페이지 코드:** [Azure Portal](https://portal.azure.com)을 열고 앱에 대한 Application Insights 리소스를 연 다음 **시작하기 > Monitor and Diagnose Client-Side(클라이언트 쪽 모니터링 및 진단)** 을 엽니다. 

    ![마스터 웹 페이지의 머리글에 스크립트를 복사합니다.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **모바일 앱 코드:** App Center SDK를 사용하여 앱에서 이벤트를 수집한 다음, 분석을 위해 [이 가이드에 따라](app-insights-mobile-center-quickstart.md) 이러한 이벤트의 복사본을 Application Insights로 보냅니다.

4. **원격 분석 가져오기:** 몇 분 동안 디버그 모드에서 프로젝트를 실행한 다음 Application Insights의 개요 블레이드에서 결과를 찾습니다.

    앱을 게시하여 앱의 성능을 모니터링하고 사용자가 앱으로 수행하는 작업을 확인합니다.

## <a name="include-user-and-session-id-in-your-telemetry"></a>원격 분석에 사용자 및 세션 ID를 포함합니다.
시간이 지남에 따라 사용자를 추적하려면 Application Insights는 식별하는 방법이 필요합니다. 이벤트 도구는 사용자 ID 또는 세션 ID가 필요 없는 유일한 사용 도구입니다.

[이 프로세스](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)를 통해 사용자 및 세션 ID 보내기를 시작합니다.

## <a name="explore-usage-demographics-and-statistics"></a>사용 현황 인구 통계 및 통계 탐색
사람들이 사용자의 앱을 사용하는 경우, 가장 큰 관심을 갖는 페이지, 사용자가 있는 위치 및 사용하는 브라우저 및 운영 체제를 알아봅니다. 

사용자 및 세션 보고서는 페이지 또는 사용자 지정 이벤트를 기준으로 데이터를 필터링하고 위치, 환경 및 페이지 등의 속성으로 나눕니다. 필터를 직접 추가할 수도 있습니다.

![사용자](./media/app-insights-usage-overview/users.png)  

오른쪽의 자세한 정보에는 데이터 집합에서 주목할 만한 패턴이 나와 있습니다.  

* **사용자** 보고서는 선택한 기간 내에 페이지에 액세스하는 고유한 사용자 수를 계산합니다. 웹앱의 경우 쿠키를 사용하여 사용자 수가 계산됩니다. 누군가가 다른 브라우저 또는 클라이언트 컴퓨터를 사용하여 사이트에 액세스하는 경우 두 번 이상 계산됩니다.
* **세션** 보고서는 사이트에 액세스하는 사용자 세션의 수를 계산합니다. 세션은 30분 이상의 비활동 기간마다 종료되는 사용자의 활동 기간입니다.

[사용자, 세션 및 이벤트 도구에 대한 추가 정보](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>페이지 보기

사용 현황 블레이드에서 페이지 보기 타일을 클릭하여 가장 인기 있는 페이지에 대한 분석 결과를 확인합니다.

![개요 블레이드에서 페이지 보기 차트 클릭](./media/app-insights-usage-overview/05-games.png)

위의 예제는 게임 웹 사이트에서 가져온 것입니다. 차트에서 바로 다음을 확인할 수 있습니다.

* 사용량은 지난 주보다 개선되지 않았습니다. 검색 엔진 최적화에 대해 생각해볼 수 있을까요?
* 테니스가 가장 인기 있는 게임 페이지입니다. 이 페이지의 향상된 기능을 집중적으로 살펴보겠습니다.
* 평균적으로 사용자는 주당 약 세 번 테니스 페이지를 방문합니다. 사용자보다 세션이 약 3배 더 많습니다.
* 대부분의 사용자는 미국 근무 주간 및 근무 시간에 사이트를 방문합니다. 웹 페이지에 “빠른 숨기기” 단추를 제공해야 할 것 같습니다.
* 차트의 [주석](app-insights-annotations.md)은 웹 사이트의 새 버전이 배포된 경우에 표시됩니다. 최근 배포는 사용 현황에 크게 영향을 미치지 않습니다.

페이지 보기 원격 분석에서 사이트가 보내는 사용자 지정 속성별로 분할하는 것과 같이, 사이트에 대한 트래픽을 좀 더 자세히 조사하고 싶으면 어떻게 할까요?

1. Application Insights 리소스 메뉴에서 **이벤트** 도구를 엽니다. 이 도구를 사용하면 다양한 필터링, 코호트 및 분할 옵션에 따라 앱에서 전송된 페이지 보기 및 사용자 지정 이벤트 수를 분석할 수 있습니다.
2. "사용한 사람" 드롭다운에서 "페이지 보기"를 선택합니다.
3. "분할 기준" 드롭다운에서 페이지 보기 원격 분석을 분할할 기준 속성을 선택합니다.

## <a name="retention---how-many-users-come-back"></a>재방문 주기 - 다시 찾아온 사용자는 몇 명이나 되나요?

재방문 주기는 특정 시간 버킷 동안 일부 비즈니스 작업을 수행한 사용자의 코호트를 기준으로 사용자가 해당 앱을 다시 사용하는 빈도를 이해하는 데 도움이 됩니다. 

- 사용자가 다시 찾아오게 만드는 특정 기능 이해 
- 실제 사용자 데이터에 따라 가설 세우기 
- 재방문 주기가 제품에서 문제가 되는지 여부 확인 

![보존](./media/app-insights-usage-overview/retention.png) 

상단의 재방문 주기 컨트롤을 사용하여 재방문 주기를 계산할 특정 이벤트 및 시간 범위를 정의할 수 있습니다. 중간에 표시되는 그래프는 지정된 시간 범위별로 전반적인 재방문 주기 비율을 시각적으로 보여 줍니다. 하단의 그래프는 지정된 기간 내의 개별 재방문 주기를 나타냅니다. 이 수준의 세부 정보를 사용하면 사용자가 수행하는 작업과 다시 방문한 사용자에게 영향을 미칠 수 있는 요인을 좀 더 자세히 이해할 수 있습니다.  

[재방문 주기 도구에 대한 추가 정보](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>사용자 지정 비즈니스 이벤트

사용자가 앱으로 수행하는 작업을 명확하게 이해하려는 경우 사용자 지정 이벤트를 로깅하는 코드 줄을 삽입하면 유용합니다. 이러한 이벤트는 특정 단추 클릭과 같은 자세한 사용자 작업부터 구매하기나 게임에서 이기기 등과 같은 좀 더 중요한 비즈니스 이벤트에 이르기까지 모든 사항을 추적할 수 있습니다. 

경우에 따라 페이지 보기에 유용한 이벤트가 표시될 수 있지만 일반적으로는 그렇지 않습니다. 사용자는 제품 페이지를 열기만 하고 제품을 구입하지 않을 수 있습니다. 

특정 비즈니스 이벤트를 사용하여 사이트를 통한 사용자의 진행 상황을 차트로 나타낼 수 있습니다. 다양한 옵션에 대한 사용자의 선호도를 알아내고 사이트를 나가거나 어려움을 느끼는 경우를 알 수 있습니다. 이러한 지식을 바탕으로 개발 백로그에서 우선 순위를 결정할 수 있습니다.

이벤트는 앱의 클라이언트 쪽에서 로깅될 수 있습니다.

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

또는 서버 쪽에서 다음을 수행할 수 있습니다.

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

포털에서 검사할 때 이벤트를 필터링하거나 분할할 수 있도록 이러한 이벤트에 속성 값을 추가할 수 있습니다. 또한 익명 사용자 ID와 같은 표준 속성 집합이 각 이벤트에 추가되므로 개별 사용자의 활동 시퀀스를 추적할 수 있습니다.

[사용자 지정 이벤트](app-insights-api-custom-events-metrics.md#trackevent) 및 [속성](app-insights-api-custom-events-metrics.md#properties)에 대해 자세히 알아보세요.

### <a name="slice-and-dice-events"></a>이벤트 분석 및 분할

사용자, 세션 및 이벤트 도구에서 사용자, 이벤트 이름 및 속성별로 사용자 지정 이벤트를 분석 및 분할할 수 있습니다.
![사용자](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>앱을 사용하여 원격 분석 디자인

앱의 각 기능을 디자인할 때 사용자를 대상으로 앱의 성공 여부를 측정하는 방법을 고려합니다. 기록해야 하는 비즈니스 이벤트를 결정하고 해당 이벤트에 대한 추적 호출을 처음부터 앱에 코딩합니다.

## <a name="a--b-testing"></a>A | B 테스트
기능의 어느 변형이 보다 성공적인지 알 수 없는 경우, 다른 사용자가 각각 접근할 수 있도록 하여 둘 다 릴리스합니다. 각각의 성공 여부를 측정한 다음 통합 버전으로 이동합니다.

이 기술의 경우 사용자 앱의 각 버전이 전송한 모든 원격 분석에 고유 속성 값을 연결합니다. 활성화된 TelemetryContext에서 속성을 정의하여 수행할 수 있습니다. 이러한 기본 속성은 사용자 지정 메시지뿐 아니라 표준 원격 분석도 응용 프로그램이 전송하는 모든 원격 분석 메시지에 추가됩니다.

Application Insights 포털에서 속성 값에 대해 데이터를 필터링하고 분할하여 다른 버전과 비교할 수 있습니다.

이렇게 하려면 [원격 분석 이니셜라이저를 설정](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer)합니다.

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Global.asax.cs 같은 웹앱 이니셜라이저에서 다음이 적용됩니다.

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

새로운 모든 TelemetryClient는 사용자가 지정하는 속성 값을 자동으로 추가합니다. 개별 원격 분석 이벤트는 기본값을 재정의할 수 있습니다.

## <a name="next-steps"></a>다음 단계
   - [사용자, 세션, 이벤트](app-insights-usage-segmentation.md)
   - [깔때기](usage-funnels.md)
   - [보존](app-insights-usage-retention.md)
   - [사용자 흐름](app-insights-usage-flows.md)
   - [통합 문서](app-insights-usage-workbooks.md)
   - [사용자 컨텍스트 추가](app-insights-usage-send-user-context.md)
