---
title: Azure Cosmos DB의 분할 및 크기 조정 | Microsoft Docs
description: Azure Cosmos DB에서 분할 작동 방법, 분할 및 파티션 키를 구성하는 방법 및 응용 프로그램에 대한 올바른 파티션 키를 선택하는 방법을 알아봅니다.
services: cosmos-db
author: rafats
manager: kfile
documentationcenter: ''
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3bdc7820910540b789fd11533389f79aa9f297f5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>SQL API를 사용하여 Azure Cosmos DB에서 분할

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md)는 전역적으로 배포된 다중 모델 데이터베이스 서비스로써 신속하고 예측 가능한 성능을 얻을 수 있고, 응용 프로그램 증가에 따라 효율적인 확장이 가능하도록 설계되었습니다. 

이 문서에서는 SQL API를 사용하여 Cosmos DB 컨테이너의 분할 작업을 수행하는 방법에 대한 개요를 제공합니다. Azure Cosmos DB API를 사용하여 분할하기 위한 개념 및 모범 사례의 개요는 [분할 및 수평적 크기 조정](../cosmos-db/partition-data.md)을 참조하세요. 

코드를 시작하려면 [Github](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark)에서 프로젝트를 다운로드합니다. 

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.   

* Azure Cosmos DB에서 분할이 작동하는 방식
* Azure Cosmos DB에서 분할을 구성하는 방법
* 파티션 키의 정의 및 응용 프로그램에 올바른 파티션 키를 선택하는 방법

코드를 시작하려면 [Azure Cosmos DB 성능 테스트 드라이버 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark)에서 프로젝트를 다운로드합니다. 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>파티션 키

SQL API에서 JSON 경로 형태로 파티션 키 정의를 지정합니다. 다음 표에서는 파티션 키 정의 및 각 키에 해당하는 값의 예를 보여 줍니다. 파티션 키는 경로로 지정됩니다. 예를 들어 `/department`는 department 속성을 나타냅니다. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>파티션 키</strong></p></td>
            <td valign="top"><p><strong>설명</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>doc가 항목인 doc.department의 값에 해당합니다.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>doc가 항목인 doc.properties.name의 값에 해당합니다(중첩된 속성).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>doc.id의 값에 해당합니다(ID 및 파티션 키는 동일한 속성임).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"department name"</p></td>
            <td valign="top"><p>doc가 항목인 doc["department name"]의 값에 해당합니다.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> 파티션 키 구문은 인덱싱 정책 경로에 대한 경로 지정과 유사하지만, 경로가 값 대신 속성에 해당한다는 주요 차이점이 있습니다. 즉, 끝에 와일드카드가 없습니다. 즉, 끝에는 와일드 카드가 없습니다. 예를 들어 부서 아래에서 값을 인덱싱하도록 /department/?를 지정하지만 /department를 파티션 키 정의로 지정합니다. 파티션 키는 암시적으로 인덱싱되며, 인덱싱 정책 재정의를 사용하여 인덱싱에서 제외할 수 없습니다.
> 
> 

파티션 키의 선택이 응용 프로그램의 성능에 미치는 영향을 살펴보겠습니다.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Azure Cosmos DB SDK 사용
Azure Cosmos DB에는 [REST API 버전 2015-12-16](/rest/api/cosmos-db/)을 사용한 자동 분할에 대한 지원이 추가되었습니다. 분할된 컨테이너를 만들려면 지원되는 SDK 플랫폼(.NET, Node.js, Java, Python, MongoDB) 중 하나에서 SDK 버전 1.6.0 이상을 다운로드해야 합니다. 

### <a name="creating-containers"></a>컨테이너 만들기
다음 샘플에서는 처리량이 초당 20,000개 요청 단위인 장치 원격 분석 데이터를 저장하는 컨테이너를 만드는 .NET 코드 조각을 보여 줍니다. SDK는 OfferThroughput 값을 설정합니다(이 값은 REST API에서 `x-ms-offer-throughput` 요청 헤더를 설정함). 여기에서는 `/deviceId`를 파티션 키로 설정합니다. 선택한 파티션 키는 이름 및 인덱싱 정책과 같은 나머지 컨테이너 메타데이터와 함께 저장됩니다.

이 샘플에서는 (a) 장치 수가 많고, 쓰기가 파티션 간에 균등하게 분산될 수 있으며, 대용량 데이터를 수집하도록 데이터베이스를 확장할 수 있으며, (b) 장치에 대한 최신 읽기 가져오기와 같은 요청이 대부분 단일 deviceId로 범위가 지정되고 단일 파티션에서 검색될 수 있음을 알고 있기 때문에 `deviceId`를 선택했습니다.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

이 메서드에서 Cosmos DB에 대한 REST API 호출을 실행하면 이 서비스가 요청된 처리량에 따라 파티션 수를 프로비전합니다. 성능은 향상되어야 하므로 컨테이너 또는 컨테이너 집합의 처리량을 변경할 수 있습니다. 

