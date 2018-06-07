---
title: .NET을 사용하여 Azure Data Factory 만들기 | Microsoft Docs
description: Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사하는 Azure 데이터 팩터리를 만듭니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: c5b7af290a5e5c45d3f64ccb50586db0811dd592
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>.NET SDK를 사용하여 데이터 팩터리 및 파이프라인 만들기
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](quickstart-create-data-factory-dot-net.md)

이 빠른 시작에서는 .NET SDK를 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 **복사합니다**. Azure Data Factory를 사용하여 데이터를 **변환**하는 방법에 대한 자습서는 [자습서: Apache Spark를 사용하여 데이터 변환](transform-data-using-spark.md)을 참조하세요. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.
>
> 이 문서는 Data Factory 서비스의 자세한 소개를 제공하지 않습니다. Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

### <a name="azure-subscription"></a>Azure 구독
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

### <a name="azure-roles"></a>Azure 역할
Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다. Azure Portal에서 오른쪽 위 모서리에 있는 **사용자 이름**을 클릭한 다음 **권한**을 선택하면 구독에 따른 사용자 권한을 볼 수입니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 사용자를 역할에 추가하는 방법에 대한 샘플 지침은 [역할 추가](../billing/billing-add-change-azure-subscription-administrator.md) 문서를 참조하세요.

### <a name="azure-storage-account"></a>Azure Storage 계정
이 빠른 시작에서는 범용 Azure Storage 계정(특히 Blob Storage)을 **원본** 및 **대상** 데이터 저장소로 사용합니다. 범용 Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하여 새로 만듭니다. 

#### <a name="get-storage-account-name-and-account-key"></a>저장소 계정 이름 및 계정 키 가져오기
이 빠른 시작에서 Azure Storage 계정 이름 및 키를 사용합니다. 다음 프로시저에서는 저장소 계정 이름 및 키를 가져오는 단계를 제공합니다. 

