---
title: Azure Cosmos DB 인덱싱 정책 | Microsoft Docs
description: Azure Cosmos DB에서 인덱싱의 작동 방식을 파악하고 자동 인덱싱 및 성능 향상을 위해 인덱싱 정책을 구성 및 변경하는 방법에 대해 알아봅니다.
keywords: 인덱싱 작동 방식, 자동 인덱싱, 데이터베이스 인덱싱
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 277ddd5777ff8edf5195e79885929e3a8c758d7c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Azure Cosmos DB는 데이터를 어떻게 인덱싱하나요?

기본적으로 모든 Azure Cosmos DB 데이터가 인덱싱됩니다. 많은 고객이 Azure Cosmos DB를 사용하여 인덱싱의 모든 측면을 자동으로 처리할 수 있어 만족하지만, Azure Cosmos DB에서는 작성 중인 컬렉션에 대한 사용자 지정 *인덱싱 정책*을 지정할 수 있습니다. Azure Cosmos DB의 인덱싱 정책은 다른 데이터베이스 플랫폼에서 제공되는 보조 인덱스보다 유연하고 강력합니다. Azure Cosmos DB에서는 스키마 유연성을 유지하면서 인덱스의 모양을 디자인하고 사용자 지정할 수 있습니다. 

Azure Cosmos DB 내에서 인덱싱이 작동하는 방식을 알아보려면, 인덱싱 정책을 관리할 때 인덱스 저장소 오버헤드, 쓰기 및 쿼리 처리량, 쿼리 일관성 간의 균형을 세밀하게 맞출 수 있음을 이해해야 합니다.  

다음 비디오에서 Azure Cosmos DB 프로그램 관리자인 Andrew Liu는 Azure Cosmos DB 자동 인덱싱 기능을 및 Azure Cosmos DB 컨테이너에서 인덱싱 정책을 조정하고 구성하는 방법을 보여줍니다. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

이 문서에서는 Azure Cosmos DB 인덱싱 정책, 인덱싱 정책을 사용자 지정할 수 있는 방법 및 연관된 장단점을 자세히 살펴봅니다. 

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

* 인덱싱에서 포함하거나 제외할 속성을 어떻게 재정의하나요?
* 최종 업데이트에 대한 인덱스를 어떻게 구성하나요?
* ORDER BY 또는 범위 쿼리를 수행하는 인덱싱을 어떻게 구성하나요?
* 컬렉션의 인덱싱 정책을 어떻게 변경하나요?
* 저장소 및 다른 인덱싱 정책의 성능을 어떻게 비교하나요?

## 컬렉션의 인덱싱 정책 사용자 지정 <a id="CustomizingIndexingPolicy"></a>  
Azure Cosmos DB 컬렉션의 기본 인덱싱 정책을 재정의하여 저장소, 쓰기/쿼리 성능 및 쿼리 일관성 간의 절충을 사용자 지정할 수 있습니다. 다음 측면을 구성할 수 있습니다.

* **문서 및 경로를 인덱스에 포함 또는 인덱스에서 제외**. 컬렉션의 문서를 삽입하거나 바꿀 때 인덱스에서 특정 문서를 제외하거나 포함할 수 있습니다. 인덱스에 포함된 문서에서 인덱싱할 특정 JSON 속성(‘경로’라고도 함)을 포함하거나 제외할 수도 있습니다. 경로에는 와일드카드 패턴이 포함됩니다.
* **다양한 인덱스 유형 구성**. 포함된 각 경로에 대해 컬렉션의 경로에 필요한 인덱스 유형을 지정할 수 있습니다. 경로 데이터, 예상 쿼리 워크로드 및 숫자/문자열 “전체 자릿수”를 기준으로 인덱스 유형을 지정할 수 있습니다.
* **인덱스 업데이트 모드 구성**. Azure Cosmos DB는 일관성, 지연 및 없음의 세 가지 인덱싱 모드를 지원합니다. Azure Cosmos DB 컬렉션의 인덱싱 정책을 통해 인덱싱 모드를 구성할 수 있습니다. 

