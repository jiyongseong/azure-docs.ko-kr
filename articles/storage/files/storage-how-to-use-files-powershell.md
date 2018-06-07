---
title: Azure PowerShell을 사용하여 Azure 파일 공유 관리
description: Azure PowerShell을 사용하여 Azure 파일 공유를 관리하는 방법을 알아봅니다.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: c796ac54eb21af18d21144a00b633c6b6efc28be
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 파일 공유 관리 
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 Windows, Linux 및 macOS에 탑재할 수 있습니다. 이 가이드에서는 PowerShell을 사용하여 Azure 파일 공유로 작업하는 기본 사항을 설명합니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 리소스 그룹 및 저장소 계정 만들기
> * Azure 파일 공유 만들기 
> * 디렉터리 만들기
> * 파일 업로드 
> * 파일 다운로드
> * 공유 스냅숏 만들기 및 사용

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만들 수 있습니다.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하려는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.1.1 이상이 필요합니다. 실행 중인 Azure PowerShell 모듈의 버전을 알아보려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Azure 리소스 그룹이 아직 없는 경우 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet을 사용하여 새 리소스 그룹을 만들 수 있습니다. 

다음 예제에서는 미국 동부 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기
저장소 계정은 Azure 파일 공유 또는 Blob나 큐와 같은 다른 저장소 리소스를 배포하는 데 사용할 수 있는 저장소의 공유 풀입니다. 저장소 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다.

이 예제에서는 `mystorageacct<random number>`라는 저장소 계정을 만들고 변수 **$storageAcct**의 해당 저장소 계정에 대한 참조를 배치합니다. 저장소 계정 이름은 고유해야 합니다. 따라서 `Get-Random`를 사용하여 고유하게 만들 이름에 숫자를 추가합니다. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
이제 첫 번째 Azure 파일 공유를 만들 수 있습니다. [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare) cmdlet을 사용하여 파일 공유를 만들 수 있습니다. 이 예제에서는 `myshare`라는 공유를 만듭니다.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> 공유 이름은 모두 소문자, 숫자 및 단일 하이픈이어야 하지만 하이픈으로 시작할 수 없습니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Azure 파일 공유의 콘텐츠 작업
이제 Azure 파일 공유를 만들었으므로 SMB를 사용하여 [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) 또는 [macOS](storage-how-to-use-files-mac.md)에 파일 공유를 탑재할 수 있습니다. 또는 Azure PowerShell 모듈을 사용하여 Azure 파일 공유로 작업할 수 있습니다. 이점이 SMB를 사용하여 파일 공유를 탑재하는 이점입니다. PowerShell을 사용하여 만든 모든 요청이 파일 REST API로 구성되어 다음에서 파일 공유의 파일 및 디렉터리를 만들고, 수정하고, 삭제할 수 있기 때문입니다.

- PowerShell Cloud Shell(SMB를 통한 파일 공유를 탑재할 수 없음)
- 포트 445의 차단을 해제하지 않은 온-프레미스 클라이언트 등 SMB 공유를 탑재할 수 없는 클라이언트
- [Azure Functions](../../azure-functions/functions-overview.md)와 같은 서버가 없는 시나리오 


### <a name="create-directory"></a>디렉터리 만들기
Azure 파일 공유의 루트에 *myDirectory*라는 새 디렉터리를 만들려면 [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory) cmdlet을 사용합니다.


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>파일 업로드
[Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent) cmdlet을 사용하여 파일을 업로드하는 방법을 보여주려면 먼저 업로드할 PowerShell Cloud Shell의 임시 드라이브 내에 파일을 만들어야 합니다. 

