---
title: Azure Blob Storage에서 SQL Database로 데이터 복사 | Microsoft Docs
description: 이 자습서에서는 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 단계별 지침을 제공합니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: bfbafa2edb1d9195760a99f63113d28d3a978a78
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob에서 Azure SQL Database로 데이터 복사
이 자습서에서는 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 Data Factory 파이프라인을 만듭니다. 이 자습서의 구성 패턴은 파일 기반 데이터 저장소에서 관계형 데이터 저장소로 복사하는 데 적용됩니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.


이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 및 Azure SQL Database 연결된 서비스를 만듭니다.
> * Azure BLob 및 Azure SQL Database 데이터 집합을 만듭니다.
> * 복사 작업이 포함된 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

이 자습서에서는 .NET SDK를 사용합니다. 다른 메커니즘을 사용하여 Azure Data Factory와 상호 작용할 수 있습니다. 이 경우 "빠른 시작" 아래에 있는 샘플을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Storage 계정**. Blob 저장소를 **원본** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.
* **Azure SQL Database**. 데이터베이스를 **싱크** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure SQL Database를 만드는 단계는 [Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서를 참조하세요.
* **Visual Studio** 2015 또는 2017 이 문서의 연습에서는 Visual Studio 2017을 사용합니다.
* **[Azure .NET SDK](http://azure.microsoft.com/downloads/)를 다운로드하고 설치합니다**.
* [이 지침](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)에 따라 **Azure Active Directory에 응용 프로그램을 만듭니다**. 나중의 단계에서 사용하는 **응용 프로그램 ID**, **인증 키** 및 **테넌트 ID** 값을 적어 둡니다. 동일한 문서의 지침에 따라 응용 프로그램을 "**참가자**" 역할에 할당합니다

### <a name="create-a-blob-and-a-sql-table"></a>Blob 및 SQL 테이블 만들기

이제 다음 단계를 수행하여 자습서에서 사용할 Azure Blob 및 Azure SQL Database를 준비합니다.

#### <a name="create-a-source-blob"></a>원본 Blob 만들기

1. 메모장을 시작합니다. 다음 텍스트를 복사하여 **inputEmp.txt** 파일로 디스크에 저장합니다.

    ```
    John|Doe
    Jane|Doe
    ```

2. [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구를 사용하여 **adfv2tutorial** 컨테이너를 만들고 **inputEmp.txt** 파일을 이 컨테이너에 업로드합니다.

#### <a name="create-a-sink-sql-table"></a>싱크 SQL 테이블 만들기

1. 다음 SQL 스크립트를 사용하여 Azure SQL Database에 **dbo.emp** 테이블을 만듭니다.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Azure 서비스에서 SQL 서버에 액세스하도록 허용합니다. Data Factory 서비스에서 Azure SQL 서버에 데이터를 쓸 수 있도록 Azure SQL 서버에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 **켜기**인지 확인합니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

    1. 왼쪽의 **더 많은 서비스** 허브를 클릭하고 **SQL 서버**를 클릭합니다.
    2. 서버를 선택하고 **설정** 아래의 **방화벽**을 클릭합니다.
    3. **방화벽 설정** 페이지에서 **Azure 서비스에 대한 액세스 허용**에 대해 **켜기**를 클릭합니다.


## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio 2015/2017을 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.

1. **Visual Studio**를 시작합니다.
2. **File**을 클릭하고 **New**를 가리킨 다음 **프로젝트**를 클릭합니다.
3. 오른쪽의 프로젝트 형식 목록에서 **Visual C#** -> **콘솔 앱(.NET Framework)**을 차례로 선택합니다. .NET 버전 4.5.2 이상이 필요합니다.
4. 이름으로 **ADFv2Tutorial**을 입력합니다.
5. **확인**을 클릭하여 프로젝트를 만듭니다.

## <a name="install-nuget-packages"></a>NuGet 패키지 설치

1. **도구** -> **NuGet 패키지 관리자** -> **패키지 관리자 콘솔**을 클릭합니다.
2. **패키지 관리자 콘솔**에서 다음 명령을 실행하여 패키지를 설치합니다.

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>데이터 팩터리 클라이언트 만들기

1. **Program.cs**를 열고 다음 문을 포함하여 네임스페이스에 대한 참조를 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
2. 변수를 설정하는 **Main** 메서드에 다음 코드를 추가합니다. 자리 표시자를 사용자의 고유 값으로 바꿉니다. 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. **Main** 메서드에 **DataFactoryManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 집합 및 파이프라인을 만듭니다. 또한 이 개체를 사용하여 파이프라인 실행 세부 정보를 모니터링합니다.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

**Main** 메서드에 **데이터 팩터리**를 만드는 다음 코드를 추가합니다.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>연결된 서비스 만들기

이 자습서에서는 원본 및 싱크 각각에 대해 하나씩, 두 개의 연결된 서비스를 만듭니다.

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기

**Main** 메서드에 **Azure Storage 연결된 서비스**를 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보는 [Azure Blob 연결된 서비스 속성](connector-azure-blob-storage.md#linked-service-properties)에서 자세히 알아보세요.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기

**Main** 메서드에 **Azure SQL Database 연결된 서비스**를 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보는 [Azure SQL Database 연결된 서비스 속성](connector-azure-sql-database.md#linked-service-properties)에서 자세히 알아보세요.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>데이터 집합 만들기

이 섹션에서는 원본과 싱크 각각에 대해 하나씩, 두 개의 데이터 집합을 만듭니다. 

### <a name="create-a-dataset-for-source-azure-blob"></a>원본 Azure Blob에 대한 데이터 집합 만들기

**Main** 메서드에 **Azure Blob 데이터 집합**을 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보는 [Azure Blob 데이터 집합 속성](connector-azure-blob-storage.md#dataset-properties)에서 자세히 알아보세요.

Azure Blob의 원본 데이터를 나타내는 데이터 집합을 정의합니다. 이 Blob 데이터 집합은 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조하며 다음을 설명합니다.

- 복사할 Blob의 원본 위치: **FolderPath** 및 **FileName**
- 내용을 구문 분석하는 방법을 나타내는 Blob 형식: **TextFormat** 및 해당 설정(예: 열 구분 기호)
- 이 경우 싱크 SQL 테이블에 매핑하는 열 이름과 데이터 형식을 포함한 데이터 구조

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>싱크 Azure SQL Database에 대한 데이터 집합 만들기

**Main** 메서드에 **Azure SQL Database 데이터 집합**을 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보는 [Azure SQL Database 데이터 집합 속성](connector-azure-sql-database.md#dataset-properties)에 대해 자세히 알아보세요.

Azure SQL Database의 싱크 데이터를 나타내는 데이터 집합을 정의합니다. 이 데이터 집합은 이전 단계에서 만든 Azure SQL Database 연결된 서비스를 참조합니다. 또한 복사된 데이터를 보관하는 SQL 테이블을 지정합니다. 

```csharp
// Create a Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

**Main** 메서드에 **복사 작업이 있는 파이프라인**을 만드는 다음 코드를 추가합니다. 이 자습서에서는 이 파이프라인에 하나의 작업, 즉 Blob 데이터 집합을 원본으로, SQL 데이터 집합을 싱크로 사용하는 복사 작업이 포함됩니다. 복사 작업에 대한 자세한 내용은 [복사 작업 개요](copy-activity-overview.md)를 참조하세요.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

**Main** 메서드에 **파이프라인 실행**을 트리거하는 다음 코드를 추가합니다.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

1. **Main** 메서드에 다음 코드를 추가하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행의 상태를 검사합니다.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. **Main** 메서드에 복사 작업 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 코드를 추가합니다.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>코드 실행

응용 프로그램을 빌드하고 시작한 다음 파이프라인 실행을 확인합니다.

콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 집합, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 그런 다음 파이프라인 실행 상태를 확인합니다. 데이터를 읽고/쓴 크기가 있는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음 SSMS(SQL Server Management Studio) 또는 Visual Studio와 같은 도구를 사용하여 대상 Azure SQL Database에 연결하고 지정한 테이블에 데이터가 복사되었는지 확인합니다.

### <a name="sample-output"></a>샘플 출력

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 및 Azure SQL Database 연결된 서비스를 만듭니다.
> * Azure Blob 및 Azure SQL Database 데이터 집합을 만듭니다.
> * 복사 작업이 포함된 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.


온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 진행하세요. 

> [!div class="nextstepaction"]
>[온-프레미스에서 클라우드로 데이터 복사](tutorial-hybrid-copy-powershell.md)
