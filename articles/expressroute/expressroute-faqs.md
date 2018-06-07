---
title: Azure ExpressRoute FAQ | Microsoft Docs
description: ExpressRoute FAQ는 Azure 서비스, 비용, 데이터 및 연결, SLA, 공급자 및 위치, 대역폭 및 추가 기술 세부 정보에 대한 정보를 포함합니다.
documentationcenter: na
services: expressroute
author: cherylmc
manager: jeconnoc
editor: ''
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/14/2018
ms.author: cherylmc
ms.openlocfilehash: f68a4d699d1ad6592fe5481cd69795b06e7d8fca
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160872"
---
# <a name="expressroute-faq"></a>ExpressRoute FAQ

## <a name="what-is-expressroute"></a>ExpressRoute란?

ExpressRoute는 온-프레미스 또는 공동 장소 환경의 Microsoft 데이터 센터와 인프라 사이에 개인 연결을 만들 수 있게 해 주는 서비스입니다. ExpressRoute 연결은 공용 인터넷을 사용하지 않으며 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>ExpressRoute 및 개인 네트워크 연결을 사용할 경우 이점은 무엇인가요?

ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 일반적인 연결보다 더 짧고 일관된 대기 시간으로 더 높은 보안, 안정성 및 속도를 제공합니다. 경우에 따라 온-프레미스 장치와 Azure 간 데이터 전송에 ExpressRoute 연결을 사용하면 상당한 비용 혜택을 얻을 수 있습니다.

### <a name="where-is-the-service-available"></a>서비스를 사용할 수 있는 곳은 어디인가요?

서비스 위치 및 가용성은 [ExpressRoute 파트너 및 위치](expressroute-locations.md)페이지를 참조하세요.

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>ExpressRoute 통신 업체 중 하나와 파트너의 관계가 아닌 경우, ExpressRoute를 사용하여 Microsoft에 연결할 수 있는 방법이 있나요?

지역 통신 업체를 선택하고 지원되는 exchange 공급자 위치 중 하나에 이더넷 연결을 제공할 수 있습니다. 그러면 공급자 위치에서 Microsoft와 피어링할 수 있습니다. [ 파트너 및 위치](expressroute-locations.md)의 마지막 섹션을 검사하여 서비스 공급자가 Exchange 위치 중 하나에 있는지 확인합니다. 그런 다음 Azure에 연결하려면 서비스 공급자를 통해 ExpressRoute 회로를 주문할 수 있습니다.

### <a name="how-much-does-expressroute-cost"></a>ExpressRoute 비용

가격 정보는 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/) 를 참조하세요.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>지정된 대역폭의 ExpressRoute 회로에 대한 비용을 지불하는 경우, 네트워크 서비스 공급자로부터 구입한 VPN 연결은 동일한 속도여야 하나요?

번호 서비스 공급자로부터 모든 속도의 VPN 연결을 구입할 수 있습니다. 그러나 Azure에 대한 연결은 구입한 ExpressRoute 회로 대역폭으로 제한됩니다.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>지정된 대역폭의 ExpressRoute 회로에 대해 비용을 지불한다면 필요한 경우 더 높은 속도로 버스트할 수 있나요?

예. ExpressRoute 회로는 추가 비용 없이 확보한 대역폭 제한의 최대 2배까지 버스트할 수 있도록 구성됩니다. 이 기능을 지원하는지 확인하려면 해당 서비스 공급자에게 문의하세요.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>가상 네트워크 및 다른 Azure 서비스와 동일한 개인 네트워크 연결을 동시에 사용할 수 있나요?

예. ExpressRoute 회로는 일단 설정되면 가상 네트워크 내 서비스와 다른 Azure 서비스에 동시에 액세스할 수 있습니다. 개인 피어링 경로를 통해 가상 네트워크에 연결하고, 공용 피어링 경로를 통해 다른 서비스에 연결합니다.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute는 SLA(서비스 수준 계약)을 제공하나요?

