---
title: 'Azure Cosmos DB: .NET의 Table API를 사용하여 개발 | Microsoft Docs'
description: .NET을 사용하는 Azure Cosmos DB의 Table API를 통해 개발하는 방법에 대해 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 52d91205e7b0daa82fb12c166222b1dce4c699eb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: .NET의 Table API를 사용하여 개발

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다.

이 자습서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"] 
> * Azure Cosmos DB 계정 만들기 
> * app.config 파일에서 기능을 사용하도록 설정 
> * [Table API](table-introduction.md)를 사용하여 테이블 만들기
> * 테이블에 엔터티 추가 
> * 엔터티 일괄 삽입 
> * 단일 엔터티 검색 
> * 자동 보조 인덱스를 사용하여 엔터티 쿼리 
> * 엔터티 바꾸기 
> * 엔터티 삭제 
> * 테이블 삭제
 
## <a name="tables-in-azure-cosmos-db"></a>Azure Cosmos DB의 테이블 

Azure Cosmos DB는 스키마를 사용하지 않는 키-값 저장소가 필요한 응용 프로그램을 위해 [테이블 API](table-introduction.md)를 제공합니다. 이제 Azure Cosmos DB 테이블 API 및 [Azure Table Storage](../storage/common/storage-introduction.md) 둘 다 동일한 SDK 및 REST API를 지원합니다. Azure Cosmos DB를 사용하면 높은 처리량 요구 사항의 테이블을 만들 수 있습니다.

이 자습서는 Azure Table Storage SDK에 익숙하고 Azure Cosmos DB에서 사용 가능한 프리미엄 기능을 사용하려는 개발자를 위한 것입니다. [.NET을 사용하여 Azure Table 저장소 시작](table-storage-how-to-use-dotnet.md)을 기반으로 하며, 보조 인덱스, 프로비전된 처리량 및 멀티 호밍과 같은 추가 기능을 활용하는 방법을 보여 줍니다. 이 자습서에서는 Azure Portal을 사용하여 Azure Cosmos DB 계정을 만든 다음 Table API 응용 프로그램을 빌드하고 배포하는 방법에 대해 설명합니다. 또한 테이블을 만들고 삭제하며, 테이블 데이터를 삽입, 업데이트, 삭제 및 쿼리하기 위한 .NET 예제를 단계별로 안내합니다. 

현재 Azure Table Storage를 사용하고 있다면 Azure Cosmos DB 테이블 API를 사용할 경우 다음과 같은 이점이 있습니다.

