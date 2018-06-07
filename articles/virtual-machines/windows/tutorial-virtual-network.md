---
title: 자습서 - Windows VM을 위한 Azure 가상 네트워크 만들기 및 관리 | Microsoft Docs
description: 이 자습서에서는 Azure PowerShell을 사용하여 Windows 가상 머신을 위한 Azure 가상 네트워크를 만들고 관리하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a13163949a52503f42642c109a4fd4c1dedd837f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Windows 가상 머신을 위한 Azure 가상 네트워크 만들기 및 관리

Azure 가상 머신은 내부 및 외부 네트워크 통신에서 Azure 네트워킹을 사용합니다. 이 자습서에서는 두 개의 가상 머신을 배포하고 이러한 VM에 Azure 네트워킹을 구성하기 위해 단계별로 안내합니다. 이 자습서의 예제에서는 VM에서 데이터베이스 백 엔드가 있는 웹 응용 프로그램을 호스팅한다고 가정하고 있지만 응용 프로그램은 이 자습서에서 배포되지 않습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 및 서브넷 만들기
> * 공용 IP 주소 만들기
> * 프런트 엔드 VM 만들기
> * 네트워크 트래픽 보안
> * 백 엔드 VM 만들기

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure PowerShell 모듈 버전이 5.7.0 이상이어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="vm-networking-overview"></a>VM 네트워킹 개요

Azure 가상 네트워크를 사용하면 네트워크에서 가상 머신, 인터넷 및 다른 Azure 서비스(예: Azure SQL 데이터베이스) 간에 안전하게 연결할 수 있습니다. 가상 네트워크는 서브넷이라는 논리적 세그먼트로 구분됩니다. 서브넷은 보안 경계로서 네트워크 흐름을 제어하는 데 사용됩니다. VM을 배포할 때는 일반적으로 서브넷에 연결된 가상 네트워크 인터페이스가 포함됩니다.

이 자습서를 완료하면 다음과 같은 리소스가 만들어진 것을 볼 수 있습니다.