자세한 내용은 [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) 페이지를 참조하세요.

## <a name="supported-services"></a>지원되는 서비스

ExpressRoute는 다양한 유형의 서비스에 대해 [세 개의 라우팅 도메인](expressroute-circuit-peerings.md)을 지원합니다.

### <a name="private-peering"></a>개인 피어링

* 모든 가상 머신 및 클라우드 서비스를 포함한 가상 네트워크

### <a name="public-peering"></a>공용 피어링

>[!NOTE]
>Microsoft 피어링은 Azure에서 호스트되는 모든 서비스에 액세스하기 위한 기본 방법입니다.
>

* Power BI
* Dynamics 365 for Finance and Operations(이전의 Dynamics AX Online)
* 대부분의 Azure 서비스가 지원됩니다. 사용하려는 서비스의 지원 유무를 직접 확인해 보세요.<br>
  다음 서비스는 지원되지 않습니다.
    * CDN
    * Visual Studio Team Services 부하 테스트
    * Multi-Factor 인증
    * Traffic Manager

### <a name="microsoft-peering"></a>Microsoft 피어링

* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Dynamics 365 Customer Engagement 응용 프로그램(이전의 CRM Online)
  * Dynamics 365 for Sales
  * Dynamics 365 for Customer Service
  * Dynamics 365 for Field Service
  * Dynamics 365 for Project Service
* [경로 필터](#route-filters-for-microsoft-peering)를 사용하면 Microsoft 피어링을 통해 동일한 공용 서비스에 액세스할 수 있습니다.
  * Power BI
  * 재무 및 운영을 위한 Dynamics 365
  * 대부분의 Azure 서비스가 지원됩니다. 사용하려는 서비스의 지원 유무를 직접 확인해 보세요.<br>
  다음 서비스는 지원되지 않습니다.
    * CDN
    * Visual Studio Team Services 부하 테스트
    * Multi-Factor 인증
    * Traffic Manager

## <a name="data-and-connections"></a>데이터 및 연결

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>ExpressRoute를 사용하여 전송할 수 있는 데이터의 양에 제한이 있나요?

데이터 전송 크기에 대한 제한을 설정하지 않습니다. 대역폭 요금에 대한 정보는 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/) 을 참조하세요.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute에서 지원되는 연결 속도는 무엇인가요?

지원되는 대역폭은 다음을 제공합니다.

50Mbps, 100Mbps, 200Mbps, 500Mbps, 1Gbps, 2Gbps, 5Gbps, 10Gbps

### <a name="which-service-providers-are-available"></a>어떤 서비스 공급자를 사용할 수 있나요?

서비스 공급자 및 위치 목록은 [ExpressRoute 파트너 및 위치](expressroute-locations.md) 를 참조하세요.

## <a name="technical-details"></a>기술 세부 정보

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>내 온-프레미스 위치를 Azure에 연결하기 위한 기술 요구 사항은 무엇인가요?

요구 사항은 [ExpressRoute 필수 구성 요소 페이지](expressroute-prerequisites.md)를 참조하세요.

### <a name="are-connections-to-expressroute-redundant"></a>ExpressRoute에 대한 연결이 중복되나요?

예. 각 ExpressRoute 회로에는 고가용성을 제공하도록 구성된 중복 쌍의 교차연결이 있습니다.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>내 ExpressRoute 링크 중 하나가 실패하면 연결이 끊어지나요?

교차 연결 중 하나가 실패할 경우 연결이 손실되지 않습니다. 네트워크의 부하를 지원하고 ExpressRoute 회로의 고가용성을 제공하기 위해 중복 연결을 사용할 수 있습니다. 회로 수준 복원력을 얻기 위해 다른 피어링 위치에 추가로 회로를 만들 수 있습니다.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>ExpressRoute에 연결된 가상 네트워크에서 고가용성을 보장하려면 어떻게 해야 하나요?

