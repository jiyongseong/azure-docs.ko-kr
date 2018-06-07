---
title: 가상 네트워크 만들기 - 빠른 시작 - Azure PowerShell | Microsoft Docs
description: 이 빠른 시작에서 Azure Portal을 사용하여 가상 네트워크를 만드는 방법에 대해 알아봅니다. 가상 네트워크를 사용하면 가상 머신과 같은 Azure 리소스에서 서로 인터넷을 통해 개별적으로 통신할 수 있습니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: b8b67b235b54fb5bde738ed5cc1605e08d182a69
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>빠른 시작: PowerShell을 사용하여 가상 네트워크 만들기

가상 네트워크를 사용하면 VM(가상 머신)과 같은 Azure 리소스에서 서로 인터넷을 통해 개별적으로 통신할 수 있습니다. 이 빠른 시작에서는 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크를 만든 후에 두 개의 VM을 가상 네트워크에 배포합니다. 그런 다음, 인터넷에서 하나의 VM에 연결하고 두 VM 간에 개별적으로 통신합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에는 AzureRM PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 ` Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들려면 먼저 가상 네트워크가 포함될 리소스 그룹을 만들어야 합니다. [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myVirtualNetwork*라는 기본 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Azure 리소스는 가상 네트워크 내의 서브넷에 배포되므로 서브넷을 만들어야 합니다. [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷 구성을 만듭니다. 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

가상 네트워크에 서브넷이 만드는 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork)를 사용하여 가상 네트워크에 서브넷 구성을 작성합니다.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 VM을 만듭니다. 다음 명령을 실행하면 자격 증명을 묻는 메시지가 표시됩니다. 입력하는 값은 VM에 대한 사용자 이름과 암호로 구성됩니다. `-AsJob` 옵션은 백그라운드에서 VM을 만들므로 다음 단계를 계속 진행할 수 있습니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

다음 예제 출력과 비슷한 출력이 반환되며, Azure는 백그라운드에서 VM을 만들기 시작합니다.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>두 번째 VM 만들기 

다음 명령을 입력합니다.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

VM을 만드는 데 몇 분이 걸립니다. 이전 명령이 실행되고 출력이 PowerShell에 반환될 때까지 다음 단계를 계속 수행하지 마세요.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVm1* VM의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

다음 명령에서 `<publicIpAddress>`을 이전 명령에서 반환된 공용 IP 주소로 바꾸고 다음 명령을 입력합니다. 

```
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 만들어지고 컴퓨터에 다운로드됩니다. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**, **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다. **확인**을 선택합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.

## <a name="communicate-between-vms"></a>VM 간 통신

*myVm1* VM의 PowerShell에서 `ping myvm2`를 입력합니다. ping에서 ICMP(Internet Control Message Protocol)를 사용하고 ICMP는 기본적으로 Windows 방화벽을 통해 허용되지 않으므로 ping이 실패합니다.

나중의 단계에서 *myVm2*를 통해 *myVm1*을 ping할 수 있게 하려면, PowerShell에서 다음과 같이 Windows 방화벽을 통한 ICMP 인바운드를 허용하는 명령을 입력합니다.

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

*myVm1*에 대한 원격 데스크톱 연결을 닫습니다. 

[인터넷에서 VM에 연결](#connect-to-a-vm-from-the-internet)의 단계를 다시 수행하지만 *myVm2*에 연결합니다. 

*myVm2* VM의 명령 프롬프트에서 `ping myvm1`을 입력합니다.

이전 단계에서 *myVm1* VM의 Windows 방화벽을 통해 ICMP를 허용했으므로 *myVm1*에서 회신을 받습니다.

*myVm2*에 대한 원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 가상 네트워크와 두 개의 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결하고 VM 및 다른 VM 간에 개별적으로 통신했습니다. 가상 네트워크 설정에 대한 자세한 내용은 [가상 네트워크 관리](manage-virtual-network.md)를 참조하세요. 

기본적으로 Azure는 가상 머신 간에 무제한 개별 통신을 허용하지만, 인터넷에서 Linux VM에 대한 인바운드 원격 데스크톱 연결만 허용합니다. VM 간에 다양한 유형의 네트워크 통신을 허용하거나 제한하는 방법을 알아보려면 [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md) 자습서를 계속 진행하세요.
