---
title: .NET을 사용하여 Azure 테이블 저장소 시작 | Microsoft Docs
description: Azure 테이블 저장소, NoSQL 데이터 저장소를 사용하여 클라우드에 구조화된 데이터를 저장합니다.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 9f8175742adc5c543b637ab69b3a9583f251da04
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360192"
---
# <a name="get-started-with-azure-table-storage-using-net"></a>.NET을 사용하여 Azure 테이블 저장소 시작
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage는 클라우드에 구조화된 NoSQL 데이터를 저장하는 서비스로, 스키마 없이 디자인된 키/특성 저장소를 제공합니다. 테이블 저장소는 스키마가 없기 때문에 응용 프로그램의 요구 사항이 변화함에 따라 데이터를 쉽게 적응시킬 수 있습니다. Table Storage 데이터에 대한 액세스는 많은 응용 프로그램 유형에 대해 빠르고 비용 효율적이며 비슷한 양의 데이터일 때 일반적으로 전통적인 SQL에 비해 비용이 매우 낮습니다.

Table Storage를 사용하여 웹 응용 프로그램의 사용자 데이터, 주소록, 장치 정보 및 서비스에 필요한 다른 유형의 메타데이터와 같은 유연한 데이터 집합을 저장할 수 있습니다. 테이블에 저장할 수 있는 엔터티 수에는 제한이 없으며, 저장소 계정에 포함할 수 있는 테이블의 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다.

### <a name="about-this-tutorial"></a>이 자습서 정보
이 자습서에서는 몇 가지 일반적인 Azure Table Storage 시나리오에서 [Microsoft Azure CosmosDB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)을 사용하는 방법을 보여 줍니다. 패키지의 이름은 Azure Cosmos DB에 사용할 것임을 의미하지만 이 패키지는 Azure Cosmos DB와 Azure Tables 저장소 모두에서 작동하며 각 서비스에 고유한 엔드포인트가 있습니다. 다음 방법을 설명하는 C# 예제를 통해 이러한 시나리오를 살펴보겠습니다.
* 테이블 만들기 및 삭제
* 행 삽입, 업데이트 및 삭제
* 쿼리 테이블

## <a name="prerequisites"></a>필수 조건

