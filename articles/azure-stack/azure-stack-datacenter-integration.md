---
title: Azure 스택 통합 고려 사항 일반 데이터 센터에 시스템 통합 | Microsoft Docs
description: 이제 계획 하 고 다중 노드 Azure 스택와 데이터 센터의 통합에 대 한 준비를 수행할 수 있는 작업에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 55243ead4f088f7a2b3d54c0581c604f0dc63d07
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Azure 스택 통합 시스템에 대 한 데이터 센터 통합 고려 사항
Azure 스택 통합 시스템에 관심이 있다면 배포 및 시스템 데이터 센터에 얼마나 적합 한지 주요 계획 고려 사항 중 일부를 이해 해야 합니다. 이 문서에서는 Azure 스택 multi-node 시스템에 대 한 중요 한 인프라 결정을 내릴 수 있도록 이러한 고려 사항에 대 한 고급 개요를 제공 합니다. 이러한 고려 사항에 대 한 이해가는 Azure 스택 데이터 센터에 배포할 때 OEM 하드웨어 공급 업체를 작업할 때 도움이 됩니다.  

> [!NOTE]
> Azure 스택 multi-node 시스템 인증된 한 하드웨어 공급 업체에서 구입할 수만 있습니다. 

Azure 스택을 배포 하려면 배포를 신속 하 고 원활 하 게 이동 하는 프로세스를 시작 하기 전에 솔루션 공급자에 계획 정보를 제공 해야 합니다. 정보는 네트워킹, 보안 및 기술에서 많은 다양 한 영역과 의사 결정권자 필요할 수 있는 많은 중요 한 결정으로 id 정보에서 범위를 필요 합니다. 따라서 배포를 시작 하기 전에 필요한 모든 정보를 준비를 갖출 있도록 조직에 여러 팀에서 사용자 가져오기 해야 합니다. 관련 결정 사항을 제공 하기 위해 유용한 권장 사항을 포함 될 수 하는 대로이 정보를 수집 하는 동안 하드웨어 공급 업체에 게 문의 하는 것이 유용 합니다.

연구 하 고 필요한 정보를 수집 하는 동안 네트워크 환경에 배포 전 구성을 변경 해야 합니다. 여기에 라우터, 스위치 및 새 Azure 스택 솔루션 스위치에 대 한 연결을 준비 하는 방화벽을 구성 하는 Azure 스택 솔루션에 대 한 IP 주소 공간을 예약이 포함 될 수 있습니다. 계획 된 최대 인라인 주제 영역 전문가가 있는지 확인 합니다.

## <a name="capacity-planning-considerations"></a>용량 계획 고려 사항
인수에 대 한 Azure 스택 솔루션을 평가할 때 Azure 스택 솔루션의 전체 용량에 직접적인 영향을 포함 하 하드웨어 구성 항목 설정 해야 합니다. 여기에 CPU, 메모리 밀도, 저장소 구성 및 소수 자릿수의 전체 솔루션 (예: 서버의 수)의 클래식 선택 됩니다. 기존의 가상화 솔루션을 달리 단순 산술 사용 가능한 용량을 결정할 때 이러한 구성 요소는 적용 되지 않습니다. 첫 번째 이유는 Azure 스택은 자체 솔루션 내에서 인프라 또는 관리 구성 요소를 호스트에 맞게 구축 됐습니다. 두 번째 이유는 솔루션의 용량 중 일부는 복원 력; 지원 하기 위해 예약 되어 테 넌 트 작업 부하의 장애를 최소화 하는 방법에는 솔루션의 소프트웨어 업데이트 합니다. 

