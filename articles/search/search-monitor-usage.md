---
title: Azure Search 서비스의 사용량 및 통계 모니터링 | Microsoft Docs
description: Microsoft Azure에서 호스팅되는 Search 서비스인 Azure Search에 대해 리소스 소비 및 인덱스 크기를 추적합니다.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 286569eef8e17909ecab017b67b0ffc044a4bfe4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-an-azure-search-service"></a>Azure Search 서비스 모니터링

Azure Search는 검색 서비스의 사용량 및 성능을 추적하기 위한 다양한 리소스를 제공합니다. Power BI에서 메트릭, 로그, 인덱스 통계 및 확장된 모니터링 기능에 액세스할 수 있게 해줍니다. 이 문서에서는 여러 모니터링 전략을 사용하는 방법 및 결과 데이터를 해석하는 방법을 설명합니다.

## <a name="azure-search-metrics"></a>Azure Search 메트릭
메트릭은 거의 실시간으로 검색 서비스에 대한 고급 정보를 제공하며, 추가 설치 없이 모든 서비스에 사용할 수 있습니다. 최대 30일까지 서비스 성능을 추적할 수 있습니다.

Azure Search는 다음 세 가지 메트릭에 대한 데이터를 수집합니다.

* 검색 대기 시간: 검색 쿼리를 처리하는 데 필요한 Search 서비스의 시간을 1분마다 집계합니다.
* QPS(초당 검색 쿼리 수): 초당 수신된 검색 쿼리 수를 1분마다 집계합니다.
* 제한된 검색 쿼리 백분율: 제한된 검색 쿼리의 비율을 1분마다 집계합니다.

![QPS 활동의 스크린 샷][1]

### <a name="set-up-alerts"></a>경고 설정
메트릭 세부 정보 페이지에서, 메트릭이 정의된 임계값을 초과하면 전자 메일 알림 또는 자동화된 작업을 트리거하도록 경고를 구성할 수 있습니다.

메트릭에 대한 자세한 내용은 Azure Monitor에 대한 전체 설명서를 확인하세요.  

## <a name="how-to-track-resource-usage"></a>리소스 사용량을 추적하는 방법
인덱스 및 문서 크기의 증가를 추적하면 서비스에 규정한 상한에 도달하기 전에 용량을 조정할 수 있습니다. 이 작업은 포털에서 수행하거나 REST API를 사용하여 프로그래밍 방식으로 수행할 수 있습니다.

### <a name="using-the-portal"></a>포털 사용