이 자습서를 성공적으로 완료하려면 다음이 필요합니다.

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage Common Library for .NET(미리 보기)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). 프로덕션 환경에서 지원되는 필수 미리 보기 패키지입니다. 
* [Microsoft Azure CosmosDB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [.NET용 Azure 구성 관리자](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure 저장소 계정](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>추가 샘플
Table Storage를 사용하는 추가 예제는 [.NET에서 Azure Table Storage 시작](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)을 참조하세요. GitHub에서 샘플 응용 프로그램을 다운로드하고 실행하거나 코드를 탐색할 수 있습니다.

## <a name="create-an-azure-service-account"></a>Azure 서비스 계정 만들기
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기
첫 번째 Azure Storage 계정을 가장 쉽게 만드는 방법은 [Azure Portal](https://portal.azure.com)을 사용하는 것입니다. 자세한 내용은 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하십시오.

[Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md) 또는 [.NET용 Storage 리소스 공급자 클라이언트 라이브러리](/dotnet/api/microsoft.azure.management.storage)를 사용하여 Azure Storage 계정을 만들 수도 있습니다.

또한 이번에 저장소 계정을 만들지 않으려는 경우 Azure 저장소 에뮬레이터를 사용하여 로컬 환경에서 코드를 실행하고 테스트할 수 있습니다. 자세한 내용은 [개발 및 테스트에 Azure Storage 에뮬레이터 사용](../storage/common/storage-use-emulator.md)을 참조하세요.

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API 계정 만들기
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정
다음으로 이 가이드에 코드 예제를 사용할 준비가 되도록 Visual Studio에서 개발 환경을 설정합니다.

### <a name="create-a-windows-console-application-project"></a>Windows 콘솔 응용 프로그램 프로젝트 만들기
Visual Studio에서 새로운 Windows 콘솔 응용 프로그램을 만듭니다. 다음 단계에서는 Visual Studio 2017에서 콘솔 응용 프로그램을 만드는 방법을 보여 줍니다. 이러한 단계는 다른 버전의 Visual Studio에서도 비슷합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
2. **설치됨** > **Visual C#** > **Windows 기본 바탕 화면**을 차례로 선택합니다.
3. **콘솔 앱(.NET Framework)** 을 선택합니다.
4. **이름:** 필드에서 응용 프로그램의 이름을 입력합니다.
5. **확인**을 선택합니다.

이 자습서의 모든 코드 예제는 콘솔 응용 프로그램에 있는 `Program.cs` 파일의 `Main()` 메서드에 추가될 수 있습니다.

Azure 클라우드 서비스, 웹앱, 바탕화면 및 모바일 응용 프로그램을 포함하여 .NET 응용 프로그램의 모든 형식에서 Azure CosmosDB 테이블 라이브러리를 사용할 수 있습니다. 이 가이드에서는 편의상 콘솔 응용 프로그램을 사용합니다.

### <a name="use-nuget-to-install-the-required-packages"></a>NuGet을 사용하여 필요한 패키지 설치
이 자습서를 완료하기 위해 프로젝트에서 참조해야 하는 세 개의 권장되는 패키지가 있습니다.

* [Azure Storage Common Library for .NET(미리 보기)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common) 
* [Microsoft Azure Cosmos DB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 이 패키지는 Azure Table Storage 계정 또는 Azure Cosmos DB 테이블 API 계정에서 데이터 리소스에 대한 프로그래밍 방식의 액세스를 제공합니다.
* [.NET용 Microsoft Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): 이 패키지는 응용 프로그램을 실행하는 위치에 관계없이 구성 파일에서 연결 문자열을 구문 분석하기 위한 클래스를 제공합니다.

NuGet을 사용하여 패키지를 모두 가져올 수 있습니다. 다음 단계를 수행하세요.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. 온라인에서 "Microsoft.Azure.Storage.Common"을 검색하고 **설치**를 선택하여 .NET용 Azure Storage 공통 라이브러리(미리 보기)와 해당 종속성을 설치합니다. 미리 보기 패키지이므로 **시험판 포함** 상자를 선택합니다.
3. 온라인에서 "Microsoft.Azure.CosmosDB.Table"을 검색하고 **설치**를 선택하여 Microsoft Azure CosmosDB 테이블 라이브러리를 설치합니다.
4. 온라인에서 "WindowsAzure.ConfigurationManager"를 검색하고 **설치**를 선택하여 Microsoft Azure Configuration Manager 라이브러리를 설치합니다.

> [!NOTE]
> Storage Common Library for .NET의 ODataLib 종속성은 WCF Data Services가 아니라 NuGet에서 사용 가능한 ODataLib 패키지에 의해 확인됩니다. ODataLib 라이브러리는 직접 다운로드하거나 NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. Storage 클라이언트 라이브러리에서 사용되는 특정 ODataLib 패키지는 [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) 및 [Spatial](http://nuget.org/packages/System.Spatial/)입니다. 이러한 라이브러리는 Azure Table Storage 클래스에서 사용하는 동안 Storage 공통 라이브러리를 통한 프로그래밍의 필수 종속성입니다.
> 
> 

> [!TIP]
> Azure 테이블 저장소에 이미 친숙한 개발자는 과거에 [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) 패키지를 사용했을 수도 있습니다. 모든 새 테이블 응용 프로그램은 [Azure Storage 공용 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 및 [Azure Cosmos DB 테이블 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)를 사용하는 것이 좋지만 WindowsAzure.Storage 패키지는 계속해서 지원됩니다. WindowsAzure.Storage 라이브러리를 사용하는 경우 using 문에 Microsoft.WindowsAzure.Storage.Table을 포함합니다.
>
>

### <a name="determine-your-target-environment"></a>대상 환경 확인
이 가이드의 예제를 실행하기 위해 다음과 같은 세 가지 환경 옵션이 있습니다.

* 클라우드에서 Azure Storage 계정에 대한 코드를 실행할 수 있습니다. 
* 클라우드에서 Azure Cosmos DB 계정에 대해 코드를 실행할 수 있습니다.
* Azure 저장소 에뮬레이터에 대해 코드를 실행할 수 있습니다. 저장소 에뮬레이터는 클라우드에서 Azure Storage 계정을 에뮬레이트하는 로컬 환경입니다. 에뮬레이터는 응용 프로그램을 개발하는 동안 코드의 테스트 및 디버깅하기 위한 무료 옵션입니다. 에뮬레이터는 잘 알려진 계정 및 키를 사용합니다. 자세한 내용은 [개발 및 테스트에 Azure Storage 에뮬레이터 사용](../storage/common/storage-use-emulator.md)을 참조하세요.

클라우드에서 저장소 계정을 대상으로 하는 경우 Azure Portal에서 저장소 계정에 대한 기본 선택키를 복사합니다. 자세한 내용은 [저장소 액세스 키 보기 및 복사](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys)를 참조하세요.

> [!NOTE]
> Azure Storage와 연결하여 비용이 초래되지 않도록 저장소 에뮬레이터를 대상으로 할 수 있습니다. 그러나 클라우드에서 Azure 저장소 계정을 대상으로 하도록 선택하는 경우 이 자습서를 수행하는 비용은 무시됩니다.
> 
> 

Azure Cosmos DB 계정을 대상으로 할 경우 Azure Portal에서 Table API 계정에 대한 기본 액세스 키를 복사합니다. 자세한 내용은 [연결 문자열 업데이트](create-table-dotnet.md#update-your-connection-string)를 참조하세요.

### <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성
Azure Storage Common Library for .NET은 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 엔드포인트 및 자격 증명을 구성하는 작업을 지원합니다. 저장소 연결 문자열을 유지하는 가장 좋은 방법은 구성 파일 안에 있습니다. 

연결 문자열에 대한 자세한 내용은 [Azure Storage에 연결 문자열 구성](../storage/common/storage-configure-connection-string.md)을 참조하세요.

> [!NOTE]
> 계정 키는 저장소 계정의 루트 암호와 비슷합니다. 항상 저장소 계정 키를 보호해야 합니다. 다른 사용자에게 배포하거나 하드 코딩하거나 다른 사람이 액세스할 수 있는 일반 텍스트 파일에 저장하지 마십시오. 손상되었다고 생각되면 Azure Portal을 사용하여 키를 다시 생성합니다.
> 
> 

연결 문자열을 구성하려면 Visual Studio의 솔루션 탐색기에서 `app.config` 파일을 엽니다. 아래와 같이 `<appSettings>` 요소의 콘텐츠를 추가합니다. `account-name`을 계정 이름으로, `account-key`를 계정 액세스 키로 바꿉니다.

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

예를 들어 Azure Storage 계정을 사용할 경우 구성 설정이 다음과 비슷하게 표시됩니다.

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Azure Cosmos DB 계정을 사용할 경우 구성 설정이 다음과 비슷하게 표시됩니다.

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

저장소 에뮬레이터를 대상으로 하려면 잘 알려진 계정 이름 및 키에 매핑되는 바로 가기를 사용할 수 있습니다. 이 경우에 연결 문자열 설정은 다음과 같습니다.

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>지시문을 사용하여 추가
다음 **using** 지시문을 `Program.cs` 파일 맨 위에 추가합니다.

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>연결 문자열 구문 분석
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Table service 클라이언트 만들기
[CloudTableClient][dotnet_CloudTableClient] 클래스를 사용하면 Table Storage에 저장된 테이블 및 엔터티를 검색할 수 있습니다. Table service 클라이언트를 만드는 한 가지 방법은 다음과 같습니다.

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

이제 데이터를 읽어 오고 테이블 저장소에 데이터를 기록하는 코드를 작성할 준비가 되었습니다.

## <a name="create-a-table"></a>테이블 만들기
이 예제에서는 테이블이 없는 경우 만드는 방법을 보여 줍니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가
엔터티는 [TableEntity][dotnet_TableEntity]에서 파생된 사용자 지정 클래스를 사용하여 C# 개체에 매핑됩니다. 테이블에 엔터티를 추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 엔터티 클래스를 정의합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있지만 다양한 파티션 키를 사용하면 병렬 작업 확장성이 커집니다. 테이블에 저장되는 엔터티는 예를 들어 [TableEntity][dotnet_TableEntity] 클래스에서 파생되는 지원되는 형식이어야 합니다. 테이블에 저장하려는 엔터티 속성은 해당 형식의 공용 속성이어야 하며 값의 가져오기 및 설정하기를 모두 지원해야 합니다. 또한 엔터티 형식은 *반드시* 매개 변수가 없는 생성자를 표시해야 합니다.

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

엔터티와 관련된 테이블 작업은 이전의 “테이블 만들기” 섹션에서 만든 [CloudTable][dotnet_CloudTable] 개체를 사용하여 수행됩니다. 수행할 작업은 [TableOperation][dotnet_TableOperation] 개체로 표시됩니다. 다음 코드 예제에서는 [CloudTable][dotnet_CloudTable] 개체, **CustomerEntity** 개체의 생성을 차례로 보여 줍니다. 작업을 준비하기 위해 고객 엔터티를 테이블에 삽입하는 [TableOperation][dotnet_TableOperation] 개체가 만들어집니다. 마지막으로, [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute]를 호출하여 작업이 실행됩니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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
하나의 쓰기 작업으로 테이블에 엔터티를 일괄 삽입할 수 있습니다. 일괄 작업에 대한 다른 참고 사항은 다음과 같습니다.

* 동일한 단일 일괄 작업에서 업데이트, 삭제 및 삽입을 수행할 수 있습니다.
* 단일 일괄 작업에 최대 100개의 엔터티가 포함될 수 있습니다.
* 단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.
* 쿼리를 일괄 작업으로 수행할 수 있지만 일괄 작업의 유일한 작업이어야 합니다.

다음 코드 예제에서는 엔터티 개체 두 개를 만들고 [Insert][dotnet_TableBatchOperation_Insert] 메서드를 사용하여 [TableBatchOperation][dotnet_TableBatchOperation]에 각 개체를 추가합니다. 그런 다음 [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch]가 호출되어 작업이 실행됩니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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

## <a name="retrieve-all-entities-in-a-partition"></a>파티션의 모든 엔터티 검색
테이블에서 파티션의 모든 엔터티를 쿼리하려면 [TableQuery][dotnet_TableQuery] 개체를 사용합니다. 다음 코드 예제에서는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>파티션의 엔터티 범위 검색
파티션의 모든 엔터티를 쿼리하지 않으려면 파티션 키 필터를 행 키 필터와 결합하여 범위를 지정할 수 있습니다. 다음 코드 예제에서는 두 개의 필터를 사용하여 행 키(이름)가 알파벳에서 'E'보다 앞에 오는 문자로 시작하는 'Smith' 파티션의 모든 엔터티를 가져온 다음 쿼리 결과를 출력합니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>단일 엔터티 검색
단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드에서는 [TableOperation][dotnet_TableOperation]을 사용하여 고객 'Ben Smith'를 지정합니다. 이 메서드는 컬렉션 대신 엔터티 하나만 반환하며, [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result]에서 반환되는 값은 **CustomerEntity** 개체입니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 Table service에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>엔터티 바꾸기
엔터티를 업데이트하려면 Table service에서 검색하고 엔터티 개체를 수정한 다음 변경 내용을 다시 Table service에 저장합니다. 다음 코드에서는 기존 고객의 전화 번호를 변경합니다. 이 코드에서는 [Insert][dotnet_TableOperation_Insert]를 호출하는 대신 [Replace][dotnet_TableOperation_Replace]를 사용합니다. [Replace][dotnet_TableOperation_Replace]를 실행하면 서버의 엔터티가 검색된 후 변경되어 작업이 실패하는 경우를 제외하고 서버에서 엔터티가 완전히 바뀝니다. 이러한 실패는 응용 프로그램이 검색 및 업데이트 사이에 다른 응용 프로그램 구성 요소에 의해 변경된 내용을 실수로 덮어쓰는 것을 방지합니다. 이 실패를 올바르게 처리하려면 엔터티를 다시 검색하고 변경한 다음(유효한 경우) 다른 [Replace][dotnet_TableOperation_Replace] 작업을 수행합니다. 다음 섹션에서는 이 동작을 재정의하는 방법을 보여 줍니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>엔터티 삽입 또는 바꾸기
서버에서 검색한 엔터티를 변경하면 [Replace][dotnet_TableOperation_Replace] 작업이 실패합니다. 또한 [Replace][dotnet_TableOperation_Replace] 작업이 성공하려면 먼저 서버에서 엔터티를 검색해야 합니다. 그러나 서버에 엔터티가 있는지 알지 못하며 저장된 현재 값이 부적절하여 업데이트로 모두 덮어써야 하는 경우도 있습니다. 이렇게 하려면 [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] 작업을 사용합니다. 이 작업은 마지막 업데이트가 수행된 시기에 관계없이 엔터티가 없는 경우 삽입하고, 엔터티가 있는 경우 바꿉니다.

다음 코드 예제에서는 'Fred Jones'에 대한 고객 엔터티가 만들어지고 'people' 테이블에 삽입됩니다. 다음으로, [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] 작업을 사용하여 동일한 파티션 키(Jones)와 행 키(Fred)를 사용하여 서버에 엔터티를 저장합니다. 이때 PhoneNumber 속성으로 다른 값을 포함합니다. [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]를 사용하므로 모든 해당 속성 값이 바뀝니다. 하지만 'Fred Jones' 엔터티가 테이블에 아직 존재하지 않으면 삽입되었을 것입니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 쿼리
테이블 쿼리는 모든 엔터티 속성 대신 엔터티에서 몇 개의 속성만 검색할 수 있습니다. 프로젝션이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. 다음 코드의 쿼리는 테이블에 있는 엔터티의 전자 메일 주소만 반환합니다. 이렇게 하려면 [DynamicTableEntity][dotnet_DynamicTableEntity]의 쿼리와 [EntityResolver][dotnet_EntityResolver]를 사용합니다. [Upsert 및 쿼리 프로젝션 소개 블로그 게시물][blog_post_upsert]에서 프로젝션에 대한 자세한 내용을 참조하세요. 저장소 에뮬레이터에서는 프로젝션이 지원되지 않으므로 이 코드는 Table service의 계정을 사용하는 경우에만 실행됩니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>엔터티 삭제
엔터티를 검색한 후 엔터티 업데이트를 위해 표시된 것과 동일한 패턴을 사용하여 엔터티를 쉽게 삭제할 수 있습니다. 다음 코드는 고객 엔터티를 검색하고 삭제합니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>테이블 삭제
마지막으로, 다음 코드 예제에서는 저장소 계정에서 테이블을 삭제합니다. 삭제된 테이블은 삭제 후 일정 기간 동안 다시 만들 수 없습니다.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>페이지에서 엔터티를 비동기적으로 검색
많은 수의 엔터티를 읽고 있는데 모든 엔터티가 반환될 때까지 기다리지 않고 엔터티가 검색되는 동안 프로세스/표시하고자 한다면 분할된 쿼리를 사용하여 엔터티를 검색할 수 있습니다. 이 예제에서는 여러 페이지에서 Async-Await 패턴을 사용하여 결과를 반환하는 방법을 보여 주므로 큰 결과 집합이 반환되도록 기다리는 동안 실행이 차단되지 않습니다. .NET에서 Async-Await 패턴의 사용에 대한 자세한 내용은 [Async 및 Await(C# 및 Visual Basic)를 사용한 비동기 프로그래밍](https://msdn.microsoft.com/library/hh191443.aspx)을 참조하세요.

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>다음 단계
이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업에 대해 알아보세요.

* [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, MacOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
* [.NET에서 Azure Table Storage 시작](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* 사용 가능한 API에 대한 자세한 내용은 Table service 참조 설명서를 참조하세요.
* [Storage Client Library for .NET 참조](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [REST API 참조](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
* [.NET을 사용하여 Azure Blob 저장소를 시작](../storage/blobs/storage-dotnet-how-to-use-blobs.md) 하여 구조화되지 않은 데이터를 저장합니다.
* [.NET(C#)을 사용하여 SQL Database에 연결](../sql-database/sql-database-develop-dotnet-simple.md)하여 관계형 데이터를 저장합니다.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
