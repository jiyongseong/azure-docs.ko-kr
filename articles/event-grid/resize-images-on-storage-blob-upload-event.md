---
title: Azure Event Grid로 업로드 이미지 크기 자동 조정 | Microsoft Docs
description: Azure Event Grid는 Azure Storage에 BLOB 업로드를 트리거할 수 있습니다. 이 기능을 활용하여 Azure Storage에 업로드된 이미지 파일을 Azure Functions 등의 타 서비스로 보내 크기를 조절하거나 다른 향상을 수행할 수 있습니다.
services: event-grid, functions
author: ggailey777
manager: cfowler
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 0edf5648ddef58db74273635c84d7473e17e1b30
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Event Grid를 사용하여 업로드된 이미지 크기 자동 조정

[Azure Event Grid](overview.md)는 클라우드용 이벤트 서비스입니다. Event Grid를 사용하면 Azure 서비스나 타 리소스에서 발생하는 이벤트에 대한 구독을 만들 수 있습니다.  

이 자습서는 스토리지 자습서의 2부입니다. [이전 저장소 자습서][previous-tutorial]를 확장하여 Azure Event Grid 및 Azure Functions를 통해 서버 없는 자동 미리 보기 생성을 추가합니다. Event Grid를 사용하면 [Azure Functions](..\azure-functions\functions-overview.md)가 [Azure Blob 저장소](..\storage\blobs\storage-blobs-introduction.md) 이벤트에 응답하고 업로드된 이미지의 미리 보기를 생성할 수 있습니다. BLOB 저장소 만들기 이벤트에 대해 이벤트 구독을 만듭니다. BLOB이 특정 BLOB 저장소 컨테이너에 추가되면 함수 끝점이 호출됩니다. Event Grid에서 함수 바인딩에 전달된 데이터를 사용하여 BLOB에 액세스하고 미리 보기 이미지를 생성합니다. 

Azure CLI 및 Azure Portal을 사용하여 크기 조정 기능을 기존 이미지 업로드 앱에 추가합니다.

