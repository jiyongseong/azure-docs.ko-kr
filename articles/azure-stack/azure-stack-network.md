---
title: 네트워크와 통합 하는 Azure 스택 시스템에 대 한 통합 고려 사항 | Microsoft Docs
description: 다중 노드 Azure 스택와 데이터 센터 네트워크 통합에 대 한 계획을 수행할 수 있는 작업에 대해 알아봅니다.
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
ms.date: 05/09/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 752481186167fccb46d5bf3beb87c1507e0f4feb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="network-connectivity"></a>네트워크 연결
이 문서에서는 가장 Azure 스택을 기존 네트워킹 환경에 통합 하는 방법을 결정할 수 있도록 Azure 스택 네트워크 인프라 정보를 제공 합니다. 

> [!NOTE]
> Azure 스택 (예를 들어 www.bing.com)에서 외부 DNS 이름을 해결 하려면 DNS 요청을 전달 하도록 DNS 서버를 제공 해야 합니다. Azure 스택 DNS 요구 사항에 대 한 자세한 내용은 참조 [스택 Azure 데이터 센터 통합 DNS](azure-stack-integrate-dns.md)합니다.

## <a name="physical-network-design"></a>실제 네트워크 디자인
Azure 스택 솔루션에는 해당 작업 및 서비스를 지원 하기 위해 탄력적이 고 항상 사용 가능한 실제 인프라가 필요 합니다. 다음 다이어그램은 권장된 설계를 나타냅니다.

