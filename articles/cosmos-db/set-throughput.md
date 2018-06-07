---
title: Azure Cosmos DB에 대한 처리량 프로비전 | Microsoft Docs
description: Azure Cosmos DB 컨테이너, 컬렉션, 그래프 및 테이블에 대해 프로비전된 처리량을 설정하는 방법을 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sngun
ms.openlocfilehash: 925167c6b4a7f173726ec094c2847a16ca3d0ef4
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Azure Cosmos DB 컨테이너 및 데이터베이스에 대한 처리량 설정 및 가져오기

Azure Portal을 사용하거나 클라이언트 SDK를 사용하여 Azure Cosmos DB 컨테이너 또는 컨테이너 집합에 대한 처리량을 설정할 수 있습니다. 컨테이너 집합에 대한 처리량을 프로비전할 때 모든 컨테이너가 프로비전된 처리량을 공유합니다. 개별 컨테이너에 대해 처리량을 프로비전하면 해당 특정 컨테이너의 처리량이 예약 보증됩니다. 반면에 데이터베이스에 대한 처리량을 프로비전하면 해당 데이터베이스에 속한 모든 컨테이너에서 처리량을 공유 할 수 있습니다. Azure Cosmos DB 데이터베이스에서 전용 처리량을 가진 컨테이너는 물론 처리량을 공유하는 컨테이너 집합을 가질 수 있습니다. 

프로비전된 처리량에 따라 Azure Cosmos DB는 컨테이너를 호스트하는 실제 파티션을 할당하고 확장됨에 따라 파티션에서 데이터를 분할/균형 조정합니다.

개별 컨테이너 수준에서 RU/초를 할당할 때 컨테이너를 *고정* 또는 *무제한*으로 만들 수 있습니다. 고정 크기 컨테이너는 최대 제한 10GB 및 10,000RU/s 처리량을 설정할 수 있습니다. 무제한 컨테이너를 만들려면 최소 1,000RU/s 처리량과 [파티션 키](partition-data.md)를 지정해야 합니다. 데이터는 여러 파티션에 분할되어야 하므로 카디널리티가 높은(백~수백만 개의 고유 값) 파티션 키를 선택해야 합니다. 고유 값이 많은 파티션 키를 선택하면 컨테이너/테이블/그래프 및 요청이 Azure Cosmos DB에서 균일하게 확장될 수 있습니다. 

컨테이너 집합에서 RU/초를 할당할 때 이 집합에 속하는 컨테이너는 *무제한* 컨테이너로 처리되어야 하며 파티션 키를 지정해야 합니다.

![개별 컨테이너 및 컨테이너의 집합에 대한 요청 단위 프로비저닝](./media/request-units/provisioning_set_containers.png)

이 문서에서는 Azure Cosmos DB 계정에 대한 여러 단계의 처리량을 구성하는 데 필요한 단계를 안내합니다. 

## <a name="provision-throughput-by-using-azure-portal"></a>Azure Portal을 사용하여 처리량 프로비전

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>컨테이너(컬렉션/그래프/테이블)에 대한 처리량 프로비전

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
2. 왼쪽 탐색 창에서 **모든 리소스**를 선택하고 Azure Cosmos DB 계정을 찾습니다.  
3. 컨테이너(컬렉션, 그래프, 테이블)을 만드는 동안 처리량을 구성하거나 기존 컨테이너의 처리량을 업데이트할 수 있습니다.  
4. 컨테이너를 만드는 동안 처리량을 지정하려면 **Data Explorer** 블레이드를 열고 **New Collection**(다른 API에 대한 New Graph, New Table)을 선택합니다.  
5. **Add Collection** 블레이드에서 폼을 입력합니다. 이 블레이드의 필드는 다음 표에 설명되어 있습니다.  

   |**설정**  |**설명**  |
   |---------|---------|
   |데이터베이스 ID  |  데이터베이스를 식별하는 고유한 이름을 제공합니다. 데이터베이스는 하나 이상 콜렉션의 논리 컨테이너입니다. 데이터베이스 이름은 1-255자여야 하며, /, \\, #,? 또는 후행 공백은 포함할 수 없습니다. |
   |컬렉션 ID  | 컬렉션을 식별하는 고유한 이름을 제공합니다. 컬렉션 ID에는 데이터베이스 이름과 동일한 문자 요구 사항이 적용됩니다. |
   |Storage 용량   | 이 값은 데이터베이스의 저장소 용량을 나타냅니다. 개별 컬렉션의 처리량을 프로비전할 때 저장 용량은 **Fixed(10 GB)** 또는 **Unlimited**일 수 있습니다. 무제한 저장 용량을 사용하려면 데이터에 대한 파티션 키를 설정해야 합니다.  |
   |처리량   | 각 콜렉션 및 데이터베이스는 초당 요청 단위로 처리량을 가질 수 있습니다.  고정 저장 용량의 경우 최소 처리량은 초당 400 요청 단위(RU/s)이며 무제한 저장 용량의 경우 최소 처리량은 1000RU/s로 설정됩니다.|