- 멀티 호밍 및 [자동 및 수동 장애 조치](regional-failover.md)와 함께 턴키 방식으로 [전역 배포](distribute-data-globally.md)
- 모든 속성("보조 인덱스")에 대한 스키마 독립적 자동 인덱싱 및 빠른 쿼리 지원 
- 여러 지역 간에 [독립적인 저장소 및 처리량 크기 조정](partition-data.md) 지원
- 초당 수백 개에서 수백만 개의 요청으로 확장할 수 있는 [테이블당 전용 처리량](request-units.md) 지원
- 응용 프로그램 요구 사항에 따라 가용성, 대기 시간 및 일관성을 조정할 수 있는 [튜닝 가능한 5가지 일관성 수준](consistency-levels.md) 지원
- 단일 지역 내 99.99% 가용성, 더 높은 가용성을 위해 더 많은 지역을 추가할 수 있는 기능 및 일반 가용성에 대한 [업계 최고의 포괄적 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 기존 Azure 저장소 .NET SDK 사용 및 응용 프로그램에 대한 코드 변경 없음

이 자습서에서는 .NET SDK를 사용하는 Azure Cosmos DB 테이블 API에 대해 설명합니다. NuGet에서 [Azure Cosmos DB 테이블 API .NET SDK](https://aka.ms/tableapinuget)를 다운로드할 수 있습니다.

복잡한 Azure Table 저장소 작업에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Cosmos DB 테이블 API 소개](table-introduction.md)
* 사용 가능한 API에 대한 자세한 내용은 Table service 참조 설명서인 [Azure Cosmos DB 테이블 API .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)를 참조하세요.

### <a name="about-this-tutorial"></a>이 자습서 정보
이 자습서는 Azure Table 저장소 SDK에 익숙하고 Azure Cosmos DB를 사용하여 제공되는 프리미엄 기능을 사용하려는 개발자를 위한 것입니다. [.NET을 사용하여 Azure Table 저장소 시작](table-storage-how-to-use-dotnet.md)을 기반으로 하며, 보조 인덱스, 프로비전된 처리량 및 멀티 호밍과 같은 추가 기능을 활용하는 방법을 보여 줍니다. Azure Portal을 사용하여 Azure Cosmos DB 계정을 만든 다음 Table 응용 프로그램을 빌드하고 배포하는 방법에 대해 설명합니다. 또한 테이블을 만들고 삭제하며, 테이블 데이터를 삽입, 업데이트, 삭제 및 쿼리하기 위한 .NET 예제를 단계별로 안내합니다. 

Visual Studio 2017을 아직 설치하지 않은 경우 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **평가판**을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

Azure Portal에서 Azure Cosmos DB 계정을 만들어 보겠습니다.  
 
> [!IMPORTANT]  
> 일반 공급 Table API SDK를 사용하려면 새 Table API 계정을 만들어야 합니다. 미리 보기 중에 만들어진 Table API 계정은 일반 공급 SDK에서 지원되지 않습니다. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 github에서 Table 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 폴더를 변경합니다. 

    ```bash
    cd "C:\git-samples"
    ```

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. 그런 다음 Visual Studio에서 솔루션을 엽니다. 

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다. 

1. [Azure Portal](http://portal.azure.com/)에서 **연결 문자열**을 클릭합니다. 

    화면의 오른쪽에서 복사 단추를 사용하여 기본 연결 문자열을 복사합니다.

    ![연결 문자열 창에서 연결 문자열 보기 및 복사](./media/create-table-dotnet/connection-string.png)

2. Visual Studio에서 app.config 파일을 엽니다. 

3. 이 자습서에서는 저장소 에뮬레이터를 사용하지 않으므로 8줄에서 StorageConnectionString 주석 처리를 제거하고, 7줄에서 StorageConnectionString을 주석으로 처리합니다. 이제 7줄과 8줄은 다음과 같이 표시됩니다.

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. 8줄에서 StorageConnectionString 값에 포털의 기본 연결 문자열 값을 붙여넣습니다. 따옴표 내에 문자열을 붙여넣습니다.
   
    > [!IMPORTANT]
    > 끝점에서 documents.azure.com을 사용하면 미리 보기 계정이 있다는 것을 의미하고 일반 공급 Table API SDK를 사용하려면 [새 Table API 계정](#create-a-database-account)을 만들어야 합니다. 
    >

    8줄은 다음과 같이 표시됩니다.

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. app.config 파일을 저장합니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

## <a name="azure-cosmos-db-capabilities"></a>Azure DB Cosmos 기능
Azure Cosmos DB는 Azure Table 저장소 API에서 사용할 수 없는 기능을 다양하게 지원합니다. 

어떤 기능은 연결 정책 및 일관성 수준을 지정할 수 있도록 하는 CreateCloudTableClient에 대한 새 오버로드를 통해 액세스합니다.

| 테이블 연결 설정 | 설명 |
| --- | --- |
| 연결 모드  | Azure Cosmos DB는 두 가지 연결 모드를 지원합니다. `Gateway` 모드에서는 항상 Azure Cosmos DB 게이트웨이로 요청을 보내고, Azure Cosmos DB 게이트웨이는 해당 데이터 파티션으로 전달합니다. `Direct` 연결 모드에서는 클라이언트에서 테이블에 대한 매핑을 파티션으로 인출하고 데이터 파티션에 대해 직접 요청합니다. 기본값인 `Direct`를 사용하는 것이 좋습니다.  |
| 연결 프로토콜 | Azure Cosmos DB는 두 가지 연결 프로토콜, 즉 `Https`과 `Tcp`를 지원합니다. 더 간단하기 때문에 기본값인 `Tcp`를 사용하는 것이 좋습니다. |
| 기본 위치 | 읽기에 대해 기본 설정된(멀티 홈) 위치를 쉼표로 구분한 목록입니다. Azure Cosmos DB 계정은 각각 1-30개 이상의 지역과 연결할 수 있습니다. 각 클라이언트 인스턴스는 대기 시간이 짧은 읽기에 대해 기본 설정된 순서대로 이러한 지역의 하위 집합을 지정할 수 있습니다. 지역 이름은 [표시 이름](https://msdn.microsoft.com/library/azure/gg441293.aspx)(예: `West US`)을 사용하여 지정해야 합니다. [멀티 호밍 API](tutorial-global-distribution-table.md)도 참조하세요. |
| 일관성 수준 | 잘 정의된 5가지 일관성 수준, 즉 `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` 및 `Eventual` 중에서 선택하여 대기 시간, 일관성 및 가용성 간에 균형을 유지할 수 있습니다. 기본값은 `Session`입니다. 선택하는 일관성 수준에 따라 다중 지역 설정에 상당한 성능 차이가 있습니다. 자세한 내용은 [일관성 수준](consistency-levels.md)을 참조하세요. |

다른 기능은 다음 `appSettings` 구성 값을 통해 사용하도록 설정할 수 있습니다.

| 키 | 설명 |
| --- | --- |
| TableQueryMaxItemCount | 단일 왕복에서 테이블 쿼리당 반환되는 최대 항목 수를 구성합니다. 기본값은 `-1`이며, Azure Cosmos DB에서 런타임에 값을 동적으로 결정할 수 있게 합니다. |
| TableQueryEnableScan | 쿼리에서 모든 필터에 대해 인덱스를 사용할 수 없더라도 스캔을 통해 인덱싱을 실행합니다. 기본값은 `false`입니다.|
| TableQueryMaxDegreeOfParallelism | 파티션 간 쿼리 실행에 대한 병렬 처리 수준입니다. `0`은 프리페치(pre-fetch)가 없는 직렬 처리이고, `1`는 프리페치가 있는 직렬 처리이며, 값이 높을수록 병렬 처리 속도가 빨라집니다. 기본값은 `-1`이며, Azure Cosmos DB에서 런타임에 값을 동적으로 결정할 수 있게 합니다. |

기본값을 변경하려면 Visual Studio의 [솔루션 탐색기]에서 `app.config` 파일을 엽니다. 아래와 같이 `<appSettings>` 요소의 콘텐츠를 추가합니다. `account-name`을 저장소 계정의 이름으로 바꾸고, `account-key`를 계정 액세스 키로 바꿉니다. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

앱에서 어떤 상황이 발생하고 있는지 빠르게 살펴보겠습니다. `Program.cs` 파일을 열면 이러한 코드 줄에서 Table 리소스를 만드는 것을 알 수 있습니다. 

## <a name="create-the-table-client"></a>테이블 클라이언트 만들기
`CloudTableClient`를 초기화하여 테이블 계정에 연결합니다.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
이 클라이언트는 앱 설정에 지정된 경우 `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` 및 `TablePreferredLocations` 구성 값을 사용하여 초기화됩니다.

## <a name="create-a-table"></a>테이블 만들기
그런 다음 `CloudTable`을 사용하여 테이블을 만듭니다. Azure Cosmos DB의 테이블은 저장소 및 처리량 측면에서 독립적으로 확장할 수 있으며, 분할은 서비스에서 자동으로 처리됩니다. Azure Cosmos DB는 고정된 크기 및 무제한 테이블을 모두 지원합니다. 자세한 내용은 [Azure Cosmos DB에서 분할](partition-data.md)을 참조하세요. 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

테이블을 만드는 방법에는 중요한 차이가 있습니다. Azure Cosmos DB는 트랜잭션에 대한 Azure 저장소의 사용량 기반 모델과 달리 처리량을 예약합니다. 처리량은 전용/예약되어 있으므로 요청 속도가 프로비전된 처리량 이하인 경우에는 절대로 제한할 수 없습니다

CreateIfNotExists의 매개 변수로 포함하여 기본 처리량을 구성할 수 있습니다.

1KB 엔터티의 읽기는 1RU로 정규화되며, 다른 작업은 CPU, 메모리 및 IOPS 사용량에 따라 고정된 RU 값으로 정규화됩니다. [Azure Cosmos DB의 요청 단위](request-units.md) 및 특히 [키 값 저장소](key-value-store-cost.md)에 대해 자세히 알아보세요.

다음으로 Azure Table 저장소 SDK를 사용한 간단한 읽기 및 쓰기(CRUD) 작업을 단계별로 안내합니다. 이 자습서에서는 Azure Cosmos DB에서 제공하는 예측 가능한 짧은 대기 시간(한 자리 수의 밀리초) 및 빠른 쿼리를 보여 줍니다.

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가
Azure Table 저장소의 엔터티는 `TableEntity` 클래스에서 확장되며 `PartitionKey` 및 `RowKey` 속성을 가지고 있어야 합니다. 다음은 고객 엔터티에 대한 샘플 정의입니다.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

다음 코드 조각에서는 Azure 저장소 SDK에 포함된 엔터티를 삽입하는 방법을 보여 줍니다. Azure Cosmos DB는 전 세계의 어떤 규모에서나 짧은 대기 시간을 보장하도록 설계되었습니다.

Azure Cosmos DB 계정과 동일한 지역에서 실행되는 응용 프로그램의 경우 쓰기 작업은 p99에서 15ms 이내, p50에서 6ms까지 완료됩니다. 그리고 이 기간 동안 쓰기 작업이 동기적으로 복제되고, 영구적으로 커밋되며, 모든 콘텐츠가 인덱싱된 후에만 클라이언트에서 다시 승인됩니다.


```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>엔터티 일괄 삽입
Azure Table Storage는 동일한 일괄 처리 작업으로 업데이트, 삭제 및 삽입을 결합할 수 있는 일괄 처리 작업 API를 지원합니다.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>단일 엔터티 검색
Azure Cosmos DB의 검색(GET)은 p99에서 10ms 이내, 동일한 Azure 지역의 검색은 p50에서 1ms까지 완료됩니다. 대기 시간이 짧은 읽기를 위해 계정에 많은 지역을 추가하고, `TablePreferredLocations`를 설정하여 로컬 지역("멀티 홈")에서 읽을 수 있도록 응용 프로그램을 배포할 수 있습니다. 

다음 코드 조각을 사용하여 단일 엔터티를 검색할 수 있습니다.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> [여러 지역을 사용하여 개발](tutorial-global-distribution-table.md)에서 멀티 호밍 API에 대해 알아보세요.
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>자동 보조 인덱스를 사용하여 엔터티 쿼리
테이블은 `TableQuery` 클래스를 사용하여 쿼리할 수 있습니다. Azure Cosmos DB에는 쓰기 액세스에 최적화된 데이터베이스 엔진이 있어 테이블 내의 모든 열을 자동으로 인덱싱합니다. Azure Cosmos DB의 인덱싱은 스키마와 관계 없이 수행됩니다. 따라서 행 간에 스키마가 다르더라도 시간이 지남에 따라 스키마가 진화하면 자동으로 인덱싱됩니다. Azure Cosmos DB는 자동 보조 인덱스를 지원하기 때문에 모든 속성에 대한 쿼리는 인덱스를 사용하여 효율적으로 처리될 수 있습니다.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

Azure Cosmos DB는 Table API에 대한 Azure Table 저장소와 동일한 쿼리 기능을 지원합니다. 또한 Azure Cosmos DB는 정렬, 집계, 지리 공간적 쿼리, 계층 구조 및 다양한 기본 제공 함수도 지원합니다. 추가 기능은 향후 서비스 업데이트의 Table API에서 제공됩니다. 이러한 기능에 대한 개요는 [Azure Cosmos DB 쿼리](sql-api-sql-query.md)를 참조하세요. 

## <a name="replace-an-entity"></a>엔터티 바꾸기
엔터티를 업데이트하려면 Table service에서 검색하고 엔터티 개체를 수정한 다음 변경 내용을 다시 Table service에 저장합니다. 다음 코드에서는 기존 고객의 전화 번호를 변경합니다. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
마찬가지로 `InsertOrMerge` 또는 `Merge` 작업을 수행할 수 있습니다.  

## <a name="delete-an-entity"></a>엔터티 삭제
엔터티를 검색한 후 엔터티 업데이트를 위해 표시된 것과 동일한 패턴을 사용하여 엔터티를 쉽게 삭제할 수 있습니다. 다음 코드는 고객 엔터티를 검색하고 삭제합니다.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>테이블 삭제
마지막으로, 다음 코드 예제에서는 저장소 계정에서 테이블을 삭제합니다. Azure Cosmos DB를 사용하여 테이블을 즉시 삭제하고 다시 만들 수 있습니다.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB를 Table API와 함께 사용하는 방법을 설명했으며 다음 작업을 수행했습니다. 

> [!div class="checklist"] 
> * Azure Cosmos DB 계정 만들기 
> * app.config 파일에서 기능을 사용하도록 설정 
> * 테이블 만들기 
> * 테이블에 엔터티 추가 
> * 엔터티 일괄 삽입 
> * 단일 엔터티 검색 
> * 자동 보조 인덱스를 사용하여 엔터티 쿼리 
> * 엔터티 바꾸기 
> * 엔터티 삭제 
> * 테이블 삭제  

이제 다음 자습서로 진행하여 테이블 데이터 쿼리에 대해 자세히 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
> [Table API를 사용하여 쿼리](tutorial-query-table.md)
