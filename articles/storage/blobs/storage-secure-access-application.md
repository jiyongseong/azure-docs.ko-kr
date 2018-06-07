---
title: Azure Storage를 사용하여 클라우드의 응용 프로그램 데이터에 대한 액세스 보호 | Microsoft Docs
description: SAS 토큰, 암호화 및 HTTPS를 사용하여 클라우드의 응용 프로그램 데이터에 대한 액세스 보호
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: tutorial
ms.date: 03/06/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 09a229d93ee8d5fec36a0cfa765e87bebaafc24d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>클라우드의 응용 프로그램 데이터에 대한 액세스 보호

이 자습서는 시리즈의 3부입니다. 저장소 계정에 대한 액세스를 보호하는 방법을 배웁니다. 

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * SAS 토큰을 사용하여 썸네일 이미지에 액세스
> * 서버 쪽 암호화 켜기
> * HTTPS 전용 전송 사용

[Azure Blob Storage](../common/storage-introduction.md#blob-storage)는 응용 프로그램용 파일을 저장하기 위한 강력한 서비스를 제공합니다. 이 자습서에서는 [이전 항목][previous-tutorial]을 확장하여 웹 응용 프로그램에서 저장소 계정에 대한 액세스를 보호하는 방법을 보여 줍니다. 완료되면 이미지가 암호화되고 웹앱은 보안 SAS 토큰을 사용하여 썸네일 이미지에 액세스합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 이전 Storage 자습서인 [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정][previous-tutorial]을 완료했어야 합니다. 

## <a name="set-container-public-access"></a>컨테이너 공용 액세스 설정

자습서 시리즈의 이 부분에서는 썸네일에 액세스하기 위해 SAS 토큰을 사용합니다. 이 단계에서는 _thumbnails_ 컨테이너에 대한 공용 액세스를 `off`로 설정합니다.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>썸네일에 대한 SAS 토큰 구성

이 자습서 시리즈의 1부에서 웹 응용 프로그램은 공용 컨테이너의 이미지를 표시했습니다. 시리즈의 이 부분에서는 [SAS(공유 액세스 서명)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) 토큰을 사용하여 썸네일 이미지를 검색합니다. SAS 토큰을 사용하여 IP, 프로토콜, 시간 간격, 허용되는 권한에 따라 컨테이너 또는 blob에 대해 제한된 액세스를 제공할 수 있습니다.

이 예제에서 소스 코드 리포지토리는 업데이트된 코드 샘플이 있는 `sasTokens` 분기를 사용합니다. [az webapp deployment source delete](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_delete)를 사용하여 기존 GitHub 배포를 삭제합니다. 다음으로, [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) 명령을 사용하여 웹앱에 대한 Git 배포를 구성합니다.  

다음 명령에서 `<web-app>`은 웹앱의 이름입니다.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

리포지토리의 `sasTokens` 분기는 `StorageHelper.cs` 파일을 업데이트합니다. `GetThumbNailUrls` 작업이 아래 코드 예제로 바뀝니다. 업데이트된 작업은 [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)를 설정하여 SAS 토큰에 대한 시작 시간, 만료 시간 및 사용 권한을 지정함으로써 썸네일 URL을 검색합니다. 웹앱을 배포했으므로 이제 SAS 토큰을 사용하여 URL을 갖는 썸네일이 검색됩니다. 업데이트된 작업은 다음 예제에 표시됩니다.
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

다음 클래스, 속성 및 메서드는 이전 작업에서 사용됩니다.

|클래스  |properties| 메서드  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [결과](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[권한](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>서버 쪽 암호화

[Azure SSE(Storage 서비스 암호화)](../common/storage-service-encryption.md)는 데이터를 보호하고 안전하게 유지하는 데 도움이 됩니다. SSE는 미사용 데이터를 암호화하고 암호화, 암호 해독 및 키 관리를 처리합니다. 모든 데이터는 가장 강력한 블록 암호화 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 암호화됩니다.

SSE는 모든 성능 계층(표준 및 프리미엄), 모든 배포 모델(Azure Resource Manager 및 클래식) 및 모든 Azure Storage 서비스(Blob, 큐, 테이블 및 파일)의 데이터를 자동으로 암호화합니다. 

## <a name="enable-https-only"></a>HTTPS만 사용

저장소 계정과의 데이터 요청을 안전하게 유지하기 위해 요청을 HTTPS로만 제한할 수 있습니다. [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 사용하여 저장소 계정 필수 프로토콜을 업데이트합니다.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

`HTTP` 프로토콜을 사용하여 `curl`을 사용하는 연결을 테스트합니다.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

보안 전송이 요구되므로 이제 다음과 같은 메시지가 표시됩니다.

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>다음 단계

시리즈의 3부에서는 다음 방법을 비롯하여 저장소 계정에 대한 액세스를 보호하는 방법을 배웠습니다.

> [!div class="checklist"]
> * SAS 토큰을 사용하여 썸네일 이미지에 액세스
> * 서버 쪽 암호화 켜기
> * HTTPS 전용 전송 사용

시리즈의 4부를 계속 진행하면서 클라우드 저장소 응용 프로그램을 모니터링하고 문제를 해결하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [응용 프로그램 클라우드 응용 프로그램 저장소 모니터링 및 문제 해결](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json