![Microsoft Edge 브라우저에 게시된 웹앱](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 일반 Azure Storage 계정 만들기
> * Azure Functions를 사용하여 서버 없는 코드 배포
> * Event Grid에서 BLOB 저장소 이벤트 구독 만들기

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

+ 이전 BLOB 저장소 자습서 [Azure Storage로 클라우드에 이미지 데이터 업로드][previous-tutorial]를 마쳐야 합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에는 Azure CLI 버전 2.0.14 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

Cloud Shell을 사용하지 않는 경우 먼저 `az login`을 사용하여 로그인해야 합니다.

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

Azure Functions에는 일반 저장소 계정이 필요합니다. [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 리소스 그룹에 별도의 일반 저장소 계정을 만듭니다.

Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 

다음 명령에서 `<general_storage_account>` 자리 표시자는 일반 저장소 계정에 대한 전역적으로 고유한 이름으로 바꿉니다. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>함수 앱 만들기  

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 서버를 사용하지 않는 함수 코드 실행을 위한 환경을 제공합니다. [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령을 사용하여 함수 앱을 만듭니다. 

다음 명령에서 `<function_app>` 자리 표시자를 고유한 함수 앱 이름으로 대체합니다. 함수 앱은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. `<general_storage_account>`의 경우 사용자가 만든 일반 저장소 계정의 이름을 대체합니다.

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

이제 [이전 자습서][previous-tutorial]에서 만든 Blob Storage에 연결하도록 함수 앱을 구성해야 합니다.

## <a name="configure-the-function-app"></a>함수 앱 구성

함수에는 Blob Storage 계정에 연결된 연결 문자열이 필요합니다. 다음 단계에서 Azure에 배포하는 함수 코드는 앱 설정 myblobstorage_STORAGE에서 연결 문자열을 검색하고, 앱 설정 myContainerName에서 썸네일 이미지 컨테이너 이름을 검색합니다. [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) 명령으로 연결 문자열을 가져옵니다. [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 명령을 사용하여 응용 프로그램 설정을 설정합니다.

다음 CLI 명령에서 `<blob_storage_account>`는 이전 자습서에서 만든 Blob Storage 계정의 이름입니다.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbnails
```

이제 이 함수 앱에 함수 코드 프로젝트를 배포할 수 있습니다.

## <a name="deploy-the-function-code"></a>함수 코드 배포 

이미지 크기 조정을 수행하는 C# 함수는 [이 GitHub 리포지토리](https://github.com/Azure-Samples/function-image-upload-resize)에서 제공됩니다. 이 Functions 코드 프로젝트를 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) 명령을 사용하여 함수 앱에 배포합니다. 

다음 명령에서 `<function_app>`은 이전에 만든 함수 앱의 이름입니다.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

이미지 크기 조정 함수는 Event Grid 서비스에서 전송된 HTTP 요청에 의해 트리거됩니다. 이벤트 구독을 만들어 Event Grid가 함수의 URL에서 이러한 알림을 가져오도록 지시합니다. 이 자습서에서 Blob 생성 이벤트를 구독합니다.

Event Grid 알림에서 함수에 전달되는 데이터에는 Blob의 URL이 포함됩니다. 차례로 URL을 입력 바인딩에 전달하여 Blob Storage에서 업로드된 이미지를 가져옵니다. 이 함수는 미리 보기 이미지를 생성하고 BLOB 저장소에서 결과 스트림을 별도의 컨테이너에 기록합니다. 

이 프로젝트에서는 트리거 형식에 `EventGridTrigger`를 사용합니다. 일반 HTTP 트리거보다는 Event Grid 트리거를 사용하는 것이 좋습니다. Event Grid는 Event Grid Function 트리거의 유효성을 자동으로 검사합니다. 일반 HTTP 트리거를 사용하면 [유효성 검사 응답](security-authentication.md#webhook-event-delivery)을 구현해야 합니다.

이 함수에 대해 자세히 알아보려면 [function.json 및 run.csx 파일](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/imageresizerfunc)을 참조하세요.
 
함수 프로젝트 코드는 공개 샘플 리포지토리에서 직접 배포됩니다. Azure Functions의 배포 옵션에 대한 자세한 내용은 [Azure Functions에 대한 지속적인 배포](../azure-functions/functions-continuous-deployment.md)를 참조하세요.

## <a name="create-an-event-subscription"></a>이벤트 구독 만들기

이벤트 구독은 특정 끝점으로 보내려는 공급자 생성 이벤트를 나타냅니다. 이 경우 해당 함수에서 끝점이 노출됩니다. Azure Portal에서 함수에 알림을 전송하는 이벤트 구독을 만들려면 다음 단계를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)의 맨 아래 왼쪽에서 화살표를 클릭하여 모든 서비스를 확장하고, **필터** 필드에 *함수*를 입력한 다음, **함수 앱**을 선택합니다. 

    ![Azure Portal의 함수 앱 찾아보기](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. 함수 앱을 확장하고 **imageresizerfunc** 함수를 선택한 후 **Event Grid 구독 추가**를 선택합니다.

    ![Azure Portal의 함수 앱 찾아보기](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. 표에 지정된 대로 이벤트 구독 설정을 사용합니다.
    
    ![Azure Portal의 함수에서 이벤트 구독 만들기](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | imageresizersub | 새 이벤트 구독을 식별하는 이름입니다. | 
    | **항목 유형** |  Storage 계정 | 저장소 계정 이벤트 공급자를 선택합니다. | 
    | **구독** | Azure 구독 | 기본적으로 현재 Azure 구독을 선택합니다.   |
    | **리소스 그룹** | myResourceGroup | **기존 항목 사용**을 선택하고 이 자습서에서 사용한 리소스 그룹을 선택합니다.  |
    | **인스턴스** |  Blob Storage 계정 |  만든 BLOB 저장소 계정을 선택합니다. |
    | **이벤트 유형** | 만든 BLOB  | 만든 **BLOB** 이외의 모든 유형을 선택 취소합니다. `Microsoft.Storage.BlobCreated` 이벤트 유형만 함수에 전달됩니다.| 
    | **구독자 형식** |  웹 후크 |  웹 후크 또는 Event Hubs를 선택합니다. |
    | **구독자 끝점** | autogenerated | 사용자에 대해 생성되는 끝점 URL을 사용합니다. | 
    | **접두사 필터** | /blobServices/default/containers/images/blobs/ | **이미지** 컨테이너의 항목에 대해서만 저장소 이벤트를 필터링합니다.| 

4. **만들기**를 클릭하여 이벤트 구독을 추가합니다. 그러면 Blob을 *이미지* 컨테이너에 추가할 때 `imageresizerfunc`를 트리거하는 이벤트 구독이 만들어집니다. 함수는 이미지의 크기를 조정하고 *썸네일* 컨테이너에 추가합니다.

이제 백엔드 서비스가 구성되었으므로 샘플 웹앱에서 이미지 크기 조정 기능을 테스트할 수 있습니다. 

## <a name="test-the-sample-app"></a>샘플 앱 테스트

웹앱에서 이미지 크기 조정을 테스트하려면 게시된 앱의 URL로 이동합니다. 웹앱의 기본 URL은 `https://<web_app>.azurewebsites.net`입니다.

**사진 업로드** 영역을 클릭하여 파일을 선택 및 업로드합니다. 이 영역에 사진을 끌어갈 수도 있습니다. 

업로드된 이미지가 사라진 후에는 업로드된 이미지의 사본이 **생성된 미리 보기** 카루셀에 표시됩니다. 이 이미지는 함수를 통해 크기 조정되어 *썸네일* 컨테이너에 추가되었으며 웹 클라이언트가 다운로드한 것입니다.

![Microsoft Edge 브라우저에 게시된 웹앱](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 일반 Azure Storage 계정 만들기
> * Azure Functions를 사용하여 서버 없는 코드 배포
> * Event Grid에서 BLOB 저장소 이벤트 구독 만들기

저장소 자습서 시리즈의 3부를 통해 저장소 계정에 대한 보안 액세스를 학습합니다.

> [!div class="nextstepaction"]
> [클라우드에서 응용 프로그램 데이터에 대한 보안 액세스](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Event Grid에 대한 소개는 [Azure Event Grid](overview.md)를 참조하세요. 
+ Azure Functions를 다룬 다른 자습서를 살펴보려면 [Azure Logic Apps와 통합하는 함수 만들기](..\azure-functions\functions-twitter-email.md)를 참조하세요. 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
