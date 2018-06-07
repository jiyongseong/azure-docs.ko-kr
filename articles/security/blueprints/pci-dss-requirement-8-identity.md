---
title: Azure Payment Processing Blueprint - ID 요구 사항
description: PCI DSS 요구 사항 8
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a83040a6b5174307ea73e5473165835458d217f6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 ID 요구 사항 
## <a name="pci-dss-requirement-8"></a>PCI DSS 요구 사항 8

**시스템 구성 요소에 대한 액세스 식별 및 인증**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

액세스할 수 있는 각 사용자에게 고유한 식별(ID)를 할당하면 각 개별 사용자는 해당 작업에서 고유하게 책임을 집니다. 이러한 책임을 사용하면 중요 데이터와 시스템에서 취해진 조치는 알려지고 권한이 부여된 사용자 및 프로세스별로 수행되고 추적될 수 있습니다.
암호의 유효성은 인증 시스템의 디자인 및 구현에 의해 크게 좌우됩니다. 특히 공격자에 의한 암호 시도 빈도 및 진입 지점, 전송 중 및 저장 중에 사용자 암호를 보호하는 보안 방법이 여기에 해당합니다.

> [!NOTE]
> 카드 소유자 데이터를 보거나 액세스하고 카드 소유자 데이터를 사용하여 시스템에 액세스하는 데 사용되는 관리 기능 및 모든 계정에서 POS(point-of-sale) 계정을 포함한 모든 계정에 이러한 요구 사항을 적용할 수 있습니다. 여기에는 공급 업체와 기타 타사에서 사용하는 계정(예: 지원 또는 유지 관리 목적)이 포함됩니다. 고객(예: cardholders)이 사용하는 계정에는 이러한 요구 사항을 적용하지 않습니다. 그러나 요구 사항 8.1.1, 8.2, 8.5, 8.2.3~8.2.5, 8.1.6~8.1.8은 단일 트랜잭션을 용이하게 하기 위해 한 번에 하나의 카드 번호에 대한 액세스 권한만을 갖는 POS(point-of-sale) 지불 응용 프로그램 내의 사용자 계정에 적용하기에는 적합하지 않습니다(예: 계산원 계정).
 
## <a name="pci-dss-requirement-81"></a>PCI DSS 요구 사항 8.1

**8.1** 다음과 같이 모든 시스템 구성 요소에서 소비자가 아닌 사용자와 관리자에게 적절한 사용자 식별 관리를 보장하는 정책 및 절차를 정의하고 구현합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 샘플 배포에서 관리자가 올바르게 사용하는 방법에 대한 사용 사례 및 설명을 제공합니다.|



### <a name="pci-dss-requirement-811"></a>PCI DSS 요구 사항 8.1.1

**8.1.1** 사용자가 시스템 구성 요소 또는 카드 소유자 데이터에 액세스하도록 허용하기 전에 고유 ID를 할당합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 사용자가 고유 ID를 갖도록 Azure Active Directory 및 Azure Active Directory RBAC(역할 기반 액세스 제어)를 구현합니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)를 참조하세요.<br /><br />|



### <a name="pci-dss-requirement-812"></a>PCI DSS 요구 사항 8.1.2

**8.1.2** 사용자 ID, 자격 증명 및 기타 식별자 개체를 추가, 삭제 및 수정하도록 제어합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 사용자가 고유 ID를 갖도록 Azure Active Directory 및 Azure Active Directory RBAC(역할 기반 액세스 제어)를 구현합니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)를 참조하세요.<br /><br />|



### <a name="pci-dss-requirement-813"></a>PCI DSS 요구 사항 8.1.3

**8.1.3** 해지된 사용자에 대한 액세스 권한을 즉시 종료합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 사용자 관리를 위해 Azure Active Directory를 활용합니다. 사용자의 해지는 Active Directory에서 수행할 수 있습니다.|



### <a name="pci-dss-requirement-814"></a>PCI DSS 요구 사항 8.1.4

**8.1.4** 비활성 사용자 계정을 90일 이내에 제거하거나 해제합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 사용자 관리를 위해 Azure Active Directory를 사용합니다. 암호가 90일 이내에 만료되도록 `-enableADDomainPasswordPolicy` 옵션을 설정할 수 있습니다.|



### <a name="pci-dss-requirement-815"></a>PCI DSS 요구 사항 8.1.5

