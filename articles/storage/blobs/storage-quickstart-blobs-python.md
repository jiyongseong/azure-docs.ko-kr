---
title: Azure 빠른 시작 - Python을 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 저장소 계정 및 컨테이너를 만듭니다. 그런 다음, Python용 저장소 클라이언트 라이브러리를 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: 88f148e7ea175e928ee9f35b8728994a738e10da
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-python"></a>빠른 시작: Python을 사용하여 BLOB 업로드, 다운로드 및 나열

이 빠른 시작에서 Python을 사용하여 Azure Blob Storage에서 컨테이너에 블록 blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다. 
* [Python 설치](https://www.python.org/downloads/)
* [Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python) 다운로드 및 설치 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드
이 빠른 시작 가이드에서 사용되는 [샘플 응용 프로그램](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git)은 기본 Python 응용 프로그램입니다.  

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Python 프로그램을 열려면 storage-blobs-python-quickstart 폴더와 example.py 파일을 찾습니다.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성
응용 프로그램에서 `BlockBlobService` 개체를 만들려면 저장소 계정 이름과 계정 키를 제공해야 합니다. IDE의 솔루션 탐색기에서 `example.py` 파일을 엽니다. **accountname** 및 **accountkey** 값을 해당하는 계정 이름과 키로 바꿉니다. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>샘플 실행
이 샘플에서는 'Documents' 폴더에 테스트 파일을 만듭니다. 샘플 프로그램은 Blob 저장소에 테스트 파일을 업로드하고, 컨테이너에 Blob를 나열하며, 새 이름으로 파일을 다운로드합니다. 

샘플을 실행합니다. 다음 출력은 응용 프로그램 실행 시 반환되는 출력의 예제입니다.
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
아무 키나 눌러 계속하면 샘플 프로그램이 저장소 컨테이너 및 파일을 삭제합니다. 계속하기 전에 'Documents' 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

[Azure Storage 탐색기](http://storageexplorer.com)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 example.py 파일을 열고 코드를 확인합니다. 

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해하도록 샘플 코드를 따라 진행합니다.

### <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기
가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* 저장소 계정의 Blob 서비스를 가리키는 **CloudBlobClient** 개체를 인스턴스화합니다. 

* 액세스하는 컨테이너를 나타내는 **CloudBlobContainer** 개체를 인스턴스화합니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

Cloud Blob 컨테이너가 있으면 관심 있는 특정 blob을 가리키는 **CloudBlockBlob** 개체를 인스턴스화하고, 업로드, 다운로드, 복사 등의 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

이 섹션에서는 개체를 인스턴스화하고, 새 컨테이너를 만든 다음, 컨테이너에 대해 사용 권한을 설정하여 Blob를 공용 Blob로 유지합니다. 컨테이너를 **quickstartblobs**로 지칭합니다. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob는 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다.  

Blob에 파일을 업로드하려면 로컬 드라이브에서 디렉터리 이름과 파일 이름을 조인하여 파일의 전체 경로를 가져옵니다. 그런 다음 **create\_blob\_from\_path** 메서드를 사용하여 지정된 경로에 파일을 업로드할 수 있습니다. 

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만들고 해당 파일이 **file\_path\_to\_file** 및 Blob 이름 **local\_file\_name**으로 업로드되게 저장합니다. 다음 예제에서는 **quickstartblobs**라는 저장소에 이 파일을 업로드합니다.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Blob 저장소에서 사용할 수 있는 몇 가지 업로드 메서드가 있습니다. 예를 들어 메모리 스트림이 있는 경우 **create\_blob\_from\_path** 대신 **create\_blob\_from\_stream** 메서드를 사용할 수 있습니다. 

블록 blob 크기는 4.7TB까지 가능하며, Excel 스프레드시트에서 큰 비디오 파일까지 다양할 수 있습니다. 페이지 blob은 IaaS VM을 백업하는 데 사용되는 VHD 파일에 주로 사용됩니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

**list_blobs** 메서드를 사용하여 컨테이너의 파일 목록을 가져옵니다. 이 메서드는 생성기를 반환합니다. 다음 코드는 Blob 목록을 검색하고, 이 과정을 반복하여 컨테이너에서 찾은 Blob의 이름을 표시합니다.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Blob 다운로드

**get\_blob\_to\_path** 메서드를 사용하여 Blob를 로컬 디스크에 다운로드합니다. 다음 코드는 이전 섹션에서 업로드된 Blob를 다운로드합니다. 로컬 디스크에서 두 파일을 확인할 수 있게 "_DOWNLOADED"가 접미사로 Blob 이름에 추가됩니다. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 **delete\_container**를 사용하여 전체 컨테이너를 삭제해도 됩니다. 만든 파일이 더 이상 필요하지 않으면 **delete\_blob** 메서드를 사용하여 파일을 삭제합니다.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Blob을 사용하여 Python 응용 프로그램을 개발하기 위한 리소스

Blob 저장소를 사용하여 Python 응용 프로그램을 개발하기 위한 추가 리소스는 다음과 같습니다.

### <a name="binaries-and-source-code"></a>이진 파일 및 소스 코드

- GitHub에서 Azure Storage용 [Python 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-python)를 검색, 다운로드 및 설치하세요.

### <a name="client-library-reference-and-samples"></a>클라이언트 라이브러리 참조 및 샘플

- Python 클라이언트 라이브러리에 대한 자세한 내용은 [Python API 참조](https://docs.microsoft.com/python/api/overview/azure/storage)를 참조하세요.
- Python 클라이언트 라이브러리를 사용하여 작성된 [Blob 저장소 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob)을 탐색하세요.

## <a name="next-steps"></a>다음 단계
 
이 빠른 시작 가이드에서는 Python을 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](./storage-python-how-to-use-blob-storage.md)
 
Storage 탐색기 및 Blob에 대한 자세한 내용은 [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
