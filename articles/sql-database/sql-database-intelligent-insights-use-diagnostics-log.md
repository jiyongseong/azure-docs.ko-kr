---
title: Intelligent Insights 성능 진단 로그 - Azure SQL Database | Microsoft Docs
description: Intelligent Insights는 Azure SQL Database 성능 문제의 진단 로그를 제공합니다.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/04/2018
ms.author: v-daljep
ms.openlocfilehash: 5ce83198e1d2afb713044879724df88ad2696548
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Intelligent Insights의 Azure SQL Database 성능 진단 로그 사용

이 페이지에서는 [Intelligent Insights](sql-database-intelligent-insights.md)에서 생성된 Azure SQL Database 성능 진단 로그, 해당 형식 및 사용자 지정 개발 요구 사항을 위해 포함된 데이터를 사용하는 방법을 설명합니다. 이 진단 로그는 사용자 지정 DevOps 경고 및 보고 기능을 위해 [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage) 또는 타사 솔루션으로 보낼 수 있습니다.

## <a name="log-header"></a>로그 헤더

진단 로그는 JSON 표준 형식을 사용하여 Intelligent Insights 결과를 출력합니다. Intelligent Insights에 액세스하기 위한 정확한 범주 속성은 고정 값 "SQLInsights"입니다.

로그의 헤더는 일반적이며 항목이 만들어진 때를 나타내는 타임스탬프(TimeGenerated)로 구성됩니다. 또한 항목이 관련된 특정 SQL Database를 나타내는 리소스 ID(ResourceId)도 포함합니다. 범주(Category), 수준(Level) 및 작업 이름(OperationName)은 고정된 속성으로, 값이 변경되지 않습니다. 이러한 속성은 로그 항목이 정보 제공용이며 Intelligent Insights(SQLInsights)에서 제공되었음을 나타냅니다.

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>문제 ID 및 영향받는 데이터베이스

문제 식별 속성(issueId_d)은 해결될 때까지 성능 문제를 고유하게 추적하는 방법을 제공합니다. Intelligent Insights는 각 문제 수명 주기를 “활성”, “확인 중” 또는 “완료됨”으로 관찰합니다. 이러한 각 상태 단계를 통해 Intelligent Insights는 여러 이벤트 레코드를 로그에 기록할 수 있습니다. 이러한 각 항목에 대한 문제 ID 번호가 고유하게 유지됩니다. Intelligent Insights는 수명 주기를 통해 문제를 추적하고 15분마다 진단 로그에서 Insight를 생성합니다.

성능 문제가 검색되고 지속되면 문제는 상태(status_s) 속성 아래에 “활성”으로 보고됩니다. 검색된 문제가 완화되면 문제가 확인되고 상태(status_s) 속성 아래에 “확인 중”으로 보고됩니다. 더 이상 문제가 없는 경우에는 상태(status_s) 속성이 이 문제를 “완료됨”으로 보고합니다.

문제 ID와 함께 진단 로그는 진단 로그에 보고된 문제에 관련된 특정 이벤트의 시작(intervalStartTime_t) 및 종료(intervalEndTme_t) 타임스탬프를 보고합니다.

탄력적 풀(elasticPoolName_s) 속성은 문제가 있는 데이터베이스가 속한 탄력적 풀을 나타냅니다. 데이터베이스가 탄력적 풀의 일부가 아니면 이 속성에 값이 없습니다. 문제가 검색된 데이터베이스는 데이터베이스 이름(databaseName_s) 속성으로 공개됩니다.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>검색된 문제

Intelligent Insights 성능 로그의 다음 섹션에는 기본 제공 인공 지능을 통해 검색된 성능 문제가 포함됩니다. 검색은 JSON 진단 로그 내의 속성으로 표시됩니다. 이러한 검색은 문제의 범주, 문제가 미치는 영향, 영향을 받는 쿼리 및 메트릭으로 구성됩니다. 검색 속성에는 검색된 여러 성능 문제가 포함될 수 있습니다.

검색된 성능 문제는 다음 검색 속성 구조로 보고됩니다.

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

진단 로그에 출력되는 검색 가능한 성능 패턴과 세부 정보가 다음 표에 나와 있습니다.

### <a name="detection-category"></a>검색 범주

범주(category) 속성은 검색 가능한 성능 패턴의 범주를 설명합니다. 검색 가능한 성능 패턴의 모든 가능한 범주에 대해서는 다음 표를 참조하세요. 자세한 내용은 [인텔리전스 Insights를 사용하여 데이터베이스 성능 문제 해결](sql-database-intelligent-insights-troubleshoot-performance.md)을 참조하세요.

검색된 성능 문제에 따라 진단 로그 파일에 출력되는 세부 정보가 달라집니다.

