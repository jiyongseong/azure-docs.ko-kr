---
title: Azure에서 특수한 디스크로 VM 만들기 | Microsoft Docs
description: Resource Manager 배포 모델에서 특수한 비관리 디스크를 연결하여 새 VM을 만듭니다.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: da1fa2b182888e623f8df734c9119e208433e2bd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012712"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>저장소 계정의 특수한 VHD에서 VM 만들기

Powershell을 사용하여 특수한 비관리 디스크를 OS 디스크로 연결하여 새 VM을 만듭니다. 특수한 디스크는 기존 VM의 VHD 복사본으로 사용자 계정, 응용 프로그램 및 원본 VM의 기타 상태 데이터를 유지합니다. 

다음 두 가지 옵션을 사용할 수 있습니다.
* [VHD 업로드](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [기존 Azure VM의 VHD 복사](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>시작하기 전에
PowerShell을 사용하는 경우 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute 
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.


## <a name="option-1-upload-a-specialized-vhd"></a>옵션 1: 특수한 VHD 업로드

Hyper-V와 같은 온-프레미스 가상화 도구를 사용하여 만든 특수한 VM 또는 다른 클라우드에서 내보낸 VM에서 VHD를 업로드할 수 있습니다.

### <a name="prepare-the-vm"></a>VM 준비
온-프레미스 VM을 사용하여 만든 특수한 VHD 또는 다른 클라우드에서 내보낸 VHD를 업로드할 수 있습니다. 특수한 VHD는 사용자 계정, 응용 프로그램 및 원본 VM의 다른 상태 데이터를 유지 관리합니다. 새 VM을 만드는데 VHD를 그대로 사용하려는 경우 다음 단계가 완료되었는지 확인합니다. 
  
  * [Azure에 업로드할 Windows VHD를 준비합니다](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sysprep을 사용하여 VM을 일반화하지 **마십시오**.
  * 모든 게스트 가상화 도구 및 VM에 설치된 에이전트를 제거합니다(예: VMware 도구).
  * VM이 DHCP를 통해 해당 IP 주소 및 DNS 설정을 가져오도록 구성되었는지 확인합니다. 이렇게 하면 서버를 시작할 때 VNet 내의 IP 주소를 가져옵니다. 


### <a name="get-the-storage-account"></a>저장소 계정 가져오기
업로드한 VM 이미지를 저장할 Azure 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 계정을 만들 수 있습니다. 

사용 가능한 저장소 계정을 표시하려면 다음을 입력합니다.

```powershell
Get-AzureRmStorageAccount
```

기존 저장소 계정을 사용하려면 [VM 이미지 업로드](#upload-the-vm-vhd-to-your-storage-account) 섹션을 진행합니다.

저장소 계정을 만들어야 하는 경우 다음 단계를 따릅니다.

1. 저장소 계정을 만들어야 하는 리소스 그룹의 이름을 알아야 합니다. 구독의 모든 리소스 그룹을 찾으려면 다음을 입력합니다.
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    **미국 서부** 지역에 **myResourceGroup** 이라는 이름의 리소스 그룹을 만들려면 다음을 입력합니다.

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet을 사용하여 이 리소스 그룹에 **mystorageaccount**라는 이름의 저장소 계정을 만듭니다.
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>저장소 계정에 VHD 업로드
[Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet을 사용하여 저장소 계정의 컨테이너에 이미지를 업로드합니다. 이 예제에서는 `"C:\Users\Public\Documents\Virtual hard disks\"`에서 **myResourceGroup** 리소스 그룹의 **mystorageaccount**라는 저장소 계정에 파일 **myVHD.vhd**를 업로드합니다. 파일은 **mycontainer**라는 컨테이너에 배치되고 새 파일 이름은 **myUploadedVHD.vhd**가 됩니다.

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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>옵션 2: 기존 Azure VM에서 VHD 복사

중복된 새 VM을 만들 때 사용할 다른 저장소 계정에 VHD를 복사할 수 있습니다.

### <a name="before-you-begin"></a>시작하기 전에
다음 사항을 확인합니다.

* **원본 및 대상 저장소 계정**에 대한 정보가 있습니다. 원본 VM의 경우 저장소 계정 및 컨테이너 이름이 필요합니다. 일반적으로 컨테이너 이름은 **vhds**가 됩니다. 또한 대상 저장소 계정도 있어야 합니다. 아직 없는 경우 포털(**모든 서비스** > 저장소 계정 > 추가) 또는 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet을 사용하여 만들 수 있습니다. 
* [AzCopy 도구](../../storage/common/storage-use-azcopy.md)를 다운로드 및 설치했습니다. 

### <a name="deallocate-the-vm"></a>VM 할당을 취소합니다.
VM 할당을 취소하여 복사할 VHD를 해제합니다. 

* **포털**: **가상 머신** > **myVM** > 중지를 클릭합니다.
* **Powershell**: [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm)을 사용하여 **myResourceGroup** 리소스 그룹에서 **myVM**으로 명명된 VM을 중지(할당 취소)합니다.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Azure Portal의 VM에 대한 **상태**가 **중지됨**에서 **중지됨(할당 취소됨)** 으로 변경됩니다.

### <a name="get-the-storage-account-urls"></a>저장소 계정 URL 가져오기
원본 및 대상 저장소 계정의 URL이 필요합니다. URL은 `https://<storageaccount>.blob.core.windows.net/<containerName>/` 형태입니다. 저장소 계정 및 컨테이너 이름을 이미 알고 있는 경우 괄호 사이의 정보를 바꿔서 URL을 만듭니다. 

Azure 포털 또는 Azure PowerShell을 사용하여 URL을 가져옵니다.

* **포털**: **>** **모든 서비스** > **저장소 계정** > *저장소 계정* > **Blob**을 클릭하면 원본 VHD 파일이 **vhds** 컨테이너에 있을 것입니다. 컨테이너의 **속성**을 클릭하고 레이블이 **URL**인 텍스트를 복사합니다. 원본 및 대상 컨테이너의 URL이 모두 필요합니다. 
* **Powershell**: [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm)을 사용하여 **myResourceGroup** 리소스 그룹에서 **myVM**으로 명명된 VM에 대한 정보를 가져옵니다. 결과에서 **Vhd Uri**에 대한 **Storage 프로필** 섹션을 살펴봅니다. Uri의 첫 번째 부분은 컨테이너에 대한 URL이고 마지막 부분은 VM에 대한 OS VHD 이름입니다.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>저장소 액세스 키 가져오기
원본 및 대상 저장소 계정에 대한 액세스 키를 찾습니다. 액세스 키에 대한 자세한 내용은 [Azure 저장소 계정 방법](../../storage/common/storage-create-storage-account.md)을 참조하세요.

* **포털**: **모든 서비스** > **저장소 계정** > *저장소 계정* > **액세스 키**를 클릭합니다. **key1**로 레이블이 지정된 키를 복사합니다.
* **Powershell**: [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey)를 사용하여 **myResourceGroup** 리소스 그룹에서 **mystorageaccount** 저장소 계정에 대한 저장소 키를 가져옵니다. **key1**로 레이블이 지정된 키를 복사합니다.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>VHD 복사
AzCopy를 사용하여 저장소 계정 간에 파일을 복사할 수 있습니다. 대상 컨테이너에 대해 지정된 컨테이너가 존재하지 않는 경우 컨테이너가 만들어집니다. 

AzCopy를 사용하려면 로컬 컴퓨터에서 명령 프롬프트를 열고 AzCopy가 설치된 폴더로 이동합니다. 이 폴더는 *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*와 유사합니다. 

컨테이너 내의 모든 파일을 복사하려면 **/S** 스위치를 사용합니다. 동일한 컨테이너에 있는 경우 OS VHD 및 모든 데이터 디스크를 복사하는 데 사용할 수 있습니다. 이 예에서는 **mysourcestorageaccount** 저장소 계정의 **mysourcecontainer** 컨테이너에 있는 모든 파일을 **mydestinationstorageaccount** 저장소 계정의 **mydestinationcontainer** 컨테이너로 복사하는 방법을 보여 줍니다. 저장소 계정 및 컨테이너의 이름을 사용자 고유 값으로 바꿉니다. `<sourceStorageAccountKey1>` 및 `<destinationStorageAccountKey1>`을 사용자 고유 키로 바꿉니다.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

여러 파일과 함께 컨테이너에서 특정 VHD를 복사하려면 /Pattern 스위치를 사용하여 파일 이름을 지정할 수도 있습니다. 이 예에서는 **myFileName.vhd**라는 파일만 복사됩니다.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


완료되면 다음과 유사한 메시지를 받게 됩니다.

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>문제 해결
* AZCopy를 사용하는 경우 “서버가 요청을 인증하지 못했습니다.”라는 오류 메시지가 표시되면 권한 부여 헤더의 값이 서명을 포함하여 올바른 형식으로 지정되었는지 확인합니다. 키 2 또는 보조 저장소 키를 사용 중인 경우 주 또는 첫 번째 저장소 키를 사용해 보세요.

## <a name="create-the-new-vm"></a>새 VM 만들기 

새 VM에서 사용할 네트워킹 및 기타 VM 리소스를 만들어야 합니다.

### <a name="create-the-subnet-and-vnet"></a>서브넷 및 VNet 만들기

[가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 VNet 및 서브넷을 만듭니다.

1. 서브넷을 만듭니다. 이 예제에서는 **myResourceGroup** 리소스 그룹에 **mySubNet**으로 명명된 서브넷을 만들고 **10.0.0.0/24**에 대한 서브넷 주소 접두사를 설정합니다.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. VNet을 만듭니다. 이 예제에서는 가상 네트워크 이름을 **myVnetName**으로, 위치를 **미국 서부**로, 가상 네트워크의 주소 접두사를 **10.0.0.0/16**으로 설정합니다. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>네트워크 보안 그룹 및 RDP 규칙 만들기
RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 보안 규칙이 필요합니다. 새 VM의 VHD가 기존의 특수한 VM에서 생성되었기 때문에 VM이 만들어진 후에 RDP를 사용하여 로그온할 사용 권한을 갖고 있던 원본 가상 머신에서 기존 계정을 사용할 수 있습니다.
연결될 네트워크 인터페이스를 만들기 전에 완료해야 합니다.  
이 예제에서는 NSG 이름을 **myNsg**로 설정하고 RDP 규칙 이름을 **myRdpRule**로 설정합니다.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

끝점 및 NSG 규칙에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

### <a name="create-a-public-ip-address-and-nic"></a>공용 IP 주소 및 NIC 만들기
가상 네트워크에서 가상 머신과 통신하려면 [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. 공용 IP를 만듭니다. 이 예제에서는 공용 IP 주소 이름을 **myIP**로 설정합니다.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 만들기. 이 예제에서는 NIC 이름을 **myNicName**으로 설정합니다. 또한 이번 단계에서는 앞에서 만든 네트워크 보안 그룹을 이 NIC와 연결합니다.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>VM 이름 및 크기 설정

이 예에서는 VM 이름을 "myVM"으로 설정하고 VM 크기를 "Standard_A2"로 설정합니다.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>NIC 추가
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>OS 디스크 구성

1. 업로드하거나 복사한 VHD의 URI를 설정합니다. 이 예에서는 **myOsDisk.vhd**라는 VHD 파일을 **myContainer**라는 컨테이너의 **myStorageAccount**라는 저장소 계정에 보관합니다.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. OS 디스크를 추가합니다. 이 예에서는 OS 디스크가 만들어지면 VM 이름에 "osDisk"가 추가되어 OS 디스크 이름이 완성됩니다. 또한 이 예에서는 이 Windows 기반 VHD를 VM에 OS 디스크로 연결해야 하는 것으로 지정합니다.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

선택 사항: VM에 연결해야 하는 데이터 디스크가 있는 경우 데이터 VHD의 URL과 적절한 LUN(논리 단위 번호)을 사용하여 데이터 디스크를 추가합니다.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

저장소 계정을 사용하는 경우 데이터 및 운영 체제 디스크 URL은 `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` 형식입니다. 포털에서 대상 저장소 컨테이너로 이동하고 운영 체제 또는 복사된 데이터 VHD를 클릭한 다음 URL의 내용을 복사하여 이 내용을 찾을 수 있습니다.


### <a name="complete-the-vm"></a>VM 완료 

방금 만든 구성을 사용하여 VM을 만듭니다.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

이 명령에 성공할 경우 다음과 유사한 출력이 표시됩니다.

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
새로 만든 VM은 [Azure Portal](https://portal.azure.com)에서 **모든 서비스** > **가상 머신**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>다음 단계
새 가상 머신에 로그인합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](connect-logon.md)을 참조하세요.

