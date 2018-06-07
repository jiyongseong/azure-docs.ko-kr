---
title: TTL(Time To Live)을 사용하여 Azure Cosmos DB의 데이터 만료 | Microsoft Docs
description: TTL을 사용하여 Microsoft Azure Cosmos DB는 일정 기간 후에 시스템에서 문서를 자동으로 삭제하는 기능을 제공합니다.
services: cosmos-db
documentationcenter: ''
keywords: TTL(Time to live)
author: SnehaGunda
manager: kfile
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 13f2caa631817a5745f39b44faccb11252a2d549
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB 컬렉션의 데이터 만료
응용 프로그램은 방대한 양의 데이터을 생성하고 저장할 수 있습니다. 컴퓨터에서 생성한 이벤트 데이터, 로그 및 사용자 세션 정보와 같은 이 데이터 중 일부는 한정된 기간에만 사용할 수 있습니다. 데이터가 응용 프로그램의 요구를 넘게 되면 이 데이터를 삭제하고 응용 프로그램의 저장소 요구를 줄이는 것이 안전합니다.

"time to live" 또는 TTL을 사용하여 Microsoft Azure Cosmos DB는 일정 기간 후에 데이터베이스에서 문서를 자동으로 삭제하는 기능을 제공합니다. 기본 TTL(Time to live)을 컬렉션 수준에서 설정할 수 있고 문서별로 재정의할 수 있습니다. TTL을 컬렉션 기본값으로 설정하거나 문서 수준에서 설정하면 Cosmos DB는 마지막으로 수정된 이후 해당 기간(초) 후에 존재하는 문서를 자동으로 제거합니다.

Cosmos DB의 TTL(Time To Live)은 문서가 마지막으로 수정된 시간을 기준으로 오프셋을 사용합니다. 이렇게 하려면 모든 문서에 있는 `_ts` 필드를 사용합니다. _ts 필드는 날짜 및 시간을 나타내는 Unix 스타일 Epoch 타임스탬프입니다. `_ts` 필드는 문서가 수정될 때마다 업데이트됩니다. 

## <a name="ttl-behavior"></a>TTL 동작
TTL 기능은 컬렉션 수준 및 문서 수준 등 두 가지 수준으로 TTL 속성에 의해 제어됩니다. 값은 초 단위로 설정되고 문서가 마지막으로 수정되는 `_ts` 필드에서 델타로 처리됩니다.

1. 컬렉션에 대한 DefaultTTL
   
   * 누락(또는 null로 설정)된 경우 문서는 자동으로 삭제되지 않습니다.
   * 표시되고 현재 값이 "-1" = 무한인 경우 문서는 기본적으로 만료되지 않습니다.
   * 표시되고 현재 값이 숫자("n")인 경우 문서는 마지막으로 수정되고 "n"초 후에 만료됩니다.
2. 문서에 대한 TTL: 
   
   * 속성은 DefaultTTL이 상위 컬렉션에 있는 경우에 적용할 수 있습니다.
   * 상위 컬렉션에 대한 DefaultTTL 값을 재정의합니다.

문서가 만료되는 즉시(`ttl` + `_ts` <= 현재 서버 시간) 문서는 “만료”된 것으로 표시됩니다. 이 시간 이후에 어떤 작업도 이러한 문서에 허용되지 않으며 수행되는 쿼리 결과에서 제외됩니다. 문서는 시스템에서 물리적으로 삭제되고 나중에 선택적으로 백그라운드에서 삭제됩니다. 이는 컬렉션 예산에서 [RU(요청 단위)](request-units.md) 를 사용하지 않습니다.

위의 논리는 다음 행렬에 표시될 수 있습니다.