| 검색 가능한 성능 패턴 | 출력되는 세부 정보 |
| :------------------- | ------------------- |
| 리소스 제한에 도달 | <li>영향받는 리소스</li><li>쿼리 해시</li><li>리소스 사용률</li> |
| 워크로드 증가 | <li>실행이 증가한 쿼리 수</li><li>워크로드 증가에 가장 크게 영향을 미치는 쿼리의 쿼리 해시</li> |
| 메모리 부족 | <li>메모리 클럭</li> |
| 잠금 | <li>영향받는 쿼리 해시</li><li>차단 쿼리 해시</li> |
| 증가한 MAXDOP | <li>쿼리 해시</li><li>CXP 대기 시간</li><li>대기 시간</li> |
| Pagelatch 경합 | <li>경합을 초래하는 쿼리의 쿼리 해시</li> |
| 누락된 인덱스 | <li>쿼리 해시</li> |
| 새 쿼리 | <li>새 쿼리의 쿼리 해시</li> |
| 비정상적인 대기 통계 | <li>비정상적인 대기 형식</li><li>쿼리 해시</li><li>쿼리 대기 시간</li> |
| TempDB 경합 | <li>경합을 초래하는 쿼리의 쿼리 해시</li><li>전제 데이터베이스 페이지 래치 경합 대기 시간에 대한 쿼리 특성[%]</li> |
| 탄력적 풀 DTU 부족 | <li>탄력적 풀</li><li>최상위 DTU 사용 데이터베이스</li><li>최상위 소비자가 사용한 풀 DTU 비율</li> |
| 계획 회귀 | <li>쿼리 해시</li><li>적절한 계획 ID</li><li>부적절한 계획 ID</li> |
| 데이터베이스 범위 구성 값 변경 | <li>기본값 대비 데이터베이스 범위 구성 변경</li> |
| 느린 클라이언트 | <li>쿼리 해시</li><li>대기 시간</li> |
| 가격 책정 계층 다운그레이드 | <li>텍스트 알림</li> |

### <a name="impact"></a>영향

영향(impact) 속성은 검색된 동작이 데이터베이스의 문제에 얼마나 많은 영향을 미쳤는지를 설명합니다. 영향 범위는 1에서 3까지이며 3이 가장 높은 영향, 2가 보통, 1이 가장 낮은 영향을 의미합니다. 특정 요구 사항에 따라 영향 값이 사용자 지정 경고 자동화의 입력으로 사용될 수 있습니다. 영향받는 쿼리(QueryHashes) 속성은 특정 검색이 영향을 미친 쿼리 해시 목록을 제공합니다.

### <a name="impacted-queries"></a>영향받는 쿼리

Intelligent Insights 로그의 다음 섹션에서는 검색된 성능 문제가 영향을 미친 특정 쿼리에 대한 정보를 제공합니다. 이 정보는 impact_s 속성에 포함된 개체 배열로 공개됩니다. 영향 속성은 엔터티 및 메트릭으로 구성됩니다. 엔터티는 특정 쿼리(Type: Query)를 나타냅니다. 고유한 쿼리 해시는 값(Value) 속성을 통해 공개됩니다. 또한 공개된 각 쿼리 뒤에 검색된 성능 문제를 나타내는 메트릭 및 값이 옵니다.

다음 로그 예제에서는 해시가 0x9102EXZ4인 쿼리의 실행 기간이 증가한 것으로 감지되었습니다(메트릭: DurationIncreaseSeconds). 값 110초는 값이 특정 쿼리를 실행하는 데 110초가 더 걸린 것을 나타냅니다. 여러 쿼리 항목이 포함될 수 있으므로 이 특정 로그 섹션에 여러 쿼리가 포함될 수 있습니다.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>메트릭

보고된 각 메트릭의 측정 단위는 메트릭(metric) 속성 아래에 가능한 값인 초, 수, 백분율과 함께 제공됩니다. 측정된 메트릭 값은 값(value) 속성으로 보고됩니다.

DurationIncreaseSeconds 속성은 초의 측정 단위를 제공합니다. CriticalErrorCount 측정 단위는 오류 수를 나타내는 숫자입니다.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>근본 원인 분석 및 개선 권장 사항

Intelligent Insights 성능 로그의 마지막 부분은 식별된 성능 저하 문제의 자동화된 근본 원인 분석과 관련이 있습니다. 이 정보는 근본 원인 분석(rootCauseAnalysis_s) 속성에 친숙한 용어로 표시됩니다. 가능한 경우 개선 권장 사항이 로그에 포함됩니다.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Intelligent Insights 성능 로그를 사용자 지정 DevOps 경고 및 보고 기능을 위해 [Azure Log Analytics]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) 또는 타사 솔루션과 함께 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Intelligent Insights](sql-database-intelligent-insights.md) 개념에 대해 알아봅니다.
- [인텔리전스 Insights를 사용하여 Azure SQL Database 성능 문제를 해결](sql-database-intelligent-insights-troubleshoot-performance.md)하는 방법을 알아봅니다.
- [Azure SQL Analytics를 사용하여 Azure SQL Database를 모니터링](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)하는 방법을 알아봅니다.
- [Azure 리소스에서 로그 데이터 수집 및 소비](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)하는 방법을 알아봅니다.



