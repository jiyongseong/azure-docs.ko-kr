---
title: Azure CDN(Content Delivery Network)에서 Azure Blob Storage 만료 관리 | Microsoft Docs
description: Azure CDN 캐싱의 Blob에 대한 TTL(Time-To-Live)을 제어하기 위한 옵션에 대해 알아봅니다.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: a0f89a272fa300f6acced2de02ba5465ab282079
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Azure CDN에서 Azure Blob 저장소의 만료 관리
> [!div class="op_single_selector"]
> * [Azure 웹 콘텐츠](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 저장소](cdn-manage-expiration-of-blob-content.md)
> 
> 

Azure Storage에서 [Blob Storage 서비스](../storage/common/storage-introduction.md#blob-storage)는 Azure CDN(Content Delivery Network)과 통합된 여러 Azure 기반 원본 중 하나입니다. TTL(time-to-live)이 경과할 때까지 공개적으로 액세스 가능한 모든 Blob 콘텐츠는 Azure CDN에 캐시될 수 있습니다. TTL은 원본 서버의 HTTP 응답에 있는 `Cache-Control` 헤더를 기반으로 결정됩니다. 이 문서에서는 Azure Storage에서 Blob에 `Cache-Control` 헤더를 설정할 수 있는 여러 가지 방법을 보여 줍니다.

[CDN 캐시 규칙](#setting-cache-control-headers-by-using-caching-rules)을 설정하여 Azure Portal에서 캐시 설정을 제어할 수도 있습니다. 캐싱 규칙을 만들고 캐싱 동작을 **재정의** 또는 **캐시 무시**로 설정하는 경우 이 문서에 설명된 원본 제공 캐싱 설정이 무시됩니다. 일반적인 캐싱 개념에 대한 자세한 내용은 [캐싱 동작 방식](cdn-how-caching-works.md)을 참조하세요.

> [!TIP]
> BLOB에 TTL을 설정하지 않을 수도 있습니다. 이 경우 Azure CDN은 사용자가 Azure Portal에서 캐싱 규칙을 설정하지 않은 한, 7일의 기본 TTL을 자동으로 적용합니다. 이 기본 TTL은 일반 웹 배달 최적화에만 적용됩니다. 대용량 파일 최적화의 경우 기본 TTL은 1일이고 미디어 스트리밍 최적화의 경우 기본 TTL은 1년입니다.
> 
> BLOB와 다른 파일에 대한 액세스 속도를 높이기 위해 Azure CDN이 작동하는 방법에 대한 자세한 내용은 [Azure CDN(Content Delivery Network) 개요](cdn-overview.md)를 참조하세요.
> 
> 자세한 내용은 [Blob Storage 소개](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)를 참조하세요.
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN 캐싱 규칙을 사용하여 Cache-Control 헤더 설정
BLOB `Cache-Control` 헤더를 설정하기 위한 기본 방법은 Azure Portal에서 캐싱 규칙을 사용하는 것입니다. CDN 캐싱 규칙에 대한 자세한 내용은 [캐싱 규칙을 사용하여 Azure CDN 캐싱 동작 제어](cdn-caching-rules.md)를 참조하세요.

> [!NOTE] 
> 캐싱 규칙은 **Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필에만 사용할 수 있습니다. **Verizon의 Azure CDN 프리미엄** 프로필은 유사한 기능을 위해 **관리** 포털에서 [Azure CDN 규칙 엔진](cdn-rules-engine.md)을 사용해야 합니다.

**CDN 캐싱 규칙 페이지로 이동하려면**:

1. Azure Portal에서 CDN 프로필을 선택한 다음, Blob용 끝점을 선택합니다.

2. 설정 아래의 왼쪽 창에서 **캐싱 규칙**을 선택합니다.

   ![CDN 캐싱 규칙 단추](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   **캐싱 규칙** 페이지가 나타납니다.

   ![CDN 캐싱 페이지](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**전역 캐싱 규칙을 사용하여 Blob Storage 서비스의 Cache-Control 헤더를 설정하려면:**

1. **전역 캐싱 규칙**에서 **쿼리 문자열 캐시 동작**을 **쿼리 문자열 무시**로 설정하고 **캐싱 동작**을 **재정의**로 설정합니다.
      
2. **캐시 만료 기간**은 **초** 상자에 3600초를 입력하거나 **시간** 상자에 1시간을 입력합니다. 

   ![CDN 전역 캐싱 규칙 예](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   해당 전역 캐싱 규칙은 1시간의 캐시 기간을 설정하고 끝점에 대한 모든 요청에 영향을 줍니다. 끝점에서 지정한 원본 서버가 보낸 `Cache-Control` 또는 `Expires` HTTP 헤더를 재정의합니다.   

3. **저장**을 선택합니다.
 
**사용자 지정 캐싱 규칙을 사용하여 Blob 파일의 Cache-Control 헤더를 설정하려면:**

1. **사용자 지정 캐싱 규칙**에 따라 일치 조건 두 개를 만듭니다.

     a. 첫 번째 일치 조건의 경우 **일치 조건**을 **경로**로 설정하고 **일치 값**으로 `/blobcontainer1/*`을 입력합니다. **캐싱 동작**을 **재정의**로 설정하고 **시간** 상자에 4시간을 입력합니다.

    B. 두 번째 일치 조건의 경우 **일치 조건**을 **경로**로 설정하고 **일치 값**으로 `/blobcontainer1/blob1.txt`를 입력합니다. **캐싱 동작**을 **재정의**로 설정하고 **시간** 상자에 2시간을 입력합니다.

    ![CDN 사용자 지정 캐싱 규칙 예](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    첫 번째 사용자 지정 캐싱 규칙은 끝점에서 지정한 원본 서버의 `/blobcontainer1`폴더 내 모든 Blob 파일에 대해 4시간의 캐시 기간을 설정합니다. 두 번째 규칙은 `blob1.txt` Blob 파일을 위한 첫 번째 규칙을 재정의하고 이에 대해 2시간의 캐시 기간을 설정합니다.

2. **저장**을 선택합니다.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 Cache-Control 헤더 설정
[Azure PowerShell](/powershell/azure/overview)은 Azure 서비스를 관리하는 가장 강력하고 빠른 방법 중 하나입니다. `Get-AzureStorageBlob` cmdlet을 사용하여 Blob에 대한 참조를 가져온 다음 `.ICloudBlob.Properties.CacheControl` 속성을 설정합니다. 

예: 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> PowerShell을 사용하여 [CDN 프로필 및 끝점을 관리](cdn-manage-powershell.md)할 수도 있습니다.
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>.NET을 사용하여 Cache-Control 헤더 설정
.NET 코드를 사용하여 Blob의 `Cache-Control` 헤더를 지정하려면 [.NET용 Azure Storage 클라이언트 라이브러리](../storage/blobs/storage-dotnet-how-to-use-blobs.md)를 사용하여 [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) 속성을 설정합니다.

예: 

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> [.NET용 Azure Blob Storage 샘플](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)에서 사용할 수 있는 추가 .NET 코드 샘플이 있습니다.
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>다른 방법을 사용하여 Cache-Control 헤더 설정

### <a name="azure-storage-explorer"></a>Azure Storage 탐색기
[Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 *CacheControl* 속성 등의 속성을 비롯한 Blob Storage 리소스를 확인하고 편집할 수 있습니다. 

Azure Storage Explorer에서 Blob의 *CacheControl* 속성을 업데이트하려면
   1. Blob를 선택하고 바로 가기 메뉴에서 **속성**을 선택합니다. 
   2. *CacheControl* 속성까지 아래로 스크롤합니다.
   3. 값을 입력한 다음, **저장**을 선택합니다.


![Azure Storage Explorer 속성](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure 명령줄 인터페이스
[Azure CLI(명령줄 인터페이스)](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)를 사용하여 명령줄에서 Azure Blob 리소스를 관리할 수 있습니다. Azure CLI를 사용하여 Blob을 업로드할 때 cache-control 헤더를 설정하려면 `-p` 스위치를 사용하여 *cacheControl* 속성을 설정합니다. 다음 예제에서는 TTL을 1시간(3600초)으로 설정하는 방법을 보여 줍니다.
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure Storage 서비스 REST API
[Azure Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)를 사용하면 요청에 다음 작업을 사용하여 *x-ms-blob-cache-control* 속성을 명시적으로 설정할 수 있습니다.
  
   - [Blob 배치](https://msdn.microsoft.com/library/azure/dd179451.aspx)
   - [블록 목록 배치](https://msdn.microsoft.com/library/azure/dd179467.aspx)
   - [Blob 속성 설정](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Cache-Control 헤더 테스트
Blob의 TTL 설정을 쉽게 확인할 수 있습니다. 브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)를 사용하여 Blob에 `Cache-Control` 응답 헤더가 포함되어 있는지 테스트합니다. [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/) 또는 [Fiddler](http://www.telerik.com/fiddler)와 같은 도구를 사용하여 응답 헤더를 검사할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure CDN에서 클라우드 서비스 콘텐츠의 만료를 관리하는 방법을 알아봅니다.](cdn-manage-expiration-of-cloud-service-content.md)
* [캐싱 개념에 대해 알아보기](cdn-how-caching-works.md)

