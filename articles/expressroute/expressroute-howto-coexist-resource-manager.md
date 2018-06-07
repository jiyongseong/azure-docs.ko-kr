---
title: '공존할 수 있는 ExpressRoute 및 사이트 간 VPN 연결 구성: Resource Manager: Azure | Microsoft Docs'
description: 이 문서에서는 Resource Manager 모델에 대해 공존할 수 있는 ExpressRoute와 사이트 간 VPN 연결을 구성하는 과정을 안내합니다.
documentationcenter: na
services: expressroute
author: charwen
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: charwen,cherylmc
ms.openlocfilehash: deb2a768d766f3fcfa5523b5b3e77b85c0b87b9c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>ExpressRoute 및 사이트 간 공존 연결 구성
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - 클래식](expressroute-howto-coexist-classic.md)
> 
> 

사이트 간 VPN과 ExpressRoute가 공존하는 연결을 구성하면 여러 장점이 있습니다. 사이트 간 VPN을 ExpressRoute에 대한 안전한 장애 조치(failover) 경로로 구성할 수 있습니다. 또는 사이트 간 VPN을 사용하여 ExpressRoute를 통해 연결되어 있지 않은 사이트에 연결할 수 있습니다. 이 문서에서는 두 시나리오를 모두 구성하는 단계를 설명합니다. 이 문서는 Resource Manager 배포 모델에 적용되며 PowerShell을 사용합니다. 이 구성은 Azure 포털에서 사용할 수 없습니다.

## <a name="limits-and-limitations"></a>제한 및 제한 사항
* **통과 라우팅이 지원되지 않습니다.** 사이트 간 VPN을 통해 연결된 로컬 네트워크와 ExpressRoute를 통해 연결된 로컬 네트워크 사이는 Azure를 통해 라우팅할 수 없습니다.
* **기본 SKU 게이트웨이는 지원되지 않습니다.** [ExpressRoute 게이트웨이](expressroute-about-virtual-network-gateways.md) 및 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) 모두에 기본이 아닌 SKU 게이트웨이를 사용해야 합니다.
* **경로 기반 VPN Gateway만 지원됩니다.** 경로 기반 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 사용해야 합니다.
* **VPN Gateway에 고정 경로를 구성해야 합니다.** 로컬 네트워크가 ExpressRoute 및 사이트 간 VPN 모두에 연결된 경우 로컬 네트워크에서 정적 경로를 구성하여 사이트 간 VPN 연결을 공용 인터넷에 라우팅해야 합니다.

## <a name="configuration-designs"></a>구성 디자인
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>사이트 간 VPN을 ExpressRoute에 대한 장애 조치(failover) 경로로 구성
ExpressRoute에 대한 백업으로 사이트 간 VPN 연결을 구성할 수 있습니다. 이 연결은 Azure 개인 피어링 경로에 연결된 가상 네트워크에만 적용됩니다. Azure Microsoft 피어링을 통해 액세스할 수 있는 서비스에 대한 VPN 기반 장애 조치(failover) 솔루션은 없습니다. ExpressRoute 회로는 항상 기본 링크입니다. ExpressRoute 회로가 실패하면 데이터는 사이트 간 VPN 경로를 통해 전송됩니다. 비대칭 라우팅을 방지하려면 로컬 네트워크 구성에서 사이트 간 VPN을 통한 ExpressRoute 회로를 사용해야 합니다. ExpressRoute를 수신한 경로에 대해 더 높은 로컬 기본 설정을 설정하여 ExpressRoute 경로를 사용할 수 있습니다. 

> [!NOTE]
> 두 경로가 동일한 경우 사이트 간 VPN보다 ExpressRoute 회로가 사용되며, Azure는 가장 긴 접두사 일치 항목을 사용하여 패킷의 대상에 대한 경로를 선택합니다.
> 
> 