![권장 되는 스택 Azure 네트워크 디자인](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>논리 네트워크
논리 네트워크는 기본 실제 네트워크 인프라의 추상화를 나타냅니다. 구성 하 고 호스트, 가상 컴퓨터 및 서비스에 대 한 네트워크 할당을 간소화 하는 데 사용 됩니다. 논리 네트워크 만들기의 일부로 네트워크 사이트 가상 local area network (Vlan), IP 서브넷 및 각 실제 위치의 논리 네트워크와 연결 된 IP 서브넷/VLAN 쌍을 정의 하기 위해 만듭니다.

다음 표에서 논리 네트워크 및 계획 해야 하는 연결 된 IPv4 서브넷 범위를 보여 줍니다.

| 논리적 네트워크 | 설명 | 크기 | 
| -------- | ------------- | ------------ | 
| 공용 VIP | Azure 스택 32 주소를이 네트워크의 합계를 사용합니다. 8 개의 공용 IP 주소가 Azure 스택 서비스 중 작은 부분만에 사용 되 고 나머지 테 넌 트 가상 컴퓨터에서 사용 됩니다. 앱 서비스 및 SQL 리소스 공급자를 사용 하려는 경우에 7 더 많은 주소 사용 됩니다. | / 26 (62 호스트) / 22 (1022 호스트)<br><br>권장 = / 24 (254 호스트) | 
| 스위치 인프라 | 전용된 라우팅 목적에 대 한 지점 간 IP 주소 관리 인터페이스 및 스위치에 할당 된 루프백 주소를 전환 합니다. | /26 | 
| 인프라 | Azure 스택 내부 구성 요소에 대 한 통신 하는 데 사용 합니다. | /24 |
| 개인 | 저장소 네트워크 및 개인 Vip에 사용 합니다. | /24 | 
| BMC | 실제 호스트에서 Bmc와 통신 하는 데 사용 합니다. | /27 | 
| | | |

## <a name="network-infrastructure"></a>네트워크 인프라
Azure 스택에 대 한 네트워크 인프라는 스위치에 구성 된 여러 논리 네트워크 구성 됩니다. 다음 다이어그램에서는 이러한 논리 네트워크 및 베이스 보드 관리 컨트롤러 (BMC)는의-tor ()와 통합 하는 방법 (고객 네트워크) 스위치 테두리를 보여 줍니다.

![논리 네트워크 다이어그램 및 스위치 연결](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC 네트워크
이 네트워크 모든 베이스 보드 관리 컨트롤러 (라고도 서비스 프로세서, 예를 들어 iDRAC, iLO, iBMC 등)를 연결 하는 전용 관리 네트워크입니다. 있는 경우에 하드웨어 수명 주기 호스트 (HLH)이이 네트워크에 있는 및 하드웨어 유지 관리 또는 모니터링에 대 한 OEM 특정 소프트웨어를 제공할 수 있습니다. 

또한는 HLH 배포 VM (DVM)를 호스트합니다. DVM Azure 스택 배포 하는 동안 사용 되 고 배포가 완료 되 면 제거 됩니다. DVM 테스트, 유효성 검사 및 여러 구성 요소에 액세스 하는 연결 된 배포 시나리오에서 인터넷 액세스가 필요 합니다. 내부와 회사 네트워크 외부에서 이러한 구성 요소 수 있습니다. 예를 들어 NTP, DNS 및 Azure입니다. 연결 요구 사항에 대 한 자세한 내용은 참조는 [NAT 섹션에서 Azure 스택 방화벽 통합](azure-stack-firewall.md#network-address-translation)합니다. 

### <a name="private-network"></a>개인 네트워크
(254 호스트 IP의) 네트워크는 Azure 스택 영역 (Azure 스택 영역의 테두리 스위치 장치 보다 더 확장지 않습니다)에 전용 포트 이며 두 서브넷으로 구분 이/24:

- **저장소 네트워크**합니다. (126 호스트 IP의) 네트워크 공간 다이렉트 및 서버 메시지 블록 (SMB) 저장소 트래픽 및 가상 컴퓨터의 사용을 지 원하는 데 /25 실시간 마이그레이션. 
- **내부 가상 IP 네트워크**합니다. A/25 소프트웨어 부하 분산 장치의 Vip를 내부 전용으로 전용된 네트워크입니다.

### <a name="azure-stack-infrastructure-network"></a>Azure 스택 인프라 네트워크
이 통신 하 고 자체 데이터를 교환할 수 있도록 24 네트워크 내부 Azure 스택 구성 요소에 전용/입니다. 라우팅 가능한 IP 주소가 이어야 하는데는 비공개로 유지 솔루션에 대 한 액세스 제어 목록 (Acl)를 사용 하 여이 서브넷입니다. 테두리 스위치와 동일한 크기는 / 27 작은 범위를 제외 하 고 다음 라우팅 것으로 예상 되지 네트워크 외부 리소스 및/또는 인터넷에 대 한 액세스를 필요로 할 때 이러한 서비스 중에서 사용 합니다. 

### <a name="public-infrastructure-network"></a>공용 인프라 네트워크
이/27 네트워크는 앞에서 언급 한 Azure 스택 인프라 서브넷에서 작은 범위의 공용 IP 주소 필요 하지 않습니다 하지만 NAT 또는 투명 프록시를 통한 인터넷 액세스 필요지 않습니다. 이 네트워크 Emergency 복구 콘솔 시스템 (ERCS)에 대 한 할당 될, Azure에 등록 하는 동안 및 인프라 백업 중 ERCS VM에는 인터넷 액세스가 필요 합니다. ERCS VM 문제 해결을 위해 관리 네트워크에 라우팅할 수 있어야 합니다.

### <a name="public-vip-network"></a>공용 VIP 네트워크
공용 VIP 네트워크 스택에서 Azure 네트워크 컨트롤러에 할당 됩니다. 스위치는 논리 네트워크는 없습니다. SLB 주소 풀을 사용 하 고 할당/32 테 넌 트 작업에 대 한 네트워크. 스위치 라우팅 테이블에서 이러한 32 Ip는 BGP 통해 사용할 수 있는 경로를 보급 합니다. 이 네트워크 외부에서 액세스할 수 있는 또는 공용 IP 주소를 포함합니다. Azure 스택 인프라 나머지 테 넌 트 Vm에서 사용 하는 동안이 공용 VIP 네트워크에서 처음 31 주소를 예약 합니다. 이 서브넷의 네트워크 크기의 /26 (64 호스트)의 최소/22 (1022 호스트)의 최대 범위, / 24를 계획 하는 것이 좋습니다 네트워크입니다.

### <a name="switch-infrastructure-network"></a>인프라 네트워크 스위치
이/26 네트워크 라우팅할 수 있는 지점 간 IP/30 (2 호스트 IP의) 서브넷 및 전용는 루프백이 포함 된 서브넷은 / 대역에 대 한 32 서브넷 전환 관리 및 BGP 라우터 id입니다. 이 IP 주소 범위는 데이터 센터에 Azure 스택 솔루션의 외부에서 라우팅할 수 있어야 합니다., 개인 또는 공용 Ip 수 있습니다.

### <a name="switch-management-network"></a>관리 네트워크 스위치
이/29 (6 호스트 Ip) 네트워크 스위치 관리 포트를 연결 하는 전용입니다. 배포, 관리 및 문제 해결에 대 한 대역폭을 벗어난 액세스 수 있습니다. 위에서 언급 한 스위치 인프라 네트워크에서 계산 됩니다.

## <a name="publish-azure-stack-services"></a>Azure 스택 서비스 게시
Azure 스택 서비스 사용할 수 있도록 사용자에 게 외부 Azure 스택에서 해야 합니다. Azure 스택 인프라 역할에 대 한 다양 한 끝점을 설정합니다. 이러한 끝점 공용 IP 주소 풀에서 Vip가 할당 됩니다. 외부 DNS 영역 배포 시에 지정 된 각 끝점에 대 한 DNS 항목이 생성 됩니다. 예를 들어 사용자 포털 포털의 DNS 호스트 항목을 할당 됩니다.  *&lt;지역 >.&lt; fqdn >* 합니다.

### <a name="ports-and-urls"></a>포트 및 Url
Azure 스택 서비스를 확인 하려면 (포털, Azure 리소스 관리자, DNS, 같은 등) 외부 네트워크에 사용할 수 있는 허용 해야 이러한 끝점에 인바운드 트래픽을 특정 Url, 포트 및 프로토콜에 대 한 합니다.
 
배포에 있는 기존 프록시 서버에 투명 프록시 업링크 허용 해야 특정 포트 및 Url 모두에 대 한 [인바운드](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) 및 [아웃 바운드](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) 통신 합니다. 여기 identity, 마켓플레이스 신디케이션, 패치 및 업데이트, 등록 및 사용 현황 데이터에 대 한 포트 및 Url이 포함 됩니다.

## <a name="next-steps"></a>다음 단계
[테두리 연결](azure-stack-border-connectivity.md)