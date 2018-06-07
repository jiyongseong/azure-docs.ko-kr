---
title: Azure Cosmos DB에서 분할 및 수평적 확장 | Microsoft Docs
description: Azure Cosmos DB에서 분할 작동 방법, 분할 및 파티션 키를 구성하는 방법 및 응용 프로그램에 대한 올바른 파티션 키를 선택하는 방법을 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1976ab5ab0bd0037163b2ad8048fcee10b204ea2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Azure Cosmos DB의 파티션 및 확장

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)는 신속하고 예측 가능한 성능을 위해 세계적으로 배포된 다중 모델 데이터베이스 서비스입니다. 응용 프로그램의 증대에 따라 자연스럽게 확장됩니다. 이 문서에서는 Azure Cosmos DB의 모든 데이터 모델에서 분할이 어떻게 작동하는지에 대한 개요를 제공합니다. 또한 효과적으로 응용 프로그램을 확장하기 위해 Azure Cosmos DB 컨테이너를 구성하는 방법에 대해서도 설명합니다.

이 비디오에서는 분할 및 파티션 키를 설명합니다.

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB에서 분할
Azure Cosmos DB에서는 규모에 상관없이 몇 밀리초 대기 시간으로 스키마 없는 데이터를 저장하고 쿼리할 수 있습니다. Azure Cosmos DB는 *컬렉션*(문서의 경우), *그래프* 또는 *테이블*이라는 데이터 저장용 컨테이너를 제공합니다. 

컨테이너는 하나 이상의 물리적 파티션 또는 서버에 걸쳐 있을 수 있는 논리적 리소스입니다. 파티션 수는 컨테이너 또는 컨테이너 집합에 대해 프로비전된 저장소 크기 및 처리량에 따라 Azure Cosmos DB에 의해 결정됩니다. 

*실제* 파티션은 고정된 양의 예약된 SSD 기반 저장소에 가변적 양의 계산 리소스(CPU 및 메모리)가 결합되어 있습니다. 고가용성을 위해 실제 파티션 각각을 복제합니다. 각 컨테이너 집합은 하나 이상의 실제 파티션을 공유할 수 있습니다. 실제 파티션 관리는 Azure Cosmos DB에 의해 완전히 관리되므로 복잡한 코드를 작성하거나 파티션을 관리할 필요가 없습니다. Azure Cosmos DB 컨테이너는 처리량 및 저장소 면에서 제한되지 않습니다. 

*논리* 파티션은 단일 파티션 키 값과 관련된 모든 데이터를 저장하는 실제 파티션 내의 파티션입니다. 여러 논리 파티션이 동일한 실제 파티션에서 종료될 수 있습니다. 다음 그림에서는 단일 컨테이너에 세 개의 논리 파티션이 있습니다. 각 논리 파티션은 각각 하나의 파티션 키, LAX, AMS 및 MEL에 대한 데이터를 저장합니다. 각 LAX, AMS 및 MEL 논리 파티션은 10GB라는 논리 파티션 최대 제한을 초과할 수 없습니다. 

![리소스 분할](./media/introduction/azure-cosmos-db-partitioning.png) 