**8.1.5** 타사에서 사용되는 ID를 관리하여 다음과 같이 원격 액세스를 통해 시스템 구성 요소에 액세스하거나, 지원하거나, 유지 관리합니다.
- 필요한 기간 동안만 활성화되고 사용하지 않을 때는 비활성화됩니다.
- 사용 중일 때 모니터링됩니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 정보 보안 정책을 비롯하여 적용 가능한 기업 및 조직 보안 정책을 채택했습니다. 정책이 Microsoft Azure에 승인되고, 게시되고, 전달되었습니다. 정보 보안 정책은 자산 소유자 권한을 사용하여 비즈니스 근거에 따라 Microsoft Azure 자산에 대한 액세스를 부여하고 "알아야 할 필요" 및 "최소 권한" 원칙에 따라 제한해야 합니다. 또한 정책은 액세스 프로비전, 인증, 액세스 권한 부여, 액세스 권한 제거 및 정기적 액세스 검토를 비롯한 액세스 관리 수명 주기에 대한 요구 사항을 해결합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 데모는 Azure Active Directory 및 Azure Active Directory 역할 기반 액세스 제어를 구현하여 설치에 대한 사용자 액세스 권한을 관리했습니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)를 참조하세요.<br /><br />|



### <a name="pci-dss-requirement-816"></a>PCI DSS 요구 사항 8.1.6

**8.1.6** 6번이 넘지 않게 시도한 후에 사용자 ID를 잠금으로써 반복되는 액세스 시도를 제한합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 데모의 모든 사용자에 대한 SOD(의무 분리)를 명확하게 구현했습니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)의 "Azure Active Directory Identity Protection"을 참조하세요.|



### <a name="pci-dss-requirement-817"></a>PCI DSS 요구 사항 8.1.7

**8.1.7** 잠금 기간을 최소 30분 또는 관리자가 사용자 ID를 사용하도록 설정할 때까지로 설정합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 PCI DSS 요구 사항을 준수하는 암호 정책을 생성하고, 적용하고, 모니터링하는 일을 담당합니다.|



### <a name="pci-dss-requirement-818"></a>PCI DSS 요구 사항 8.1.8

**8.1.8** 세션이 15분 동안 유휴 상태였을 경우 사용자가 터미널 또는 세션을 다시 활성화하기 위해 다시 인증해야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 PCI DSS 요구 사항을 준수하는 암호 정책을 생성하고, 적용하고, 모니터링하는 일을 담당합니다.|



## <a name="pci-dss-requirement-82"></a>PCI DSS 요구 사항 8.2

**8.2** 고유 ID를 할당하는 것 외에도 모든 사용자를 인증하기 위해 다음 방법 중 하나 이상을 사용하여 모든 시스템 구성 요소에서 소비자가 아닌 사용자 및 관리자에 대해 적절하게 사용자 인증 관리하도록 합니다.
- 암호 또는 암호 등 사용자가 아는 항목
- 토큰 장치 또는 스마트 카드 등 사용자가 가진 항목
- 생체 인식 등 사용자 특징

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Multi-Factor Authentication을 위해 Contoso Webstore를 구현하지 않도록 비활성화되어 데모에 대한 사용 편의성을 제공합니다. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)을 사용하여 Multi-Factor Authentication을 구현할 수 있습니다.|



### <a name="pci-dss-requirement-821"></a>PCI DSS 요구 사항 8.2.1

**8.2.1** 강력한 암호화를 사용하여 모든 시스템 구성 요소에 전송 또는 저장하는 동안 읽을 수 없는 모든 인증 자격 증명(예: 암호/구)을 렌더링합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure에서는 해당 수명 주기 전체에서 암호화 키를 관리하도록 키 관리 절차를 설정했습니다(예: 생성, 배포, 해지). Microsoft Azure에서는 Microsoft의 회사 PKI 인프라를 사용합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 가이드에서 설명한 강력한 암호를 적용합니다. 자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.<br /><br />|



### <a name="pci-dss-requirement-822"></a>PCI DSS 요구 사항 8.2.2

