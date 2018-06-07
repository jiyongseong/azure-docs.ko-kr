---
title: Azure Security Center에서 보안 정책 설정 | Microsoft Docs
description: 이 문서를 통해 Azure Security Center에서 보안 정책을 구성할 수 있습니다.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: fed4a587d2258cdab35d09f5e3947a8cacade157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364389"
---
# <a name="set-security-policies-in-azure-security-center"></a>Azure Security Center에서 보안 정책 설정
이 문서를 통해 Security Center에서 보안 정책을 구성할 수 있습니다.

## <a name="how-security-policies-work"></a>보안 정책 작동 방법
Security Center는 각 Azure 구독에 대한 기본 보안 정책을 자동으로 만듭니다. Security Center에서 정책을 편집하고 정책 준수를 모니터링할 수 있습니다.

> [!NOTE]
> 이제 제한된 미리 보기 상태인 Azure Policy를 사용하여 Security Center 정책을 확장할 수 있습니다. 미리 보기에 조인하려면 [Azure Policy 등록](https://aka.ms/getpolicy)으로 이동합니다. 자세한 내용은 [Azure Policy를 사용하여 Security Center 보안 정책 통합](security-center-azure-policy.md)을 참조하세요.

개발 또는 테스트에 사용되는 리소스에 대한 보안 요구 사항은 프로덕션 응용 프로그램에 사용되는 리소스에 대한 요구 사항과 다를 수 있습니다. 개인 식별 정보와 같은 규제된 데이터를 가진 응용 프로그램에는 더 높은 수준의 보안이 필요할 수 있습니다. Azure Security Center에서 활성화된 보안 정책에 따라 잠재적 취약점을 파악하고 위험을 완화하는 데 도움이 되는 보안 권장 사항과 모니터링이 결정됩니다. 더 적절한 옵션을 결정하는 방법에 대한 자세한 내용은 [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md)를 참조하세요.

## <a name="edit-security-policies"></a>보안 정책 편집
Security Center에서 각 Azure 구독에 대한 기본 보안 정책을 편집할 수 있습니다. 보안 정책을 수정하려면 구독의 소유자, 참여자 또는 보안 관리자여야 합니다. Security Center에서 보안 정책을 구성하려면 다음을 수행합니다.

1. Azure 포털에 로그인합니다.

2. **Security Center** 대시보드의 **일반** 아래에서 **보안 정책**을 클릭합니다.

3. 보안 정책을 사용하도록 설정하려는 구독을 선택합니다.

4. **정책 구성 요소** 섹션에서 **보안 정책**을 클릭합니다.  
    Security Center에 할당된 기본 정책입니다. 사용 가능한 보안 권장 사항을 설정 또는 해제할 수 있습니다.

5. 편집이 완료되면 **저장**을 선택합니다.

## <a name="available-security-policy-definitions"></a>사용 가능한 보안 정책 정의

기본 보안 정책에서 사용할 수 있는 정책 정의를 이해하려면 다음 표를 참조하세요.

| 정책 | 정책에서 수행하는 작업 |
| --- | --- |
| 시스템 업데이트 |Windows 업데이트 또는 Windows Server Update Services에서 사용 가능한 보안 및 중요 업데이트의 일일 목록을 검색합니다. 검색된 목록은 가상 머신에 구성된 서비스에 따라 다르며, 누락된 업데이트를 적용하도록 권장합니다. Linux 시스템의 경우 정책은 배포판에서 제공하는 패키지 관리 시스템을 사용하여 사용 가능한 업데이트가 있는 패키지를 확인합니다. 또한 [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) 가상 머신에서 보안 및 중요 업데이트를 확인합니다. |
| 보안 구성 |운영 체제 구성을 매일 분석하여 가상 머신을 공격에 취약하게 만들 수 있는 문제를 확인합니다. 또한 정책은 이러한 취약점을 해결하는 구성 변경 내용을 권장합니다. 모니터링되는 특정 구성에 대한 자세한 내용은 [권장되는 기준 목록](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)을 참조하세요. (현재는 Windows Server 2016이 완전히 지원되지 않습니다.) |
| Endpoint Protection |바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하도록 모든 Windows VM(가상 머신)에 끝점 보호를 설정하는 것이 좋습니다. |
| 디스크 암호화 |모든 가상 머신에서 디스크 암호화를 사용하여 휴지 상태의 데이터 보호 기능을 향상시키는 것이 좋습니다. |
| 네트워크 보안 그룹 |공용 끝점을 사용하여 VM에 대한 인바운드 및 아웃바운드 트래픽을 제어할 수 있도록 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 구성하는 것이 좋습니다. 서브넷에 대해 구성된 네트워크 보안 그룹은 별도로 지정하지 않는 한 모든 가상 컴퓨터 네트워크 인터페이스에 상속됩니다. 이 정책은 네트워크 보안 그룹이 구성되었는지 확인하는 것 외에도 인바운드 보안 규칙을 평가하여 들어오는 트래픽을 허용하는 규칙을 식별합니다. |
| 웹 응용 프로그램 방화벽 |다음 중 하나가 true인 경우 가상 머신에 웹 응용 프로그램 방화벽을 설정하는 것이 좋습니다. <ul><li>[인스턴스 수준 공용 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)를 사용하고, 연결된 네트워크 보안 그룹에 대한 인바운드 보안 규칙에서 80/443 포트에 대한 액세스를 허용하도록 구성합니다.</li><li>부하 분산된 IP를 사용하고, 연결된 부하 분산 및 인바운드 NAT(Network Address Translation) 규칙에서 80/443 포트에 대한 액세스를 허용하도록 구성합니다. 자세한 내용은 [부하 분산 장치에 대한 Azure Resource Manager 지원](../load-balancer/load-balancer-arm.md)을 참조하세요.</li> |
| 차세대 방화벽 |Azure에 기본 제공되는 네트워크 보안 그룹 외에도 네트워크 보호 기능을 확장합니다. Security Center에서 차세대 방화벽이 권장되는 배포를 검색한 다음 가상 어플라이언스를 설정할 수 있습니다. |
| SQL 감사 및 위협 검색 |조사를 위해 SQL 데이터베이스에 대한 액세스 감사를 규정 준수 및 고급 위협 검색에 사용하도록 설정하는 것이 좋습니다. |
| SQL 암호화 |미사용 데이터 암호화를 SQL 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일에 사용하도록 설정하는 것이 좋습니다. 데이터가 위반되는 경우에도 해당 데이터를 읽을 수 없습니다. |
| 취약점 평가 |VM에 취약점 평가 솔루션을 설치하는 것이 좋습니다. |
| 저장소 암호화 |현재 이 기능은 Blob 및 Azure Files에서 사용할 수 있습니다. 저장소 서비스 암호화를 사용하도록 설정하면 새 데이터만 암호화되고 이 저장소 계정의 기존 파일은 암호화되지 않은 상태로 유지됩니다. |
| JIT 네트워크 액세스 |JIT(Just-In-Time)를 사용하도록 설정하면 Security Center에서 네트워크 보안 그룹 규칙을 만들어 Azure VM에 대한 인바운드 트래픽을 잠급니다. VM에서 인바운드 트래픽을 잠글 포트를 선택합니다. 자세한 내용은 [Just-In-Time를 사용하여 가상 머신 액세스 관리](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 참조하세요. |


## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 보안 정책을 구성하는 방법을 알아보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md): Azure Security Center에 대한 디자인 고려 사항을 계획하고 이해하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답을 가져옵니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
