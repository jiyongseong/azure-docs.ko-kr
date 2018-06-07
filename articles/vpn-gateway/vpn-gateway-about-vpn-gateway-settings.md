---
title: 크로스-프레미스 Azure 연결에 대한 VPN Gateway 설정 | Microsoft Docs
description: Azure Virtual Network 게이트웨이의 VPN Gateway 설정에 대해 알아봅니다.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 9ecd8dc40e168c2fd37e3d58ee588a0d9626a04a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN Gateway 구성 설정 정보

VPN Gateway는 공용 연결을 통해 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 전송하는 가상 네트워크 게이트웨이의 유형입니다. 또한 VPN Gateway를 사용하여 Azure 백본에 있는 가상 네트워크 간에 트래픽을 전송할 수 있습니다.

VPN Gateway 연결은 각각이 구성 가능한 설정을 포함하는 여러 리소스의 구성에 따라 좌우됩니다. 이 문서의 섹션에서는 Resource Manager 배포 모델에서 생성된 가상 네트워크의 VPN Gateway와 관련된 리소스 및 설정에 대해 설명합니다. [VPN Gateway 정보](vpn-gateway-about-vpngateways.md) 문서에서 각 연결 솔루션에 대한 설명 및 토폴로지 다이어그램을 찾을 수 있습니다.

