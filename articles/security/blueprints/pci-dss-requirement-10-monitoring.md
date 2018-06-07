---
title: Azure Payment Processing Blueprint - 모니터링 요구 사항
description: PCI DSS 요구 사항 10
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3d26d31c592c34d1e51c3e2c2d6501edefa8dce
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 모니터링 요구 사항 
## <a name="pci-dss-requirement-10"></a>PCI DSS 요구 사항 10

**네트워크 리소스 및 카드 소유자 데이터에 대한 모든 액세스 추적 및 모니터링**

> [!NOTE]
> 이 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(Payment Card Industry) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에서 정의합니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

로깅 메커니즘과 사용자 작업 추적 기능은 데이터 손상 방지, 검색 또는 그 영향의 최소화에 중요합니다. 모든 환경에서 로그가 있기 때문에 무언가 잘못되었을 때 철저한 추적, 경고 및 분석이 가능합니다. 시스템 활동 로그가 없으면 손상 원인의 파악이 불가능하거나 매우 어렵습니다.

## <a name="pci-dss-requirement-101"></a>PCI DSS 요구 사항 10.1

**10.1** 모든 시스템 구성 요소 액세스를 각각의 개별 사용자와 연결하는 감사 추적을 구현합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 관리 및 진단 도구에 대한 액세스를 관련 작업을 담당하는 권한 있는 개인으로 제한합니다. Microsoft Azure는 최소 권한 원칙을 기준으로 프로덕션 환경에서 사용되는 도구에 대한 권한 있는 액세스를 제한합니다. Microsoft Azure는 플랫폼 환경의 Microsoft Azure 시스템 구성 요소에 대한 모든 개인 사용자 액세스 로그를 기록 및 유지 관리합니다.<br /><br />Microsoft Azure 구성 요소(OS, CloudNet, Fabric 및 기타 포함)는 보안 이벤트를 기록 및 수집하도록 구성됩니다. Microsoft Azure 플랫폼의 관리 활동은 기록됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 모든 시스템과 사용자 활동에 대한 광범위한 로깅이 있습니다(CHD 로깅 포함). 자세한 내용은 [PCI 지침 - 로깅](payment-processing-blueprint.md#logging-and-auditing)을 참조하세요.|



## <a name="pci-dss-requirement-102"></a>PCI DSS 요구 사항 10.2

**10.2** 다음 이벤트를 재구성하기 위해 모든 시스템 구성 요소에 대한 자동화된 감사 추적을 구현합니다.
- **10.2.1** 카드 소유자 데이터에 대한 모든 개인 사용자 액세스
- **10.2.2** 루트 또는 관리자 권한이 있는 개인이 수행한 모든 작업
- **10.2.3** 모든 감사 추적에 대한 액세스
- **10.2.4** 잘못된 논리적 액세스 시도
- **10.2.5** 식별 및 인증 메커니즘 사용 및 변경 내용(새 계정 만들기 및 권한 상승을 망라한 여러 항목) 및 루트나 관리 권한이 있는 계정에 대한 모든 변경 내용, 추가 또는 삭제
- **10.2.6** 감사 로그 초기화, 중지 또는 일시 중지
- **10.2.7** 시스템 수준 개체 만들기 및 삭제

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 관리 및 진단 도구에 대한 액세스를 관련 작업을 담당하는 권한 있는 개인으로 제한합니다. Microsoft Azure는 최소 권한 원칙을 기준으로 프로덕션 환경에서 사용되는 도구에 대한 권한 있는 액세스를 제한합니다. Microsoft Azure는 플랫폼 환경의 Microsoft Azure 시스템 구성 요소에 대한 모든 개인 사용자 액세스 로그를 기록 및 유지 관리합니다.<br /><br />Microsoft Azure 구성 요소(OS, CloudNet, Fabric 및 기타 포함)는 보안 이벤트를 기록 및 수집하도록 구성됩니다. Microsoft Azure 플랫폼의 관리 활동은 기록됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 CHD 로깅을 포함하여 모든 시스템 및 사용자 작업에 대한 광범위한 로깅이 있습니다. 자세한 내용은 [PCI 지침 - 로깅](payment-processing-blueprint.md#logging-and-auditing)을 참조하세요.|



## <a name="pci-dss-requirement-103"></a>PCI DSS 요구 사항 10.3

**10.3** 각 이벤트의 모든 시스템 구성 요소에 대해 최소한 다음 감사 추적 항목을 기록합니다.
- **10.3.1** 사용자 ID
- **10.3.2** 이벤트 유형
- **10.3.3** 날짜 및 시간
- **10.3.4** 성공 또는 실패 표시 
- **10.3.5** 이벤트의 발생 위치
- **10.3.6** 영향 받는 데이터, 시스템 구성 요소 또는 리소스의 ID나 이름

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 GPS(Global Positioning System) 위성에 동기화된 NTP Stratum 1 시간 서버에 대해 Microsoft Azure 환경의 서버와 네트워크 장치를 동기화하는 프로시저를 수립했습니다. 동기화는 5분마다 자동으로 수행됩니다. Microsoft Azure는 서비스 호스트가 적합하게 시간을 동기화하도록 할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 10.3 제어의 요구대로 사용자 ID, 이벤트 유형, 날짜 시간 스탬프, 성공 실패 이벤트, 이벤트 원본 및 리소스 이름을 기록합니다.|



## <a name="pci-dss-requirement-104"></a>PCI DSS 요구 사항 10.4

**10.4** 시간-동기화 기술을 사용하여 모든 중요한 시스템 시계 및 시간을 동기화하고 시간 획득, 배포 및 저장에 대해 다음의 구현을 확인합니다. 
> [!NOTE]
> 시간 동기화 기술의 한 예에는 NTP(Network Protocol)가 있습니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 GPS(Global Positioning System) 위성에 동기화된 NTP Stratum 1 시간 서버에 대해 Microsoft Azure 환경의 서버와 네트워크 장치를 동기화하는 프로시저를 수립했습니다. 동기화는 5분마다 자동으로 수행됩니다. Microsoft Azure는 서비스 호스트가 적합하게 시간을 동기화하도록 할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | PaaS 서비스에 대한 시간 동기화는 Azure에서 수행합니다.|



### <a name="pci-dss-requirement-1041"></a>PCI DSS 요구 사항 10.4.1

**10.4.1** 중요 시스템의 시간이 올바르고 정확합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.4](#pci-dss-requirement-10-4)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | PaaS 서비스에 대한 시간 동기화는 Azure에서 수행합니다.|



### <a name="pci-dss-requirement-1042"></a>PCI DSS 요구 사항 10.4.2

**10.4.2** 시간 데이터를 보호합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.4](#pci-dss-requirement-10-4)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | PaaS 서비스에 대한 시간 동기화는 Azure에서 수행합니다.|



### <a name="pci-dss-requirement-1043"></a>PCI DSS 요구 사항 10.4.3

**10.4.3** 시간 설정을 업계에서 인정하는 시간 원본에서 받습니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.4](#pci-dss-requirement-10-4)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | PaaS 서비스에 대한 시간 동기화는 Azure에서 수행합니다.|



## <a name="pci-dss-requirement-105"></a>PCI DSS 요구 사항 10.5

**10.5** 보안 감사를 변경할 수 없게 보호합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | FIM 및 ID 도구는 Microsoft Azure 환경 내에서 구현됩니다. Microsoft Azure는 EWS를 사용하여 운영 환경 내 이벤트의 실시간 분석을 지원합니다. Ma 및 AIMS는 잠재적으로 시스템을 손상시킬 수 있는 이벤트에 대해 실시간에 근접한 경고를 생성합니다. <br /><br />서비스, 사용자 및 보안 이벤트에 대한 로깅(웹 서버 로그, FTP 서버 로그)을 사용하고 중앙 집중식으로 유지합니다. Azure는 직무에 따라 감사 로그 액세스를 권한 있는 개인으로 제한합니다. 이벤트 로그는 Azure의 보안 보관 인프라에 보관되며 180일 동안 유지됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure에 모든 요소에 대한 감사를 제공합니다. 외부 원본에 대한 백업은 [Azure Backup](https://azure.microsoft.com/services/backup/)이 수행할 수 있습니다.|



### <a name="pci-dss-requirement-1051"></a>PCI DSS 요구 사항 10.5.1

**10.5.1** 감사 추적 보기를 업무와 관련하여 필요한 개인으로 제한합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.5](#pci-dss-requirement-10-5)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure에 모든 요소에 대한 감사를 제공합니다. 외부 원본에 대한 백업은 [Azure Backup](https://azure.microsoft.com/services/backup/)이 수행할 수 있습니다.|



### <a name="pci-dss-requirement-1052"></a>PCI DSS 요구 사항 10.5.2

**10.5.2** 감사 추적 파일이 무단으로 수정되지 않게 보호합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.5](#pci-dss-requirement-10-5)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure에 모든 요소에 대한 감사를 제공합니다. 외부 원본에 대한 백업은 [Azure Backup](https://azure.microsoft.com/services/backup/)이 수행할 수 있습니다.|



### <a name="pci-dss-requirement-1053"></a>PCI DSS 요구 사항 10.5.3

**10.5.3** 감사 추적 파일을 변경이 어려운 중앙 집중식 로그 서버나 미디어에 즉시 백업합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.5](#pci-dss-requirement-10-5)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure에 모든 요소에 대한 감사를 제공합니다. 외부 원본에 대한 백업은 [Azure Backup](https://azure.microsoft.com/services/backup/)이 수행할 수 있습니다.|



### <a name="pci-dss-requirement-1054"></a>PCI DSS 요구 사항 10.5.4

**10.5.4** 안전한 중앙 집중식 내부 로그 서버나 미디어 장치에 외부 연결 기술에 대한 로그를 기록합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.5](#pci-dss-requirement-10-5)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure에 모든 요소에 대한 감사를 제공합니다. 외부 원본에 대한 백업은 [Azure Backup](https://azure.microsoft.com/services/backup/)이 수행할 수 있습니다.|



### <a name="pci-dss-requirement-1055"></a>PCI DSS 요구 사항 10.5.5

**10.5.5** 로그에서 파일 무결성 모니터링이나 변경 내용 검색 소프트웨어를 사용하여 기존 로그 데이터가 경고 생성 없이 변경되지 않게 합니다(새 데이터 추가는 경고를 생성하지 않더라도).

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.5](#pci-dss-requirement-10-5)는 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure에 모든 요소에 대한 감사를 제공합니다. 외부 원본에 대한 백업은 [Azure Backup](https://azure.microsoft.com/services/backup/)이 수행할 수 있습니다.|



## <a name="pci-dss-requirement-106"></a>PCI DSS 요구 사항 10.6

**10.6** 모든 시스템 구성 요소에 대해 로그 및 보안 이벤트를 검토하여 예외 또는 의심스러운 활동을 식별합니다.
 
> [!NOTE]
> 로그 수집, 구문 분석 및 경고 도구를 사용하여 이 요구 사항을 충족할 수 있습니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | FIM 및 ID 도구는 Microsoft Azure 환경 내에서 구현됩니다. Microsoft Azure는 EWS를 사용하여 운영 환경 내 이벤트의 실시간 분석을 지원합니다. Ma 및 AIMS는 잠재적으로 시스템을 손상시킬 수 있는 이벤트에 대해 실시간에 근접한 경고를 생성합니다. <br /><br />서비스, 사용자 및 보안 이벤트에 대한 로깅(웹 서버 로그, FTP 서버 로그)을 사용하고 중앙 집중식으로 유지합니다. Azure는 직무에 따라 감사 로그 액세스를 권한 있는 개인으로 제한합니다. 이벤트 로그는 Azure의 보안 보관 인프라에 보관되며 180일 동안 유지됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 사용하여 예외를 모니터링, 보고 및 방지합니다. [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다.|



### <a name="pci-dss-requirement-1061"></a>PCI DSS 요구 사항 10.6.1

**10.6.1** 최소한 다음을 매일 검토합니다.
- 모든 보안 이벤트
- CHD 및/또는 SAD를 저장, 처리 또는 전송하는 모든 시스템 구성 요소의 로그
- 모든 중요 시스템 구성 요소의 로그
- 보안 기능(예: 방화벽, 침입 탐지 시스템/침입 방지 시스템(IDS/IPS), 인증 서버, 전자상거래 리디렉션 서버 등)을 수행하는 모든 서버 및 시스템 로그

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.6](#pci-dss-requirement-10-6)은 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 사용하여 예외를 모니터링, 보고 및 방지합니다. [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다.|



### <a name="pci-dss-requirement-1062"></a>PCI DSS 요구 사항 10.6.2

**10.6.2** 조직의 연간 위험 평가에서 정한 바에 따라 조직의 정책과 위험 관리 전략을 기준으로 모든 다른 시스템 구성 요소의 로그를 검토합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.6](#pci-dss-requirement-10-6)은 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 사용하여 예외를 모니터링, 보고 및 방지합니다. [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다.|



### <a name="pci-dss-requirement-1063"></a>PCI DSS 요구 사항 10.6.3

**10.6.3** 검토 프로세스 중에 확인된 예외와 비정상 부분에 대해 후속 조치를 수행합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | [요구 사항 10.6](#pci-dss-requirement-10-6)은 "Microsoft Azure" 섹션을 참조하세요. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 사용하여 예외를 모니터링, 보고 및 방지합니다. [Azure Advisor](/azure/advisor/advisor-security-recommendations)는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다.|



## <a name="pci-dss-requirement-107"></a>PCI DSS 요구 사항 10.7

**10.7** 최소 1년의 감사 추적 내역을 유지합니다. 최소 3개월은 즉시 분석에 사용할 수 있어야 합니다(예: 온라인, 보관 및 백업에서 복원 가능).

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 최근 3개월을 내부 포털에서 즉시 액세스 가능한 상태로 1년 동안 감사 로그를 유지합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 모든 시스템과 사용자 활동에 대한 광범위한 로깅이 있습니다(CHD 로깅 포함). 자세한 내용은 [PCI 지침 - 로깅](payment-processing-blueprint.md#logging-and-auditing)을 참조하세요.|



## <a name="pci-dss-requirement-108"></a>PCI DSS 요구 사항 10.8

**10.8**  **서비스 공급자 전용 추가 요구 사항:** 다음을 망라하여 중요 보안 제어 시스템의 시기 적절한 검색 및 보고를 위한 프로세스를 구현합니다.
- 방화벽
- IDS/IPS
- FIM
- 바이러스 백신
- 물리적 액세스 제어
- 논리적 액세스 제어
- 감사 로깅 메커니즘
- (사용하는 경우) 구분 제어 

> [!NOTE]
> 이 요구 사항은 2018년 1월 31일까지 모범 사례이고, 이후에는 요구 사항이 됩니다.



**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 EWS를 사용하여 운영 환경 내 이벤트의 실시간 분석을 지원합니다. Ma 및 AIMS는 잠재적으로 시스템을 손상시킬 수 있는 이벤트에 대해 실시간에 근접한 경고를 생성합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 모든 시스템과 사용자 활동에 대한 광범위한 로깅이 있습니다(CHD 로깅 포함). 자세한 내용은 [PCI 지침 - 로깅](payment-processing-blueprint.md#logging-and-auditing)을 참조하세요.|



### <a name="pci-dss-requirement-1081"></a>PCI DSS 요구 사항 10.8.1

**10.8.1** **서비스 공급자 전용 추가 요구 사항:** 시기 적절한 방식으로 중요 보안 제어의 실패에 대처합니다. 보안 제어의 오류에 대처하기 위한 프로세스는 다음을 포함해야 합니다.
- 보안 기능 복원
- 보안 실패 기간(시작에서 종료까지의 날짜와 시간) 식별 및 문서화
- 근본 원인을 포함한 실패 원인 식별 및 문서화와, 근본 원인 해결을 위해 필요한 조치 문서화
- 실패 중 발생한 보안 문제의 식별 및 해결
- 보안 실패의 결과로 추가적인 조치가 필요한지 여부를 판단하기 위한 위험 평가 수행
- 실패 원인의 반복을 방지하기 위한 제어 구현 - 보안 제어의 모니터링 계속 

> [!NOTE]
> 이 요구 사항은 2018년 1월 31일까지 모범 사례이고, 이후에는 요구 사항이 됩니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 EWS를 사용하여 운영 환경 내 이벤트의 실시간 분석을 지원합니다. Ma 및 AIMS는 잠재적으로 시스템을 손상시킬 수 있는 이벤트에 대해 실시간에 근접한 경고를 생성합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 모든 시스템과 사용자 활동에 대한 광범위한 로깅이 있습니다(CHD 로깅 포함). 자세한 내용은 [PCI 지침 - 로깅](payment-processing-blueprint.md#logging-and-auditing)을 참조하세요.|



## <a name="pci-dss-requirement-109"></a>PCI DSS 요구 사항 10.9

**10.9** 네트워크 리소스 및 카드 소유자 데이터에 대한 모든 액세스를 모니터링하기 위해 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 합니다.


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 CHD가 관리 및 보호되는 방식에 대한 사용 사례와 설명을 제공합니다.|




