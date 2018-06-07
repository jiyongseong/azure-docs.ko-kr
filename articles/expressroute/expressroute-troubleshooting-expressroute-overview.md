---
title: '연결 확인: Azure ExpressRoute 문제 해결 가이드 | Microsoft Docs'
description: ExpressRoute 회로에 대한 종단 간 연결의 유효성 검사 및 문제 해결에 대한 지침을 제공합니다.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 5d01f2e402e4b793274761703ec3ca1ea3ff8164
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="verifying-expressroute-connectivity"></a>ExpressRoute 연결 확인
연결 공급자가 지원하는 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장하는 ExpressRoute에는 다음 세 가지 고유 네트워크 영역이 필요합니다.

-   고객 네트워크
-   공급자 네트워크
-   Microsoft 데이터 센터

이 문서는 사용자가 연결 문제가 있는 위치와 해당 영역을 파악하여 문제를 해결할 수 있도록 적절한 팀에 지원을 요청하는 데 도움이 되기 위한 것입니다. 문제를 해결하기 위해 지원이 필요하면 [Microsoft 지원][Support]에서 지원 티켓을 열어주세요.

> [!IMPORTANT]
> 이 문서는 간단한 문제를 진단하고 수정하는 데 사용됩니다. 이 문서는 Microsoft 기술 지원 서비스를 대체하지 않습니다. 제공된 지침을 사용하여 문제를 해결할 수 없으면 [Microsoft 지원][Support]에서 지원 티켓을 열어주세요.
>
>

## <a name="overview"></a>개요
다음 다이어그램에서는 ExpressRoute를 통한 고객 네트워크와 Microsoft 네트워크 간의 논리적 연결을 보여 줍니다.
[![1]][1]

위의 다이어그램에서 숫자는 주요 네트워크 지점을 나타냅니다. 이 문서에서 네트워크 지점은 관련 번호로 자주 참조됩니다.

ExpressRoute 연결 모델(Cloud Exchange Co-location, 지점 간 이더넷 연결 또는 Any-to-Any(IPVPN))에 따라 네트워크 지점 3 및 4는 스위치(계층 2 장치)일 수 있습니다. 위의 다이어그램에서 보여 주는 주요 네트워크 지점은 다음과 같습니다.

1.  고객 계산 장치(예: 서버 또는 PC)
2.  CE: 고객 에지 라우터 
3.  PE(CE 연결): 고객 에지 라우터에 연결되는 공급자 에지 라우터/스위치입니다. 이 문서에서는 PE-CE라고 합니다.
4.  PE(MSEE 연결): MSEE에 연결되는 공급자 에지 라우터/스위치입니다. 이 문서에서는 PE-MSEE라고 합니다.
5.  MSEE: MSEE(Microsoft Enterprise Edge) ExpressRoute 라우터
6.  VNet(Virtual Network) 게이트웨이
7.  Azure VNet의 계산 장치

연결 모델로 Cloud Exchange Co-location 또는 지점 간 이더넷 연결을 사용하는 경우 고객 에지 라우터(2)에서 MSEE(5)와의 BGP 피어링을 설정합니다. 네트워크 지점 3과 4는 여전히 존재하지만 계층 2 장치로 다소 투명하게 됩니다.

Any-to-Any(IPVPN) 연결 모델을 사용하는 경우 PE(MSEE 연결)(4)에서 MSEE(5)와의 BGP 피어링을 설정합니다. 그러면 경로가 IPVPN 서비스 공급자 네트워크를 통해 고객 네트워크로 다시 전파됩니다.

>[!NOTE]
>ExpressRoute 고가용성을 위해 MSEE(5)와 PE-MSEE(4) 간에 중복 쌍의 BGP 세션이 필요합니다. 고객 네트워크와 PE-CE 간에도 중복 쌍의 네트워크 경로를 사용하는 것이 좋습니다. 그러나 Any-to-Any(IPVPN) 연결 모델에서는 단일 CE 장치(2)가 하나 이상의 PE(3)에 연결될 수 있습니다.
>
>

