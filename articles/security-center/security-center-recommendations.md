---
title: Azure Security Center에서 보안 권장 사항 관리 | Microsoft Docs
description: 이 문서에서는 Azure Security Center의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 어떤 도움이 되는지 알아봅니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 72070f46309adb526901192752fe421a3846398b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364253"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Azure Security Center에서 보안 권장 사항 관리
이 문서에서는 Azure Security Center의 권장 사항을 사용하여 Azure 리소스를 보호하는 데 도움이 되는 방법을 안내합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  이 문서는 단계별 가이드가 아닙니다.
>
>

## <a name="what-are-security-recommendations"></a>보안 권장 사항이란?
Security Center에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 권장 사항을 만듭니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다.

## <a name="implementing-security-recommendations"></a>보안 권장 사항 구현
### <a name="set-recommendations"></a>권장 사항 설정
[Azure Security Center의 보안 정책 설정](security-center-policies.md)에서 다음을 배울 수 있습니다.

* 보안 정책 구성.
* 데이터 수집 사용.
* 보안 정책의 일부로 표시될 권장 사항 선택.

현재 정책 권장 사항은 시스템 업데이트, 기준 규칙, 맬웨어 방지 프로그램, 서브넷 및 네트워크 인터페이스의 [네트워크 보안 그룹](../virtual-network/security-overview.md), SQL 데이터베이스 감사, SQL 데이터베이스 투명한 데이터 암호화 및 웹 응용 프로그램 방화벽에 중점을 두고 있습니다.  [보안 정책 설정](security-center-policies.md) 은 각 권장 사항 옵션에 대한 설명을 제공합니다.

### <a name="monitor-recommendations"></a>권장 사항 모니터링
보안 정책이 설정되면 보안 센터는 리소스의 보안 상태를 분석하여 잠재적인 취약성을 식별합니다. **개요**의 **권장 사항** 타일은 Security Center에서 식별된 권장 사항의 총 수를 알려줍니다.

![권장 사항 타일][1]

각 권장 사항에 대한 세부 정보를 보려면 **개요**에서 **권장 사항** 타일을 선택합니다. **권장 사항**이 열립니다.

![필터 권장 사항][2]

권장 사항은 각 줄이 한 가지 특정 권장을 나타내는 표 형식으로 표시됩니다. 이 테이블의 열은 다음과 같습니다.

* **설명**: 권장 사항 및 권장 사항을 해결하기 위해 수행해야 할 작업을 설명합니다.
* **리소스**:이 권장 사항이 적용되는 리소스를 나열합니다.
* **상태**: 권장 사항의 현재 상태를 설명합니다.
  * **열기**: 권장 사항이 아직 해결되지 않았습니다.
  * **진행 중**: 권장 사항이 현재 해당 리소스에 적용되고 있으며 사용자가 아무런 작업도 수행할 필요가 없습니다.
  * **해결됨**: 권장 사항이 이미 완료되었습니다(이 경우에 줄은 회색으로 표시됨).
* **심각도**: 특정 권장 사항의 심각도를 설명합니다.
  * **높음**: 중요한 리소스(응용 프로그램, VM, 네트워크 또는 보안 그룹 등)에 취약점이 있으며 주의가 필요합니다.
  * **보통**: 취약점이 있으며 중요하지 않은 또는 추가 단계가 프로세스를 완료하거나 취약점을 제거하는 데 필요합니다.
  * **낮음**: 해결해야 하지만 즉시 조치하지 않아도 되는 취약점이 있습니다. (기본적으로 낮은 권장 사항은 표시되지 않지만 표시하려는 경우 낮은 권장 사항으로 필터링할 수 있습니다.)

아래 테이블을 참조로 사용하여 제공되는 권장 사항을 이해하고 각 권장 사항을 적용할 경우 어떻게 되는지 이해할 수 있습니다.

> [!NOTE]
> Azure 리소스의 [클래식 및 Resource Manager 배포 모델](../azure-classic-rm.md) 을 이해하게 됩니다.
>
>