리소스 사용량을 모니터링하려면 [포털](https://portal.azure.com)에서 서비스에 대한 개수와 통계를 봅니다.

1. [포털](https://portal.azure.com)에 로그인합니다.
2. Azure Search 서비스의 서비스 대시보드를 엽니다. 홈페이지에서 서비스에 대한 타일을 찾거나 표시줄에 있는 찾아보기에서 서비스를 탐색할 수 있습니다.

사용 섹션에는 사용 가능한 리소스의 어떤 부분이 현재 사용 중인지 알려주는 측정기가 포함됩니다. 인덱스, 문서, 저장소에 대한 서비스당 제한에 대한 정보는 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

  ![사용량 타일][2]

> [!NOTE]
> 위의 스크린샷은 무료 서비스의 경우로 여기에는 하나의 복제본과 각각의 파티션의 최대값이 있으며, 3개의 인덱스, 10,000개의 문서 또는 50MB의 데이터 중 먼저 도달하는 것까지만 호스팅할 수 있습니다. 기본 또는 표준 계층에서 만든 서비스는 훨씬 더 큰 서비스 제한이 있습니다. 계층 선택에 대한 자세한 내용은 [계층 또는 SKU 선택](search-sku-tier.md)을 참조하세요.
>
>

### <a name="using-the-rest-api"></a>REST API 사용
Azure Search REST API와 .NET SDK는 모두 서비스 메트릭에 대한 프로그래밍 방식 액세스를 제공합니다.  [인덱서](https://msdn.microsoft.com/library/azure/dn946891.aspx)를 사용하여 Azure SQL Database나 Azure Cosmos DB에서 인덱스를 로드하는 경우, 추가 API를 사용하여 필요한 숫자를 얻을 수 있습니다.

* [인덱스 통계 가져오기](/rest/api/searchservice/get-index-statistics)
* [문서 수 계산](/rest/api/searchservice/count-documents)
* [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>로그 및 메트릭을 내보내는 방법

이전 섹션에서 설명한 것처럼 서비스의 작업 로그와 메트릭의 원시 데이터를 내보낼 수 있습니다. 작업 로그를 통해 서비스가 사용되는 방식을 파악할 수 있으며 저장소 계정에 데이터를 복사할 때 Power BI에서 작업 로그를 사용할 수 있습니다. Azure Search는 이 목적을 위해 모니터링 Power BI 콘텐츠 팩을 제공합니다.


### <a name="enabling-monitoring"></a>모니터링 사용
모니터링 사용 옵션 아래에 있는 [Azure Portal](http://portal.azure.com)에서 Azure Search 서비스를 엽니다.

내보낼 데이터를 선택합니다(로그, 메트릭 또는 둘 다). 데이터를 저장소 계정에 복사하고, 이벤트 허브로 전송하거나 Log Analytics로 내보낼 수 있습니다.

![포털에서 모니터링을 사용하는 방법][3]

PowerShell 또는 Azure CLI를 사용하려면 [여기](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs) 설명서를 참조하세요.

### <a name="logs-and-metrics-schemas"></a>로그 및 메트릭 스키마
데이터가 저장소 계정에 복사될 때 해당 데이터는 JSON 형식으로 다음 두 컨테이너에 저장됩니다.

* insights-logs-operationlogs: 검색 트래픽 로그인 경우
* insights-metrics-pt1m: 메트릭인 경우

시간 및 컨테이너당 하나의 Blob가 있습니다.

경로 예: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>로그 스키마
로그 Blob는 검색 서비스 트래픽 로그를 포함합니다.
각 Blob는 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.
각 Blob에는 같은 시간 중에 발생한 모든 작업에 대한 레코드가 포함됩니다.

| Name | type | 예 | 메모 |
| --- | --- | --- | --- |
| 실시간 |Datetime |"2015-12-07T00:00:43.6872559Z" |작업 타임스탬프 |
| ResourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ResourceId |
| operationName |string |"Query.Search" |작업 이름 |
| operationVersion |string |"2015-02-28" |사용된 api-version |
| 카테고리 |string |"OperationLogs" |constant |
| resultType |string |"Success" |가능한 값: Success 또는 Failure |
| resultSignature |int |200 |HTTP 결과 코드 |
| durationMS |int |50 |밀리초 단위의 작업 기간 |
| properties |object |다음 테이블 참조 |데이터별 작업을 포함하는 개체 |

**속성 스키마**
| Name | type | 예 | 메모 |
| --- | --- | --- | --- |
| 설명 |string |"GET /indexes('content')/docs" |작업의 끝점 |
| 쿼리 |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |쿼리 매개 변수 |
| 문서 |int |42 |처리된 문서 수 |
| IndexName |string |"testindex" |작업과 연결된 인덱스의 이름 |

#### <a name="metrics-schema"></a>메트릭 스키마
| Name | type | 예 | 메모 |
| --- | --- | --- | --- |
| ResourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |리소스 ID |
| metricName |string |"Latency" |메트릭 이름 |
| 실시간 |Datetime |"2015-12-07T00:00:43.6872559Z" |작업의 타임스탬프 |
| average |int |64 |메트릭 시간 간격에 원시 샘플의 평균 값 |
| minimum |int |37 |메트릭 시간 간격에 원시 샘플의 최소 값 |
| maximum |int |78 |메트릭 시간 간격에 원시 샘플의 최대 값 |
| total |int |258 |메트릭 시간 간격에 원시 샘플의 총 값 |
| count |int |4 |메트릭을 생성하는 데 사용되는 원시 샘플 수 |
| timegrain |string |"PT1M" |ISO 8601에서 메트릭의 시간 조직 |

모든 메트릭은 1 분 간격으로 보고됩니다. 각 메트릭은 분당 최소, 최대 및 평균 값을 표시합니다.

SearchQueriesPerSecond 메트릭의 경우, 최소값은 해당 분 동안 등록된 초당 검색 쿼리 수에 대한 가장 낮은 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분에 대한 집계입니다.
1분 동안 이 시나리오에 관한 생각: SearchQueriesPerSecond에 대한 최대값 만큼 부하가 매우 높은 1초가 지난 후 58초 동안 중간 부하 상태가 계속되다가 마지막으로 최소값인 쿼리 한 개만 있는 1초가 이어집니다.

ThrottledSearchQueriesPercentage의 경우, 최소값, 최대값, 평균 및 합계가 모두 같은 값이며, 이 값은 1분 동안 총 검색 쿼리 수에서 제한된 검색 쿼리의 비율입니다.

## <a name="analyzing-your-data-with-power-bi"></a>Power BI를 사용하여 데이터 분석

[Power BI](https://powerbi.microsoft.com)를 사용하여 데이터를 탐색하고 시각화하는 것이 좋습니다. Azure Storage 계정에 쉽게 연결하고 데이터 분석을 신속하게 시작할 수 있습니다.

Azure Search는 미리 정의된 차트와 테이블을 사용하여 검색 트래픽을 모니터링하고 이해할 수 있는 [Power BI 콘텐츠 팩](https://app.powerbi.com/getdata/services/azure-search)을 제공합니다. 자동으로 데이터를 연결하고 검색 서비스에 대한 시각적 정보를 제공하는 Power BI 보고서 집합을 포함하고 있습니다. 자세한 내용은 [콘텐츠 팩 도움말 페이지](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/)를 참조하세요.

![Azure Search에 대한 Power BI 대시보드][4]

## <a name="next-steps"></a>다음 단계
기존 서비스에 대해 파티션과 복제본의 할당 균형을 조정하는 방법에 대한 지침은 [복제본 및 파티션 확장](search-limits-quotas-capacity.md)을 검토하세요.

서비스 관리에 대한 자세한 내용은 [Microsoft Azure에서 Search 서비스 관리](search-manage.md) 또는 튜닝 지침은 [성능 및 최적화](search-performance-optimization.md)를 방문하십시오.

놀라운 보고서 만들기에 대해 자세히 알아보세요. 자세한 내용은 [Power BI Desktop 시작](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)을 참조하세요.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