**8.2.2** 암호 재설정을 수행하거나, 새 토큰을 프로비전하거나, 새 키를 생성하는 등 인증 자격 증명을 수정하기 전에 사용자 ID를 확인합니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure에서는 해당 수명 주기 전체에서 암호화 키를 관리하도록 키 관리 절차를 설정했습니다(예: 생성, 배포, 해지). Microsoft Azure에서는 Microsoft의 회사 PKI 인프라를 사용합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 가이드에서 설명한 강력한 암호를 적용합니다. 자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-823"></a>PCI DSS 요구 사항 8.2.3

**8.2.3** 암호는 다음 항목을 충족해야 합니다.
- 최소한 길이가 7자여야 합니다.
- 숫자와 문자를 모두 포함합니다.
또는 암호에는 위에 지정된 매개 변수에 해당할 만큼 복잡하고 강력해야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 가이드에서 설명한 강력한 암호를 적용합니다.|



### <a name="pci-dss-requirement-824"></a>PCI DSS 요구 사항 8.2.4

**8.2.4** 사용자 암호를 90일마다 한 번 이상 변경합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 사용자 관리를 위해 Azure Active Directory를 사용합니다. 암호가 90일마다 한 번 이상 만료되도록 `-enableADDomainPasswordPolicy` 옵션을 설정할 수 있습니다.|



### <a name="pci-dss-requirement-825"></a>PCI DSS 요구 사항 8.2.5

**8.2.5** 개인 사용자는 누군가가 사용하는 최근 4개의 암호와 동일한 새 암호를 전송할 수 없습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 가이드에서 설명한 강력한 암호를 적용합니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)를 참조하세요.<br /><br />|



### <a name="pci-dss-requirement-826"></a>PCI DSS 요구 사항 8.2.6

**8.2.6** 처음으로 사용할 암호를 설정한 후에, 각 사용자에게 고유한 값으로 다시 설정하고, 처음 사용한 후 즉시 변경합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 가이드에서 설명한 강력한 암호를 적용합니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)를 참조하세요.<br /><br />|



## <a name="pci-dss-requirement-83"></a>PCI DSS 요구 사항 8.3

**8.3** 다단계 인증을 사용하여 모든 개별 비 콘솔 관리 액세스 및 CDE(카드 소유자 데이터 환경)에 대한 모든 원격 액세스를 보호합니다.

> [!NOTE]
> 다단계 인증은 인증하기 위해 최소한 두 가지 인증 방법(인증 방법에 대한 설명은 요구 사항 8.2 참조)을 사용해야 합니다. 1단계를 두 번 사용하는 것(예: 두 개의 별도 암호 사용)은 다단계 인증으로 간주되지 않습니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure 관리자가 Azure 시스템 및 서버를 유지 관리하고 관리하는 경우 액세스하기 위해 다단계 인증을 사용해야 합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중에 admin, sqladmin 및 edna(데모 실행 중에 웹앱에 로그인한 기본 사용자)라는 세 개의 계정을 만듭니다. 다단계 인증은 데모에서 구현되지 않습니다.|



### <a name="pci-dss-requirement-831"></a>PCI DSS 요구 사항 8.3.1

**8.3.1** 관리 액세스를 사용하여 직원의 CDE에 대한 모든 콘솔이 아닌 액세스에 다단계 인증을 통합합니다.

> [!NOTE]
> 이 요구 사항은 2018년 1월 31일까지 모범 사례이고, 이후에는 요구 사항이 됩니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure 관리자가 Azure 시스템 및 서버를 유지 관리하고 관리하는 경우 액세스하기 위해 다단계 인증을 사용해야 합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중에 admin, sqladmin 및 edna(데모 실행 중에 웹앱에 로그인한 기본 사용자)라는 세 개의 계정을 만듭니다. 다단계 인증은 데모에서 구현되지 않습니다.|



### <a name="pci-dss-requirement-832"></a>PCI DSS 요구 사항 8.3.2

**8.3.2** 엔터티의 네트워크 외부에서 발생한 모든 원격 네트워크 액세스(사용자 및 관리자 모두, 지원 또는 유지 관리를 위한 타사 액세스 포함)에 다단계 인증을 통합합니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure 관리자가 Azure 시스템 및 서버를 유지 관리하고 관리하는 경우 액세스하기 위해 다단계 인증을 사용해야 합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중에 admin, sqladmin 및 edna(데모 실행 중에 웹앱에 로그인한 기본 사용자)라는 세 개의 계정을 만듭니다. 다단계 인증은 데모에서 구현되지 않습니다.|