>[!NOTE]
> 이 문서에 나오는 값은 -GatewayType 'Vpn'을 사용하는 가상 네트워크 게이트웨이에 적용됩니다. 이러한 특정 가상 네트워크 게이트웨이가 VPN 게이트웨이라고 하는 이유입니다. ExpressRoute 게이트웨이에 대한 값은 VPN 게이트웨이에 사용하는 값과 같지 않습니다.
>
>-GatewayType 'ExpressRoute'에 적용되는 값에 대해서는 [ExpressRoute에 대한 가상 네트워크 게이트웨이](../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요.
>
>

## <a name="gwtype"></a>게이트웨이 유형

가상 네트워크마다 각 유형의 가상 네트워크 게이트웨이를 하나씩만 포함할 수 있습니다. 가상 네트워크 게이트웨이를 만들 때 게이트웨이 유형이 구성에 정확한지 확인해야 합니다.

-GatewayType에 사용 가능한 값은 다음과 같습니다.

* Vpn
* ExpressRoute

VPN Gateway에는 `-GatewayType` *Vpn*이 필요합니다.

예:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>게이트웨이 SKU

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>게이트웨이 SKU 구성

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 Resource Manager 가상 네트워크 게이트웨이를 만드는 경우 드롭다운을 사용하여 게이트웨이 SKU를 선택할 수 있습니다. 표시되는 옵션은 선택한 게이트웨이 형식 및 선택한 VPN 형식에 해당합니다.

#### <a name="powershell"></a>PowerShell

다음 PowerShell 예제에서는 `-GatewaySku`를 VpnGw1으로 지정합니다. PowerShell을 사용하여 게이트웨이를 만드는 경우 먼저 IP 구성을 만든 다음, 변수를 사용하여 참조해야 합니다. 이 예제에서 구성 변수는 $gwipconfig입니다.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>SKU 크기 조정 또는 변경

VPN 게이트웨이가 있는데 다른 게이트웨이 SKU를 사용하려는 경우 게이트웨이 SKU 크기를 조정하거나 다른 SKU로 변경할 수 있습니다. 다른 게이트웨이 SKU로 변경할 때는 기존 게이트웨이를 완전히 삭제하고 새로 작성하게 됩니다. 새로 작성하는 데는 최대 45분이 걸릴 수 있습니다. 그에 비해, 게이트웨이 SKU 크기를 조정할 때는 게이트웨이를 삭제했다가 다시 작성할 필요가 없으므로 아주 짧은 가동 중지 시간만 발생합니다. 게이트웨이 SKU를 변경하지 않고 크기를 조정할 수 있다면 그렇게 하는 것이 좋습니다. 그러나 크기 조정에 대한 규칙이 있습니다.

1. VpnGw1, VpnGw2와 VpnGw3 SKU 간에 크기를 조정할 수 있습니다.
2. 이전 게이트웨이 SKU로 작동하는 경우 Basic, Standard 및 HighPerformance SKU 간에 크기를 조정할 수 있습니다.
3. Basic/Standard/HighPerformance SKU에서 새 VpnGw1/VpnGw2/VpnGw3 SKU까지 크기를 조정할 수 **없습니다**. 대신 새 SKU로 [변경](#change)해야 합니다.

#### <a name="resizegwsku"></a>게이트웨이의 크기를 조정하려면

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>이전(레거시) SKU에서 새로운 SKU로 변경하려면

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>연결 유형

Resource Manager 배포 모델에서 각 구성이 작동하려면 특정 가상 네트워크 게이트웨이 연결 유형이 필요합니다. `-ConnectionType` 에 대해 사용 가능한 Resource Manager PowerShell 값은 다음과 같습니다.

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

다음 PowerShell 예제에서는 *IPsec*연결 유형이 필요한 S2S 연결을 만듭니다.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN 유형

VPN Gateway 구성에 대한 가상 네트워크 게이트웨이 만들 때 VPN 유형을 지정해야 합니다. 선택하는 VPN 유형은 만들려는 연결 토폴로지에 따라 달라집니다. 예를 들어 P2S 연결에는 RouteBased VPN 유형이 필요합니다. 또한 VPN 유형은 사용하는 하드웨어에 따라서도 달라질 수 있습니다. S2S 구성에는 VPN 장치가 필요합니다. 일부 VPN 장치는 특정 VPN 유형을 지원합니다.

선택하는 VPN 유형은 만들려는 솔루션에 대한 연결 요구 사항을 모두 충족해야 합니다. 예를 들어 동일한 가상 네트워크에 대해 S2S VPN Gateway 연결 및 P2S VPN Gateway 연결을 만들려는 경우 P2S에는 RouteBased VPN 유형이 필요하기 때문에 VPN 유형 *RouteBased* 를 사용해야 합니다. VPN 장치가 RouteBased VPN 연결을 지원하는지 확인해야 합니다. 

가상 네트워크 게이트웨이를 만든 후에는 VPN 유형을 변경할 수 없습니다. 가상 네트워크 게이트웨이를 삭제하고 새로 만들어야 합니다. 두 가지 VPN 유형이 있습니다.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

다음 PowerShell 예제에서는 `-VpnType` 를 *RouteBased*로 지정합니다. 게이트웨이를 만들 때 -VpnType이 구성에 정확한지 확인해야 합니다.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>게이트웨이 요구 사항

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>게이트웨이 서브넷

VPN Gateway를 만들기 전에 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은 가상 네트워크 게이트웨이 VM 및 서비스에서 사용하는 IP 주소를 포함합니다. 가상 네트워크 게이트웨이 만들 때 게이트웨이 VM은 게이트웨이 서브넷에 배포되고 필수 VPN Gateway 설정으로 구성됩니다. 게이트웨이 서브넷에 다른 항목(예: 추가 VM)을 배포하지 않아야 합니다. 게이트웨이 서브넷이 제대로 작동하려면 이름을 'GatewaySubnet'으로 지정해야 합니다. 게이트웨이 서브넷 이름을 'GatewaySubnet'으로 지정하면 Azure에서 해당 서브넷이 가상 네트워크 게이트웨이 VM 및 서비스를 배포할 서브넷임을 알 수 있습니다.

게이트웨이 서브넷을 만드는 경우 서브넷이 포함하는 IP 주소의 수를 지정합니다. 게이트웨이 서브넷의 IP 주소는 게이트웨이 VM 및 게이트웨이 서비스에 할당됩니다. 일부 구성에는 다른 구성보다 더 많은 IP 주소가 필요합니다. 만들려는 구성에 대한 지침을 검토하고 가지고 있는 만들려는 게이트웨이 서브넷이 해당 요구 사항을 충족하는지 확인합니다. 또한 이후 추가 구성이 추가될 가능성에 대비하여 게이트웨이 서브넷에 IP 주소가 충분히 포함되어 있는지 확인하려고 할 수 있습니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 게이트웨이 서브넷을 /28 이상으로 만드는 것이 좋습니다(/28, /27, /26 등). 이런 방식으로 나중에 기능을 추가할 경우 게이트웨이를 중지하거나 서브넷을 삭제하고 다시 만들어서 추가 IP 주소를 허용하지 않아도 됩니다.

다음 Resource Manager PowerShell 예제에서는 이름이 GatewaySubnet인 게이트웨이 서브넷을 보여 줍니다. CIDR 표기법이 /27을 지정하는 것을 확인할 수 있으며 이는 이번에 존재하는 대부분의 구성에 대한 충분한 IP 주소를 허용합니다.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>로컬 네트워크 게이트웨이

VPN Gateway 구성을 만들 때 로컬 네트워크 게이트웨이는 종종 온-프레미스 위치를 나타냅니다. 클래식 배포 모델에서 로컬 네트워크 게이트웨이는 로컬 사이트라고 했습니다. 

로컬 네트워크 게이트웨이에 이름인 온-프레미스 VPN 장치의 공용 IP 주소를 지정하고 온-프레미스 위치에 있는 주소 접두사를 지정합니다. Azure는 네트워크 트래픽에 대상 주소 접두사를 보고 로컬 네트워크 게이트웨이에 대해 지정한 구성을 참조하며 그에 따라 패킷을 라우팅합니다. VPN Gateway 연결을 사용하는 VNet-VNet 구성에 대해서도 로컬 네트워크 게이트웨이를 지정합니다.

다음 PowerShell 예제에서는 새 로컬 네트워크 게이트웨이 만듭니다.

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

로컬 네트워크 게이트웨이 설정을 수정해야 하는 경우도 있습니다. 예를 들어 주소 범위를 추가 또는 수정할 경우 또는 VPN 장치의 IP 주소가 변경될 때가 여기에 해당합니다. [PowerShell을 사용하여 로컬 네트워크 게이트웨이 설정 수정](vpn-gateway-modify-local-network-gateway.md)을 참조하세요.

## <a name="resources"></a>REST API, PowerShell cmdlet 및 CLI

VPN Gateway를 구성하기 위해 REST API, PowerShell cmdlet 또는 Azure CLI를 사용할 경우 추가 기술 리소스 및 특정 구문 요구 사항에 대해서는 다음 페이지를 참조하세요.

| **클래식** | **리소스 관리자** |
| --- | --- |
| [PowerShell](/powershell/module/azure#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| 지원되지 않음 | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>다음 단계

사용 가능한 연결 구성에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md) 를 참조하세요.
