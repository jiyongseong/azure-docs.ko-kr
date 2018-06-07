---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 203f5a766c4c8a8f1e577f6be1e18d0f9ac95403
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
Azure Blob 저장소는 클라우드를 위한 Microsoft의 개체 저장소 솔루션입니다. Blob 저장소는 텍스트 또는 이진 데이터와 같이 구조화되지 않은 대량의 데이터를 저장하는 데 최적화되어 있습니다.

Blob 저장소는 다음에 이상적입니다.

* 브라우저에 이미지 또는 문서 직접 제공
* 분산 액세스용 파일 저장
* 동영상 및 오디오 스트리밍
* 로그 파일에 쓰기
* 백업/복원, 재해 복구 및 보관용 데이터 저장
* 온-프레미스 또는 Azure 호스티드 서비스에 의한 분석용 데이터 저장

Blob 저장소의 개체는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. 사용자 또는 클라이언트 응용 프로그램은 URL, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) 또는 Azure Storage 클라이언트 라이브러리를 통해 Blob에 액세스할 수 있습니다. 저장소 클라이언트 라이브러리는 [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/) 및 [Ruby](http://azure.github.io/azure-storage-ruby)를 비롯한 여러 언어에서 사용할 수 있습니다.

## <a name="blob-service-concepts"></a>Blob service 개념

Blob Storage는 세 가지 리소스(저장소 계정, 계정의 컨테이너, 컨테이너의 BLOB)를 노출합니다. 다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Blob(개체) 저장소 아키텍처 다이어그램](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Storage 계정

Azure Storage의 데이터 개체에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 자세한 내용은 [Azure 저장소 계정 정보](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

### <a name="container"></a>컨테이너

컨테이너는 파일 시스템의 폴더와 비슷하게 BLOB 집합을 구성합니다. 모든 BLOB은 컨테이너에 상주합니다. 한 저장소 계정에 포함될 수 있는 컨테이너 수에 제한이 없으며, 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다. 컨테이너 이름은 소문자여야 합니다.

### <a name="blob"></a>Blob
 
Azure Storage는 블록 Blob, 추가 Blob 및 [페이지 Blob](../articles/storage/blobs/storage-blob-pageblob-overview.md)(VHD 파일에 사용)의 세 가지 Blob 유형을 제공합니다.

* 블록 Blob은 텍스트 및 이진 데이터를 최대 약 4.7TB까지 저장합니다. 블록 Blob은 개별적으로 관리할 수 있는 데이터 블록으로 구성됩니다.
* 추가 Blob은 블록 Blob과 같이 블록으로 구성되지만 추가 작업에 최적화되어 있습니다. 추가 Blob은 가상 머신의 데이터 로깅 같은 시나리오에 적합합니다.
* 페이지 Blob은 임의 액세스 파일을 최대 8TB까지 저장합니다. 페이지 Blob은 VM을 백업하는 VHD 파일을 저장합니다.

모든 BLOB은 컨테이너에 상주합니다. 컨테이너는 파일 시스템의 폴더와 비슷합니다. BLOB을 가상 디렉터리에 추가로 정리하고 파일 시스템과 동일한 방법으로 트래버스할 수 있습니다. 

네트워크 제약 조건으로 인해 네트워크를 통해 Blob 저장소를 대상으로 데이터를 업로드하거나 다운로드하는 것이 불가능한 대규모 데이터 집합의 경우, 일련의 하드 드라이브를 Microsoft로 운송하여 데이터 센터에서 바로 데이터를 가져오거나 내보내도록 요청할 수 있습니다. 자세한 내용은 [Microsoft Azure Import/Export Service를 사용하여 Blob Storage에 데이터 전송](../articles/storage/common/storage-import-export-service.md)을 참조하세요.
  
컨테이너 및 Blob를 명명하는 세부 정보는 [컨테이너, Blob 및 메타데이터 명명 및 참조](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)를 참조하세요.
