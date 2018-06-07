---
title: 테이블 저장소 및 Visual Studio 연결된 서비스 시작 방법(ASP.NET Core) | Microsoft Docs
description: Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio ASP.NET Core 프로젝트에서 Azure 테이블 저장소 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e53e8ed27cfc048f24bda4ef92fcd2a50a85ed07
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Azure 테이블 저장소 및 Visual Studio 연결된 서비스를 시작하는 방법

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

이 문서에서는 Visual Studio **연결된 서비스** 기능을 사용하여 ASP.NET Core 프로젝트에서 Azure 저장소 계정을 만들거나 참조한 후 Visual Studio에서 Azure Table Storage 사용을 시작하는 방법에 대해 설명합니다. **연결된 서비스** 작업은 프로젝트의 Azure Storage에 액세스하는 데 적합한 NuGet 패키지를 설치하고 프로젝트 구성 파일에 저장소 계정에 대한 연결 문자열을 추가합니다. (Azure Storage에 대한 일반적인 내용은 [저장소 설명서](https://azure.microsoft.com/documentation/services/storage/)를 참조하세요.)

Azure 테이블 저장소 서비스를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다. Azure 테이블 저장소를 사용하는 방법에 대한 자세한 내용은 [.NET을 사용하여 Azure 테이블 저장소 시작](../storage/storage-dotnet-how-to-use-tables.md)을 참조하세요.

시작하려면 먼저 저장소 계정에서 테이블을 만듭니다. 이 문서에서는 C#으로 테이블을 만드는 방법과 함께 테이블 항목 추가, 수정, 읽기 및 제거 같은 기본 큐 작업을 수행하는 방법을 설명합니다.  코드는 .NET용 Azure Storage 클라이언트 라이브러리를 사용합니다. ASP.NET에 대한 자세한 내용은 [ASP.NET(영문)](http://www.asp.net)을 참조하세요.

Azure Storage API 중 일부는 비동기적이며 이 문서의 코드는 비동기 메서드를 사용하는 것으로 가정합니다. 자세한 내용은 [비동기 프로그래밍](https://docs.microsoft.com/dotnet/csharp/async)을 참조하세요.

## <a name="access-tables-in-code"></a>코드에서 테이블 액세스하기

ASP.NET Core 프로젝트의 테이블에 액세스하려면 Azure 테이블 저장소에 액세스하는 C# 소스 파일에 다음 항목을 포함해야 합니다.

1. 필요한 `using` 문을 추가합니다.

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. 저장소 계정 정보를 나타내는 `CloudStorageAccount` 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다.

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 저장소 계정의 테이블 개체를 참조하려면 `CloudTableClient` 개체를 가져옵니다.

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 특정 테이블과 엔터티를 참조하려면 `CloudTable` 참조 개체를 가져옵니다.

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>코드에서 테이블 만들기

Azure 테이블을 만들려면 ``CreateIfNotExistsAsync()`를 호출합니다.

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

테이블에 엔터티를 추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 `CustomerEntity` 엔터티 클래스를 정의합니다.

```cs
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

엔터티와 관련된 테이블 작업은 이전에 [코드에서 테이블 액세스](#access-tables-in-code)에서 만든 `CloudTable` 개체를 사용합니다. `TableOperation` 개체는 수행할 작업을 나타냅니다. 다음 코드 예제에서는 `CloudTable` 개체 및 `CustomerEntity` 개체를 만드는 방법을 보여 줍니다. 작업을 준비하기 위해 고객 엔터티를 테이블에 삽입하는 `TableOperation`이 생성됩니다. 마지막으로, `CloudTable.ExecuteAsync`를 호출하여 작업이 실행됩니다.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>엔터티 일괄 삽입

하나의 쓰기 작업으로 테이블에 여러 엔터티를 삽입할 수 있습니다. 다음 코드 예제에서는 두 개의 엔터티 개체("Jeff Smith" 및 "Ben Smith")를 만들고 `Insert` 메서드를 사용하여 `TableBatchOperation` 개체에 이 두 개체를 추가한 다음 `CloudTable.ExecuteBatchAsync`를 호출하여 작업을 시작합니다.

```cs
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
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>파티션의 모든 엔터티 가져오기

테이블에서 파티션의 모든 엔터티를 쿼리하려면 `TableQuery` 개체를 사용합니다. 다음 코드 예제에서는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>단일 엔터티 가져오기

단일 특정 엔터티를 가져오는 쿼리를 작성할 수 있습니다. 다음 코드에서는 `TableOperation` 개체를 사용하여 'Ben Smith'라는 고객을 지정합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하며, `TableResult.Result`의 반환된 값은 `CustomerEntity` 개체입니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 `Table`에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>엔터티 삭제

엔터티를 찾은 후 삭제할 수 있습니다. 다음 코드는 "Ben Smith"라는 고객 엔터티를 찾아서 삭제합니다.

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