1. 웹 브라우저를 시작하고 [Azure Portal](https://portal.azure.com)로 이동합니다. Azure 사용자 이름과 암호를 사용하여 로그인합니다. 
2. 왼쪽 메뉴에서 **더 많은 서비스>**를 클릭하고 **저장소** 키워드를 사용하여 필터링하고 **저장소 계정**을 선택합니다.

    ![저장소 계정 검색](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. 저장소 계정 목록에서 저장소 계정(필요한 경우)을 필터링한 다음 **저장소 계정**을 선택합니다. 
4. **저장소 계정** 페이지의 메뉴에서 **액세스 키**를 선택합니다.

    ![저장소 계정 이름 및 키 가져오기](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. **저장소 계정 이름** 및 **key1** 필드의 값을 클립보드에 복사합니다. 메모장이나 다른 편집기에 붙여넣고 저장합니다.  

#### <a name="create-input-folder-and-files"></a>입력 폴더 및 파일 만들기
이 섹션에서는 Azure Blob Storage에 **adftutorial**이라는 Blob 컨테이너를 만듭니다. 그런 다음 컨테이너에 **입력**이라는 폴더를 만든 다음 입력 폴더에 샘플 파일을 업로드합니다. 

1. **저장소 계정** 페이지에서 **개요**로 전환한 다음, **Blobs**을 클릭합니다. 

    ![Blob 옵션 선택](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. **Blob service** 페이지의 도구 모음에서 **+ 컨테이너**를 클릭합니다. 

    ![컨테이너 단추 추가](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. **새 컨테이너** 대화 상자에서 **adftutorial**을 이름으로 입력하고 **확인**을 클릭합니다. 

    ![컨테이너 이름 입력](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. 컨테이너 목록에서 **adftutorial**을 클릭합니다. 

    ![컨테이너를 선택합니다.](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. **컨테이너** 페이지의 도구 모음에서 **업로드**를 클릭합니다.  

    ![업로드 단추](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. **BLOB 업로드** 페이지에서 **고급**을 클릭합니다.

    ![고급 링크 클릭합니다.](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. **메모장**을 시작하고 다음과 같은 내용으로 **emp.txt**이라는 파일을 만듭니다: 그것을 **c:\ADFv2QuickStartPSH** 폴더에 저장합니다: **ADFv2QuickStartPSH** 폴더가 아직 없는 경우 새로 만듭니다.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Azure Portal의 **BLOB 업로드** 페이지에서 **파일** 필드에 대해 **emp.txt** 파일을 탐색 및 선택합니다. 
9. **입력**을 **폴더로 업로드** 필드 값으로 입력합니다. 

    ![BLOB 업로드 설정](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. 폴더는 **입력**이고 파일은 **emp.txt**인지 확인한 후 **업로드**를 클릭합니다.
11. 목록에서 **emp.txt** 파일 및 업로드 상태를 참조하세요. 
12. 모서리에 있는 **X**를 클릭하여 **BLOB 업로드** 페이지를 닫습니다. 

    ![BLOB 업로드 닫기](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. **컨테이너** 페이지를 열린 상태로 유지합니다. 이 빠른 시작의 끝부분에서 출력을 확인하는 데 사용합니다.

### <a name="visual-studio"></a>Visual Studio
이 문서의 연습에서는 Visual Studio 2017을 사용합니다. 또한 Visual Studio 2013 또는 2015를 사용할 수 있습니다.

### <a name="azure-net-sdk"></a>Azure .NET SDK
[Azure .NET SDK](http://azure.microsoft.com/downloads/)를 컴퓨터에 다운로드하여 설치합니다.

### <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory에서 응용 프로그램 만들기
[이 아티클](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)의 섹션에 있는 지침을 따라 다음과 같은 작업을 수행합니다. 

1. **Azure Active Directory 응용 프로그램을 만듭니다**. 이 자습서에서 만드는.NET 응용 프로그램을 나타내는 Microsoft Azure Active Directory에 응용 프로그램을 만듭니다. sign-on URL의 경우 (`https://contoso.org/exampleapp`)에 보이는 더미 URL을 제공할 수 있습니다.
2. **응용 프로그램 ID** 및 **인증 키**를 가져오고, 이 자습서의 뒷부분에서 사용하기 위해 이러한 값을 적어둡니다. 
3. **테넌트 ID**를 가져오고, 이 자습서의 뒷부분에서 사용하기 위해 이 값을 적어둡니다.
4. 응용 프로그램이 구독에 데이터 팩터리를 생성할 수 있도록 구독 수준에서 응용 프로그램을 **참여자** 역할에 할당합니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio 2013/2015/2017을 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.

1. **Visual Studio**를 시작합니다.
2. **File**을 클릭하고 **New**를 가리킨 다음 **프로젝트**를 클릭합니다.
3. 오른쪽의 프로젝트 형식 목록에서 **Visual C#** -> **콘솔 앱(.NET Framework)**을 차례로 선택합니다. .NET 버전 4.5.2 이상이 필요합니다.
4. 이름에 **ADFv2QuickStart**를 입력합니다.
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

1. **Program.cs**를 열고 다음 문을 포함시켜서 네임스페이스에 대한 참조를 추가합니다.

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

2. 변수를 설정하는 **Main** 메서드에 다음 코드를 추가합니다. 자리 표시자를 고유한 값으로 바꿉니다. 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
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
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
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

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

**Main** 메서드에 **Azure Storage 연결된 서비스**를 만드는 다음 코드를 추가합니다.

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 계산 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서는 복사 원본 및 싱크 저장소 모두에 대해 샘플의 “AzureStorageLinkedService”라는 하나의 Azure Storage 연결된 서비스를 만들기만 하면 됩니다.

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

## <a name="create-a-dataset"></a>데이터 집합 만들기

**Azure Blob Storage 데이터 집합**을 만드는 **Main** 메서드에 다음 코드를 추가합니다.

원본에서 싱크로 복사할 데이터를 나타내는 데이터 집합을 정의합니다. 이 예에서 이 Blob 데이터 집합은 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다. 데이터 집합은 값이 데이터 집합을 사용하는 활동에 설정되어 있는 매개 변수를 사용합니다. 매개 변수는 데이터가 상주/저장되는 위치를 가리키는 “folderPath”를 구성하는 데 사용됩니다.

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
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

**Main** 메서드에 **복사 작업이 있는 파이프라인**을 만드는 다음 코드를 추가합니다.

이 예제에서 이 파이프라인은 하나의 활동을 포함하고 입력 Blob 경로 및 출력 Blob 경로의 두 매개 변수를 사용합니다. 이러한 매개 변수의 값은 파이프라인이 트리거/실행될 때 설정됩니다. 복사 활동은 입력 및 출력 시 이전 단계에서 만든 동일한 Blob 데이터 집합을 참조합니다. 데이터 집합을 입력된 데이터 집합으로 사용하는 경우 입력된 경로가 지정됩니다. 또한 데이터 집합을 출력된 데이터 집합으로 사용하는 경우 출력된 경로가 지정됩니다. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

**Main** 메서드에 **파이프라인 실행**을 트리거하는 다음 코드를 추가합니다.

이 코드는 파이프라인에 지정된 **inputPath** 및 **outputPath** 매개 변수의 값을 원본 및 싱크 Blob 경로의 실제 값으로 설정합니다.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

1. **Main** 메서드에 다음 코드를 추가하여 데이터 복사가 완료될 때까지 지속적으로 상태를 검사합니다.

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
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>코드 실행

응용 프로그램을 빌드하고 시작한 다음 파이프라인 실행을 확인합니다.

콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 집합, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 그런 다음 파이프라인 실행 상태를 확인합니다. 데이터를 읽고/쓴 크기가 있는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 Blob이 변수에 지정한 대로 “inputBlobPath”에서 “outputBlobPath”로 복사되었는지 검사합니다.

### <a name="sample-output"></a>샘플 출력: 
```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>출력 확인
파이프라인은 자동으로 adftutorial Blob 컨테이너에서 출력 폴더를 만듭니다. 그런 다음 입력 폴더에서 출력 폴더로 emp.txt 파일을 복사합니다. 

1. Azure Portal의 **adftutorial** 컨테이너 페이지에서 출력 폴더를 보려면 **새로 고침**을 클릭합니다. 
    
    ![새로 고침](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. 폴더 목록에서 **출력**을 클릭합니다. 
2. **emp.txt**가 출력 폴더에 복사되었는지 확인합니다. 

    ![새로 고침](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>리소스 정리
데이터 팩터리를 프로그래밍 방식으로 삭제하려면 프로그램에 다음 코드 줄을 추가합니다. 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
