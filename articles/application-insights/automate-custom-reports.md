---
title: Azure Application Insights 데이터를 사용하여 사용자 지정 보고서 자동화
description: Azure Application Insights 데이터를 사용하여 사용자 지정 일간/주간/월간 보고서 자동화
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072660"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Azure Application Insights 데이터를 사용하여 사용자 지정 보고서 자동화

정기 간행물 보고서는 팀이 중요한 비즈니스 서비스가 어떻게 진행되고 있는지 정보를 받을 수 있도록 도와줍니다. 개발자, DevOps/SRE 팀 및 관리자는 모두가 포털에 로그온할 필요 없이 안정적으로 인사이트를 제공하는 자동화된 보고서로 생산적일 수 있습니다. 이런 보고서는 어떤 경고 규칙도 트리거하지 않을 수 있는 대기 시간, 부하 또는 실패율의 점진적인 증가를 식별하는 데 도움이 될 수 있습니다.

각 엔터프라이즈에는 다음과 같은 고유의 보고 필요성이 있습니다. 

* 보고서에서 사용자 지정 메트릭 또는 메트릭의 특정 백분위수 집계입니다.
* 다양한 대상을 위한 데이터의 일간, 주간 및 월간 롤업에 대한 다양한 보고서가 있습니다.
* 영역 또는 환경 같은 사용자 지정 특성에 의한 구분입니다. 
* 서로 다른 구독 또는 리소스 그룹 등에 있을 수 있는 경우라 해도 단일 보고서에서 일부 AI 리소스를 그룹화합니다.
* 선택적 대상에게 보내는 중요한 메트릭이 포함된 별도 보고서입니다.
* 포털 리소스에 액세스할 수 없는 관련자에게 보고서를 제공합니다.

> [!NOTE] 
> 주간 Application Insights 다이제스트 이메일은 모든 사용자 지정을 허용하지 않으며 아래 나열된 사용자 지정 옵션을 위해 중단됩니다. 마지막 주간 다이제스트 이메일은 2018년 6월 11일에 전송됩니다. 유사한 사용자 지정 보고서를 가져오기 위한 다음 옵션 중 하나를 구성하세요(아래 제시된 쿼리 사용).

## <a name="to-automate-custom-report-emails"></a>사용자 지정 보고서 이메일을 자동화하려면

[Application Insights 데이터를 프로그래밍 방식으로 쿼리](https://dev.applicationinsights.io/)하여 일정에 따라 사용자 지정 보고서를 생성할 수 있습니다. 다음 옵션은 빠르게 시작할 수 있게 돕습니다.

* [Microsoft Flow를 사용하여 보고서 자동화](app-insights-automate-with-flow.md)
* [논리 앱을 사용하여 보고서 자동화](automate-with-logic-apps.md)
* 모니터링 시나리오에서 "Application Insights 예약된 다이제스트" [Azure 함수](https://azure.microsoft.com/services/functions/) 템플릿을 사용합니다. 이 함수는 SendGrid를 사용하여 이메일을 배달합니다. 

    ![Azure 함수 템플릿](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>주간 다이제스트 이메일에 대한 샘플 쿼리
다음 쿼리는 보고서처럼 주간 다이제스트 이메일에 대한 여러 데이터 집합 가입을 표시합니다. 주간 보고서를 자동화하려면 위에 나열된 옵션 중 하나를 사용하고 필요에 따라 사용자 지정합니다.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>다음 단계

- [Analytics 쿼리](app-insights-analytics-using.md) 만들기에 대해 자세히 알아봅니다.
- [Application Insights 데이터를 프로그래밍 방식으로 쿼리](https://dev.applicationinsights.io/)하는 방법에 대해 자세히 알아보기
- [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)에 대해 자세히 알아봅니다.
- [Microsoft Flow](https://ms.flow.microsoft.com)에 대해 자세히 알아봅니다.


