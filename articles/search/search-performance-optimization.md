---
title: Azure Search 성능 및 최적화 고려 사항 | Microsoft Docs
description: Azure Search 성능 조정 및 최적의 크기 조정 구성
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: 89c0352723f1ed00784250b566902028af853d10
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Azure Search 성능 및 최적화 고려 사항
뛰어난 검색 환경은 많은 모바일 및 웹 응용 프로그램의 성공에 반드시 필요합니다. 부동산에서 중고차 마켓플레이스와 온라인 카탈로그에 이르기까지 빠른 검색 및 관련성 높은 결과는 고객 환경에 영향을 줍니다. 이 문서는 확장성, 다국어 지원 또는 사용자 지정 순위 등을 요구하는 고급 시나리오에서, 특히 Azure Search를 최대한 활용하는 방법에 대한 모범 사례를 알아내는 데 도움을 주기 위해 작성되었습니다.  또한 이 문서는 내부 구성을 간략하게 설명하고 실제 고객 앱에서 효과적으로 작동하는 접근 방법을 소개합니다.

## <a name="performance-and-scale-tuning-for-search-services"></a>Search 서비스의 성능 및 규모 확장
우리 모두 Bing, Google 등의 검색 엔진과 이러한 검색 엔진이 제공하는 높은 성능에 익숙해져 있습니다.  따라서 고객이 검색 기능이 지원되는 웹 또는 모바일 응용 프로그램을 사용할 때도 비슷한 성능 특성을 기대할 것입니다.  검색 성능을 최적화할 때 가장 좋은 접근 방법은 쿼리가 완료되고 결과를 반환하는 데 소요되는 시간을 나타내는 대기 시간을 집중적으로 관리하는 것입니다.  검색 대기 시간을 최적화하는 경우 다음 작업이 중요합니다.

1. 일반적인 검색 요청을 완료하는 데 걸리는 목표 대기 시간(또는 최대 시간)을 선택합니다.
2. 실제 데이터 집합을 사용하여 검색 서비스에 대한 실제 작업을 만들고 테스트하여 이러한 대기 시간 속도를 측정합니다.
3. 낮은 QPS(초당 쿼리 수)로 시작한 후 쿼리 대기 시간이 정의된 목표 대기 시간 아래로 떨어질 대까지 테스트에서 실행되는 쿼리 수를 계속 늘립니다.  이러한 방식은 응용 프로그램 사용량이 증가함에 따라 확장을 계획하는 데 도움이 되는 중요한 벤치마크입니다.
4. 가능하면 HTTP 연결을 다시 사용합니다.  Azure Search .NET SDK를 사용하는 경우 인스턴스 또는 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 인스턴스를 다시 사용해야 하고 REST API를 사용하는 경우에는 단일 HttpClient를 다시 사용해야 합니다.

이러한 테스트 워크로드를 만드는 동안 다음과 같은 Azure Search의 몇 가지 특성에 유의해야 합니다.

1. 한 번에 너무 많은 검색 쿼리를 푸시하면 Azure Search 서비스에서 사용할 수 있는 리소스에 과부하가 걸립니다.  이 경우 HTTP 503 응답 코드가 표시됩니다.  이러한 이유로 검색 요청 추가에 따른 대기 시간 속도의 차이를 알 수 있도록 다양한 범위의 검색 요청으로 시작하는 것이 좋습니다.
2. Azure Search로 콘텐츠를 업로드하면 Azure Search 서비스의 전반적인 성능 및 대기 시간에 영향을 줍니다.  사용자가 검색을 수행하는 동안 데이터를 전송하려는 경우 테스트에서 이 워크로드를 고려하는 것이 중요합니다.
3. 모든 검색 쿼리가 동일한 성능 수준에서 수행되는 것은 아닙니다.  예를 들어 문서 조회 또는 검색 제안은 패싯과 필터를 상당히 많이 사용하는 쿼리보다 더 빠르게 작동합니다.  테스트를 작성할 때는 마주치게 될 다양한 쿼리를 고려하는 것이 좋습니다.  
4. 동일한 검색 요청을 지속적으로 실행하는 경우 좀 더 이질적인 쿼리 집합으로 데이터를 쿼리할 때보다 성능이 더 나아 보일 수 있으므로 검색 요청을 변형해서 테스트하는 것도 중요합니다.

