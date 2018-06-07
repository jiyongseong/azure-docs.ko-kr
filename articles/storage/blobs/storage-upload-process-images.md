---
title: Azure Storage를 사용하여 클라우드에 이미지 데이터 업로드 | Microsoft Docs
description: 웹앱에서 Azure Blob Storage를 사용하여 응용 프로그램 데이터 저장
services: storage
documentationcenter: ''
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 29accb3394e9a2f6939a657172c1a5c2e411706a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Azure Storage를 사용하여 클라우드에 이미지 데이터 업로드

이 자습서는 시리즈의 1부입니다. 이 자습서에서는 Azure Storage 클라이언트 라이브러리를 사용하여 저장소 계정에 이미지를 업로드하는 웹 응용 프로그램을 배포하는 방법을 보여 줍니다. 완료되면 Azure Storage에서 이미지를 저장 및 표시하는 웹앱이 생성됩니다.

![Images 컨테이너 보기](media/storage-upload-process-images/figure2.png)

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 저장소 계정 만들기
> * 컨테이너 만들기 및 사용 권한 설정
> * 액세스 키 가져오기
> * 응용 프로그램 설정 구성
> * Azure에 웹앱 배포
> * 웹 응용 프로그램과 상호 작용

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기 

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.
 
다음 예제에서는 `myResourceGroup`이라는 리소스 그룹을 만듭니다.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>저장소 계정 만들기
 
