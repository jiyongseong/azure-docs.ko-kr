---
title: Azure 빠른 시작 - Ruby를 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 저장소 계정 및 컨테이너를 만듭니다. 그런 다음, Ruby용 저장소 클라이언트 라이브러리를 사용하여 Blob을 Azure Storage에 업로드하고, Blob을 다운로드하고, Blob을 컨테이너에 나열합니다.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: seguler
ms.openlocfilehash: 7ef74fe59400ff38a96e7b1b652393eef60eac81
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>빠른 시작: Ruby를 사용하여 Blob 업로드, 다운로드 및 나열

이 빠른 시작에서 Ruby를 사용하여 Azure Blob Storage에서 컨테이너에 블록 blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다. 
* [Ruby](https://www.ruby-lang.org/en/downloads/) 설치
* rubygem 패키지를 사용하여 [Ruby용 Azure Storage 라이브러리](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage)를 설치합니다. 

```
gem install azure-storage-blob
```

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드
이 빠른 시작 가이드에서 사용되는 [샘플 응용 프로그램](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git)은 기본 Ruby 응용 프로그램입니다.  

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Ruby 샘플 응용 프로그램을 열려면 storage-blobs-ruby-quickstart 폴더를 찾아 example.rb 파일을 엽니다.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성
응용 프로그램에서 응용 프로그램용 `BlobService` 인스턴스를 만들려면 저장소 계정 이름과 계정 키를 제공해야 합니다. IDE의 솔루션 탐색기에서 `example.rb` 파일을 엽니다. **accountname** 및 **accountkey** 값을 해당하는 계정 이름과 키로 바꿉니다. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>샘플 실행
이 샘플에서는 'Documents' 폴더에 테스트 파일을 만듭니다. 샘플 프로그램은 Blob 저장소에 테스트 파일을 업로드하고, 컨테이너에 Blob를 나열하며, 새 이름으로 파일을 다운로드합니다. 

샘플을 실행합니다. 다음 출력은 응용 프로그램 실행 시 반환되는 출력의 예제입니다.
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
아무 키나 눌러 계속하면 샘플 프로그램이 저장소 컨테이너 및 파일을 삭제합니다. 계속하기 전에 'Documents' 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

[Azure Storage 탐색기](http://storageexplorer.com)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 example.rb 파일을 열고 코드를 확인합니다. 

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해하도록 샘플 코드를 따라 진행합니다.

### <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기
가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* Azure Storage **BlobService** 개체의 인스턴스를 만들어 연결 자격 증명을 설정합니다. 
* 액세스하는 컨테이너를 나타내는 **Container** 개체를 만듭니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

Cloud Blob 컨테이너가 있으면 관심 있는 특정 blob을 가리키는 **Block** Blob 개체를 만들고, 업로드, 다운로드, 복사 등의 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

이 섹션에서는 Azure Storage 클라이언트의 인스턴스를 설정하고 Blob 서비스 객체를 인스턴스화하고 새 컨테이너를 만든 다음 Blob이 공개되도록 컨테이너에 대한 권한을 설정합니다. 컨테이너를 **quickstartblobs**로 지칭합니다. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob는 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다.  

Blob에 파일을 업로드하려면 로컬 드라이브에서 디렉터리 이름과 파일 이름을 조인하여 파일의 전체 경로를 가져옵니다. 그런 다음 **create\_block\_blob()** 메서드를 사용하여 지정된 경로에 파일을 업로드할 수 있습니다. 

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만들고 해당 파일이 **file\_path\_to\_file** 및 Blob 이름 **local\_file\_name**으로 업로드되게 저장합니다. 다음 예제에서는 **quickstartblobs**라는 저장소에 이 파일을 업로드합니다.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

블록 Blob의 콘텐츠를 부분적으로 업데이트하려면 **create\_block\_list()** 메서드를 사용합니다. 블록 blob 크기는 4.7TB까지 가능하며, Excel 스프레드시트에서 큰 비디오 파일까지 다양할 수 있습니다. 페이지 blob은 IaaS VM을 백업하는 데 사용되는 VHD 파일에 주로 사용됩니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. 추가 Blob은 단일 작성자 모델에서 사용해야 합니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

**list\_blobs()** 메서드를 사용하여 컨테이너의 파일 목록을 가져올 수 있습니다. 다음 코드는 Blob 목록을 검색하고, 이 과정을 반복하여 컨테이너에서 찾은 Blob의 이름을 표시합니다.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Blob 다운로드

**get\_blob()** 메서드를 사용하여 Blob를 로컬 디스크에 다운로드합니다. 다음 코드는 이전 섹션에서 업로드된 Blob를 다운로드합니다. 로컬 디스크에서 두 파일을 확인할 수 있게 "_DOWNLOADED"가 접미사로 Blob 이름에 추가됩니다. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 **delete\_container()** 메서드를 사용하여 전체 컨테이너를 삭제해도 됩니다. 만든 파일이 더 이상 필요하지 않으면 **delete\_blob()** 메서드를 사용하여 파일을 삭제합니다.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Blob을 사용하여 Ruby 응용 프로그램을 개발하기 위한 리소스

Blob 저장소를 사용하여 Ruby 응용 프로그램을 개발하기 위한 추가 리소스는 다음과 같습니다.

- GitHub에서 Azure Storage용 [Ruby 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-ruby)를 검색 및 다운로드하세요.
- Ruby 클라이언트 라이브러리를 사용하여 작성된 [Blob 저장소 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob)을 탐색하세요.

## <a name="next-steps"></a>다음 단계
 
이 빠른 시작 가이드에서는 Ruby를 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](./storage-ruby-how-to-use-blob-storage.md)


Storage 탐색기 및 Blob에 대한 자세한 내용은 [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
