---
title: Azure Search이란? | Microsoft Docs
description: Azure Search는 완벽하게 관리되는 호스트된 클라우드 Search 서비스입니다. 이 기능 개요에 대해 자세히 알아보세요.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: ad5c60c246c2946e4dd3a5bb6b4d6e8d21d2b03d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-search"></a>Azure Search란?
Azure Search는 개발자에게 웹, 모바일 및 엔터프라이즈 응용 프로그램의 콘텐츠에 대한 풍부한 검색 환경을 추가하기 위한 API 및 도구를 제공하는 SaaS(Search-as-a-Service) 클라우드 솔루션입니다.

기능은 정보 검색의 내재된 복잡성을 표시하는 간단한 [REST API](/rest/api/searchservice/) 또는 [.NET SDK](search-howto-dotnet-sdk.md)를 통해 표시됩니다. API 외에도 Azure Portal에서 운영 및 콘텐츠 관리 지원과 인덱스 프로토타입 및 쿼리를 위한 도구를 제공합니다. 이 서비스는 클라우드에서 실행되므로 인프라 및 가용성은 Microsoft에서 관리합니다.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>기능 요약

| Category | 기능 |
|----------|----------|
|전체 텍스트 검색 및 텍스트 분석 | [전체 텍스트 검색](search-lucene-query-architecture.md)은 대부분의 검색 기반 앱에서 기본적으로 사용되는 검색 방식입니다. 쿼리는 지원되는 구문을 사용하여 작성할 수 있습니다. <br/><br/>[**간단한 쿼리 구문**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)은 논리 연산자, 구 검색 연산자, 후위 연산자, 선행 연산자를 제공합니다.<br/><br/>[**Lucene 쿼리 구문**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)에는 간단한 구문의 모든 연산이 포함되며 유사 항목 검색, 근접 검색, 용어 상승 및 정규식에 대한 확장이 포함됩니다.| 
| 데이터 통합 | Azure Search 인덱스는 JSON 데이터 구조로 제출되기만 하면 모든 원본의 데이터를 수용합니다. <br/><br/> 필요에 따라 Azure에서 지원되는 데이터 원본에 대해 [**인덱서**](search-indexer-overview.md)를 사용해 자동으로 [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) 또는 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)를 크롤링하여 검색 인덱스의 콘텐츠를 주 데이터 저장소와 동기화할 수 있습니다. Azure Blob 인덱서는 Microsoft Office, PDF 및 HTML 문서를 비롯한 [주요 파일 형식 인덱싱](search-howto-indexing-azure-blob-storage.md)을 위해 *문서 크래킹*을 수행할 수 있습니다. |
| 언어 분석 | 분석기는 인덱싱 및 검색 작업 중 텍스트 처리에 사용되는 구성 요소입니다. 두 가지 형식이 있습니다. <br/><br/>[**사용자 지정 어휘 분석기**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)는 표기 일치 및 정규식을 사용하는 복잡한 검색 쿼리에 사용됩니다. <br/><br/>Lucene 또는 Microsoft의 [**언어 분석기**](https://docs.microsoft.com/rest/api/searchservice/language-support)는 동사 시제, 성, 불규칙 복수 명사(예: ‘mouse’와 ‘mice’), 단어 분해, 단어 분철(띄어쓰기가 없는 언어의 경우) 등을 비롯한 언어별 언어 체계를 지능적으로 처리할 수 있습니다. |
| 지리적 검색 | Azure Search는 지리적 위치를 처리하고, 필터링하고, 표시합니다. 이 기능을 통해 사용자는 실제 위치에 대한 검색 결과의 근접도에 따라 데이터를 검색할 수 있습니다. [이 동영상을 시청](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)하거나 [이 예제를 검토](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)하여 자세히 알아보세요. |
| 사용자 환경 기능 | [**검색 제안**](https://docs.microsoft.com/rest/api/searchservice/suggesters)은 검색 창의 자동 완성 쿼리에 사용할 수 있습니다. 사용자가 검색어 일부를 입력하면 인덱스의 실제 문서가 제안됩니다. <br/><br/>[**패싯 탐색**](https://docs.microsoft.com/azure/search/search-faceted-navigation)은 단일 쿼리 매개 변수를 통해 사용하도록 설정됩니다. Azure Search는 자기 주도형 필터링(예: 가격 범위 또는 브랜드별로 카탈로그 항목 필터링)을 위해 코드 숨김 카탈로그 목록으로 사용할 수 있는 패싯 탐색 구조를 반환합니다. <br/><br/> [**필터**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)를 사용하여 패싯 탐색을 응용 프로그램 UI에 통합하고, 쿼리 작성을 향상하고, 사용자 또는 개발자가 지정한 기준에 따라 필터링할 수 있습니다. OData 구문을 사용하여 필터를 만듭니다.<br/><br/> [**적중 항목 강조 표시**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)는 검색 결과의 일치하는 키워드에 텍스트 서식을 적용합니다. 강조 표시된 조각을 반환할 필드를 선택할 수 있습니다.<br/><br/>[**정렬**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)은 인덱스 스키마를 통해 여러 필드에 제공된 후 쿼리 시 단일 검색 매개 변수를 통해 전환됩니다.<br/><br/> 검색 결과 [**페이징**](search-pagination-page-layout.md) 및 제한은 Azure Search에서 검색 결과에 대해 제공하는 세밀하게 조정된 컨트롤을 사용하여 손쉽게 적용할 수 있습니다.  
| 관련성 | [**간단한 점수 매기기**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)는 Azure Search의 주요 이점입니다. 점수 매기기 프로필은 문서 자체의 값에 대한 함수로서 관련성을 모델링하는 데 사용됩니다. 예를 들어 최신 제품 또는 할인 제품을 검색 결과의 더 높은 부분에 나타내려고 할 수 있습니다. 또한 따로 추적하고 저장한 고객 검색 기본 설정에 따라 개인화된 점수에 태그를 사용하여 점수 매기기 프로필을 만들 수도 있습니다. |
| 모니터링 및 보고 | [**검색 트래픽 분석**](search-traffic-analytics.md)을 수집하고 분석하여 사용자가 검색 상자에 입력하는 내용으로부터 정보를 얻을 수 있습니다. <br/><br/>필요한 추가 구성 없이 포털 페이지에서 초당 쿼리 수, 대기 시간 및 제한에 대한 메트릭이 캡처되고 보고됩니다. 인덱스 및 문서 수를 쉽게 모니터링할 수 있으므로 필요에 따라 용량을 조정할 수 있습니다. 자세한 내용은 [서비스 관리](search-manage.md)를 참조하세요. |
| 프로토타입 및 검사용 도구 | 포털에서 [**데이터 가져오기 마법사**](search-import-data-portal.md)를 사용하여 인덱서를 구성하고, 인덱스 디자이너를 사용하여 인덱스를 나타내고, [**검색 탐색기**](search-explorer.md)를 사용하여 쿼리를 테스트하고 점수 매기기 프로필을 구체화할 수 있습니다. 인덱스를 열어 해당 스키마를 볼 수도 있습니다. |
| 인프라 | **가용성이 높은 플랫폼**은 매우 안정적인 검색 서비스 환경을 보장합니다. 규모를 적절하게 조정하면 [Azure Search는 99.9%의 SLA를 제공](https://azure.microsoft.com/support/legal/sla/search/v1_0/)합니다.<br/><br/> **완벽하게 관리 및 확장되는** 종합 솔루션인 Azure Search는 인프라 관리가 전혀 필요 없습니다. 더 많은 문서 저장소, 더 많은 쿼리 부하 또는 둘 모두를 처리할 수 있도록 두 가지 크기를 조정하여 요구 사항에 따라 서비스를 맞춤 구성할 수 있습니다.

## <a name="how-to-use-azure-search"></a>Azure Search를 사용하는 방법
### <a name="step-1-provision-service"></a>1단계: 서비스 프로비전
[Azure Portal](https://portal.azure.com/) 또는 [Azure 리소스 관리 API](/rest/api/searchmanagement/)에서 Azure Search 서비스를 스핀업할 수 있습니다. 다른 구독자와 공유되는 무료 서비스 또는 서비스에만 사용되는 전용 리소스를 제공하는 [유료 계층](https://azure.microsoft.com/pricing/details/search/) 중에서 선택할 수 있습니다. 유료 계층의 경우 다음 2가지 차원에서 서비스를 확장할 수 있습니다. 

- 복제본을 추가하여 과도한 쿼리 부하를 처리하도록 용량을 늘립니다.   
- 파티션을 추가하여 더 많은 문서를 위해 저장소를 늘립니다. 

문서 저장소 및 쿼리 처리량을 별도로 처리하여 프로덕션 요구에 따라 리소스 관리를 조정할 수 있습니다.

### <a name="step-2-create-index"></a>2단계: 인덱스 만들기
검색 가능한 콘텐츠를 업로드하려면 먼저 Azure Search 인덱스를 정의해야 합니다. 인덱스는 데이터가 보관된 데이터베이스 테이블과 비슷하며 검색 쿼리를 수용할 수 있습니다. 데이터베이스의 필드와 마찬가지로, 매핑할 인덱스 스키마를 정의하여 검색하려는 문서의 구조를 반영합니다.

스키마는 Azure Portal에서 만들거나 [.NET SDK](search-howto-dotnet-sdk.md) 또는 [REST API](/rest/api/searchservice/)를 사용하여 프로그래밍 방식으로 만들 수 있습니다.

### <a name="step-3-index-data"></a>3단계: 인덱스 데이터
인덱스를 정의하고 나면 콘텐츠를 업로드할 준비가 된 것입니다. 밀어넣기 또는 끌어오기 모델을 사용할 수 있습니다.

끌어오기 모델은 외부 데이터 원본에서 데이터를 검색합니다. 이 방식은 데이터에 연결, 데이터 읽기 및 데이터 직렬화 등의 데이터 수집 측면을 능률화하고 자동화하는 *인덱서*를 통해 지원됩니다. [인덱서](/rest/api/searchservice/Indexer-operations)는 Azure Cosmos DB, Azure SQL Database, Azure Blob Storage 및 Azure VM에 호스트된 SQL Server에서 사용할 수 있습니다. 필요 시 또는 예약된 데이터 새로 고침을 위해 인덱서를 구성할 수 있습니다.

밀어넣기 모델은 SDK 또는 업데이트된 문서를 인덱스에 보내기 위해 사용되는 REST API를 통해 제공됩니다. JSON 형식을 사용하여 거의 모든 데이터 집합에서 데이터를 밀어넣을 수 있습니다. 데이터 로드에 대한 지침은 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents) 또는 [.NET SDK를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

### <a name="step-4-search"></a>4단계: 검색
인덱스를 채운 후에 REST API 또는 .NET SDK와 함께 간단한 HTTP 요청을 사용하여 서비스 끝점에 [검색 쿼리를 실행](/rest/api/searchservice/Search-Documents) 할 수 있습니다.

## <a name="how-azure-search-compares"></a>Azure Search 비교 원리

Azure Search가 다른 검색 관련 솔루션과 어떻게 비교되는지 질문하는 경우가 많습니다. 다음 표에 주요 차이점이 요약되어 있습니다.

| 비교 대상 | 주요 차이점 |
|--|--|
|Bing | [Bing Web Search API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/)는 Bing.com의 인덱스에서 일치하는 용어를 검색합니다. 인덱스는 HTML, XML 및 공개 사이트의 다른 웹 콘텐츠로 빌드됩니다. [Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)는 개별 웹 사이트에 적용되는 웹 콘텐츠 유형에 대해 동일한 크롤러 기술을 제공합니다.<br/><br/>Azure Search는 사용자가 정의하고 다양한 출처에서 나오는 사용자가 소유한 데이터 및 문서로 채워지는 색인을 검색합니다. Azure Search는 [인덱서](search-indexer-overview.md)를 통해 일부 데이터 소스에 대한 크롤러 기능을 갖추고 있지만 인덱스 스키마를 준수하는 모든 JSON 문서를 통합 검색이 가능한 단일 리소스로 푸시할 수 있습니다. |
|데이터베이스 검색 | [SQL Server 전체 텍스트 검색](https://docs.microsoft.com/sql/relational-databases/search/full-text-search)은 SQL 테이블의 DBMS 내부 콘텐츠용입니다. <br/><br/>Azure Search는 다른 유형의 원본 콘텐츠를 저장하고 언어 및 사용자 지정 분석과 같은 특수 텍스트 처리 기능을 제공합니다. Azure Search의 [전체 텍스트 검색 엔진](search-lucene-query-architecture.md)은 정보 검색 업계 표준인 Apache Lucene을 기반으로 합니다. <br/><br/>리소스 사용률도 또 다른 변곡점입니다. 자연어 검색은 종종 계산 집약적입니다. 전용 솔루션으로 검색을 오프로딩하면 트랜잭션 처리를 위한 리소스가 보존됩니다. 검색 과정을 외부에서 진행하면 규모를 쿼리 볼륨에 맞게 쉽게 조정할 수 있습니다.|
|전용 검색 솔루션 | 온-프레미스 또는 클라우드 서비스 솔루션은 전반적인 기능을 갖춘 전용 검색 솔루션입니다. 검색 기술은 일반적으로 인덱싱 및 쿼리 파이프라인 제어, 풍부한 쿼리 및 필터링 구문에 대한 액세스, 순위 및 관련성 제어, 자체 주도형 및 지능형 검색 기능을 제공합니다. <br/><br/>클라우드 서비스 또는 온-프레미스나 가상 컴퓨터에서 호스트되는 독립 실행형 서버로 제공되는 전용 검색 솔루션을 찾을 수 있습니다. 클라우드 서비스는 [최소한의 오버헤드 및 유지 관리, 규모 조정이 가능한 턴키 솔루션](#cloud-service-advantage)을 원하는 경우에 적합합니다. <br/><br/>클라우드 패러다임 내에서는 일부 공급자가 전체 텍스트 검색, 지리적 검색 및 특정 수준의 검색 입력 모호성을 처리하는 기능을 비롯하여 비슷한 기준 기능을 제공합니다. 일반적으로 이러한 기능은 [특수 기능](#feature-drilldown) 또는 자동 맞춤을 결정하는 API, 도구 및 관리의 전반적인 편리성 및 간편성을 나타냅니다. |

클라우드 공급자 중에서 Azure Search는 정보 검색 및 콘텐츠 탐색 작업을 주로 검색 기능에 의존하는 앱을 위한 Azure의 콘텐츠 저장소 및 데이터베이스에 대한 전체 텍스트 검색 워크로드에 가장 강력합니다. 주요 장점은 다음과 같습니다.

+ 인덱싱 계층에서의 Azure 데이터 통합(크롤러)
+ 중앙 관리를 위한 Azure Portal
+ Azure 규모, 안정성 및 세계 수준의 가용성
+ 56개 언어로 안정적인 전체 텍스트 검색을 수행하기 위한 분석기를 사용한 언어 및 사용자 지정 분석
+ [검색 중심 앱에 공통적인 핵심 기능](#feature-drilldown): 점수 매기기, 패싯, 제안, 동의어, 지리적 검색 기능 등

> [!Note]
> Azure 이외 데이터 원본은 완전히 지원되지만 이 경우 인덱서가 아닌 좀 더 코드 집약적인 푸시 방법론이 사용됩니다. API를 사용하여 모든 JSON 문서 컬렉션을 Azure Search 인덱스로 파이프할 수 있습니다.

Azure Search의 가장 광범위한 기능을 활용할 수 있는 고객 사용 사례로는 온라인 카탈로그, 기간 업무 응용 프로그램 및 문서 검색 응용 프로그램이 포함됩니다.

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

포털에서 다양한 작업을 수행할 수 있으나 Azure Search는 검색 기능을 기존 응용 프로그램에 통합하려는 개발자를 위한 것입니다. 다음과 같은 프로그래밍 인터페이스를 사용할 수 있습니다.

|플랫폼 |설명 |
|-----|------------|
|[REST](/rest/api/searchservice/) | 모든 프로그래밍 플랫폼 및 언어(Xamarin, Java 및 JavaScript 포함)에서 지원하는 HTTP 명령|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API에 대한 .NET 래퍼는 C# 및 .NET Framework를 대상으로 하는 다른 관리되는 코드 언어로 효율적인 코딩 제공 |

## <a name="free-trial"></a>평가판
Azure 구독자는 [무료 계층에서 서비스를 프로비전](search-create-service-portal.md)할 수 있습니다.

구독자가 아닌 경우 [Azure 계정을 무료로 개설](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다. 유료 Azure 서비스를 사용해볼 수 있는 크레딧이 제공됩니다. 크레딧이 소진되더라도 계정이 유지되므로 [무료 Azure 서비스](https://azure.microsoft.com/free/)를 계속 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.

또는 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있음: MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다. 

## <a name="how-to-get-started"></a>시작하는 방법

1. [무료 계층](search-create-service-portal.md)에서 서비스를 만듭니다.

2. 다음 자습서 중 하나 이상을 단계별로 진행합니다. 

  + [.NET SDK를 사용하는 방법](search-howto-dotnet-sdk.md)에서는 관리 코드의 주요 단계를 보여 줍니다.  
  + [Get started with the REST API](https://github.com/Azure-Samples/search-rest-api-getting-started)(REST API 시작)에서는 REST API를 사용하여 같은 단계를 보여 줍니다.  
  + [포털에서 첫 번째 인덱스 만들기](search-get-started-portal.md)에서는 기본 제공 인덱싱 및 프로토타입 기능을 사용합니다.   

검색 엔진은 Mobile Apps, 웹 및 회사 데이터 저장소에서 일반적으로 사용되는 정보 검색 드라이버입니다. Azure Search는 대규모 상업용 웹 사이트와 유사한 검색 환경을 만들기 위한 도구를 제공합니다.

프로그램 관리자인 Liam Cavanagh가 제공하는 이 9분짜리 비디오에서 검색 엔진을 통합할 때 앱이 얻는 이점을 알아보세요. 짧은 데모에 Azure Search의 핵심 기능과 일반적인 워크플로가 제시됩니다. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3분 구간에서는 주요 기능 및 사용 사례가 나옵니다.
+ 3-4분 구간에서는 서비스 프로비전을 다룹니다. 
+ 4-6분 구간에서는 기본 제공 부동산 데이터 집합을 사용하여 인덱스를 만드는 데 사용되는 데이터 가져오기 마법사를 다룹니다.
+ 6-9분 구간에서는 검색 탐색기 및 다양한 쿼리를 다룹니다.


