---
title: "공존할 수 있는 ExpressRoute 및 사이트 간 VPN 연결 구성: 클래식: Azure | Microsoft Docs"
description: "이 문서에서는 클래식 배포 모델에 대해 공존할 수 있는 ExpressRoute와 사이트 간 VPN 연결을 구성하는 과정을 안내합니다."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: dcf1a5af-a289-466a-b812-0bfedbd2bda0
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.openlocfilehash: 09d1649f0ca0cf4ca464d95b29461cad3fe51788
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2017
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>ExpressRoute 및 사이트 간 공존 연결 구성(클래식)
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - 클래식](expressroute-howto-coexist-classic.md)
> 
> 

사이트 간 VPN 및 ExpressRoute를 구성하는 기능이 있으면 여러 장점이 있습니다. 사이트 간 VPN을 Exress 경로에 대한 안전한 장애 조치(failover) 경로로 구성하거나 사이트 간 VPN을 사용하여 ExpressRoute를 통해 연결되지 않은 사이트에 연결할 수 있습니다. 이 문서에서는 두 시나리오 모두를 구성하는 단계를 설명합니다. 이 문서는 클래식 배포 모델에 적용됩니다. 이 구성은 포털에서 사용할 수 없습니다.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> ExpressRoute 회로는 아래 지침을 수행하기 전에 미리 구성되어야 합니다. 다음 단계를 수행하기 전에 [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 및 [라우팅 구성](expressroute-howto-routing-classic.md)을 위해 지침을 수행했는지 확인합니다.
> 
> 

## <a name="limits-and-limitations"></a>제한 및 제한 사항
* **통과 라우팅이 지원되지 않습니다.** 사이트 간 VPN을 통해 연결된 로컬 네트워크와 ExpressRoute를 통해 연결된 로컬 네트워크 사이는 Azure를 통해 라우팅할 수 없습니다.
* **지점 및 사이트 간 라우팅이 지원되지 않습니다.** 지점 및 사이트 간 VPN을 ExpressRoute에 연결된 동일한 VNet에 연결할 수 없습니다. 동일한 VNet에 대해 지점 및 사이트 간 VPN과 ExpressRoute를 함께 사용할 수 없습니다.
* **사이트 간 VPN Gateway에서 강제 터널링을 사용할 수 없습니다.** 모든 인터넷 바인딩된 트래픽을 ExpressRoute를 통해 온-프레미스 네트워크에 다시 '강제 적용'할 수 있습니다.
* **기본 SKU 게이트웨이는 지원되지 않습니다.** [ExpressRoute 게이트웨이](expressroute-about-virtual-network-gateways.md) 및 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) 모두에 기본이 아닌 SKU 게이트웨이를 사용해야 합니다.
* **경로 기반 VPN Gateway만 지원됩니다.** 경로 기반 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 사용해야 합니다.
* **VPN Gateway에 고정 경로를 구성해야 합니다.** 로컬 네트워크가 ExpressRoute 및 사이트 간 VPN 모두에 연결된 경우 로컬 네트워크에서 정적 경로를 구성하여 사이트 간 VPN 연결을 공용 인터넷에 라우팅해야 합니다.
* **ExpressRoute 게이트웨이를 먼저 구성해야 합니다.** 사이트 간 VPN Gateway를 추가하기 전에 ExpressRoute 게이트웨이를 먼저 만들어야 합니다.

## <a name="configuration-designs"></a>구성 디자인
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>사이트 간 VPN을 ExpressRoute에 대한 장애 조치(failover) 경로로 구성
ExpressRoute에 대한 백업으로 사이트 간 VPN 연결을 구성할 수 있습니다. Azure 개인 피어링 경로에 연결된 가상 네트워크에만 적용됩니다. 공용 Azure 및 Microsoft 피어링을 통해 액세스할 수 있는 서비스에 대한 VPN 기반 장애 조치 솔루션은 없습니다. ExpressRoute 회로는 항상 기본 링크입니다. ExpressRoute 회로가 실패하면 데이터는 사이트 간 VPN 경로를 통해 전송됩니다. 

> [!NOTE]
> 두 경로가 동일한 경우 사이트 간 VPN보다 ExpressRoute 회로가 사용되며, Azure는 가장 긴 접두사 일치 항목을 사용하여 패킷의 대상에 대한 경로를 선택합니다.
> 
> 

