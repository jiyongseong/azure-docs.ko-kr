---
title: Azure 결제 처리 청사진 - 테스트 요구 사항
description: PCI DSS 요구 사항 11
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 21cb854ba35adcf7576874e3c6c7149fbd33d998
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 테스트 요구 사항 
## <a name="pci-dss-requirement-11"></a>PCI DSS 요구 사항 11

**정기적인 보안 시스템 및 프로세스 테스트**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

취약성은 악의적인 개인이나 연구원에 의해 지속적으로 발견되고 새로운 소프트웨어에서 소개하고 있습니다. 시스템 구성 요소, 프로세스 및 사용자 지정 소프트웨어를 자주 테스트하여 변화하는 환경을 보안 제어에 계속 반영하도록 해야 합니다.

## <a name="pci-dss-requirement-111"></a>PCI DSS 요구 사항 11.1

**11.1** 무선 액세스 지점(802.11)의 존재 여부를 테스트하는 프로세스를 구현하고, 권한이 있거나 없는 모든 무선 액세스 지점을 분기별로 검색하고 식별합니다.

> [!NOTE]
> 이 프로세스에 사용할 수 있는 방법으로 무선 네트워크 검사, 시스템 구성 요소와 인프라의 물리적/논리적 검사, NAC(네트워크 액세스 제어) 또는 무선 IDS/IPS가 있습니다.
어떤 방법을 사용하든 권한이 있는 장치와 권한이 없는 장치를 모두 검색하고 식별하는 데 충분해야 합니다.


**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure는 Azure 네트워크 환경에서 무선 연결을 허용하지 않습니다. 내부 보안 팀은 정기적으로 불량 무선 신호를 분기별로 검사하여 불량 신호를 조사하고 제거합니다. 고객은 Azure 환경에서 무선 기술을 배포하도록 허용되지 않습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 무선 및 SNMP는 솔루션에서 구현되지 않습니다.|



### <a name="pci-dss-requirement-1111"></a>PCI DSS 요구 사항 11.1.1