이 샘플은 Azure Storage 계정의 blob 컨테이너에 이미지를 업로드합니다. 저장소 계정은 Azure Storage 데이터 개체의 저장 및 액세스를 위한 고유한 네임스페이스를 제공합니다. [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 만든 리소스 그룹에 저장소 계정을 만듭니다. 

> [!IMPORTANT] 
> 자습서의 2부에서는 blob 저장소에 대해 이벤트 구독을 사용합니다. 이벤트 구독은 현재 West Central US 및 West US 2의 Blob Storage 계정에 대해서만 지원됩니다. 이러한 제한으로 인해 이미지 및 썸네일을 저장하기 위해 샘플 앱에서 사용하는 Blob Storage 계정을 만들어야 합니다.   

다음 명령에서 `<blob_storage_account>` 자리 표시자는 Blob Storage 계정에 대한 전역적으로 고유한 이름으로 바꿉니다.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Blob Storage 컨테이너 만들기
 
앱은 Blob Storage 계정에서 두 컨테이너를 사용합니다. 컨테이너는 폴더와 비슷하며 blob을 저장하는 데 사용됩니다. _images_ 컨테이너는 앱이 고해상도 이미지를 업로드하는 위치입니다. 시리즈의 뒷부분에서 Azure 함수 앱은 크기가 조정된 이미지 썸네일을 _썸네일_ 컨테이너에 업로드합니다. 

[az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) 명령을 사용하여 저장소 계정 키를 가져옵니다. 그런 다음 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령을 사용하여 이 키로 2개의 컨테이너를 만듭니다.  
 
이 경우 `<blob_storage_account>`는 만든 BLOB 저장소 계정의 이름입니다. _이미지_ 컨테이너 공용 액세스는 `off`로 설정되고, _썸네일_ 컨테이너 공용 액세스는 `container`로 설정됩니다. `container` 공용 액세스 설정을 사용하면 웹 페이지를 방문하는 모든 사람이 해당 썸네일을 볼 수 있습니다.
 
```azurecli-interactive 
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Blob Storage 계정 이름 및 키를 적어둡니다. 샘플 앱은 이러한 설정을 사용하여 저장소 계정에 연결한 후 이미지를 업로드합니다. 

## <a name="create-an-app-service-plan"></a>App Service 계획 만들기 

[App Service 계획](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)은 위치, 크기 및 앱을 호스트하는 웹 서버 팜의 기능을 지정합니다. 

[az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) 명령으로 App Service 계획을 만듭니다. 

다음 예에서는 **체험** 가격 책정 계층에서 `myAppServicePlan`이라는 App Service 계획을 만듭니다. 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>웹앱 만들기 

웹앱은 GitHub 샘플 리포지토리에서 배포되는 샘플 앱 코드에 대한 호스팅 공간을 제공합니다. [az webapp create](/cli/azure/webapp#az_webapp_create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](../../app-service/app-service-web-overview.md)을 만듭니다.  
 
다음 명령에서 `<web_app>`을 고유한 이름으로 바꿉니다(유효한 문자는 `a-z`, `0-9` 및 `-`). `<web_app>`이 고유하지 않으면 _지정된 이름이 `<web_app>`인 웹 사이트가 이미 있습니다."라는 오류 메시지가 표시됩니다._ 웹앱의 기본 URL은 `https://<web_app>.azurewebsites.net`입니다.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>GitHub 리포지토리에서 샘플 앱 배포 

App Service는 웹앱에 콘텐츠를 배포하는 여러 가지 방법을 지원합니다. 이 자습서에서는 [공용 GitHub 샘플 리포지토리](https://github.com/Azure-Samples/storage-blob-upload-from-webapp)에서 웹앱을 배포합니다. [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) 명령을 사용하여 웹앱에 대한 Git 배포를 구성합니다. `<web_app>`을 이전 단계에서 만든 웹앱의 이름으로 바꿉니다.

샘플 프로젝트에는 이미지를 수락하고, 저장소 계정에 저장한 후 썸네일 컨테이너의 이미지를 표시하는 [ASP.NET MVC](https://www.asp.net/mvc) 앱이 포함되어 있습니다. 웹 응용 프로그램은 Azure Storage 클라이언트 라이브러리의 [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet), 및 [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) 네임스페이스를 사용하여 Azure Storage와 상호 작용합니다. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>웹앱 설정 구성 

샘플 웹앱은 [Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용하여 이미지 업로드에 사용되는 액세스 토큰을 요청합니다. Storage SDK에서 사용되는 저장소 계정 자격 증명은 웹앱에 대한 응용 프로그램 설정에 지정됩니다. [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용하여 응용 프로그램 설정을 배포된 앱에 추가합니다. 

다음 명령에서 `<blob_storage_account>`는 Blob Storage 계정의 이름이고 `<blob_storage_key>`는 관련된 키입니다. `<web_app>`을 이전 단계에서 만든 웹앱의 이름으로 바꿉니다.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

웹앱이 배포되고 구성되면 앱에서 이미지 업로드 기능을 테스트할 수 있습니다.   

## <a name="upload-an-image"></a>이미지 업로드 

웹앱을 테스트하려면 게시된 앱의 URL로 이동합니다. 웹앱의 기본 URL은 `https://<web_app>.azurewebsites.net`입니다. **사진 업로드** 영역을 선택하고 파일을 선택 및 업로드하거나 파일을 해당 영역으로 끌어다 놓습니다. 성공적으로 업로드되면 이미지가 사라집니다.

![ImageResizer 앱](media/storage-upload-process-images/figure1.png)

샘플 코드에서 `Storagehelper.cs` 파일의 `UploadFiletoStorage` 작업은 [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) 메서드를 사용하여 저장소 계정 내의 `images` 컨테이너에 이미지를 업로드하는 데 사용됩니다. 다음 코드 샘플에는 `UploadFiletoStorage` 작업이 포함되어 있습니다. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

다음 클래스 및 메서드는 이전 작업에서 사용됩니다.

|클래스  |방법  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>저장소 계정에서 이미지가 표시되는지 확인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다. 왼쪽 메뉴에서 **저장소 계정**을 선택하고 저장소 계정의 이름을 선택합니다. **개요** 아래에서 **images** 컨테이너를 선택합니다.

컨테이너에 이미지가 표시되는지 확인합니다.

![Images 컨테이너 보기](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>썸네일 보기 테스트

썸네일 보기를 테스트하려면 응용 프로그램이 썸네일 컨테이너를 읽을 수 있도록 썸네일 컨테이너에 이미지를 업로드합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다. 왼쪽 메뉴에서 **저장소 계정**을 선택하고 저장소 계정의 이름을 선택합니다. **Blob 서비스** 아래에서 **컨테이너**를 선택하고 **썸네일** 컨테이너를 선택합니다. **업로드**를 선택하여 **blob 업로드** 창을 엽니다.

파일 선택기를 사용하여 파일을 선택하고 **업로드**를 선택합니다.

앱으로 다시 이동하여 **썸네일** 컨테이너에 업로드된 이미지가 보이는지 확인합니다.

![Images 컨테이너 보기](media/storage-upload-process-images/figure2.png)

Azure Portal의 **썸네일** 컨테이너에서 업로드한 이미지를 선택하고 **삭제**를 선택하여 이미지를 삭제합니다. 시리즈의 2부에서는 썸네일 이미지 생성을 자동화하여 이 테스트 이미지가 필요하지 않도록 합니다.

CDN을 사용하도록 설정하여 Azure Storage 계정의 콘텐츠를 캐시할 수 있습니다. 이 자습서에서는 설명되어 있지 않지만, Azure Storage 계정으로 CDN을 사용하도록 설정하는 방법을 알아보려면 [Azure CDN과 Azure Storage 계정 통합](../../cdn/cdn-create-a-storage-account-with-cdn.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

시리즈의 1부에서는 다음 방법을 비롯하여 저장소와 상호 작용하는 웹앱을 구성하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 저장소 계정 만들기
> * 컨테이너 만들기 및 사용 권한 설정
> * 액세스 키 가져오기
> * 응용 프로그램 설정 구성
> * Azure에 웹앱 배포
> * 웹 응용 프로그램과 상호 작용

시리즈 2부를 계속 진행하면서 Event Grid를 사용하여 이미지 크기를 조정하도록 Azure 함수를 트리거하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Event Grid를 사용하여 업로드된 이미지의 크기를 조정하도록 Azure 함수 트리거](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