|  | 컬렉션에서 DefaultTTL 누락/설정되지 않음 | 컬렉션에서 DefaultTTL = -1 | 컬렉션에서 DefaultTTL = "n" |
| --- |:--- |:--- |:--- |
| 문서에서 TTL 누락 |문서와 컬렉션에 TTL의 개념이 없으므로 문서 수준에서 아무 것도 재정의할 수 없습니다. |이 컬렉션에서 만료되는 문서가 없습니다. |간격 n이 경과하면 이 컬렉션에서 문서가 만료됩니다. |
| 문서에서 TTL = -1 |문서가 재정의할 수 있는 DefaultTTL 속성을 수집이 정의하지 않으므로 문서 수준에서 아무 것도 재정의되지 않습니다. 문서에서 TTL은 시스템에 의해 해석되지 않습니다. |이 컬렉션에서 만료되는 문서가 없습니다. |이 컬렉션에서 TTL = -1인 문서는 만료되지 않습니다. 다른 모든 문서는 "n" 간격 후에 만료됩니다. |
| 문서에서 TTL = n |문서 수준에서 아무 것도 재정의하지 않습니다. 문서에서 TTL은 시스템에 의해 해석되지 않습니다. |TTL = n인 문서는 간격 n(초) 후에 만료됩니다. 다른 문서는 간격 -1을 상속하며 만료되지 않습니다. |TTL = n인 문서는 간격 n(초) 후에 만료됩니다. 다른 문서는 컬렉션에서 "n" 간격을 상속합니다. |

## <a name="configuring-ttl"></a>TTL 구성
기본적으로 TTL(Time To Live)은 모든 Cosmos DB 컬렉션 및 문서에서 사용되지 않습니다. TTL은 프로그래밍 방식으로 또는 Azure Portal의 해당 컬렉션에 대한 **설정**에서 설정할 수 있습니다. 

## <a name="enabling-ttl"></a>TTL 사용
컬렉션 또는 컬렉션 내 문서에서 TTL을 사용하려면 컬렉션의 DefaultTTL 속성을 -1 또는 0이 아닌 양수로 설정해야 합니다. DefaultTTL을 -1로 설정하면 기본적으로 컬렉션에 있는 모든 문서가 계속 존재하지만 Cosmos DB 서비스는 이 기본값을 재정의한 문서에 대해 이 컬렉션을 모니터링해야 합니다.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>컬렉션에서 기본 TTL 구성
기본 수명을 컬렉션 수준에서 구성할 수 있습니다. 컬렉션에서 TTL을 설정하려면 문서(`_ts`)의 타임스탬프를 마지막으로 수정한 이후 컬렉션에 있는 모든 문서를 만료할 기간(초)을 나타내는 0이 아닌 양수 값을 제공해야 합니다. 또는 기본값을 -1로 설정할 수 있습니다. 즉, 컬렉션에 삽입된 모든 문서가 기본적으로 영원히 지속됩니다.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>문서에서 TTL 설정
컬렉션에서 기본 TTL을 설정하는 것 외에도 특정 문서 수준에서 TTL을 설정할 수 있습니다. 이렇게 하면 컬렉션의 기본값을 재정의합니다.

* 문서에서 TTL을 설정하려면 문서(`_ts`)의 타임스탬프를 마지막으로 수정한 이후 문서를 만료할 기간(초)을 나타내는 0이 아닌 양수 값을 제공해야 합니다.
* 문서에 TTL 필드가 없는 경우 컬렉션의 기본값이 적용됩니다.
* 컬렉션 수준에서 TTL을 사용하지 않으면 문서에서 TTL 필드는 컬렉션에 다시 TTL을 사용할 때까지 무시됩니다.

