---
title: Azure Application Insights 앱 데이터 보기 | Microsoft Docs
description: Application Insights 커넥터 솔루션을 사용하여 성능 문제를 진단하고 Application Insights를 통해 모니터링하는 경우 사용자가 해당 앱으로 수행하는 작업을 파악할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: magoedte
ms.openlocfilehash: 854ec70c897b6a561fdec056228f82ccec3ae16c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="application-insights-connector-management-solution-preview"></a>Application Insights 커넥터관 리 솔루션(미리 보기)

![Application Insights 기호](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

Application Insights 커넥터 솔루션은 성능 문제를 진단하고 [Application Insights](../application-insights/app-insights-overview.md)를 통해 모니터링하는 경우 사용자가 해당 앱으로 수행하는 작업을 파악하는 데 도움이 됩니다. 개발자가 Application Insights에서 참조하는 것과 동일한 응용 프로그램 원격 분석 뷰를 Log Analytics에서 사용할 수 있습니다. 그러나 Log Analytics와 Application Insights 앱을 통합하는 경우 작업 및 응용 프로그램 데이터가 한 곳에 있게 되므로 응용 프로그램에 대한 가시성이 향상됩니다. 동일한 뷰가 있으면 앱 개발자와 공동 작업을 수행하는 데 도움이 됩니다. 공통 뷰는 응용 프로그램 및 플랫폼 문제를 감지하고 해결하는 데 소요되는 시간을 줄일 수 있습니다.

이 솔루션을 사용하면 다음을 수행할 수 있습니다.

- Application Insights 앱이 서로 다른 Azure 구독에 있는 경우에도 모두 한 곳에서 보기
- 응용 프로그램 데이터와 인프라 데이터 연결
- 로그 검색에서 큐브 뷰로 응용 프로그램 데이터 시각화
- Azure Portal의 Log Analytics 데이터에서 Application Insights 앱으로 피벗

## <a name="connected-sources"></a>연결된 소스

대부분의 다른 Log Analytics 솔루션과 달리 Application Insights 커넥터에 대해서는 데이터가 에이전트별로 수집되지 않습니다. 솔루션에서 사용하는 모든 데이터는 Azure에서 직접 옵니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| [Windows 에이전트](log-analytics-windows-agent.md) | 아니오 | 솔루션이 Windows 에이전트에서 정보를 수집하지 않습니다. |
| [Linux 에이전트](log-analytics-linux-agents.md) | 아니오 | 솔루션이 Linux 에이전트에서 정보를 수집하지 않습니다. |
| [SCOM 관리 그룹](log-analytics-om-agents.md) | 아니요 | 솔루션이 연결된 SCOM 관리 그룹의 에이전트에서 정보를 수집하지 않습니다. |
| [Azure 저장소 계정](log-analytics-azure-storage.md) | 아니오 | 솔루션이 Azure Storage에서 정보를 수집하지 않습니다. |

## <a name="prerequisites"></a>필수 조건

- Application Insights 커넥터 정보에 액세스하려면 Azure 구독이 있어야 합니다.
- 구성된 Application Insights 리소스가 하나 이상 있어야 합니다.
- Application Insights 리소스 소유자 또는 참가자여야 합니다.

## <a name="configuration"></a>구성

1. [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용하여 Azure Web Apps 분석 솔루션을 사용하도록 설정합니다.
2. OMS 포털에서 **설정** &gt; **데이터** &gt; **Application Insights**를 클릭합니다.
3. **구독 선택**에서 Application Insights 리소스가 포함된 구독을 선택하고 **응용 프로그램 이름**에서 하나 이상의 응용 프로그램을 선택합니다.
4. **저장**을 클릭합니다.

약 30분 내에 데이터가 제공되며 다음 이미지와 같이 Application Insights 타일이 데이터로 업데이트됩니다.

![Application Insights 타일](./media/log-analytics-app-insights-connector/app-insights-tile.png)

기타 주의할 사항은 다음과 같습니다.

- 하나의 Log Analytics 작업 영역에만 Application Insights 앱을 연결할 수 있습니다.
- [기본 또는 엔터프라이즈 Application Insights 리소스](https://azure.microsoft.com/pricing/details/application-insights)만 Log Analytics에 연결할 수 있습니다. 그러나 Log Analytics의 무료 계층을 사용할 수 있습니다.

## <a name="management-packs"></a>관리 팩

이 솔루션은 연결된 관리 그룹에서 관리 팩을 설치하지 않습니다.

## <a name="use-the-solution"></a>솔루션 사용

다음 섹션에서는 Application Insights 대시보드에 표시된 블레이드를 사용하여 앱의 데이터를 보고 상호 작용하는 방법을 설명합니다.

### <a name="view-application-insights-connector-information"></a>Application Insights 커넥터 정보 보기

**Application Insights** 타일을 클릭하여 **Application Insights** 대시보드를 열고 다음 블레이드를 표시합니다.

![Application Insights 대시보드](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Application Insights 대시보드](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

표에 표시된 블레이드가 대시보드에 포함되어 있습니다. 각 블레이드에는 지정된 범위 및 시간 범위에 대한 해당 블레이드의 기준과 일치하는 항목이 최대 10개까지 나열됩니다. 블레이드 맨 아래에서 **모두 보기**를 클릭하거나 블레이드 헤더를 클릭하면 모든 레코드를 반환하는 로그 검색을 실행할 수 있습니다.

[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **열** | **설명** |
| --- | --- |
| 응용 프로그램 - 응용 프로그램 수 | 응용 프로그램 리소스에 응용 프로그램 수를 표시합니다. 응용 프로그램 이름과 각 항목의 응용 프로그램 레코드 수도 나열합니다. <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. <br><br>  호스트별 응용 프로그램 레코드, 원격 분석 유형별 레코드 및 형식별 모든 데이터(마지막 날 기준)를 보여 주는 응용 프로그램에 대한 로그 검색을 실행할 응용 프로그램 이름을 클릭합니다. |
| 데이터 볼륨 - 데이터를 전송하는 호스트 | 데이터를 전송하는 컴퓨터 호스트의 수를 보여 줍니다. 컴퓨터 호스트 및 각 호스트의 레코드 수도 나열합니다. <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. <br><br> 호스트별 응용 프로그램 레코드, 원격 분석 유형별 레코드 및 형식별 모든 데이터(마지막 날 기준)를 보여 주는 호스트에 대한 로그 검색을 실행할 컴퓨터 이름을 클릭합니다. |
| 가용성 - 웹 테스트 결과 | 웹 테스트 결과를 성공 또는 실패로 나타내는 도넛형 차트를 보여 줍니다. 차트를 클릭하면 <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code>에 대한 로그 검색이 실행됩니다. <br><br> 결과는 모든 테스트의 성공 및 실패 수를 보여 줍니다. 마지막 1분 동안 트래픽이 발생한 모든 Web Apps가 표시됩니다. 실패한 웹 테스트에 대한 세부 정보를 보여 주는 로그 검색을 보려면 응용 프로그램 이름을 클릭합니다. |
| 서버 요청 – 시간당 요청 | 다양한 응용 프로그램의 시간당 서버 요청에 대한 꺾은선형 차트를 보여 줍니다. 차트의 선을 마우스로 가리키면 특정 시점에 요청을 가장 많이 수신한 상위 3개 응용 프로그램이 표시됩니다. 요청을 수신한 응용 프로그램 목록과 선택한 기간의 요청 수도 보여 줍니다. <br><br>다양한 응용 프로그램의 시간당 서버 요청에 대한 더 자세한 꺾은선형 차트를 보여 주는 <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>에 대한 로그 검색을 실행하려면 그래프를 클릭합니다. <br><br> 요청 목록, 시간 및 요청 기간에 따른 요청 차트, 요청 응답 코드 목록을 보여 주는 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>에 대한 로그 검색을 실행하려면 목록에서 응용 프로그램을 클릭합니다.   |
| 실패 - 시간당 실패한 요청 수 | 시간당 실패한 응용 프로그램 요청 수에 대한 꺾은선형 차트를 보여 줍니다. 차트를 마우스로 가리키면 특정 시점에 실패한 요청이 가장 많은 상위 3개 응용 프로그램이 표시됩니다. 각 응용 프로그램의 실패한 요청 수가 표시된 응용 프로그램 목록도 보여 줍니다. 실패한 응용 프로그램 요청을 자세한 꺾은선형 차트로 보여 주는 <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>에 대한 로그 검색을 실행하려면 차트를 클릭합니다. <br><br>실패한 요청, 시간 및 요청 기간에 따른 실패한 요청 차트, 실패한 요청 응답 코드 목록을 보여 주는 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>에 대한 로그 검색을 실행하려면 목록에서 항목을 클릭합니다. |
| 예외 – 시간당 예외 | 시간당 예외에 대한 꺾은선형 차트를 보여 줍니다. 차트를 마우스로 가리키면 특정 시점에 예외가 가장 많은 상위 3개 응용 프로그램이 표시됩니다. 각 응용 프로그램의 예외 수가 표시된 응용 프로그램 목록도 보여 줍니다. 예외에 대한 더 자세한 연결 차트를 보여 주는 <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>에 대한 로그 검색을 실행하려면 차트를 클릭합니다. <br><br>예외 목록, 시간 및 실패한 요청에 따른 예외 차트, 예외 형식 목록을 보여 주는 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code>에 대한 로그 검색을 실행하려면 목록에서 항목을 클릭합니다.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>로그 검색을 통해 Application Insights 큐브 뷰 보기

대시보드에서 임의의 항목을 클릭하면 검색에 Application Insights 큐브 뷰가 표시됩니다. 큐브 뷰는 선택한 원격 분석 유형에 따라 확장된 시각화를 제공합니다. 따라서 원격 분석 유형에 따라 시각화 콘텐츠가 변경됩니다.

응용 프로그램 블레이드에서 아무 곳이나 클릭하면 기본 **응용 프로그램** 큐브 뷰가 표시됩니다.

![Application Insights 응용 프로그램 큐브 뷰](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

큐브 뷰는 선택한 응용 프로그램에 대한 개요를 보여 줍니다.

**가용성** 블레이드는 웹 테스트 결과와 관련된 실패한 요청을 볼 수 있는 다양한 큐브 뷰를 보여 줍니다.

![Application Insights 가용성 큐브 뷰](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

**서버 요청** 또는 **실패** 블레이드에서 아무 곳이나 클릭하면 큐브 뷰 구성 요소가 변경되어 요청과 관련된 시각화를 제공합니다.

![Application Insights 실패 블레이드](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

**예외** 블레이드에서 아무 곳이나 클릭하면 예외에 맞는 시각화가 표시됩니다.

![Application Insights 예외 블레이드](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

항목을 클릭한 위치가 **Application Insights 커넥터** 대시보드인지, **검색** 페이지 내인지 상관없이 Application Insights 데이터를 반환하는 쿼리는 Application Insights 큐브 뷰를 보여 줍니다. 예를 들어 Application Insights 데이터를 보고 있는 경우 **&#42;** 쿼리도 다음 이미지와 같은 큐브 뷰 탭을 보여 줍니다.

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

큐브 뷰 구성 요소는 검색 쿼리에 따라 업데이트됩니다. 즉, 데이터를 볼 수 있는 기능을 제공하는 검색 필드를 사용하여 결과를 필터링할 수 있습니다.

- 모든 응용 프로그램
- 선택된 단일 응용 프로그램
- 응용 프로그램 그룹

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Azure Portal에서 앱에 피벗

Application Insights 커넥터 블레이드는 사용자가 *OMS 포털을 사용하는 경우* 선택한 Application Insights 앱에 피벗할 수 있도록 설계되었습니다. 솔루션을 앱 문제 해결에 도움이 되는 상위 수준 모니터링 플랫폼으로 사용할 수 있습니다. 연결된 응용 프로그램에서 잠재적인 문제가 발견되면 Log Analytics 검색에서 자세히 알아보거나 Application Insights 앱에 직접 피벗할 수 있습니다.

피벗하려면 각 행의 끝에 표시되는 줄임표(**…**)를 클릭하고 **Open in Application Insights**(Application Insights에서 열기)를 선택합니다.

>[!NOTE]
>Azure Portal에서는 **Open in Application Insights**(Application Insights에서 열기)를 사용할 수 없습니다.

![Application Insights에서 열기](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>샘플 수정 데이터

Application Insights는 *[샘플링 수정](../application-insights/app-insights-sampling.md)*을 제공하므로 원격 분석 트래픽을 줄이는 데 도움이 됩니다. Application Insights 앱에서 샘플링을 사용하도록 설정하는 경우 Application Insights 및 Log Analytics 모두에서 저장되는 항목의 수가 감소됩니다. **Application Insights 커넥터** 페이지 및 큐브 뷰에서 데이터 일관성이 유지되지만 사용자 지정 쿼리를 위해 샘플링된 데이터는 수동으로 수정해야 합니다.

다음은 로그 검색 쿼리의 샘플링 수정 예입니다.

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**Sampled Count**(샘플링된 수) 필드는 모든 항목에 있으며 항목이 나타내는 데이터 요소 수를 보여 줍니다. Application Insights 앱에 대해 샘플링이 설정된 경우 **Sampled Count**(샘플링된 수)가 1보다 큽니다. 응용 프로그램에서 생성하는 실제 항목 수를 계산하려면 **Sampled Count**(샘플링된 수) 필드를 합산합니다.

샘플링은 응용 프로그램에서 생성하는 총 항목 수에만 영향을 줍니다. **RequestDuration** 또는 **AvailabilityDuration** 같은 메트릭 필드는 표시된 항목의 평균을 보여 주므로 해당 필드에 대한 샘플링은 수정할 필요가 없습니다.

## <a name="input-data"></a>데이터 입력

솔루션은 연결된 Application Insights 앱에서 다음과 같은 원격 분석 유형의 데이터를 수신합니다.

- Availability
- 예외
- 요청
- 페이지 보기 - 작업 영역에서 페이지 보기를 받으려면 앱에서 해당 정보를 수집하도록 구성해야 합니다. 자세한 내용은 [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views)를 참조하세요.
- 사용자 지정 이벤트 - 작업 영역에서 사용자 지정 이벤트를 받으려면 앱에서 해당 정보를 수집하도록 구성해야 합니다. 자세한 내용은 [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent)를 참조하세요.

데이터를 사용할 수 있게 되면 Application Insights에서 Log Analytics로 수신됩니다.

## <a name="output-data"></a>출력 데이터

각 형식의 입력 데이터에 대해 *종류*가 *ApplicationInsights*인 레코드가 만들어집니다. ApplicationInsights 레코드에는 다음 섹션에 표시된 속성이 포함되어 있습니다.

### <a name="generic-fields"></a>일반 필드

| 자산 | 설명 |
| --- | --- |
| 유형 | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | 레코드 시간 |
| ApplicationId | Application Insights 앱의 계측 키 |
| ApplicationName | Application Insights 앱의 이름 |
| RoleInstance | 서버 호스트의 ID |
| DeviceType | 클라이언트 장치 |
| ScreenResolution |   |
| Continent | 요청이 시작된 대륙 |
| 국가 | 요청이 시작된 국가 |
| Province | 요청이 시작된 시/도 또는 로캘 |
| City | 요청이 시작된 구/군/시 또는 동/면 |
| isSynthetic | 요청이 사용자에 의해 만들어졌는지 자동화된 방법을 통해 만들어졌는지 나타냅니다. 사용자가 생성한 경우 True, 자동화된 방법인 경우 False입니다. |
| SamplingRate | 포털에 전송되는 SDK에 의해 생성된 원격 분석의 비율입니다. 범위는 0.0-100.0입니다. |
| SampledCount | 100/(SamplingRate)입니다. 예를 들어 4 =&gt; 25%입니다. |
| IsAuthenticated | True 또는 False |
| OperationID | 작업 ID가 동일한 항목은 포털에서 관련 항목으로 표시됩니다. 일반적으로 요청 ID입니다. |
| ParentOperationID | 부모 작업 ID |
| OperationName |   |
| SessionId | 요청이 만들어진 세션을 고유하게 식별하는 GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>가용성 관련 필드

| 자산 | 설명 |
| --- | --- |
| TelemetryType | Availability |
| AvailabilityTestName | 웹 테스트의 이름 |
| AvailabilityRunLocation | Http 요청의 지리적 소스 |
| AvailabilityResult | 웹 테스트의 성공 결과 표시 |
| AvailabilityMessage | 웹 테스트에 연결된 메시지 |
| AvailabilityCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| DataSizeMetricValue | 1.0 또는 0.0 |
| DataSizeMetricCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| AvailabilityDuration | 웹 테스트 기간 시간(밀리초) |
| AvailabilityDurationCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | 웹 테스트의 고유 GUID |
| AvailabilityTimestamp | 정확한 가용성 테스트 타임스탬프 |
| AvailabilityDurationMin | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최소 웹 테스트 기간(밀리초)을 보여 줍니다. |
| AvailabilityDurationMax | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최대 웹 테스트 기간(밀리초)을 보여 줍니다. |
| AvailabilityDurationStdDev | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 모든 웹 테스트 기간(밀리초) 사이의 표준 편차를 보여 줍니다. |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>예외 관련 필드

| 유형 | ApplicationInsights |
| --- | --- |
| TelemetryType | 예외 |
| ExceptionType | 예외 형식 |
| ExceptionMethod | 예외를 만드는 메서드 |
| ExceptionAssembly | 어셈블리에는 공개 키 토큰뿐만 아니라 프레임워크와 버전 이 포함됩니다. |
| ExceptionGroup | 예외 형식 |
| ExceptionHandledAt | 예외를 처리한 수준 표시 |
| ExceptionCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| ExceptionMessage | 예외 메시지 |
| ExceptionStack | 예외의 전체 스택 |
| ExceptionHasStack | 예외에 스택이 있는 경우 True |



### <a name="request-specific-fields"></a>요청 관련 필드

| 자산 | 설명 |
| --- | --- |
| 유형 | ApplicationInsights |
| TelemetryType | 요청 |
| ResponseCode | 클라이언트에 보낸 HTTP 응답 |
| RequestSuccess | 성공 또는 실패를 표시합니다. True 또는 False입니다. |
| RequestID | 요청을 고유하게 식별하는 ID |
| RequestName | GET/POST + URL 기본 |
| RequestDuration | 요청 기간 시간(초) |
| URL | 호스트를 포함하지 않는 요청의 URL |
| 호스트 | 웹 서버 호스트 |
| URLBase | 요청의 전체 URL |
| ApplicationProtocol | 응용 프로그램에서 사용되는 프로토콜의 유형 |
| RequestCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| RequestDurationCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| RequestDurationMin | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최소 요청 기간(밀리초)을 보여 줍니다. |
| RequestDurationMax | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최대 요청 기간(밀리초)을 보여 줍니다. |
| RequestDurationStdDev | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 모든 요청 기간(밀리초) 사이의 표준 편차를 보여 줍니다. |

## <a name="sample-log-searches"></a>샘플 로그 검색

이 솔루션에는 대시보드에 표시되는 샘플 로그 검색 집합이 없습니다. 그러나 설명이 포함된 샘플 로그 검색 쿼리가 [Application Insights 커넥터 정보 보기](#view-application-insights-connector-information) 섹션에 표시됩니다.

## <a name="next-steps"></a>다음 단계

- Application Insights 앱에 대한 자세한 정보를 보려면 [로그 검색](log-analytics-log-searches.md)을 사용합니다.