| 권장 사항 | 설명 |
| --- | --- |
| [구독에 대해 데이터 수집 활성화](security-center-enable-data-collection.md) |각 구독과 모든 Azure VM(VIrtual Machines) 및 비 Azure 컴퓨터에 대한 보안 정책에서 데이터 수집을 켜는 것이 좋습니다. |
| [보안 구성 수정](security-center-remediate-os-vulnerabilities.md) |OS 구성을 권장 보안 구성 규칙에 맞추도록 권장합니다. 예를 들어 암호 저장을 허용하지 않습니다. |
| [시스템 업데이트 적용](security-center-apply-system-updates.md) |누락된 시스템 보안 및 중요 업데이트를 Windows 및 Linux VM과 컴퓨터에 배포하는 것이 좋습니다. |
| [Just-In-Time 네트워크 액세스 제어 적용](security-center-just-in-time.md) | Just-In-Time VM 액세스만 적용해야 합니다. Just-In-Time 기능은 미리 보기로 제공되며 Security Center의 표준 계층에서 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요. |
| [시스템 업데이트 후 다시 부팅](security-center-apply-system-updates.md#reboot-after-system-updates) |시스템 업데이트 적용 프로세스를 완료하려면 VM을 다시 부팅하는 것이 좋습니다. |
| [웹 응용 프로그램 방화벽 추가](security-center-add-web-application-firewall.md) |웹 끝점에 WAF(웹 응용 프로그램 방화벽)를 배포하는 것이 좋습니다. 공개 인바운드 웹 포트(80,443)으로 연결된 네트워크 보안 그룹에 있는 모든 공용 연결 IP(인스턴스 수준 IP 또는 부하 분산된 IP)에 대해 WAF 권장 사항이 표시됩니다. </br>Security Center에서는 가상 머신과 App Service 환경에 있는 웹 응용 프로그램을 대상으로 한 공격을 방어할 수 있도록 WAF를 프로비전할 것을 권장합니다. ASE(App Service 환경)는 Azure App Service의 [프리미엄](https://azure.microsoft.com/pricing/details/app-service/) 서비스 계획 옵션으로, Azure App Service 앱의 안전한 실행을 위해 완전히 격리된 전용 환경을 제공합니다. ASE에 대한 자세한 내용을 보려면 [App Service Environment 설명서](../app-service/environment/intro.md)를 참조하세요.</br>기존 WAF 배포에 이러한 응용 프로그램을 추가하여 보안 센터에서 여러 웹 응용 프로그램을 보호할 수 있습니다. |
| [응용 프로그램 보호 완료](security-center-add-web-application-firewall.md#finalize-application-protection) |WAF 구성을 완료하려면 트래픽 경로가 WAF 어플라이언스로 전환되어야 합니다. 이 권장 사항을 따르면 필요한 설정 변경이 완료됩니다. |
| [차세대 방화벽 추가](security-center-add-next-generation-firewall.md) |보안 보호를 증가시키기 위해 Microsoft 파트너의 차세대 방화벽(NGFW)을 추가하라는 권장 사항입니다. |
| [NGFW를 통해서만 트래픽 라우팅](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |인바운드 트래픽이 NGFW를 통해 VM로 강제하도록 네트워크 보안 그룹(NSG) 규칙을 구성하는 것이 좋습니다. |
| [Endpoint Protection 설치](security-center-install-endpoint-protection.md) |VM(Windows VM만 해당)에 맬웨어 방지 프로그램을 프로비전하는 것이 좋습니다. |
| [서브넷 또는 가상 머신에서 네트워크 보안 그룹 활성화](security-center-enable-network-security-groups.md) |서브넷 또는 VM에서 NSG를 활성화하는 것이 좋습니다. |
| [인터넷 끝점을 통한 액세스 제한](security-center-restrict-access-through-internet-facing-endpoints.md) |NSG에 대한 인바운드 트래픽 규칙을 구성하라는 권장 사항입니다. |
| [SQL Server에서 감사 및 위협 감지 사용](security-center-enable-auditing-on-sql-servers.md) |Azure SQL 서버에 대한 감사 및 위협 감지를 켜는 것이 좋습니다. (Azure SQL 서비스에만 해당됩니다. 가상 머신에서 실행 중인 SQL을 포함하지 않습니다.) |
| [SQL Database에서 감사 및 위협 감지 사용](security-center-enable-auditing-on-sql-databases.md) |Azure SQL Database에 대한 감사 및 위협 감지를 켜는 것이 좋습니다. (Azure SQL 서비스에만 해당됩니다. 가상 머신에서 실행 중인 SQL을 포함하지 않습니다.) |
| [SQL 데이터베이스에서 투명한 데이터 암호화 활성화](security-center-enable-transparent-data-encryption.md) |SQL Database에 대해 암호화를 활성화하는 것이 좋습니다. (Azure SQL 서비스에만 해당됩니다.) |
| [VM 에이전트 사용](security-center-enable-vm-agent.md) |VM 에이전트가 필요한 VM을 확인할 수 있습니다. 패치 검색, 기준 검색 및 맬웨어 방지 프로그램을 프로비전하려면 VM에 VM 에이전트가 설치되어 있어야 합니다. Azure Marketplace에서 배포된 VM에 VM 에이전트가 기본적으로 설치됩니다. [VM 에이전트 및 확장 - 2부](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) 문서에 VM 에이전트 설치 방법이 설명되어 있습니다. |
| [디스크 암호화 적용](security-center-apply-disk-encryption.md) |Azure 디스크 암호화(Windows 및 Linux VM)를 사용하여 VM 디스크를 암호화하는 것이 좋습니다. VM에서 OS 및 데이터 볼륨에 암호화를 사용하는 것이 좋습니다. |
| [보안 연락처 세부 정보 제공](security-center-provide-security-contact-details.md) |각 구독에 대한 보안 연락처 정보를 제공하는 것을 권장합니다. 연락처 정보는 전자 메일 주소 및 전화 번호입니다. 정보는 보안 팀에서 리소스가 손상된 것을 발견한 경우에 고객에게 연락하는 데 사용됩니다. |
| [OS 버전 업데이트](security-center-update-os-version.md) |클라우드 서비스의 OS(운영 체제) 버전을 OS 제품군에 대해 사용할 수 있는 가장 최신 버전으로 업데이트하는 것이 좋습니다.  Cloud Services에 대한 자세한 내용은 [Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요. |
| [취약점 평가 설치되지 않음](security-center-vulnerability-assessment-recommendations.md) |VM에 취약점 평가 솔루션을 설치하는 것이 좋습니다. |
| [취약점 해결](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |VM에 설치된 취약점 평가 솔루션에 감지되는 시스템 및 응용 프로그램 취약점을 볼 수 있습니다. |
| [Azure Storage 계정에 암호화 사용](security-center-enable-encryption-for-storage-account.md) | 미사용 데이터에 대한 Azure Storage 서비스 암호화를 사용하도록 권장합니다. SSE(Storage 서비스 암호화)는 Azure Storage에 기록되는 데이터를 암호화하고 검색 전 해독하는 방식으로 작동합니다. SSE는 현재 Azure Blob service에만 사용할 수 있으며 블록 blob, 페이지 blob 및 추가 blob에 사용할 수 있습니다. 자세한 내용을 알아보려면 [미사용 데이터에 대한 Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.</br>SSE는 Resource Manager 저장소 계정에만 지원됩니다. |

필터링을 수행하고 권장 사항을 해제할 수 있습니다.

1. **권장 사항** 블레이드에서 **필터**를 선택합니다. **필터** 블레이드가 열리면 확인하려는 심각도 및 상태 값을 선택합니다.

2. 권장 사항이 적용하기에 적절하지 않다고 생각되면 권장 사항을 해제하고 보기에서 필터링합니다. 권장 사항을 해제하는 두 가지 방법이 있습니다. 한 가지 방법은 항목을 마우스 오른쪽 단추로 클릭한 다음 **해제**를 선택하는 것입니다. 다른 방법은 마우스 포인터로 항목을 가리키고 오른쪽에 나타나는 점 세 개를 클릭한 다음 **해제**를 선택하는 것입니다. **필터**를 클릭하고 **해제됨**을 선택하면 해제된 권장 사항을 볼 수 있습니다.

    ![권장 사항 해제][3]

### <a name="apply-recommendations"></a>권장 사항 적용
모든 권장 사항을 검토한 후에 가장 먼저 적용해야 할 권장 사항을 결정합니다. 심각도 등급을 기본 매개 변수로 사용하여 가장 먼저 적용해야 할 권장 사항을 평가하는 것이 좋습니다.

위의 권장 사항 표에서 권장 사항을 선택하고 권장 사항을 적용하는 방법의 예제로 연습합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터의 보안 권장 사항을 소개했습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
