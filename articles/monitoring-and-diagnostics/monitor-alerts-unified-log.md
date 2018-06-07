---
title: Azure Monitor의 로그 경고 - 경고 | Microsoft Docs
description: Azure Alerts에 대해 지정한 분석 쿼리 조건이 충족될 경우 이메일, 알림, 웹 사이트 URL 호출(웹후크) 또는 자동화를 트리거합니다.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 8bf534177e8236a7d72d6dfdd4612b5f6f492b17
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057324"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Azure Monitor의 로그 경고 - 경고 
이 아티클에서는 로그 경고의 세부 정보를 제공합니다. 즉, 새 [Azure 경고](monitoring-overview-unified-alerts.md) 내에서 지원되는 경고 형식 중 하나이며 사용자가 경고의 기준으로 Azure의 분석 플랫폼을 사용할 수 있도록 합니다. 로그를 사용하는 메트릭 경고에 대한 자세한 내용은 [거의 실시간 메트릭 경고](monitoring-near-real-time-metric-alerts.md)를 참조하세요.


로그 경고는 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 또는 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)에 대해 만든 로그 검색 규칙으로 이루어집니다.


## <a name="log-search-alert-rule---definition-and-types"></a>로그 검색 경고 규칙 - 정의 및 형식

로그 검색 규칙은 일정한 간격으로 지정된 로그 쿼리를 자동으로 실행하도록 Azure Alerts에 의해 만들어집니다.  로그 쿼리 결과가 특정 조건과 일치하는 경우 경고 레코드가 만들어집니다. 그런 다음, 규칙은 [작업 그룹](monitoring-action-groups.md)을 사용하여 하나 이상의 작업을 자동으로 실행할 수 있습니다. 

로그 검색 규칙은 다음 세부 정보에 의해 정의됩니다.
- **로그 쿼리**  경고 규칙이 실행될 때마다 실행되는 쿼리입니다.  이 쿼리에서 반환된 레코드는 경고를 생성할지 여부를 결정하는 데 사용됩니다. *Azure Application Insights* 쿼리는 [응용 프로그램 간 호출](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery)을 포함할 수 있으며 사용자에게 외부 응용 프로그램에 대한 액세스 권한을 제공합니다. 

    > [!IMPORTANT]
    > [Application Insights에 대한 응용 프로그램 간 쿼리](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) 지원은 미리 보기 상태이며 기능 및 사용자 환경은 변경될 수 있습니다. [작업 영역 간 쿼리](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) 및 [Log Analytics에 대한 리소스 간 쿼리](../log-analytics/log-analytics-cross-workspace-search.md)의 사용은 현재 Azure 경고에서 **지원되지 않습니다**.

- **기간**  쿼리에 대한 시간 범위를 지정합니다. 쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다. 기간은 남용을 방지하고 쿼리 로그에 사용되는 시간 명령(ago 등)을 회피하도록 로그 쿼리에 대해 인출된 데이터를 제한합니다. <br>*예를 들어 기간을 60분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 로그 쿼리를 실행하기 위해 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 반환됩니다. 이제 로그 쿼리가 ago(7d)와 같은 명령을 사용하는 경우 데이터가 지난 60분 동안만 존재하는 것처럼 로그 쿼리는 오후 12시 15분에서 1시 15분 사이의 데이터에 대해서만 실행됩니다. 쿼리 로그에서 지정된 대로 7일 동안의 데이터에 대해서는 실행되지 않습니다.*
- **빈도**.  쿼리를 실행해야 하는 빈도를 지정합니다. 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 기간 이하여야 합니다.  값이 기간을 초과하는 경우 레코드가 누락될 위험이 있습니다.<br>*예를 들어 30분의 기간과 60분의 빈도를 사용하는 것이 좋습니다.  쿼리가 1:00에 실행되면 오후 12:30 및 1:00 사이의 레코드를 반환합니다.  1:30 및 2:00 사이의 레코드를 반환하게 된다면 다음으로 쿼리가 실행되는 시간은 2:00입니다.  1:00 및 1:30 사이에 생성된 레코드는 평가되지 않습니다.*
- **임계값**.  로그 검색 결과를 평가하여 경고를 만들어야 하는지 여부를 결정합니다.  임계값은 다양한 유형의 로그 검색 경고 규칙에 따라 다릅니다.

로그 검색 규칙은 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 또는 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)에 적용되고 두 가지 형식일 수 있습니다. 이러한 각 유형에 대해서는 다음 섹션에서 자세히 설명합니다.