**11.1.1** 문서화된 비즈니스 타당성을 포함하여 권한이 있는 무선 액세스 지점의 인벤토리를 유지 관리합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 11.1](#pci-dss-requirement-11-1) "Microsoft Azure" 섹션 참조 |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 무선 및 SNMP는 솔루션에서 구현되지 않습니다.|



### <a name="pci-dss-requirement-1112"></a>PCI DSS 요구 사항 11.1.2

**11.1.2** 권한이 없는 무선 액세스 지점이 검색되는 경우 인시던트 대응 절차를 구현합니다.


**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 11.1](#pci-dss-requirement-11-1) "Microsoft Azure" 섹션 참조 |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 무선 및 SNMP는 솔루션에서 구현되지 않습니다.|



## <a name="pci-dss-requirement-112"></a>PCI DSS 요구 사항 11.2

**11.2** 적어도 매 분기 및 중요한 네트워크 변경(예: 새 시스템 구성 요소 설치, 네트워크 토폴로지 변경, 방화벽 규칙 수정, 제품 업그레이드) 후에 내/외부 네트워크 취약성 검사를 실행합니다. 

> [!NOTE]
> 분기별 검사 프로세스에 대해 여러 검사 보고서를 결합하여 모든 시스템을 검사하고 적용 가능한 모든 취약성이 해결되었는지 확인할 수 있습니다. 수정되지 않은 취약성이 해결 중에 있는지 확인하려면 추가 설명서가 필요할 수 있습니다.
> 초기 PCI DSS 준수의 경우 평가자가 1) 가장 최근의 검사 결과가 시행 검사였는지, 2) 회사에서 분기별 검사를 요구하는 정책과 절차를 문서화했는지 및 3) 검사 결과에 명시된 취약성이 다시 검사에 표시된 대로 수정되었는지를 확인하는 경우 4개 분기의 시행 검사를 완료할 필요는 없습니다. 초기 PCI DSS 검토 후에 이어지는 연도부터 4개 분기의 시행 검사가 발생되어야 합니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure는 분기별 내/외부 취약성 검사를 수행합니다. 검사는 자격 있는 담당자에 의해 수행됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 '있는 그대로'의 활동으로 침투 테스트를 통해 취약성을 검사했습니다. 침투 테스트 결과는 nmap 또는 pentest-tools.com과 같은 공통 도구를 사용하여 복제할 수 있습니다. 그리고 결과가 불충분하고 악용할 수 있는 항목이 없는 공격 노출 영역을 제공합니다. 또한 [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공합니다.|



### <a name="pci-dss-requirement-1121"></a>PCI DSS 요구 사항 11.2.1

**11.2.1** 분기별 내부 취약성 검사를 수행합니다. 취약성을 해결하고, "위험 수준이 높은" 모든 취약성이 회사의 취약성 순위(요구 사항 6.1 기준)에 따라 해결되는지 확인하기 위해 다시 검사를 수행합니다. 검사는 자격 있는 담당자에 의해 수행되어야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 범위 내의 기본 인프라에 대한 취약성 검사를 수행합니다. Microsoft Azure는 적절한 취약성 검사 도구를 사용하여 서버 운영 체제, 데이터베이스 및 네트워크 장치에 대한 취약성 검사를 구현합니다. Azure 웹 응용 프로그램은 적절한 업계 검사 솔루션을 사용하여 검사됩니다. 취약성 검사는 분기별로 수행됩니다.<br /><br />다시 검사는 "위험 수준이 높은" 모든 취약성(요구 사항 6.1에서 확인됨)이 해결될 때까지 필요에 따라 모든 시스템에 대해 수행됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 '있는 그대로'의 활동으로 침투 테스트를 통해 취약성을 검사했습니다. 침투 테스트 결과는 nmap 또는 pentest-tools.com과 같은 공통 도구를 사용하여 복제할 수 있습니다. 그리고 결과가 불충분하고 악용할 수 있는 항목이 없는 공격 노출 영역을 제공합니다. 또한 [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공합니다.|



### <a name="pci-dss-requirement-1122"></a>PCI DSS 요구 사항 11.2.2

**11.2.2** PCI SSC(결제 카드 산업 보안 표준 위원회)에서 승인한 ASV(승인된 검사 공급업체)를 통해 분기별 외부 취약성 검사를 수행합니다. 시행 검사를 통과할 때까지 필요에 따라 다시 검사를 수행합니다. 

> [!NOTE]
> 분기별 외부 취약성 검사는 PCI SSC에서 승인한 ASV에 의해 수행되어야 합니다.
> 고객의 검사 책임, 검사 준비 등은 PCI SSC 웹 사이트에 게시된 ASV 프로그램 가이드를 참조하세요.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 외부에서 액세스할 수 있는 범위 내의 기본 인프라에 대한 외부 취약성 검사를 수행합니다. 검사는 ASV에 의해 수행됩니다.<br /><br />Microsoft Azure는 MSRC/OSSC 월별 패치 알림을 구독하고 적어도 분기별로 취약성을 검사합니다. 식별된 취약성은 위험 수준에 따라 설정된 타임라인마다 평가되고 수정됩니다.<br /><br />우선 순위가 지정된 Microsoft Azure 환경의 구성 요소에 대해 각 분기마다 목표로 하는 광범위한 보안 취약성 검사가 수행되어 보안 취약성을 식별합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore를 배포할 때 데모 버전을 사용하는 고객은 CDE(카드 소유자 데이터 환경)의 모든 PaaS 인스턴스에 대해 필요에 따라 결제 카드 산업 보안 표준 위원회에서 승인한 ASV(승인된 검사 공급업체)를 통해 분기별 외부 취약성 검사 및 다시 검사를 수행할 책임이 있습니다.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>PCI DSS 요구 사항 11.2.3

**11.2.3** 중요한 변경 후에는 내/외부 검사를 수행하고 필요에 따라 다시 검사합니다. 검사는 자격 있는 담당자에 의해 수행되어야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 결과는 이해 관계자에게 보고되고, 수정은 NDA(비밀 유지 계약)를 통해 Azure 보안 팀에 의해 추적됩니다. Azure 테스트 결과는 NDA에 따라 고객과 공유할 수 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 CDE의 모든 PaaS 인스턴스에 대해 필요에 따라 분기별 내/외부 취약성 검사 및 다시 검사를 수행할 책임이 있습니다. 범위 내 환경에 대한 중요한 변경 후에는 검사를 수행해야 합니다.<br /><br />검사는 ASV 또는 조직에서 독립된 담당자에 의해 수행되어야 합니다.|



## <a name="pci-dss-requirement-113"></a>PCI DSS 요구 사항 11.3

**11.3** 다음 항목이 적용된 침투 테스트 방법을 구현합니다.
- 업계에서 인정하는 침투 테스트 방법(예: NIST SP800-115)을 기반으로 합니다.
- 전체 CDE 경계 및 중요 시스템에 대한 적용 범위를 포함합니다.
- 네트워크 내부 및 외부 테스트를 모두 포함합니다.
- 모든 조각화 및 범위 감소 제어에 대한 유효성을 검사하는 테스트를 포함합니다.
- 적어도 요구 사항 6.5에 나열된 취약성을 포함하도록 응용 프로그램 계층 침투 테스트를 정의합니다.
- 운영 체제 및 네트워크 기능을 지원하는 구성 요소를 포함하도록 네트워크 계층 침투 테스트를 정의합니다.
- 지난 12개월 동안 경험한 위협 및 취약성에 대한 검토 및 고려 사항을 포함합니다.
- 침투 테스트 결과 및 수정 작업 결과를 보존하도록 지정합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 CREST 인증 테스터를 사용하는 OWASP(Open Web Application Security Project) 상위 10개 항목을 기반으로 하여 타사 침투 테스트를 통해 서비스의 유효성을 검사합니다. 테스트 결과는 보안 사례를 준수하는지 확인하기 위해 정기적으로 감사 및 검토되는 위험 목록을 통해 추적됩니다. <br /><br />또한 Microsoft는 Microsoft에서 관리하는 인프라, 서비스 및 응용 프로그램에 대해 레드 팀을 사용합니다. 최종 고객 데이터는 레드 팀 및 라이브 사이트 침투 테스트 중에 의도적으로 대상으로 지정되지 않습니다. Microsoft Azure 인프라 및 플랫폼뿐만 아니라 Microsoft 고유의 응용 프로그램 및 데이터에 대해 테스트됩니다. Azure에서 호스팅되는 고객 테넌트, 응용 프로그램 및 데이터는 절대로 대상으로 지정되지 않습니다.<br /><br />Microsoft Azure는 독립적인 평가자를 채택하여 시스템 평가 계획을 개발하고 제어 평가를 수행했습니다. 제어 평가는 매년 수행되며, 결과는 관련 당사자에게 보고됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 '있는 그대로'의 활동으로 침투 테스트를 통해 취약성을 검사했습니다. 침투 테스트 결과는 nmap 또는 pentest-tools.com과 같은 공통 도구를 사용하여 복제할 수 있습니다. 그리고 결과가 불충분하고 악용할 수 있는 항목이 없는 공격 노출 영역을 제공합니다. 또한 [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공합니다.|



### <a name="pci-dss-requirement-1131"></a>PCI DSS 요구 사항 11.3.1

**11.3.1** 적어도 매년 및 중요한 인프라 또는 응용 프로그램(예: 운영 체제 업그레이드, 환경에 추가된 하위 네트워크 또는 환경에 추가된 웹 서버)을 업그레이드하거나 수정한 후에 *외부* 침투 테스트를 수행합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 11.3](#pci-dss-requirement-11-3) "Microsoft Azure" 섹션 참조 |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 '있는 그대로'의 활동으로 침투 테스트를 통해 취약성을 검사했습니다. 침투 테스트 결과는 nmap 또는 pentest-tools.com과 같은 공통 도구를 사용하여 복제할 수 있습니다. 그리고 결과가 불충분하고 악용할 수 있는 항목이 없는 공격 노출 영역을 제공합니다. 또한 [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공합니다.|



### <a name="pci-dss-requirement-1132"></a>PCI DSS 요구 사항 11.3.2

**11.3.2** 적어도 매년 및 중요한 인프라 또는 응용 프로그램(예: 운영 체제 업그레이드, 환경에 추가된 하위 네트워크 또는 환경에 추가된 웹 서버)을 업그레이드하거나 수정한 후에 *내부* 침투 테스트를 수행합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 독립적인 평가자와 계약하여 Microsoft Azure 경계의 침투 테스트를 수행합니다. 레드 팀 연습도 정기적으로 수행되며 보안 향상을 위해 사용됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 '있는 그대로'의 활동으로 침투 테스트를 통해 취약성을 검사했습니다. 침투 테스트 결과는 nmap 또는 pentest-tools.com과 같은 공통 도구를 사용하여 복제할 수 있습니다. 그리고 결과가 불충분하고 악용할 수 있는 항목이 없는 공격 노출 영역을 제공합니다. 또한 [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공합니다.|



### <a name="pci-dss-requirement-1133"></a>PCI DSS 요구 사항 11.3.3

**11.3.3** 침투 테스트 중에 발견된 악용할 수 있는 취약성을 수정하고, 테스트를 반복하여 수정 사항을 확인합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 알려진 보안 취약성에 대해 Microsoft Azure 플랫폼 구성 요소를 모니터링하기 위한 절차가 수립되었습니다. <br /><br /><br /><br />우선 순위가 지정된 Azure 프로덕션 환경의 구성 요소에 대해 각 분기마다 목표로 하는 광범위한 보안 취약성 검사가 수행되어 보안 취약성을 식별합니다. 결과는 이해 관계자에게 보고되고, 수정은 NDA(비밀 유지 계약)를 통해 팀에 의해 추적됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공하여 Contoso Webstore 데모 CDE에 대한 모든 미해결 문제가 수정되었는지 확인했습니다.|



### <a name="pci-dss-requirement-1134"></a>PCI DSS 요구 사항 11.3.4

**11.3.4** 조각화를 사용하여 다른 네트워크에서 CDE를 격리하는 경우, 적어도 매년 및 조각화 제어/방법을 변경한 후에 침투 테스트를 수행하여 조각화 방법이 작동하고 효과적인지 확인하고 CDE의 시스템에서 범위 외부의 모든 시스템을 격리합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 알려진 보안 취약성에 대해 Microsoft Azure 플랫폼 구성 요소를 모니터링하기 위한 절차가 수립되었습니다. <br /><br /><br /><br />우선 순위가 지정된 Azure 프로덕션 환경의 구성 요소에 대해 각 분기마다 목표로 하는 광범위한 보안 취약성 검사가 수행되어 보안 취약성을 식별합니다. 결과는 이해 관계자에게 보고되고, 수정은 NDA(비밀 유지 계약)를 통해 팀에 의해 추적됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공하여 Contoso Webstore 데모 CDE에 대한 모든 미해결 문제가 수정되었는지 확인했습니다.|



### <a name="pci-dss-requirement-11341"></a>PCI DSS 요구 사항 11.3.4.1

**11.3.4.1** *서비스 공급자에만 해당하는 추가 요구 사항:* 조각화를 사용하는 경우, 적어도 매6개월 및 조각화 제어/방법 변경 후에 조각화 제어에 대한 침투 테스트를 수행하여 PCI DSS 범위를 확인합니다.

> [!NOTE]
> 이 요구 사항은 2018년 1월 31일까지 모범 사례이고, 이후에는 요구 사항이 됩니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 11.3.4](#pci-dss-requirement-11-3-4) "Microsoft Azure" 섹션 참조 |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 취약성 정보 및 수정 사항을 제공하여 Contoso Webstore 데모 CDE에 대한 모든 미해결 문제가 수정되었는지 확인했습니다.|



## <a name="pci-dss-requirement-114"></a>PCI DSS 요구 사항 11.4

**11.4** 침입 탐지 및/또는 침입 방지 기술을 사용하여 네트워크 침입을 탐지 및/또는 방지합니다. 카드 소유자 데이터 환경의 주변 지점 및 중요 지점의 모든 트래픽을 모니터링하고 의심되는 손상에 대해 담당자에게 경고합니다.
모든 침입 탐지 및 방지 엔진, 기준 및 서명을 최신 상태로 유지합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 운영 환경 내에서 이벤트의 실시간 분석을 수행하고, IDS 시스템에서는 잠재적으로 시스템이 손상될 수 있는 이벤트에 대해 거의 실시간으로 경고를 생성합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 서비스이고, 네트워크 침입 탐지 및 방지는 Azure의 책임입니다. [Azure Security Center](https://azure.microsoft.com/services/security-center/) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 침입 경고 및 수정 사항을 제공합니다.|



## <a name="pci-dss-requirement-115"></a>PCI DSS 요구 사항 11.5

**11.5** 중요한 시스템 파일, 구성 파일 또는 콘텐츠 파일에 대한 무단 수정(변경, 추가 및 삭제 포함)을 담당자에게 경고하는 변경 탐지 메커니즘(예: 파일 무결성 모니터링 도구)을 배포하고, 적어도 매주 중요한 파일 비교를 수행하도록 소프트웨어를 구성합니다. 

> [!NOTE]
> 변경 탐지를 위해 중요한 파일은 일반적으로 정기적으로 변경되지 않지만, 시스템 손상 또는 손상 위험을 나타낼 수 있는 수정 사항입니다. 파일 무결성 모니터링 제품과 같은 변경 탐지 메커니즘은 일반적으로 관련 운영 체제의 중요한 파일로 미리 구성되어 제공됩니다. 사용자 지정 응용 프로그램과 같이 중요한 다른 파일은 회사(즉, 판매자 또는 서비스 공급자)가 평가하고 정의해야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 온라인 서비스 대시보드를 통해 서비스의 보안 또는 가용성에 영향을 줄 수 있는 잠재적인 변경 및 이벤트를 유지하고 고객에게 알려줍니다. Microsoft Azure 고객에 대한 보안 약속 및 보안 의무에 대한 변경 내용은 Microsoft Azure 웹 사이트에서 시기 적절하게 업데이트됩니다.<br /><br />권한이 있는 관리 담당자만 Microsoft Azure 프로덕션 환경에서 소프트웨어를 설치하거나 변경하도록 제한되며, 이 경우 변경 관리 절차를 따릅니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 데모는 PaaS 서비스이고, 변경 탐지는 Log Analytics를 사용하여 구현되었습니다. 자세한 내용은 [PCI 지침 - 미리 설치된 관리 솔루션](payment-processing-blueprint.md#management-solutions)을 참조하세요.<br /><br />|



### <a name="pci-dss-requirement-1151"></a>PCI DSS 요구 사항 11.5.1

**11.5.1** 변경 탐지 솔루션에서 생성된 모든 경고에 응답하는 프로세스를 구현합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure 모니터링 이벤트 규칙은 위험 수준이 높은 작업 및 자산에 대해 향상된 수준의 모니터링을 제공합니다. Azure에서 관리되는 네트워크 장치는 설정된 보안 표준을 준수하는지 모니터링됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 변경에 대한 Contoso Webstore 경고는 Log Analytics 구현에 의해 제공됩니다. 자세한 내용은 [PCI 지침 - 미리 설치된 관리 솔루션](payment-processing-blueprint.md#management-solutions)을 참조하세요.<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>PCI DSS 요구 사항 11.6

**11.6** 보안 모니터링 및 테스트에 대한 보안 정책 및 운영 절차가 문서화되고, 사용되고, 영향을 받는 모든 당사자에게 알려지도록 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 변경에 대한 Contoso Webstore 경고는 Log Analytics 구현에 의해 제공됩니다. 자세한 내용은 [PCI 지침 - 미리 설치된 관리 솔루션](payment-processing-blueprint.md#management-solutions)을 참조하세요.<br /><br /><br /><br />|




