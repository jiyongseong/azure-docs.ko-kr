---
title: 자습서 - .NET용 Azure Batch 클라이언트 라이브러리 사용 | Microsoft Docs
description: Azure Batch의 기본 개념을 알아보고 .NET을 사용하여 간단한 솔루션을 빌드합니다.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbd96187ad73a9c7e27b28f137e25fe66e2944ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>.NET용 Batch 클라이언트 라이브러리를 사용한 솔루션 빌드 시작

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

이 문서에서는 C# 응용 프로그램 예제를 단계별로 설명하면서 [Azure Batch][azure_batch] 및 [Batch .NET][net_api] 라이브러리의 기본에 대해 알아봅니다. 샘플 응용 프로그램에서 Batch 서비스를 활용하여 클라우드에서 병렬 워크로드를 처리하는 방법과 파일 준비 및 검색을 위해 [Azure Storage](../storage/common/storage-introduction.md)와 상호 작용하는 방식을 살펴봅니다. 일반적인 Batch 응용 프로그램 워크플로를 습득하고 작업, 태스크, 풀 및 계산 노드와 같은 Batch의 주요 구성 요소에 대해 이해합니다.

![Batch 솔루션 워크플로(기본)][11]<br/>

## <a name="prerequisites"></a>필수 조건
이 문서에는 사용자에게 C# 및 Visual Studio에 대한 실용적인 지식이 있다고 가정합니다. 또한 Azure와 Batch 및 Storage 서비스에 대해 아래 지정된 계정 생성 요구 사항을 충족할 수 있다고 가정합니다.

