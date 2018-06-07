---
title: Azure Data Factory 파이프라인 분기 | Microsoft Docs
description: 분기 및 연결 작업을 통해 Azure Data Factory에서 데이터 흐름을 제어하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: eec2b5f84d11c946c5cae1d7d90d0b96dacc9d8c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Data Factory 파이프라인 분기 및 연결 작업
이 자습서에서는 몇 가지 컨트롤 흐름 기능을 보여 주는 Data Factory 파이프라인을 만듭니다. 이 파이프라인은 Azure Blob Storage의 컨테이너에서 동일한 저장소 계정의 다른 컨테이너로 간단한 복사를 수행합니다. 복사 작업이 성공하면 성공 전자 메일로 성공적인 복사 작업에 대한 세부 정보(예: 기록된 데이터 양)를 보내려고 합니다. 복사 작업이 실패하면 실패 전자 메일로 실패한 복사 작업에 대한 세부 정보(예: 오류 메시지)를 보내려고 합니다. 자습서 전체에서 매개 변수를 전달하는 방법을 확인할 수 있습니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

대략적인 시나리오 개요: ![개요](media/tutorial-control-flow/overview.png)

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 연결된 서비스 만들기
> * Azure Blob 데이터 집합 만들기
> * 복사 작업 및 웹 작업이 포함된 파이프라인 만들기
> * 후속 작업에 작업 출력 보내기
> * 매개 변수 전달 및 시스템 변수 활용
> * 파이프라인 실행 시작
> * 파이프라인 및 작업 실행 모니터링