다른 피어링 위치(예: Singapore, Singapore2)에 있는 ExpressRoute 회로를 가상 네트워크에 연결하여 고가용성을 달성할 수 있습니다. 한 ExpressRoute 회로가 다운되면 연결이 다른 ExpressRoute 회로로 장애 조치(Failover)됩니다. 기본적으로 가상 네트워크에서 나오는 트래픽은 ECMP(동일 비용 다중 경로 라우팅)를 기준으로 라우팅됩니다. 연결 가중치를 사용하여 특정 회로를 기본 회로로 사용할 수 있습니다. 연결 가중치에 대한 자세한 내용은 [ExpressRoute 라우팅 최적화](expressroute-optimize-routing.md)를 참조하세요.

### <a name="onep2plink"></a>클라우드 교환에 공동 배치되지 않았으며 서비스 공급자가 점 대 점 연결을 공급하는 경우 온-프레미스 네트워크와 Microsoft 간에 두 개의 실제 연결을 주문해야 하나요?

서비스 공급자가 실제 연결을 통해 두 개의 이더넷 가상 회로를 설정할 수 있으면 하나의 실제 연결만 필요합니다. 실제 연결(예: 광섬유)은 레이어 1(L1) 장치에서 종료됩니다(이미지 참조). 두 개의 이더넷 가상 회로에는 서로 다른 VLAN ID로 태그가 지정되는데, 하나는 기본 회로용이고 다른 하나는 보조 회로용입니다. 해당 VLAN ID는 외부 802.1Q 이더넷 헤더에 있습니다. 내부 802.1Q 이더넷 헤더(표시되지 않음)는 특정 [ExpressRoute 라우팅 도메인](expressroute-circuit-peerings.md)에 매핑됩니다.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>ExpressRoute를 사용하여 Azure에 대한 내 VLAN 중 하나를 확장할 수 있나요?

번호 Azure까지의 계층 2 연결 확장을 지원하지 않습니다.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>내 구독에 둘 이상의 ExpressRoute 회로가 있을 수 있나요?

예. 사용자 구독에 둘 이상의 ExpressRoute 회로가 있을 수 있습니다. 기본 제한은 10으로 설정되어 있습니다. 필요한 경우 Microsoft 지원에 문의하여 제한을 늘릴 수 있습니다.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>다른 서비스 공급자의 ExpressRoute 회로를 사용할 수 있나요?

예. 여러 서비스 공급자의 ExpressRoute 회로가 있을 수 있습니다. 각 ExpressRoute 회로마다 하나의 서비스 공급자와만 연결됩니다. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-eg-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>동일한 메트로에 두 ExpressRoute 피어링 위치(예: Singapore 및 Singapore2)가 있습니다. 내 ExpressRoute 회로를 어느 피어링 위치에 만들어야 합니까?
서비스 공급자가 두 사이트 모두에서 ExpressRoute를 제공할 경우 공급자와 함께 ExpressRoute를 설정할 사이트를 선택할 수 있습니다. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>같은 메트로에 여러 ExpressRoute 회로를 포함할 수 있나요? 동일한 가상 네트워크에 연결할 수 있나요?

예. 같거나 다른 서비스 공급자로 여러 ExpressRoute 회로를 포함할 수 있습니다. 메트로에 여러 개의 ExpressRoute 피어링 위치가 있고 다른 피어링 위치에 회로가 만들어지는 경우 이러한 회로를 동일한 가상 네트워크에 연결할 수 있습니다. 회로가 동일한 피어링 위치에 생성되면 동일한 가상 네트워크에 연결할 수 없습니다. Azure Portal 또는 PowerShell/CLI API의 각 위치 이름은 하나의 피어링 위치를 나타냅니다. 예를 들어 "Singapore" 및 "Singapore2" 피어링 위치를 선택하고 각 회로를 동일한 가상 네트워크에 연결할 수 있습니다. 

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>가상 네트워크를 ExpressRoute 회로에 연결하려면 어떻게 해야 하나요?

