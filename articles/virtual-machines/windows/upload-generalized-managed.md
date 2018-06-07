---
title: 일반화된 온-프레미스 VHD에서 관리되는 Azure VM 만들기 | Microsoft Docs
description: 일반화된 VHD를 Azure에 업로드하고 이를 사용하여 Resource Manager 배포 모델에서 새 VM을 만듭니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f2f5eae8a4512595457d92d17832cf462b4bec4
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012600"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>일반화된 VHD를 업로드하고 사용하여 Azure에서 새 VM 만들기

이 항목에서는 PowerShell을 사용하여 일반화된 VM의 VHD를 Azure에 업로드하고, VHD에서 이미지를 만들고, 해당 이미지에서 새 VM을 만드는 과정을 안내합니다. 다른 클라우드 또는 온-프레미스 가상화 도구에서 내보낸 VHD를 업로드할 수 있습니다. 새 VM에 대해 [Managed Disks](managed-disks-overview.md)를 사용하면 VM 관리를 간소화하고 VM이 가용성 집합에 배치되는 경우 가용성이 증가됩니다. 

샘플 스크립트를 사용하려면 [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

- Azure에 VHD를 업로드하기 전에 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행해야 합니다.
- [Managed Disks](managed-disks-overview.md)로 마이그레이션을 시작하기 전에 [Managed Disks로 마이그레이션하기 위한 계획](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)을 검토하세요.
- 이 문서에서는 AzureRM 모듈 버전 5.6 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM.Compute`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.


## <a name="generalize-the-source-vm-using-sysprep"></a>Sysprep을 사용하여 원본 VM 일반화

Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Azure에 VHD를 업로드하기 전에 Sysprep을 처음으로 실행하는 경우 Sysprep을 실행하기 전에 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 
> 
> 

1. Windows 가상 머신에 로그인
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\system32\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 클릭합니다.
   
    ![Sysprep 시작](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep이 완료되면 가상 머신을 종료합니다. VM을 다시 시작하지 않습니다.


## <a name="get-the-storage-account"></a>저장소 계정 가져오기

업로드한 VM 이미지를 저장할 Azure 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 계정을 만들 수 있습니다. 

VHD를 사용하여 VM의 Managed Disk를 만드는 경우 저장소 계정 위치가 VM을 만들 위치와 동일해야 합니다.

사용 가능한 저장소 계정을 표시하려면 다음을 입력합니다.

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>저장소 계정에 VHD 업로드

[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet을 사용하여 저장소 계정의 컨테이너에 VHD를 업로드합니다. 이 예제에서는 *"C:\Users\Public\Documents\Virtual hard disks\"* 에서 *myResourceGroup* 리소스 그룹의 *mystorageaccount*라는 저장소 계정에 파일 *myVHD.vhd*를 업로드합니다. 파일은 *mycontainer*라는 컨테이너에 배치되고 새 파일 이름은 *myUploadedVHD.vhd*가 됩니다.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


성공하면 다음과 유사한 응답을 얻게 됩니다.

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

이 명령은 네트워크 연결 및 VHD 파일의 크기에 따라 완료하는 데 다소 시간이 걸립니다.

### <a name="other-options-for-uploading-a-vhd"></a>VHD를 업로드하기 위한 기타 옵션
 
또한 다음 방법 중 하나를 사용하여 저장소 계정에 VHD를 업로드할 수 있습니다.

- [AZCopy](http://aka.ms/downloadazcopy)
- [Azure Storage Blob 복사 API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure Storage 탐색기 Blob 업로드](https://azurestorageexplorer.codeplex.com/)
- [저장소 Import/Export 서비스 REST API 참조](https://msdn.microsoft.com/library/dn529096.aspx)
-   예상 업로드 시간이 7일보다 긴 경우 Import/Export 서비스를 사용하는 것이 좋습니다. [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html)를 사용하여 데이터 크기 및 전송 단위로 시간을 예측할 수 있습니다. 
    Import/Export를 사용하여 표준 저장소 계정을 복사할 수 있습니다. AzCopy와 같은 도구를 사용하여 표준 저장소에서 프리미엄 저장소 계정으로 복사해야 합니다.

> [!IMPORTANT]
> AzCopy를 사용하여 VHD를 Azure에 업로드하는 경우, 업로드 스크립트를 실행하기 전에 [/BlobType:page](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append)를 설정했는지 확인합니다. 대상이 Blob인데 이 옵션을 지정하지 않으면 기본적으로 AzCopy는 블록 Blob를 만듭니다.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>업로드된 VHD에서 관리되는 이미지 만들기 

일반화된 OS VHD를 사용하여 관리 이미지를 만듭니다. 사용자 고유의 정보로 값을 대체합니다.


먼저, 매개 변수를 설정합니다.

```powershell
$location = "East US" 
$imageName = "myImage"
```

일반화된 OS VHD를 사용하여 이미지를 만듭니다.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>VM 만들기

이미지가 생겼으니, 이 이미지에서 하나 이상의 새 VM을 만들 수 있습니다. 이 예에서는 *myResourceGroup*에 *myImage*에서 *myVM*이라는 VM을 만듭니다.


```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>다음 단계

새 가상 머신에 로그인합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 

