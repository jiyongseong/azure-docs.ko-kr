---
title: Azure 빠른 시작 - .NET을 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 저장소 계정 및 컨테이너를 만듭니다. 그런 다음, .NET용 저장소 클라이언트 라이브러리를 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: tamram
ms.openlocfilehash: 7607643698cddb0cf5a113191ef638d36ab176bc
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>빠른 시작: .NET을 사용하여 BLOB 업로드, 다운로드 및 나열

이 빠른 시작에서 Azure Storage에 대해 .NET 클라이언트 라이브러리를 사용하여 컨테이너에 블록 Blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 먼저 [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)에서 Azure 저장소 계정을 만듭니다. 저장소 계정을 만드는 데 도움이 필요한 경우 [저장소 계정 만들기](../common/storage-quickstart-create-account.md)를 참조하세요.

다음으로 운영 체제용 .NET Core 2.0을 다운로드 및 설치합니다. Windows를 실행하는 경우 원한다면 Visual Studio를 설치하고 .NET Framework를 사용할 수 있습니다. 또한 운영 체제와 사용할 편집기를 설치하도록 선택할 수도 있습니다.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- [Windows용 .NET Core](https://www.microsoft.com/net/download/windows) 또는 [.NET Framework](https://www.microsoft.com/net/download/windows)(Windows용 Visual Studio에 포함)를 설치합니다.
- [Windows용 Visual Studio](https://www.visualstudio.com/)를 설치합니다. .NET Core를 사용하는 경우 Visual Studio는 선택 사항입니다.  

.NET Core와 .NET Framework 중에서 선택하는 방법에 대한 내용은 [서버 앱에 .NET Core와 .NET Framework 중에서 무엇을 사용할지 선택하는 방법](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)을 참조하세요.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- [Linux용 .NET Core](https://www.microsoft.com/net/download/linux) 설치
- 필요에 따라 [Visual Studio Code](https://www.visualstudio.com/) 및 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068) 설치

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- [macOS용 .NET Core](https://www.microsoft.com/net/download/macos)를 설치합니다.
- 필요에 따라 [Mac용 Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/) 설치

---

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드

이 빠른 시작 가이드에서 사용되는 샘플 응용 프로그램은 기본적인 콘솔 응용 프로그램입니다. [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart)에서 샘플 응용 프로그램을 탐색할 수 있습니다.

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Visual Studio 솔루션을 열려면 *storage-blobs-dotnet-quickstart* 폴더를 찾아서 열고 *storage-blobs-dotnet-quickstart.sln*을 두 번 클릭합니다. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성

응용 프로그램을 실행하려면 저장소 계정에 대한 연결 문자열을 제공해야 합니다. 이 응용 프로그램 예제는 환경 변수의 연결 문자열을 읽어서 Azure Storage에 대한 요청을 인증하는 데 사용합니다.

연결 문자열을 복사한 후 응용 프로그램을 실행 중인 로컬 컴퓨터의 새 환경 변수에 씁니다. 환경 변수를 설정하려면 콘솔 창을 열고 사용 중인 운영 체제의 지침을 따릅니다. `<yourconnectionstring>`을 실제 연결 문자열로 바꿉니다.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창을 포함하여 실행 중인 프로그램 중에서 환경 변수를 읽어야 하는 프로그램을 다시 시작해야 할 수도 있습니다. 예를 들어 편집기로 Visual Studio를 사용하는 경우 Visual Studio를 다시 시작한 후 샘플을 실행합니다. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

.bash_profile을 편집하고, 환경 변수를 추가합니다.

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.

---

## <a name="run-the-sample"></a>샘플 실행

이 샘플은 로컬 **MyDocuments** 폴더에 테스트 파일을 만들고 Blob 저장소에 업로드합니다. 그런 다음, 샘플은 컨테이너에 Blob을 나열하고 이전 파일과 새 파일을 비교할 수 있도록 새 이름으로 해당 파일을 다운로드합니다. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

편집기로 Visual Studio를 사용하는 경우 **F5** 키를 눌러 실행할 수 있습니다. 

그렇지 않으면 응용 프로그램 디렉터리로 이동하여 `dotnet run` 명령으로 응용 프로그램을 실행합니다.

```
dotnet run
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

응용 프로그램 디렉터리로 이동하여 `dotnet run` 명령으로 응용 프로그램을 실행합니다.

```
dotnet run
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

응용 프로그램 디렉터리로 이동하여 `dotnet run` 명령으로 응용 프로그램을 실행합니다.

```
dotnet run
```

---

응용 프로그램 예제의 출력은 다음 예제와 비슷합니다.

```
Azure Blob storage - .NET Quickstart sample

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the sample files and example container.
```

**Enter** 키를 누르면 응용 프로그램은 저장소 컨테이너 및 파일을 삭제합니다. 삭제하기 전에 **MyDocuments** 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다. 콘솔 창에서 Blob의 URL을 복사하고 브라우저에 붙여넣어 Blob의 콘텐츠를 봅니다.

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 Program.cs 파일을 열고 코드를 확인합니다. 

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로 작동 방식을 이해하도록 샘플 코드를 탐색합니다.

### <a name="try-parsing-the-connection-string"></a>연결 문자열 구문 분석 시도

샘플에서 수행하는 첫 번째 작업은 환경 변수가 저장소 계정을 가리키는 [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) 개체를 만들도록 구문 분석될 수 있는 연결 문자열을 포함하는지 확인하는 것입니다. 연결 문자열이 유효한지 확인하려면 [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) 메서드를 사용합니다. **TryParse**가 성공적인 경우 *storageAccount* 변수를 초기화하고 **true**를 반환합니다.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell or application needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>컨테이너 만들기 및 사용 권한 설정

다음으로 샘플은 컨테이너를 만들고 컨테이너의 모든 Blob이 공용이도록 해당 권한을 설정합니다. Blob이 공용인 경우 모든 클라이언트에서 익명으로 액세스할 수 있습니다.

컨테이너를 만들려면 먼저 저장소 계정의 Blob 저장소를 가리키는 [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) 개체의 인스턴스를 만듭니다. 다음으로 [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) 개체의 인스턴스를 만든 다음, 컨테이너를 만듭니다. 

이 경우 샘플은 [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) 메서드를 호출하여 컨테이너를 만듭니다. GUID 값은 고유한지 확인하기 위해 컨테이너 이름에 추가됩니다. 프로덕션 환경에서 컨테이너가 존재하지 않는 경우에만 [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) 메서드를 사용하여 컨테이너를 만들고 이름 충돌을 피하는 것이 좋습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름 지정에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

다음으로 샘플은 블록 Blob에 로컬 파일을 업로드합니다. 코드 예제는 이전 섹션에서 만든 컨테이너에서 [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) 메서드를 호출하여 **CloudBlockBlob** 개체에 대한 참조를 가져옵니다. 그런 다음, [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) 메서드를 호출하여 선택한 파일을 Blob에 업로드합니다. 이 메서드는 Blob이 없는 경우 만들고, Blob이 있는 경우 덮어씁니다. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

샘플은 [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) 메서드를 사용하여 컨테이너에 Blob을 나열합니다. 샘플의 경우 하나의 Blob만 컨테이너에 추가되었으므로 나열된 작업은 하나의 해당 Blob만 반환합니다.

한 번의 호출에 반환할 너무 많은 Blob이 있는 경우(기본적으로 5000개 초과) **ListBlobsSegmentedAsync** 메서드는 총 결과 집합 및 연속 토큰의 세그먼트를 반환합니다. Blob의 다음 세그먼트를 검색하려면 연속 토큰이 Null이 될 때까지 이전 호출 등에서 반환된 연속 토큰을 제공합니다. Null 연속 토큰은 모든 Blob이 검색되었음을 나타냅니다. 샘플 코드는 모범 사례를 위해 연속 토큰을 사용하는 방법을 보여 줍니다.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Blob 다운로드

다음으로 샘플은 [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) 메서드를 사용하여 이전에 만든 Blob을 로컬 파일 시스템에 다운로드합니다. 샘플 코드는 로컬 파일 시스템에서 두 파일을 볼 수 있도록 Blob 이름에 "_DOWNLOADED"의 접미사를 추가합니다.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>리소스 정리

샘플은 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync)를 사용하여 전체 컨테이너를 삭제하여 만든 리소스를 정리합니다. 원하는 경우 로컬 파일을 삭제할 수도 있습니다.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="resources-for-developing-net-applications-with-blobs"></a>Blob을 사용하는 .NET 응용 프로그램 개발을 위한 리소스

Blob 저장소를 사용하는 .NET 개발에 대한 이러한 추가 리소스를 참조합니다.

### <a name="binaries-and-source-code"></a>이진 파일 및 소스 코드

- 최신 버전의 Azure Storage용 [.NET 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage/)에 대한 NuGet 패키지를 다운로드하세요. 
- GitHub에서 [.NET 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-net)를 찾아보세요.

### <a name="client-library-reference-and-samples"></a>클라이언트 라이브러리 참조 및 샘플

- .NET 클라이언트 라이브러리에 대한 자세한 내용은 [.NET API 참조](https://docs.microsoft.com/dotnet/api/overview/azure/storage)를 참조하세요.
- .NET 클라이언트 라이브러리를 사용하여 작성된 [Blob 저장소 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)을 탐색하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 .NET을 사용하여 Blob을 업로드, 다운로드 및 나열하는 방법을 배웠습니다. 

Blob 저장소로 이미지를 업로드하는 웹앱을 만드는 방법에 대해 자세히 알아보려면 [Azure Storage를 사용하여 클라우드에 이미지 데이터 업로드](storage-upload-process-images.md)를 참조하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](storage-dotnet-how-to-use-blobs.md)

- .NET Core에 대해 자세히 알아보려면 [10분 안에 .NET 시작](https://www.microsoft.com/net/learn/get-started/)을 참조하세요.
- Windows용 Visual Studio에서 배포할 수 있는 샘플 응용 프로그램을 탐색하려면 [Azure Blob Storage를 사용하여 .NET 사진 갤러리 웹 응용 프로그램 샘플](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/)을 참조하세요.
 