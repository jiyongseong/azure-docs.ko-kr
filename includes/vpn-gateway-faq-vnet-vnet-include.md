---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66ff1e2e02728e05cb0aeedce90de1882a8804ce
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
VNet 간 FAQ는 VPN Gateway 연결에 적용됩니다. VNet 피어링을 찾으려면 [Virtual Network 피어링](../articles/virtual-network/virtual-network-peering-overview.md)을 참조하세요.

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Azure는 VNet 간 트래픽에 요금을 청구하나요?

VPN 게이트웨이 연결을 사용하는 경우 동일한 지역 내의 VNet 간 트래픽은 양방향 모두에 대해 무료입니다. 지역 전체 VNet 간 송신 트래픽은 원본 지역을 기반으로 아웃바운드 VNet 간 데이터 전송 요금으로 청구됩니다. 자세한 내용은 [VPN Gateway 가격 책정 페이지](https://azure.microsoft.com/pricing/details/vpn-gateway/)를 참조하세요. VPN Gateway 대신 VNet 피어링을 사용하여 VNet을 연결하는 경우 [Virtual Network 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-network/)를 참조하세요.

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>VNet 간 트래픽은 인터넷을 거쳐서 이동하나요?

번호 VNet 간 트래픽은 인터넷이 아닌 Microsoft Azure 백본을 거쳐서 이동합니다.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-aad-tenants"></a>AAD 테넌트 사이에 VNet 간 연결을 설정할 수 있나요?

예, Azure VPN 게이트웨이를 사용하는 VNet 간 연결은 AAD 테넌트 전체에서 작동합니다.

### <a name="is-vnet-to-vnet-traffic-secure"></a>VNet 간 트래픽은 안전한가요?

예, IPsec/IKE 암호화로 보호됩니다.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>VNet을 함께 연결하려면 VPN 장치가 필요한가요?

번호 크로스-프레미스 연결이 필요한 경우가 아니면 여러 Azure 가상 네트워크를 함께 연결할 때 VPN 장치는 필요하지 않습니다.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>VNet이 동일한 지역에 있어야 하나요?

번호 가상 네트워크는 같은 Azure 지역(위치)에 있을 수도 있고 다른 Azure 지역(위치)에 있을 수도 있습니다.

### <a name="if-the-vnets-are-not-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-ad-tenant"></a>VNet이 동일한 구독에 없으면 구독이 동일한 AD 테넌트와 연결되어야 하나요?

번호

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>별도 가상 네트워크에 있는 Azure 인스턴스를 연결하는 데 VNet 간 연결을 사용할 수 있나요? 

번호 VNet 간 연결은 동일한 Azure 인스턴스 내에서 가상 네트워크 연결을 지원합니다. 예를 들어 공용 Azure와 중국/독일/US Gov Azure 인스턴스 사이에 연결을 만들 수 없습니다. 이러한 시나리오의 경우 사이트 간 VPN 연결을 사용하는 것이 좋습니다.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>VNet간 연결을 멀티 사이트 연결과 함께 사용할 수 있나요?

예. 가상 네트워크 연결을 다중 사이트 VPN과 동시에 사용할 수 있습니다.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>하나의 가상 네트워크에 연결할 수 있는 온-프레미스 사이트 및 가상 네트워크 수는 어떻게 됩니까?

[게이트웨이 요구 사항](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) 테이블을 참조하세요.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>VNet 간 연결을 사용하여 VNet 외부의 VM 또는 클라우드 서비스를 연결할 수 있나요?

번호 VNet 간 연결은 가상 네트워크 연결을 지원합니다. 가상 네트워크에 포함되어 있지 않은 가상 머신 또는 클라우드 서비스 연결은 지원되지 않습니다.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>클라우드 서비스 또는 부하 분산 끝점이 VNet까지 이어지나요?

번호 클라우드 서비스 또는 부하 분산 끝점은 연결되어 있더라도 여러 가상 네트워크에 분산될 수 없습니다.

### <a name="can-i-used-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>VNet 간 또는 멀티 사이트 연결에 PolicyBased VPN 유형을 사용할 수 있나요?

번호 VNet 간 연결 및 멀티 사이트 연결에는 RouteBased(이전 명칭: 동적 라우팅) VPN 유형의 Azure VPN 게이트웨이가 필요합니다.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>PolicyBased VPN 형식을 가진 VNet을 다른 RouteBased VPN 형식을 가진 VNet과 연결할 수 있습니까?

아니요, 두 가상 네트워크는 모두 경로 기반(이전 명칭: 동적 라우팅) VPN을 사용해야 합니다.

### <a name="do-vpn-tunnels-share-bandwidth"></a>VPN 터널은 대역폭을 공유하나요?

예. 가상 네트워크의 모든 VPN 터널은 Azure VPN 게이트웨이의 사용 가능한 대역폭 및 Azure의 동일 VPN 게이트웨이 작동 시간 SLA를 공유합니다.

### <a name="are-redundant-tunnels-supported"></a>중복 터널이 지원되나요?

하나의 가상 네트워크 게이트웨이를 active-active로 구성하면 한 쌍의 가상 네트워크 사이의 중복 터널이 지원됩니다.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>VNet 간 구성에 대해 겹치는 주소 공간을 가질 수 있나요?

번호 겹치는 IP 주소 범위를 가질 수 있습니다.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>연결된 가상 네트워크와 온-프레미스 로컬 사이트 사이에 겹치는 주소 공간이 있을 수 있나요?

번호 겹치는 IP 주소 범위를 가질 수 있습니다.