### <a name="accounts"></a>계정
* **Azure 계정**: Azure 구독이 아직 없는 경우 [무료 Azure 계정][azure_free_account]을 만듭니다.
* **Batch 계정**: Azure 구독이 있으면 [Azure Batch 계정을 만듭니다](batch-account-create-portal.md).
* **저장소 계정**: Batch의 저장소 계정 옵션은 [Batch 기능 개요](batch-api-basics.md#azure-storage-account)를 참조하세요.


### <a name="visual-studio"></a>Visual Studio
샘플 프로젝트를 빌드하려면 **Visual Studio 2015 이상**이 있어야 합니다. [Visual Studio 제품 개요][visual_studio]에서 Visual Studio의 무료 및 평가판 버전을 찾을 수 있습니다.

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial* 코드 샘플
[DotNetTutorial][github_dotnettutorial] 샘플은 GitHub의 [azure-batch-samples][github_samples] 리포지토리에서 찾은 많은 Batch 코드 샘플 중 하나입니다. 리포지토리 홈 페이지에서 **복제 또는 다운로드 > ZIP 다운로드** 단추를 클릭하거나 [azure-batch-samples-master.zip][github_samples_zip] 직접 다운로드 링크를 클릭하여 모든 샘플을 다운로드할 수 있습니다. ZIP 파일의 내용을 추출하면 다음 폴더에서 솔루션을 찾을 수 있습니다.

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="batchlabs-optional"></a>BatchLabs(선택 사항)
[BatchLabs][github_batchlabs]는 Azure Batch 응용 프로그램을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. 이 자습서를 완료하는 것이 필수는 아니지만 Batch 솔루션을 개발하고 디버깅하는 과정에서 유용할 수 있습니다.

## <a name="dotnettutorial-sample-project-overview"></a>DotNetTutorial 샘플 프로젝트 개요
*DotNetTutorial* 코드 샘플은 두 프로젝트 **DotNetTutorial** 및 **TaskApplication**으로 구성된 Visual Studio 솔루션입니다.

* **DotNetTutorial**은 계산 노드(가상 머신)에서 병렬 워크로드를 실행하기 위해 Batch 및 Storage 서비스와 상호 작용하는 클라이언트 응용 프로그램입니다. DotNetTutorial은 로컬 워크스테이션에서 실행합니다.
* **TaskApplication**은 실제 작업을 수행하기 위해 Azure의 계산 노드에서 실행하는 프로그램입니다. 이 예제에서 `TaskApplication.exe`는 Azure Storage에서 다운로드한 파일(입력 파일)의 텍스트를 구문 분석합니다. 그 후 입력 파일에 표시되는 맨 위 단어 세 개를 포함하는 텍스트 파일(출력 파일)을 생성합니다. 출력 파일을 만든 후에 TaskApplication이 Azure Storage에 파일을 업로드합니다. 이렇게 하면 클라이언트 응용 프로그램에서 다운로드가 가능해 집니다. TaskApplication은 Batch 서비스의 여러 계산 노드에서 병렬로 실행합니다.

다음 다이어그램은 클라이언트 응용 프로그램, *DotNetTutorial*, 태스크에서 실행하는 응용 프로그램, *TaskApplication*에서 수행되는 기본 작업을 보여 줍니다. 이러한 기본 워크플로는 Batch를 사용하여 만드는 많은 계산 솔루션의 일반적인 형태입니다. Batch 서비스에서 사용 가능한 모든 기능을 보여 주지 않지만 거의 모든 Batch 시나리오는 이 워크플로의 일부를 포함합니다.

![Batch 예제 워크플로][8]<br/>

[**1단계.**](#step-1-create-storage-containers) Azure Blob Storage에 **컨테이너**를 만듭니다.<br/>
[**2단계.**](#step-2-upload-task-application-and-data-files) 컨테이너에 태스크 응용 프로그램 파일 및 입력 파일을 업로드합니다.<br/>
[**3단계.**](#step-3-create-batch-pool) Batch **풀**을 만듭니다.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** **StartTask** 풀은 노드가 풀에 연결되면 태스크 이진 파일(TaskApplication)을 노드에 다운로드합니다.<br/>
[**4단계.**](#step-4-create-batch-job) Batch **작업**을 만듭니다.<br/>
[**5단계.**](#step-5-add-tasks-to-job) 작업에 **태스크**를 추가합니다.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** 태스크는 노드에서 실행되도록 예약됩니다.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** 각 태스크는 Azure Storage에서 입력 데이터를 다운로드한 다음 실행을 시작합니다.<br/>
[**6단계.**](#step-6-monitor-tasks) 태스크를 모니터링합니다.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** 태스크가 완료되면 출력 데이터를 Azure Storage에 업로드합니다.<br/>
[**7단계.**](#step-7-download-task-output) 저장소에서 태스크 출력을 다운로드합니다.

언급한 바와 같이, 모든 Batch 솔루션이 정확히 이러한 단계를 수행하는 것은 아니며, 훨씬 더 많은 단계를 포함할 수 있지만, *DotNetTutorial* 샘플 응용 프로그램은 Batch 솔루션에서 찾을 수 있는 일반적인 프로세스를 보여줍니다.

## <a name="build-the-dotnettutorial-sample-project"></a>*DotNetTutorial* 샘플 프로젝트 빌드
샘플을 성공적으로 실행할 수 있으려면, *DotNetTutorial* 프로젝트의 `Program.cs` 파일에 Batch 및 Storage 계정 자격 증명을 지정해야 합니다. 아직 수행하지 않은 경우 `DotNetTutorial.sln` 솔루션 파일을 두 번 클릭하여 Visual Studio에서 솔루션을 엽니다. 또는 **파일 > 열기 > 프로젝트/솔루션** 메뉴를 사용하여 Visual Studio 내에서 솔루션을 엽니다.

*DotNetTutorial* 프로젝트 내에서 `Program.cs`를 엽니다. 파일의 위쪽에 지정된 대로 자격 증명을 추가합니다.

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

[Azure Portal][azure_portal]의 각 서비스의 계정 블레이드 내에서 Batch 및 Storage 계정 자격 증명을 찾을 수 있습니다.

![포털에서 자격 증명 Batch][9]
![포털의 Storage 자격 증명][10]<br/>

자격 증명으로 프로젝트를 업데이트했으므로 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **솔루션 빌드**를 클릭합니다. 메시지가 표시되면 모든 NuGet 패키지 복원을 확인합니다.

> [!TIP]
> NuGet 패키지가 자동으로 복원되지 않거나 패키지 복원 실패와 관련된 오류가 표시되는 경우 [NuGet 패키지 관리자][nuget_packagemgr]가 설치되었는지 확인합니다. 그런 다음 누락된 패키지의 다운로드를 활성화합니다. 패키지 다운로드를 활성화하려면 [빌드하는 동안 패키지 복원 활성화][nuget_restore]를 참조하세요.
>
>

다음 섹션에서는 샘플 응용 프로그램을 Batch 서비스에서 워크로드를 처리하기 위해 수행하는 단계로 세분화하고 이러한 단계를 자세히 설명합니다. 샘플에 있는 코드의 모든 줄이 설명되어 있지 않으므로 이 문서의 나머지 부분에서 작업하는 동안 Visual Studio에서 공개 솔루션을 참조하는 것이 좋습니다.

*DotNetTutorial* 프로젝트의 `Program.cs` 파일에서 `MainAsync` 메서드의 맨 위로 이동하여 단계1부터 시작합니다. 아래 각 단계 후 `MainAsync`에서 메서드 호출의 진행을 따릅니다.

## <a name="step-1-create-storage-containers"></a>1단계: Storage 컨테이너 만들기
![Azure Storage에 컨테이너 만들기][1]
<br/>

배치에는 Azure 저장소와의 상호 작용을 위해 기본 제공되는 지원이 포함됩니다. Storage 계정 내의 컨테이너는 Batch 계정에서 실행되는 태스크에 필요한 파일을 제공합니다. 컨테이너는 태스크가 생성하는 출력 데이터를 저장할 공간도 제공합니다. *DotNetTutorial* 클라이언트 응용 프로그램이 실행하는 첫 번째 작업은 [Azure Blob Storage](../storage/common/storage-introduction.md)에 세 개의 컨테이너를 만드는 것입니다.

* **응용 프로그램**: 이 컨테이너는 태스크가 실행하는 응용 프로그램은 물론 DLL과 같은 모든 종속 파일을 저장합니다.
* **입력**: 태스크가 *입력* 컨테이너에서 처리할 데이터 파일을 다운로드합니다.
* **출력**: 태스크가 입력 파일의 처리를 완료하면 그 결과를 *출력* 컨테이너에 업로드합니다.

Storage 계정과 상호 작용하고 컨테이너를 만들기 위해 [.NET용 Azure Storage 클라이언트 라이브러리][net_api_storage]를 사용합니다. [CloudStorageAccount][net_cloudstorageaccount]로 계정에 대한 참조를 만들고, 그 참조에서 [CloudBlobClient][net_cloudblobclient]를 만듭니다.

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

응용 프로그램 전체적으로 `blobClient` 참조를 사용하여 매개 변수로 많은 메서드에 전달합니다. 이 예는 위를 즉시 따르는 코드 블록에 있으며 여기에서 `CreateContainerIfNotExistAsync`를 호출하여 실제로 컨테이너를 만듭니다.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

컨테이너를 만든 후 이제 응용 프로그램은 작업에 의해 사용될 파일을 업로드할 수 있습니다.

> [!TIP]
> [.NET에서 Blob Storage를 사용하는 방법](../storage/blobs/storage-dotnet-how-to-use-blobs.md)에서는 Azure Storage 컨테이너 및 Blob을 사용한 작업의 개요를 제공합니다. Batch 작업 시작 단계 읽기 목록의 거의 위쪽에 있습니다.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>2단계: 작업 응용 프로그램 및 데이터 파일 업로드
![컨테이너에 작업 응용 프로그램 및 입력(데이터) 파일 업로드][2]
<br/>

파일 업로드 작업에서 *DotNetTutorial*은 먼저 로컬 컴퓨터에 있는 **응용 프로그램**의 컬렉션 및 **입력** 파일 경로를 지정합니다. 그런 다음 이전 단계에서 만든 컨테이너에 이러한 파일을 업로드합니다.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

업로드 프로세스에 관련된 `Program.cs` 에는 두 가지 메서드가 있습니다.

* `UploadFilesToContainerAsync`: 이 메서드는 [ResourceFile][net_resourcefile] 개체(아래 설명 참조)의 컬렉션을 반환하고 내부적으로 `UploadFileToContainerAsync`을 호출하여 *filePaths* 매개 변수에 전달된 각 파일을 업로드합니다.
* `UploadFileToContainerAsync`: 실제로 파일 업로드를 수행하고 [ResourceFile][net_resourcefile] 개체를 만드는 메서드입니다. 파일 업로드 후 파일에 대한 SAS(공유 액세스 서명)을 가져오고 이를 나타내는 ResourceFile 개체를 반환합니다. 공유 액세스 서명도 아래에 설명되어 있습니다.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
[ResourceFile][net_resourcefile]은 해당 작업을 실행하기 전에 계산 노드에 다운로드되는 Azure Storage의 파일에 대한 URL로 Batch 작업을 제공합니다. [ResourceFile.BlobSource][net_resourcefile_blobsource] 속성은 Azure Storage에 있는 파일의 전체 URL을 지정합니다. URL에는 파일에 대한 보안 액세스를 제공하는 SAS(공유 액세스 서명)가 포함될 수 있습니다. Batch .NET 내에서 대부분의 작업 형식은 다음을 포함하는 *ResourceFiles* 속성을 포함합니다.

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

DotNetTutorial 샘플 응용 프로그램은 JobPreparationTask 또는 JobReleaseTask 태스크 유형을 사용하지 않지만 [Azure Batch 계산 노드에서 작업 준비와 완료 태스크 실행](batch-job-prep-release.md)에서 이에 대해 자세히 알아볼 수 있습니다.

### <a name="shared-access-signature-sas"></a>공유 액세스 서명(SAS)
공유 액세스 서명은 URL의 일부분으로 포함되는 경우 Azure Storage의 컨테이너 및 Blob에 대한 보안 액세스를 제공하는 문자열입니다. DotNetTutorial 응용 프로그램은 Blob 및 컨테이너 공유 액세스 서명 URL 모두를 사용하고 Storage 서비스에서 이러한 공유 액세스 서명 문자열을 가져오는 방법을 보여 줍니다.

* **Blob 공유 액세스 서명**: DotNetTutorial에서 풀의 StartTask는 Storage에서 응용 프로그램 이진 및 입력 데이터 파일을 다운로드하는 경우 Blob 공유 액세스 서명을 사용합니다(아래 #3단계 참조). DotNetTutorial의 `Program.cs`에서 `UploadFileToContainerAsync` 메서드는 각 Blob의 공유 액세스 서명을 가져오는 코드를 포함합니다. [CloudBlob.GetSharedAccessSignature][net_sas_blob]를 호출하여 작업을 수행합니다.
* **컨테이너 공유 액세스 서명**: 각 태스크는 계산 노드에서 작업을 완료하면 해당 출력 파일을 Azure Storage의 *출력* 컨테이너에 업로드합니다. 이렇게 하려면 TaskApplication은 파일을 업로드하는 경우 경로의 일부로 컨테이너에 쓰기 액세스를 제공하는 컨테이너 공유 액세스 서명을 사용합니다. 컨테이너 공유 액세스 서명 가져오기는 BLOB 공유 액세스 서명을 가져올 때와 유사한 방식으로 수행됩니다. DotNetTutorial에서 이렇게 하기 위해 `GetContainerSasUrl` 도우미 메서드가 [CloudBlobContainer.GetSharedAccessSignature][net_sas_container]를 호출하는 것을 확인할 수 있습니다. “6단계: 작업 모니터링"에서 TaskApplication이 컨테이너 공유 액세스 서명을 사용하는 방법에 대해 자세히 알아봅니다.

> [!TIP]
> 공유 액세스 서명에 대해 두 부분으로 이루어진 시리즈, [1부: 공유 액세스 서명(SAS) 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 및 [2부: Blob Storage를 통해 공유 액세스 서명(SAS) 만들기 및 사용](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)을 확인하여 Storage 계정의 데이터에 대한 보안 액세스 제공에 대한 자세한 내용을 확인합니다.
>
>

## <a name="step-3-create-batch-pool"></a>3단계: Batch 풀 만들기
![Batch 풀 만들기][3]
<br/>

Batch **풀** 은 Batch가 작업의 태스크를 실행하는 계산 노드(가상 머신)의 컬렉션입니다.

Azure Storage API를 사용하여 응용 프로그램 및 데이터 파일을 Storage 계정에 업로드하면 *DotNetTutorial*이 Batch .NET 라이브러리에서 제공한 API를 사용하여 Batch 서비스에 대한 호출을 시작합니다. 이 코드는 먼저 [BatchClient][net_batchclient]를 만듭니다.

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

그런 다음, 샘플이 `CreatePoolIfNotExistsAsync`에 대한 호출을 통해 Batch 계정에 계산 노드의 풀을 만듭니다. `CreatePoolIfNotExistsAsync`는 [BatchClient.PoolOperations.CreatePool][net_pool_create] 메서드를 사용하여 Batch 서비스에 새로운 풀을 만듭니다.

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-vCPU, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

[CreatePool][net_pool_create]을 사용하여 풀을 만들 때 계산 노드 수, [노드의 크기](../cloud-services/cloud-services-sizes-specs.md), 노드의 운영 체제와 같은 매개 변수를 몇 가지 지정합니다. *DotNetTutorial*에서 [CloudServiceConfiguration][net_cloudserviceconfiguration]을 사용하여 [Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md)의 Windows Server 2012 R2를 지정합니다. 

풀에 대한 [VirtualMachineConfiguration][net_virtualmachineconfiguration]을 지정하여 Azure VM(Virtual Machines)인 계산 노드의 풀을 만들 수도 있습니다. Windows 또는 [Linux 이미지](batch-linux-nodes.md)에서 VM 계산 노드의 풀을 만들 수 있습니다. VM 이미지에 대한 소스는 다음 중 하나일 수 있습니다.

- [Azure Virtual Machines Marketplace][vm_marketplace]: 즉시 사용할 수 있는 Windows 및 Linux 이미지를 제공합니다. 
- 사용자가 준비하고 제공하는 사용자 지정 이미지입니다. 사용자 지정 이미지에 대한 자세한 내용은 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md#pool)을 참조하세요.

> [!IMPORTANT]
> Batch의 계산 리소스에 대한 요금이 부과됩니다. 비용을 최소화하려면 샘플을 실행하기 전에 `targetDedicatedComputeNodes`를 1로 낮출 수 있습니다.
>
>

이러한 실제 노드 속성과 함께 풀에 대해 [StartTask][net_pool_starttask]를 지정할 수도 있습니다. StartTask는 해당 노드가 풀을 연결할 때 각 노드에서 실행하고 이 때마다 노드가 다시 시작됩니다. StartTask는 작업을 실행하기 전에 계산 노드에서 응용 프로그램을 설치하는 데 특히 유용합니다. 예를 들어 태스크에서 Python 스크립트를 사용하여 데이터를 처리하는 경우 계산 노드에서 Python을 설치하는 데 StartTask를 사용할 수 있습니다.

이 샘플 응용 프로그램에서 StartTask는 Storage([StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles] 속성을 사용하여 지정됨)에서 다운로드하는 파일을 StartTask 작업 디렉터리에서 노드에서 실행되는 *모든* 태스크를 액세스할 수 있는 공유 디렉터리에 복사합니다. 기본적으로 노드가 풀에 조인하면 `TaskApplication.exe` 및 해당 종속성이 각 노드의 공유 디렉터리에 복사되므로 노드에서 실행되는 모든 작업이 공유 디렉터리에 액세스할 수 있습니다.

> [!TIP]
> Azure Batch의 **응용 프로그램 패키지** 기능은 풀의 계산 노드로 응용 프로그램을 가져올 수 있는 또 다른 방법을 제공합니다. 자세한 내용은 [Batch 응용 프로그램 패키지를 사용하여 계산 노드에 응용 프로그램 배포](batch-application-packages.md)를 참조하세요.
>
>

위의 코드 조각에서 주목할 만한 것은 StartTask의 *CommandLine* 속성에서 두 개의 환경 변수(`%AZ_BATCH_TASK_WORKING_DIR%` 및 `%AZ_BATCH_NODE_SHARED_DIR%`) 사용입니다. Batch 풀 내의 각 계산 노드는 Batch에 해당하는 몇 가지 환경 변수를 사용하여 자동으로 구성됩니다. 태스크에 의해 실행되는 모든 프로세스는 이러한 환경 변수에 대한 액세스를 갖습니다.

> [!TIP]
> 태스크 작업 디렉터리에 대한 정보뿐만 아니라 Batch 풀의 계산 노드에 사용할 수 있는 환경 변수에 대한 자세한 내용은 [개발자를 위한 Batch 기능 개요](batch-api-basics.md)에서 [태스크에 대한 환경 설정](batch-api-basics.md#environment-settings-for-tasks) 및 [파일 및 디렉터리](batch-api-basics.md#files-and-directories) 섹션을 참조하세요.
>
>

## <a name="step-4-create-batch-job"></a>4단계: Batch 작업 만들기
![Batch 작업 만들기][4]<br/>

Batch **작업**은 태스크의 컬렉션이며 계산 노드의 풀과 관련됩니다. 작업의 태스크는 연결된 풀의 계산 노드에서 실행됩니다.

워크로드와 관련된 태스크를 구성하고 추적하는 것뿐만 아니라, 작업(더 나아가, 태스크)에 대한 최대 실행 시간은 물론 Batch 계정 내 다른 작업 대비 작업 우선 순위와 같은 제약 조건을 부과하는 데도 작업을 사용합니다. 하지만 이 예제에서 작업은 3단계에서 만든 풀에만 연결됩니다. 추가적으로 구성되는 다른 속성은 없습니다.

모든 Batch 작업은 특정 풀에 연결됩니다. 연결은 작업의 태스크가 실행되는 노드를 나타냅니다. 이것은 아래 코드 조각에 표시된 것처럼 [CloudJob.PoolInformation][net_job_poolinfo] 속성을 사용하여 지정할 수 있습니다.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

이제 작업이 만들어졌으므로 작업은 작업 수행에 추가됩니다.

## <a name="step-5-add-tasks-to-job"></a>5단계: 작업에 태스크 추가
![작업에 태스크 추가][5]<br/>
*(1) 태스크가 작업에 추가됨, (2) 태스크가 노드에서 실행되도록 예약됨, (3) 태스크가 처리할 데이터 파일을 다운로드함*

Batch **태스크**는 계산 노드에서 실행되는 개별 작업 단위입니다. 작업에는 명령줄이 있으며 해당 명령줄에서 지정한 스크립트 또는 실행 파일을 실행합니다.

실제로 작업을 수행하려면 태스크가 작업에 추가되어야 합니다. 각 [CloudTask][net_task]는 명령줄 속성 및 명령줄이 자동으로 실행되기 전에 태스크가 노드에 다운로드하는 [ResourceFiles][net_task_resourcefiles](풀의 StartTask와 마찬가지로)를 사용하여 구성됩니다. *DotNetTutorial* 샘플 프로젝트에서 각 태스크는 파일을 하나만 처리합니다. 따라서 ResourceFiles 컬렉션은 단일 요소를 포함합니다.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> `%AZ_BATCH_NODE_SHARED_DIR%` 같은 환경 변수에 액세스하거나 노드의 `PATH`에서 찾을 수 없는 응용 프로그램을 실행하는 경우, 태스크 명령줄에 `cmd /c`를 접두사로 사용해야 합니다. 이렇게 하면 명령 인터프린터를 명시적으로 실행하고 명령을 수행한 후에 종료하도록 지시합니다. 이 요구 사항은 태스크가 노드의 `PATH`(예: *robocopy.exe* 또는 *powershell.exe*)에서 응용 프로그램을 실행하고 환경 변수가 사용되지 않는 경우 필요하지 않습니다.
>
>

위의 코드 조각의 `foreach` 루프 내에서 태스크에 대한 명령줄이 *TaskApplication.exe*에 전달되는 세 개의 명령줄 인수로 구성되어 있는 것을 확인할 수 있습니다.

1. **첫 번째 인수**는 처리할 파일의 경로입니다. 노드에 있는 것과 같은 파일에 대한 로컬 경로입니다. 위의 `UploadFileToContainerAsync`에 ResourceFile 개체를 처음으로 만드는 경우 이 속성(ResourceFile 생성자에 대한 매개 변수로)에 대해 파일 이름이 사용됩니다. 따라서 *TaskApplication.exe*와 동일한 디렉터리에서 파일을 찾을 수 있습니다.
2. **두 번째 인수**는 상위 *N* 단어를 출력 파일에 써야 한다는 것을 지정합니다. 샘플에서 상위 세 개 단어를 출력 파일에 쓸 수 있도록 하드 코드됩니다.
3. **세 번째 인수**는 Azure Storage의 **출력** 컨테이너에 쓰기 액세스를 제공하는 공유 액세스 서명(SAS)입니다. *TaskApplication.exe*는 Azure Storage에 출력 파일을 업로드할 때 공유 액세스 서명 URL을 사용합니다. TaskApplication 프로젝트의 `Program.cs` 파일에 포함된 `UploadFileToContainer` 메서드에서 이를 위한 코드를 찾을 수 있습니다.

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>6단계: 작업 모니터링
![작업 모니터링][6]<br/>
*클라이언트 응용 프로그램이 (1) 태스크의 완료 및 성공 상태를 모니터링하고 (2) 태스크는 결과 데이터를 Azure Storage에 업로드합니다.*

태스크가 작업에 추가되면 작업에 연결된 풀 내에서 계산 노드에서 실행되도록 자동으로 큐에 대기 및 예약됩니다. 지정한 설정에 따라 Batch는 대기, 예약, 다시 시도하는 모든 작업 및 기타 담당 작업 관리 업무를 처리합니다.

태스크 실행을 모니터링하는 방법은 여러 가지가 있습니다. DotNetTutorial은 완료 및 태스크 실패 또는 성공 상태에 대해서만 보고하는 간단한 예제를 보여 줍니다. DotNetTutorial의 `Program.cs`에 있는 `MonitorTasks` 메서드 내에 설명의 근거가 되는 세 가지 Batch .NET 개념이 있습니다. 표시된 순서에 따라 아래 나열되어 있습니다.

1. **ODATADetailLevel**: 목록 작업에서 [ODATADetailLevel][net_odatadetaillevel] 지정(작업의 태스크 목록을 가져오는 것과 같은)은 Batch 응용 프로그램 성능을 보장하는 데 반드시 필요합니다. Batch 응용 프로그램 내에서 상태 모니터링을 수행하려는 경우 [효율적인 Azure Batch 서비스 쿼리](batch-efficient-list-queries.md)를 읽기 목록에 추가합니다.
2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor]는 태스크 상태를 모니터링하기 위한 도우미 유틸리티를 사용하여 Batch .NET 응용 프로그램을 제공합니다. `MonitorTasks`에서 *DotNetTutorial*은 제한 시간 내에서 모든 태스크가 [TaskState.Completed][net_taskstate]에 도달할 때까지 기다립니다. 그 후 작업을 종료합니다.
3. **TerminateJobAsync**: [JobOperations.TerminateJobAsync][net_joboperations_terminatejob]로 작업 종료(또는 JobOperations.TerminateJob 차단)는 해당 작업을 완료로 표시합니다. Batch 솔루션에서 [JobReleaseTask][net_jobreltask]를 사용하는 경우 반드시 수행해야 합니다. 이것은 특수한 유형의 태스크이며, [작업 준비 및 완료 태스크](batch-job-prep-release.md)에 설명이 있습니다.

*DotNetTutorial*에서 `Program.cs`의 `MonitorTasks` 메서드는 아래와 같이 나타납니다.

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>7단계: 작업 출력 다운로드
![Storage에서 작업 출력 다운로드][7]<br/>

이제 작업이 완료되었으므로 태스크의 출력을 Azure Storage에서 다운로드할 수 있습니다. 이 작업은 *DotNetTutorial*의 `Program.cs`에서 `DownloadBlobsFromContainerAsync`에 대한 호출로 수행됩니다.

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> *DotNetTutorial* 응용 프로그램에서 `DownloadBlobsFromContainerAsync`에 대한 호출은 파일을 `%TEMP%` 폴더에 다운로드해야 하는 것을 지정합니다. 이 출력 위치를 수정해도 됩니다.
>
>

## <a name="step-8-delete-containers"></a>8단계: 컨테이너 삭제
Azure Storage에 있는 데이터에 대한 요금이 부과되므로 Batch 작업에 더 이상 필요 없는 모든 Blob을 제거하는 것이 좋습니다. DotNetTutorial의 `Program.cs`에서 도우미 메서드 `DeleteContainerAsync`에 대한 세 번의 호출로 수행됩니다.

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

메서드 자체는 단순히 컨테이너에 대한 참조를 가져온 다음 [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]를 호출합니다.

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>9단계: 작업 및 풀 삭제
마지막 단계로, DotNetTutorial 응용 프로그램에서 만든 작업 및 풀을 삭제하라는 메시지가 표시됩니다. 작업 및 태스크 자체에 대한 요금이 부과되지 않지만 계산 노드에 대한 요금이 청구 *됩니다*. 따라서 노드를 필요할 때만 할당하는 것이 좋습니다. 사용하지 않는 풀을 삭제하는 것이 유지 관리 프로세스의 일부가 될 수 있습니다.

BatchClient의 [JobOperations][net_joboperations] 및 [PoolOperations][net_pooloperations]에는 사용자가 삭제를 확인하는 경우 호출되는 것에 해당하는 삭제 메서드가 있습니다.

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> 계산 리소스에 대해 요금이 부과되고 사용하지 않는 풀 삭제는 비용을 최소화한다는 점을 유의하세요. 풀 삭제는 해당 풀 내의 모든 계산 노드를 삭제하고 노드의 모든 데이터는 풀이 삭제되면 복구할 수 없게 됩니다.
>
>

## <a name="run-the-dotnettutorial-sample"></a>*DotNetTutorial* 샘플 실행
샘플 응용 프로그램을 실행하는 경우 콘솔 출력은 다음과 유사하게 됩니다. 실행 중에 풀의 계산 노드가 시작되는 동안 `Awaiting task completion, timeout in 00:30:00...`에서 일시 중지가 발생합니다. [Azure Portal][azure_portal]을 사용하여 실행 중 및 실행 후에 풀, 계산 노드, 작업 및 태스크를 모니터링합니다. [Azure portal][azure_portal] 또는 [Azure Storage 탐색기][storage_explorers]를 사용하여 응용 프로그램에서 만든 Storage 리소스(컨테이너 및 Blob)를 봅니다.

기본 구성에서 응용 프로그램을 실행하는 경우 일반적인 실행 시간은 **약 5분** 입니다.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>다음 단계
다른 계산 시나리오를 실험하려면 *DotNetTutorial* 및 *TaskApplication*을 자유롭게 변경합니다. 예를 들어, 포털에서 장기 실행 태스크를 시뮬레이션하고 이를 모니터링하려면 [Thread.Sleep][net_thread_sleep] 등을 사용하여 *TaskApplication*에 실행 지연을 추가해 봅니다. 더 많은 태스크를 추가하거나 계산 노드 수를 조정합니다. 실행 시간을 줄이기 위해 기존 풀의 사용을 검사 및 허용하도록 논리를 추가합니다(*힌트*: [azure-batch-samples][github_samples]의 [Microsoft.Azure.Batch.Samples.Common][github_samples_common] 프로젝트에서 `ArticleHelpers.cs`을 확인).

이제 Batch 솔루션의 기본 워크플로에 익숙하다면 Batch 서비스의 추가 기능을 살펴볼 시간입니다.

* 이 서비스를 처음 사용하는 경우 [Azure Batch 기능 개요](batch-api-basics.md) 문서를 검토하는 것이 좋습니다.
* [Batch 학습 경로][batch_learning_path]의 **개발 세부 정보** 아래에서 다른 Batch 개발 문서를 시작하세요.
* [TopNWords][github_topnwords] 샘플의 Batch를 사용하여 "상위 n개 단어" 워크로드 처리의 다른 구현을 확인하세요.
* 라이브러리의 최신 변경 사항은 Batch .NET [릴리스 정보](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes)를 검토합니다.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchlabs]: https://azure.github.io/BatchLabs/
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Azure Storage에 컨테이너 만들기"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "컨테이너에 작업 응용 프로그램 및 입력(데이터) 파일 업로드"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Batch 풀 만들기"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Batch 작업 만들기"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "작업에 태스크 추가"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "작업 모니터링"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Storage에서 작업 출력 다운로드"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Batch 솔루션 워크플로(전체 다이어그램)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "포털의 Batch 자격 증명"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "포털의 Storage 자격 증명"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Batch 솔루션 워크플로(최소 다이어그램)"