다음 Microsoft .NET 코드 조각에서는 컬렉션을 만들 때 사용자 지정 인덱싱 정책을 설정하는 방법을 보여 줍니다. 이 예제에서는 최대 전체 자릿수의 문자열 및 숫자에 대한 범위 인덱스를 사용하여 정책을 설정합니다. 이 정책을 사용하여 문자열에 대한 ORDER BY 쿼리를 실행할 수 있습니다.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> REST API 2015-06-03 버전 릴리스에서 인덱싱 정책에 대한 JSON 스키마가 변경되었습니다. 해당 릴리스의 인덱싱 정책에 대한 JSON 스키마는 문자열에 대한 범위 인덱스를 지원합니다. .NET SDK 1.2.0과 Java, Python 및 Node.js SDK 1.1.0은 새 정책 스키마를 지원합니다. 이전 버전의 SDK는 REST API 2015-04-08 버전을 사용하며, 인덱싱 정책에 대한 이전 스키마를 지원합니다.
> 
> 기본적으로 Azure Cosmos DB는 해시 인덱스를 사용하여 문서 내의 모든 문자열 속성을 일관되게 인덱싱하고, 범위 인덱스를 사용하여 문서 내의 모든 숫자 속성을 일관되게 인덱싱합니다.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Portal에서 인덱싱 정책 사용자 지정

Azure Portal에서 컬렉션의 인덱싱 정책을 변경할 수 있습니다. 

1. Portal에서 Azure Cosmos DB 계정으로 이동한 다음, 컬렉션을 선택합니다. 
2. 왼쪽 탐색 메뉴에서 **설정**, **인덱싱 정책**을 차례로 선택합니다. 
3. **인덱싱 정책**에서 인덱싱 정책을 변경한 다음, **확인**을 선택합니다. 

### 데이터베이스 인덱싱 모드 <a id="indexing-modes"></a>  
Azure Cosmos DB는 Azure Cosmos DB 컬렉션의 인덱싱 정책을 통해 구성할 수 있는 세 가지 인덱싱 모드 즉, 일관성, 지연 및 없음을 지원합니다.

**일관성**: Azure Cosmos DB 컬렉션의 정책이 일관성이면 특정 Azure Cosmos DB 컬렉션의 쿼리는 지점 읽기(강력, 제한된 부실, 세션 또는 최종)에 대해 지정된 것과 동일한 일관성 수준을 따릅니다. 인덱스는 문서 업데이트(Azure Cosmos DB 컬렉션의 문서 삽입, 바꾸기, 업데이트 및 삭제)의 일부로 동기적으로 업데이트됩니다.

일관된 인덱싱은 일관된 쿼리를 지원하지만 쓰기 처리량이 줄 수 있습니다. 이와 같이 감소하는 정도는 인덱싱해야 하는 고유한 경로와 “일관성 수준”에 따라 달라집니다. 일관성 인덱싱 모드는 "신속하게 쿼리를 즉시 쓰기" 워크로드를 위해 설계되었습니다.

**지연**: Azure Cosmos DB 컬렉션이 정지 상태 즉, 사용자 요청을 처리할 만큼 컬렉션의 처리량 용량이 충분히 활용되지 않은 경우 인덱스가 비동기적으로 업데이트됩니다. 지연 인덱싱 모드는 문서 수집을 필요로 하는 “지금 수집, 나중에 쿼리” 작업에 적합할 수 있습니다. 데이터가 천천히 수집되고 인덱싱되기 때문에 결과가 일관되지 않을 수 있습니다. 즉, COUNT 쿼리 또는 특정 쿼리 결과가 지정된 시간에 일관되지 않거나 반복될 수 있습니다. 

인덱스는 일반적으로 수집된 데이터를 사용하는 캐치업 모드입니다. 지연 인덱싱에서는 TTL(Time to Live)을 변경하면 인덱스가 삭제되고 다시 생성됩니다. 이로 인해 COUNT 및 쿼리 결과가 일정 기간 동안 일관되지 않습니다. 따라서 대부분의 Azure Cosmos DB 계정은 일관성 인덱싱 모드를 사용해야 합니다.

**없음**: 없음 인덱싱 모드를 사용하는 컬렉션에는 연관된 인덱스가 없습니다. 이 모드는 Azure Cosmos DB를 키-값 저장소로 이용하고 해당 ID 속성에 의해서만 문서에 액세스하는 경우에 흔히 사용됩니다. 

> [!NOTE]
> [없음]으로 인덱싱 정책을 구성하면 모든 기존 인덱스가 삭제되는 부작용이 있습니다. 액세스 패턴에 ID 또는 자체 링크만 필요한 경우 이 모드를 사용합니다.
> 
> 