> [!NOTE]
> [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)은 Azure Search에 대해 쿼리를 실행하는 데 필요한 HTTP 요청을 실행할 수 있도록 하며 요청의 병렬 처리를 사용할 수 있게 하므로 벤치마크 테스트를 수행하는 데 있어서 아주 유용한 방법입니다.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>높은 쿼리 속도 및 제한된 요청에 따라 Azure Search 확장
제한된 요청을 너무 많이 받거나 쿼리 부하가 높아져서 목표 대기 시간을 초과하게 될 경우 다음 두 가지 방법 중 하나로 대기 시간 속도를 줄여볼 수 있습니다.

1. **복제본 증가:** 복제본은 데이터 복사본과 같은 것으로, Azure Search 기능은 이러한 복제본을 통해 여러 복사본에 대한 요청의 부하를 분산시킬 수 있습니다.  복제본에 대한 모든 부하 분산 및 데이터 복제 작업은 Azure Search를 통해 관리되며 언제든지 서비스에 할당되는 복제본 수를 변경할 수 있습니다.  표준 검색 서비스에서는 최대 12개, 기본 검색 서비스에서는 최대 3개의 복제본을 할당할 수 있습니다. 복제본은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.
2. **검색 계층 증가:** Azure Search에는 [여러 계층](https://azure.microsoft.com/pricing/details/search/) 이 포함되어 있으며 이러한 각 계층은 다른 수준의 성능을 제공합니다.  경우에 따라 쿼리 수가 너무 많아서 복제본이 최대 제한을 초과한 경우에도 현재 사용자가 있는 계층에서 대기 시간 속도가 충분히 낮지 않을 수 있습니다.  이 경우 많은 수의 문서와 상당히 많은 쿼리 워크로드가 있는 시나리오에 적합한 Azure Search S3 계층 등의 상위 검색 계층 중 하나를 활용하는 것을 고려할 수 있습니다.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>느린 개별 쿼리에 맞게 Azure Search 속도 조정
대기 시간 속도가 느려질 수 있는 또 다른 이유는 단일 쿼리를 완료하는 데 시간이 너무 오래 걸려서일 수도 있습니다.  이 경우 복제본을 추가해도 대기 시간 속도가 향상되지 않습니다.  이러한 경우에 다음 두 가지 옵션을 사용할 수 있습니다.

1. **파티션 증가** 파티션은 추가 리소스로 데이터를 분할하는 메커니즘입니다.  이러한 이유로 두 번째 파티션을 추가하면 데이터가 둘로 분할됩니다.  세 번째 파티션을 추가하면 인덱스가 셋으로 분할됩니다.  또한 상황에 따라 계산 병렬 처리로 인해 느린 쿼리가 더 빠르게 수행되는 효과가 나타나기도 합니다.  선택도가 낮은 쿼리를 포함하는 쿼리에서 이 병렬 처리가 매우 잘 작동한다는 몇 가지 예가 있습니다.  많은 문서와 일치하는 쿼리로 구성되거나 패싯 중에 많은 문서의 수를 계산해야 하는 경우가 여기에 해당됩니다.  문서의 관련성 점수를 매기거나 문서 수를 계산하기 위해 많은 계산 작업이 필요하므로 파티션을 더 추가하면 추가 계산 성능을 제공하는 데 도움이 될 수 있습니다.  
   
   표준 검색 서비스의 파티션 제한은 12개이고, 기본 검색 서비스의 파티션 제한은 1개입니다.  파티션은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.
2. **높은 카디널리티 필드 제한:** 높은 카디널리티 필드는 상당히 많은 수의 고유 값을 가지고 있어서 결과 계산을 위해 많은 리소스를 소비하는 패싯 가능 또는 필터링 가능 필드로 구성되어 있습니다.   예를 들어 제품 ID 또는 설명 필드를 패싯 가능/필터링 가능 필드로 설정하면 문서 간 값 대부분이 고유하므로 높은 카디널리티가 형성됩니다. 가능한 경우 높은 카디널리티 필드의 수를 제한하도록 합니다.
3. **검색 계층 증가:** 상위 Azure Search 계층으로 이동하는 것도 느린 쿼리의 성능을 향상시키는 또 다른 방법일 수 있습니다.  또한 각 상위 계층은 더 빠른 CPU와 더 많은 메모리를 제공하므로 쿼리 성능에 긍정적인 영향을 줄 수 있습니다.

## <a name="scaling-for-availability"></a>가용성에 따른 크기 조정
복제본은 쿼리 대기 시간을 줄이는 데 도움이 될 뿐만 아니라 고가용성도 가능하게 합니다.  단일 복제본을 사용하는 경우 소프트웨어 업데이트 이후의 서버 다시 부팅 또는 발생하게 되는 기타 유지 관리 이벤트로 인한 주기적인 작동 중지가 예상될 수 있습니다.  결과적으로 응용 프로그램이 높은 검색(쿼리) 및 쓰기(인덱싱 이벤트) 가용성을 요구하는지를 고려하는 것이 중요합니다.  Azure Search는 다음 특성을 갖는 모든 유료 Search 서비스에 대해 SLA 옵션을 제공합니다.

* 읽기 전용 작업(쿼리)의 고가용성을 위한 복제본 2개
* 읽기/쓰기 작업(쿼리 및 인덱싱)의 고가용성을 위한 복제본 3개 이상

이에 대한 자세한 내용은 [Azure Search 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/search/v1_0/)을 참조하세요.

복제본은 데이터의 복사본이므로 복제본이 여러 개 있으면 다른 복제본에 대해 쿼리가 계속 실행되면서, Azure Search 기능이 한 번에 한 복제본에 대해 컴퓨터 다시 부팅 및 유지 관리를 수행할 수 있습니다.  따라서 데이터가 1개 더 적은 복사본에 대해 실행되어야 하는 쿼리에 이러한 가동 중지가 어떤 영향을 줄 수 있는지도 고려해야 합니다.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>지리적으로 분산된 워크로드 조정 및 지리적 중복 제공
지리적으로 분산된 워크로드의 경우 Azure Search 서비스가 호스트되는 데이터 센터에서 멀리 떨어진 사용자의 대기 시간 속도가 더 높은 경우가 확인되고 있습니다.  이러한 이유로 종종 이러한 사용자와 더 가까운 지역에 여러 검색 서비스를 두는 것이 중요합니다.  Azure Search 기능은 현재 여러 지역에 걸쳐 Azure Search 인덱스를 자동으로 지리적으로 복제하는 방법을 제공하지 않지만, 이러한 프로세스를 간단히 구현하고 관리하는 데 사용할 수 있는 몇 가지 기술이 있습니다. 이러한 기술은 다음 일부 섹션에 간단히 설명되어 있습니다.

Search 서비스 집합을 지리적으로 분산시키는 이유는 다음 예제와 같이 둘 이상의 지역에서 둘 이상의 인덱스를 사용할 수 있게 지원함으로써 대기 시간이 최소인 Azure Search 서비스로 사용자를 라우팅하기 위해서입니다.

   ![지역별 서비스 크로스탭][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>여러 Azure Search 서비스에서 데이터를 동기화 상태로 유지
분산 검색 서비스를 동기화 상태로 유지하기 위한 두 가지 방법이 있습니다. 이러한 방법은 [Azure Search 인덱서](search-indexer-overview.md) 또는 푸시 API([Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)라고도 함)를 사용합니다.  

### <a name="azure-search-indexers"></a>Azure Search 인덱서
Azure Search 인덱서를 사용하고 있다면 이미 Azure SQL DB 또는 Azure Cosmos DB와 같은 중앙 데이터 저장소에서 데이터 변경 내용을 가져오고 있는 것입니다. 새 Search 서비스를 만들 때 해당 서비스에 대해 동일한 이 데이터 저장소를 가리키는 새 Azure Search 인덱서를 만들면 됩니다. 이러한 방식으로 새 변경 내용이 데이터 저장소에 들어올 때마다 다양한 인덱서에 의해 인덱싱됩니다.  

이러한 아키텍처 형태의 예는 다음과 같습니다.

   ![분산 인덱서 및 서비스 조합을 포함하는 단일 데이터 원본][2]

### <a name="push-api"></a>푸시 API
Azure Search 푸시 API를 사용하여 [Azure Search 인덱스의 내용을 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index)하는 경우 업데이트가 필요할 때마다 모든 Search 서비스에 변경 내용을 푸시하여 다양한 Search 서비스를 동기화 상태로 유지할 수 있습니다.  이 작업을 수행할 때는 한 검색 서비스에 대한 업데이트가 실패하고 하나 이상의 업데이트가 성공하는 경우를 잘 처리하는 것이 중요합니다.

## <a name="leveraging-azure-traffic-manager"></a>Azure Traffic Manager 활용
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 사용하면 여러 지역에 위치한 웹 사이트로 요청을 라우팅할 수 있습니다. 그러면 이러한 요청은 여러 Azure Search 서비스를 통해 처리될 수 있습니다.  이 Traffic Manager의 장점 중 하나는 Azure Search가 사용 가능한지 조사하고 가동 중지 시간이 발생할 경우 사용자를 대체 Search 서비스로 라우팅할 수 있다는 것입니다.  또한 Azure 웹 사이트를 통해 검색 요청을 라우팅하면, 웹 사이트는 작동되지만 Azure Search는 작동되지 않는 경우의 부하를 Azure Traffic Manager를 사용하면 분산할 수 있습니다.  Traffic Manager를 활용하는 아키텍처 예는 다음과 같습니다.

   ![지역별 서비스 크로스탭(중앙 Traffic Manager 포함)][3]

## <a name="monitoring-performance"></a>성능 모니터링
Azure Search는 [STA(트래픽 분석 검색)](search-traffic-analytics.md)를 통해 서비스 성능을 분석하고 모니터링하는 기능을 제공합니다. 필요에 따라, 집계된 메트릭뿐 아니라 개별 검색 작업이 STA를 통해 Azure Storage 계정에 기록될 수 있습니다. 그러면 기록된 데이터가 Power BI에서 분석을 위해 처리되거나 시각화될 수 있습니다.  STA 메트릭을 사용하여, 쿼리의 평균 개수 또는 쿼리 응답 시간과 같은 성능 통계를 검토할 수 있습니다.  또한 작업 로깅을 사용하여 특정 검색 작업의 세부 정보를 찾아볼 수 있습니다.

STA는 해당 Azure Search 관점에서 대기 시간 속도를 이해하는 중요한 도구입니다.  로깅된 쿼리 성능 메트릭은 Azure Search에서 쿼리가 완전히 처리되는 데 소요되는 시간(요청된 시간부터 전송된 시간까지)을 기준으로 하므로 이 값을 사용하여 대기 시간 문제가 Azure Search 서비스 쪽에서 발생한 것인지 또는 서비스 외부(예: 네트워크 대기 시간)에서 발생한 것인지 확인할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
각각의 가격 책정 계층 및 서비스 제한에 대해 자세히 알아보려면 [Azure Search의 서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

파티션 및 복제본 조합에 대해 자세히 알아보려면 [용량 계획](search-capacity-planning.md) 을 참조하세요.

성능에 대해 좀 더 드릴다운하고 이 문서에 설명된 최적화 구현 방법의 추가적인 예시를 보려면 다음 동영상을 시청하세요.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
