---
title: Azure의 특수한 VHD에서 Windows VM 만들기 | Microsoft Docs
description: Resource Manager 배포 모델에서 특수한 관리 디스크를 OS 디스크로 연결하여 새 Windows VM을 만듭니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: be7933b038fb5a648249e9b0c73415bff778930b
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012787"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>PowerShell을 사용하여 특수 디스크에서 Windows VM 만들기

특수 관리 디스크를 OS 디스크로 연결하여 새 VM을 만듭니다. 특수한 디스크는 기존 VM의 VHD(가상 하드 디스크) 복사본으로 사용자 계정, 응용 프로그램 및 원본 VM의 기타 상태 데이터를 유지합니다. 

특수한 VHD를 사용하여 새 VM을 만드는 경우 새 VM은 원본 VM의 컴퓨터 이름을 그대로 유지합니다. 다른 컴퓨터 관련 정보도 유지되며, 경우에 따라 이 중복된 정보로 인해 문제가 발생할 수 있습니다. VM을 복사할 때 응용 프로그램이 어떤 유형의 컴퓨터 관련 정보에 의존하는지 알아야 합니다.

여러 옵션이 있습니다.
* [기존 관리 디스크 사용](#option-1-use-an-existing-disk). 이 옵션은 올바르게 작동하지 않는 VM이 있는 경우에 유용합니다. VM을 삭제하고 관리 디스크를 다시 사용하여 새 VM을 만들 수 있습니다. 
* [VHD 업로드](#option-2-upload-a-specialized-vhd) 
* [스냅숏을 사용하여 기존 Azure VM 복사](#option-3-copy-an-existing-azure-vm)

Azure Portal을 사용하여 [특수 VHD에서 새 VM을 만들](create-vm-specialized-portal.md) 수도 있습니다.

이 항목에서는 관리 디스크를 사용하는 방법을 보여 줍니다. 저장소 계정을 사용해야 하는 레거시 배포가 있는 경우 [저장소 계정의 특수한 VHD에서 VM 만들기](sa-create-vm-specialized.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
PowerShell을 사용하는 경우 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.

## <a name="option-1-use-an-existing-disk"></a>옵션 1: 기존 디스크 사용

삭제한 VM이 있고 OS 디스크를 다시 사용하여 새 VM을 만들려는 경우 [Get-AzureRmDisk](/azure/powershell/get-azurermdisk)를 사용합니다.

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
이제 이 디스크를 [새 VM](#create-the-new-vm)에 OS 디스크로 연결할 수 있습니다.

## <a name="option-2-upload-a-specialized-vhd"></a>옵션 2: 특수 VHD 업로드

Hyper-V와 같은 온-프레미스 가상화 도구를 사용하여 만든 특수한 VM 또는 다른 클라우드에서 내보낸 VM에서 VHD를 업로드할 수 있습니다.

### <a name="prepare-the-vm"></a>VM 준비
새 VM을 만드는데 VHD를 그대로 사용하려는 경우 다음 단계가 완료되었는지 확인합니다. 
  
  * [Azure에 업로드할 Windows VHD를 준비합니다](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sysprep을 사용하여 VM을 일반화하지 **마십시오**.
  * 모든 게스트 가상화 도구 및 VM에 설치된 에이전트를 제거합니다(예: VMware 도구).
  * VM이 DHCP를 통해 해당 IP 주소 및 DNS 설정을 가져오도록 구성되었는지 확인합니다. 이렇게 하면 서버를 시작할 때 VNet 내의 IP 주소를 가져옵니다. 


### <a name="get-the-storage-account"></a>저장소 계정 가져오기
업로드한 VHD를 저장할 Azure Storage 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 계정을 만들 수 있습니다. 

사용 가능한 저장소 계정을 표시하려면 다음을 입력합니다.

```powershell
Get-AzureRmStorageAccount
```

기존 저장소 계정을 사용하려면 [VHD 업로드](#upload-the-vhd-to-your-storage-account) 섹션을 진행합니다.

저장소 계정을 만들어야 하는 경우 다음 단계를 따릅니다.

1. 저장소 계정을 만들어야 하는 리소스 그룹의 이름을 알아야 합니다. 구독의 모든 리소스 그룹을 찾으려면 다음을 입력합니다.
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    *미국 서부* 지역에 *myResourceGroup* 이라는 이름의 리소스 그룹을 만들려면 다음을 입력합니다.

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet을 사용하여 이 리소스 그룹에 *mystorageaccount*라는 이름의 저장소 계정을 만듭니다.
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>저장소 계정에 VHD 업로드 
[Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet을 사용하여 저장소 계정의 컨테이너에 VHD를 업로드합니다. 이 예제에서는 `"C:\Users\Public\Documents\Virtual hard disks\"`에서 *myResourceGroup* 리소스 그룹의 *mystorageaccount*라는 저장소 계정에 파일 *myVHD.vhd*를 업로드합니다. 파일은 *mycontainer*라는 컨테이너에 저장되고 새 파일 이름은 *myUploadedVHD.vhd*가 됩니다.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
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

### <a name="create-a-managed-disk-from-the-vhd"></a>VHD에서 관리 디스크를 만들려면

[New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk)를 사용하여 사용자의 저장소 계정에 있는 특수한 VHD로 관리 디스크를 만듭니다. 이 예제에서는 디스크 이름에 **myOSDisk1**을 사용하고, 디스크를 *Standard_LRS* 저장소에 배치하고, *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd*을 원본 VHD의 URI로 사용합니다.

새 VM에 대한 새 리소스 그룹을 만듭니다.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

업로드된 VHD에서 새 OS 디스크를 만듭니다. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>옵션 3: 기존 Azure VM 복사

VM의 스냅숏을 만든 다음 이 스냅숏을 사용하여 새 관리 디스크 및 새 VM을 만드는 방식으로 관리 디스크를 사용하는 VM의 복사본을 만들 수 있습니다.


### <a name="take-a-snapshot-of-the-os-disk"></a>OS 디스크의 스냅숏 만들기

전체 VM(모든 디스크 포함) 또는 단일 디스크의 스냅숏을 만들 수 있습니다. 다음 단계에서는 [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet을 사용하여 VM의 OS 디스크에 대한 스냅숏을 만드는 방법을 보여 줍니다. 

일부 매개 변수를 설정합니다. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

VM 개체를 가져옵니다.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
OS 디스크 이름을 가져옵니다.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

스냅숏 구성을 만듭니다. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

스냅숏을 만듭니다.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


스냅숏을 사용하여 고성능이 필요한 VM을 만들려면 `-AccountType Premium_LRS` 매개 변수와 New-AzureRmSnapshot 명령을 사용합니다. 매개 변수는 스냅숏을 만들어서 프리미엄 Managed Disk로 저장되도록 합니다. 프리미엄 Managed Disks는 표준 Managed Disks보다 비용이 많이 듭니다. 따라서 해당 매개 변수를 사용하기 전에 프리미엄이 필요한지 확인합니다.

### <a name="create-a-new-disk-from-the-snapshot"></a>스냅숏에서 새 디스크 만들기

[New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk)를 사용하여 스냅숏에서 관리 디스크를 만듭니다. 이 예제에서는 디스크 이름에 *myOSDisk*를 사용합니다.

새 VM에 대한 새 리소스 그룹을 만듭니다.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

OS 디스크 이름을 설정합니다. 

```powershell
$osDiskName = 'myOsDisk'
```

관리 디스크를 만듭니다.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>새 VM 만들기 

새 VM에서 사용할 네트워킹 및 기타 VM 리소스를 만듭니다.

### <a name="create-the-subnet-and-vnet"></a>서브넷 및 VNet 만들기

[가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 VNet 및 서브넷을 만듭니다.

서브넷을 만듭니다. 이 예제에서는 **myDestinationResourceGroup** 리소스 그룹에 **mySubNet**으로 명명된 서브넷을 만들고 **10.0.0.0/24**에 대한 서브넷 주소 접두사를 설정합니다.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

VNet을 만듭니다. 이 예제에서는 가상 네트워크 이름을 **myVnetName**으로, 위치를 **미국 서부**로, 가상 네트워크의 주소 접두사를 **10.0.0.0/16**으로 설정합니다. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>네트워크 보안 그룹 및 RDP 규칙 만들기
RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 보안 규칙이 필요합니다. 새 VM의 VHD가 기존의 특수한 VM에서 생성되었기 때문에 원본 가상 머신의 계정을 RDP에 사용할 수 있습니다.

이 예제에서는 NSG 이름을 **myNsg**로 설정하고 RDP 규칙 이름을 **myRdpRule**로 설정합니다.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

끝점 및 NSG 규칙에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

### <a name="create-a-public-ip-address-and-nic"></a>공용 IP 주소 및 NIC 만들기
가상 네트워크에서 가상 머신과 통신하려면 [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

공용 IP를 만듭니다. 이 예제에서는 공용 IP 주소 이름을 **myIP**로 설정합니다.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

NIC 만들기. 이 예제에서는 NIC 이름을 **myNicName**으로 설정합니다.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>VM 이름 및 크기 설정

이 예에서는 VM 이름을 *myVM*으로 설정하고 VM 크기를 *Standard_A2*로 설정합니다.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>NIC 추가
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>OS 디스크 추가 

[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk)를 사용하여 OS 디스크를 구성에 추가합니다. 이 예에서는 디스크 크기를 *128GB*로 설정하고 Managed Disk를 *Windows* OS 디스크로 연결합니다.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>VM 완료 

방금 만든 [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) 구성을 사용하여 VM을 만듭니다.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

이 명령에 성공할 경우 다음과 유사한 출력이 표시됩니다.

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
새로 만든 VM은 [Azure Portal](https://portal.azure.com)에서 **찾아보기** > **가상 머신**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>다음 단계
새 가상 머신에 로그인합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](connect-logon.md)을 참조하세요.