ExpressRoute 회로의 유효성을 검사하기 위해 다음 단계가 적용됩니다(관련 번호로 표시된 네트워크 지점 사용).
1. [회로 프로비전 및 상태 확인 유효성 검사(5)](#validate-circuit-provisioning-and-state)
2. [하나 이상의 ExpressRoute 피어링이 구성되었는지 확인(5)](#validate-peering-configuration)
3. [Microsoft와 서비스 공급자 간의 ARP 유효성 검사(4와 5 사이의 링크)](#validate-arp-between-microsoft-and-the-service-provider)
4. [MSEE에서 BGP 및 경로 유효성 검사(VNet이 연결된 경우 4와 5, 5와 6 사이의 BGP)](#validate-bgp-and-routes-on-the-msee)
5. [트래픽 통계 확인(5를 통과하는 트래픽)](#check-the-traffic-statistics)

나중에 더 많은 유효성 검사와 확인이 추가되며 매월 다시 확인해 주세요!

## <a name="validate-circuit-provisioning-and-state"></a>회로 프로비전 및 상태 유효성 검사
연결 모델에 관계 없이 ExpressRoute 회로를 만들어야 하므로 회로 프로비전을 위해 생성된 서비스 키가 필요합니다. ExpressRoute 회로를 프로비전하면 PE-MSEE(4)와 MSEE(5) 간에 계층 2 중복 연결이 설정됩니다. ExpressRoute 회로 만들기, 수정, 프로비전 및 확인 방법에 대한 자세한 내용은 [ExpressRoute 회로 만들기 및 수정][CreateCircuit] 문서를 참조하세요.

>[!TIP]
>서비스 키는 ExpressRoute 회로를 고유하게 식별합니다. 이 키는 이 문서에서 언급하는 PowerShell 명령 대부분에 필요합니다. 또한 Microsoft 또는 ExpressRoute 파트너로부터 도움을 받아 ExpressRoute 문제를 해결하려면 회로를 쉽게 식별할 수 있도록 서비스 키를 제공해야 합니다.
>
>

### <a name="verification-via-the-azure-portal"></a>Azure Portal을 통한 확인
Azure Portal에서 ExpressRoute 회로의 상태는 왼쪽 세로 막대 메뉴에서 ![2][2]를 선택한 다음 ExpressRoute 회로를 선택하여 확인할 수 있습니다. "모든 리소스" 아래에 나열된 ExpressRoute 회로를 선택하면 ExpressRoute 회로 블레이드가 열립니다. 블레이드의 ![3][3] 섹션에는 ExpressRoute Essentials가 다음 스크린샷과 같이 나열됩니다.

![4][4]    

ExpressRoute Essentials에서 *회로 상태*는 Microsoft 쪽 회로 상태를 나타냅니다. *공급자 상태*는 서비스 공급자 쪽에서 회로가 *프로비전됨/프로비전되지 않음*인지를 나타냅니다. 

ExpressRoute 회로가 작동하려면 *회로 상태*가 *사용*이고, *공급자 상태*가 *프로비전됨*이어야 합니다.

>[!NOTE]
>*회로 상태*가 ‘사용’이 아니면 [Microsoft 지원][Support]에 문의하고, *공급자 상태*가 ‘프로비전되지 않음’이면 서비스 공급자에게 문의하세요.
>
>

### <a name="verification-via-powershell"></a>PowerShell을 통한 확인
리소스 그룹의 모든 ExpressRoute 회로를 나열하려면 다음 명령을 사용합니다.

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Azure를 통해 리소스 그룹 이름을 가져올 수 있습니다. 이 문서의 이전 하위 섹션을 참조하고, 리소스 그룹 이름은 예제 스크린샷에 나열되어 있습니다.
>
>

[리소스 그룹]에서 특정 ExpressRoute 회로를 선택하려면 다음 명령을 사용합니다.

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

샘플 응답은 다음과 같습니다.

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

ExpressRoute 회로가 작동하는지 확인하려면 다음 필드에 특히 주의하세요.

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>*CircuitProvisioningState*가 '사용'이 아니면 [Microsoft 지원][Support]에 문의하고, *ServiceProviderProvisioningState*가 '프로비전되지 않음'이면 서비스 공급자에게 문의하세요.
>
>

### <a name="verification-via-powershell-classic"></a>PowerShell(클래식)을 통한 확인
구독 중인 모든 ExpressRoute 회로를 나열하려면 다음 명령을 사용합니다.

    Get-AzureDedicatedCircuit

특정 ExpressRoute 회로를 선택하려면 다음 명령을 사용합니다.

    Get-AzureDedicatedCircuit -ServiceKey **************************************

샘플 응답은 다음과 같습니다.

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

ExpressRoute 회로가 작동하는지 확인하려면 ServiceProviderProvisioningState : Provisioned Status                           : Enabled 필드에 특히 주의하세요.

>[!NOTE]
>*상태*가 ‘사용’이 아니면 [Microsoft 지원][Support]에 문의하고, *ServiceProviderProvisioningState*가 '프로비전되지 않음'이면 서비스 공급자에게 문의하세요.
>
>

## <a name="validate-peering-configuration"></a>피어링 구성 유효성 검사
서비스 공급자가 ExpressRoute 회로 프로비전을 완료하면 MSEE-PR(4)와 MSEE(5) 사이의 ExpressRoute 회로를 통해 라우팅 구성을 만들 수 있습니다. ExpressRoute 회로마다 하나, 둘 또는 셋의 라우팅 컨텍스트, 즉 Azure 개인 피어링(Azure에서 개인 가상 네트워크로의 트래픽), Azure 공용 피어링(Azure에서 공용 IP 주소로의 트래픽) 및 Microsoft 피어링(Office 365 및 Dynamics 365로의 트래픽)을 사용할 수 있습니다. 라우팅 구성을 만들고 수정하는 방법에 대한 자세한 내용은 [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering] 문서를 참조하세요.

### <a name="verification-via-the-azure-portal"></a>Azure Portal을 통한 확인

>[!NOTE]
>레이어 3을 서비스 공급자가 제공하고 포털에서 피어링이 비어 있는 경우 포털의 새로 고침 단추를 사용하여 회로 구성을 새로 고칩니다. 이 작업은 회로에 올바른 라우팅 구성을 적용합니다. 
>
>

Azure Portal에서 ExpressRoute 회로의 상태는 왼쪽 세로 막대 메뉴에서 ![2][2]를 선택한 다음 ExpressRoute 회로를 선택하여 확인할 수 있습니다. "모든 리소스" 아래에 나열된 ExpressRoute 회로를 선택하면 ExpressRoute 회로 블레이드가 열립니다. 블레이드의 ![3][3] 섹션에는 ExpressRoute Essentials가 다음 스크린샷과 같이 나열됩니다.

![5][5]

앞의 예제에서 명시한 대로 Azure 개인 피어링 라우팅 컨텍스트는 사용할 수 있지만, Azure 공용 피어링 및 Microsoft 피어링 라우팅 컨텍스트는 사용할 수 없습니다. 또한 성공적으로 사용되는 피어링 컨텍스트에는 기본 및 보조 지점 간(BGP에 필수) 서브넷이 나열되어 있습니다. /30 서브넷은 MSEE 및 PE-MSEE의 인터페이스 IP 주소에 사용됩니다. 

>[!NOTE]
>피어링을 사용하지 않는 경우 할당된 기본 및 보조 서브넷이 PE-MSEE의 구성과 일치하는지 확인합니다. 그렇지 않은 경우 MSEE 라우터의 구성을 변경하려면 [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]을 참조하세요.
>
>

### <a name="verification-via-powershell"></a>PowerShell을 통한 확인
Azure 개인 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

성공적으로 구성된 개인 피어링에 대한 샘플 응답은 다음과 같습니다.

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 성공적으로 사용되는 피어링 컨텍스트에는 기본 및 보조 주소 접두사가 나열되어 있습니다. /30 서브넷은 MSEE 및 PE-MSEE의 인터페이스 IP 주소에 사용됩니다.

Azure 공용 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Microsoft 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

피어링이 구성되어 있지 않으면 오류 메시지가 표시됩니다. 명시된 피어링(이 예제에서는 Azure 공용 피어링)이 회로 내에 구성되지 않은 경우 샘플 응답은 다음과 같습니다.

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>피어링을 사용하지 않는 경우 할당된 기본 및 보조 서브넷이 연결된 PE-MSEE의 구성과 일치하는지 확인합니다. 또한 올바른 *VlanId*, *AzureASN* 및 *PeerASN*이 MSEE에서 사용되는지 여부와 이러한 값이 연결된 PE-MSEE에서 사용된 값에 매핑되는지 여부를 확인합니다. MD5 해싱을 선택하는 경우 공유 키가 MSEE 및 PE-MSEE 쌍에서 동일해야 합니다. MSEE 라우터의 구성을 변경하려면 [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]을 참조하세요.  
>
>

### <a name="verification-via-powershell-classic"></a>PowerShell(클래식)을 통한 확인
Azure 개인 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

성공적으로 구성된 개인 피어링에 대한 샘플 응답은 다음과 같습니다.

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

성공적으로 사용되는 피어링 컨텍스트에는 기본 및 보조 피어 서브넷이 나열되어 있습니다. /30 서브넷은 MSEE 및 PE-MSEE의 인터페이스 IP 주소에 사용됩니다.

Azure 공용 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Microsoft 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>서비스 공급자가 계층 3 피어링을 설정한 경우 포털 또는 PowerShell을 통해 ExpressRoute 피어링을 설정하면 서비스 공급자 설정을 덮어씁니다. 공급자 쪽 피어링 설정을 다시 설정하려면 서비스 공급자의 지원이 필요합니다. 서비스 공급자가 계층 2 서비스만 제공하는 것이 확실한 경우에만 ExpressRoute 피어링을 수정하세요!
>
>

<p/>
>[!NOTE]
>피어링을 사용하지 않는 경우 할당된 기본 및 보조 피어 서브넷이 연결된 PE-MSEE의 구성과 일치하는지 확인합니다. 또한 올바른 *VlanId*, *AzureAsn* 및 *PeerAsn*이 MSEE에서 사용되는지 여부와 이러한 값이 연결된 PE-MSEE에서 사용된 값에 매핑되는지 여부를 확인합니다. MSEE 라우터의 구성을 변경하려면 [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]을 참조하세요.
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Microsoft와 서비스 공급자 간의 ARP 유효성 검사
이 섹션에서는 PowerShell(클래식) 명령을 사용합니다. PowerShell Azure Resource Manager 명령을 사용하고 있으면 관리자/공동 관리자로서 구독에 액세스할 수 있는지 확인합니다. Azure Resource Manager 명령 사용과 관련된 문제를 해결하려면 [Resource Manager 배포 모델에서 ARP 테이블 가져오기][ARP] 문서를 참조하세요.

>[!NOTE]
>ARP를 가져오려면 Azure Portal 및 Azure Resource Manager PowerShell 명령을 모두 사용할 수 있습니다. Azure Resource Manager PowerShell 명령에 오류가 발생하는 경우 클래식 PowerShell 명령은 Azure Resource Manager ExpressRoute 회로에서도 작동하므로 클래식 PowerShell 명령이 작동해야 합니다.
>
>

개인 피어링을 위해 기본 MSEE 라우터에서 ARP 테이블을 가져오려면 다음 명령을 사용합니다.

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

성공적인 시나리오에서 명령에 대한 응답 예제는 다음과 같습니다.

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

마찬가지로 *개인*/*공용*/*Microsoft* 피어링의 *기본*/*보조* 경로에 있는 MSEE에서 ARP 테이블을 확인할 수 있습니다.

다음 예제에서는 피어링에 대한 명령의 응답이 없음을 보여 줍니다.

    ARP Info:
       
>[!NOTE]
>ARP 테이블에 MAC 주소에 매핑된 인터페이스의 IP 주소가 없으면 다음 정보를 검토합니다.
>1. MSEE-PR과 MSEE 사이의 링크에 할당된 /30 서브넷의 첫 번째 IP 주소가 MSEE-PR의 인터페이스에서 사용되는 경우 Azure에서 항상 두 번째 IP 주소를 MSEE에 사용합니다.
>2. 고객(C-Tag) 및 서비스(S-Tag) VLAN 태그가 MSEE-PR 및 MSEE 쌍 모두와 일치하는지 확인합니다.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>MSEE에서 BGP 및 경로 유효성 검사
이 섹션에서는 PowerShell(클래식) 명령을 사용합니다. PowerShell Azure Resource Manager 명령을 사용하고 있으면 관리자/공동 관리자로서 구독에 액세스할 수 있는지 확인합니다.

>[!NOTE]
>BGP 정보를 가져오려면 Azure Portal 및 Azure Resource Manager PowerShell 명령을 모두 사용할 수 있습니다. Azure Resource Manager PowerShell 명령에 오류가 발생하는 경우 클래식 PowerShell 명령은 Azure Resource Manager ExpressRoute 회로에서도 작동하므로 클래식 PowerShell 명령이 작동해야 합니다.
>
>

특정 라우팅 컨텍스트에 대한 라우팅 테이블(BGP 인접 라우터) 요약을 가져오려면 다음 명령을 사용합니다.

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

예제 응답은 다음과 같습니다.

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

앞의 예제와 같이 이 명령은 라우팅 컨텍스트가 설정된 기간을 확인하는 데 유용합니다. 또한 피어링 라우터에서 보급하는 경로 접두사의 수도 나타냅니다.

>[!NOTE]
>상태가 활성 또는 유휴 상태이면 할당된 기본 및 보조 피어 서브넷이 연결된 PE-MSEE의 구성과 일치하는지 확인합니다. 또한 올바른 *VlanId*, *AzureAsn* 및 *PeerAsn*이 MSEE에서 사용되는지 여부와 이러한 값이 연결된 PE-MSEE에서 사용된 값에 매핑되는지 여부를 확인합니다. MD5 해싱을 선택하는 경우 공유 키가 MSEE 및 PE-MSEE 쌍에서 동일해야 합니다. MSEE 라우터의 구성을 변경하려면 [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]을 참조하세요.
>
>

<p/>
>[!NOTE]
>특정 피어링을 통해 특정 대상에 연결할 수 없는 경우 특정 피어링 컨텍스트에 속한 MSEE의 경로 테이블을 확인하세요. 라우팅 테이블에 일치하는 접두사(NATed IP일 수 있음)가 있는 경우 경로에 방화벽/NSG/ACL이 있는지와 트래픽을 허용하는지를 확인하세요.
>
>

특정 *개인* 라우팅 컨텍스트의 *기본* 경로에 있는 MSEE에서 전체 라우팅 테이블을 가져오려면 다음 명령을 사용합니다.

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

명령의 성공적인 예제 결과는 다음과 같습니다.

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

마찬가지로 *개인*/*공용*/*Microsoft* 피어링 컨텍스트의 *기본*/*보조* 경로에 있는 MSEE에서 라우팅 테이블을 확인할 수 있습니다.

다음 예제에서는 피어링에 대한 명령의 응답이 없음을 보여 줍니다.

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>트래픽 통계 확인
피어링 컨텍스트의 기본 및 보조 경로 트래픽 통계(송/수신 바이트 수)를 가져오려면 다음 명령을 사용합니다.

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

명령 출력의 예제는 다음과 같습니다.

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

존재하지 않는 피어링에 대한 명령 출력의 예제는 다음과 같습니다.

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>다음 단계
자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [Microsoft 지원][Support]
- [ExpressRoute 회로 만들기 및 수정][CreateCircuit]
- [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "논리 ExpressRoute 연결"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "모든 리소스 아이콘"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "개요 아이콘"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials 샘플 스크린샷"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials 샘플 스크린샷"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