- **[결과 수](#number-of-results-alert-rules)** - 로그 검색에서 반환되는 레코드 수가 지정된 수를 초과할 때 만들어지는 단일 경고입니다.
- **[미터법](#metric-measurement-alert-rules)** -  로그 검색 결과에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 만들어지는 경고입니다.

경고 규칙 유형 간의 차이점은 다음과 같습니다.

- *결과 수* 경고 규칙은 항상 단일 경고를 만드는 반면, *미터법* 경고 규칙은 임계값을 초과하는 각 개체에 대해 경고를 만듭니다.
- *결과 수* 경고 규칙은 임계값을 한 번 초과하면 경고를 만듭니다. *미터법* 경고 규칙은 특정 시간 간격 동안 임계값이 특정 횟수를 초과하면 경고를 만들 수 있습니다.

### <a name="number-of-results-alert-rules"></a>결과 수 경고 규칙
**결과 수** 경고 규칙은 검색 쿼리에서 반환된 레코드 수가 지정된 임계값을 초과하면 단일 경고를 만듭니다. 이 형식의 경고 규칙은 Windows 이벤트 로그, Syslog, WebApp 응답 및 사용자 지정 로그와 같은 이벤트 작업에 적합합니다.  특정 오류 이벤트가 생성될 때 또는 특정 기간 내에 복수의 오류 이벤트가 생성되는 경우 경고를 만드는 것이 좋습니다.

**임계값**: 결과 수 경고 규칙의 임계값은 특정 값보다 크거나 작습니다.  로그 검색에서 반환된 레코드 수가 이 조건과 일치하면 경고를 만듭니다.

단일 이벤트에 대해 경고하려면 결과 수를 0보다 크게 설정하고, 마지막 쿼리 실행 이후 생성된 단일 이벤트 발생을 확인합니다. 일부 응용 프로그램은 경고를 발생시키지 않아야 할 가끔씩 발생하는 오류를 기록할 수 있습니다.  예를 들어 응용 프로그램이 오류 이벤트를 생성한 프로세스를 재시도한 후 다음에 성공할 수 있습니다.  이 경우 특정 기간 내에 복수의 이벤트가 생성되지 않은 한 경고를 만들지 않는 것이 좋습니다.  

경우에 따라 이벤트가 없을 때 경고를 만드는 것이 좋습니다.  예를 들어 프로세스에서 일반 이벤트가 올바르게 작동 중임을 나타내기 위해 일반 이벤트를 기록할 수 있습니다.  특정 기간 내에 이러한 이벤트 중 하나가 기록되지 않으면 경고를 만들어야 합니다.  이 경우 임계값을 **1보다 작게**로 설정할 수 있습니다.

#### <a name="example"></a>예
웹 기반 앱이 코드 500(즉) 내부 서버 오류로 사용자에게 응답하는 경우를 알아야 하는 시나리오를 고려해보세요. 다음 세부 정보로 경고 규칙을 만들 수 있습니다.  
- **쿼리:** requests | resultCode == "500"<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **임계값:** 0 초과<br>

그런 후 경고가 30분 동안의 데이터로 5분 간격으로 쿼리를 실행하여 결과 코드가 500인 레코드를 찾습니다. 이러한 레코드가 하나라도 있으면 경고가 발생하고, 구성된 작업이 트리거됩니다.

### <a name="metric-measurement-alert-rules"></a>미터법 경고 규칙

- **미터법** 경고 규칙은 쿼리에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 경고를 만듭니다.  **결과 수** 경고 규칙과는 다음과 같이 분명하게 구별됩니다.
- **집계 함수**: 수행되는 계산과 잠재적으로 집계할 숫자 필드를 결정합니다.  예를 들어 **count()** 는 쿼리의 레코드 수를 반환하고, **avg(CounterValue)** 는 해당 간격 동안 CounterValue 필드의 평균을 반환합니다. 쿼리의 집계 함수는 AggregatedValue로 명명되어야 하며 숫자 값을 제공해야 합니다.다. 
- **그룹 필드**: 이 필드의 각 인스턴스에 대해 집계된 값이 있는 레코드가 만들어지며 각각에 대해 경고가 생성될 수 있습니다.  예를 들어 각 컴퓨터에 대해 경고를 생성하려면 **컴퓨터별**을 사용합니다. 

    > [!NOTE]
    > Application Insights를 기반으로 하는 메트릭 측정 경고 규칙에 대해서는 데이터를 그룹화하기 위한 필드를 지정할 수 있습니다. 이를 위해 규칙 정의의 **집계** 옵션을 사용합니다.   
    
- **시간 간격**: 데이터가 집계되는 시간 간격을 정의합니다.  예를 들어 **5분**을 지정한 경우 경고에 대해 지정한 기간 동안 5분 간격으로 집계된 그룹 필드의 각 인스턴스에 대한 레코드가 만들어집니다.

    > [!NOTE]
    > 쿼리에서 bin 함수를 사용하여 간격을 지정해야 합니다. bin()이 결과로 같지 않은 시간 간격을 출력할 수 있으므로, 경고는 런타임 시 적절한 시간을 사용하여 bin 명령을 bin_at 명령으로 변환하여 고정 시점의 결과를 보장합니다.
    
- **임계값**: 미터법 경고 규칙의 임계값은 집계 값 및 위반 횟수로 정의됩니다.  로그 검색에서 데이터 요소가 이 값을 초과하면 위반으로 간주됩니다.  결과의 개체에서 위반 횟수가 지정된 값을 초과하면 해당 개체에 대한 경고가 만들어집니다.

#### <a name="example"></a>예
컴퓨터에서 30분 동안 90% 프로세서 사용률을 3회 초과하는 경우 경고가 필요한 시나리오를 고려해 보세요.  다음 세부 정보로 경고 규칙을 만들 수 있습니다.  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **집계 값:** 90 초과<br>
- **경고 트리거 조건:** 총 위반 2 초과<br>

이 쿼리는 각 컴퓨터에 대해 5분 간격으로 평균 값을 만듭니다.  이 쿼리는 이전 30분 동안 수집된 데이터에 대해 5분마다 실행됩니다.  세 개의 컴퓨터에 대한 샘플 데이터는 다음과 같습니다.

![샘플 쿼리 결과](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

이 예제에서 srv02와 srv03은 기간 동안 90% 임계값을 3회 위반했기 때문에 별도의 경고가 만들어집니다.  **경고 트리거 조건**이 **연속**으로 변경된 경우 세 개의 연속 샘플에 대한 임계값을 위반했기 때문에 srv03에 대해서만 경고가 만들어집니다.


## <a name="log-search-alert-rule---creation-and-modification"></a>로그 검색 경고 규칙 - 생성 및 수정

다음의 로그 경고뿐만 아니라 구성하는 로그 검색 경고 규칙은 보거나, 만들거나 수정할 수 있습니다.
- Azure portal
- REST API(PowerShell을 통해)
- Azure 리소스 관리자 템플릿

### <a name="azure-portal"></a>Azure portal
[새 Azure 경고](monitoring-overview-unified-alerts.md)가 도입된 이후 이제 사용자는 단일 위치 및 유사한 단계에서 Azure Portal에 있는 모든 형식의 경고를 관리할 수 있습니다. [새 Azure 경고 사용](monitor-alerts-unified-usage.md)에 대해 자세히 알아봅니다.

또한 사용자는 Azure에서 선택한 Analytics 플랫폼으로 해당 쿼리를 완전하게 수행한 다음, *쿼리를 저장하여 경고에서 사용할 수 있게 가져올 수* 있습니다. 수행할 단계는 다음과 같습니다.
- *Application Insights의 경우*: Analytics 포털로 이동하고, 쿼리 및 해당 결과가 유효한지 검사합니다. 그런 후 고유한 이름을 사용하여 *공유 쿼리*에 저장합니다.
- *Log Analytics의 경우*: 로그 검색으로 이동하고, 쿼리 및 해당 결과가 유효한지 검사합니다. 그런 후 고유한 이름을 사용하여 아무 범주에나 저장합니다.

이제 [Alerts에서 로그 경고를 만들 때](monitor-alerts-unified-usage.md) 아래 예제인 ![Alerts로 가져온 저장된 쿼리](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)에 설명된 대로, 저장된 쿼리가 신호 유형 **로그(저장된 쿼리)** 로 표시되는 것을 볼 수 있습니다.

> [!NOTE]
> **로그(저장된 쿼리)** 를 사용하면 Alerts로 가져오기됩니다. 따라서 Analytics에서 그 이후에 변경한 내용이 로그 검색 경고 규칙에 반영되지 않고 그 반대의 경우도 마찬가지입니다.

### <a name="rest-apis"></a>REST API
로그 경고에 제공된 API는 RESTful이며 Azure Resource Manager REST API를 통해 액세스할 수 있습니다. 따라서 PowerShell 및 다른 옵션을 통해 액세스하여 API를 활용할 수 있습니다.

세부 정보뿐만 아니라 REST API를 사용하는 예제는 다음을 참조하세요.
- [Log Analytics 경고 REST API](../log-analytics/log-analytics-api-alerts.md) - Azure Log Analytics에 대한 로그 검색 경고 규칙을 만들고 관리합니다.
- [Azure Monitor 예약 쿼리 규칙 REST API](https://docs.microsoft.com/en-us/rest/api/monitorr/scheduledqueryrules/) - Azure Application Insights에 대한 로그 검색 경고 규칙을 만들고 관리합니다.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
사용자는 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)에서 제공한 유연성을 사용하여 로그 경고를 만들거나 업데이트하기 위해 리소스를 만들고 업데이트할 수 있습니다.

세부 정보뿐만 아니라 Resource Manager 템플릿를 사용하는 예제는 다음을 참조하세요.
- Azure Log Analytics에 따라 로그 경고에 대한 [검색 및 경고 관리 저장](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics)
- Azure Application Insights에 따라 로그 경고에 대한 [쿼리 규칙 예약](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights)
 

## <a name="next-steps"></a>다음 단계
* [Azure의 로그 경고](monitor-alerts-unified-log-webhook.md)를 이해합니다.
* [Azure 경고](monitoring-overview-unified-alerts.md)에 대해 알아봅니다.
* [Application Insights](../application-insights/app-insights-analytics.md)에 대해 자세히 알아봅니다.
* [Log Analytics](../log-analytics/log-analytics-overview.md)에 대해 자세히 알아보기    