컨테이너가 [분할 필수 구성 요소](#prerequisites)를 충족하는 경우 분할은 응용 프로그램에 대해 투명합니다. Azure Cosmos DB는 빠른 읽기 및 쓰기, 쿼리, 트랜잭션 논리, 일관성 수준 및 단일 컨테이너 리소스에 대한 메서드/API를 통해 세분화된 액세스 제어를 지원합니다. 이 서비스에서는 실제 및 논리 파티션 간에 데이터를 분산하고 올바른 파티션에 대한 쿼리 요청을 라우팅하는 작업을 처리합니다. 

## <a name="how-does-partitioning-work"></a>분할 작동 방법

분할은 어떻게 작동하나요? 각 항목에는 *파티션 키* 및 *행 키*가 있어서 항목을 고유하게 식별해야 합니다. 파티션 키는 데이터에 대한 논리적 분할의 역할을 하고 실제 파티션 간에 데이터를 배포하는 자연 경계를 가진 Azure Cosmos DB를 제공합니다. 단일 논리 파티션에 대한 데이터는 단일 실제 파티션 내에 있어야 하지만 실제 파티션은 Azure Cosmos DB에서 관리됩니다. 

간단히 말해, Azure Cosmos DB에서 분할의 작동 방식은 다음과 같습니다.

* **T**RU/s(초당 요청) 처리량으로 Azure Cosmos DB 컨테이너 집합을 프로비전합니다.
* Azure Cosmos DB는 내부적으로 초당 **T** 요청을 제공하는 데 필요한 실제 파티션을 프로비전합니다. **T**가 실제 파티션당 최대 처리량 **t**보다 높은 경우 Azure Cosmos DB는 **N = T/t**의 실제 파티션을 프로비전합니다. 파티션(t)당 최대 처리량 값은 Azure Cosmos DB에 의해 구성되며, 이 값은 사용된 하드웨어 구성 및 프로비전된 총 처리량에 따라 할당됩니다. 
* Azure Cosmos DB는 **N**의 실제 파티션에 걸쳐 균등하게 파티션 키 해시의 주요 공간을 할당합니다. 따라서 각 실제 파티션은 **1/N** 파티션 키 값(논리 파티션)을 호스팅합니다.
* 실제 파티션 **p**가 저장소 한도에 도달하면 Azure Cosmos DB가 자연스럽게 **p**를 새로운 두 실제 파티션 **p1**과 **p2**로 분할합니다. 각각의 새 실제 파티션에는 키의 약 절반에 해당하는 값을 배포합니다. 이 분리 작업은 응용 프로그램에 전혀 나타나지 않습니다. 실제 파티션이 저장소 제한에 도달하고 실제 파티션의 모든 데이터가 동일한 논리 파티션 키에 속하는 경우 분할 작업이 발생하지 않습니다. 단일 논리 파티션 키에 대한 모든 데이터가 동일한 실제 파티션에 있어야 하기 때문입니다. 이 경우 다른 파티션 키 전략을 사용해야 합니다.
* **t*N**보다 높은 처리량을 프로비전할 경우 Azure Cosmos DB는 더 높은 처리량을 지원하기 위해 하나 이상의 실제 파티션을 나눕니다.

파티션 키의 의미 체계는 다음 표에 나와 있는 것처럼 각 API의 의미 체계와 약간 다릅니다.

| API | 파티션 키 | 행 키. |
| --- | --- | --- |
| SQL | 사용자 지정 파티션 키 경로 | 고정 `id` | 
| MongoDB | 사용자 지정 분할 키  | 고정 `_id` | 
| Gremlin | 사용자 지정 파티션 키 속성 | 고정 `id` | 
| 테이블 | 고정 `PartitionKey` | 고정 `RowKey` | 

Azure Cosmos DB는 해시 기반 분할을 사용합니다. 항목을 작성하는 경우 Azure Cosmos DB는 파티션 키 값을 해시하고 해시된 결과를 사용하여 항목을 저장할 파티션을 결정합니다. Azure Cosmos DB는 동일한 실제 파티션에 동일한 파티션 키를 가진 모든 항목을 저장합니다. 파티션 키의 선택은 디자인 타임에서 결정해야 하는 중요한 사항입니다. 다양한 범위의 값과 균일한 액세스 패턴을 가진 속성 이름을 선택합니다. 실제 파티션이 저장소 제한에 도달하고 동일한 파티션의 데이터가 동일한 파티션 키를 갖는다면 Azure Cosmos DB는 *"파티션 키가 최대 크기인 10GB에 도달했습니다."* 라는 메시지를 반환하고 파티션은 분할되지 않습니다. 파티션 키 선택은 매우 중요한 결정입니다.

> [!NOTE]
> 많은 수의 고유 값(예: 수백 또는 수천 개)을 갖는 파티션 키가 적합합니다. 이렇게 하면 값 사이에 워크로드가 고르게 배분됩니다. 이상적인 파티션 키는 쿼리에서 자주 필터로 표시되며 솔루션 확장에 충분한 카디널리티가 있는 키입니다.
>

Azure Portal에서 Azure Cosmos DB 컨테이너를 *고정* 또는 *무제한*으로 만들 수 있습니다. 고정 크기 컨테이너는 최대 제한 10GB 및 10,000RU/s 처리량을 설정할 수 있습니다. 무제한으로 컨테이너를 만들려면 파티션 키와 최소 1,000RU/s 처리량을 지정해야 합니다. 

Azure DB Cosmos 컨테이너를 컨테이너 집합 간에 처리량을 공유하도록 구성할 수도 있습니다. 이 경우 각 컨테이너는 파티션 키를 지정해야 하며 무제한으로 커질 수 있습니다.

파티션에 데이터가 분산되는 방법을 확인하는 것이 좋습니다. 포털에서 데이터 분산을 확인하려면 Azure Cosmos DB 계정으로 이동하고 **모니터링** 섹션에서 **메트릭**을 클릭한 다음, **저장소** 탭을 클릭하여 데이터가 다른 실제 파티션으로 분할되는지 확인합니다.

![리소스 분할](./media/partition-data/partitionkey-example.png)

위의 왼쪽 이미지는 잘못된 파티션 키의 결과이며, 오른쪽 이미지는 좋은 파티션 키를 선택한 경우의 결과입니다. 왼쪽 이미지에서 데이터가 파티션 간에 균등하게 배포되지 않는 것을 확인할 수 있습니다. 데이터가 오른쪽 이미지처럼 배포되는 파티션 키를 선택하도록 해야 합니다.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>분할을 위한 필수 구성 요소

[분할 작동 방법](#how-does-partitioning-work)에 설명된 대로 실제 파티션을 **p1** 및 **p2**로 자동 분할하는 경우 1,000RU/s 이상의 처리량을 포함(또는 컨테이너 집합 간에 처리량을 공유)하는 컨테이너를 만들고 파티션 키를 제공해야 합니다. Azure Portal에서 컨테이너(예: 컬렉션, 그래프 또는 테이블)를 만들 때 무제한 확장을 활용할 수 있게 **무제한** 저장소 용량 옵션을 선택합니다. 

Azure Portal에서 또는 프로그래밍 방식으로 컨테이너를 만들었고 최초 처리량이 1,000RU/s 이상이었으며 파티션 키를 제공한 경우, 컨테이너 변경 없이 무제한 확장을 활용할 수 있습니다.  최초 컨테이너가 처리량이 1,000RU/s 이상으로 만들어졌고 파티션 키를 지정했다면 **고정** 컨테이너가 여기에 포함됩니다.

컨테이너 집합의 일부로 처리량을 공유하도록 구성된 모든 컨테이너는 **무제한** 컨테이너로 취급됩니다.

파티션 키 없이 또는 1,000RU/s 미만의 처리량으로 **고정** 컨테이너를 만든 경우 이 컨테이너는 이 문서에서 설명한 것처럼 자동 확장되지 않습니다. 고정 컨테이너에서 무제한 컨테이너(최소 1,000RU/s 처리량 및 파티션 키가 있는 하나의 컨테이너)로 데이터를 마이그레이션하려면 [데이터 마이그레이션 도구](import-data.md) 또는 [변경 피드 라이브러리](change-feed.md)를 사용해야 합니다. 

## <a name="partitioning-and-provisioned-throughput"></a>분할 및 프로비전된 처리량
Azure Cosmos DB는 예측 가능한 성능을 위해 설계되었습니다. 컨테이너 또는 컨테이너 집합을 만들 때 *초당 [RU(요청 단위)](request-units.md) 단위*로 처리량을 예약합니다. 각 요청에서는 작업에 소비된 CPU, 메모리 및 IO 같은 시스템 리소스의 양에 비례하는 RU 요금이 발생합니다. 세션 일관성에 따라 1KB 문서를 읽을 경우 1RU가 소비됩니다. 저장된 항목 수 또는 동시에 실행되는 요청 수에 상관없이 읽기당 1RU입니다. 항목의 크기가 클수록 크기에 따라 더 높은 RU가 필요합니다. 응용 프로그램에 지원해야 하는 엔터티 크기 및 읽기 수를 알고 있는 경우 응용 프로그램의 요구에 맞게 정확히 필요한 처리량을 프로비전할 수 있습니다. 

> [!NOTE]
> 컨테이너 또는 컨테이너 집합에 대해 프로비전된 처리량을 완벽하게 활용하려면 모든 고유한 파티션 키 값 간에 요청을 균등하게 배분할 수 있는 파티션 키를 선택해야 합니다.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Azure Cosmos DB API 작업
Azure Portal 또는 Azure CLI를 사용하여 언제든지 컨테이너를 만들고 확장할 수 있습니다. 이 섹션에서는 컨테이너를 만들고 각각의 API를 사용하여 프로비전된 처리량 및 파티션 키를 지정하는 방법을 보여줍니다.


### <a name="sql-api"></a>SQL API
다음 샘플에서는 SQL API를 사용하여 컨테이너(컬렉션)를 만드는 방법을 보여줍니다. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

REST API에서 `GET` 메서드 또는 SDK 중 하나에서 `ReadDocumentAsync`를 사용하여 항목(문서)을 읽을 수 있습니다.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

자세한 내용은 [SQL API를 사용하여 Azure Cosmos DB에서 분할](sql-api-partition-data.md)을 참조하세요.

### <a name="mongodb-api"></a>MongoDB API
MongoDB API를 사용하면 선호하는 도구, 드라이버 또는 SDK를 통해 공유 컬렉션을 만들 수 있습니다. 이 예제에서는 Mongo Shell을 사용하여 컬렉션을 만듭니다.

Mongo Shell에서:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
결과:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>테이블 API

Table API를 사용하여 테이블을 만들려면 `CreateIfNotExists` 메서드를 사용합니다. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

프로비전된 처리량은 `CreateIfNotExists`의 인수로 설정됩니다. 파티션 키는 암시적으로 `PartitionKey` 값으로 만들어집니다. 

다음 코드를 사용하여 단일 엔터티를 검색할 수 있습니다.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
자세한 내용은 [테이블 API를 통한 개발](tutorial-develop-table-dotnet.md)을 참조하세요.

### <a name="gremlin-api"></a>Gremlin API

Gremlin API를 통해 Azure Portal이나 Azure CLI를 사용하여 그래프를 나타내는 컨테이너를 만들 수 있습니다. 또는 Azure Cosmos DB가 다중 모델이므로 다른 API를 사용하여 그래프 컨테이너를 만들고 확장할 수 있습니다.

Gremlin에서 파티션 키 및 ID를 사용하여 꼭짓점 또는 에지를 읽을 수 있습니다. 예를 들어, 지역("미국")의 그래프가 파티션 키이고 "시애틀"이 행 키인 경우 다음 구문을 사용하여 꼭짓점을 찾을 수 있습니다.

```
g.V(['USA', 'Seattle'])
```

파티션 키와 행 키를 사용하는 에지를 참조할 수 있습니다.

```
g.E(['USA', 'I5'])
```

자세한 내용은 [Azure Cosmos DB에서 분할된 그래프 사용](graph-partitioning.md)을 참조하세요.


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>확장을 위한 디자인
Azure Cosmos DB를 효과적으로 확장하려면 컨테이너를 만들 때 적절한 파티션 키를 선택해야 합니다. 좋은 파티션 키를 선택하는 데는 다음 두 가지 주요 고려 사항이 있습니다.

* **쿼리 경계 및 트랜잭션** 파티션 키를 선택할 때는 트랜잭션 사용에 대한 요구와 여러 파티션 키에 엔터티를 분산하는 데 대한 요구 사항(솔루션의 확장성 향상) 사이의 균형 유지가 필요합니다. 한 쪽으로 치우치면 모든 항목에 동일한 파티션 키를 설정할 수 있지만 이 옵션에서는 솔루션의 확장성이 제한될 수 있습니다. 다른 한편으로 각 항목에 대해 고유의 파티션 키를 할당할 수 있습니다. 이 선택은 확장성이 높으나 저장 프로시저와 트리거를 통해 문서 간 트랜잭션을 사용할 수 없게 됩니다. 이상적인 파티션 키는 효율적인 쿼리를 사용할 수 있도록 하고 솔루션을 확장할 수 있는 충분한 카디널리티를 보장합니다. 
* **저장소 및 성능 병목 현상 없음** 쓰기를 다양한 고유 값에 분산할 수 있는 속성을 선택해야 합니다. 동일한 파티션 키에 대한 요청은 파티션에 할당된 프로비전된 처리량을 초과할 수 없고 속도가 제한됩니다. 따라서 응용 프로그램 내에서 "핫 스폿"을 초래하지 않는 파티션 키를 선택해야 합니다. 단일 파티션 키에 대한 모든 데이터는 파티션 내에 저장해야 하므로 같은 가격에 데이터 볼륨이 많은 파티션 키는 피해야 합니다. 

몇 가지 실제 시나리오 및 적절한 파티션 키를 각각 살펴보겠습니다.
* 사용자 프로필 백 엔드를 구현하는 경우 *사용자 ID*를 파티션 키로 선택하는 것이 좋습니다.
* IoT 데이터(예: 장치 상태)를 저장하는 경우 *장치 ID*를 파티션 키로 선택하는 것이 좋습니다.
* 시계열 데이터를 기록하는 데 Azure Cosmos DB를 사용하는 경우 *호스트 이름* 또는 *프로세스 ID*를 파티션 키로 선택하는 것이 좋습니다.
* 다중 테넌트 아키텍처가 있는 경우 *테넌트 ID*를 파티션 키로 선택하는 것이 좋습니다.

일부 사례(예: IoT 및 사용자 프로필)에서는 파티션 키가 사용자 *ID*(문서 키)와 동일할 수 있습니다. 시계열 데이터와 같은 다른 사례에서는 *ID*와 다른 파티션 키를 선택할 수 있습니다.

### <a name="partitioning-and-loggingtime-series-data"></a>분할 및 로깅/시계열 데이터
Azure Cosmos DB의 일반적인 사용 사례 중 하나는 로깅 및 원격 분석입니다. 이 시나리오에서는 방대한 양의 데이터에 대한 읽기/쓰기가 필요할 수 있으므로 적절한 파티션 키를 선택하는 것이 중요합니다. 파티션 키 선택은 사용자의 읽기 및 쓰기 비율 및 실행할 쿼리의 종류에 따라 달라집니다. 다음은 적절한 파티션 키를 선택하는 방법에 대한 몇 가지 팁입니다.

* 사용 사례에서 쓰기 비율이 적으며 시간 경과에 따라 누적되고 다른 필터가 있는 광범위 타임스탬프를 통한 쿼리가 필요한 경우 타임스탬프 롤업을 사용합니다. 예를 들어 파티션 키로 날짜를 사용 하는 것이 좋은 방법입니다. 이 방식으로 단일 파티션에서 특정 날짜에 대한 모든 데이터를 쿼리할 수 있습니다. 
* 이 시나리오에서는 매우 일반적인 경우인데, 쓰기 작업이 많은 워크로드에는 타임스탬프를 기반으로 하지 않는 파티션 키를 사용합니다. 이렇게 하면 Azure Cosmos DB가 여러 파티션에서 쓰기 작업을 배분 및 확장할 수 있습니다. 여기서 높은 카디널리티의 *호스트 이름*, *프로세스 ID*, *작업 ID* 또는 다른 속성을 선택하는 것이 좋습니다. 
* 다른 방법은 여러 컨테이너가 있는(각 일/월에 대해 하나) 하이브리드 방식으로, 파티션 키는 *호스트 이름*처럼 더 세부적인 속성입니다. 이 방식은 시간 범위 및 규모와 성능 요구에 따라 각 컨테이너 또는 컨테이너 집합에 대해 서로 다른 처리량을 설정할 수 있는 장점이 있습니다. 예를 들어 이번 달의 컨테이너는 읽기와 쓰기를 서비스하므로 더 높은 처리량을 프로비전할 수 있습니다. 지난 달의 경우 읽기만 서비스했으므로 더 낮은 처리량을 프로비전했습니다.

### <a name="partitioning-and-multitenancy"></a>분할 및 다중 테넌트
Azure Cosmos DB를 사용하여 다중 테넌트 응용 프로그램을 구현하는 경우 *테넌트당 하나의 파티션 키* 및 *테넌트당 하나의 컨테이너* 등, 널리 사용되는 두 가지 디자인을 고려할 수 있습니다. 각각의 장단점은 다음과 같습니다.

* **테넌트당 하나의 파티션 키**  이 모델에서는 테넌트가 단일 컨테이너 내에 함께 배치됩니다. 단일 테넌트에 대한 쿼리 및 삽입을 단일 파티션에 대해 수행할 수 있습니다. 또한 테넌트에 속한 모든 항목에서 트랜잭션 논리를 구현할 수 있습니다. 여러 테넌트가 한 컨테이너를 공유하기 때문에, 각각의 테넌트에 대한 프로비전이 아니라 한 컨테이너 안의 모든 테넌트에 대해 리소스를 풀링하여 저장소 및 프로비전된 처리량을 더 잘 활용할 수 있습니다. 단점은 테넌트당 성능 격리가 없다는 점입니다. 성능을 위해 처리량을 높이면 개별 테넌트에 대해 목표한 만큼이 증가하는 것이 아니라 모든 테넌트가 있는 전체 컨테이너에 적용됩니다.
* **테넌트당 하나의 컨테이너**  이 모델에서는 각 테넌트에 자체 컨테이너가 있고 처리량을 예약하여 테넌트별로 성능을 보장할 수 있습니다. 이 모델은 몇 개의 테넌트가 있는 다중 테넌트 응용 프로그램의 경우에 더 비용 효율적입니다.

작은 테넌트를 함께 배치하고 더 큰 테넌트는 고유한 컨테이너로 격리하는 하이브리드 방식을 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Cosmos DB에서의 확장 및 분할을 위한 개념 및 모범 사례의 개요를 제공했습니다. 

* [Azure Cosmos DB에서 프로비전된 처리량](request-units.md)에 대한 자세한 정보
* [Azure Cosmos DB에서 글로벌 배포](distribute-data-globally.md)에 대한 자세한 정보