![공존](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>사이트 간 VPN을 구성하여 ExpressRoute를 통해 연결되지 않은 사이트에 연결
일부 사이트는 사이트 간 VPN을 통해 Azure에 직접 연결하고 일부 사이트는 ExpressRoute를 통해 연결된 네트워크를 구성할 수 있습니다. 

![공존](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> 가상 네트워크를 통과 라우터로 구성할 수 없습니다.
> 
> 

## <a name="selecting-the-steps-to-use"></a>사용할 단계 선택
연결이 공존할 수 있도록 구성하기 위해 선택하는 서로 다른 두 절차가 있습니다. 연결할 기존 가상 네트워크가 있는지, 아니면 새 가상 네트워크를 만들 것인지에 따라 구성 절차를 선택합니다.

* VNet이 없어서 만들어야 하는 경우
  
    가상 네트워크가 아직 없는 경우 이 절차에서는 클래식 배포 모델을 사용하여 새 가상 네트워크를 만들고 새 ExpressRoute 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 구성하려면 이 문서의 [새 가상 네트워크 및 공존 연결을 만들려면](#new)섹션에 있는 단계를 따릅니다.
* 이미 클래식 배포 모델 VNet이 있는 경우
  
    기존 사이트 간 VPN 또는 ExpressRoute에 연결된 가상 네트워크가 이미 있을 수 있습니다. [기존 VNet에 대해 공존 연결을 구성하려면](#add) 문서 섹션에서는 게이트웨이를 삭제한 다음 새 ExpressRoute 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 새 연결을 만들 때 지정된 순서대로 단계를 완료해야 합니다. 게이트웨이 및 연결을 만들 때 다른 문서의 지침을 사용하지 마세요.
  
    이 절차에서 함께 사용할 수 있는 연결을 만들려면 게이트웨이를 삭제한 다음 공존할 수 있는 새 게이트웨이를 구성해야 합니다. 이 경우 게이트웨이 및 연결을 삭제하고 다시 만드는 동안 크로스-프레미스 연결을 위한 가동 중지 시간이 발생하지만 VM 또는 서비스를 새 가상 네트워크로 마이그레이션할 필요는 없습니다. VM 및 서비스는 그렇게 구성된 경우 게이트웨이를 구성하는 동안 부하 분산 장치를 통해 계속 통신할 수 있습니다.

## <a name="new"></a>새 가상 네트워크 및 공존 연결을 만들려면
이 절차는 VNet 만들기를 안내하고 함께 사용하는 사이트 간 및 ExpressRoute 연결을 만듭니다.

1. 최신 버전의 Azure PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 을 참조하세요. 이 구성에 사용할 cmdlet은 지금까지 익숙하던 cmdlet과는 약간 다를 수 있습니다. 다음 지침에 지정된 cmdlet을 사용해야 합니다. 
2. 가상 네트워크의 스키마를 만듭니다. 구성 스키마에 대한 자세한 내용은 [Azure Virtual Network 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.
   
    스키마를 만들 때 다음 값을 사용해야 합니다.
   
   * 가상 네트워크의 게이트웨이 서브넷은 /27 또는 더 짧은 접두사여야 합니다.(/26 또는 /25와 같이)
   * 게이트웨이 연결 유형은 "전용"입니다.
     
             <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
               <AddressSpace>
                 <AddressPrefix>10.17.159.192/26</AddressPrefix>
               </AddressSpace>
               <Subnets>
                 <Subnet name="Subnet-1">
                   <AddressPrefix>10.17.159.192/27</AddressPrefix>
                 </Subnet>
                 <Subnet name="GatewaySubnet">
                   <AddressPrefix>10.17.159.224/27</AddressPrefix>
                 </Subnet>
               </Subnets>
               <Gateway>
                 <ConnectionsToLocalNetwork>
                   <LocalNetworkSiteRef name="MyLocalNetwork">
                     <Connection type="Dedicated" />
                   </LocalNetworkSiteRef>
                 </ConnectionsToLocalNetwork>
               </Gateway>
             </VirtualNetworkSite>
3. xml 스키마 파일을 만들고 구성한 후 파일을 업로드합니다. 그러면 가상 네트워크가 만들어집니다.
   
    다음 cmdlet(사용자 고유의 값으로 대체)을 사용하여 파일을 업로드합니다.
   
        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
4. <a name="gw"></a>ExpressRoute 게이트웨이를 만듭니다. GatewaySKU를 *Standard*, *HighPerformance* 또는 *UltraPerformance*로 지정하고 GatewayType을 *DynamicRouting*으로 지정해야 합니다.
   
    다음 샘플(사용자 고유의 값으로 대체)을 사용합니다.
   
        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
5. ExpressRoute 게이트웨이를 ExpressRoute 회로에 연결합니다. 이 단계를 완료하면 ExpressRoute를 통해 온-프레미스 네트워크와 Azure 간의 연결이 설정됩니다.
   
        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
6. <a name="vpngw"></a>그런 다음 사이트 간 VPN 게이트웨이를 만듭니다. GatewaySKU는 *Standard*, *HighPerformance* 또는 *UltraPerformance*이어야 하고 GatewayType은 *DynamicRouting*이어야 합니다.
   
        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
   
    게이트웨이 ID와 공용 IP를 비롯한 가상 네트워크 게이트웨이 설정을 검색하려면 `Get-AzureVirtualNetworkGateway` cmdlet을 사용합니다.
   
        Get-AzureVirtualNetworkGateway
   
        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded
7. 로컬 사이트 VPN 게이트웨이 엔터티를 만듭니다. 이 명령은 온-프레미스 VPN Gateway를 구성하지 않습니다. 대신, Azure VPN 게이트웨이를 연결할 수 있도록 공용 IP 주소 및 온-프레미스 주소 공간과 같은 로컬 게이트웨이 설정을 제공할 수 있게 해줍니다.
   
   > [!IMPORTANT]
   > 사이트 간 VPN의 로컬 사이트는 netcfg에 정의되지 않습니다. 대신, 다음 cmdlet을 사용하여 로컬 사이트 매개 변수를 지정해야 합니다. 포털 또는 netcfg 파일을 사용하여 정의할 수 없습니다.
   > 
   > 
   
    다음 샘플(사용자 고유의 값으로 대체)을 사용합니다.
   
        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
   
   > [!NOTE]
   > 로컬 네트워크에 여러 경로가 있는 경우 모두 배열로 전달할 수 있습니다.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    게이트웨이 ID와 공용 IP를 비롯한 가상 네트워크 게이트웨이 설정을 검색하려면 `Get-AzureVirtualNetworkGateway` cmdlet을 사용합니다. 다음 예제를 참조하세요.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


1. 새 게이트웨이에 연결할 로컬 VPN 장치를 구성합니다. VPN 장치를 구성할 때 6단계에서 검색한 정보를 사용합니다. VPN 장치 구성에 대한 자세한 내용은 [VPN 장치 구성](../vpn-gateway/vpn-gateway-about-vpn-devices.md)을 참조하세요.
2. Azure의 사이트 간 VPN 게이트웨이를 로컬 게이트웨이에 연결합니다.
   
    이 예제에서 connectedEntityId는 `Get-AzureLocalNetworkGateway`를 실행하여 찾을 수 있는 로컬 게이트웨이 ID입니다. virtualNetworkGatewayId는 `Get-AzureVirtualNetworkGateway` cmdlet을 사용하여 찾을 수 있습니다. 이 단계를 수행하면 사이트 간 VPN 연결을 통해 로컬 네트워크와 Azure 간의 연결이 설정됩니다.

        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>기존 VNet에 대한 공존 연결을 구성하려면
기존 가상 네트워크가 있는 경우 게이트웨이 서브넷 크기를 확인합니다. 게이트웨이 서브넷이 /28 또는 /29인 경우 우선 가상 네트워크 게이트웨이를 삭제하고 게이트웨이 서브넷 크기를 늘려야 합니다. 이 섹션에서 단계별 수행 방법을 보여줍니다.

게이트웨어 서브넷이 /27 이상이고 가상 네트워크가 ExpressRoute를 통해 연결된 경우 아래 단계를 건너뛰고 이전 섹션의 ["6단계 - 사이트 간 VPN 게이트웨이 만들기"](#vpngw) 를 진행할 수 있습니다.

> [!NOTE]
> 기존 게이트웨이를 삭제하면 이 구성에서 작업하는 동안 로컬 프레미스와 가상 네트워크의 연결이 끊어집니다.
> 
> 

1. 최신 버전의 Azure 리소스 관리자 PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 을 참조하세요. 이 구성에 사용할 cmdlet은 지금까지 익숙하던 cmdlet과는 약간 다를 수 있습니다. 다음 지침에 지정된 cmdlet을 사용해야 합니다. 
2. 기존 ExpressRoute 또는 사이트 간 VPN 게이트웨이를 삭제합니다. 다음 cmdlet(사용자 고유의 값으로 대체)을 사용합니다.
   
        Remove-AzureVNetGateway –VnetName MyAzureVNET
3. 가상 네트워크 스키마를 내보냅니다. 다음 PowerShell cmdlet(사용자 고유의 값으로 대체)을 사용합니다.
   
        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”
4. 게이트웨이 서브넷이 /27 또는 더 짧은 접두사가 되도록 네트워크 구성 파일 스키마를 편집합니다.(/26 또는 /25와 같이) 다음 예제를 참조하세요. 
   
   > [!NOTE]
   > 가상 네트워크에 게이트웨이 서브넷 크기를 늘릴 수 있는 IP 주소가 충분히 남아 있지 않을 경우 추가 IP 주소 공간을 추가해야 합니다. 구성 스키마에 대한 자세한 내용은 [Azure Virtual Network 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.
   > 
   > 
   
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
5. 이전 게이트웨이가 사이트 간 VPN인 경우 연결 형식을 **전용**으로 변경해야 합니다.
   
                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
6. 이제 게이트웨이가 없는 VNet이 설정됩니다. 새 게이트웨이를 만들고 연결을 완료하려면 이전 단계의 [4단계 - ExpressRoute 게이트웨이 만들기](#gw)를 진행합니다.

## <a name="next-steps"></a>다음 단계
ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)

