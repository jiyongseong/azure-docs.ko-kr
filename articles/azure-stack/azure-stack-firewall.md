---
title: Azure 스택 계획 azure 스택 방화벽 시스템을 통합 | Microsoft Docs
description: 다중 노드 배포 Azure 스택 Azure에 연결 된 Azure 스택 방화벽 고려 사항에 설명합니다.
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
ms.date: 05/23/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 9d980c800f930c00b2b0140314f78ff3f043aa58
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604219"
---
# <a name="azure-stack-firewall-integration"></a>Azure 스택 방화벽 통합
Azure 스택 보안을 위해 방화벽 장치를 사용 하는 것이 좋습니다. 하지만 방화벽 등으로 분산 된 서비스 거부 (DDOS) 공격, 침입 감지 및 콘텐츠 검사에 도움이 될 수, blob, 테이블 및 큐와 같은 Azure 저장소 서비스에 대 한 처리량 병목 지점이 될 수도 있습니다.

Azure Active Directory (Azure AD) 또는 Windows Server Active Directory Federation Services (AD FS) id 모델에 따라, 있습니다 해야 할 수 AD FS 끝점을 게시 합니다. 연결이 끊긴된 배포 모드를 사용 하는 경우 AD FS 끝점을 게시 해야 합니다. 자세한 내용은 참조는 [데이터 센터 통합 identity 문서](azure-stack-integrate-identity.md)합니다.

Azure 리소스 관리자 (관리자), 관리자 포털 및 주요 자격 증명 모음 (관리자) 끝점 하지 않아도 외부 게시 합니다. 예를 들어 서비스 공급자로 하려는 공격 노출 영역 제한 및 내부 네트워크 및 인터넷에서에서 Azure 스택만 관리 합니다.

엔터프라이즈 조직 외부 네트워크에 기존 회사 네트워크 가능 합니다. 이 시나리오에서는 Azure 스택 회사 네트워크에서 작동 하도록 해당 끝점을 게시 해야 합니다.

### <a name="network-address-translation"></a>NAT(Network Address Translation)
네트워크 주소 변환 (NAT)는 (DVM) 외부 리소스 및 배포 하는 동안 인터넷에 액세스할 뿐만 아니라 Emergency 복구 콘솔 (ERCS) Vm에 가상 컴퓨터를 배포 또는 권한 있는 끝점 (PEP) 하는 동안 허용 하도록 권장 되는 방법 등록 및 문제 해결입니다.

NAT에서 외부 네트워크 또는 공용 Vip에 공용 IP 주소에 대 한 대안을 될 수도 있습니다. 그러나 테 넌 트 사용자 경험을 제한 하 고 복잡성 증가 하기 때문에 이렇게 하지 좋습니다. 두 옵션 1:1 NAT 여전히 사용자 IP 풀에 연결 됩니다. 사용자가 사용할 수 있는 모든 포트를 포함 하는 사용자 VIP 당 NAT 규칙을 필요로 하는 NAT 다: 1 당 하나의 공용 IP를 필요로 하는 것입니다.

공용 VIP에 대 한 NAT를 사용 하 여의 단점은 다음과 같습니다.
- 사용자가 고유한 끝점 및 소프트웨어 정의 네트워킹 (SDN) 스택에 직접 게시 규칙을 제어 하기 때문에 방화벽 규칙을 관리 하는 경우 NAT 오버 헤드가 추가 합니다. 사용자가 자신의 Vip를 게시 하 고 포트 목록을 업데이트 하는 Azure 스택 연산자를 연결 해야 합니다.
- 사용자 환경을 제한 하는 NAT를 사용 하는 동안이 전체 운영자에 게 효과적으로 제어할을 수 게시 요청을 처리 합니다.
- Azure 사용한 하이브리드 클라우드 시나리오에 대 한 Azure 설정을 NAT.를 사용 하는 끝점에 대 한 VPN 터널을 지원 하지 않습니다 것이 좋습니다

