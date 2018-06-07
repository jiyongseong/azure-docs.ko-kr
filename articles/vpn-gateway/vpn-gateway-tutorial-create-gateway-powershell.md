---
title: PowerShell을 사용하여 Azure VPN 게이트웨이 만들기 및 관리 | Microsoft Docs
description: 자습서 - Azure PowerShell 모듈을 사용하여 VPN 게이트웨이 만들기 및 관리
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/14/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 0f10384e7e21d65b3a16869a10f8294b9643c74c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210201"
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Azure PowerShell 모듈을 사용하여 VPN 게이트웨이 만들기 및 관리

Azure VPN 게이트웨이는 고객 프레미스와 Azure 사이에 프레미스 간 연결을 제공합니다. 이 자습서에서는 VPN 게이트웨이 만들기 및 관리 같은 기본적인 Azure VPN 게이트웨이 배포 항목을 다룹니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VPN 게이트웨이 만들기
> * VPN 게이트웨이 크기 조정
> * VPN Gateway 다시 설정

다음 다이어그램은 이 자습서에서 만든 가상 네트워크 및 VPN 게이트웨이를 보여줍니다.

![VNet 및 VPN 게이트웨이](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell 및 Azure PowerShell

[!INCLUDE [working with cloudshell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.3 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 

## <a name="common-network-parameter-values"></a>일반 네트워크 매개 변수 값

환경 및 네트워크 설정에 따라 아래 값을 변경합니다.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령으로 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 리소스 그룹을 먼저 만들어야 합니다. 다음 예제에서는 *미국 동부* 지역에 *TestRG1*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Azure VPN 게이트웨이는 가상 네트워크를 위한 프레미스 간 연결 및 P2S VPN 서버 기능을 제공합니다. 기존 가상 네트워크에 VPN 게이트웨이를 추가하거나 새 가상 네트워크 및 게이트웨이를 만듭니다. 이 예제에서는 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 및 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 명령을 사용하여 세 개의 서브넷 Frontend, Backend 및 GatewaySubnet이 있는 새 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>VPN 게이트웨이에 대한 공용 IP 주소 요청

Azure VPN 게이트웨이는 인터넷을 통해 온-프레미스 VPN 장치와 통신하여 IKE(Internet Key Exchange) 협상을 수행하고 IPsec 터널을 설정합니다. [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 및 [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig) 명령을 사용하여 아래 예제와 같이 공용 IP 주소를 만들어서 VPN 게이트웨이에 할당합니다.

> [!IMPORTANT]
> 현재는 게이트웨이에 동적 공용 IP 주소만 사용할 수 있습니다. 고정 IP 주소는 Azure VPN 게이트웨이에서 지원되지 않습니다.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>VPN 게이트웨이 만들기

VPN 게이트웨이를 만드는 데에는 45분 이상이 걸릴 수 있습니다. 게이트웨이 만들기가 완료되면 가상 네트워크와 VNet 간에 연결을 만들 수 있습니다. 또는 가상 네트워크와 온-프레미스 위치 간에 연결을 만듭니다. [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway) cmdlet을 사용하여 VPN 게이트웨이를 만듭니다.

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

키 매개 변수 값:
* GatewayType: 사이트 간 연결 및 VNet-VNet 연결에 **Vpn**을 사용합니다.
* VpnType: **RouteBased**를 사용하여 더 넓은 범위의 VPN 장치 및 더 많은 라우팅 기능과 상호 작용합니다.
* GatewaySku: 기본값은 **VpnGw1**입니다. 더 높은 처리량 또는 더 많은 연결이 필요한 경우 VpnGw2 또는 VpnGw3로 변경합니다. 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

게이트웨이 만들기가 완료되면 가상 네트워크와 다른 VNet 간에 연결을 만들거나 가상 네트워크와 온-프레미스 위치 간에 연결을 만들 수 있습니다. 클라이언트 컴퓨터에서 VNet으로 P2S 연결을 구성할 수도 있습니다.

## <a name="resize-vpn-gateway"></a>VPN 게이트웨이 크기 조정

게이트웨이를 만든 후 VPN 게이트웨이 SKU를 변경할 수 있습니다. 다양한 게이트웨이 SKU가 처리량, 연결 수 등의 다양한 사양을 지원합니다. 다음 예제에서는 [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) 명령을 사용하여 게이트웨이 크기를 VpnGw1에서 VpnGw2로 조정합니다. 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

또한 VPN 게이트웨이 크기 조정 작업이 기존 연결 및 구성을 중단하거나 제거하지는 **않지만**, 완료하는 데 약 30-45분이 걸립니다.

## <a name="reset-vpn-gateway"></a>VPN 게이트웨이 다시 설정

문제 해결 단계의 일부로, VPN 게이트웨이가 IPsec/IKE 터널 구성을 강제로 다시 시작하도록 Azure VPN 게이트웨이를 다시 설정할 수 있습니다. 게이트웨이를 다시 설정하려면 [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) 명령을 사용합니다.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

자세한 내용은 [VPN 게이트웨이 다시 설정](vpn-gateway-resetgw-classic.md)을 참조하세요.

## <a name="get-the-gateway-public-ip-address"></a>게이트웨이 공용 IP 주소 가져오기

공용 IP 주소의 이름을 알고 있는 경우 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) 명령을 사용하여 게이트웨이에 할당된 공용 IP 주소를 표시합니다.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>VPN 게이트웨이 삭제

프레미스 간 연결 및 VNet-VNet 연결을 완전하게 구성하려면 VPN 게이트웨이 외에도 여러 리소스 종류가 필요합니다. 게이트웨이 자체를 삭제하려면 먼저 VPN 게이트웨이와 이어진 연결을 삭제해야 합니다. 게이트웨이를 삭제한 후에는 게이트웨이의 공용 IP 주소를 삭제할 수 있습니다. 자세한 단계는 [VPN 게이트웨이 삭제](vpn-gateway-delete-vnet-gateway-powershell.md)를 참조하세요.

게이트웨이가 프로토타입 또는 개념 증명의 일부인 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹, VPN 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같이 기본 VPN 게이트웨이를 만들고 관리하는 방법을 배웠습니다.

> [!div class="checklist"]
> * VPN 게이트웨이 만들기
> * VPN 게이트웨이 크기 조정
> * VPN Gateway 다시 설정

그 다음 자습서로 넘어가서 S2S, VNet-VNet 연결 및 P2S 연결에 대해 알아보세요.

> [!div class="nextstepaction"]
> * [S2S 연결 만들기](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [VNet-VNet 연결 만들기](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S 연결 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
