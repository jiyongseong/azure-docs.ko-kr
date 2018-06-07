---
title: Azure에서 일반화된 VM의 관리되지 않는 이미지 만들기 | Microsoft Docs
description: Azure에서 VM의 여러 복사본을 만드는 데 사용할 일반화된 Windows VM의 관리되지 않는 이미지를 만듭니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: b416acd9a2a3b03502b7eca11eade9dbd56f3afe
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072052"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Azure VM에서 관리되지 않는 VM 이미지를 만드는 방법

이 문서에서는 저장소 계정을 사용하여 설명합니다. 저장소 계정 대신 관리 디스크 및 관리되는 이미지를 사용하는 것이 좋습니다. 자세한 내용은 [Azure에서 일반화된 VM의 관리되는 이미지 캡처](capture-image-resource.md)를 참조하세요.

이 문서에서는 저장소 계정을 사용하여 일반화된 Azure VM 이미지를 만드는 데 Azure PowerShell을 사용하는 방법을 보여 줍니다. 그런 다음 이미지를 사용하여 다른 VM을 만들 수 있습니다. 이 이미지에는 OS 디스크를 비롯해 가상 머신에 연결된 데이터 디스크가 포함됩니다. 새 VM을 만들 때 해당 리소스를 설정해야 하므로 이미지는 가상 네트워크 리소스를 포함하지 않습니다. 

## <a name="prerequisites"></a>필수 조건
Azure PowerShell 버전 1.0.x 이상을 설치해야 합니다. PowerShell을 아직 설치하지 않은 경우 설치 단계에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 을 참조하세요.

## <a name="generalize-the-vm"></a>VM 일반화 
이 섹션에서는 이미지로 사용하기 위해 Windows 가상 머신을 일반화하는 방법을 보여 줍니다. VM을 일반화하면 여러 정보 중에서 모든 개인 계정 정보가 제거되고 이미지로 사용할 컴퓨터가 준비됩니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Azure에 VHD를 처음으로 업로드하는 경우 Sysprep을 실행하기 전에 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 
> 
> 

`sudo waagent -deprovision+user`을 사용하여 Linux VM을 일반화한 다음 PowerShell을 사용하여 VM을 캡처할 수 있습니다. VM을 캡처하는 데 CLI를 사용하는 방법에 대한 정보는 [Azure CLI를 사용하여 Linux 가상 컴퓨터를 일반화하고 캡처하는 방법](../linux/capture-image.md)을 참조하세요.


1. Windows 가상 컴퓨터에 로그인
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\system32\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 클릭합니다.
   
    ![Sysprep 시작](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep이 완료되면 가상 컴퓨터를 종료합니다. 

> [!IMPORTANT]
> Azure에 VHD를 업로드하거나 VM에서 이미지를 만드는 작업을 완료할 때까지 VM을 다시 시작하지 않습니다. VM이 실수로 다시 시작되면 Sysprep을 실행하여 다시 일반화합니다.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Azure PowerShell에 로그인합니다.
1. Azure PowerShell을 열고 Azure 계정에 로그인합니다.
   
    ```powershell
    Connect-AzureRmAccount
    ```
   
    Azure 계정 자격 증명을 입력하기 위한 팝업 창이 열립니다.
2. 사용 가능한 구독에 대한 구독을 ID를 가져옵니다.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 구독 ID를 사용하여 올바른 구독을 설정합니다.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>VM의 할당을 취소하고 상태를 일반화됨으로 설정합니다.

> [!IMPORTANT] 
> 일반화됨으로 표시되면 VM에서 태그를 추가하거나, 편집하거나, 제거할 수 없습니다. VM에 태그를 추가하려는 경우 일반화됨으로 표시하기 전에 태그를 추가해야 합니다.
> 

1. VM 리소스 할당을 취소합니다.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure Portal의 VM에 대한 *상태*가 **중지됨**에서 **중지됨(할당 취소됨)** 으로 변경됩니다.
2. 가상 머신의 상태를 **일반화됨**으로 설정합니다. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. VM의 상태를 확인합니다. VM에 대한 **OSState/일반화됨** 섹션은 **DisplayStatus**를 **VM 일반화됨**으로 설정해야 합니다.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>이미지 만들기

이 명령을 사용하여 대상 저장소 컨테이너에서 관리되지 않는 가상 머신 이미지를 만듭니다. 이미지는 원래 가상 컴퓨터와 동일한 저장소 계정에 만들어집니다. `-Path` 매개 변수는 원본 VM에 대한 JSON 템플릿의 복사본을 로컬 컴퓨터에 저장합니다. `-DestinationContainerName` 매개 변수는 이미지를 유지할 컨테이너의 이름입니다. 컨테이너가 없으면 컨테이너가 만들어집니다.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
JSON 파일 템플릿에서 이미지의 URL을 얻을 수 있습니다. 이미지의 전체 경로에 대한 **resources** > **storageProfile** > **osDisk** > **image** > **uri** 섹션으로 이동합니다. 이미지의 URL은 `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`과 같습니다.
   
포털에서 URI를 확인할 수도 있습니다. 이미지는 저장소 계정에서 **시스템** 라는 컨테이너에 복사됩니다. 

## <a name="create-a-vm-from-the-image"></a>이미지에서 VM 만들기

이제 관리되지 않는 이미지에서 하나 이상의 VM을 만들 수 있습니다.

### <a name="set-the-uri-of-the-vhd"></a>VHD의 URI 설정

VHD에 대한 URI는 형식: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. 이 예에서는 **myVHD**로 명명된 VHD가 **mycontainer** 컨테이너의 **mystorageaccount** 저장소 계정에 있습니다.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 vNet 및 서브넷을 만듭니다.

1. 서브넷을 만듭니다. 다음 예제에서는 **10.0.0.0/24** 주소 접두사가 있는 **myResourceGroup** 리소스 그룹에 **mySubnet**으로 명명된 서브넷을 만듭니다.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 가상 네트워크 만들기 다음 예제에서는 **10.0.0.0/16** 주소 접두사가 있는 **미국 서부** 위치에 **myVnet**으로 명명된 가상 네트워크를 만듭니다.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>공용 IP 주소 및 네트워크 인터페이스 만들기
가상 네트워크에서 가상 머신과 통신하려면 [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. 공용 IP 주소 만들기. 이 예에서는 **myPip**라는 공용 IP 주소를 만듭니다. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 만들기. 이 예에서는 **myNic**라는 NIC를 만듭니다. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>네트워크 보안 그룹 및 RDP 규칙 만들기
RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 보안 규칙이 필요합니다. 

이 예에서는 포트 3389를 통한 RDP 트래픽을 허용하는 **myRdpRule**이라는 규칙을 포함하는 **myNsg**로 명명된 NSG를 만듭니다. NSG에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>가상 네트워크에 대한 변수 만들기
완료된 가상 네트워크에 대한 변수를 만듭니다. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>VM 만들기
다음 PowerShell에서는 가상 머신 구성을 완료하고 관리되지 않는 이미지를 새 설치에 대한 소스로 사용합니다.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

### <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
완료되면 새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 머신**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>다음 단계
Azure PowerShell을 사용하여 새 가상 머신을 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 머신 관리](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.


