---
title: "Azure Application Insights에서 Search 사용 | Microsoft Docs"
description: "웹앱에서 전송된 원시 원격 분석을 검색하고 필터링합니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: ce2fd9ed1ce796762cc15622cb1c59a316c1909d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="using-search-in-application-insights"></a>Application Insights에서 Search 사용
Search는 페이지 보기, 예외 사항 또는 웹 요청과 같은 개별 원격 분석 항목을 찾고 검색하는 데 사용하는 [Application Insights](app-insights-overview.md)의 기능입니다. 또한 코딩한 로그 추적 및 이벤트를 볼 수 있습니다.

(사용자 데이터에 비해 좀 더 복잡한 쿼리를 위해서는 [Analytics](app-insights-analytics-tour.md)를 사용합니다.)

## <a name="where-do-you-see-search"></a>Search 기능은 어디에 있나요?
### <a name="in-the-azure-portal"></a>Azure 포털에서 다음을 수행합니다.
응용 프로그램의 Application Insights 개요 블레이드에서 명시적으로 진단 검색을 열 수 있습니다.

![진단 검색 열기](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

일부 차트와 표 항목을 클릭할 때에도 열립니다. 이 경우 해당 필터는 선택한 항목의 형식에 초점을 맞추어 미리 설정됩니다. 

예를 들어 개요 블레이드에는 응답 시간에 따라 분류되는 요청의 가로 막대형 차트입니다. 성능 범위를 클릭하면서 해당 응답 시간 범위의 개별 요청 목록을 볼 수 있습니다.

![요청 성능 클릭](./media/app-insights-diagnostic-search/07-open-from-filters.png)

진단 검색의 본문은 코딩한 서버 요청, 페이지 보기, 사용자 지정 이벤트 등의 원격 분석 항목의 목록입니다. 목록의 위쪽은 시간이 지남에 따른 이벤트의 수를 보여주는 요약 차트입니다.

새 이벤트를 가져오려면 새로 고침을 클릭합니다.

### <a name="in-visual-studio"></a>Visual Studio에서

Visual Studio에는 Application Insights Search 창도 있습니다. 이 창은 디버그 중인 응용 프로그램에 의해 생성된 원격 분석 이벤트를 표시하는 데 가장 유용합니다. 하지만 게시된 앱에서 수집된 이벤트를 Azure Portal에 표시할 수도 있습니다.

Visual Studio에서 Search 창을 엽니다.

![Visual Studio에서 열린 Application Insights 검색](./media/app-insights-diagnostic-search/32.png)

Search 창은 웹 포털과 비슷한 기능을 제공합니다.

![Visual Studio Application Insights 검색 창](./media/app-insights-diagnostic-search/34.png)

추적 작업 탭은 요청 또는 페이지 보기를 여는 경우에 사용할 수 있습니다. '연산'은 단일 요청 또는 페이지 보기와 연결된 이벤트의 시퀀스입니다. 예를 들어 종속성 호출, 예외, 추적 로그 및 사용자 지정 이벤트는 단일 연산의 일부일 수 있습니다. 추적 연산 탭에는 요청 또는 페이지 보기와 관련해서 이러한 이벤트의 타이밍 및 기간을 그래픽으로 보여 줍니다. 

## <a name="inspect-individual-items"></a>개별 항목 검사
키 필드 및 관련 항목을 보려면 원격 분석 항목을 선택합니다. 필드의 전체 집합을 확인하려는 경우 "..."를 클릭합니다. 

![새 작업 항목을 클릭하고, 필드를 편집한 다음 확인을 클릭합니다.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>이벤트 유형 필터링
필터 블레이드를 열고 확인하려는 이벤트 유형을 선택합니다. (나중에 열었던 블레이드로 필터를 복원하려는 경우 재설정을 클릭합니다.)

![필터 선택 및 원격 분석 유형 선택](./media/app-insights-diagnostic-search/02-filter-req.png)

이벤트 유형은 다음과 같습니다.

* **추적** -  TrackTrace, log4Net, NLog 및 System.Diagnostic.Trace 호출을 포함한 [진단 로그](app-insights-asp-net-trace-logs.md)입니다.
* **요청** - 페이지, 스크립트, 이미지, 스타일 파일 및 데이터를 포함하는 서버 응용 프로그램이 수신하는 HTTP 요청입니다. 이러한 이벤트는 요청을 만들고 개요 차트에 응답하는 데 사용됩니다.
* **페이지 보기** - [웹 클라이언트가 전송한 원격 분석](app-insights-javascript.md)이며, 페이지 보기 보고서를 만드는 데 사용됩니다. 
* **사용자 지정 이벤트** - [사용량을 모니터링](app-insights-api-custom-events-metrics.md)하기 위해 TrackEvent()에 호출을 삽입한 경우 여기에서 검색할 수 있습니다.
* **예외 사항** - [서버에서 확인할 수 없는 예외 사항](app-insights-asp-net-exceptions.md)이며 TrackException()을 사용하여 로그합니다.
* **종속성** - [서버 응용 프로그램에서](app-insights-asp-net-dependencies.md) REST API 또는 데이터베이스 등의 다른 서비스로의 호출 및 [클라이언트 코드](app-insights-javascript.md)에서의 AJAX 호출입니다.
* **가용성** - [가용성 테스트](app-insights-monitor-web-app-availability.md)의 결과입니다.

## <a name="filter-on-property-values"></a>속성 값에서 필터링
해당 속성 값에서 이벤트를 필터링할 수 있습니다. 사용 가능한 속성은 선택한 이벤트 유형에 따라 다릅니다. 

예를들어 특정 응답 코드에 대한 요청을 선택합니다. 

![속성 확장 및 값 선택](./media/app-insights-diagnostic-search/03-response500.png)

특정 속성 값을 선택하지 않는 것은 모든 값을 선택하는 것과 동일한 효과가 있습니다. 해당 속성에서 필터링을 전한합니다.

### <a name="narrow-your-search"></a>검색 범위 좁히기
필터 값의 오른쪽에 있는 수는 현재 필터링된 집합에서 발생한 수를 보여줍니다. 

이 예제에서는 'Rpt/Employees' 요청이 500개의 오류 중 대부분을 초래함이 명확합니다.

![속성 확장 및 값 선택](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>동일한 속성이 있는 이벤트 찾기
동일한 속성 값을 가지는 모든 항목 찾기:

![마우스 오른쪽 단추로 속성 클릭](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>데이터 검색

> [!NOTE]
> 더 복잡 한 쿼리를 작성하려면 검색 블레이드 위쪽에서 [**분석**](app-insights-analytics-tour.md)을 엽니다.
> 

속성 값 중 하나에서 용어를 검색할 수 있습니다. 속성 값을 포함하는 [사용자 지정 이벤트](app-insights-api-custom-events-metrics.md)를 작성한 경우에 특히 유용합니다. 

더 짧은 시간 범위로 검색하는 것이 더 빠른 것처럼 시간 범위를 설정하고자 할 수 있습니다. 

![진단 검색 열기](./media/app-insights-diagnostic-search/appinsights-311search.png)

하위 문자열이 아닌 전체 단어를 검색합니다. 인용 부호를 사용하여 특수 문자를 묶습니다.

| string | 해당 문자열을 검색하지 *않는* 용어 | 해당 용어가 검색되는 문자열 |
| --- | --- | --- |
| HomeController.About |home<br/>controller<br/>out | homecontroller<br/>about<br/>"homecontroller.about"|
|미국|Uni<br/>ted|united<br/>states<br/>united AND states<br/>"united states"

다음은 사용할 수 있는 검색 식입니다.

| 샘플 쿼리 | 결과 |
| --- | --- |
| `apple` |지정된 시간 범위의 필드에 단어 "apple"이 포함된 모든 이벤트를 찾습니다. |
| `apple AND banana` |두 단어를 모두 포함하는 이벤트를 찾습니다. "and"가 아닌 대문자 "AND"를 사용하세요. |
| `apple OR banana`<br/>`apple banana` |둘 중 한 단어를 포함하는 이벤트를 찾습니다. "or"가 아닌 "OR"을 사용하세요.<br/>약식입니다. |
| `apple NOT banana` |한 단어는 포함하지만 다른 단어는 포함하지 않는 이벤트를 찾습니다. |



## <a name="sampling"></a>샘플링
앱에서 다양한 원격 분석을 생성하는 경우(ASP.NET SDK 버전 2.0.0-beta3 이상 사용), 적응 샘플링 모듈 이벤트의 대표적인 일부만 전송하여 포털에 전송되는 볼륨이 자동으로 줄어듭니다. 그러나, 동일한 요청과 관련된 이벤트가 그룹으로 선택되거나 선택 취소되므로 관련 이벤트 간을 이동할 수 있습니다. 

[샘플링에 대해 알아봅니다](app-insights-sampling.md).



## <a name="create-work-item"></a>작업 항목 만들기
원격 분석 항목의 세부 정보를 이용하여 GitHub 또는 Visual Studio Team Services에서 버그를 만들 수 있습니다. 

![새 작업 항목을 클릭하고, 필드를 편집한 다음 확인을 클릭합니다.](./media/app-insights-diagnostic-search/42.png)

처음 이 작업을 할 때는 Team Services 계정과 프로젝트 링크를 구성하라는 메시지가 나타납니다.

![Team Services 서버 URL과 프로젝트 이름을 입력하고 권한 부여를 클릭합니다.](./media/app-insights-diagnostic-search/41.png)

(작업 항목 블레이드에서 링크를 구성할 수도 있습니다.)

## <a name="save-your-search"></a>검색 저장
원하는 모든 필터를 설치할 때 즐겨찾기로 검색을 저장할 수 있습니다. 조직 계정으로 작업하는 경우 다른 팀 구성원과 공유할 것인지를 선택할 수 있습니다.

![즐겨찾기 클릭, 이름 설정 및 저장 클릭](./media/app-insights-diagnostic-search/08-favorite-save.png)

검색을 다시 보려면 **개요 블레이드로 이동** 하여 즐겨찾기를 엽니다.

![즐겨찾기 타일](./media/app-insights-diagnostic-search/09-favorite-get.png)

상대 시간 범위로 저장한 경우 다시 열린 블레이드는 최신 데이터입니다. 절대 시간 범위로 저장한 경우 매번 같은 데이터가 보입니다. (즐겨찾기를 저장하려고 할 때 '상대'를 사용할 수 없으면 머리글에서 시간 범위를 클릭하고 사용자 지정 범위가 아닌 시간 범위를 설정합니다.)

## <a name="send-more-telemetry-to-application-insights"></a>Application Insights에 더 많은 원격 분석 전송
Application Insights SDK에서 보낸 기본 원격 분석 외에도 다음을 수행할 수 있습니다.

* [.NET](app-insights-asp-net-trace-logs.md) 또는 [Java](app-insights-java-trace-logs.md)의 즐겨찾는 로깅 프레임워크에서 로그 추적을 캡처합니다. 이는 로그 추적을 통해 검색하고 페이지 보기, 예외 사항 및 기타 이벤트와 상관 관계를 지정할 수 있음을 의미합니다. 
* [코드를 작성](app-insights-api-custom-events-metrics.md)하여 사용자 지정 이벤트, 페이지 보기 및 예외 사항을 보냅니다. 

[Application Insights에 로그 및 사용자 지정 원격 분석을 보내는 방법에 대해 알아봅니다](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>질문 및 답변
### <a name="limits"></a>얼마나 많은 데이터가 보존되나요?

[제한 요약](app-insights-pricing.md#limits-summary)을 참조하세요.

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>내 서버 요청에서 게시 데이터를 어떻게 볼 수 있나요?
게시 데이터를 자동으로 기록하지는 않지만 [TrackTrace 또는 로그 호출](app-insights-asp-net-trace-logs.md)을 사용할 수 있습니다. 메시지 매개 변수에 게시 데이터를 넣습니다. 속성을 필터링할 수 있는 동일한 방법으로 메시지에서 필터링할 수는 없지만 크기 제안은 더 깁니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>다음 단계
* [분석에서 복잡한 쿼리 작성](app-insights-analytics-tour.md)
* [Application Insights에 로그 및 사용자 지정 원격 분석 전송](app-insights-asp-net-trace-logs.md)
* [가용성 및 응답성 테스트 설정](app-insights-monitor-web-app-availability.md)
* [문제 해결](app-insights-troubleshoot-faq.md)
