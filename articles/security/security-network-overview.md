---
title: Azure의 네트워크 보안 개념 및 요구 사항 | Microsoft Docs
description: 이 문서에서는 Azure에서 이러한 각 영역에서 제공해야 하는 사항에 대한 정보는 물론, 핵심 네트워크 보안 개념 및 요구 사항에 대한 기본적인 설명을 제공합니다.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: fbd589aedb955ee4bd61dc0ec754d8713a98179a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365630"
---
# <a name="azure-network-security-overview"></a>Azure 네트워크 보안 개요
Azure에는 사용자의 응용 프로그램과 서비스 연결 요구 사항을 지원하기 위한 강력한 네트워킹 인프라가 포함되어 있습니다. 네트워크 연결은 Azure에 위치한 리소스 간, 온-프레미스와 Azure 호스팅 리소스 간, 그리고 인터넷과 Azure 간에 가능합니다.

이 문서의 목표는 Azure가 네트워크 보안 영역에서 제공하는 사항을 설명하는 것입니다. 핵심 네트워크 보안 개념 및 요구 사항에 대한 기본적인 설명 외에 다음에 대해서도 알아봅니다.

* Azure 네트워킹
* 네트워크 액세스 제어
* 안전한 원격 액세스 및 크로스-프레미스 연결
* 가용성
* 이름 확인
* 경계 네트워크(DMZ) 아키텍처
* 감사 및 위협 검색

## <a name="azure-networking"></a>Azure 네트워킹
가상 머신은 네트워크 연결이 필요합니다. 이 요구 사항을 지원하려면 가상 머신을 Azure Virtual Network에 연결해야 합니다. Virtual Network는 물리적 Azure 네트워크 패브릭 위에 구축되는 논리적 구조체입니다. 각 논리적 Virtual Network는 다른 모든 Virtual Network와 격리됩니다. 이는 사용자 배포의 네트워크 트래픽이 다른 Azure 고객에게 액세스하지 못하도록 해줍니다.

자세한 정보:

* [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>네트워크 액세스 제어
네트워크 액세스 제어는 Virtual Network 내의 특정 장치 또는 서브넷 간 연결을 제한하는 행위입니다. 네트워크 액세스 제어의 목표는 가상 머신 및 서비스에 대한 액세스를 승인 된 사용자 및 장치로 제한하는 것입니다. 액세스 제어는 가상 머신 또는 서비스 간 연결에 대한 허용 또는 거부 결정에 기반합니다.

Azure는 다음과 같은 다양한 유형의 네트워크 액세스 제어를 지원합니다.

* 네트워크 계층 제어
* 경로 제어 및 터널링 적용
* 가상 네트워크 보안 어플라이언스

### <a name="network-layer-control"></a>네트워크 계층 제어
모든 보안 배포에는 몇 가지 네트워크 액세스 제어가 필요합니다. 네트워크 액세스 제어의 목표는 가상 컴퓨터 통신을 필요한 시스템으로 제한하는 것입니다. 다른 통신 시도는 차단됩니다.

기본적인 네트워크 수준 액세스 제어(IP 주소 및 TCP 또는 UDP 프로토콜 기반)가 필요한 경우 NSG(네트워크 보안 그룹)를 사용할 수 있습니다. NSG는 기본적인 상태 저장 패킷 필터링 방화벽이며, [5-튜플](https://www.techopedia.com/definition/28190/5-tuple)에 기반하여 액세스를 제어할 수 있게 합니다. NSG는 응용 프로그램 계층 검사 또는 인증된 액세스 제어를 제공하지 않습니다.

자세한 정보:

* [네트워크 보안 그룹](../virtual-network/security-overview.md)

### <a name="route-control-and-forced-tunneling"></a>경로 제어 및 터널링 적용
Virtual Network에서 라우팅 동작을 제어하는 기능은 매우 중요합니다. 라우팅이 잘못 구성된 경우 가상 머신에서 호스트하는 응용 프로그램 및 서비스가 잠재적 공격자가 소유 및 작동하는 시스템을 비롯하여 허가되지 않은 장치에 연결될 수 있습니다.

Azure 네트워킹은 Virtual Network의 네트워크 트래픽에 대한 라우팅 동작을 사용자 지정할 수 있는 기능을 지원하므로 사용자는 Virtual Network에서 기본 라우팅 테이블 항목을 변경할 수 있습니다. 라우팅 동작을 제어하면 특정 장치 또는 장치 그룹의 모든 트래픽이 특정 위치를 통해 Virtual Network에 진입하거나 떠납니다.

예를 들어, Virtual Network에 Virtual Network 보안 어플라이언스가 있을 수 있습니다. Virtual Network에 들어 오고 나가는 모든 트래픽이 가상 보안 어플라이언스를 거치게 할 수 있습니다. 이렇게 하려면 Azure에서 [UDR(사용자 정의 경로)](../virtual-network/virtual-networks-udr-overview.md)을 구성하면 됩니다.

[터널링 적용](https://www.petri.com/azure-forced-tunneling)은 사용자의 서비스가 인터넷에서 장치에 대한 연결을 개시하지 못하게 하도록 사용할 수 있는 메커니즘입니다. 수신 연결을 수락한 다음 응답하는 것과는 다릅니다. 프런트 엔드 웹 서버는 인터넷 호스트의 요청에 응답해야 하므로, 인터넷 출처의 트래픽은 이러한 웹 서버에 대한 인바운드가 허용되며 웹 서버는 응답할 수 있습니다.

허용하지 않으려는 것은 프런트 엔드 웹 서버가 아웃바운드 요청을 개시하는 것입니다. 이러한 연결이 맬웨어 다운로드에 사용될 수 있으므로 위와 같은 요청은 보안 위험을 나타낼 수 있습니다. 이러한 프런트 엔드 서버가 인터넷에 아웃바운드 요청을 개시하기를 원하는 경우에도 온-프레미스 웹 프록시를 거치게 할 수 있습니다. 이렇게 하면 URL 필터링 및 로깅 기능을 활용할 수 있습니다.

대신, 이를 방지하기 위해 터널링 적용을 사용할 수 있습니다. 터널링 적용을 사용할 경우 인터넷에 대한 모든 연결은 사용자의 온-프레미스 게이트웨이를 통해 강제됩니다. UDR을 활용하여 터널링 적용을 구성할 수 있습니다.

자세한 정보:

* [사용자 정의된 경로 및 IP 전달이란?](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>가상 네트워크 보안 어플라이언스
NSG, UDR 및 터널링 적용은 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model)의 네트워크 및 전송 계층에서 어느 정도의 네트워크 보안을 제공할 수 있지만 네트워크보다 상위 수준에서 보안을 구현해야 하는 여러 상황이 있을 수도 있습니다.

예를 들어, 다음과 같은 보안 요구 사항이 있을 수 있습니다.

* 응용 프로그램에 대한 액세스를 허용하기 전에 인증 및 권한 부여
* 침입 감지 및 침입 대응
* 높은 수준의 프로토콜에 대한 응용 프로그램 계층 검사
* URL 필터링
* 네트워크 수준 바이러스 백신 및 맬웨어 방지
* 안티봇 보호
* 응용 프로그램 액세스 제어
* 추가적인 DDoS 보호(Azure 패브릭 자체에서 제공하는 DDoS 보호 이외)

Azure 파트너 솔루션을 사용하여 이러한 향상된 네트워크 보안 기능에 액세스할 수 있습니다. [Azure Marketplace](https://azure.microsoft.com/marketplace/)를 방문하여 "보안" 및 "네트워크 보안"을 검색하여 가장 최신의 Azure 파트너 네트워크 보안 솔루션을 찾을 수 있습니다.

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>안전한 원격 액세스 및 크로스-프레미스 연결
Azure 리소스의 설정, 구성 및 관리는 원격으로 수행해야 합니다. 또한, 온-프레미스와 Azure 공용 클라우드에 구성 요소가 있는 [하이브리드 IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) 솔루션을 배포할 수 있습니다. 이러한 시나리오에는 보안 원격 액세스가 필요합니다.

Azure 네트워킹은 다음 보안 원격 액세스 시나리오를 지원합니다.

* Virtual Network에 개별 워크스테이션 연결
* VPN으로 Virtual Network에 온-프레미스 네트워크 연결
* 전용 WAN 링크로 Virtual Network에 온-프레미스 네트워크 연결
* Virtual Network 상호 연결

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Virtual Network에 개별 워크스테이션 연결
개별 개발자 또는 작업 담당자가 Azure에서 가상 머신 및 서비스를 관리하도록 하려는 경우가 있을 수 있습니다. 예를 들어, Virtual Network의 가상 머신에 액세스해야 하지만 보안 정책에서 개별 가상 머신에 대한 RDP 또는 SSH 원격 액세스를 허용하지 않는 경우가 있습니다. 이 경우에는 지점 및 사이트 간 VPN 연결을 사용할 수 있습니다.

지점 및 사이트 간 VPN 연결에서는 [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) 프로토콜을 사용하여 사용자와 Virtual Network 간 비공개 및 보안 연결을 설정할 수 있습니다. VPN 연결이 설정되면 사용자는 VPN 링크를 통해 RDP 또는 SSH를 Virtual Network의 가상 머신에 연결할 수 있습니다(사용자가 인증할 수 있고 권한이 부여되었다고 가정).

자세한 정보:

* [PowerShell을 사용하여 가상 네트워크에 지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>VPN으로 Virtual Network에 온-프레미스 네트워크 연결
회사 네트워크 전체 또는 일부를 Virtual Network에 연결할 수 있습니다. 이는 조직에서 [온-프레미스 데이터 센터를 Azure로 확장](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)하는 하이브리드 IT 시나리오에서 흔히 일어나는 일입니다. 대부분 경우 조직에서는 Azure와 온-프레미스에서 서비스의 일부를 호스팅합니다. 예를 들어, Azure의 프런트-엔드 웹 서버와 온-프레미스의 백-엔드 데이터베이스가 솔루션에 포함된 경우에 그럴 수 있습니다. 이러한 종류의 "크로스-프레미스" 연결은 Azure 위치 리소스를 더 안전하게 리하고 Active Directory 도메인 컨트롤러를 Azure로 확장하는 등의 시나리오를 활성화할 수 있게 줍니다.

이 작업을 수행하는 한 가지 방법은 [사이트 간 VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)을 사용하는 것입니다. 사이트 간 VPN과 지점 및 사이트 간 VPN의 차이점은, 지점 및 사이트 간 VPN은 단일 장치를 Virtual Network에 연결하는 반면, 사이트 간 VPN은 네트워크 전체(온-프레미스 네트워크 등)를 Virtual Network에 연결한다는 점입니다. Virtual Network의 사이트 간 VPN은 매우 안전한 IPsec 터널 모드 VPN 프로토콜을 사용합니다.

자세한 정보:

* [Azure Portal을 사용하여 사이트 간 VPN 연결로 Resource Manager VNet 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VPN 게이트웨이 계획 및 설계](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>전용 WAN 링크로 Virtual Network에 온-프레미스 네트워크 연결
지점 및 사이트 간과 사이트 간 VPN 연결은 크로스-프레미스 연결에 효과적입니다. 그러나 일부 조직에서는 다음과 같은 장애가 있는지 고려합니다.

* VPN 연결은 인터넷을 통해 데이터를 이동합니다. 따라서 이러한 연결은 공용 네트워크를 통한 데이터 이동과 관련된 잠재적인 보안 문제에 노출됩니다. 또한, 인터넷 연결의 안정성 및 가용성을 보장할 수 없습니다.
* Virtual Network에 VPN 연결 시 일부 응용 프로그램 및 용도에 사용할 수 있는 대역폭(최대 약 200Mbps)이 없을 수도 있습니다.

크로스-프레미스 연결에 대해 가장 높은 수준의 보안 및 가용성이 필요한 조직은 일반적으로 원격 사이트에 연결하기 위해 전용 WAN 링크를 사용합니다. Azure에서는 Virtual Network에 온-프레미스 네트워크를 연결하기 위해 사용할 수 있는 전용 WAN 링크를 사용할 수 있습니다. 이 작업은 Azure ExpressRoute를 통해 구현됩니다.

자세한 정보:

* [ExpressRoute 기술 개요](../expressroute/expressroute-introduction.md)

### <a name="connect-virtual-networks-to-each-other"></a>Virtual Network 상호 연결
배포에 많은 Virtual Network를 사용할 수 있습니다. 예를 들어, 관리를 간소화하거나 보안을 강화하는 등 여러 가지 이유로 이렇게 할 수 있습니다. 여러 Virtual Network에 리소스를 두는 동기에 관계없이 각 네트워크의 리소스를 상호 연결하려는 상황이 있을 수 있습니다.

한 가지 옵션은 인터넷을 통해 "루프백"을 실행하여 특정 Virtual Network의 서비스를 다른 Virtual Network의 서비스에 연결하는 것입니다. 연결은 하나의 Virtual Network에서 시작하여 인터넷을 거친 다음 대상 Virtual Network로 돌아옵니다. 이 옵션을 사용하면 인터넷 기반 통신에 내재된 보안 문제에 연결이 노출됩니다.

더 나은 옵션은 두 Virtual Network를 연결하는 사이트 간 VPN을 만드는 것입니다. 이 방법에서는 위에 언급된 크로스-프레미스 사이트 간 VPN 연결과 동일한 [IPsec 터널 모드](https://technet.microsoft.com/library/cc786385.aspx) 프로토콜을 사용합니다.

이 접근 방법의 이점은 VPN 연결이 인터넷을 통해 연결되지 않고 Azure 네트워크 패브릭을 통해 설정된다는 것입니다. 따라서 인터넷을 통해 연결하는 사이트 간 VPN에 비해 추가적인 보안 계층을 제공합니다.

자세한 정보:

* [Azure Resource Manager 및 PowerShell을 사용하여 VNet-VNet 연결 구성](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>가용성
가용성은 보안 프로그램의 핵심 구성 요소입니다. 사용자와 시스템이 네트워크를 통해 액세스하기 위해 필요한 것에 액세스할 수 없는 경우 서비스는 손상된 것으로 간주할 수 있습니다. Azure에는 다음 고가용성 메커니즘을 지원하는 네트워킹 기술이 있습니다.

* HTTP 기반 부하 분산
* 네트워크 수준 부하 분산
* 전역 부하 분산

부하 분산은 여러 장치 사이의 연결을 고르게 분산하도록 설계된 메커니즘입니다. 부하 분산의 목표는 다음과 같습니다.

* 가용성 증가. 여러 장치에서 연결 부하를 분산시키면 하나 이상의 장치가 사용할 수 없게 되더라도 서비스가 영향을 받지 않습니다. 즉, 나머지 온라인 장치에서 실행 중인 서비스가 해당 서비스에서 콘텐츠를 계속 제공할 수 있습니다.
* 성능 향상. 여러 장치에서 연결 부하를 분산시키면 단일 장치에서 모든 처리를 실행하지 않아도 됩니다. 대신, 콘텐츠 제공에 대한 처리 및 메모리 요구는 여러 장치 전반에서 분산됩니다.

### <a name="http-based-load-balancing"></a>HTTP 기반 부하 분산
웹 기반 서비스를 실행하는 조직은 이러한 웹 서비스 앞에 HTTP 기반 부하 분산 장치를 두기를 원하는 경우가 종종 있습니다. 이는 적정한 수준의 성능과 고가용성을 보장하는 데 도움이 됩니다. 기존 네트워크 기반 부하 분산 장치는 네트워크 및 전송 계층 프로토콜을 기반으로 하는 반면, HTTP 기반 부하 분산 장치는 HTTP 프로토콜의 특성을 기반으로 의사 결정을 내립니다.

Azure Application Gateway는 웹 기반 서비스에 대한 HTTP 기반 부하 분산을 제공합니다. Application Gateway는 다음을 지원합니다.

* 쿠키 기반 세션 선호도. 이 기능은 해당 부하 분산 장치 뒤의 서버 중 하나에 설정된 연결이 클라이언트와 서버 사이에서 그대로 유지되도록 해줍니다. 따라서 트랜잭션의 안정성이 보장됩니다.
* SSL 오프로드. 클라이언트가 부하 분산 장치와 연결된 경우 해당 세션은 HTTPS(SSL/) 프로토콜을 사용하여 암호화됩니다. 그러나 성능을 개선하기 위해 HTTP(암호화되지 않은) 프로토콜을 사용하여 부하 분산 장치와 그 뒤의 웹 서버를 연결할 수 있습니다. 부하 분산 장치 뒤의 웹 서버에서 암호화와 관련된 프로세서 오버헤드가 발생하지 않아 요청을 보다 신속하게 제공할 수 있으므로 이를 "SSL 오프로드"라고 합니다.
* URL 기반 콘텐츠 라우팅. 이 기능을 통해 부하 분산 장치가 대상 URL에 기반하여 연결을 전달할 위치를 결정할 수 있습니다. IP 주소에 기반하여 부하 분산 결정을 내리는 솔루션보다 훨씬 더 많은 유연성을 제공합니다.

자세한 정보:

* [Application Gateway 개요](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>네트워크 수준 부하 분산
HTTP 기반 부하 분산과는 달리, 네트워크 수준 부하 분산은 IP 주소 및 포트(TCP 또는 UDP) 번호를 기반으로 의사 결정을 내립니다.
Azure Load Balancer를 사용하여 Azure에서 네트워크 수준 부하 분산의 이점을 얻을 수 있습니다. Load Balancer의 몇 가지 주요 특징은 다음과 같습니다.

* IP 주소와 포트 번호에 기반한 네트워크 수준 부하 분산
* 모든 응용 프로그램 계층 프로토콜에 대한 지원
* Azure 가상 머신 및 클라우드 서비스 역할 인스턴스에 대한 부하 분산
* 인터넷 연결(외부 부하 분산) 및 비-인터넷 연결(내부 부하 분산) 응용 프로그램과 가상 머신 모두에 사용할 수 있음
* 부하 분산 장치 뒤에서 사용할 수 없게 되는 서비스가 있는지 확인하기 위해 사용하는 끝점 모니터링

자세한 정보:

* [여러 가상 머신 또는 서비스 간의 인터넷 연결 부하 분산 장치](../load-balancer/load-balancer-internet-overview.md)
* [내부 부하 분산 장치 개요](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>전역 부하 분산
일부 조직에서는 가능한 가장 높은 수준의 가용성을 원합니다. 이 목표에 도달하는 한 가지 방법은 전 세계적으로 분산된 데이터 센터에서 응용 프로그램을 호스팅하는 것입니다. 전 세계적으로 위치한 데이터 센터에서 응용 프로그램을 호스팅할 경우 지역 전체가 사용할 수 없게 되어도 응용 프로그램은 계속 실행할 수 있습니다.

이 부하 분산 전략을 사용하면 성능 이점도 얻을 수 있습니다. 서비스에 대한 요청을 해당 요청을 하는 장치에 가장 가까운 데이터 센터로 전달할 수 있습니다.

Azure에서는 Azure Traffic Manager를 사용하여 글로벌 부하 분산의 이점을 얻을 수 있습니다.

자세한 정보:

* [Traffic Manager란?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>이름 확인
이름 확인은 Azure에서 호스팅하는 모든 서비스에 대해 중요한 기능입니다. 보안 관점에서 이름 확인 기능이 손상되면 공격자가 사용자의 사이트에서 공격자의 사이트로 요청을 리디렉션할 수 있습니다. 보안 이름 확인은 사용자의 모든 클라우드 호스팅 서비스의 요구 사항입니다.

해결해야 할 두 가지 유형의 이름 확인이 있습니다.

* 내부 이름 확인. Virtual Network, 온-프레미스 네트워크 또는 둘 다에 있는 서비스에서 사용됩니다. 내부 이름 확인에 사용되는 이름은 인터넷을 통해 액세스할 수 없습니다. 최적의 보안을 위해 외부 사용자가 내부 이름 확인 스킴에 액세스하지 않게 하는 것이 중요합니다.
* 외부 이름 확인. 온-프레미스 네트워크와 Virtual Network 외부의 사용자 및 장치에서 사용됩니다. 인터넷에 표시되며 사용자의 클라우드 기반 서비스에 직접 연결하는 데 사용되는 이름입니다.

내부 이름 확인에는 두 가지 옵션이 있습니다.

* Virtual Network DNS 서버. 새 Virtual Network를 만들 경우 사용자에 대한 DNS 서버가 만들어집니다. 이 DNS 서버는 해당 Virtual Network에 있는 컴퓨터의 이름을 확인할 수 있습니다. 이 DNS 서버는 구성할 수 없고 Azure 패브릭 관리자가 관리하므로 이름 확인 솔루션의 보안을 유지하는 데 도움이 될 수 있습니다.
* 자체 DNS 서버 가져오기. Virtual Network에서 직접 선택한 DNS 서버를 가져오는 옵션이 있습니다. 이 DNS 서버는 Active Directory 통합 DNS 서버, 또는 Azure Marketplace에서 확보할 수 있는 Azure 파트너가 제공하는 전용 DNS 서버 솔루션이 될 수 있습니다.

자세한 정보:

* [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)
* [Virtual Network에서 사용하는 DNS 서버 관리](../virtual-network/manage-virtual-network.md#change-dns-servers)

외부 이름 확인에는 두 가지 옵션이 있습니다.

* 자체 외부 DNS 서버 온-프레미스 호스팅
* 서비스 공급자를 통해 자체 외부 DNS 서버 호스팅

많은 대규모 조직에서는 자체 DNS 서버 온-프레미스를 호스팅합니다. 이를 수행할 수 있는 네트워킹 전문성과 글로벌 입지가 있기 때문에 가능합니다.

대부분 경우에 서비스 공급자를 통해 DNS 이름 확인 서비스를 호스팅하는 것이 좋습니다. 이러한 서비스 공급자는 네트워크 전문성과 글로벌 입지를 보유하고 있으므로 사용자의 이름 확인 서비스에 대한 매우 높은 가용성을 보장합니다. 이름 확인 서비스에 장애가 발생할 경우 아무도 사용자의 인터넷 연결 서비스에 연결할 수 없게 되므로, 가용성은 DNS 서비스에 있어 중요합니다.

Azure는 Azure DNS의 형태로 가용성과 성능이 뛰어난 외부 DNS 솔루션을 제공합니다. 이 외부 이름 확인 솔루션은 전 세계 Azure DNS 인프라를 활용합니다. 이를 통해 다른 Azure 서비스와 같은 자격 증명, API, 도구 및 청구를 사용하여 Azure에서 도메인을 호스팅할 수 있습니다. Azure이 일부로, 플랫폼에 구축된 강력한 보안 제어를 상속하기도 합니다.

자세한 정보:

* [Azure DNS 개요](../dns/dns-overview.md)

## <a name="perimeter-network-architecture"></a>경계 네트워크 아키텍처
많은 대규모 조직에서는 경계 네트워크를 사용하여 네트워크를 분할하고 인터넷과 자체 서비스 사이에 완충 지대를 만듭니다. 네트워크의 경계 부분은 낮은 수준의 보안 영역으로 간주되며 이 네트워크 세그먼트에는 고가의 자산이 배치되지 않습니다. 일반적으로, 네트워크 보안 장치는 경계 네트워크 세그먼트에 네트워크 인터페이스가 있습니다. 다른 네트워크 인터페이스는 인터넷에서 인바운드 연결을 수락하는 가상 머신 및 서비스가 있는 네트워크에 연결됩니다.

다양한 방법으로 경계 네트워크를 설계할 수 있습니다. 경계 네트워크 배포 및 사용할 경계 네트워크 유형에 대한 결정은 네트워크 보안 요구 사항에 따라 달라집니다.

자세한 정보:

* [Microsoft Cloud Services 및 네트워크 보안](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>감사 및 위협 검색

Azure에서는 초기 검색, 모니터링, 네트워크 트래픽 수집 및 검토 기능으로 이 주요 영역에서 사용자를 지원합니다.

### <a name="azure-network-watcher"></a>Azure Network Watcher
Azure Network Watcher는 문제 해결을 지원할 수 있으며, 보안 문제 식별에 도움이 되는 완전히 새로운 도구 집합을 제공합니다.

[보안 그룹 보기](../network-watcher/network-watcher-security-group-view-overview.md)는 Virtual Machine의 감사 및 보안 준수를 도와줍니다. 이 기능을 사용하여 조직에서 정의한 기준 정책을 각 VM에 적용되는 규칙과 비교하는 프로그래밍 방식의 감사를 수행할 수 있습니다. 그러면 모든 구성 드리프트를 식별하는 데 도움이 됩니다.

[패킷 캡처](../network-watcher/network-watcher-packet-capture-overview.md)를 사용하면 가상 머신에서 네트워크 트래픽을 캡처할 수 있습니다. 네트워크 통계를 수집하고 응용 프로그램 문제를 해결할 수 있으며, 이는 네트워크 침입에 대한 조사에 매우 유용할 수 있습니다. 또한 이 기능을 Azure Functions와 함께 사용하여 특정 Azure 경고에 대한 응답으로 네트워크 캡처를 시작할 수 있습니다.

Network Watcher 및 사용자 실험실에서 몇 가지 기능 테스트를 시작하는 방법에 대한 자세한 내용은 [Azure Network Watcher 모니터링 개요](../network-watcher/network-watcher-monitoring-overview.md)를 참조하세요.

>[!NOTE]
이 서비스의 가용성 및 상태에 대한 최신 알림을 보려면 [Azure 업데이트 페이지](https://azure.microsoft.com/updates/?product=network-watcher)를 참조하세요.

### <a name="azure-security-center"></a>Azure Security Center
Azure Security Center는 위협을 예방, 감지 및 대응하는 데 도움이 되며 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 제공합니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 다양한 보안 솔루션 집합에서 작동합니다.

Security Center는 다음과 같은 방법을 통해 네트워크 보안을 최적화하고 모니터링하는 데 도움을 줍니다.

* 네트워크 보안 권장 사항 제공
* 네트워크 보안 구성의 상태 모니터링
* 끝점 및 네트워크 수준에서 네트워크 기반 위협에 대해 경고

자세한 정보:

* [Azure Security Center 소개](../security-center/security-center-intro.md)


### <a name="logging"></a>로깅
네트워크 수준에서 로깅은 네트워크 보안 시나리오의 핵심 기능입니다. Azure에서는 NSG에 대해 획득한 정보를 기록하여 네트워크 수준 로깅 정보를 얻을 수 있습니다. NSG 로깅을 사용하여 다음에서 정보를 얻습니다.

* [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). 이러한 로그를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다. 이러한 로그는 기본적으로 사용하도록 설정되며 Azure Portal 내에서 사용할 수 있습니다. 이전에는 이러한 로그를 감사 로그 또는 작업 로그라고 했습니다.
* 이벤트 로그. 이러한 로그는 적용된 NSG 규칙에 대한 정보를 제공합니다.
* 카운터 로그. 이러한 로그는 각 NSG 규칙이 트래픽을 허용하거나 거부하는 데 적용된 횟수를 알려 줍니다.

또한 강력한 시각화 도구인 [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)를 사용하여 이러한 로그를 보고 분석할 수도 있습니다.

자세한 정보:

* [NSG(네트워크 보안 그룹)에 대한 Log Analytics](../virtual-network/virtual-network-nsg-manage-log.md)
