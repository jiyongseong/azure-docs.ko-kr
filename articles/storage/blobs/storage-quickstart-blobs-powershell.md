---
title: Azure 빠른 시작 - Azure PowerShell을 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 Azure PowerShell을 사용합니다. 그런 다음, PowerShell을 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: f028d37a98cecf14706773a2eb7cb601481435d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 BLOB 업로드, 다운로드 및 나열

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 이 가이드에서는 PowerShell을 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 빠른 시작에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>컨테이너 만들기

Blob은 항상 컨테이너에 업로드됩니다. 폴더에서 컴퓨터의 파일을 구성하는 방식과 비슷하게 Blob 그룹을 구성할 수 있습니다.

컨테이너 이름을 설정하고, [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer)를 사용하여 컨테이너를 만들고, 권한을 ‘blob’으로 설정하여 파일에 대한 공용 액세스를 허용합니다. 이 예제의 컨테이너 이름은 *quickstartblobs*입니다.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. IaaS VM을 백업하는 데 사용된 VHD 파일은 페이지 Blob입니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. Blob Storage에 저장된 대부분의 파일은 블록 Blob입니다. 

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 해당 컨테이너의 블록 Blob에 대한 참조를 가져옵니다. Blob 참조가 있으면 [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent)를 사용하여 데이터를 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 만들고, Blob이 이미 있는 경우 덮어씁니다.

다음 예제에서는 Image001.jpg 및 Image002.png를 로컬 디스크의 D:\\_TestImages 폴더에서 방금 만든 컨테이너로 업로드합니다.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

원하는 만큼 파일을 업로드한 후 계속합니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob)을 사용하여 컨테이너의 Blob 목록을 가져옵니다. 이 예제에서는 업로드된 Blob의 이름만 보여 줍니다.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Blob 다운로드

로컬 디스크로 Blob을 다운로드합니다. 다운로드할 각 Blob에 대해 이름을 설정하고 [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent)를 호출하여 Blob을 다운로드합니다.

이 예제에서는 로컬 디스크의 D:\\_TestImages\Downloads에 Blob을 다운로드합니다. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>AzCopy를 사용한 데이터 전송

[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 유틸리티는 Azure Storage를 위한 또 다른 스크립팅 가능한 고성능 데이터 전송 옵션입니다. AzCopy를 사용하여 Blob, File 및 Table Storage 간에 데이터를 전송할 수 있습니다.

간단한 예제로, 다음은 PowerShell 창 내에서 *myfile.txt* 파일을 *mystoragecontainer* 컨테이너에 업로드하기 위한 AzCopy 명령입니다.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>리소스 정리

만든 자산을 모두 제거합니다. 이 작업을 수행하는 가장 쉬운 방법은 리소스 그룹을 삭제하는 것입니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 저장소 계정 및 리소스 그룹 자체가 제거됩니다.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 로컬 디스크와 Azure Blob 저장소 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell Storage cmdlet 참조
* [Storage PowerShell cmdlet](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage 탐색기
* [Microsoft Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)는 Windows, MacOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.