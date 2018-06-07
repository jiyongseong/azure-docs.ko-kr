---
title: Azure Payment Processing Blueprint - 시스템 보호 요구 사항
description: PCI DSS 요구 사항 6
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2f6f1bfc853f261eecf5357cef5d3e3d972781b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 시스템 보호 요구 사항 
## <a name="pci-dss-requirement-6"></a>PCI DSS 요구 사항 6

**보안 시스템 및 응용 프로그램 개발 및 유지 관리**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

악의적인 개인은 시스템에 권한이 있는 액세스를 확보하기 위해 보안 취약성을 사용합니다. 많은 취약성은 공급업체가 제공하는 보안 패치로 해결될 수 있으므로 시스템을 관리하는 주체가 이를 설치해야 합니다. 악의적인 개인과 악성 소프트웨어에 의한 카드 소유자 데이터 악용 및 손상을 방지하기 위해 모든 시스템에는 적합한 모든 소프트웨어 패치가 설치되어야 합니다. 

> [!NOTE]
> 적합한 소프트웨어 패치란 패치가 기존 보안 구성과 충돌하지 않음을 판단하기 위해 충분히 평가 및 테스트를 거친 패치를 말합니다. 사내 개발 응용 프로그램의 경우 표준 시스템 개발 프로세스와 보안 코딩 기법을 사용하면 많은 취약성을 방지할 수 있습니다.

## <a name="pci-dss-requirement-61"></a>PCI DSS 요구 사항 6.1

**6.1** 보안 취약성 정보에 대해 신뢰할 수 있는 외부 원본을 사용하여 보안 취약성을 식별하는 프로세스를 수립하고 새로 발견된 보안 취약성에 대해 보안 등급(예: "높음", "중간", "낮음")을 부여합니다.

