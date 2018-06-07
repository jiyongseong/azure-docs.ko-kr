---
title: Azure Search를 위해 Azure Cosmos DB 데이터 원본 인덱싱 | Microsoft Docs
description: 이 문서에서는 Azure Cosmos DB 데이터 원본을 사용하여 Azure Search 인덱서를 만드는 방법을 보여 줍니다.
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: a724057981b5b389011ffc4c2fc93994c2b8be9e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777494"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>인덱서를 사용해서 Cosmos DB를 Azure Search에 연결

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Cosmos DB 컬렉션을 데이터 원본으로 사용하는 [Azure Search 인덱서](search-indexer-overview.md)를 구성합니다.
> * JSON 호환 데이터 형식을 사용하여 검색 인덱스를 만듭니다.
> * 주문형 및 되풀이 인덱싱에 대해 인덱서를 구성합니다.
> * 기본 데이터의 변경 내용을 기준으로 인덱스를 증분 방식으로 새로 고칩니다.

> [!NOTE]
> Azure Cosmos DB는 DocumentDB의 다음 세대입니다. 제품 이름이 변경되었지만 Azure Search 인덱서의 `documentdb` 구문은 이전 버전과의 호환성을 위해 Azure Search API와 포털 페이지 둘 다에 계속 존재합니다. 인덱서를 구성할 때 이 문서에 설명된 대로 `documentdb` 구문을 지정해야 합니다.

다음 비디오에서 Azure Cosmos DB 프로그램 관리자 Andrew Liu는 Azure Cosmos DB 컨테이너에 Azure Search 인덱스를 추가하는 방법을 보여 줍니다.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>지원되는 API 유형

Azure Cosmos DB는 다양한 데이터 모델과 API를 지원하지만 Azure Search 인덱서 프로덕션 지원은 SQL API에만 확장됩니다. MongoDB API 지원은 현재 공개 미리 보기로 제공됩니다.  

추가 API 지원이 제공될 예정입니다. 먼저 지원할 API의 우선 순위 결정에 도움이 되도록 사용자 의견 웹 사이트에서 투표해 주세요.