다음 표에서 컬렉션에 대해 구성된 인덱싱 모드(일관성 및 지연) 및 쿼리 요청에 대해 지정된 일관성 수준에 따라 쿼리에 대한 일관성을 보여줍니다. REST API, SDK 등 인터페이스를 사용하여 만들거나 저장 프로시저 및 트리거 내에서 만든 쿼리에 적용됩니다. 

|일관성|인덱싱 모드: 일관성|인덱싱 모드: 지연|
|---|---|---|
|강력|강력|최종|
|제한된 부실|제한된 부실|최종|
|세션|세션|최종|
|최종|최종|최종|

Azure Cosmos DB는 없음 인덱싱 모드를 사용하는 컬렉션에 대해 실행된 쿼리에 대해 오류를 반환합니다. 그래도 REST API의 **x-ms-documentdb-enable-scan** 헤더 또는 .NET SDK의 **EnableScanInQuery** 요청 옵션을 통해 쿼리를 검색으로 실행할 수는 있습니다. ORDER BY와 같은 일부 쿼리 기능은 **EnableScanInQuery**를 사용한 검색으로 지원되지 않습니다.

다음 표에서는 **EnableScanInQuery**를 지정했을 때 인덱싱 모드(일관성, 지연 및 없음)에 따른 쿼리의 일관성을 보여 줍니다.

|일관성|인덱싱 모드: 일관성|인덱싱 모드: 지연|인덱싱 모드: 없음|
|---|---|---|---|
|강력|강력|최종|강력|
|제한된 부실|제한된 부실|최종|제한된 부실|
|세션|세션|최종|세션|
|최종|최종|최종|최종|

다음 코드 샘플은 모든 문서 삽입에 일관성 인덱싱을 사용하여 .NET SDK로 Azure Cosmos DB 컬렉션을 만드는 방법을 보여 줍니다.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>인덱스 경로
Azure Cosmos DB는 JSON 문서 및 인덱스를 트리로 모델링합니다. 트리 내 경로에 대한 정책으로 조정할 수 있습니다. 문서 내에서 경로를 선택하여 인덱싱에 포함하거나 제외할 수 있습니다. 따라서 쿼리 패턴을 사전에 알고 있는 시나리오의 경우 쓰기 성능이 향상되고 인덱스 저장소를 줄일 수 있습니다.

인덱스 경로는 루트(/)로 시작하고 일반적으로 ? 와일드카드 연산자로 끝나, 접두사로 사용 가능한 값이 여러 개 있음을 나타냅니다. 예를 들어 SELECT * FROM Families F WHERE F.familyName = "Andersen"을 처리하려면 컬렉션의 인덱스 정책에 /familyName/?의 인덱스 경로를 포함해야 합니다.

