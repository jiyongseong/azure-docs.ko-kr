---
title: 자습서 - Azure PowerShell을 사용하여 Windows VM 만들기 및 관리 | Microsoft Docs
description: 이 자습서에서는 Azure PowerShell을 사용하여 Azure에서 Windows VM을 만들고 관리하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2a1d89b1e1b7c398ae05fef5577bb1631409631b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211109"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Windows VM 만들기 및 관리

Azure Virtual Machines는 완전히 구성 가능하고 유연한 컴퓨팅 환경을 제공합니다. 이 자습서에서는 VM 크기 선택, VM 이미지 선택 및 VM 배포 등 기본적인 Azure Virtual Machines 배포 항목에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * VM 크기 조정
> * VM 상태 보기 및 이해

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure PowerShell 모듈 버전이 5.7.0 이상이어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령으로 리소스 그룹을 만듭니다. 

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신보다 먼저 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *EastUS* 지역에 *myResourceGroupVM*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

리소스 그룹은 VM을 만들거나 수정할 때 지정되며 이 자습서 전체에서 확인할 수 있습니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

가상 머신을 만들 때 운영 체제 이미지, 네트워크 구성 및 관리 자격 증명과 같은 몇 가지 옵션을 사용할 수 있습니다. 이 예제에서는 최신의 Windows Server 2016 Datacenter 기본 버전을 실행하는 *myVM*이라는 가상 머신이 만들어집니다.

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 가상 머신의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 머신을 만듭니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>VM에 연결

배포가 완료된 후 가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

다음 명령을 실행하여 가상 머신의 공용 IP 주소를 반환합니다. 다음 단계에서 웹 연결을 테스트하기 위해 브라우저와 연결할 수 있도록 이 IP 주소를 기록해 둡니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

다음 명령을 사용하여 로컬 컴퓨터에서 가상 머신과의 원격 데스크톱 세션을 만듭니다. 해당 IP 주소를 가상 머신의 *publicIPAddress*로 바꿉니다. 가상 머신을 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```powershell
mstsc /v:<publicIpAddress>
```

**Windows 보안** 창에서 **추가 선택 사항** 및 **다른 계정 사용**을 차례로 선택합니다. 가상 머신에 대해 만든 사용자 이름 및 암호를 입력한 다음, **확인**을 클릭합니다.

## <a name="understand-vm-images"></a>VM 이미지 이해