[Azure 스택 capacity planner 워크시트](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 쉽게 만들어야 정확한 정보를 다음 두 가지 방법에 대 한 용량 계획에 대해 결정할: 중 하나는 하드웨어 제공을 선택 하 고 넣으려고 또는 리소스의 조합을 정의 하 여 여 Azure 스택 하는 작업은 사용 가능한 하드웨어에서 지 원하는 Sku를 보려면를 실행 하는 데 사용 됩니다. 마지막으로, Azure 스택 계획 및 구성에 관련 된 결정을 내리는 데 도움이 되도록 안내 하는 대로 스프레드시트는 것입니다. 

대신 사용자 고유의 조사와 분석에 대 한 역할을 하는 스프레드시트를 사용 하는 것이 없습니다.  Microsoft는 진술이 나 보증도, 명시적 또는 묵시적으로 스프레드시트에 제공 된 정보는.



## <a name="management-considerations"></a>관리 고려 사항
여기서는 인프라는 잠겨 있으며는 사용 권한에서 둘 다 azure 스택은 봉인 된 시스템 및 네트워크 관점입니다. 네트워크 액세스 제어 목록 (Acl) 허가 되지 않은 들어오는 모든 트래픽 및 인프라 구성 요소 간의 불필요 한 모든 통신을 차단에 적용 됩니다. 이렇게 하면 시스템에 액세스할 수 있는 권한이 없는 사용자가 어려워집니다.

일별 관리 및 작업에 대 한 인프라에 제한 되지 않은 관리자 액세스 권한 없음 있습니다. Azure 스택 연산자에는 시스템 관리자 포털을 통해 또는 Azure 리소스 관리자를 통해 PowerShell 또는 REST API) (통해 관리 해야 합니다. Hyper-v 관리자 또는 장애 조치 클러스터 관리자와 같은 다른 관리 도구에서 시스템에 액세스할 수 없습니다 있습니다. 시스템을 보호 하기 위해 Azure 스택 인프라의 구성 요소 내부 제 3 자 소프트웨어 (예를 들어, 에이전트)를 설치할 수 없습니다. 외부 관리 및 보안 소프트웨어와의 상호 운용성 PowerShell 또는 REST API를 통해 발생합니다.

경고 중재 단계를 통해 확인 되지 문제 해결을 위한 높은 수준의 액세스 필요 하면 Microsoft 기술 지원 서비스 작업 해야 합니다. 지원을 통해 된 고급 작업을 수행 하는 시스템에 대 한 임시 전체 관리자 액세스를 제공 하는 방법은 없습니다. 

## <a name="identity-considerations"></a>Identity 고려 사항

### <a name="choose-identity-provider"></a>Id 공급자를 선택 합니다.
Azure 스택 배포의 경우 Azure AD 또는 AD FS 사용 하려면 id 공급자를 고려해 야 합니다. 전체 시스템 재배포 하지 않고 배포 후 id 공급자를 전환할 수 없습니다. Azure AD 계정을 소유 하지 않은 경우 클라우드 서비스 공급자가 제공한 인 계정을 사용 하 고 공급자를 전환 하 고 다른 Azure AD를 사용 하려는 경우 계정,이 시점에서 f 솔루션 다시 배포 하려면 솔루션 공급자에 게 문의 해야 합니다 또는 비용에 있습니다.



Identity provider 선택한 테 넌 트 가상 컴퓨터, id 시스템 및 사용 하는 계정에는 영향을 주지 않습니다는 등 Active Directory 도메인에 참가 합니다. 이 별개입니다.

에 id 공급자를 선택 하는 방법에 대 한 자세히 알아볼 수 있습니다는 [Azure 스택 통합형된 시스템 연결 모델 문서](.\azure-stack-connection-models.md)합니다.

### <a name="ad-fs-and-graph-integration"></a>AD FS 및 그래프 통합
AD FS를 사용 하 여 id 공급자로 Azure 스택 배포 하려는 경우 Azure 스택에 AD FS 인스턴스 페더레이션 트러스트를 통해 기존 AD FS 인스턴스와 통합 해야 합니다. 따라서 기존 Active Directory 포리스트를 스택에서 Azure 리소스를 사용 하 여 인증에서 id 수 있습니다.

기존 Active Directory와 Azure 스택의 그래프 서비스를 통합할 수도 있습니다. 이렇게 하면 역할 기반 액세스 제어 (RBAC) Azure 스택의 관리할 수 있습니다. 리소스에 대 한 액세스 위임 되는 그래프 구성 요소 LDAP 프로토콜을 사용 하 여 기존 Active Directory 포리스트의 사용자 계정을 조회 합니다.

다음 다이어그램에서는 통합 된 AD FS 및 그래프 트래픽 흐름을 보여 줍니다.
![AD FS 및 그래프 트래픽 흐름을 보여 주는 다이어그램](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>라이선스 모델
사용할 라이선스 모델을 결정 해야 합니다. 사용 가능한 옵션은 인터넷에 연결 하는 Azure 스택 배포 여부에 따라 달라 집니다.
- 에 대 한는 [배포 연결](azure-stack-connected-deployment.md), 지불으로-있습니다-사용 또는 용량 기반 라이선스를 선택할 수 있습니다. 사용량 기준 과금-으로-있습니다-사용 필요 사용량을 보고, Azure 상거래를 통해 다음 청구는 Azure에 연결 합니다. 
- 경우에 용량 기반 라이선스 지원 됩니다 있습니다 [배포 연결이 끊어진](azure-stack-disconnected-deployment.md) 인터넷에서 합니다. 

라이선스 모델에 대 한 자세한 내용은 참조 [패키징 및 가격에 Microsoft Azure 스택](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)합니다.


## <a name="naming-decisions"></a>명명 결정

Azure 스택 네임 스페이스, 특히 지역 이름 및 외부 도메인 이름 계획 하고자 하는 방법에 대해 생각 하는 것이 해야 합니다. 공용 끝점에 대 한 Azure 스택 배포의 외부 정규화 된 도메인 이름 (FQDN)은 이러한 두 가지 이름 중 조합: &lt; *지역*&gt;.&lt; *fqdn*&gt;합니다. 예를 들어 *east.cloud.fabrikam.com*합니다. 이 예제에서는 Azure 스택 포털 다음 Url에서 사용할 수 있는 합니다.

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Azure 스택 배포에 대 한 선택한 지역 이름은 고유 해야 하며 포털 주소에 표시 됩니다. 

다음 표에서 이러한 도메인 명명 결정을 요약 합니다.

| 이름 | 설명 | 
| -------- | ------------- | 
|지역 이름 | 첫 번째 Azure 스택 영역의 이름입니다. 이 이름은 공용 가상 IP 주소 (Vip)를 Azure 스택에서 관리에 대 한 FQDN의 일부로 사용 됩니다. 일반적으로 지역 이름 데이터 센터 위치와 같은 물리적 위치 식별자 것입니다. | 
| 외부 도메인 이름 | 외부 연결 Vip 사용 하 여 끝점에 대 한 DNS 도메인 이름 () 영역의 이름입니다. 이러한 공용 Vip FQDN에 사용 됩니다. | 
| 개인 (내부) 도메인 이름 | 인프라 관리에 대 한 Azure 스택에 만든 도메인 (및 내부 DNS 영역)의 이름입니다. 
| | |

## <a name="certificate-requirements"></a>인증서 요구 사항

배포에 대 한 공용 끝점에 대 한 Secure Sockets Layer (SSL) 인증서를 제공 해야 합니다. 상위 수준에서 인증서에는 다음과 같은 요구 사항이 있습니다.

- 단일 와일드 카드 인증서를 사용할 수 있습니다 또는 전용된 인증서 집합을 사용 하 고 저장소 및 키 자격 증명 모음 등 끝점에 대 한 와일드 카드를 사용할 수 있습니다.
- 신뢰할 수 있는 공용 인증 기관 (CA)에서 인증서를 발급할 수 또는 고객이 관리 하는 CA입니다.

어떤 PKI에 대 한 자세한 내용은 얻을, 참조, 하는 방법과 Azure 스택 배포 위해 인증서가 필요한 [Azure 스택 공개 키 인프라 인증서 요구 사항](azure-stack-pki-certs.md)합니다.  


> [!IMPORTANT]
> 일반적인 지침으로 제공된 된 PKI 인증서 정보를 사용 해야 합니다. Azure 스택에 대 한 모든 PKI 인증서를 구입 하기 전에 OEM 하드웨어 파트너와 작동 합니다. 더 자세한 인증서 지침 및 요구 사항을 제공 됩니다.


## <a name="time-synchronization"></a>시간 동기화
서버를 사용 하 여 Azure 스택을 동기화 하는 특정 시간을 선택 해야 합니다.  시간 symbolization 하려면 반드시 Azure 스택 및 인프라 역할을 서로 사용 하 여 내부 서비스를 인증 하는 데 사용 되는 Kerberos 티켓을 생성 하는 데 사용 합니다.

시간 동기화 서버에 대 한 IP 인프라의 구성 요소 대부분은 URL을 확인할 수 있지만 일부를 지원할 수 IP 주소를 지정 해야 합니다. 인 경우는 연결이 끊어진된 배포 옵션을 사용할 지정 해야 Azure 스택에 인프라 네트워크에서 연결할 수 있는지에 있는 회사 네트워크에서 시간 서버.

## <a name="connect-azure-stack-to-azure"></a>Azure 스택 Azure에 연결

하이브리드 클라우드 시나리오에 대 한 Azure 스택 Azure에 연결 하려는 방법을 계획 해야 합니다. Azure에서 가상 네트워크를 Azure 스택에서 가상 네트워크를 연결 하는 데 지원 되는 두 방법에는 
- **사이트 간**합니다. IPsec (IKE v1 및 IKE v2)을 통한 가상 사설망 (VPN) 연결 합니다. 이러한 종류의 연결에는 VPN 장치 또는 라우팅 및 원격 액세스 서비스 (RRAS) 필요합니다. Azure에서 VPN 게이트웨이에 대 한 자세한 내용은 참조 하십시오. [에 대 한 VPN 게이트웨이](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)합니다. 이 터널을 통해 통신 암호화 및 안전 합니다. 그러나 대역폭 (100-200 Mbps) 터널의 최대 처리량으로 제한 됩니다.
- **아웃 바운드 NAT**합니다. 기본적으로 모든 가상 컴퓨터가 Azure 스택 갖습니다 아웃 바운드 NAT. 통해 외부 네트워크에 연결 Azure 스택에서 생성 된 각 가상 네트워크에 할당 된 공용 IP 주소를 가져옵니다. 가상 컴퓨터에 직접 할당 된 공용 IP 주소 또는 공용 IP 주소와 부하 분산 장치 뒤를 통해 아웃 바운드 NAT 가상 네트워크의 VIP를 사용 하 여 아웃 바운드 액세스를 갖습니다. 이 가상 컴퓨터에 의해 시작 되 고 외부 네트워크 (인터넷 또는 인트라넷)에 대해 대상이 지정 되었으며 통신에만 작동 합니다. 외부에서 가상 컴퓨터와 통신 하는 사용할 수 없습니다.

### <a name="hybrid-connectivity-options"></a>하이브리드 연결 옵션

하이브리드 연결을 제공 하려는 어떤 종류의 배포와 배포 될 고려 하는 합니다. 테 넌 트 당 네트워크 트래픽을 격리 해야 하는지 여부 및 인트라넷 또는 인터넷 배포 해야 하는지 여부를 고려해 야 할 필요 합니다.

- **단일 테 넌 트 Azure 스택**합니다. Azure 스택 배포 보이는, 적어도 네트워킹 측면에서 단일 테 넌 트 말 이죠. 구독, 테 넌 트 많은 될 수 있지만 인트라넷 서비스와 마찬가지로 모든 트래픽이 동일한 네트워크를 통해 이동 합니다. 구독 간에 네트워크 트래픽을 다른 구독으로 동일한 네트워크 연결을 통해 이동 하 고 암호화 된 터널을 통해 격리 될 필요가 없습니다.

- **다중 테 넌 트 Azure 스택**합니다. Azure 스택 배포는 Azure 스택 외부에 있는 네트워크에 대 한 바인딩된 각 테 넌 트 구독 트래픽이 다른 테 넌 트의 네트워크 트래픽으로부터 격리 되어야 합니다.
 
- **인트라넷 배포**합니다. 하나 이상의 방화벽으로 보호 하 고 일반적으로 개인 IP 주소 공간에 회사 인트라넷에 있는 Azure 스택 배포 합니다. 공용 IP 주소 진정으로 공개 하지은 공용 인터넷을 통해 직접 라우팅할 수 없기 때문입니다.

- **인터넷 배포**합니다. 공용 VIP 범위에 대 한 공용에 연결 된 Azure 스택 배포 인터넷 라우팅 가능한 공용 IP를 사용 하 여 인터넷을 해결 합니다. 배포 방화벽 뒤에 계속 배치 될 수 있습니다 하지만 공용 VIP 범위는 공용 인터넷 및 Azure에서 직접 연결할 수 있습니다.
 
다음 표에서 장점과 단점, 사용 사례와 하이브리드 연결 시나리오를 요약 합니다.

| 시나리오 | 연결 방법 | 장점 | 단점 | 에 적합 합니다. |
| -- | -- | --| -- | --|
| 단일 테 넌 트 Azure 스택, 인트라넷 배포 | 아웃 바운드 NAT | 더 빠른 전송에 대 한 향상 된 대역폭입니다. 간편한 구현 합니다. 게이트웨이가 필요 없습니다. | 트래픽 암호화 되지 않습니다. 격리 없음 또는 TOR 다음 암호화 합니다. | 엔터프라이즈 배포의 모든 테 넌 트에 동일 하 게 신뢰할 수 있는 위치입니다.<br><br>Azure에 Azure ExpressRoute 회로 있는 기업을 합니다. |
| 인트라넷 배포의 다중 테 넌 트 Azure 스택 | 사이트 간 VPN | 대상 테 넌 트 VNet의에서 트래픽은 보안입니다. | 사이트 간 VPN 터널 대역폭 제한 됩니다.<br><br>가상 네트워크와 대상 네트워크 VPN 장치에 게이트웨이가 필요합니다. | 여기서 일부 테 넌 트 트래픽 엔터프라이즈 배포는 다른 테 넌 트 로부터 보호 되어야 합니다. |
| 단일 테 넌 트 Azure 스택, 인터넷 배포 | 아웃 바운드 NAT | 더 빠른 전송에 대 한 향상 된 대역폭입니다. | 트래픽 암호화 되지 않습니다. 격리 없음 또는 TOR 다음 암호화 합니다. | 호스팅 시나리오 테 넌 트 자신의 Azure 스택 배포 및 Azure 스택 환경에 전용된 회로 가져오는 위치입니다. 예를 들어 ExpressRoute 및 다중 프로토콜 레이블 스위칭 (MPLS).
| 다중 테 넌 트 Azure 스택, 인터넷 배포 | 사이트 간 VPN | 대상 테 넌 트 VNet의에서 트래픽은 보안입니다. | 사이트 간 VPN 터널 대역폭 제한 됩니다.<br><br>가상 네트워크와 대상 네트워크 VPN 장치에 게이트웨이가 필요합니다. | 하려는 시나리오에서 공급자는 다중 테 넌 트 클라우드 제공할 호스팅, 서로 캡슐화 된 트래픽과 테 넌 트를 신뢰 하지 않는 암호화 되어야 합니다.
|  |  |  |  |  |

### <a name="using-expressroute"></a>ExpressRoute 사용

Azure 스택을 통해 Azure에 연결할 수 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 인트라넷 단일 테 넌 트 및 다중 테 넌 트 시나리오 둘 다에 대 한 합니다. 통해 프로 비전 된 ExpressRoute 회로 해야 [연결 공급자](https://docs.microsoft.com/azure/expressroute/expressroute-locations)합니다.

다음 다이어그램에서는 단일 테 넌 트 시나리오에 대 한 express 경로 보여 줍니다. (여기서 "고객의 연결을" express 경로 회로).

![표시 된 단일 테 넌 트 ExpressRoute 시나리오를 다이어그램합니다](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

다음 다이어그램에서는 다중 테 넌 트 시나리오에 대 한 express 경로 보여 줍니다.

![다중 테 넌 트 ExpressRoute 시나리오를 보여 주는 다이어그램](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>외부 모니터링
Azure 스택 배포 및 장치에서 모든 경고의 단일 뷰에서 하 고 티켓에 대 한 기존 IT 서비스 관리 워크플로에 경고를 통합 하면 [솔루션을모니터링하는외부데이터센터와Azure스택통합](azure-stack-integrate-monitor.md).

Azure 스택 솔루션에 포함 된, 하드웨어 수명 주기 호스트는 하드웨어에 대 한 OEM 공급 업체에서 제공 하는 관리 도구를 실행 하는 Azure 스택 외부 컴퓨터입니다. 이러한 도구 또는 데이터 센터에서 기존 모니터링 솔루션 직접 통합 하는 다른 솔루션을 사용할 수 있습니다.

다음 표에서 현재 사용할 수 있는 옵션 목록이 요약 되어 있습니다.

| 영역 | 외부 모니터링 솔루션 |
| -- | -- |
| Azure 스택 소프트웨어 | [Operations Manager 용 관리 팩 azure 스택](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios 플러그 인](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST 기반 API 호출 | 
| 물리적 서버 (IPMI 통해 Bmc) | OEM 하드웨어-Operations Manager 공급 업체 관리 팩<br>OEM 하드웨어 공급 업체에서 제공한 솔루션<br>하드웨어 공급 업체 Nagios 플러그 인 | OEM 파트너 지원 모니터링 솔루션 (포함) | 
| 네트워크 장치에서는 SNMP) | Operations Manager 네트워크 장치 검색<br>OEM 하드웨어 공급 업체에서 제공한 솔루션<br>Nagios 스위치 플러그 인 |
| 테 넌 트 구독 상태 모니터링 | [Windows Azure 용 system Center 관리 팩](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

다음 요구 사항에 유의하세요.
- 사용 하면 솔루션은 에이전트 없는 이어야 합니다. Azure 스택 구성 요소 안의 제 3 자 에이전트를 설치할 수 없습니다. 
- System Center Operations Manager를 사용 하려는 경우 Operations Manager 2012 R2 또는 Operations Manager 2016가 필요 합니다.

## <a name="backup-and-disaster-recovery"></a>Backup 및 재해 복구

계획 인프라에 대해 모두 기본 스택 Azure IaaS 가상 컴퓨터 및 PaaS 서비스를 호스팅하는 및 테 넌 트 응용 프로그램 및 데이터에 대 한 백업 및 재해 복구 계획 작업이 포함 됩니다. 계획 해야 이러한 별도로.

### <a name="protect-infrastructure-components"></a>인프라 구성 요소를 보호 합니다.

있습니다 수 [Azure 스택 백업을](azure-stack-backup-back-up-azure-stack.md) SMB 인프라 구성 요소를 지정 하는 공유:

- 외부 SMB 파일 공유 기존의 Windows 기반 파일 서버 또는 제 3 자 장치에 필요 합니다.
- 네트워크 스위치와 하드웨어 수명 주기 호스트의 백업에 대 한이 동일한 공유를 사용 해야 합니다. OEM 하드웨어 공급 업체는 이러한 방화벽은 Azure 스택에 외부 백업 및 복원 구성 요소에 대 한 지침을 제공 하는 데 도움이 됩니다. 넌 OEM 공급 업체의 권장 사항을 기반으로 하는 백업 워크플로 실행 합니다.

예: 배포 입력과 식별자, 서비스 계정, CA 루트 인증서, 페더레이션된 리소스 (연결이 끊어진된 배포의 경우), 계획, 제안, reseed 배포 데이터에 대 한 인프라 백업 치명적인 데이터 손실을 발생 하는 경우 사용할 수 있습니다. 구독, 할당량, RBAC 정책 및 역할 할당 및 주요 자격 증명 모음 암호입니다.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>IaaS 가상 컴퓨터에서 테 넌 트 응용 프로그램을 보호

Azure 스택 백업 되지 않습니다 테 넌 트 응용 프로그램 및 데이터를 합니다. 에 대 한 백업 및 재해 복구 보호는 대상 외부 Azure 스택에 계획 해야 합니다. 테 넌 트 보호는 테 넌 트 기반 작업입니다. 테 넌 트 IaaS 가상 컴퓨터에 대 한 파일 폴더, 응용 프로그램 데이터 및 시스템 상태를 보호 하기 위해 게스트 기술을 사용할 수 있습니다. 그러나 엔터프라이즈 또는 서비스 공급 업체는 동일한 데이터 센터 내부 또는 외부 클라우드에 백업 및 복구 솔루션을 제공 수 있습니다.

Linux 또는 Windows IaaS 가상 컴퓨터를 백업 하려면 파일, 폴더, 운영 체제 상태 및 응용 프로그램 데이터를 보호 하기 위해 게스트 운영 체제에 액세스할 수 있는 백업 제품을 사용 해야 있습니다. 지원 되는 타사 제품 또는 Azure 백업으로 System Center 데이터 센터 Protection Manager를 사용할 수 있습니다.

데이터를 보조 위치에 복제 하 고 재해가 발생할 경우 응용 프로그램 장애 조치를 조정를 Azure Site Recovery 또는 지원 되는 타사 제품을 사용할 수 있습니다. (통합형된 시스템의 초기 릴리스에서 Azure Site Recovery을 지원 하지 않습니다 장애 복구 합니다. 그러나를 얻을 수 있습니다는 수동 프로세스를 통해 장애 복구 합니다.) 또한 네이티브 복제 (예: Microsoft SQL Server)를 지 원하는 응용 프로그램은 응용 프로그램이 실행 되 고 있는 다른 위치로 데이터를 복제할 수 있습니다.

> [!IMPORTANT]
> 통합된 시스템의 초기 릴리스에서 IaaS 가상 컴퓨터의 게스트 수준에서 작동 하는 보호 기술 지원 합니다. 기본 인프라 서버에 에이전트를 설치할 수 없습니다.

## <a name="learn-more"></a>자세한 정보

- 사용 사례, 구매, 파트너 및 OEM 하드웨어 공급 업체에 대 한 정보를 참조 하십시오.는 [Azure 스택](https://azure.microsoft.com/overview/azure-stack/) 제품 페이지.
- Azure 스택에 대 한 로드맵 및 지리적 가용성에 대 한 정보에 대 한 통합된 시스템 백서를 참조: [Azure 스택: Azure의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)합니다. 

## <a name="next-steps"></a>다음 단계
[Azure 스택 배포 연결 모델](azure-stack-connection-models.md)