6. 이 필드의 값을 입력한 후 **확인**을 선택하여 설정을 저장합니다.  

   ![컬렉션의 처리량 설정](./media/set-throughput/set-throughput-for-container.png)

7. 기존 컨테이너의 처리량을 업데이트하려면 데이터베이스와 컨테이너를 확장한 다음, **Settings**를 클릭합니다. 새 창에서 새 처리량 값을 입력한 다음, **저장**을 선택합니다.  

   ![컬렉션에 대한 처리량 업데이트](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>컨테이너 집합에 대한 또는 데이터베이스 수준에서 처리량 프로비전

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
2. 왼쪽 탐색 창에서 **모든 리소스**를 선택하고 Azure Cosmos DB 계정을 찾습니다.  
3. 데이터베이스를 만드는 동안 처리량을 구성하거나 기존 데이터베이스 처리량을 업데이트할 수 있습니다.  
4. 데이터베이스 만드는 동안 처리량을 지정하려면 **Data Explorer** 블레이드를 열고 **New Database**를 선택합니다.  
5. **Database ID** 값을 입력하고, **프로비전 처리량** 옵션을 확인한 다음, 처리량 값을 구성합니다. 최소 처리량 값 50,000 RU/s로 데이터베이스를 프로비전할 수 있습니다.  

   ![새 데이터베이스 옵션으로 처리량 설정](./media/set-throughput/set-throughput-with-new-database-option.png)

6. 기존 데이터베이스 처리량을 업데이트하려면 데이터베이스와 컨테이너를 확장한 다음, **Scale**을 클릭합니다. 새 창에서 새 처리량 값을 입력한 다음, **저장**을 선택합니다.  

   ![데이터베이스에 대한 처리량 업데이트](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>데이터베이스에서 개별 컨테이너는 물론 컨테이너 집합에 대한 처리량 프로비전

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
2. 왼쪽 탐색 창에서 **모든 리소스**를 선택하고 Azure Cosmos DB 계정을 찾습니다.  
3. 데이터베이스를 만들고 처리량을 할당합니다. **Data Explorer** 블레이드를 열고 **New Database**를 선택합니다.  
4. **Database ID** 값을 입력하고, **프로비전 처리량** 옵션을 확인한 다음, 처리량 값을 구성합니다. 최소 처리량 값 50,000 RU/s로 데이터베이스를 프로비전할 수 있습니다.  

   ![새 데이터베이스 옵션으로 처리량 설정](./media/set-throughput/set-throughput-with-new-database-option.png)

5. 그런 다음, 위 단계에서 만든 데이터베이스 내에 컬렉션을 만듭니다. 컬렉션을 만들려면 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **New Collection**을 선택합니다.  

6. **Add Collection** 블레이드에 컬렉션에 대한 이름과 파티션 키를 입력합니다. 선택적으로 처리량 값을 지정하지 않을 경우 해당 특정 컨테이너에 대한 처리량을 프로비전할 수 있으며, 데이터베이스에 할당된 처리량은 컬렉션에 공유됩니다.  

   ![선택적으로 컨테이너의 처리량을 설정합니다.](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>처리량을 프로비전할 때 고려 사항

다음은 처리량 예약 전략을 결정하는 데 도움이 되는 몇 가지 고려 사항입니다.

다음과 같은 경우 데이터베이스 수준(즉, 컨테이너 집합인 경우)에서 처리량을 프로비전하는 것이 좋습니다.

* 일부 또는 전체 컨테이너에 걸쳐 처리량을 공유할 수 있는 컨테이너 수가 12개 이상인 경우.  

* IaaS 호스팅된 VM 또는 온-프레미스(예를 들어, NoSQL 또는 관계형 데이터베이스)에서 실행되도록 설계된 단일 테넌트 데이터베이스에서 Azure Cosmos DB로 마이그레이션하고 컨테이너가 많은 경우.  

* 데이터베이스 수준에서 풀링된 처리량을 사용함으로써 작업 부하에 예기치 않은 스파이크를 고려하려는 경우.  

* 개별 컨테이너에서 처리량을 설정하는 대신 데이터베이스 내의 컨테이너 집합에서 집계된 처리량을 얻는 데 관심이 있습니다.

다음과 같은 경우에는 개별 컨테이너에서 처리량을 프로비전하는 것이 좋습니다.

* Azure Cosmos DB 컨테이너 수가 적은 경우.  

* SLA가 뒷받침하는 주어진 컨테이너에서 보장된 처리량을 얻고자 할 경우.

## <a name="throughput-ranges"></a>처리량 범위

다음 테이블에는 컨테이너에 사용할 수 있는 처리량이 나열되어 있습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>단일 파티션 컨테이너</strong></p></td>
            <td valign="top"><p><strong>분할된 컨테이너</strong></p></td>
            <td valign="top"><p><strong>컨테이너 집합</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>최소 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 400개</p></td>
            <td valign="top"><p>초당 요청 단위 1,000개</p></td>
            <td valign="top"><p>초당 요청 단위 50,000개</p></td>
        </tr>
        <tr>
            <td valign="top"><p>최대 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 10,000개</p></td>
            <td valign="top"><p>Unlimited</p></td>
            <td valign="top"><p>Unlimited</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>SQL API for .NET를 사용하여 처리량 설정

다음은 SQL API의 .NET SDK를 사용하여 개별 컨테이너에 대해 초당 3,000개 요청 단위로 컨테이너를 만들기 위한 코드 조각입니다.

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

다음은 SQL API의 .NET SDK를 사용하여 컨테이너 집합에서 초당 100,000개 요청 단위를 프로비저닝하기 위한 코드 조각입니다.

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos DB는 처리량의 예약 모델에서 작동합니다. 즉, 활발하게 *사용된* 처리량에 관계없이 *예약된* 처리량에 따라 요금이 청구됩니다. 응용 프로그램의 부하, 데이터 및 사용 패턴이 변하면 그에 따라 SDK를 통해 또는 [Azure Portal](https://portal.azure.com)을 사용하여 예약된 RU 수를 간단하게 늘리거나 줄일 수 있습니다.

각 컨테이너 또는 컨테이너 집합은 프로비전된 처리량에 대한 메타데이터가 있는 Azure Cosmos DB의 `Offer` 리소스에 매핑됩니다. 컨테이너에 해당하는 제품 리소스를 조회한 다음 새 처리량 값으로 업데이트하여 할당된 처리량을 변경할 수 있습니다. 다음 코드 조각에서는 .NET SDK를 사용하여 컨테이너 처리량을 5,000RU/s로 변경합니다.

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

처리량을 변경할 때 컨테이너 또는 컨테이너 집합의 가용성에는 영향을 주지 않습니다. 일반적으로 새로 예약된 처리량은 새 처리량의 응용 프로그램에서 몇 초 이내에 유효합니다.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>SQL API for Java를 사용하여 처리량을 설정하려면

다음 코드 조각은 현재 처리량을 검색하고 500RU/s로 변경합니다. 전체 코드 샘플은 GitHub의 [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 파일을 참조하세요. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>MongoDB API의 GetLastRequestStatistics 명령을 사용하여 처리량 가져오기

MongoDB API는 지정된 작업에 대한 요청 비용을 검색하는 데 사용자 지정 명령인 *getLastRequestStatistics*를 지원합니다.

예를 들어 Mongo Shell에서 요청 요금을 확인하고자 하는 작업을 실행합니다.
```
> db.sample.find()
```

다음으로 *getLastRequestStatistics* 명령을 실행합니다.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

응용 프로그램에 필요한 예약된 처리량을 예측하는 한 가지 방법은 응용 프로그램에서 사용하는 대표적인 항목에 대해 실행되는 일반 작업과 연결된 요청 단위 요금을 기록한 다음, 예상되는 초당 수행되는 작업 수를 추정하는 것입니다.

> [!NOTE]
> 인덱싱된 속성과 크기 및 개수가 완전히 다른 항목 유형이 있는 경우에는 일반 항목의 각 *유형*과 연결된 적용 가능한 작업 요청 단위 요금을 기록합니다.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>MongoDB API 포털 메트릭을 사용하여 처리량 가져오기

MongoDB API 데이터베이스에 대한 요청 단위 요금을 적절히 추정하는 가장 간단한 방법은 [Azure Portal](https://portal.azure.com) 메트릭을 사용하는 것입니다. *요청 수* 및 *요청 요금* 차트에서 각 작업에서 사용하는 요청 단위 수와 서로 상대적으로 사용하는 요청 단위 수를 추정할 수 있습니다.

![MongoDB API 포털 메트릭][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> MongoDB API에서 예약된 처리량 제한 초과
컨테이너 또는 컨테이너 집합에 대한 프로비전된 처리량을 초과하는 응용 프로그램의 경우 사용률이 프로비전된 처리량 비율 아래로 떨어질 때까지 비율이 제한됩니다. 비율 제한이 발생하면 백 엔드는 `16500` 오류 코드 - `Too Many Requests`으로 요청을 종료합니다. 기본적으로 MongoDB API는 `Too Many Requests` 오류 코드를 반환하기 전에 재시도를 최대 10번까지 자동으로 수행합니다. `Too Many Requests` 오류 코드가 자주 발생하면 응용 프로그램의 오류 처리 루틴에서 재시도 논리를 추가하거나 [컨테이너에 대해 프로비전된 처리량을 늘리는 방법](set-throughput.md)을 고려해 볼 수 있습니다.

## <a name="throughput-faq"></a>처리량 FAQ

**내 처리량을 400RU/s 미만으로 설정할 수 있나요?**

Cosmos DB 단일 파티션 컨테이너에서 사용할 수 있는 최소 처리량은 400RU/s이고 분할된 컨테이너에 대한 최소값은 1000RU/s입니다. 요청 단위는 100RU/s 간격으로 설정되어 있지만 처리량은 100RU/s 또는 400RU/s 미만인 값으로 설정할 수 없습니다. Cosmos DB를 개발하고 테스트하는 비용 효과적인 방법을 찾으려는 경우 비용 없이 로컬에 배포할 수 있는 [Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용할 수 있습니다. 

**MongoDB API를 사용하여 처리량을 설정하려면 어떻게 해야 하나요?**

처리량을 설정할 수 있는 MongoDB API 확장은 없습니다. [SQL API for .NET을 사용하여 처리량을 설정하려면](#set-throughput-sdk)에 나와 있는 대로 SQL API를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 처리량 및 요청 단위를 계산하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 요청 단위 및 처리량 추정 ](request-units.md)을 참조하세요.

* Cosmos DB를 사용하여 프로비전을 수행하고 대규모로 크기를 조정하려면 [Cosmos DB로 분할 및 크기 조정](partition-data.md)을 참조하세요.

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
