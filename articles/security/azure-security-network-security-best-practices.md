---
title: Azure 네트워크 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 기본 제공 Azure 기능을 사용한 네트워크 보안 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: TomShinder
manager: mbaldwin
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 5ebeadd9c0805ac5f6ac543a49cb9ff63d8ded3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="azure-network-security-best-practices"></a>Azure 네트워크 보안 모범 사례
Microsoft Azure는 가상 머신 및 어플라이언스를 Azure Virtual Network에 배치하여 네트워크에 연결된 기타 장치에 연결할 수 있습니다. Azure Virtual Network는 가상 네트워크 인터페이스 카드를 가상 네트워크에 연결하여 네트워크 지원 장치 간에 TCP/IP 통신을 허용하는 구조입니다. Azure Virtual Network에 연결된 Azure Virtual Machines는 동일한 Azure Virtual Network, 다른 Azure Virtual Network, 인터넷 또는 고객의 자체 온-프레미스 네트워크에 있는 장치에 연결할 수 있습니다.

이 문서에서는 Azure 네트워크 보안 모범 사례 컬렉션에 대해 설명합니다. 이러한 모범 사례는 Azure 네트워킹에 대한 Microsoft의 경험 그리고 여러분 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 문서에 설명된 사항:

* 각 모범 사례
* 해당 모범 사례를 사용해야 하는 이유
* 해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
* 해당 모범 사례를 대체할 수 있는 대안
* 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure 네트워크 보안 모범 사례 문서는 이 문서가 작성될 당시의 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 다루는 Azure 네트워크 보안 모범 사례에는 다음이 포함됩니다.

* 서브넷을 논리적으로 분할
* 라우팅 동작 제어
* 강제 터널링 적용
* 가상 네트워크 어플라이언스 사용
* DMZ를 배포하여 보안 영역 지정
* 인터넷에 노출되지 않도록 전용 WAN 링크 사용
* 가동 시간 및 성능 최적화
* 전역 부하 분산 사용
* Azure Virtual Machines에 대한 RDP 액세스 비활성화
* Azure Security Center 활성화
* 데이터 센터를 Azure로 확장

## <a name="logically-segment-subnets"></a>서브넷을 논리적으로 분할
[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/)는 온-프레미스 네트워크의 LAN과 비슷합니다. 단일 개인 IP 주소 공간 기반 네트워크를 만들어서 모든 [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)를 배치하는 것이 Azure Virtual Network의 기본 개념입니다. 사용 가능한 개인 IP 주소 공간은 클래스 A(10.0.0.0/8), 클래스 B(172.16.0.0/12) 및 클래스 C(192.168.0.0/16) 범위에 있습니다.

