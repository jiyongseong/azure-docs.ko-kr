---
title: 네트워크 트래픽 필터링 - 자습서 - Azure PowerShell | Microsoft Docs
description: 이 자습서에서는 PowerShell을 사용하 여 네트워크 보안 그룹을 통해 서브넷에 네트워크 트래픽을 필터링하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 165bd6770109348bd19ebb4fa1735bedf83004b1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261320"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-powershell"></a>자습서: PowerShell을 사용하여 네트워크 보안 그룹을 통해 네트워크 트래픽 필터링

네트워크 보안 그룹을 통해 가상 네트워크 서브넷에서 네트워크 트래픽을 인바운드 및 아웃바운드로 필터링할 수 있습니다. 네트워크 보안 그룹에는 IP 주소, 포트, 프로토콜에 따라 네트워크 트래픽을 필터링하는 보안 규칙이 포함됩니다. 보안 규칙은 서브넷에 배포된 리소스에 적용됩니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 네트워크 보안 그룹 및 보안 규칙 만들기
> * 가상 네트워크 만들기 및 서브넷에 네트워크 보안 그룹 연결
> * 서브넷에 VM(가상 머신) 배포
> * 트래픽 필터 테스트

원하는 경우 [Azure CLI](tutorial-filter-network-traffic-cli.md)를 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 ` Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

네트워크 보안 그룹은 보안 규칙을 포함합니다. 보안 규칙은 원본 및 대상을 지정합니다. 원본 및 대상은 응용 프로그램 보안 그룹이 될 수 있습니다.

### <a name="create-application-security-groups"></a>응용 프로그램 보안 그룹 만들기

먼저 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)으로 이 자습서에서 만든 모든 리소스에 대한 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 리소스 그룹을 만듭니다. 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)으로 응용 프로그램 보안 그룹을 만듭니다. 응용 프로그램 보안 그룹을 사용하면 유사한 포트 필터링 요구 사항을 갖는 서버를 그룹화할 수 있습니다. 다음 예제에서는 두 응용 프로그램 보안 그룹을 만듭니다.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>보안 규칙 만들기

[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)를 통해 보안 규칙을 만듭니다. 다음 예제에서는 포트 80 및 443을 통해 인터넷에서 *myWebServers* 응용 프로그램 보안 그룹으로 가는 트래픽을 허용하는 규칙을 만듭니다.

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

이 자습서에서 RDP(포트 3389)는 *myAsgMgmtServers* VM에 대해 인터넷에 노출되어 있습니다. 프로덕션 환경에서는 포트 3389를 인터넷에 노출하는 대신 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [개인](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 네트워크 연결을 통해 관리하려는 Azure 리소스에 연결하는 것이 좋습니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNsg*라는 네트워크 보안 그룹을 만듭니다. 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVirtualNetwork*라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 통해 서브넷 구성을 만든 다음, [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)를 통해 가상 네트워크에 서브넷 구성을 기록합니다. 다음 예에서는 *mySubnet*이라는 서브넷을 가상 네트워크에 추가하고 *myNsg* 네트워크 보안 그룹을 연결합니다.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

VM을 만들기 전에 [Get AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)를 통해 서브넷과 가상 네트워크 개체를 검색합니다.

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)를 사용하여 각 VM에 대한 공용 IP 주소를 만듭니다.

```powershell-interactive
$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 통해 두 네트워크 인터페이스를 만들고 공용 IP 주소를 네트워크 인터페이스에 할당합니다. 다음 예제에서는 네트워크 인터페이스를 만들어 *myVmWeb* 공용 IP 주소를 연결하고, *myAsgWebServers* 응용 프로그램 보안 그룹의 멤버로 지정합니다.

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

다음 예제에서는 네트워크 인터페이스를 만들어 *myVmMgmt* 공용 IP 주소를 연결하고, *myAsgMgmtServers* 응용 프로그램 보안 그룹의 멤버로 지정합니다.

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