* [테이블 API 데이터 원본 지원](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Graph API 데이터 원본 지원](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Apache Cassandra API 데이터 원본 지원](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>필수 조건

Cosmos DB 계정 외에도 [Azure Search 서비스](search-create-service-portal.md)가 필요합니다. 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure Search 인덱서 개념

**데이터 원본**은 인덱싱할 데이터, 자격 증명 및 데이터의 변경 사항 식별 정책(예: 컬렉션 내 수정 또는 삭제된 문서)을 지정합니다. 데이터 소스는 독립 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

**인덱서** 는 데이터 소스에서 대상 검색 인덱스로 데이터 흐름 방법을 설명합니다. 인덱서를 사용하여 다음을 수행할 수 있습니다.

* 인덱스를 채우기 위해 데이터에 대한 일회성 복사를 수행합니다.
* 예약에 따라 인덱스를 데이터 소스의 변경 사항과 동기화합니다.
* 필요에 따라 인덱스에 대해 요청 시 업데이트를 호출합니다.

Azure Cosmos DB 인덱서를 설정하려면 인덱스, 데이터 원본, 인덱서를 차례로 만들어야 합니다. [포털](search-import-data-portal.md), [.NET SDK](/dotnet/api/microsoft.azure.search) 또는 [REST API](/rest/api/searchservice/)를 사용하여 이러한 개체를 만들 수 있습니다. 

이 문서에서는 REST API를 사용하는 방법을 보여 줍니다. 포털을 사용할 경우 [데이터 가져오기 마법사](search-import-data-portal.md)가 인덱스를 비롯한 이러한 모든 리소스의 생성 과정을 안내합니다.

> [!TIP]
> Azure Cosmos DB 대시보드에서 **데이터 가져오기** 마법사를 시작하여 해당 데이터 원본에 대한 인덱싱을 단순화할 수 있습니다. 왼쪽 탐색에서 **컬렉션** > **Azure Search 추가**로 이동하여 시작합니다.

> [!NOTE] 
> 지금은 Azure Portal 또는 .NET SDK를 사용하여 **MongoDB** 데이터 원본을 만들거나 편집할 수 없습니다. 그러나 포털에서 MongoDB 인덱서의 실행 기록을 모니터링할 수 **있습니다**.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기
데이터 원본을 만들려면 POST를 수행합니다.

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

* **이름**: 데이터베이스를 나타낼 이름을 선택합니다.
* **형식**: `documentdb`여야 합니다.
* **자격 증명**:
  
  * **connectionString**: 필수입니다. Azure Cosmos DB 데이터베이스에 대한 연결 정보를 `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` 형식으로 지정합니다. MongoDB 컬렉션의 경우 **ApiKind=MongoDB**를 연결 문자열에 추가합니다. `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDB` 
* **컨테이너**:
  
  * **이름**: 필수입니다. 인덱싱할 데이터베이스 컬렉션의 ID를 지정합니다.
  * **쿼리**: 선택 사항입니다. 추상 JSON 문서를 Azure Search가 인덱싱할 수 있는 평면 스키마로 평면화하는 쿼리를 지정할 수 있습니다. MongoDB 컬렉션의 경우 쿼리가 지원되지 않습니다. 
* **dataChangeDetectionPolicy**: 권장 사항입니다. [변경된 문서 인덱싱](#DataChangeDetectionPolicy) 섹션을 참조하세요.
* **dataDeletionDetectionPolicy**: 선택 사항입니다. [삭제된 문서 인덱싱](#DataDeletionDetectionPolicy) 섹션을 참조하세요.

### <a name="using-queries-to-shape-indexed-data"></a>쿼리를 사용하여 인덱싱된 데이터 형성
중첩된 속성 또는 배열을 평면화하고, JSON 속성을 프로젝션하고, 인덱싱할 데이터를 필터링하는 SQL 쿼리를 지정할 수 있습니다. 

> [!WARNING]
> **MongoDB** 컬렉션에 대한 사용자 지정 쿼리는 지원되지 않습니다. `container.query` 매개 변수를 null로 설정하거나 생략해야 합니다. 사용자 지정 쿼리를 사용해야 하는 경우 [사용자 의견](https://feedback.azure.com/forums/263029-azure-search)에서 알려주세요.

예제 문서:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

필터 쿼리:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

평면화 쿼리:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
프로젝션 쿼리:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


배열 평면화 쿼리:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기
대상 Azure Search 인덱스가 아직 없으면 만듭니다. [Azure Portal UI](search-create-index-portal.md)를 사용하거나 [인덱스 REST API 만들기](/rest/api/searchservice/create-index) 또는 [인덱스 클래스](/dotnet/api/microsoft.azure.search.models.index)를 사용하여 인덱스를 만들 수 있습니다.

다음 예제에서는 id 및 설명 필드를 사용해서 인덱스를 만듭니다.

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

대상 인덱스의 스키마가 소스 JSON 문서의 스키마 또는 사용자 지정 쿼리 프로젝션의 출력과 호환되는지 확인합니다.

> [!NOTE]
> 분할된 컬렉션의 경우, 기본 문서 키는 Azure Cosmos DB의 `_rid` 속성입니다. 필드 이름은 밑줄 문자로 시작할 수 없으므로 Azure Search에서 이름이 `rid`로 자동으로 바뀝니다. 또한 Azure Cosmos DB의 `_rid` 값은 Azure Search 키에 잘못된 문자를 포함합니다. 따라서 `_rid` 값은 Base64로 인코딩됩니다.
> 
> MongoDB 컬렉션의 경우 Azure Search에서 `_id` 속성의 이름이 `doc_id`로 자동으로 바뀝니다.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON 데이터 형식과 Azure Search 데이터 형식 사이의 매핑
| JSON 데이터 형식 | 호환되는 대상 인덱스 필드 형식 |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| 정수와 같이 보이는 숫자 |Edm.Int32, Edm.Int64, Edm.String |
| 부동소수점처럼 보이는 숫자 |Edm.Double, Edm.String |
| 문자열 |Edm.String |
| 기본 형식의 배열, 예: ["a", "b", "c"] |Collection(Edm.String) |
| 날짜처럼 보이는 문자열 |Edm.DateTimeOffset, Edm.String |
| GeoJSON 개체, 예: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 기타 JSON 개체 |해당 없음 |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>3단계: 인덱서 만들기

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-indexer)를 확인하세요.

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>요청 시 인덱서 실행
인덱서를 일정에 따라 주기적으로 실행할 수 있을 뿐 아니라 요청 시에 호출할 수도 있습니다.

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> API 실행이 성공적으로 반환되면 인덱서 호출이 예약된 것이지만 실제 처리는 비동기적으로 발생합니다. 

포털에서 또는 인덱서 상태 가져오기 API(다음에 설명됨)를 사용하여 인덱서 상태를 모니터링할 수 있습니다. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>인덱서 상태 가져오기
인덱서의 상태 및 실행 기록을 검색할 수 있습니다.

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

응답에는 전반적인 인덱서 상태, 마지막(또는 진행 중인) 인덱서 호출 및 최근 인덱서 호출 기록이 포함됩니다.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

실행 기록은 50개의 최근에 완료한 실행까지 포함할 수 있으며, 시간 순서의 반대로 정렬됩니다(최신 항목이 응답에서 먼저 표시됨)

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>변경된 문서 인덱싱
데이터 변경 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재 지원되는 정책은 Azure Cosmos DB에서 제공하는 `_ts`(타임스탬프) 속성을 사용하는 `High Water Mark` 정책뿐이며, 다음과 같이 지정됩니다.

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

적절한 인덱서 성능을 보장하기 위해서는 반드시 이 정책을 사용하는 것이 좋습니다. 

사용자 지정 쿼리를 사용하는 경우 `_ts` 속성이 쿼리에 의해 프로젝션되어야 합니다.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>증분 진행률 및 사용자 지정 쿼리
인덱싱하는 동안 증분 진행률은 인덱서 실행이 일시적 오류 또는 실행 시간 제한에 의해 중단되었는지를 확인합니다. 인덱서는 처음부터 전체 컬렉션을 다시 인덱스하는 대신 다음으로 실행할 위치를 선택할 수 있습니다. 대규모 컬렉션을 인덱싱할 때 특히 유용합니다. 

사용자 지정 쿼리를 사용하는 경우 증분 진행률을 사용하려면 쿼리가 `_ts` 열을 기준으로 결과를 정렬해야 합니다. 그러면 Azure Search에서 사용하는 정기적 검사점이 오류가 있는 경우 증분 진행률을 제공할 수 있습니다.   

어떤 경우에는 쿼리에 `ORDER BY [collection alias]._ts` 절이 포함되더라도 Azure Search이 `_ts` 기준으로 쿼리를 정렬하는지 유추하지 않을 수 있습니다. Azure Search에서 결과가 `assumeOrderByHighWaterMarkColumn` 구성 속성을 사용하여 정렬되도록 지시할 수 있습니다. 이 힌트를 지정하려면 다음과 같이 인덱서를 만들거나 업데이트합니다. 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>삭제된 문서 인덱싱
컬렉션에서 행이 삭제된 경우 일반적으로 검색 인덱스에서도 해당 행을 삭제하려고 할 것입니다. 데이터 삭제 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재까지 지원되는 유일한 정책은 다음과 같이 지정되는 `Soft Delete` 정책입니다(삭제 시 일부 유형의 플래그로 표시됨).

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

사용자 지정 쿼리를 사용하는 경우 `softDeleteColumnName`에서 참조되는 속성이 쿼리에 의해 프로젝션됩니다.

다음 예제에서는 일시 삭제 정책을 사용하여 데이터 원본을 만듭니다.

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>다음 단계
축하합니다! 인덱서를 사용하여 Azure Search에 Azure Cosmos DB를 통합하는 방법을 알아보았습니다.

* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 서비스 페이지](https://azure.microsoft.com/services/cosmos-db/)를 참조하세요.
* Azure Search에 대해 자세히 알아보려면 [Search 서비스 페이지](https://azure.microsoft.com/services/search/)를 참조하세요.