## <a name="pci-dss-requirement-84"></a>PCI DSS 요구 사항 8.4

**8.4** 다음을 비롯한 모든 사용자에게 인증 정책 및 절차를 문서화하고 전달합니다.
- 강력한 인증 자격 증명을 선택하는 지침
- 사용자가 해당 인증 자격 증명을 보호하는 방법에 대한 지침
- 이전에 사용한 암호를 다시 사용하지 않는 지침
- 암호가 손상될 수 있는 경우 암호를 변경하는 지침

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 지침을 따르고 모든 사용자에게 인증 절차 및 정책을 문서화하고 전달하는 역할을 담당합니다.|



## <a name="pci-dss-requirement-85"></a>PCI DSS 요구 사항 8.5

**8.5** 다음과 같이 그룹 ID, 공유 ID 일반 ID, 암호 또는 다른 인증 방법을 사용하지 마십시오.
- 일반 사용자 ID는 비활성화되거나 제거됩니다.
- 공유 사용자 ID는 시스템 관리 및 기타 중요 기능에 존재하지 않습니다.
- 공유 및 일반 사용자 ID는 시스템 구성 요소를 관리하는 데 사용되지 않습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중에 admin, sqladmin 및 edna(데모 실행 중에 웹앱에 로그인한 기본 사용자)라는 세 개의 계정을 만듭니다. 다단계 인증은 데모에서 구현되지 않습니다.|



### <a name="pci-dss-requirement-851"></a>PCI DSS 요구 사항 8.5.1

**8.5.1** **서비스 공급자 전용 추가 요구 사항:** 고객 프레미스(예: POS 시스템 또는 서버 지원)에 대한 원격 액세스 권한을 갖는 서비스 공급자는 각 고객에게 고유한 인증 자격 증명(암호 등)을 사용해야 합니다. 

> [!NOTE]
> 이 요구 사항은 고유한 호스팅 환경에 액세스하는 공유 호스팅 공급자에 적용하는 데 적합하지 않습니다. 여기서는 여러 고객 환경이 호스팅됩니다.

**책임:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 고객에 적용되지 않습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Microsoft Azure 고객에 적용되지 않습니다.|



## <a name="pci-dss-requirement-86"></a>PCI DSS 요구 사항 8.6

**8.6** 다른 인증 메커니즘을 사용하는 경우(예: 실제 또는 논리 보안 토큰, 스마트 카드, 인증서 등) 이러한 인증 메커니즘을 사용하도록 다음과 같이 할당되어야 합니다.
- 인증 메커니즘은 개별 계정에 할당되고 여러 계정 간에 공유되지 않아야 합니다.
- 물리적 및/또는 논리 컨트롤은 원래 사용하려던 계정만이 액세스 권한을 얻기 위해 해당 메커니즘을 사용할 수 있도록 해야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중에 admin, sqladmin 및 edna(데모 실행 중에 웹앱에 로그인한 기본 사용자)라는 세 개의 계정을 만듭니다. 다단계 인증은 데모에서 구현되지 않습니다. 모든 액세스는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 통해 관리됩니다. 해당 기능은 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. |



## <a name="pci-dss-requirement-87"></a>PCI DSS 요구 사항 8.7

**8.7** 카드 소유자 데이터를 포함하는 데이터베이스에 대한 모든 액세스(응용 프로그램, 관리자 및 다른 모든 사용자에 의한 액세스 포함)는 다음과 같이 제한됩니다.
- 모든 사용자 액세스, 사용자 쿼리 및 데이터베이스에 대한 사용자 조치는 프로그래밍 방식을 통해 수행됩니다.
- 데이터베이스 관리자만이 데이터베이스를 직접 액세스하거나 쿼리할 수 있습니다.
- 데이터베이스 응용 프로그램에 대한 응용 프로그램 ID는 해당 응용 프로그램에서만 사용할 수 있습니다(다른 개별 사용자 또는 응용 프로그램이 아닌 프로세스는 불가).

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 사용하여 모든 카드 소유자 데이터를 보호하고 레코드의 암호화는 배포 설명서에 나와 있습니다. 자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.<br /><br />|



## <a name="pci-dss-requirement-88"></a>PCI DSS 요구 사항 8.8

**8.8** 식별 및 인증을 위한 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 식별 및 인증을 위한 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 하는 작업을 담당합니다.|