![두 서브넷을 사용하는 가상 네트워크](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM이 다른 VM 및 인터넷과 통신할 때 사용하는 가상 네트워크.
- *myFrontendSubnet* - 프런트 엔드 리소스에서 사용되는 *myVNet*의 서브넷.
- *myPublicIPAddress* - 인터넷에서 *myFrontendVM*을 액세스할 때 사용되는 공용 IP 주소.
- *myFrontentNic* - *myFrontendVM*이 *myBackendVM*과 통신할 때 사용하는 네트워크 인터페이스.
- *myFrontendVM* - 인터넷과 *myBackendVM* 사이에 통신할 때 사용되는 VM.
- *myBackendNSG* - *myFrontendVM*과 *myBackendVM* 사이의 통신을 제어하는 네트워크 보안 그룹.
- *myBackendSubnet* - *myBackendNSG*에 연동되어 백엔드 리소스에 의해 사용되는 서브넷.
- *myBackendNic* - *myBackendVM*이 *myFrontendVM*과 통신할 때 사용하는 네트워크 인터페이스.
- *myBackendVM* - 포트 1433을 사용하여 *myFrontendVM*과 통신하는 VM.


## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

이 자습서에서는 두 개의 서브넷이 있는 단일 가상 네트워크를 만듭니다. 서브넷 하나는 웹 응용 프로그램을 호스트하기 위한 프런트 엔드 서브넷이고 다른 하나는 데이터베이스 서버를 호스트하기 위한 백 엔드 서브넷입니다.

가상 네트워크를 만들려면 먼저 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myRGNetwork*라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>서브넷 구성 만들기

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 *myFrontendSubnet*이라는 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

그런 다음 *myBackendSubnet*이라는 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>가상 네트워크 만들기

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 통해 *myFrontendSubnet*과 *myBackendSubnet*을 사용하여 *myVNet*이라는 VNET을 만듭니다.

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

이 시점에서 네트워크가 만들어져 하나는 프론트 엔드 서비스를, 다른 하나는 백 엔드 서비스를 위한 두 개의 서브넷으로 분할되었습니다. 다음 섹션에서는 가상 머신을 만들어 이 서브넷에 연결합니다.

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

공용 IP 주소를 사용하면 Azure 리소스를 인터넷에서 액세스할 수 있습니다. 공용 IP 주소의 할당 방법은 동적 또는 정적 할당으로 구성할 수 있습니다. 기본적으로 공용 IP 주소는 동적 할당됩니다. VM 할당을 취소하는 경우 동적 IP 주소도 해제됩니다. 이 동작은 VM 할당 취소를 포함하는 모든 작업 중에 IP 주소가 변경되게 합니다.

할당 방법은 정적으로 설정할 수 있으며, 이렇게 하면 할당 취소된 상태에서도 VM에 할당된 IP 주소는 그대로 유지됩니다. 정적으로 할당된 IP 주소를 사용하는 경우 IP 주소 자체를 지정할 수 없습니다. 대신 사용 가능한 주소 풀에서 할당됩니다.

[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)를 사용하여 *myPublicIPAddress*라는 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

-AllocationMethod 매개 변수를 `Static`으로 변경하면 정적 공용 IP 주소를 할당할 수 있습니다.

## <a name="create-a-front-end-vm"></a>프런트 엔드 VM 만들기

가상 네트워크가 VNet에서 통신하려면 가상 네트워크 인터페이스(NIC)가 필요합니다. [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 NIC를 만듭니다.

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다. 다음과 같은 추가 단계에서 이러한 자격 증명을 사용하여 VM에 연결합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 VM을 만듭니다.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>네트워크 트래픽 보안

NSG(네트워크 보안 그룹)에는 VNet(Azure Virtual Network)에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷 또는 개별 네트워크 인터페이스에 연결할 수 있습니다. NSG가 네트워크 인터페이스에 연결되면 연결된 VM만 적용됩니다. NSG를 서브넷에 연결하면 규칙이 서브넷에 연결된 모든 리소스에 적용됩니다.

### <a name="network-security-group-rules"></a>네트워크 보안 그룹 규칙

NSG 규칙은 트래픽이 허용되거나 거부되는 네트워킹 포트를 정의합니다. 규칙에는 원본 및 대상 IP 주소 범위가 포함될 수 있으므로 특정 시스템이나 서브넷 간의 트래픽이 제어됩니다. NSG 규칙에는 우선 순위(1-4096 사이)도 포함됩니다. 규칙은 우선 순위에 따라 평가됩니다. 우선 순위가 100인 규칙은 우선 순위가 200인 규칙보다 먼저 평가됩니다.

모든 NSG에는 기본 규칙 집합이 포함됩니다. 기본 규칙은 삭제할 수 없지만, 가장 낮은 우선순위가 할당되기 때문에 직접 만든 규칙으로 재정의할 수 있습니다.

- **가상 네트워크:** 가상 네트워크에서 시작하고 끝나는 트래픽은 인바운드와 아웃바운드 방향 둘 다에서 허용됩니다.
- **인터넷:** 아웃바운드 트래픽은 허용되지만 인바운드 트래픽은 차단됩니다.
- **부하 분산 장치:** Azure의 부하 분산 장치에서 VM과 역할 인스턴스의 상태를 검색할 수 있도록 허용합니다. 부하 분산된 집합을 사용하지 않는 경우 이 규칙을 재정의할 수 있습니다.

### <a name="create-network-security-groups"></a>네트워크 보안 그룹 만들기

*myFrontendVM*에서 웹 트래픽의 수신이 허용되도록 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)를 사용하여 *myFrontendNSGRule*이라는 인바운드 규칙을 만듭니다.

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

백 엔드 서브넷에 대한 NSG를 만들어 *myFrontendVM*에서만 *myBackendVM*으로 내부 트래픽을 보내도록 제한할 수 있습니다. 다음 예제는 *myBackendNSGRule*이라는 NSG 규칙을 만듭니다.

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)을 사용하여 *myFrontendNSG*라는 네트워크 보안 그룹을 추가합니다.

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

New-AzureRmNetworkSecurityGroup을 사용하여 *myBackendNSG*라는 네트워크 보안 그룹을 추가합니다.

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

서브넷에 네트워크 보안 그룹을 추가합니다.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>백엔드 VM 만들기

이 자습서에서 백엔드 VM을 만드는 가장 쉬운 방법은 SQL Server 이미지를 사용하는 것입니다. 이 자습서에서는 데이터베이스 서버를 사용하여 VM을 만들기만 하며 데이터베이스에 액세스하는 방법에 대한 정보는 제공하지 않습니다.

*myBackendNic*을 만듭니다.

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Get-Credential을 사용하여 가상 컴퓨터의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

*myBackendVM*을 만듭니다.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

사용되는 이미지에는 SQL Server가 설치되어 있지만 이 자습서에서는 사용하지 않습니다. 여기서는 웹 트래픽을 처리하도록 VM을 구성하는 방법 및 데이터베이스 관리를 처리하도록 VM을 구성하는 방법을 보여 주기 위해 포함되었습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 머신과 관련된 Azure Networks를 만들고 보호했습니다. 

> [!div class="checklist"]
> * 가상 네트워크 및 서브넷 만들기
> * 공용 IP 주소 만들기
> * 프런트 엔드 VM 만들기
> * 네트워크 트래픽 보안
> * 백엔드 VM 만들기

다음 자습서로 이동하여 Azure Backup을 사용하는 가상 머신의 데이터 보안 모니터링에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Azure에서 Windows 가상 머신 백업](./tutorial-backup-vms.md)