온-프레미스에서 하던 것과 마찬가지로, 큰 주소 공간을 여러 서브넷으로 분할하는 것이 좋습니다. [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 기반 서브넷 지정 원칙에 따라 서브넷을 만들 수 있습니다.

서브넷 간의 라우팅은 자동으로 발생하므로 라우팅 테이블을 수동으로 구성할 필요가 없습니다. 그러나 Azure Virtual Network에 만드는 서브넷 간에 네트워크 액세스 제어가 없는 것이 기본 설정입니다. 서브넷 간에 네트워크 액세스 제어를 만들려면 서브넷 사이에 무언가를 배치해야 합니다.

이 작업을 수행하는 데 사용할 수는 것 중 하나는 [NSG(네트워크 보안 그룹)](../virtual-network/security-overview.md)입니다. NSG는 간단한 상태 저장 패킷 검사 장치로 5개 튜플(원본 IP, 원본 포트, 대상 IP, 대상 포트 및 계층 4 프로토콜) 접근 방식을 사용하여 네트워크 트래픽에 대한 허용/거부 규칙을 만듭니다. 단일 IP 주소의 트래픽, 여러 IP 주소의 트래픽 또는 전체 서브넷의 트래픽을 허용하거나 거부할 수 있습니다.

서브넷 간의 네트워크 액세스 제어에 NSG를 사용하면 고유한 서브넷의 동일한 보안 영역 또는 역할에 속한 리소스를 배치할 수 있습니다. 웹 계층, 응용 프로그램 논리 계층, 데이터베이스 계층으로 구성된 간단한 3계층 응용 프로그램을 예로 들 수 있습니다. 각 계층에 속한 가상 머신을 각각의 고유한 서브넷에 배치합니다. 그런 다음 NSG를 사용하여 서브넷 간의 트래픽을 제어합니다.

* 웹 계층 가상 머신은 응용 프로그램 논리 컴퓨터에 대한 연결만 시작할 수 있으며 인터넷에서 오는 연결만 수용할 수 있습니다.
* 응용 프로그램 논리 가상 머신은 데이터베이스 계층에 대한 연결만 시작할 수 있으며 웹 계층에서 오는 연결만 수용할 수 있습니다.
* 데이터베이스 계층 가상 머신은 자체 서브넷 외부의 무엇과도 연결을 시작할 수 없으며 응용 프로그램 논리 계층에서 오는 연결만 수용할 수 있습니다.

네트워크 보안 그룹 및 네트워크 보안 그룹을 사용하여 Azure Virtual Network를 논리적으로 분할하는 방법에 대한 자세한 내용은 [NSG(네트워크 보안 그룹)란?](../virtual-network/security-overview.md) 문서를 참조하세요.

## <a name="control-routing-behavior"></a>라우팅 동작 제어
Azure Virtual Network에 가상 머신을 배치하면 다른 가상 머신이 다른 서브넷에 있더라도 해당 가상 머신이 동일한 Azure Virtual Network에 있는 다른 모든 가상 머신에 연결할 수 있다는 사실을 알 수 있습니다. 이러한 통신 유형을 허용하며 기본적으로 활성화되는 시스템 경로 컬렉션이 있기 때문에 가능합니다. 이러한 기본 경로는 동일한 Azure Virtual Network에 있는 가상 머신이 서로 그리고 인터넷(인터넷으로 나가는 아웃바운드 통신만)과의 연결을 시작하도록 허용합니다.

기본 시스템 경로는 여러 배포 시나리오에 유용하지만 배포의 라우팅 구성을 사용자 지정하려는 경우가 있습니다. 이러한 사용자 지정을 사용하면 특정 대상에 도달하도록 다음 홉 주소를 구성할 수 있습니다.

가상 네트워크 보안 어플라이언스를 배포할 때에는 사용자 정의 경로를 구성하는 것이 좋습니다. 이에 대해서는 뒷부분에 나오는 모범 사례에 설명되어 있습니다.

> [!NOTE]
> 대부분의 경우 사용자 정의 경로가 필요 없고 기본 시스템 경로로 충분합니다.
>
>

사용자 정의 경로 및 구성 방법에 대한 자세한 내용은 [사용자 정의된 경로 및 IP 전달이란?](../virtual-network/virtual-networks-udr-overview.md) 문서를 참조하세요.

## <a name="enable-forced-tunneling"></a>강제 터널링 적용
강제 터널링을 정확하게 이해하려면 "분할 터널링"이 무엇인지 이해해야 합니다.
분할 터널링의 가장 일반적인 예는 VPN 연결에서 볼 수 있습니다. 호텔 객실에서 회사 네트워크로 VPN 연결을 설정한다고 가정하겠습니다. 이 연결을 통해 회사 리소스에 액세스할 수 있으며 회사 네트워크에 대한 모든 통신이 VPN 터널을 통과합니다.

인터넷에 있는 리소스에 연결하면 어떻게 될까요? 분할 터널링을 활성화하면 이러한 연결이 VPN 터널을 거치지 않고 인터넷으로 직접 이동합니다. 일부 보안 전문가는 이것이 잠재적인 위험이 될 수 있으므로 분할 터널링을 사용하지 않고 모든 연결을 권장합니다. 인터넷을 대상으로 하는 연결과 회사 리소스를 대상으로 하는 연결은 VPN 터널을 통과해야 합니다. 이렇게 하면 인터넷 연결이 강제로 회사 네트워크 보안 장치를 거치게 되며, VPN 클라이언트가 VPN 터널 외부에서 인터넷에 연결되는 경우에는 그렇지 않습니다.

이제 Azure Virtual Network의 가상 머신으로 돌아가겠습니다. Azure Virtual Network의 기본 경로는 가상 머신이 인터넷에 대한 트래픽을 시작하도록 허용합니다. 마찬가지로 이로 인해 보안 위험이 발생할 수 있습니다. 이러한 아웃바운드 연결 때문에 가상 머신의 공격 노출 영역이 늘어나서 공격자에게 활용될 수 있습니다.
이러한 이유로 Azure Virtual Network와 온-프레미스 네트워크 간에 크로스-프레미스 연결이 있는 경우 가상 머신에서 강제 터널링을 활성화하는 것이 좋습니다. 프레미스 간 연결은 Azure 네트워킹 모범 사례 문서의 뒷부분에 설명되어 있습니다.

크로스 프레미스 연결이 없으면 앞에서 설명한 네트워크 보안 그룹 또는 다음에 설명할 Azure 가상 네트워크 보안 어플라이언스를 활용하여 Azure Virtual Machines에서 인터넷으로의 아웃바운드 연결을 막아야 합니다.

강제 터널링에 대한 자세한 내용 및 활성화 방법은 [PowerShell 및 Azure Resource Manager를 사용하여 강제 터널링 구성](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)을 참조하세요.

## <a name="use-virtual-network-appliances"></a>가상 네트워크 어플라이언스 사용
네트워크 보안 그룹 및 사용자 정의 라우팅이 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model)의 네트워크 및 전송 계층에서 어느 정도의 네트워크 보안을 제공할 수 있지만 상위 스택에서 보안을 강화해야 하는 여러 상황이 발생할 것입니다. 이러한 상황에서는 Azure 파트너가 제공하는 가상 네트워크 보안 어플라이언스를 배포하는 것이 좋습니다.