### <a name="ssl-decryption"></a>SSL 암호 해독
현재 모든 Azure 스택 트래픽을 SSL 암호 해독을 사용 하지 않도록 설정 하는 것이 좋습니다. 향후 업데이트에서 지원 않을 경우 Azure 스택에 대 한 SSL 암호 해독을 설정 하는 방법에 대 한 지침을 제공 됩니다.

## <a name="edge-firewall-scenario"></a>가장자리 방화벽 시나리오
가장자리 배포에서 Azure 스택은에 지 라우터 또는 방화벽 뒤에 직접 배포 됩니다. 이러한 시나리오에서 액티브-액티브 및 액티브-패시브 방화벽 구성 또는 액티브-액티브 방화벽만 지원 하므로 여기서 역할 테두리 장치 (시나리오 2)을 모두 지원 여기서 테두리 (시나리오 1)을 초과 하는 방화벽에 지원 됩니다. 신뢰에 같은 비용 다중 경로 ECMP ()를 BGP 또는 장애 조치에 대 한 정적 라우팅 구성입니다.

일반적으로 라우팅 가능한 공용 IP 주소는 외부 네트워크에서 공용 VIP 풀에 대 한 배포 시에 지정 됩니다. 가장자리 시나리오에서는 다른 네트워크에 보안을 위해 라우팅할 수 있는 공용 Ip를 사용 하도록 하지 좋습니다. 이 시나리오에는 사용자를를 Azure와 같은 공용 클라우드 처럼 전체 자체 제어 클라우드 환경을 경험할 수 있습니다.  

![Azure 스택 가장자리 방화벽 예제](.\media\azure-stack-firewall\firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>엔터프라이즈 인트라넷 이나 경계 네트워크 방화벽 시나리오
엔터프라이즈 인트라넷 또는 주변 배포에서 Azure 스택 사이의 경계 면 방화벽 및 회사 내부 네트워크 방화벽 또는 다중 영역 설정 방화벽에 배포 됩니다. 해당 트래픽을 분산 하는 다음의 보안, 경계 네트워크 (또는 DMZ) 사이 및 아래에서 설명으로 보안 되지 않은 영역:

- **보안 영역**: 이것은 내부 또는 회사 라우팅 가능한 IP 주소를 사용 하는 내부 네트워크입니다. 보안 네트워크 나눌 수 있습니다, 인터넷에 액세스할 아웃 바운드 NAT 통해 방화벽에서 및 내부 네트워크를 통해 데이터 센터 내에서 일반적으로 액세스할 수 있습니다. 모든 Azure 스택 네트워크는 외부 네트워크의 공용 VIP 풀 제외 하 고 보안 영역에 있어야 합니다.
- **경계 영역**합니다. 경계 네트워크가 외부 위치 또는 인터넷 연결 웹 서버를 일반적으로 배포와 같은 응용 프로그램입니다. 일반적으로 DDoS 및 인터넷에서 지정 된 인바운드 트래픽을 허용 하는 동안 (해킹) 침입 공격을 방지 하기 위해 방화벽에 의해 모니터링 됩니다. 만 외부 네트워크 공용 VIP 풀의 Azure 스택 DMZ 영역에 있어야 합니다.
- **보안 되지 않은 영역**합니다. 외부 네트워크, 인터넷입니다. 그 **않습니다** 보안 되지 않은 영역에 Azure 스택을 배포 하는 것이 좋습니다.

![Azure 스택 경계 네트워크 예제](.\media\azure-stack-firewall\perimeter-network-scenario.png)

## <a name="learn-more"></a>자세한 정보
에 대 한 자세한 내용은 [Azure 스택 끝점에서 사용할 포트와 프로토콜](azure-stack-integrate-endpoints.md)합니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택 PKI 요구 사항](azure-stack-pki-certs.md)