![공존](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>사이트 간 VPN을 구성하여 ExpressRoute를 통해 연결되지 않은 사이트에 연결
일부 사이트는 사이트 간 VPN을 통해 Azure에 직접 연결하고 일부 사이트는 ExpressRoute를 통해 연결된 네트워크를 구성할 수 있습니다. 

![공존](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> 가상 네트워크를 중계 라우터로 구성할 수 없습니다.
> 
> 

## <a name="selecting-the-steps-to-use"></a>사용할 단계 선택
두 가지 절차 중에서 선택할 수 있습니다. 연결할 기존 가상 네트워크가 있는지 아니면 새 가상 네트워크를 만들 것인지에 따라 구성 절차를 선택합니다.

* VNet이 없어서 만들어야 하는 경우
  
    가상 네트워크가 아직 없는 경우 이 절차에서는 Resource Manager 배포 모델을 사용하여 새 가상 네트워크를 만들고 새 ExpressRoute 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 가상 네트워크를 구성하려면 [새 가상 네트워크 및 공존 연결을 만들려면](#new) 섹션의 단계를 따릅니다.
* 이미 Resource Manager 배포 모델 VNet이 있는 경우
  
    기존 사이트 간 VPN 또는 ExpressRoute에 연결된 가상 네트워크가 이미 있을 수 있습니다. 이 시나리오에서 게이트웨이 서브넷 마스크가 /28 이상인 경우 기존 게이트웨이를 삭제해야 합니다. [기존 VNet에 대해 공존 연결을 구성하려면](#add) 섹션에서는 게이트웨이를 삭제한 다음 새 ExpressRoute 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다.
  
    게이트웨이를 삭제하고 다시 만드는 경우 프레미스 간 연결에 대한 가동 중지 시간을 갖습니다. 그러나 VM 및 서비스는 그렇게 구성된 경우 게이트웨이를 구성하는 동안 부하 분산 장치를 통해 계속 통신할 수 있습니다.

## <a name="new"></a>새 가상 네트워크 및 공존 연결을 만들려면
이 절차에서는 공존하는 VNet 연결과 사이트 간 및 ExpressRoute 연결을 만드는 방법을 안내합니다.

1. 최신 버전의 Azure PowerShell cmdlet을 설치합니다. cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요. 이 구성에 사용할 cmdlet은 지금까지 사용하던 것과 약간 다를 수 있습니다. 다음 지침에 지정된 cmdlet을 사용해야 합니다.

2. 계정에 로그인하여 환경을 설정합니다.

  ```powershell
  Connect-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65010
  ```
3. 게이트웨이 서브넷을 포함하여 가상 네트워크를 만듭니다. 가상 네트워크 만들기에 대한 자세한 내용은 [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요. 서브넷 만들기에 대한 자세한 내용은 [서브넷 만들기](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)를 참조하세요.
   
   > [!IMPORTANT]
   > 게이트웨이 서브넷은 /27 또는 더 짧은 접두사(예: /26 또는 /25)여야 합니다.
   > 
   > 
   
    새 VNet을 만듭니다.

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    서브넷을 추가합니다.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    VNet 구성을 저장합니다.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="vpngw"></a>그런 다음 사이트 간 VPN Gateway를 만듭니다. VPN Gateway 구성에 대한 자세한 내용은 [사이트 간 연결로 VNet 구성](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)을 참조하세요. GatewaySku는 *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* 및 *HighPerformance* VPN 게이트웨이에 대해서만 지원됩니다. ExpressRoute-VPN Gateway 공존 구성은 기본 SKU에서 지원되지 않습니다. VpnType은 *RouteBased*여야 합니다.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
  ```
   
    Azure VPN Gateway는 BGP 라우팅 프로토콜을 지원합니다. 다음 명령에서 -Asn 스위치를 추가하여 해당 Virtual Network에 대해 ASN(AS 번호)을 지정할 수 있습니다. 매개 변수를 지정하지 않으면 AS 번호는 기본적으로 65515가 됩니다.

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
  ```
   
    Azure가 $azureVpn.BgpSettings.BgpPeeringAddress 및 $azureVpn.BgpSettings.Asn에서 VPN Gateway에 사용하는 BGP 피어링 IP 및 AS 번호를 찾을 수 있습니다. 자세한 내용은 Azure VPN Gateway에 대한 [BGP 구성](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.
5. 로컬 사이트 VPN Gateway 엔터티를 만듭니다. 이 명령은 온-프레미스 VPN Gateway를 구성하지 않습니다. 대신, Azure VPN Gateway를 연결할 수 있도록 공용 IP 주소 및 온-프레미스 주소 공간과 같은 로컬 게이트웨이 설정을 제공할 수 있게 해줍니다.
   
    로컬 VPN 장치가 고정 라우팅만을 지원하는 경우 다음과 같은 방식으로 고정 경로를 구성할 수 있습니다.

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    로컬 VPN 장치가 BGP를 지원하고 동적 라우팅을 사용하도록 설정하려는 경우 로컬 VPN 장치가 사용하는 BGP 피어링 IP 및 AS 번호를 알아야 합니다.

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
6. 새 Azure VPN Gateway에 연결할 로컬 VPN 장치를 구성합니다. VPN 장치 구성에 대한 자세한 내용은 [VPN 장치 구성](../vpn-gateway/vpn-gateway-about-vpn-devices.md)을 참조하세요.

7. Azure의 사이트 간 VPN Gateway를 로컬 게이트웨이에 연결합니다.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```
 

8. 기존 ExpressRoute 회로에 연결하는 경우 8-9단계를 건너뛰고, 10단계로 이동합니다. ExpressRoute 회로를 구성합니다. ExpressRoute 회로 구성에 대한 자세한 내용은 [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)를 참조하세요.


9. ExpressRoute 회로를 통해 Azure 개인 피어링을 구성합니다. ExpressRoute 회로를 통한 Azure 개인 피어링에 대한 자세한 내용은 [피어링 구성](expressroute-howto-routing-arm.md)을 참조하세요.

10. <a name="gw"></a>ExpressRoute 게이트웨이를 만듭니다. ExpressRoute 게이트웨이 구성에 대한 자세한 내용은 [ExpressRoute 게이트웨이 구성](expressroute-howto-add-gateway-resource-manager.md)을 참조하세요. GatewaySKU는 *Standard*, *HighPerformance* 또는 *UltraPerformance*이어야 합니다.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
11. ExpressRoute 게이트웨이를 ExpressRoute 회로에 연결합니다. 이 단계를 완료하면 ExpressRoute를 통해 온-프레미스 네트워크와 Azure 간의 연결이 설정됩니다. 링크 작업에 대한 자세한 내용은 [Vnet을 ExpressRoute에 연결](expressroute-howto-linkvnet-arm.md)을 참조하세요.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```

## <a name="add"></a>기존 VNet에 대한 공존 연결을 구성하려면
기존 가상 네트워크가 있는 경우 게이트웨이 서브넷 크기를 확인합니다. 게이트웨이 서브넷이 /28 또는 /29인 경우 우선 가상 네트워크 게이트웨이를 삭제하고 게이트웨이 서브넷 크기를 늘려야 합니다. 이 섹션에서 단계별 수행 방법을 보여줍니다.

게이트웨어 서브넷이 /27 이상이고 가상 네트워크가 ExpressRoute를 통해 연결된 경우 아래 단계를 건너뛰고 이전 섹션의 ["4단계 - 사이트 간 VPN 게이트웨이 만들기"](#vpngw)를 진행할 수 있습니다. 

> [!NOTE]
> 기존 게이트웨이를 삭제하면 이 구성에서 작업하는 동안 로컬 프레미스와 가상 네트워크의 연결이 끊어집니다. 
> 
> 

1. 최신 버전의 Azure PowerShell cmdlet을 설치해야 합니다. cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요. 이 구성에 사용할 cmdlet은 지금까지 사용하던 것과 약간 다를 수 있습니다. 다음 지침에 지정된 cmdlet을 사용해야 합니다. 
2. 기존 ExpressRoute 또는 사이트 간 VPN Gateway를 삭제합니다.

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. 게이트웨이 서브넷을 삭제합니다.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. /27 이상인 게이트웨이 서브넷을 추가합니다.
   
   > [!NOTE]
   > 가상 네트워크에 게이트웨이 서브넷 크기를 늘릴 수 있는 IP 주소가 충분히 남아 있지 않을 경우 추가 IP 주소 공간을 추가해야 합니다.
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    VNet 구성을 저장합니다.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. 이제 게이트웨이 없는 VNet이 생겼습니다. 새 게이트웨이를 만들고 연결을 완료하려면 이전 단계의 [4단계 - 사이트 간 VPN 게이트웨이 만들기"](#vpngw)를 진행합니다.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>VPN Gateway에 지점 및 사이트 간 구성을 추가하려면
아래 단계에 따라 공존 설정에서 VPN Gateway에 지점 및 사이트 간 구성을 추가할 수 있습니다.

1. VPN 클라이언트 주소 풀을 추가합니다.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. VPN Gateway에 대한 Azure에 VPN 루트 인증서를 업로드합니다. 이 예에서는 다음 PowerShell cmdlet이 실행되는 로컬 컴퓨터에 루트 인증서가 저장되는 것으로 가정합니다.

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