Azure 네트워크 보안 어플라이언스는 네트워크 수준 제어에서 제공하는 보안 수준을 강화할 수 있습니다. 다음은 가상 네트워크 보안 어플라이언스에서 제공하는 네트워크 보안 기능 중 일부입니다.

* 방화벽
* 침입 감지/침입 방지
* 취약점 관리
* 응용 프로그램 제어
* 네트워크 기반 이상 감지
* 웹 필터링
* 바이러스 백신
* 봇 넷 보호

네트워크 수준 액세스 제어를 통해 얻을 수 있는 것보다 높은 수준의 네트워크 보안이 필요한 경우 Azure 가상 네트워크 보안 어플라이언스를 살펴보고 배포할 것을 권장합니다.

제공되는 Azure 가상 네트워크 보안 어플라이언스 및 해당 기능에 대한 자세한 내용을 알아보려면 [Azure Marketplace](https://azure.microsoft.com/marketplace/)를 방문하여 "보안" 및 "네트워크 보안"을 검색하세요.

## <a name="deploy-dmzs-for-security-zoning"></a>DMZ를 배포하여 보안 영역 지정
DMZ 또는 "경계 네트워크"는 자산과 인터넷 사이에 추가 보안 계층을 제공하도록 설계된 물리적 또는 논리적 네트워크 세그먼트입니다. DMZ의 목적은 원하는 트래픽만 네트워크 보안 장치를 통과하여 Azure Virtual Network로 들어올 수 있도록 DMZ 네트워크의 가장자리에 특수 네트워크 액세스 제어 장치를 배치하는 것입니다.

DMZ를 사용하면 Azure Virtual Network 가장 자리에 있는 장치의 네트워크 액세스 제어 관리, 모니터링, 로깅 및 장치 보고에 집중할 수 있으므로 아주 유용합니다. 여기서 일반적으로 DDoS 방지, 침입 감지/침입 방지 시스템(IDS/IPS), 방화벽 규칙 및 정책, 웹 필터링, 네트워크 맬웨어 방지 프로그램 등을 활성화합니다. 네트워크 보안 장치는 인터넷과 Azure Virtual Network 사이에 위치하며 두 네트워크에 인터페이스가 있습니다.

이것은 DMZ의 기본 설계이며 백투백, 트라이홈, 멀티홈 등 다양한 DMZ 설계가 있습니다.

모든 고급 보안 배포 시에는 DMZ를 배포하여 Azure 리소스의 네트워크 보안 수준을 높이는 것이 좋습니다.

DMZ 및 Azure에서 DMZ를 배포하는 방법에 대한 자세한 내용은 [Microsoft Cloud Services 및 네트워크 보안](../best-practices-network-security.md)을 참조하세요.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>인터넷에 노출되지 않도록 전용 WAN 링크 사용
여러 조직에서 하이브리드 IT 경로를 선택했습니다. 하이브리드 IT에서는 회사의 정보 자산 중 일부는 Azure에, 나머지는 온-프레미스에 있습니다. 서비스의 구성 요소 중 일부는 Azure에서 실행되고 나머지 구성 요소는 온-프레미스에서 실행되는 경우가 많습니다.

하이브리드 IT 시나리오에서는 크로스-프레미스 연결이 일반적입니다. 이 크로스-프레미스 연결은 회사가 온-프레미스 네트워크를 Azure Virtual Network에 연결할 수 있습니다. 다음과 같은 두 가지 크로스-프레미스 연결 솔루션이 제공됩니다.

* 사이트 간 VPN
* ExpressRoute

[사이트 간 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)은 온-프레미스 네트워크와 Azure Virtual Network 간의 가상 사설 연결을 나타냅니다. 이 연결은 인터넷을 통해 발생하며 네트워크와 Azure 사이의 암호화된 링크 내부에서 정보를 "터널링"할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포해 온 안전하고 완성도 높은 기술입니다. 터널 암호화는 [IPsec 터널 모드](https://technet.microsoft.com/library/cc786385.aspx)를 사용하여 수행됩니다.

사이트 간 VPN이 신뢰할 수 있고 안정적이며 인정받은 기술이지만 터널 내부 트래픽은 인터넷을 트래버스합니다. 뿐만 아니라 대역폭이 약 200Mbps로 제한됩니다.

크로스-프레미스 연결에 강력한 보안 또는 성능 수준이 필요한 경우 크로스-프레미스 연결에 Azure ExpressRoute를 사용하는 것이 좋습니다. ExpressRoute는 온-프레미스 위치 또는 Exchange 호스팅 공급자 사이의 WAN 링크입니다. 이 연결은 telco 연결이기 때문에 데이터가 인터넷을 통해 전달되지 않고, 따라서 인터넷 통신의 본질적인 잠재적 위험에 노출되지 않습니다.

Azure ExpressRoute의 작동 원리 및 배포 방법에 대한 자세한 내용은 [ExpressRoute 기술 개요](../expressroute/expressroute-introduction.md)를 참조하세요.

## <a name="optimize-uptime-and-performance"></a>가동 시간 및 성능 최적화
CIA(기밀성, 무결성 및 가용성)는 오늘날 가장 영향력이 큰 보안 모델을 구성하는 3요소입니다. 기밀성은 암호화 및 개인 정보 보호와 관련이 있고, 무결성은 권한이 없는 사람이 데이터를 변경하지 못하게 하는 것과 관련이 있고, 가용성은 권한이 있는 사람이 액세스가 허용된 정보에 액세스할 할 수 있도록 하는 것과 관련이 있습니다. 이러한 영역 중 하나에서 오류가 발생하면 잠재적 보안 위반이 있다는 뜻입니다.

가용성은 가동 시간 및 성능에 대한 것으로 생각할 수 있습니다. 서비스가 중지되면 정보에 액세스할 수 없습니다. 성능이 매우 좋지 않아서 데이터를 사용할 수 없으면 해당 데이터에 액세스할 수 없는 것으로 간주할 수 있습니다. 따라서 보안의 관점에서 서비스의 가동 시간 및 성능을 최적화하기 위해 할 수 있는 모든 조치를 취해야 합니다.
가용성 및 성능을 향상하기 위해 널리 사용되는 효과적인 방법은 부하 분산입니다. 부하 분산은 서비스에 포함된 서버 간에 네트워크 트래픽을 분산하는 방법입니다. 예를 들어 프런트 엔드 웹 서버가 서비스에 포함되어 있으면 부하 분산을 사용하여 여러 프런트 엔드 웹 서버 간에 트래픽을 분산할 수 있습니다.

이렇게 트래픽을 분산하면 가용성이 향상됩니다. 웹 서버 중 하나를 사용할 수 없게 되면 부하 분산 장치가 해당 서버로 더 이상 트래픽을 보내지 않고 아직 온라인 상태인 서버로 리디렉션하기 때문입니다. 부하 분산은 성능 향상에도 도움이 됩니다. 요청을 처리하기 위한 프로세서, 네트워크 및 메모리 오버헤드가 모든 부하 분산 서버로 분산되기 때문입니다.

서비스에 적합하다면 되도록이면 부하 분산을 사용하는 것이 좋습니다. 적합성은 다음 섹션에서 살펴볼 예정입니다. Azure는 Azure Virtual Network 수준에서 다음과 같은 세 가지 기본 부하 분산 옵션을 제공합니다.

* HTTP 기반 부하 분산
* 외부 부하 분산
* 내부 부하 분산

## <a name="http-based-load-balancing"></a>HTTP 기반 부하 분산
HTTP 기반 부하 분산은 HTTP 프로토콜의 특징을 사용하여 어떤 서버에 연결을 보낼 것인지 결정합니다. Azure에는 Application Gateway라는 HTTP 부하 분산 장치가 있습니다.

다음과 같은 경우에는 Azure Application Gateway를 사용하는 것이 좋습니다.

* 동일한 사용자/클라이언트 세션의 요청이 동일한 백 엔드 가상 머신에 도달해야 하는 응용 프로그램. 이 작업의 예는 쇼핑 카트 앱 및 웹 메일 서버가 될 것입니다.
* Application Gateway의 [SSL 오프로드](https://f5.com/glossary/ssl-offloading) 기능을 활용하여 웹 서버 팜을 SSL 종료 오버헤드로부터 벗어나게 하려는 응용 프로그램.
* Content Delivery Network와 같은 응용 프로그램은 다른 서버로 라우팅 또는 부하 분산을 위해 여러 HTTP 요청을 동일한 장기 실행 TCP 연결에서 요구합니다.

Azure Application Gateway의 작동 원리 및 배포에 사용하는 방법에 대한 자세한 내용은 [Application Gateway 개요](../application-gateway/application-gateway-introduction.md)를 참조하세요.

## <a name="external-load-balancing"></a>외부 부하 분산
외부 부하 분산은 인터넷에서 들어오는 연결이 Azure Virtual Network에 있는 여러 서버 간에 분산될 때 발생합니다. Azure 외부 부하 분산 장치가 이 기능을 제공할 수 있으며 고정 세션 또는 SSL 오프로드가 필요 없는 경우에는 외부 부하 분산을 사용하는 것이 좋습니다.

HTTP 기반 부하 분산과는 반대로, 외부 부하 분산 장치는 OSI 네트워킹 모델의 네트워크 및 전송 계층에 있는 정보를 사용하여 어떤 서버에 연결 부하를 분산할 것인지 결정합니다.

인터넷에서 들어오는 요청을 수락하는 [상태 비저장 응용 프로그램](http://whatis.techtarget.com/definition/stateless-app)이 있는 경우에는 외부 부하 분산을 사용하는 것이 좋습니다.

Azure 외부 부하 분산 장치의 작동 원리 및 배포 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Resource Manager에서 인터넷 연결 부하 분산 장치 만들기 시작](../load-balancer/load-balancer-get-started-internet-arm-ps.md)을 참조하세요.

## <a name="internal-load-balancing"></a>내부 부하 분산
내부 부하 분산은 외부 부하 분산과 비슷하며 동일한 메커니즘을 사용하여 내부에 있는 서버에 연결 부하를 분산합니다. 유일한 차이점은 부하 분산 장치가 인터넷에 없는 가상 머신에서 들어오는 연결을 수락한다는 점입니다. 대부분의 경우 부하 분산이 허용되는 연결은 Azure Virtual Network의 장치에 의해 시작됩니다.

SQL 서버 또는 내부 웹 서버로 연결 부하를 분산해야 하는 경우처럼 이 기능이 도움이 되는 시나리오에는 내부 부하 분산을 사용하는 것이 좋습니다.

Azure 내부 부하 분산의 작동 원리 및 배포 방법에 대한 자세한 내용은 [PowerShell을 사용하여 내부 부하 분산 장치 만들기 시작](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)을 참조하세요.

## <a name="use-global-load-balancing"></a>전역 부하 분산 사용
공용 클라우드 컴퓨팅을 사용하면 구성 요소가 전 세계의 데이터 센터에 흩어져 있는 전역 분산 응용 프로그램을 배포할 수 있습니다. 이것이 가능한 것은 Microsoft Azure의 글로벌 데이터 센터가 있기 때문입니다. 앞에서 언급한 부하 분산 기술과는 반대로, 전역 부하 분산은 전체 데이터 센터가 중지되더라도 서비스를 제공할 수 있습니다.

[Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)를 활용하여 Azure에서 이 유형의 전역 부하 분산을 사용할 수 있습니다. Traffic Manager는 사용자의 위치에 따라 서비스에 대한 부하를 분산할 수 있습니다.

예를 들어 사용자가 EU에서 서비스 요청을 만드는 경우 연결은 EU 데이터 센터에 있는 서비스로 전송됩니다. Traffic Manager 전역 부하 분산의 이러한 특징은 성능 개선에 도움이 됩니다. 가장 가까운 데이터 센터에 연결하는 것이 멀리 떨어진 데이터 센터에 연결하는 것보다 빠르기 때문입니다.

가용성 측면에서 전역 부하 분산을 사용하면 데이터 센터 전체가 중지되더라도 서비스를 제공할 수 있습니다.

예를 들어 환경적 이유 또는 정전(예: 지역 네트워크 장애)으로 인해 Azure 데이터 센터가 중지되면 서비스에 대한 연결이 가장 가까운 온라인 데이터 센터로 다시 라우팅됩니다. 이 전역 부하 분산은 Traffic Manager에서 만들 수 있는 DNS 정책을 활용하여 수행됩니다.

여러 지역에 걸쳐 광범위하게 분산되고 가장 높은 수준의 가동 시간이 필요한 클라우드 솔루션을 개발하는 경우에는 Traffic Manager를 사용하는 것이 좋습니다.

Azure Traffic Manager 및 배포 방법에 대한 자세한 내용은 [Traffic Manager란](../traffic-manager/traffic-manager-overview.md)을 참조하세요.

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Azure Virtual Machines에 대한 RDP/SSH 액세스 비활성화
[RDP(원격 데스크톱 프로토콜)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) 및 [SSH(Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) 프로토콜을 사용하여 Azure Virtual Machines에 연결할 수 있습니다. 이러한 프로토콜은 원격 위치에서 가상 머신을 관리할 수 있으며 데이터 센터 컴퓨팅의 표준입니다.

인터넷을 통해 이러한 프로토콜을 사용할 때 발생할 수 있는 보안 문제로, 공격자가 다양한 [무차별 암호 대입](https://en.wikipedia.org/wiki/Brute-force_attack) 기술을 사용하여 Azure Virtual Machines에 대한 액세스 권한을 얻을 수 있습니다. 공격자가 액세스 권한을 얻으면 사용자의 가상 머신을 시작 지점으로 사용하여 Azure Virtual Network의 다른 컴퓨터를 손상시키거나 심지어 Azure 외부의 네트워크 장치를 공격할 수 있습니다.

이러한 이유로 인터넷에서 Azure Virtual Machines로의 RDP 및 SSH 직접 액세스를 비활성화하는 것이 좋습니다. 인터넷으로부터의 RDP 및 SSH 직접 액세스를 비활성화하면 가상 머신에 액세스하여 원격으로 관리할 수 있는 다른 옵션이 제공됩니다.

* 지점 및 사이트 간 VPN
* 사이트 간 VPN
* ExpressRoute

[지점 및 사이트 간 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)은 원격 액세스 VPN 클라이언트/서버 연결의 또 다른 용어입니다. 지점 및 사이트 간 VPN을 사용하면 단일 사용자가 인터넷을 통해 Azure Virtual Network에 연결할 수 있습니다. 지점 및 사이트 간 연결이 설정되면 사용자는 RDP 또는 SSH를 사용하여 지점 및 사이트 간 VPN을 통해 연결한 Azure Virtual Network의 모든 가상 머신에 연결할 수 있습니다. 이는 사용자가 해당 가상 머신에 연결할 권한이 있다는 전제 하에 성립합니다.

지점 및 사이트 간 VPN은 사용자가 가상 머신에 연결하려면 두 번 인증해야 하므로 직접 RDP 또는 SSH 연결보다 안전합니다. 먼저, 사용자가 인증(및 권한 획득)하여 지점 및 사이트 간 VPN 연결을 설정해야 합니다. 그 다음에는 사용자가 인증(및 권한 획득)하여 RDP 또는 SSH 세션을 설정해야 합니다.

[사이트 간 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)은 인터넷을 통해 전체 네트워크를 또 다른 네트워크에 연결합니다. 사이트 간 VPN을 사용하여 온-프레미스 네트워크를 Azure Virtual Network에 연결할 수 있습니다. 사이트 간 VPN을 배포하는 경우 온-프레미스 네트워크의 사용자가 RDP 또는 SSH 프로토콜을 사용하여 사이트 간 VPN 연결을 통해 Azure Virtual Network의 가상 머신에 연결할 수 있으므로 인터넷을 통해 RDP 또는 SSH 직접 액세스를 허용할 필요가 없습니다.

또한 전용 WAN 링크를 사용하여 사이트 간 VPN과 비슷한 기능을 제공할 수 있습니다. 주요 차이점은 다음과 같습니다. 1. 전용 WAN 링크가 인터넷을 트래버스하지 않습니다. 2. 전용 WAN 링크가 일반적으로 더 안정적이고 성능이 뛰어납니다. Azure는 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/)의 형태로 전용 WAN 링크 솔루션을 제공합니다.

## <a name="enable-azure-security-center"></a>Azure Security Center 활성화
Azure Security Center는 위협을 예방, 감지 및 대응하는 데 도움이 되며 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 제공합니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

Azure Security Center는 다음과 같은 방법을 통해 네트워크 보안을 최적화하고 모니터링하는 데 도움을 줍니다.

* 네트워크 보안 권장 사항 제공
* 네트워크 보안 구성의 상태 모니터링
* 끝점 및 네트워크 수준에서 네트워크 기반 위협에 대해 경고

모든 Azure 배포에서 Azure Security Center를 활성화하는 것이 좋습니다.

Azure Security Center에 대한 자세한 내용 및 배포에 사용하는 방법은 [Azure Security Center 소개](../security-center/security-center-intro.md)를 참조하세요.

## <a name="securely-extend-your-datacenter-into-azure"></a>데이터 센터를 Azure로 안전하게 확장
많은 엔터프라이즈 IT 조직이 온-프레미스 데이터 센터를 확장하는 대신 클라우드로 확장하려 합니다. 이 확장은 기존 IT 인프라를 공용 클라우드로 확장한다는 의미입니다. 프레미스 간 연결 옵션을 활용하면 Azure Virtual Network를 온-프레미스 네트워크 인프라의 다른 서브넷처럼 다룰 수 있습니다.

그러나 먼저 해결해야 할 계획 및 디자인 문제가 있습니다. 이는 네트워크 보안 영역에서 특히 중요합니다. 이러한 디자인 접근 방법을 이해하는 가장 좋은 방법 중 하나는 예제를 살펴보는 것입니다.

Microsoft에서는 데이터 센터 확장이 어떤 형태로 이루어지는지 이해하는 데 도움을 주기 위해 [데이터 센터 확장 참조 아키텍처 다이어그램](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content)과 지원 자료를 만들었습니다. 이 자료에는 클라우드로의 안전한 엔터프라이즈 데이터 센터 확장을 계획 및 설계하는 데 사용할 수 있는 예제 참조 구현이 포함되어 있습니다. 이 문서를 살펴보고 안전한 솔루션의 주요 구성 요소에 대해 알아두는 것이 좋습니다.

데이터 센터를 Azure로 안전하게 확장하는 방법에 대한 자세한 내용은 [데이터 센터를 Microsoft Azure로 확장](https://www.youtube.com/watch?v=Th1oQQCb2KA) 비디오를 참조하세요.
