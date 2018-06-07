---
title: 병렬 워크로드 실행 - Azure Batch .NET
description: 자습서 - Batch .NET 클라이언트 라이브러리를 사용하여 Azure Batch의 ffmpeg로 미디어 파일 트랜스코딩
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 57fc70d5b47f18affa90e1153884e8af23d937ec
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>자습서: .NET API를 사용하여 Azure Batch에서 병렬 워크로드 실행

클라우드에서 Azure Batch를 사용하여 대규모 병렬 및 HPC(고성능 컴퓨팅) 일괄 작업을 Azure에서 효율적으로 실행합니다. 이 자습서는 Batch를 사용하여 병렬 워크로드를 실행하는 C#의 예제를 안내합니다. 일반적인 Batch 응용 프로그램 워크플로, 그리고 Batch 및 Storage 리소스와 프로그래밍 방식으로 상호 작용하는 방법을 알아봅니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Batch 계정에 응용 프로그램 패키지 추가
> * Batch 및 Storage 계정 인증
> * 입력 파일을 Storage에 업로드
> * 계산 노드 풀을 만들어 응용 프로그램 실행
> * 작업 및 태스크를 만들어 입력 파일 처리
> * 태스크 실행 모니터링
> * 출력 파일 검색

이 자습서에서는 [ffmpeg](http://ffmpeg.org/) 오픈 소스 도구를 사용하여 MP4 미디어 파일을 병렬로 MP3 형식으로 변환합니다. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [Visual Studio IDE](https://www.visualstudio.com/vs)(Visual Studio 2015 또는 최신 버전) 

* Batch 계정 및 연결된 Azure Storage 계정. 이러한 계정을 만들려면 [Azure Portal](quick-create-portal.md) 또는 [Azure CLI](quick-create-cli.md)를 사용하는 Batch 빠른 시작을 참조하세요.

* [Windows 64비트 버전의 ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)(.zip). 로컬 컴퓨터에 zip 파일을 다운로드하세요. 이 자습서에서는 zip 파일만 필요합니다. 파일의 압축을 풀거나 로컬에 설치할 필요가 없습니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.


## <a name="add-an-application-package"></a>응용 프로그램 패키지 추가

Azure Portal을 사용하여 ffmpeg를 Batch 계정에 [응용 프로그램 패키지](batch-application-packages.md)로 추가합니다. 응용 프로그램 패키지를 사용하면 풀의 계산 노드에 태스크 응용 프로그램 및 해당 배포를 간편하게 관리할 수 있습니다. 

1. Azure Portal에서 **추가 서비스** > **Batch 계정**을 클릭하고 Batch 계정의 이름을 클릭합니다.
3. **응용 프로그램** > **추가**를 클릭합니다.
4. **응용 프로그램 ID**에 *ffmpeg*와 패키지 버전 *3.4*를 입력합니다. 이전에 다운로드한 ffmpeg zip 파일을 선택한 다음 **확인**을 클릭합니다. ffmpeg 응용 프로그램 패키지가 Batch 계정에 추가됩니다.

![응용 프로그램 패키지 추가](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>샘플 다운로드 및 실행

### <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [샘플 앱을 다운로드 또는 복제](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)합니다. Git 클라이언트를 사용하여 샘플 앱 리포지토리를 복제하려면 다음 명령을 사용합니다.

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

`BatchDotNetFfmpegTutorial.sln`(Visual Studio 솔루션 파일)이 있는 디렉터리로 이동합니다.

Visual Studio에서 솔루션 파일을 열고 `program.cs`의 자격 증명 문자열을 계정에 대해 가져온 값으로 업데이트합니다. 예: 

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

또한 솔루션의 ffmpeg 응용 프로그램 패키지 참조가 Batch 계정에 업로드한 ffmpeg 패키지의 ID 및 버전과 일치하는지 확인하세요.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "3.4";
```
### <a name="build-and-run-the-sample-project"></a>샘플 프로젝트 빌드 및 실행

* 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **솔루션 빌드**를 클릭합니다. 

* 메시지가 표시되면 모든 NuGet 패키지 복원을 확인합니다. 누락된 패키지를 다운로드해야 하는 경우 [NuGet 패키지 관리자](https://docs.nuget.org/consume/installing-nuget)가 설치되어 있는지 확인합니다.

그런 다음 실행합니다. 샘플 응용 프로그램을 실행하는 경우 콘솔 출력은 다음과 비슷합니다. 실행 중에 풀의 계산 노드가 시작되는 동안 `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...`에서 일시 중지가 발생합니다. 

```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```


Azure Portal에서 Batch 계정으로 가서 풀, 계산 노드, 작업 및 태스크를 모니터링합니다. 예를 들어 풀의 계산 노드에 대한 열 지도를 보려면 **풀** > *WinFFmpegPool*을 클릭합니다.

태스크가 실행 중일 때 열 지도는 다음과 유사합니다.

![풀 열 지도](./media/tutorial-parallel-dotnet/pool.png)


기본 구성에서 응용 프로그램을 실행하는 경우 일반적인 실행 시간은 **약 10분**입니다. 풀을 만드는 데 가장 많은 시간이 걸립니다.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>코드 검토

다음 섹션에서는 샘플 응용 프로그램을 Batch 서비스에서 워크로드를 처리하기 위해 수행하는 단계로 세분화합니다. 샘플에 있는 코드의 모든 줄이 설명되어 있지 않으므로 이 문서의 나머지 부분을 읽을 때 Visual Studio에서 공개 솔루션을 참조하세요.

### <a name="authenticate-blob-and-batch-clients"></a>Blob 및 Batch 클라이언트 인증

앱은 연결된 저장소 계정과 상호 작용하기 위해 .NET용 Azure Storage 클라이언트 라이브러리를 사용합니다. 공유 키 인증 사용을 인증하는 [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount)를 사용하여 계정에 대한 참조를 만듭니다. 그런 다음 [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient)를 만듭니다.

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

이 앱은 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 개체를 만들어 Batch 서비스의 풀, 작업 및 태스크를 만들고 관리합니다. 샘플의 Batch 클라이언트는 공유 키 인증을 사용합니다. 또한 Batch는 [Azure Active Directory](batch-aad-auth.md)를 통한 인증도 지원하여 개별 사용자 또는 무인 응용 프로그램을 인증합니다.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>입력 파일 업로드

이 앱은 `CreateContainerIfNotExist` 메서드에 `blobClient` 개체를 전달하여 입력 파일(MP4 형식)에 대한 저장소 컨테이너와 태스크 출력에 대한 컨테이너를 만듭니다.

```csharp
  CreateContainerIfNotExist(blobClient, inputContainerName;
  CreateContainerIfNotExist(blobClient, outputContainerName);
```

그러면 파일이 로컬 `InputFiles` 폴더에서 입력 컨테이너로 업로드됩니다. 저장소의 파일은 Batch가 나중에 계산 노드에 다운로드할 수 있는 Batch [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) 개체로 정의됩니다. 

`Program.cs`의 두 메서드는 파일 업로드에 관여합니다.

* `UploadResourceFilesToContainer`: ResourceFile 개체의 컬렉션을 반환하고 내부적으로 `UploadResourceFileToContainer`을 호출하여 `filePaths` 매개 변수에 전달된 각 파일을 업로드합니다.
* `UploadResourceFileToContainer`: 입력 컨테이너에 각 파일을 Blob으로 업로드합니다. 파일 업로드 후 Blob에 대한 SAS(공유 액세스 서명)을 가져오고 이를 나타내는 ResourceFile 개체를 반환합니다. 

```csharp
  List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(@"..\..\InputFiles", "*.mp4",
      SearchOption.TopDirectoryOnly));

  List<ResourceFile> inputFiles = UploadResourceFilesToContainer(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

.NET을 사용하여 저장소 계정에 Blob으로 파일을 업로드하는 방법에 대한 자세한 내용은 [.NET을 사용하여 Azure Blob Storage 시작](../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요.

### <a name="create-a-pool-of-compute-nodes"></a>계산 노드 풀 만들기

그런 다음, 샘플이 `CreatePoolIfNotExist`에 대한 호출을 통해 Batch 계정에 계산 노드의 풀을 만듭니다. 이 정의된 메서드는 [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) 메서드를 사용하여 노드 수, VM 크기 및 풀 구성을 설정합니다. 여기서 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) 개체는 Azure Marketplace에 게시된 Windows Server 이미지에 대한 [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference)를 지정합니다. Batch는 Azure Marketplace의 광범위한 VM 이미지뿐만 아니라 사용자 지정 VM 이미지도 지원합니다.

노드 수 및 VM 크기는 정의된 상수를 사용하여 설정됩니다. Batch는 전용 노드와 [우선 순위가 낮은](batch-low-pri-vms.md) 노드를 지원하며, 풀에서 하나 또는 둘 다 사용할 수 있습니다. 전용 노드는 풀에 예약되어 있습니다. 우선 순위가 낮은 노드는 Azure의 잔여 VM 용량에서 할인된 가격으로 제공됩니다. Azure에 충분한 용량이 없으면 우선 순위가 낮은 노드는 사용할 수 없게 됩니다. 이 샘플은 기본적으로 *Standard_A1_v2* 크기의 우선 순위가 낮은 노드 5개만 포함된 풀을 만듭니다. 

풀 구성에 [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference)를 추가하면 계산 노드에 ffmpeg 응용 프로그램이 배포됩니다. 

[Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) 메서드는 풀을 Batch 서비스에 제출합니다.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2012-R2-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,                                                
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

pool.Commit();  
```

### <a name="create-a-job"></a>작업 만들기

Batch 작업은 태스크를 실행할 풀과 우선 순위 및 작업 일정과 같은 선택적 설정을 지정합니다. 이 샘플은 `CreateJobIfNotExist`를 호출하여 작업을 만듭니다. 이 정의된 메서드는 [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) 메서드를 사용하여 풀에 작업을 만듭니다. 

[Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit) 메서드는 Batch 서비스에 작업을 제출합니다. 처음에는 작업에 태스크가 없습니다.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

job.Commit();        
```

### <a name="create-tasks"></a>태스크 만들기

이 샘플은 [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) 개체 목록을 만드는 `AddTasks` 메서드를 호출하여 작업에 태스크를 만듭니다. 각 `CloudTask`는 [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline) 속성을 사용하여 입력된 `ResourceFile` 개체를 처리하는 ffmpeg를 실행합니다. ffmpeg는 이전에 풀이 생성될 때 각 노드에 설치되었습니다. 여기서 명령줄은 fmpeg를 실행하여 입력된 각 MP4(비디오) 파일을 MP3(오디오) 파일로 변환합니다.

이 샘플에서는 명령줄을 실행한 후 MP3 파일에 대한 [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) 개체를 만듭니다. 각 태스크의 출력 파일(이 경우에는 하나)은 태스크의 [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) 속성을 사용하여 연결된 저장소 계정의 컨테이너에 업로드됩니다.

그런 다음 이 샘플은 [AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask) 메서드를 사용하여 작업에 태스크를 추가하고, 계산 노드 실행 대기열에 추가합니다. 

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-3.4-win64-static\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line) 
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
   

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
batchClient.JobOperations.AddTask(jobId, tasks);
```

### <a name="monitor-tasks"></a>태스크 모니터링

Batch가 작업에 태스크를 추가하면 서비스가 연결된 풀의 계산 노드 실행 대기열에 자동으로 추가하고 예약합니다. 지정한 설정에 따라 Batch는 대기, 예약, 다시 시도하는 모든 작업 및 기타 담당 작업 관리 업무를 처리합니다. 

태스크 실행을 모니터링하는 방법은 여러 가지가 있습니다. 이 샘플은 완료 시에만 태스크 실패 또는 성공 상태를 보고하는 `MonitorTasks` 메서드를 정의합니다. `MonitorTasks` 코드는 태스크에 대한 최소한의 정보만 효율적으로 선택하는 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel)을 지정합니다. 그런 다음 태스크 상태를 모니터링하는 도우미 유틸리티를 제공하는 [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor)를 만듭니다. `MonitorTasks`에서는 샘플이 제한된 시간 이내에 모든 태스크가 `TaskState.Completed`에 도달할 때까지 기다립니다. 그런 다음 작업을 종료하고, 완료되었지만 0이 아닌 종료 코드와 같은 오류가 발생한 태스크를 보고합니다.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    batchClient.Utilities.CreateTaskStateMonitor().WaitAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId, failureMessage);
    Console.WriteLine(failureMessage);
}
batchClient.JobOperations.TerminateJob(jobId, successMessage);
...

```

## <a name="clean-up-resources"></a>리소스 정리

앱은 태스크를 실행한 후 생성된 입력 저장소 컨테이너를 자동으로 삭제하고 사용자에게 Batch 풀 및 작업을 삭제하는 옵션을 제공합니다. BatchClient의 [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) 및 [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) 클래스에는 해당하는 삭제 메서드가 있고 이는 삭제를 확인하는 경우 호출됩니다. 작업 및 태스크 자체에 대한 요금이 부과되지 않지만 계산 노드에 대한 요금이 청구됩니다. 따라서 풀을 필요할 때만 할당하는 것이 좋습니다. 풀을 삭제하면 노드의 모든 태스크 출력이 삭제됩니다. 그러나 입력 및 출력 파일은 저장소 계정에 남아 있습니다.

더 이상 필요하지 않은 경우 리소스 그룹, Batch 계정 및 저장소 계정을 삭제합니다. Azure Portal에서 이렇게 하려면 배치 계정에 대한 리소스 그룹을 선택하고 **리소스 그룹 삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Batch 계정에 응용 프로그램 패키지 추가
> * Batch 및 Storage 계정 인증
> * 입력 파일을 Storage에 업로드
> * 계산 노드 풀을 만들어 응용 프로그램 실행
> * 작업 및 태스크를 만들어 입력 파일 처리
> * 태스크 실행 모니터링
> * 출력 파일 검색

.NET API를 사용하여 Batch 워크로드를 예약하고 처리하는 방법에 대한 예를 더 보려면 GitHub의 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [Batch C# 샘플](https://github.com/Azure/azure-batch-samples/tree/master/CSharp)