기본 단계는 다음과 같습니다.

* ExpressRoute 회로를 설정하고 서비스 공급자가 이를 사용하도록 설정합니다.
* 사용자 또는 공급자가 BGP 피어링을 구성해야 합니다.
* 가상 네트워크를 ExpressRoute 회로에 연결합니다.

자세한 내용은 [회로 프로비전 및 회로 상태에 대한 ExpressRoute 워크플로](expressroute-workflows.md)를 참조하세요.

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>내 ExpressRoute 회로에 대한 연결 경계가 있나요?

예. [ExpressRoute 파트너 및 위치](expressroute-locations.md) 문서에서 ExpressRoute 회로에 대한 연결 경계를 간략하게 설명하고 있습니다. ExpressRoute 회로에 대한 연결은 단일 지역으로 제한됩니다. 연결은 ExpressRoute 프리미엄 기능을 사용하여 지역을 교차하도록 확장할 수 있습니다.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>둘 이상의 가상 네트워크를 ExpressRoute 회로에 연결할 수 있나요?

예. 표준 ExpressRoute 회로에는 가상 네트워크 연결을 최대 10개, [프리미엄 ExpressRoute 회로](#expressroute-premium)에는 최대 100개를 포함할 수 있습니다. 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>가상 네트워크를 포함하는 여러 Azure 구독을 가지고 있습니다. 개별 구독에 속한 가상 네트워크를 단일 ExpressRoute 회로에 연결할 수 있나요?

예. 단일 ExpressRoute 회로를 사용하여 다른 Azure 구독을 최대 10개까지 권한을 부여할 수 있습니다. ExpressRoute 프리미엄 기능을 사용하여 이 한도를 늘릴 수 있습니다.

자세한 내용은 [여러 구독에서 ExpressRoute 회로 공유](expressroute-howto-linkvnet-arm.md)를 참조하세요.

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>다른 Azure Active Directory 테넌트 또는 기업계약 등록에 연결된 여러 Azure 구독이 있습니다. 별도 테넌트 및 등록에 속하는 가상 네트워크를 동일한 테넌트 또는 등록에 없는 단일 ExpressRoute 회로에 연결할 수 있나요?

예. ExpressRoute 인증은 추가 구성 없이도 구독, 테넌트 및 등록 간에 적용될 수 있습니다. 

자세한 내용은 [여러 구독에서 ExpressRoute 회로 공유](expressroute-howto-linkvnet-arm.md)를 참조하세요.

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>가상 네트워크가 서로 격리된 동일한 회로에 연결되어 있나요?

번호 라우팅 관점에서 동일한 ExpressRoute 회로에 연결된 모든 가상 네트워크는 동일한 라우팅 도메인의 일부이며 서로 격리되지 않습니다. 경로 격리가 필요하면 별도의 ExpressRoute 회로를 만들어야 합니다.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>하나의 가상 네트워크를 둘 이상의 ExpressRoute 회로에 연결할 수 있나요?

예. 단일 가상 네트워크를 최대 4개의 ExpressRoute 회로와 연결할 수 있습니다. 4개의 다른 [ExpressRoute 위치](expressroute-locations.md)를 통해 순서를 지정해야 합니다.

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 회로에 연결된 가상 네트워크에서 인터넷에 액세스할 수 있나요?

예. BGP 세션을 통해 기본 경로(0.0.0.0/0) 또는 인터넷 경로 접두사를 보급하지 않았다면 ExpressRoute 회로에 연결된 가상 네트워크에서 인터넷에 연결할 수 있습니다.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 회로에 연결된 가상 네트워크에 대한 인터넷 연결을 차단할 수 있나요?

예. 기본 경로(0.0.0.0/0)를 보급하여 가상 네트워크 내에 배포된 가상 머신에 대한 모든 인터넷 연결을 차단하고 ExpressRoute 회로를 통해 모든 트래픽을 라우팅할 수 있습니다.

기본 경로를 보급하는 경우 공용 피어링(예: Azure storage 및 SQL DB)을 통해 프레미스에 다시 제공된 서비스로 트래픽을 수행합니다. 공용 피어링 경로 또는 인터넷을 통해 트래픽을 Azure로 반환하도록 라우터를 구성해야 합니다. 서비스에 대해 서비스 끝점(미리 보기)을 사용하도록 설정한 경우 서비스에 대한 트래픽은 프레미스에 강제로 전송되지 않습니다. 트래픽은 Azure 백본 네트워크 내에 유지됩니다. 서비스 끝점에 대한 자세한 내용은 [가상 네트워크 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)을 참조하세요.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>동일한 ExpressRoute 회로에 연결된 가상 네트워크가 서로 통신할 수 있나요?

예. 동일한 ExpressRoute 회로에 연결된 가상 네트워크에 배포된 가상 머신은 서로 통신할 수 있습니다.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>ExpressRoute와 함께 가상 네트워크에 대한 사이트 간 연결을 사용할 수 있나요?

예. ExpressRoute는 사이트 간 VPN과 공존할 수 있습니다.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>ExpressRoute를 사용하여 사이트 간 / 지점과 사이트 간 구성에서 가상 네트워크를 이동할 수 있나요?

예. 가상 네트워크 내에 ExpressRoute 게이트웨이를 만들어야 합니다. 프로세스와 관련된 가동 중지 시간이 짧습니다.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>가상 네트워크에서 ExpressRoute 게이트웨이와 연결된 공용 IP 주소가 있는 이유는 무엇인가요?

공용 IP 주소는 내부 관리용으로만 사용되며, 가상 네트워크의 보안 노출을 구성하지 않습니다.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>ExpressRoute를 통해 Azure 저장소에 연결해야 하나요?

ExpressRoute 회로를 설정하고 공용 피어링에 대한 경로를 구성해야 합니다.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>보급할 수 있는 경로의 수에 제한이 있나요?

예. 개인 피어링에 대해 최대 4000개의 경로 접두사를 허용하고, 공용 피어링과 Microsoft 피어링에 대해 각각 200개의 경로 접두사를 허용합니다. ExpressRoute 프리미엄 기능을 사용하도록 설정하면 개인 피어링에 대해 10,000개의 경로까지 늘릴 수 있습니다.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>BGP 세션을 통해 보급할 수 있는 IP 범위에 제한 사항이 있나요?

공용 및 Microsoft 피어링 BGP 세션에서 개인 접두사(RFC1918)는 허용하지 않습니다.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>BGP 제한을 초과하면 어떻게 되나요?

BGP 세션이 삭제됩니다. 접두사 개수가 제한보다 적으면 재설정됩니다.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>ExpressRoute BGP 확보 시간이란 무엇인가요? 조정할 수 있나요?

확보 시간은 180입니다. 연결 유지 메시지는 60초마다 전송됩니다. 이 설정은 Microsoft 쪽에서 고정된 설정으로, 변경할 수 없습니다. 다른 타이머를 구성 할 수 있으며, 이에 따라 BGP 세션 매개 변수가 협상됩니다.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>가상 네트워크에 기본 경로(0.0.0.0/0)를 보급한 후 Azure VM에서 실행되는 Windows를 활성화할 수 없습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

다음 단계는 Azure에서 활성화 요청을 인식하는 데 도움이 됩니다.

1. ExpressRoute 회로에 공용 피어링을 설정합니다.
2. DNS 조회를 수행하고 **kms.core.windows.net**
3. 키 관리 서비스는 활성화 요청이 Azure에서 제공됨을 인식하고 해당 요청을 승인해야 합니다. 다음 세 가지 작업 중 하나를 수행합니다.

   * 온-프레미스 네트워크에서 2단계에서 얻은 IP 주소로 향하는 트래픽을 공용 피어링을 통해 Azure로 다시 라우팅합니다.
   * NSP 공급자는 공용 피어링을 통해 Azure에 다시 트래픽을 hair-pin합니다.
   * 인터넷을 다음 홉으로 사용하는 IP를 가리키는 사용자 정의 경로를 만들고 이러한 가상 머신이 있는 서브넷에 적용합니다.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 회로의 대역폭을 변경할 수 있나요?

예, Azure Portal 또는 PowerShell을 사용하여 ExpressRoute 회로의 대역폭을 늘릴 수 있습니다. 회로가 만들어진 실제 포트에 사용 가능한 용량이 있으면 성공적으로 변경된 것입니다. 

변경이 실패하면 현재 포트에 남아 있는 용량이 부족하거나(이 경우 더 높은 대역폭으로 새 ExpressRoute 회로를 만들어야 함) 해당 위치에 추가 용량이 없습니다(이 경우 대역폭을 늘릴 수 없음). 

또한 연결 공급자는 네트워크 내 제한을 업데이트하여 대역폭 증가를 지원하도록 후속 작업해야 합니다. 하지만 ExpressRoute 회로의 대역폭은 줄일 수 없습니다. 낮은 대역폭으로 새 ExpressRoute 회로를 만들고, 이전 회로는 삭제해야 합니다.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 회로의 대역폭을 변경하려면 어떻게 해야 하나요?

REST API 및 PowerShell cmdlet을 사용하여 ExpressRoute 회로의 대역폭을 업데이트할 수 있습니다.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>ExpressRoute 프리미엄이란?

ExpressRoute Premium은 다음 기능의 모음입니다.

* 증가된 라우팅 테이블은 개인 피어링에 대해 4000개의 경로에서 경로 10, 000개의 경로로 제한합니다.
* ExpressRoute 회로에 연결할 수 있는 VNets 수가 증가합니다(기본값은 10). 자세한 내용은 [ExpressRoute 제한](#limits) 표를 참조하세요.
* Office 365 및 Dynamics 365에 연결합니다.
* Microsoft 핵심 네트워크를 통해 전역 연결합니다. 이제 한 지리적 지역의 VNet을 다른 지역의 ExpressRoute 회로와 연결할 수 있습니다.<br>
    **예제:**

    *  유럽 서부에서 만든 VNet을 실리콘밸리에서 만든 ExpressRoute 회로에 연결할 수 있습니다. 
    *  공용 피어링에서는 실리콘밸리의 회로에서 유럽 서부의 SQL Azure에 연결할 수 있는 것처럼 다른 지리적 지역의 접두사가 보급됩니다.


### <a name="limits"></a>ExpressRoute Premium을 사용하는 경우 ExpressRoute 회로에 연결할 수 있는 VNet의 수는 어떻게 되나요?

아래 표에서는 ExpressRoute 제한 및 ExpressRoute 회로당 VNet 수를 보여 줍니다.

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>ExpressRoute Premium을 사용하려면 어떻게 하나요?

ExpressRoute Premium 기능은 사용하도록 설정되면 활성화할 수 있으며, 회로 상태를 업데이트하여 종료할 수 있습니다. ExpressRoute Premium은 회로를 만들 때 또는 REST API/PowerShell cmdlet을 호출하여 사용하도록 설정할 수 있습니다.

### <a name="how-do-i-disable-expressroute-premium"></a>ExpressRoute Premium을 사용하지 않도록 하려면 어떻게 하나요?

ExpressRoute Premium은 REST API/PowerShell cmdlet을 호출하여 사용하지 않도록 설정할 수 있습니다. ExpressRoute Premium을 사용하지 않도록 설정하기 전에 기본 제한을 충족하도록 연결 요구 사항을 조정했는지 확인해야 합니다. 사용률이 기본 제한을 초과하면 ExpressRoute Premium을 사용하지 않도록 설정하는 요청이 실패합니다.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>프리미엄 기능 집합에서 원하는 기능을 선택할 수 있나요?

번호 기능은 선택할 수 없습니다. ExpressRoute Premium을 켜면 모든 기능을 사용합니다.

### <a name="how-much-does-expressroute-premium-cost"></a>ExpressRoute Premium 비용

비용은 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/) 을 참조하세요.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>표준 ExpressRoute 요금 외에도 ExpressRoute premium에 대한 납부 여부

예. ExpressRoute premium 요금은 ExpressRoute 회로 요금 및 연결 공급자에서 필요한 요금 위에 적용됩니다.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>Office 365 및 Dynamics 365에 대한 ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Office 365 서비스 및 Dynamics 365에 연결하려면 어떻게 ExpressRoute 회로를 만드나요?

1. [ExpressRoute 필수 구성 요소 페이지](expressroute-prerequisites.md)를 검토하여 요구 사항을 충족하는지 확인합니다.
2. 사용자 연결 요구 사항이 충족되는지 확인하려면 [ExpressRoute 파트너 및 위치](expressroute-locations.md) 문서에서 서비스 공급자 및 위치 목록을 검토합니다.
3. [Office 365에 대한 네트워크 계획 및 성능 튜닝](http://aka.ms/tune/)을 검토하여 용량 요구 사항을 계획합니다.
4. [회로 프로비전 및 회로 상태에 대한 ExpressRoute 워크플로](expressroute-workflows.md)의 연결 설정 워크플로에 나열된 단계를 따릅니다.

> [!IMPORTANT]
> Office 365 서비스 및 Dynamics 365에 대한 연결을 구성할 때 ExpressRoute Premium 추가 기능을 사용하도록 설정했는지 확인하세요.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>Office 365 서비스 및 Dynamics 365에 연결하려면 Azure 공용 피어링을 사용하도록 설정해야 하나요?

아니요, Microsoft 피어링을 사용하도록 설정하기만 하면 됩니다. Azure AD에 대한 인증 트래픽은 Microsoft 피어링을 통해 전송됩니다. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>기존 ExpressRoute 회로가 Office 365 서비스 및 Dynamics 365에 대한 연결을 지원할 수 있나요?

예. 기존 ExpressRoute 회로가 Office 365 서비스에 대한 연결을 지원하도록 구성할 수 있나요? Office 365 서비스에 연결하는 데 충분한 용량이 있는지와 프리미엄 추가 기능을 사용하도록 설정했는지를 확인합니다. [Office 365의 네트워크 계획 및 성능 조정](http://aka.ms/tune/)을 참조하면 연결 요구 사항을 계획할 수 있습니다. [ExpressRoute 회로 만들기 및 수정](expressroute-howto-circuit-classic.md)도 참조하세요.

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>ExpressRoute 연결을 통해 어느 Office 365 서비스에 액세스할 수 있나요?

ExpressRoute에서 지원되는 최신 서비스 목록은 [Office 365 URL 및 IP 주소 범위](http://aka.ms/o365endpoints) 페이지를 참조하세요.

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Office 365 서비스 및 Dynamics 365용 ExpressRoute 비용은 얼마인가요?

Office 365 서비스 및 Dynamics 365를 사용하려면 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 자세한 내용은 [가격 세부 정보 페이지](https://azure.microsoft.com/pricing/details/expressroute/)를 참조하세요.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Office 365용 ExpressRoute가 지원하는 영역

[ExpressRoute 파트너 및 위치](expressroute-locations.md)를 참조하세요.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>ExpressRoute가 내 조직에 구성되어 있더라도 인터넷을 통해 Office 365에 액세스할 수 있나요?

예. ExpressRoute가 네트워크에 구성되어 있더라도 인터넷을 통해 Office 365 서비스 끝점에 연결할 수 있습니다. 사용자의 위치에서 네트워크가 ExpressRoute 통해 Office 365 서비스에 연결되도록 구성하는 경우 조직의 네트워킹 팀에 문의하십시오.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Azure ExpressRoute에서 Office 365 네트워크 트래픽에 대한 고가용성을 위해 어떤 계획이 가능합니까?
[Azure ExpressRoute의 고가용성 및 장애 조치](https://aka.ms/erhighavailability)에 대한 권장 사항 참조

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Azure 미국 정부 ExpressRoute 회로를 통해 Office 365 미국 정부 커뮤니티(GCC) 서비스에 액세스할 수 있나요?

예. Office 365 GCC 서비스 끝점은 Azure 미국 정부 ExpressRoute를 통해 연결할 수 있습니다. 그러나 먼저 Azure Portal에서 지원 티켓을 열어 Microsoft에 보급하려는 접두사를 제공해야 합니다. 지원 티켓이 확인된 후 Office 365 GCC 서비스로의 연결이 설정됩니다. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>ExpressRoute 연결을 통해 Dynamics 365 for Operations(이전의 Dynamics AX Online)에 액세스할 수 있나요?

예. [Dynamics 365 for Operations](https://www.microsoft.com/dynamics365/operations)가 Azure에서 호스팅됩니다. 연결할 ExpressRoute 회로에서 Azure 공용 피어링을 연결할 수 있습니다.

## <a name="route-filters-for-microsoft-peering"></a>Microsoft 피어링용 경로 필터

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Microsoft 피어링을 처음 설정해 봅니다. 어떤 경로를 확인해야 하나요?

경로는 표시되지 않습니다. 접두사 보급을 시작하려면 회로에 경로 필터를 연결해야 합니다. 자세한 내용은 [Microsoft 피어링용 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Microsoft 피어링을 사용하도록 설정했고, 지금은 Exchange Online을 선택하려고 했지만 이 작업을 수행할 권한이 없다는 오류 메시지가 표시됩니다.

경로 필터를 사용하면 모든 고객이 Microsoft 피어링을 설정할 수 있습니다. 그러나 Office 365 서비스를 사용하려면 여전히 Office 365에서 권한을 부여받아야 합니다.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Microsoft 피어링을 통해 Dynamics 365를 설정하는 경우 필요한 권한을 부여받아야 하나요?

아니요, Dynamics 365에 대해 권한을 부여받을 필요가 없습니다. 규칙을 만든 다음, 권한을 부여받지 않고 Dynamics 365 커뮤니티를 선택하면 됩니다.

### <a name="i-enabled-microsoft-peering-prior-to-august-1st-2017-how-can-i-take-advantage-of-route-filters"></a>2017년 8월 1일 이전에 Microsoft 피어링을 사용하도록 설정했습니다. 경로 필터를 사용하려면 어떻게 해야 하나요?

기존 회로가 Office 365 및 Dynamics 365용 접두사를 계속 보급하게 됩니다. 동일한 Microsoft 피어링을 통해 Azure 공용 접두사 보급 알림을 추가하려면 경로 필터를 만들고, 보급해야 하는 서비스(필요한 Office 365 서비스 및 Dynamics 365 포함)를 선택하고, 해당 필터를 Microsoft 피어링에 추가할 수 있습니다. 자세한 내용은 [Microsoft 피어링용 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>한 위치에서 Microsoft 피어링을 사용하고 있습니다. 이제 다른 위치에서 사용하도록 설정하려고 했지만 접두사가 표시되지 않습니다.

* 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다.

* 2017년 8월 1일 이후에 구성되는 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터를 회로에 연결할 때까지 접두사가 보급되지 않습니다. 접두사는 기본적으로 표시되지 않습니다.
