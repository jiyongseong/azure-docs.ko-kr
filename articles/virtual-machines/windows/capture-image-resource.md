---
title: Azure에서 관리 이미지 만들기 | Microsoft Docs
description: Azure에서 일반화된 VM 또는 VHD의 관리 이미지를 만듭니다. 이미지를 사용하여 관리 디스크를 사용하는 여러 VM을 만들 수 있습니다.
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
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 4445787fd559c6d0a6dfc891910cb9a139a6907e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure에서 일반화된 VM의 관리 이미지 만들기

저장소 계정에 관리 디스크 또는 비관리 디스크로 저장되는 일반화된 VM으로 관리 이미지 리소스를 만들 수 있습니다. 여러 VM을 만드는 데 이미지를 사용할 수 있습니다. 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep을 사용하여 Windows VM 일반화

Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> VM에 대해 sysprep을 실행한 경우 *일반화* 상태로 간주되며 다시 시작할 수 없습니다. VM 일반화 프로세스는 되돌릴 수 없습니다. 원래 VM이 작동되도록 해야 할 경우 [VM의 복사본](create-vm-specialized.md#option-3-copy-an-existing-azure-vm)을 만들고 복사본을 일반화합니다. 
>
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


## <a name="create-a-managed-image-in-the-portal"></a>포털에서 관리 이미지 만들기 

1. [포털](https://portal.azure.com)을 엽니다.
2. 왼쪽의 메뉴에서 Virtual Machines를 클릭하고 목록에서 VM을 선택합니다.
3. VM에 대한 페이지의 위쪽 메뉴에서 **캡처**를 클릭합니다.
3. **이름**에 이미지에 사용할 이름을 입력합니다.
4. **리소스 그룹**에서 **새로 만들기**를 선택하고 이름을 입력하거나 **기존 항목**을 선택하고 드롭다운 목록에서 사용할 리소스 그룹을 선택합니다.
5. 이미지가 만들어진 후 원본 VM을 삭제하려면 **이미지를 만든 후 이 가상 머신을 자동으로 삭제**를 선택합니다.
6. 완료하면 **만들기**를 클릭합니다.
16. 이미지가 생성되면 리소스 그룹의 리소스 목록에 **이미지** 리소스로 표시될 것입니다.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Powershell을 사용하여 VM 이미지 만들기

VM에서 직접 이미지를 만들면 OS 디스크와 데이터 디스크를 포함하여 VM에 연결된 모든 디스크가 이미지에 포함됩니다. 이 예제에서는 VM에서 관리되는 디스크를 사용하는 관리되는 이미지를 만드는 방법을 보여 줍니다.


시작하기 전에 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 이 문서에서는 AzureRM 모듈 버전 5.7.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.


> [!NOTE]
> 이미지를 영역 중복 저장소에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 이미지 구성에서 `-ZoneResilient` 매개 변수를 포함하는 지역에 만들어야 합니다.


1. 일부 변수를 만듭니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM의 할당이 취소되었는지 확인합니다.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 가상 머신의 상태를 **일반화됨**으로 설정합니다. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 가상 머신을 가져옵니다. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Powershell을 사용하여 관리되는 디스크에서 이미지 만들기

OS 디스크의 이미지만 만들려면 관리 디스크 ID를 OS 디스크로 지정하여 이미지를 만들 수도 있습니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. VM을 가져옵니다.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 관리되는 디스크의 ID를 가져옵니다.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Powershell을 사용하여 스냅숏에서 이미지 만들기

일반화된 VM의 스냅숏에서 관리되는 이미지를 만들 수도 있습니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 스냅숏을 가져옵니다.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>저장소 계정의 VHD에서 이미지 만들기

저장소 계정의 일반화된 OS VHD에서 관리되는 이미지를 만듭니다. 저장소 계정에 있는 VHD의 URI가 필요합니다. https://*mystorageaccount*.blob.core.windows.net/*container*/*vhd_filename.vhd* 형식을 사용해야 합니다. 이 예제에서 사용하는 VHD는 *vhdcontainer*라는 컨테이너의 *mystorageaccount*에 있으며 VHD 파일 이름은 *osdisk.vhd*입니다.


1.  먼저, 공통 매개 변수를 설정합니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. VM을 중지/할당 취소합니다.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM을 일반화됨으로 표시합니다.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  일반화된 OS VHD를 사용하여 이미지를 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>다음 단계
- 이제 [일반화된 관리 이미지로 VM 만들기](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행할 수 있습니다.  