이 예제에서는 임시 드라이브의 새 파일에 현재 날짜 및 시간을 저장한 다음, 파일 공유에 파일을 업로드합니다.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell을 로컬로 실행 중인 경우 `C:\Users\ContainerAdministrator\CloudDrive\`를 컴퓨터에 있는 경로로 대체해야 합니다.

파일을 업로드한 후에 [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) cmdlet을 사용하여 파일이 Azure 파일 공유에 업로드되었는지 확인할 수 있습니다. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>파일 다운로드
[Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) cmdlet을 사용하여 Cloud Shell의 임시 드라이브에 업로드된 파일의 복사본을 다운로드할 수 있습니다.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

파일에 다운로드한 후에 `Get-ChildItem`을 사용하여 PowerShell Cloud Shell 임시 드라이브에 파일을 다운로드했는지 확인할 수 있습니다.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>파일 복사
일반적인 작업은 하나의 파일 공유에서 다른 파일 공유로 파일을 복사하거나 Azure Blob Storage 컨테이너 간에 파일을 복사하는 것입니다. 이 기능을 보여주기 위해 [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy) cmdlet을 사용하여 새 공유를 만들고, 이러한 새 공유에 업로드한 파일을 복사할 수 있습니다. 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

이제 새 공유에서 파일을 나열하는 경우 복사된 파일이 표시됩니다.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

`Start-AzureStorageFileCopy` cmdlet은 Azure 파일 공유와 Azure Blob Storage 컨테이너 간에 임시 파일 이동에 편리하지만(이동되는 파일의 수 또는 크기 측면에서) 대규모 이동에 AzCopy를 사용하는 것이 좋습니다. [Windows용 AzCopy](../common/storage-use-azcopy.md) 및 [Linux용 AzCopy](../common/storage-use-azcopy-linux.md)에 대해 자세히 알아봅니다. AzCopy를 로컬로 설치해야 합니다. Cloud Shell에서 사용할 수 없습니다. 

## <a name="create-and-modify-share-snapshots"></a>공유 스냅숏 만들기 및 수정
Azure 파일 공유로 수행할 수 있는 유용한 한 가지 추가 작업은 공유 스냅숏을 만드는 것입니다. 스냅숏은 Azure 파일 공유의 지정 시간을 유지합니다. 공유 스냅숏은 이미 익숙한 다음과 같은 운영 시스템 기술과 유사합니다.
- NTFS 및 ReFS와 같은 Windows 파일 시스템용 [VSS(볼륨 섀도 복사본 서비스)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)
- Linux 시스템용 [LVM(논리 볼륨 관리자)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 스냅숏
- macOS용 [APFS(Apple 파일 시스템)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 스냅숏 

파일 공유에 대한 PowerShell 개체에서 `Snapshot` 메서드를 사용하여 공유에 대한 공유 스냅숏을 만들 수 있습니다. 이 항목은 [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare) cmdlet을 사용하여 검색됩니다. 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>공유 스냅숏 찾아보기
`Get-AzureStorageFile` cmdlet의 `-Share` 매개 변수에 스냅숏 참조(`$snapshot`)를 전달하여 공유 스냅숏의 콘텐츠를 찾아볼 수 있습니다.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>공유 스냅숏 나열
다음 명령을 사용하여 공유에 만든 스냅숏의 목록을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>공유 스냅숏에서 복원
이전에 사용한 `Start-AzureStorageFileCopy` 명령을 사용하여 파일을 복원할 수 있습니다. 이 빠른 시작의 목적상 스냅숏에서 복원할 수 있도록 먼저 이전에 업로드한 `SampleUpload.txt` 파일을 삭제합니다.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>공유 스냅숏 삭제
`-Share` 매개 변수에 대한 `$snapshot` 참조를 포함하는 변수와 함께 [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare) cmdlet을 사용하여 공유 스냅숏을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>리소스 정리
작업을 완료하면 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) cmdlet을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

또는 리소스를 하나씩 제거할 수 있습니다.

- 이 빠른 시작에서 만든 Azure 파일 공유를 제거하려면:
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- 저장소 계정 자체를 제거하려면: (그러면 암시적으로 만든 Azure 파일 공유뿐만 아니라 Azure Blob Storage 컨테이너와 같이 만든 다른 저장소 리소스도 제거합니다.)
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>다음 단계
- [Azure Portal을 사용하여 파일 공유 관리](storage-how-to-use-files-portal.md)
- [Azure CLI를 사용하여 파일 공유 관리](storage-how-to-use-files-cli.md)
- [저장소 탐색기를 사용하여 파일 공유 관리](storage-how-to-use-files-storage-explorer.md)
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