> [!NOTE]
> 위험 등급은 업계 모범 사례와 잠재적인 영향에 대한 고려를 기준으로 합니다. 예를 들어 취약성 등급 기준에 CVSS 기준 점수, 공급업체의 분류 및/또는 영향 받는 시스템 종류 등이 포함될 수 있습니다. 
> 
> 취약성 평가 및 위험 등급 할당 방법은 조직의 환경과 위험 평가 전략에 따라 달라집니다. 최소한 위험 등급은 환경에 대해 "매우 위험"하다고 판단되는 모든 취약성을 식별해야 합니다. 위험 등급 외에도 취약성이 환경에 중요한 위협이 되거나 중요 시스템에 영향을 미치거나 해결되지 않으면 손상 가능성이 있는 경우 이를 "중요"하다고 판단할 수 있습니다. 중요 시스템에는 보안 시스템, 공용 장치 및 시스템, 데이터베이스, 기타 카드 소유자 데이터를 저장, 처리 또는 전송하는 시스템이 포함될 수 있습니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 범위 경계에서 하이퍼바이저 호스트의 취약성을 검색하는 프로시저가 수립 및 구현되었습니다. 적절한 취약성 검사를 수행하여 서버 운영 체제, 데이터베이스 및 네트워크 장치에 대한 취약성 검사를 구현합니다. 취약성 검사는 분기에 한 번 이상 수행합니다. Microsoft Azure는 Microsoft Azure 경계 침투 테스트를 수행하는 독립 평가사와 계약하고 있습니다. 레드 팀 연습도 정기적으로 수행되며 보안 향상을 위해 사용됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 WAF와의 Application Gateway와 OWASP 규칙 집합을 사용하여 보안 취약성의 위험을 줄입니다. 자세한 내용은 [PCI 지침 - 보안 취약성 위험 완화](payment-processing-blueprint.md#application-gateway)를 참조하세요.|



## <a name="pci-dss-requirement-62"></a>PCI DSS 요구 사항 6.2

**6.2** 가능한 공급업체 제공 보안 패치를 설치하여 모든 시스템 구성 요소와 소프트웨어가 알려진 취약성으로부터 보호되게 합니다. 중요 패치는 릴리스 후 한 달 이내에 설치합니다.

> [!NOTE]
> 중요 보안 패치는 요구 사항 6.1에서 정한 위험 등급 프로세스에 따라 식별해야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 해당하는 공급업체 제공 보안 패치를 설치하여 알려진 취약성으로부터 모든 네트워크 장치 및 하이퍼파이저 OS 소프트웨어를 보호하도록 할 책임이 있습니다. 고객이 서비스를 사용하지 않도록 요청하지 않은 한, 운영 체제 수준 취약성을 시기 적절하게 방지 및 조치하기 위해 패치 관리 프로세스가 존재합니다. 매월 프로덕션 서버를 검색하여 패치 호환성의 유효성을 검사합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 서비스 솔루션입니다. Azure가 모든 서비스 패치 유지 관리를 제공합니다.|



## <a name="pci-dss-requirement-63"></a>PCI DSS 요구 사항 6.3

**6.3** 다음과 같이 안전하게 내부 및 외부 소프트웨어 응용 프로그램(응용 프로그램에 대한 웹 기반 관리 액세스)을 개발합니다.
- PCI DSS(예: 보안 인증 및 로깅)에 따라
- 업계 표준 및/또는 모범 사례에 따라
- 소프트웨어 개발 수명 주기 전체에서 정보 보안 통합 

> [!NOTE]
> 이것은 사내에서 개발한 모든 소프트웨어와, 제3자가 개발한 맞춤식 또는 사용자 지정 소프트웨어에 모두 적용됩니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 응용 프로그램 및 끝점은 DSS 요구 사항에 부합하는 Microsoft SDL(Security Development Lifecycle)에 따라 개발됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 CHD 보호를 위한 업계 모범 사례를 따르도록 설계되었습니다. 배포 지침은 보안 방법의 세부 정보를 제공하며 로깅이 사용됩니다.|



### <a name="pci-dss-requirement-631"></a>PCI DSS 요구 사항 6.3.1

**6.3.1** 응용 프로그램을 활성화하거나 고객에게 배포하기 전에 개발, 테스트 및/또는 사용자 지정 응용 프로그램 계정, 사용자 ID 및 암호를 제거합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 프로덕션 배포에 앞서 중요 릴리스를 위해 Azure 개발 팀 외부의 전담 보안 자문을 통해 FSR(최종 보안 검토)를 수행하여 프로덕션이 가능한 응용 프로그램만 배포되게 합니다. 이 최종 검토의 일환으로 모든 테스트 계정 및 테스트 데이터가 제거되었는지 확인합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 기록 및 격리되는 스테이징 서비스를 제공합니다. 각 네트워크 계층에는 전용 NSG(네트워크 보안 그룹)가 있습니다. 자세한 내용은 [PCI 지침 - 네트워크 보안 그룹](payment-processing-blueprint.md#network-security-groups)을 참조하세요.|



### <a name="pci-dss-requirement-632"></a>PCI DSS 요구 사항 6.3.2

**6.3.2** 프로덕션 또는 고객 릴리스에 앞서 사용자 지정 코드를 검토하여 최소한 다음을 포함하는 잠재적인 코딩 취약성을 식별합니다(수동 또는 자동 프로세스 사용).
- 코드 변경 내용을 원래의 코드 작성자가 아닌 다른 사람과, 코드 검토 기법 및 안전 코딩 실무를 아는 사람이 검토합니다.
- 코드 검토에서는 코드가 안전 코딩 지침에 맞게 개발되었는지 확인합니다.
- 릴리스에 앞서 적합한 수정 조치 구현
- 릴리스에 앞서 경영진의 코드 검토 결과 검토 및 승인 

> [!NOTE]
> 이러한 코드 검토 요구 사항은 시스템 개발 수명 주기의 일환으로 모든 사용자 지정 코드(내부 및 공용 모두)에 적용됩니다. 
>
> 지식을 갖춘 내부 직원이나 제3자가 코드 검토를 수행할 수 있습니다. 공용 웹 응용 프로그램도 PCI DSS 요구 사항 6.6에서 정의한 대로 지속적 위협과 취약성을 해결하기 위해 추가적인 제어의 대상입니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 응용 프로그램 및 끝점은 Microsoft SDL(Security Development Lifecycle) 방법에 따라 개발됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 기록 및 격리되는 스테이징 서비스를 제공합니다. 각 네트워크 계층에는 전용 NSG(네트워크 보안 그룹)가 있습니다. 자세한 내용은 [PCI 지침 - 네트워크 보안 그룹](payment-processing-blueprint.md#network-security-groups)을 참조하세요.|



## <a name="pci-dss-requirement-64"></a>PCI DSS 요구 사항 6.4

**6.4** 시스템 구성 요소에 대한 모든 변경은 변경 제어 프로세스 및 절차에 따릅니다. 이 프로세스는 다음을 포함해야 합니다(요구 사항 6.4.1~6.4.6 참조).

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft는 정보 보안이 시스템 시작부터 SDLC에 통합되어야 하는 소프트웨어 개발 보안 고려 사항에 관한 NIST 지침을 따릅니다. Microsoft SDL에서의 지속적 보안 업무 통합을 통해 다음이 가능합니다.<ul><li>보안 취약성과 구성 오류의 조기 식별 및 완화</li><li>필요한 보안 제어에 따른 가능한 소프트웨어 코딩 과제 인식</li><li>입증된 방법 및 기법을 통해 보안 상태를 개선하는 보안 모범 사례 도구의 재사용 및 공유 보안 서비스 식별</li><li>Microsoft의 포괄적 위험 관리 프로그램 적용</li></ul>Microsoft Azure는 다음과 같은 중요 변경의 구현을 제어하기 위한 변경 및 릴리스 관리 프로세스를 설정했습니다.<ul><li>계획된 변경의 식별 및 문서화</li><li>제품 계획 중 비즈니스 목표, 우선 순위 및 시나리오 식별</li><li>기능/구성 요소 설계 사양</li><li>미리 정의된 기준/체크리스트를 기준으로 운영 준비를 검토하여 전체적인 위험/영향 평가</li><li>DEV(Development, 개발), INT (Integration Testing, 통합 테스트), STAGE(프로덕션 전) 및 PROD(Production, 프로덕션)에 대해 적합하게 진입/종료 기준에 따라 테스트, 권한 부여 및 변경 관리 고객은 Microsoft Azure에서 호스팅되는 자체 응용 프로그램에 대해 책임이 있습니다.</li></ul> |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 기록 및 격리되는 데모를 제공합니다. <br /><br />각 네트워크 계층에는 전용 NSG(네트워크 보안 그룹)가 있습니다. 자세한 내용은 [PCI 지침 - 네트워크 보안 그룹](payment-processing-blueprint.md#network-security-groups)을 참조하세요.<br /><br />변경 내용은 Operations Management Suite를 사용하여 기록되며 Runbook을 사용하여 로그를 수집합니다. Log Analytics는 광범위한 변경 내용 로깅을 제공합니다. 변경을 검토하고 정확도를 확인할 수 있습니다. 자세한 지침은 [PCI 지침 - OMS(Operations Management Suite)](payment-processing-blueprint.md#logging-and-auditing)를 참조하세요.|



### <a name="pci-dss-requirement-641"></a>PCI DSS 요구 사항 6.4.1

**6.4.1** 프로덕션 환경과 개발/테스트 환경을 분리하고 액세스 제어와의 분리를 적용합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 6.4](#pci-dss-requirement-6-4) "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 기록 및 격리되는 스테이징 서비스를 제공합니다. 각 네트워크 계층에는 전용 NSG(네트워크 보안 그룹)가 있습니다. 자세한 내용은 [PCI 지침 - 네트워크 보안 그룹](payment-processing-blueprint.md#network-security-groups)을 참조하세요.|



### <a name="pci-dss-requirement-642"></a>PCI DSS 요구 사항 6.4.2

**6.4.2** 개발/테스트 및 프로덕션 환경 간에 업무 분리

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 6.4](#pci-dss-requirement-6-4) "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 기록 및 격리되는 스테이징 서비스를 제공합니다. 각 네트워크 계층에는 전용 NSG(네트워크 보안 그룹)가 있습니다. 자세한 내용은 [PCI 지침 - 네트워크 보안 그룹](payment-processing-blueprint.md#network-security-groups)을 참조하세요.|



### <a name="pci-dss-requirement-643"></a>PCI DSS 요구 사항 6.4.3

**6.4.3** 프로덕션 데이터(라이브 계획)는 테스트 또는 개발에 사용되지 않습니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 6.4](#pci-dss-requirement-6-4) "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 실제 PAN(기본 계정 번호) 데이터가 없습니다.|



### <a name="pci-dss-requirement-644"></a>PCI DSS 요구 사항 6.4.4

**6.4.4** 시스템이 활성화되거나 프러덕션 상태가 되기 전에 시스템 구성 요소에서 테스트 데이터와 계정을 제거합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 6.4](#pci-dss-requirement-6-4) "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 테스트 계정이 없습니다.|



### <a name="pci-dss-requirement-645"></a>PCI DSS 요구 사항 6.4.5

**6.4.5** 보안 패치 및 소프트웨어 수정의 구현을 귀한 변경 제어 절차는 다음을 포함해야 합니다.
- **6.5.4.1** 영향 문서화
- **6.5.4.2** 권한 있는 당사자에 의한 변경 승인 문서화
- **6.5.4.3** 변경이 시스템 보안에 부정적인 영향을 미치지 않음을 확인하는 기능 테스트
- **6.5.4.4** 철회 절차

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 6.4](#pci-dss-requirement-6-4) "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 서비스 솔루션입니다. Azure가 모든 서비스 패치 유지 관리를 제공합니다.|



### <a name="pci-dss-requirement-646"></a>PCI DSS 요구 사항 6.4.6

**6.4.6** 중요한 변경이 완료되는 시점에 모든 관련 PCI DSS 요구 사항을 모든 새로운 또는 변경된 시스템 및 네트워크에서 실행하고 적합하게 문서를 업데이트해야 합니다.

> [!NOTE]
> 이 요구 사항은 2018년 1월 31일까지 모범 사례이고, 이후에는 요구 사항이 됩니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 6.4](#pci-dss-requirement-6-4) "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 서비스 솔루션입니다. Azure가 모든 서비스 패치 유지 관리를 제공합니다.|



## <a name="pci-dss-requirement-65"></a>PCI DSS 요구 사항 6.5

**6.5** 소프트웨어 개발 프로세스에서 다음과 같이 일반적인 코딩 취약성을 해결합니다.
- 매년 한 번 이상 일반적인 코딩 취약성 방지 방법을 포함한 안전 코딩 기법에 대해 개발자를 교육합니다.
- 안전 코딩 지침에 따라 응용 프로그램을 개발합니다.

> [!NOTE]
> 6.5.1~6.5.10에 열거된 취약성은 이 PCI DSS 버전이 게시된 시점 현재 업계 모범 사례에서 최신입니다. 그러나 취약성 관리에 대한 업계 모범 사례는 업데이트되므로(예: OWASP Guide, SANS CWE Top 25, CERT Secure Coding 등) 해당 요구 사항에 최신 모범 사례를 사용해야 합니다. 
> 
> [!NOTE]
> 아래의 요구 사항 6.5.1~6.5.6은 모든 응용 프로그램(내부 또는 외부)에 적용됩니다. 아래의 요구 사항 6.5.7~6.5.10은 웹 응용 프로그램 및 응용 프로그램 인터페이스(내부 또는 외부)에 적용됩니다. 

- **6.5.1** 주입 결함(특히 SQL 주입). OS 명령 주입, LDAP 및 XPath 주입 결함도 다른 주입 결함과 함께 고려합니다.
- **6.5.2** 버퍼 오버플로
- **6.5.3** 안전하지 않은 암호화 저장
- **6.5.4** 안전하지 않은 통신
- **6.5.5** 부적절한 오류 처리
- **6.5.6** 취약성 식별 프로세스에서 "높은 위험"으로 식별된 모든 취약성(PCI DSS 요구 사항 6.1에서 정의)
- **6.5.7** 사이트 간 스크립팅(XSS)
- **6.5.8** 부적절한 액세스 제어(예: 비보안 직접 개체 참조, URL 액세스 제한 실패, 디렉터리 탐색, 사용자의 기능 액세스 제한 실패)
- **6.5.9** CSRF(교차 사이트 요청 위조)
- **6.5.10** 인증 및 세션 관리 손상

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 6.4](#pci-dss-requirement-6-4) "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 데모는 안전한 개발 업무를 설명하는 안전한 개발, DFD 및 위협 모델에 대한 지침을 제공합니다.|



## <a name="pci-dss-requirement-66"></a>PCI DSS 요구 사항 6.6

**6.6** 공용 웹 응용 프로그램의 경우 다음 방법을 통해 지속적으로 새로운 위협과 취약성을 해결하고 이러한 응용 프로그램을 알려진 공격으로부터 보호할 수 있도록 합니다.

- 매년 한 번 이상 및 모든 변경 후 수동 또는 자동 응용 프로그램 취약성 보안 평가 도구를 사용하여 공용 웹 응용 프로그램 검토. 

   > [!NOTE]
   > 이 평가는 요구 사항 11.2에 대해 수행한 취약성 검색과 다릅니다. 

- 공용 웹 응용 프로그램 앞에 웹 기반 공격을 검색 및 차단하는 자동 기술 솔루션(예: 웹 응용 프로그램 방화벽)을 설치하여 지속적으로 모든 트래픽을 확인합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 응용 프로그램을 프로덕션 환경에 배포하기 전에 SDL 프로세스의 일환으로 공용 웹 응용 프로그램을 테스트합니다. 또한 Microsoft는 정기적으로 프로덕션의 모든 공용 웹 응용 프로그램을 검색하여 가능한 취약성을 탐지합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 참조 솔루션은 WAF와의 Application Gateway와 OWASP 규칙 집합을 사용하여 보안 취약성의 위험을 줄입니다. 자세한 내용은 [PCI 지침 - 보안 취약성 위험 완화](payment-processing-blueprint.md#application-gateway)를 참조하세요.|



## <a name="pci-dss-requirement-67"></a>PCI DSS 요구 사항 6.7

**6.7** 안전한 시스템 및 응용 프로그램의 개발과 유지 관리를 위한 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 참조 솔루션은 WAF와의 Application Gateway와 OWASP 규칙 집합을 사용하여 보안 취약성의 위험을 줄입니다. 자세한 내용은 [PCI 지침 - 보안 취약성 위험 완화](payment-processing-blueprint.md#application-gateway)를 참조하세요.|