또한 인덱스 경로에 \* 와일드카드 연산자를 사용하여 접두사 아래에 재귀적으로 경로에 대한 동작을 지정할 수 있습니다. 예를 들어 /payload/*를 사용하여 페이로드 속성의 모든 항목을 인덱싱에서 제외할 수 있습니다.

인덱스 경로를 지정하는 일반적인 패턴은 다음과 같습니다.

| path                | 설명/사용 사례                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | 컬렉션의 기본 경로입니다. 재귀적이며 전체 문서 트리에 적용됩니다.                                                                                                                                                                                                                                   |
| /prop/?             | 인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시, 범위 유형이 포함되어 있음).<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/*             | 지정된 레이블 아래의 모든 경로의 인덱스 경로입니다. 다음 쿼리로 작동<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | ["a", "b", "c"]와 같은 스칼라의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다.<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | [{subprop: "a"}, {subprop: "b"}]와 같은 개체의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다.<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | 인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시 또는 범위 유형이 포함되어 있음).<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> 사용자 지정 인덱스 경로를 설정하는 경우 특수 경로 “/*”로 지정된 전체 문서 트리에 대한 기본 인덱싱 규칙을 지정해야 합니다. 
> 
> 

다음 예제에서는 범위 인덱스를 사용하고 사용자 지정 전체 자릿수 값이 20바이트인 특정 경로를 구성합니다.

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>인덱스 데이터 형식, 종류 및 전체 자릿수
경로에 대한 인덱싱 정책을 구성하는 여러 옵션이 있습니다. 모든 경로에 대해 하나 이상의 인덱싱 정의를 지정할 수 있습니다.

* **데이터 형식**: 문자열, 숫자, 점, 다각형 또는 LineString(경로별로 데이터 형식당 하나만 포함할 수 있음)
* **인덱스 종류**: 해시(같음 쿼리), 범위(같음, 범위 또는 Order By 쿼리) 또는 공간(공간 쿼리)
* **전체 자릿수**: 해시 인덱스의 경우 문자열 및 숫자 둘 다에서 1~8 범위로 다양하며, 기본값은 3입니다. 범위 인덱스의 경우 이 값은 -1(최대 전체 자릿수)일 수 있습니다. 문자열 또는 숫자 값의 경우 1에서 100(최대 전체 자릿수)까지 다양할 수 있습니다.

#### <a name="index-kind"></a>인덱스 종류
Azure Cosmos DB는 모든 경로에 대해 문자열, 숫자 데이터 형식 또는 둘 다로 구성할 수 있는 해시 및 범위 인덱스 종류를 지원합니다.

* **해시** 는 효율적인 같음 및 JOIN 쿼리를 지원합니다. 대부분의 사용 사례에서는 해시 인덱스의 전체 자릿수가 기본값 3바이트보다 높을 필요가 없습니다. 데이터 형식은 문자열 또는 숫자일 수 있습니다.
* **범위**는 효율적인 같음 쿼리, 범위 쿼리(>, <, >=, <=, != 사용) 및 ORDER BY 쿼리를 지원합니다. 또한 ORDER BY 쿼리에는 기본적으로 최대 인덱스 전체 자릿수(-1)가 필요합니다. 데이터 형식은 문자열 또는 숫자일 수 있습니다.

Azure Cosmos DB는 모든 경로에 대해 점, 다각형 또는 LineString 데이터 형식으로 지정할 수 있는 공간 인덱스 종류도 지원합니다. 지정된 경로에 있는 값은 `{"type": "Point", "coordinates": [0.0, 10.0]}`과 같은 유효한 GeoJSON 조각이어야 합니다.

* **공간** 은 효율적인 공간(이내 및 거리) 쿼리를 지원합니다. 데이터 형식은 점, 다각형 또는 LineString일 수 있습니다.

> [!NOTE]
> Azure Cosmos DB는 점, 다각형 및 LineString 데이터 형식의 자동 인덱싱을 지원합니다.
> 
> 

다음은 지원되는 인덱스 종류와 제공될 수 있는 쿼리 예입니다.

| 인덱스 종류 | 설명/사용 사례                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 해시       | Hash over/prop/? (또는 /)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (또는 /나 /props/)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| 범위      | /prop/? (또는 /)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| 공간     | /prop/? (또는 /)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --with indexing on points enabled<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --with indexing on polygons enabled              |

기본적으로, 쿼리를 처리하는 데 검색이 필요할 수 있음을 알리기 위한 범위 인덱스(임의의 전체 자릿수)가 없는 경우 >=와 같은 범위 연산자를 사용하는 쿼리에 대해 오류가 반환됩니다. REST API의 **x-ms-documentdb-enable-scan** 헤더를 사용하거나 .NET SDK의 **EnableScanInQuery** 요청 옵션을 사용하여 범위 인덱스 없이 범위 쿼리를 수행할 수 있습니다. 쿼리에 다른 필터가 있어서 Azure Cosmos DB가 인덱스를 사용하여 필터링할 수 있는 경우 오류가 반환되지 않습니다.

공간 쿼리에 대해서도 동일한 규칙이 적용됩니다. 기본적으로 공간 인덱스가 없고 인덱스에서 제공될 수 있는 다른 필터가 없는 경우 공간 쿼리에 오류가 반환됩니다. **x-ms-documentdb-enable-scan** 또는 **EnableScanInQuery**를 사용하여 검색으로 쿼리를 수행할 수 있습니다.

#### <a name="index-precision"></a>인덱스 전체 자릿수
인덱스 전체 자릿수를 사용하면 인덱스 저장소 오버헤드와 쿼리 성능 간에 적절한 균형을 유지할 수 있습니다. 숫자의 경우 기본 전체 자릿수 구성인 -1(최대값)을 사용하는 것이 좋습니다. 숫자는 JSON에서 8바이트이므로 8바이트 구성과 같습니다. 1-7과 같이 낮은 전체 자릿수 값을 선택하면 일부 범위 내의 값이 같은 인덱스 항목에 매핑됩니다. 따라서 인덱스 저장소 공간을 줄일 수 있지만 쿼리 실행 시 더 많은 문서를 처리해야 할 수 있습니다. 따라서 요청 단위의 처리량 사용이 더 많습니다.

인덱스 전체 자릿수 구성은 문자열 범위와 함께 사용할 때 실질적으로 더 유용합니다. 문자열은 임의의 길이일 수 있으므로 인덱스 전체 자릿수 선택은 문자열 범위 쿼리의 성능에 영향을 줄 수 있으며 필요한 인덱스 저장소 공간의 양에도 영향을 줄 수 있습니다. 문자열 범위 인덱스는 1-100 또는 -1(최대값)로 구성할 수 있습니다. 문자열 속성에 대한 ORDER BY 쿼리를 수행하려는 경우, 해당 경로에 대해 -1의 전체 자릿수를 지정해야 합니다.

공간 인덱스는 항상 모든 형식(점, LineString 및 다각형)에 대한 기본 인덱스 전체 자릿수를 사용하며, 공간 인덱스의 기본 인덱스 전체 자릿수는 재정의할 수 없습니다. 

다음 예제에서는 .NET SDK를 사용하여 컬렉션의 범위 인덱스에 대한 전체 자릿수를 늘리는 방법을 보여 줍니다. 

**사용자 지정 인덱스 전체 자릿수로 컬렉션 만들기**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB는 쿼리가 ORDER BY를 사용하지만 최대 전체 자릿수로 쿼리된 경로에 대한 범위 인덱스가 없는 경우 오류를 반환합니다. 
> 
> 

마찬가지로 인덱싱에서 경로를 완전히 제외할 수 있습니다. 다음 예제는 \* 와일드카드 연산자를 사용하여 인덱싱에서 문서의 전체 섹션(‘하위 트리’라고도 함)을 제외하는 방법을 보여 줍니다.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>인덱싱 옵트인 및 옵트아웃
컬렉션에서 모든 문서를 자동으로 인덱싱하도록 할지 여부를 선택할 수 있습니다. 기본적으로 모든 문서는 자동으로 인덱싱되지만 자동 인덱싱을 해제할 수도 있습니다. 인덱싱을 해제하면 자체 링크를 통해서나 문서 ID를 사용한 쿼리로만 문서에 액세스할 수 있습니다.

자동 인덱싱이 해제된 상태에서도 특정 문서만 선택적으로 인덱스에 추가할 수 있습니다. 반대로, 자동 인덱싱을 설정된 상태로 두고 특정 문서를 선택적으로 제외할 수도 있습니다. 인덱싱 설정/해제 구성은 문서의 하위 집합만 쿼리해야 할 경우에 유용합니다.

다음 샘플은 [SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) 및 [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) 속성을 사용하여 문서를 명시적으로 포함하는 방법을 보여 줍니다.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>컬렉션의 인덱싱 정책 수정
Azure Cosmos DB에서는 즉석에서 컬렉션의 인덱싱 정책을 변경할 수 있습니다. Azure Cosmos DB 컬렉션에 대한 인덱싱 정책을 변경하면 인덱스 모양이 변경될 수 있습니다. 이 변경은 인덱싱할 수 있는 경로, 해당 전체 자릿수 및 인덱스 자체의 일관성 모델에 영향을 줍니다. 따라서 인덱싱 정책을 변경하려면 실제로 이전 인덱스를 새 인덱스로 변환해야 합니다. 

**온라인 인덱스 변환**

![인덱싱 작동 방식 – Azure Cosmos DB 온라인 인덱스 변환](./media/indexing-policies/index-transformations.png)

인덱스 변환은 온라인으로 이루어집니다. 즉, 이전 정책을 기준으로 인덱싱된 문서가 컬렉션의 ‘쓰기 가용성 또는 프로비전된 처리량에 영향을 주지 않고’ 새 정책을 기준으로 효율적으로 변환됩니다. REST API 또는 SDK를 사용하거나 저장 프로시저 및 트리거 내에서 유지된 읽기 및 쓰기 작업의 일관성은 인덱스 변환 중 영향을 받지 않습니다. 인덱싱 정책을 변경해도 앱 성능이 저하되지 않으며 앱의 가동 중지 시간이 발생하지 않습니다.

그러나 인덱스 변환이 진행되는 동안 쿼리는 결국 인덱싱 모드 구성(일관성 또는 지연)에 관계 없이 일관됩니다. 이는 REST API, SDK 등의 모든 인터페이스와 저장 프로시저 및 트리거 내의 쿼리에도 적용됩니다. 지연 인덱싱과 마찬가지로, 인덱스 변환은 특정 복제본에 대해 사용 가능한 예비 리소스를 사용하여 백그라운드에서 복제본에 대해 비동기적으로 수행됩니다. 

인덱스 변환도 수행됩니다. Azure Cosmos DB는 인덱스의 두 복사본을 유지하지 않으며 이전 인덱스를 새 인덱스로 교체합니다. 따라서 인덱스 변환을 수행하는 동안 컬렉션에서 디스크 공간이 추가로 필요하거나 사용되지 않습니다.

인덱싱 정책을 변경하면 기본적으로 인덱싱 모드 구성에 따라 변경 내용이 적용되어 이전 인덱스에서 새 인덱스로 이동됩니다. 인덱싱 모드 구성은 포함되거나 제외된 경로, 인덱스 종류 및 전체 자릿수와 같은 다른 값보다 큰 역할을 합니다. 

이전 정책과 새 정책 모두 일관성 인덱싱을 사용하는 경우 Azure Cosmos DB는 온라인 인덱스 변환을 수행합니다. 변환이 진행 중인 동안 일관성 인덱싱 모드인 다른 인덱싱 정책 변경을 적용할 수 없습니다. 그러나 변환이 진행 중인 동안 지연 또는 없음 인덱싱 모드로 이동할 수 있습니다. 

* 지연으로 이동하면 인덱스 정책 변경이 즉시 적용됩니다. Azure Cosmos DB는 인덱스를 비동기적으로 다시 만들기 시작합니다. 
* 없음으로 이동하면 인덱스가 즉시 삭제됩니다. 진행 중인 변환을 취소하고 다른 인덱싱 정책으로 새로 시작하려는 경우 없음으로 이동하는 것이 유용합니다. 

다음 코드 조각은 일관성 컬렉션의 인덱싱 정책을 일관성 인덱싱 모드에서 지연 인덱싱 모드로 수정하는 방법을 보여 줍니다. .NET SDK를 사용하는 경우 새 **ReplaceDocumentCollectionAsync** 메서드를 사용하여 인덱싱 정책 변경을 시작할 수 있습니다.

**일관성에서 지연으로 인덱싱 정책 수정**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**인덱스 변환의 진행률 추적**

**ReadDocumentCollectionAsync** 호출에서 **IndexTransformationProgress** 응답 속성을 사용하여 일관성 인덱스로 인덱스 변환의 진행률을 추적할 수 있습니다. 다른 SDK 및 REST API는 해당하는 속성과 메서드를 통해 인덱싱 정책 변경을 지원합니다. **ReadDocumentCollectionAsync**를 호출하여 일관성 인덱스로 인덱스 변환의 진행률을 확인할 수 있습니다. 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * **IndexTransformationProgress** 속성은 일관성 인덱스로 변환할 때만 적용됩니다. 지연 인덱스로 변환을 추적하려면 **ResourceResponse.LazyIndexingProgress** 속성을 사용합니다.
> * **IndexTransformationProgress** 및 **LazyIndexingProgress** 속성은 분할되지 않은 컬렉션 즉, 파티션 키 없이 만든 컬렉션에 대해서만 채워집니다.
>

없음 인덱싱 모드로 이동하여 컬렉션에 대한 인덱스를 삭제할 수 있습니다. 진행 중인 변환을 취소하고 새 변환을 즉시 시작하려는 경우 유용한 작업 도구일 수 있습니다.

**컬렉션에 대한 인덱스 삭제**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

언제 Azure Cosmos DB 컬렉션에 대한 인덱싱 정책을 변경하나요? 다음은 가장 일반적인 사용 사례입니다.

* 정상 작동 중에는 일관된 결과를 제공하지만, 대량 데이터를 가져오는 동안 지연 인덱싱 모드로 대체하는 경우
* 현재 Azure Cosmos DB 컬렉션에 새 인덱싱 기능을 사용하기 시작합니다. 예를 들어 지리 공간적 쿼리를 사용하려면 공간 인덱스 종류가 필요하고 ORDER BY/문자열 범위 쿼리를 사용하려면 문자열 범위 인덱스 종류가 필요할 수 있습니다.
* 인덱싱할 속성을 엄선하고 시간이 지남에 따라 변경하는 경우
* 인덱싱 전체 자릿수를 조정하여 쿼리 성능을 향상하거나 소비되는 저장소 공간을 줄이려는 경우

> [!NOTE]
> **ReplaceDocumentCollectionAsync**를 사용하여 인덱싱 정책을 수정하려면 1.3.0 이상 버전의 .NET SDK를 사용해야 합니다.
> 
> 인덱스 변환을 성공적으로 완료하려면 컬렉션에 사용할 수 있는 저장소 공간이 충분해야 합니다. 컬렉션이 해당 저장소 할당량에 도달하면 인덱스 변환이 일시 중지됩니다. 일부 문서를 삭제하는 등을 통해 저장소 공간을 사용할 수 있게 되면 인덱스 변환이 자동으로 다시 시작됩니다.
> 
> 

## <a name="performance-tuning"></a>성능 튜닝
SQL API는 사용된 인덱스 저장소와 같은 성능 메트릭에 대한 정보와 모든 작업에 대한 처리량 비용(요청 단위)을 제공합니다. 이 정보를 활용하여 다양한 인덱싱 정책을 비교하거나 성능을 조정할 수 있습니다.

컬렉션의 저장소 할당량 및 사용량을 확인하려면 컬렉션 리소스에 대해 **HEAD** 또는 **GET** 요청을 실행하고 **x-ms-request-quota** 및 **x-ms-request-usage** 헤더를 검사합니다. .NET SDK에서는 [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx)의 [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) 및 [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) 속성은 이에 해당하는 값을 포함합니다.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


각 쓰기 작업(만들기, 업데이트 또는 삭제)에 대한 인덱싱 오버헤드를 측정하려면 **x-ms-request-charge** 헤더(또는 .NET SDK의 [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx)에 있는 동등한 [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) 속성)를 검사하여 이 작업에 사용된 요청 단위 수를 측정합니다.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>인덱싱 정책 사양의 변경 내용
2015년 7월 7일 REST API 버전 2015-06-03에서 인덱싱 정책의 스키마가 변경되었습니다. SDK 버전에서 해당 클래스는 스키마와 일치하도록 새로 구현됩니다. 

다음과 같은 변경 내용은 JSON 사양에서 구현됩니다.

* 인덱싱 정책에서 문자열에 대한 범위 인덱스 지원
* 각 경로는 각 데이터 형식에 하나씩 여러 인덱스 정의를 가질 수 있음
* 인덱싱 전체 자릿수는 숫자의 경우 1-8, 문자열의 경우 1-100을 지원하며 -1의 최대 전체 자릿수 지원
* 경로 세그먼트에서 각 경로를 이스케이프하기 위해 큰따옴표를 사용할 필요가 없음 예를 들어 **/"title"/?** 대신 **/title/?**에 대한 경로를 추가할 수 있습니다.
* “모든 경로”를 나타내는 루트 경로는 **/\*** (및 **/**)로 표시

버전 1.1.0 이하의 .NET SDK로 작성된 사용자 지정 인덱싱 정책을 사용하여 컬렉션을 프로비전하는 코드가 있는 경우 SDK 버전 1.2.0으로 이동하려면 응용 프로그램 코드를 변경하여 이러한 변경 내용을 처리해야 합니다. 인덱싱 정책을 구성하는 코드가 없거나 이전 SDK 버전을 계속 사용하려는 경우에는 변경하지 않아도 됩니다.

실제로 비교할 수 있도록 다음 예제에서는 REST API 버전 2015-06-03을 사용하여 사용자 지정 인덱싱 정책을 작성한 후, 동일한 인덱싱 정책을 이전 REST API 버전 2015-04-08을 사용하여 작성합니다.

**현재 인덱싱 정책 JSON(REST API 버전 2015-06-03)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**이전 인덱싱 정책 JSON(REST API 버전 2015-04-08)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>다음 단계
인덱스 정책 관리 샘플 및 Azure Cosmos DB 쿼리 언어에 대한 자세한 내용을 보려면 아래 링크를 참조하세요.

* [SQL API .NET index management code samples](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)(SQL API .NET 인덱스 관리 코드 샘플)
* [SQL API REST collection operations](https://msdn.microsoft.com/library/azure/dn782195.aspx)(SQL API REST 컬렉션 작업)
* [SQL을 사용한 쿼리](sql-api-sql-query.md)