이후 단계에서 트래픽 필터링의 유효성을 검사할 수 있도록 가상 네트워크에 두 VM을 만듭니다. 

[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)로 VM 구성을 만든 다음, [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)으로 VM을 만듭니다. 다음 예제에서는 웹 서버 역할을 수행 하는 VM을 만듭니다. `-AsJob` 옵션은 다음 단계를 계속하기 위해 백그라운드에서 VM을 만듭니다. 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

관리 서버 역할을 하는 VM을 만듭니다.

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

가상 머신을 만드는 데 몇 분 정도 걸립니다. Azure에서 VM 만들기가 완료될 때까지 다음 단계를 진행하지 않습니다.

## <a name="test-traffic-filters"></a>트래픽 필터 테스트

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVmMgmt* VM의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

다음 명령을 사용하여 로컬 컴퓨터에서 *myVmMgmt* VM과의 원격 데스크톱 세션을 만듭니다. `<publicIpAddress>`를 이전 명령에서 반환된 IP 주소로 바꿉니다.

```
mstsc /v:<publicIpAddress>
```

다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.

VM을 만들 때 지정한 사용자 이름과 암호를 입력(VM을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있음)한 다음, **확인**을 선택합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예**를 선택하여 연결을 진행합니다. 
   
포트 3389는 *myAsgMgmtServers* VM에 연결된 네트워크 인터페이스가 In인 *myVmMgmt* 응용 프로그램 보안 그룹으로 가는 인터넷의 인바운드 트래픽을 허용하기 때문에 연결에 성공합니다.

다음 명령을 사용하여 PowerShell에서 *myVmMgmt* VM으로부터의 *myVmWeb* VM에 대한 원격 데스크톱 연결을 만듭니다.

``` 
mstsc /v:myvmWeb
```

각 네트워크 보안 그룹의 기본 보안 규칙에서 가상 네트워크 내 모든 IP 주소 간에 모든 포트를 경유한 트래픽을 허용하기 때문에 연결이 성공합니다. *myAsgWebServers*에 대한 보안 규칙에서는 인터넷으로부터의 포트 3389 인바운드 트래픽을 허용하지 않으므로 인터넷으로부터 *myVmWeb* VM에 대한 원격 데스크톱 연결을 만들 수 없습니다.

다음 명령을 사용하여 PowerShell에서 *myVmWeb* VM에 Microsoft IIS를 설치합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

IIS 설치가 완료된 후 *myVmWeb* VM 연결을 끊으면 *myVmMgmt* VM 원격 데스크톱 연결이 종료됩니다. IIS 시작 화면을 보려면 인터넷 브라우저를 열고 http://myVmWeb으로 이동합니다.

*myVmMgmt* VM과의 연결을 끊습니다.

컴퓨터에서 다음 PowerShell 명령을 입력하여 *myVmWeb* 서버의 공용 IP 주소를 검색합니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Azure 외부에서 *myVmWeb* 웹 서버에 액세스할 수 있는지 확인하기 위해 컴퓨터에서 인터넷 브라우저를 열고 `http://<public-ip-address-from-previous-step>`로 이동합니다. 포트 80은 *myVmWeb* VM에 연결된 네트워크 인터페이스가 In인 *myAsgWebServers* 응용 프로그램 보안 그룹으로 가는 인터넷의 인바운드 트래픽을 허용하기 때문에 연결에 성공합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 네트워크 보안 그룹을 만들어 가상 네트워크 서브넷에 연결했습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md) 및 [네트워크 보안 그룹 관리](manage-network-security-group.md)를 참조하세요.

기본적으로 Azure는 서브넷 간에 트래픽을 라우팅합니다. 그 대신 방화벽 역할을 하는 VM 등을 통해 서브넷 간에 트래픽을 라우팅하도록 선택할 수 있습니다. 경로 테이블을 만드는 방법을 알아보려면 다음 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [경로 테이블 만들기](./tutorial-create-route-table-portal.md)