### <a name="reading-and-writing-items"></a>항목 읽기 및 쓰기
이제 Cosmos DB에 데이터를 삽입해 보겠습니다. 다음은 장치 읽기 및 새 장치 읽기를 컨테이너에 삽입하는 CreateDocumentAsync 호출이 포함된 샘플 클래스입니다. 다음은 SQL API를 활용하는 예제 코드 블록입니다.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

파티션 키와 ID로 항목을 읽고 업데이트한 다음 마지막 단계로 파티션 키와 ID로 해당 항목을 삭제해 보겠습니다. 읽기에 PartitionKey 값(REST API의 `x-ms-documentdb-partitionkey` 요청 헤더에 해당)이 포함됩니다.

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>분할된 컨테이너 쿼리
분할된 컨테이너의 데이터를 쿼리하면 Azure Cosmos DB에서 필터에 지정된 파티션 키 값(있는 경우)에 해당하는 파티션으로 쿼리를 자동으로 라우팅합니다. 예를 들어 이 쿼리는 파티션 키 "XMS-0001"이 포함된 파티션으로만 라우팅됩니다.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
다음 쿼리는 파티션 키(DeviceId)에 대한 필터가 없으므로 파티션의 인덱스에 대해 실행되는 모든 파티션으로 팬아웃됩니다. SDK가 파티션에 걸쳐 쿼리를 실행하도록 EnableCrossPartitionQuery(REST API의 `x-ms-documentdb-query-enablecrosspartition`)를 지정해야 합니다.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB는 SDK 1.12.0 이상에서 SQL을 사용하는 분할된 컨테이너에서 [집계 함수](sql-api-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX` 및 `AVG`를 지원합니다. 쿼리는 단일 집계 연산자를 포함해야 하고 프로젝션에 단일 값을 포함해야 합니다.

### <a name="parallel-query-execution"></a>병렬 쿼리 실행
Cosmos DB SDK 1.9.0 이상에서는 다수의 파티션에 연결해야 하는 경우에도 분할된 컬렉션에 대해 대기 시간이 짧은 쿼리를 수행할 수 있도록 하는 병렬 쿼리 실행 옵션을 지원합니다. 예를 들어 다음 쿼리는 파티션에 걸쳐 병렬로 실행되도록 구성되어 있습니다.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
다음 매개 변수를 조정하여 병렬 쿼리 실행을 관리할 수 있습니다.

* `MaxDegreeOfParallelism`을 설정하여 컨테이너의 파티션에 대한 최대 동시 네트워크 연결 수를 나타내는 병렬 처리 수준을 제어할 수 있습니다. 이 속성을 -1로 설정하는 경우 병렬 처리 수준이 SDK에서 관리됩니다. `MaxDegreeOfParallelism`이 지정되지 않았거나 기본값인 0으로 설정된 경우 컨테이너의 파티션에 단일 네트워크 연결이 생성됩니다.
* `MaxBufferedItemCount`를 설정하여 쿼리 대기 시간과 클라이언트 쪽 메모리 사용률 간에 균형을 유지할 수 있습니다. 이 매개 변수를 생략하거나 이 속성을 -1로 설정하는 경우 병렬 쿼리 실행 중에 버퍼링되는 항목의 수가 SDK에서 관리됩니다.

컬렉션에 동일한 상태를 지정할 경우, 병렬 쿼리는 직렬 실행의 경우와 동일한 순서로 결과를 반환합니다. 정렬(ORDER BY 및/또는 TOP)을 포함하는 파티션 간 쿼리를 수행할 경우 Azure Cosmos DB SDK는 파티션에 걸쳐 병렬로 쿼리를 실행하고, 클라이언트 쪽에서 부분적으로 정렬된 결과를 병합하여 전역으로 정렬된 결과를 생성합니다.

### <a name="executing-stored-procedures"></a>저장된 프로시저 실행
장치 ID가 동일한 문서에 대해 원자성 트랜잭션을 실행할 수도 있습니다(예: 장치의 최신 상태 또는 집계를 단일 항목에서 유지 관리하는 경우). 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
다음 섹션에서는 단일 파티션 컨테이너에서 분할된 컨테이너로 이동하는 방법을 살펴보겠습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 SQL API를 사용하여 Azure Cosmos DB 컨테이너의 분할 작업을 수행하는 방법에 대한 개요를 제공했습니다. Azure Cosmos DB API를 사용하여 분할하기 위한 개념 및 모범 사례의 개요는 [분할 및 수평적 크기 조정](../cosmos-db/partition-data.md)을 참조하세요. 

* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행합니다. 샘플에 대해서는 [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)를 참조하세요.
* [SDKs](sql-api-sdk-dotnet.md) 또는 [REST API](/rest/api/cosmos-db/)를 사용하여 코딩 시작
* [Azure Cosmos DB에서 프로비전된 처리량](request-units.md)에 대한 자세한 정보