문서에서 TTL 만료를 설정하는 방법을 보여 주는 코드 조각은 다음과 같습니다.

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>기존 문서에서 TTL 확장
문서에 쓰기 작업을 수행하여 TTL을 재설정할 수 있습니다. 이렇게 하여 `_ts`를 현재 시간으로 설정하면 문서에 대한 만료 카운트다운이 `ttl`에서 설정한 대로 다시 시작됩니다. 문서의 `ttl`을 변경하려는 경우 설정할 수 있는 다른 필드에서처럼 필드를 업데이트할 수 있습니다.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="removing-ttl-from-a-document"></a>문서에서 TTL 제거
문서에서 TTL을 설정했지만 해당 문서가 더 이상 만료되지 않게 하려면 문서를 검색하고 TTL 필드를 제거하여 서버에 있는 문서를 바꿀 수 있습니다. 문서에서 TTL 필드를 제거하면 컬렉션의 기본값이 적용됩니다. 문서가 만료되지 않도록 중지하고 컬렉션에서 상속하지 않으려면 TTL 값을 -1로 설정해야 합니다.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="disabling-ttl"></a>TTL 사용 안 함
컬렉션에서 TTL을 사용하지 않고 만료된 문서를 찾는 백그라운드 프로세스를 중지하려면 컬렉션에서 DefaultTTL 속성을 삭제해야 합니다. 이 속성을 삭제하는 것은 -1로 설정하는 것과 다릅니다. -1로 설정하면 컬렉션에 추가된 새 문서는 계속 존재하게 되지만 컬렉션에 있는 특정 문서에서 이를 재정의할 수 있습니다. 컬렉션에서 이 속성을 완전히 제거하면 이전의 기본값을 명시적으로 재정의한 문서가 있더라도 문서가 만료되지 않습니다.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>TTL 및 인덱스 상호 작용
컬렉션에서 TTL 설정을 추가하거나 변경하면 기본 인덱스를 변경합니다. TTL 값이 Off에서 On으로 변경되면 컬렉션 인덱스가 다시 작성됩니다. 인덱싱 모드가 일관될 때 인덱싱 정책을 변경하는 경우 사용자는 인덱스에 대한 변경을 알지 못합니다. 인덱싱 모드가 지연으로 설정된 경우 인덱스는 항상 따라 잡고 TTL 값이 변경되면 인덱스는 처음부터 다시 만들어집니다. TTL 값이 변경되고 인덱스 모드가 지연으로 설정된 경우 인덱스 다시 작성 중에 수행된 쿼리는 전체 또는 올바른 결과를 반환하지 않습니다.

반환된 정확한 데이터가 필요한 경우 인덱싱 모드가 지연으로 설정된 경우 TTL 값을 변경하지 마십시오. 이상적으로 일관된 쿼리 결과를 보장하도록 일관된 인덱스를 선택해야 합니다. 

## <a name="faq"></a>FAQ
**TTL의 비용은 얼마인가요?**

문서에 TTL을 설정하는 추가 비용은 없습니다.

**TTL이 실행되면 문서를 삭제하는 데 얼마나 걸리나요?**

문서는 TTL이 실행되면 즉시 만료되고 CRUD 또는 쿼리 API를 통해 액세스할 수 없습니다. 

**문서에서 TTL은 RU 요금에 영향을 주나요?**

아니요, Cosmos DB에서 TTL을 통해 만료된 문서를 삭제할 경우 RU 요금에 영향을 주지 않습니다.

**TTL 기능이 전체 문서에 적용되나요, 또는 개별 문서 속성 값을 만료할 수 있나요?**

TTL은 문서 전체에 적용됩니다. 문서의 일부만 만료하고 싶다면 "링크된" 별도 문서의 기본 문서에서 부분을 추출한 다음 추출된 문서에서 TTL을 사용하는 것이 좋습니다.

**TTL 기능에는 특정 인덱싱 요구 사항이 있나요?**

예. 컬렉션에는 일관성 또는 지연 중 하나에 대한 [인덱싱 정책 집합](indexing-policies.md) 이 있어야 합니다. 인덱싱 설정을 사용하여 컬렉션에서 DefaultTTL을 없음으로 설정하면 DefaultTTL이 이미 설정되어 있는 컬렉션에서 인덱싱을 해제하는 것과 같은 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB에 대한 자세한 내용은 서비스 [*설명서*](https://azure.microsoft.com/documentation/services/cosmos-db/) 페이지를 참조하세요.