이 자습서에서는 .NET SDK를 사용합니다. 다른 메커니즘을 사용하여 Azure Data Factory와 상호 작용할 수 있습니다. 이 경우 목차에서 "빠른 시작"을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Storage 계정**. Blob 저장소를 **원본** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.
* **Azure SQL Database**. 데이터베이스를 **싱크** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure SQL Database를 만드는 단계는 [Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서를 참조하세요.
* **Visual Studio** 2013, 2015 또는 2017 - 이 문서의 연습에서는 Visual Studio 2017을 사용합니다.
* **[Azure .NET SDK](http://azure.microsoft.com/downloads/)를 다운로드 및 설치합니다**.
* [이 지침](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)에 따라 **Azure Active Directory에 응용 프로그램을 만듭니다**. 나중의 단계에서 사용하는 **응용 프로그램 ID**, **인증 키** 및 **테넌트 ID** 값을 적어 둡니다. 동일한 문서의 지침에 따라 응용 프로그램을 "**참가자**" 역할에 할당합니다

### <a name="create-blob-table"></a>Blob 테이블 만들기

1. 메모장을 시작합니다. 다음 텍스트를 복사하여 **input.txt** 파일로 디스크에 저장합니다.

    ```
    John|Doe
    Jane|Doe
    ```
2. [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구를 사용하여 **adfv2branch** 컨테이너를 만들고 **input.txt** 파일을 이 컨테이너에 업로드합니다.

## <a name="create-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio 2015/2017을 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.

1. **Visual Studio**를 시작합니다.
2. **File**을 클릭하고 **New**를 가리킨 다음 **프로젝트**를 클릭합니다. .NET 버전 4.5.2 이상이 필요합니다.
3. 오른쪽의 프로젝트 형식 목록에서 **Visual C#** -> **콘솔 앱(.NET Framework)**을 차례로 선택합니다.
4. 이름으로 **ADFv2BranchTutorial**을 입력합니다.
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

2. Add these static variables to the **Program class**. Replace place-holders with your own values. Currently, Data Factory V2 allows you to create data factories only in the East US, East US2, and West Europe regions. The data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
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
Program.cs 파일에 "CreateOrUpdateDataFactory" 함수를 만듭니다.

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



**Main** 메서드에 **데이터 팩터리**를 만드는 다음 코드를 추가합니다. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기
Program.cs 파일에 "StorageLinkedServiceDefinition" 함수를 만듭니다.

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```
**Main** 메서드에 **Azure Storage 연결된 서비스**를 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보는 [Azure Blob 연결된 서비스 속성](connector-azure-blob-storage.md#linked-service-properties)에서 자세히 알아보세요.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>데이터 집합 만들기

이 섹션에서는 원본과 싱크 각각에 대해 하나씩, 두 개의 데이터 집합을 만듭니다. 

### <a name="create-a-dataset-for-source-azure-blob"></a>원본 Azure Blob에 대한 데이터 집합 만들기
**Main** 메서드에 **Azure Blob 데이터 집합**을 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보는 [Azure Blob 데이터 집합 속성](connector-azure-blob-storage.md#dataset-properties)에서 자세히 알아보세요.

Azure Blob의 원본 데이터를 나타내는 데이터 집합을 정의합니다. 이 Blob 데이터 집합은 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조하며 다음을 설명합니다.

- 복사할 Blob의 원본 위치: **FolderPath** 및 **FileName**
- FolderPath에 대한 매개 변수 사용을 확인합니다. "sourceBlobContainer"는 매개 변수의 이름이고, 식은 파이프라인 실행에서 전달된 값으로 바뀝니다. 매개 변수를 정의하는 구문은 `@pipeline().parameters.<parameterName>`입니다.

Program.cs 파일에 "SourceBlobDatasetDefinition" 함수를 만듭니다.

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>싱크 Azure Blob에 대한 데이터 집합 만들기

Program.cs 파일에 "SourceBlobDatasetDefinition" 함수를 만듭니다.

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

**Main** 메서드에 Azure Blob 원본 및 싱크 데이터 집합을 모두 만드는 다음 코드를 추가합니다. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>C# 클래스 만들기: EmailRequest
C# 프로젝트에서 **EmailRequest**라는 클래스를 만듭니다. 이 클래스는 전자 메일을 보낼 때 파이프라인이 본문 요청에서 보내는 속성을 정의합니다. 이 자습서에서 파이프라인은 다음 네 가지 파이프라인 속성을 전자 메일로 보냅니다.

- **메시지**: 전자 메일의 본문입니다. 성공적인 복사의 경우 이 속성에는 실행 세부 정보(기록된 데이터 수)가 포함됩니다. 실패한 복사의 경우 이 속성에는 오류 세부 정보가 포함됩니다.
- **데이터 팩터리 이름**: 데이터 팩터리의 이름
- **파이프라인 이름**: 파이프라인의 이름
- **받는 사람**: 전달되는 매개 변수 이 속성은 전자 메일의 받는 사람을 지정합니다.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## <a name="create-email-workflow-endpoints"></a>전자 메일 워크플로 끝점 만들기
전자 메일 보내기를 트리거하려면 [Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 워크플로를 정의합니다. 논리 앱 워크플로를 만드는 방법에 대한 자세한 내용은 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 참조하세요. 

### <a name="success-email-workflow"></a>성공 전자 메일 워크플로 
`CopySuccessEmail`이라는 논리 앱 워크플로를 만듭니다. 워크플로 트리거를 `When an HTTP request is received`로 정의하고 `Office 365 Outlook – Send an email` 작업을 추가합니다.

![성공 전자 메일 워크플로](media/tutorial-control-flow/success-email-workflow.png)

요청 트리거의 경우 `Request Body JSON Schema`를 다음 JSON으로 채웁니다.

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
이는 이전 섹션에서 만든 **EmailRequest** 클래스와 일치합니다. 

요청은 Logic Apps 디자이너에서 다음과 같이 표시됩니다.

![Logic Apps 디자이너 - 요청](media/tutorial-control-flow/logic-app-designer-request.png)

**전자 메일 보내기** 작업의 경우 요청 본문 JSON 스키마에 전달된 속성을 활용하여 전자 메일의 형식을 지정하는 방법을 사용자 지정합니다. 다음은 예제입니다.

![Logic Apps 디자이너 - 전자 메일 작업 보내기](media/tutorial-control-flow/send-email-action.png)

성공 전자 메일 워크플로에 대한 HTTP Post 요청 URL을 적어 둡니다.

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>실패 전자 메일 워크플로 
**CopySuccessEmail**을 복제하고, **CopyFailEmail**에 대한 다른 Logic Apps 워크플로를 만듭니다. 요청 트리거에서 `Request Body JSON schema`은 동일합니다. 간단히 `Subject`와 같은 전자 메일의 형식만 변경하여 실패 전자 메일에 맞게 조정합니다. 다음은 예제입니다.

![Logic Apps 디자이너 - 실패 전자 메일 워크플로](media/tutorial-control-flow/fail-email-workflow.png)

실패 전자 메일 워크플로에 대한 HTTP Post 요청 URL을 적어 둡니다.

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

이제 두 개의 워크플로 URL이 있습니다.

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
Main 메서드에 복사 작업 및 dependsOn 속성이 있는 파이프라인을 만드는 다음 코드를 추가합니다. 이 자습서에서는 파이프라인에 하나의 작업, 즉 Blob 데이터 집합을 원본으로, 다른 Blob 데이터 집합을 싱크로 사용하는 복사 작업이 포함됩니다. 복사 작업이 성공하거나 실패하면 서로 다른 전자 메일 작업을 호출합니다.

이 파이프라인에서 사용하는 기능은 다음과 같습니다.

- 매개 변수
- 웹 작업
- 작업 종속성
- 작업의 출력을 후속 작업에 대한 입력으로 사용

다음 파이프라인 섹션을 섹션별로 분석해 보겠습니다.

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```
**Main** 메서드에 파이프라인을 만드는 다음 코드를 추가합니다.

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>매개 변수
파이프라인의 첫 번째 섹션에서는 매개 변수를 정의합니다. 

- sourceBlobContainer - 원본 Blob 데이터 집합에서 사용하는 파이프라인의 매개 변수입니다.
- sinkBlobContainer - 싱크 Blob 데이터 집합에서 사용하는 파이프라인의 매개 변수입니다.
- receiver – 이 매개 변수는 이메일 주소가 이 매개 변수에 의해 지정되는 받는 사람에게 성공 또는 실패 전자 메일을 보내는 파이프라인의 두 가지 웹 작업에 사용됩니다.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>웹 작업
웹 작업은 모든 REST 끝점에 대한 호출을 허용합니다. 작업에 대한 자세한 내용은 [웹 작업](control-flow-web-activity.md)을 참조하세요. 이 파이프라인은 웹 작업을 사용하여 Logic Apps 전자 메일 워크플로를 호출합니다. **CopySuccessEmail** 워크플로와 **CopyFailWorkFlow**를 호출하는 두 가지 웹 작업을 만듭니다.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
이에 따라 "URL" 속성에서 Logic Apps 워크플로의 요청 URL 끝점을 붙여넣습니다. "Body" 속성에서 "EmailRequest" 클래스의 인스턴스를 전달합니다. 전자 메일 요청에 포함되는 속성은 다음과 같습니다.

- 메시지 - `@{activity('CopyBlobtoBlob').output.dataWritten`의 값을 전달합니다. 이전 복사 작업의 속성에 액세스하고 dataWritten 값을 전달합니다. 실패의 경우 `@{activity('CopyBlobtoBlob').error.message` 대신 오류 출력을 전달합니다.
- 데이터 팩터리 이름 - `@{pipeline().DataFactory}`의 값을 전달합니다. 이 변수는 시스템 변수이며, 해당 데이터 팩터리 이름에 액세스할 수 있게 합니다. 시스템 변수 목록은 [시스템 변수](control-flow-system-variables.md) 문서를 참조하세요.
- 파이프라인 이름 - `@{pipeline().Pipeline}`의 값을 전달합니다. 이는 시스템 변수이기도 하므로 해당 파이프라인 액세스할 수 있게 합니다. 
- 받는 사람 - "@pipeline().parameters.receiver")의 값을 전달합니다. 파이프라인 매개 변수에 액세스합니다.
 
이 코드는 성공한 이전 복사 작업에 따라 새 작업 종속성을 만듭니다.

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기
**Main** 메서드에 **파이프라인 실행**을 트리거하는 다음 코드를 추가합니다.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main 클래스 
마지막 Main 메서드는 다음과 같이 표시됩니다. 파이프라인 실행을 트리거하는 프로그램을 빌드하고 실행합니다!

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>코드 실행
응용 프로그램을 빌드하고 시작한 다음 파이프라인 실행을 확인합니다.
콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 집합, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 그런 다음 파이프라인 실행 상태를 확인합니다. 데이터를 읽고/쓴 크기가 있는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음 Azure Storage 탐색기와 같은 도구를 사용하여 변수에 지정한 대로 Blob이 "inputBlobPath"에서 "outputBlobPath"로 복사되었는지 확인합니다.

**샘플 출력:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 연결된 서비스 만들기
> * Azure Blob 데이터 집합 만들기
> * 복사 작업 및 웹 작업이 포함된 파이프라인 만들기
> * 후속 작업에 작업 출력 보내기
> * 매개 변수 전달 및 시스템 변수 활용
> * 파이프라인 실행 시작
> * 파이프라인 및 작업 실행 모니터링

이제 개념 섹션으로 진행하여 Azure Data Factory에 대한 자세한 내용을 확인할 수 있습니다.
> [!div class="nextstepaction"]
>[파이프라인 및 활동](concepts-pipelines-activities.md)