Azure Marketplace에는 새 가상 컴퓨터를 만드는 데 사용할 수 있는 여러 가상 컴퓨터 이미지가 포함되어 있습니다. 이전 단계에서는 Windows Server 2016 Datacenter 이미지를 사용하여 가상 머신을 만들었습니다. 이 단계에서는 PowerShell 모듈을 사용하여 Marketplace에서 새 VM의 기반이 될 수도 있는 다른 Windows 이미지를 검색합니다. 이 프로세스는 이미지를 [식별](cli-ps-findimage.md#terminology)하기 위한 게시자, 제품, SKU 및 버전 번호(선택 사항)로 구성되어 있습니다. 

[Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) 명령을 사용하여 이미지 게시자 목록을 반환합니다.

```powershell
Get-AzureRmVMImagePublisher -Location "EastUS"
```

이미지 제안 목록을 반환하려면 [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer)를 사용합니다. 이 명령을 사용하면 반환된 목록이 지정된 게시자를 기준으로 필터링됩니다.

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

그런 다음 [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) 명령으로 게시자 및 제안 이름을 기준으로 필터링하여 이미지 이름 목록을 반환합니다.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

이 정보를 사용하여 특정 이미지가 있는 VM을 배포할 수 있습니다. 이 예제에서는 컨테이너 이미지가 있는 Windows Server 2016의 최신 버전을 사용하여 가상 머신을 배포합니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

`-AsJob` 매개 변수는 VM을 백그라운드 작업으로 만들므로 PowerShell 프롬프트가 반환됩니다. `Get-Job` cmdlet을 사용하여 백그라운드 작업의 세부 정보를 볼 수 있습니다.


## <a name="understand-vm-sizes"></a>VM 크기 이해

가상 머신 크기에 따라 CPU, GPU, 메모리 등 가상 머신에 사용할 수 있는 계산 리소스의 양이 결정됩니다. Virtual Machines는 예상되는 워크로드에 맞는 크기로 만들어야 합니다. 워크로드가 증가할 경우 기존 가상 머신의 크기를 조정할 수 있습니다.

### <a name="vm-sizes"></a>VM 크기

다음 표에서는 크기를 사용 사례로 분류합니다.  
| type                     | 일반적인 크기           |    설명       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [범용](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU 대 메모리 비율이 적당합니다. 개발/테스트와 소규모에서 중간 정도의 응용 프로그램 및 데이터 솔루션에 적합합니다.  |
| [Compute에 최적화](sizes-compute.md)   | Fs, F             | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 응용 프로그램, 네트워크 어플라이언스 및 일괄 처리 프로세스에 적합합니다.        |
| [메모리에 최적화](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | 메모리 대 코어 비율이 높습니다. 관계형 데이터베이스, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |
| [Storage에 최적화](sizes-storage.md)      | Ls                | 높은 디스크 처리량 및 IO 빅 데이터, SQL, NoSQL 데이터베이스에 적합합니다.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | 대량의 그래픽 렌더링 및 비디오 편집에 적합한 전문 VM입니다.       |
| [고성능](sizes-hpc.md) | H, A8-11          | 당사의 가장 강력한 CPU VM으로, 필요한 경우 처리량이 높은 네트워크 인터페이스(RDMA)도 제공합니다. 


### <a name="find-available-vm-sizes"></a>사용 가능한 VM 크기 찾기

특정 영역에서 사용할 수 있는 VM 크기의 목록을 보려면 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 명령을 사용합니다.

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>VM 크기 조정

VM을 배포한 후에 크기를 조정하여 리소스 할당을 늘리거나 줄일 수 있습니다.

VM의 크기를 조정하기 전에 원하는 크기를 현재 VM 클러스터에서 사용할 수 있는지 확인합니다. [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 명령은 크기 목록을 반환합니다. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

원하는 크기를 사용할 수 있는 경우 전원이 켜진 상태에서 VM 크기를 조정할 수 있지만 작업 중 다시 부팅됩니다.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

원하는 크기가 현재 클러스터에 없는 경우 크기 조정 작업 전에 VM 할당을 취소해야 합니다. VM의 전원이 다시 켜지면 임시 디스크의 모든 데이터가 제거되고 고정 IP 주소를 사용하지 않는 한 공용 IP 주소가 변경됩니다. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>VM 전원 상태

Azure VM의 전원 상태는 여러 상태 중 하나일 수 있습니다. 이 상태는 하이퍼바이저의 관점에서 VM의 현재 상태를 나타냅니다. 

### <a name="power-states"></a>전원 상태

| 전원 상태 | 설명
|----|----|
| 시작 중 | 가상 머신이 시작되고 있음을 나타냅니다. |
| 실행 중 | 가상 머신이 실행되고 있음을 나타냅니다. |
| 중지 중 | 가상 머신이 중지되고 있음을 나타냅니다. | 
| 중지됨 | 가상 머신이 중지되었음을 나타냅니다. 중지됨 상태의 Virtual Machines에도 여전히 계산 요금이 발생됩니다.  |
| 할당 취소 중 | 가상 컴퓨터의 할당이 취소되고 있음을 나타냅니다. |
| 할당 취소됨 | 가상 머신이 하이퍼바이저에서 완전히 제거되었지만 제어 영역에서 계속 사용할 수 있음을 나타냅니다. 할당 취소됨 상태의 Virtual Machines에는 계산 요금이 발생하지 않습니다. |
| - | 가상 머신의 전원 상태가 알 수 없음을 나타냅니다. |

### <a name="find-power-state"></a>전원 상태 찾기

특정 VM의 상태를 검색하려면 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 명령을 사용합니다. 가상 머신 및 리소스 그룹에 대한 올바른 이름을 지정해야 합니다. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

출력

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>관리 작업

가상 머신의 수명 주기 동안 가상 머신 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 스크립트를 만들 수도 있습니다. Azure PowerShell을 사용하면 명령줄이나 스크립트로 여러 가지 일반적인 관리 작업을 실행할 수 있습니다.

### <a name="stop-virtual-machine"></a>가상 머신 중지

[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm)을 사용하여 가상 머신을 중지하고 할당을 취소합니다.

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

가상 머신을 프로비전된 상태로 유지하려면 -StayProvisioned 매개 변수를 사용합니다.

### <a name="start-virtual-machine"></a>가상 머신 시작

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>리소스 그룹 삭제

리소스 그룹을 삭제하면 그 안에 포함된 리소스도 모두 삭제됩니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법과 같이 기본 VM을 만들고 관리하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * VM 크기 조정
> * VM 상태 보기 및 이해

VM 디스크에 대해 자세히 알아보려면 다음 자습서로 이동합니다.  

> [!div class="nextstepaction"]
> [VM 디스크 만들기 및 관리](./tutorial-manage-data-disk.md)
