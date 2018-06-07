---
title: Azure 빠른 시작 - Go를 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 저장소 계정 및 컨테이너를 만듭니다. 그런 다음, Go용 저장소 클라이언트 라이브러리를 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
services: storage
author: seguler
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: seguler
ms.openlocfilehash: 1ce3debd8b04cf6c799dd7d6a3c87f843f1e0338
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>빠른 시작: Go를 사용하여 BLOB 업로드, 다운로드 및 나열

이 빠른 시작에서 Go 프로그래밍 언어를 사용하여 Azure Blob Storage의 컨테이너에 블록 Blob을 업로드하고, 다운로드하고, 나열하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다. 
* [Go 1.8 이상](https://golang.org/dl/) 설치
* `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob`을 사용하여 [Azure Storage Blob SDK for Go](https://github.com/azure/azure-storage-blob-go/)를 다운로드하고 설치합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드
이 빠른 시작에서 사용되는 [샘플 응용 프로그램](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git)은 기본 Go 응용 프로그램입니다.  

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Blob Storage에 대한 Go 샘플을 열려면 storage-quickstart.go 파일을 검색합니다.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성
이 솔루션에서는 샘플을 실행하는 컴퓨터에 로컬인 환경 변수에 저장소 계정 이름과 키를 안전하게 저장해야 합니다. 운영 체제에 따라 아래 예제 중 하나를 따라 환경 변수를 만듭니다.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>샘플 실행
이 샘플에서는 현재 폴더에 테스트 파일을 만들고, Blob Storage에 테스트 파일을 업로드하고, 컨테이너에 Blob을 나열하고, 파일을 버퍼에 다운로드합니다. 

샘플을 사용하여 다음 명령을 실행합니다. 

```go run storage-quickstart.go```

다음 출력은 응용 프로그램 실행 시 반환되는 출력의 예제입니다.
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
키를 눌러 계속하면 샘플 프로그램이 저장소 컨테이너 및 파일을 삭제합니다. 

> [!TIP]
> [Azure Storage 탐색기](http://storageexplorer.com)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 
>

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해하도록 샘플 코드를 따라 진행합니다.

### <a name="create-containerurl-and-bloburl-objects"></a>ContainerURL 및 BlobURL 개체 만들기
가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 ContainerURL 및 BlobURL 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 REST API를 실행하는 Create, PutBlob 및 GetBlob과 같은 하위 수준 API를 제공합니다.

* **SharedKeyCredential** 구조체를 사용하여 자격 증명을 저장합니다. 

* 자격 증명 및 옵션을 사용하여 **파이프라인**을 만듭니다. 파이프라인은 다시 시도 정책, 로깅, HTTP 응답 페이로드의 deserialization과 같은 항목을 지정합니다.  

* 새 ContainerURL 및 새 BlobURL 개체를 인스턴스화하여 컨테이너(Create) 및 Blob(PutBlob 및 GetBlob)에서 작업을 실행합니다.


ContainerURL이 있으면 Blob을 가리키는 **BlobURL** 개체를 인스턴스화하고, 업로드, 다운로드 및 복사 등의 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

이 섹션에서는 새 컨테이너를 만듭니다. 컨테이너는 **quickstartblobs-[random string]** 라고 합니다. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob는 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다.  

Blob에 파일을 업로드하려면 **os.Open**을 사용하여 파일을 엽니다. 그런 다음 PutBlob, PutBlock/PutBlockList와 같은 REST API 중 하나를 사용하여 지정된 경로에 파일을 업로드할 수 있습니다. 

또는 SDK가 하위 수준 REST API를 기반으로 하는 [고급 수준의 API](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go)를 제공합니다. 예를 들어 ***UploadFileToBlockBlob*** 함수는 처리량을 최적화하기 위해 PutBlock 작업을 사용하여 동시에 청크에서 파일을 업로드합니다. 파일이 256MB보다 작은 경우 대신 PutBlob을 사용하여 단일 트랜잭션에서 전송을 완료합니다.

다음 예제에서는 **quickstartblobs-[randomstring]** 이라는 컨테이너에 파일을 업로드합니다.

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

**ContainerURL**에서 **ListBlobs** 메서드를 사용하여 컨테이너의 파일 목록을 가져옵니다. ListBlobs는 지정된 **표식**에서 시작하여 Blob의 단일 세그먼트(최대 5000개)를 반환합니다. 빈 표식을 사용하여 시작 부분에서 열거형을 시작합니다. Blob 이름은 사전적 순서대로 반환됩니다. 세그먼트를 가져온 후에 처리한 다음, 이전에 반환된 표식을 다시 전달하여 ListBlobs를 호출합니다.  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Blob 다운로드

BlobURL에서 **GetBlob** 하위 수준 메서드를 사용하여 Blob을 다운로드합니다. 또는 [highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go)에서 제공된 **NewDownloadStream** 높은 수준의 API를 사용하여 스트림을 만들고 이 범위를 읽습니다. NewDownloadStream 함수는 연결에 실패하면 다시 시도합니다. 반면 Blob API 가져오기는 503(서버 작업 중) 등의 HTTP 상태 코드에서만 다시 시도합니다. 다음 코드는 **NewDownloadStream** 함수를 사용하여 Blob을 다운로드합니다. Blob의 콘텐츠를 버퍼에 작성하고 콘솔에 표시합니다.

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 **Delete** 메서드를 사용하여 전체 컨테이너를 삭제해도 됩니다. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>BLOB을 사용하여 Go 응용 프로그램을 개발하기 위한 리소스

BLOB 저장소를 사용하여 Go 응용 프로그램을 개발하기 위한 추가 리소스는 다음과 같습니다.

- GitHub에서 Azure Storage용 [Go 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-blob-go)를 검색 및 설치하세요.
- Go 클라이언트 라이브러리를 사용하여 작성된 [BLOB 저장소 샘플](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob#pkg-examples)을 탐색하세요.

## <a name="next-steps"></a>다음 단계
 
이 빠른 시작 가이드에서는 Go를 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Azure Storage Blob SDK에 대한 자세한 내용은 [소스 코드](https://github.com/Azure/azure-storage-blob-go/) 및 [API 참조](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob)를 참조하세요.
