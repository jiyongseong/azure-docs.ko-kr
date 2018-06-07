---
title: Azure Payment Processing Blueprint - 암호 요구 사항
description: PCI DSS 요구 사항 2
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3ca83294702a138713f31e34b0e94b1ccb87be0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 암호 요구 사항 
## <a name="pci-dss-requirement-2"></a>PCI DSS 요구 사항 2

**시스템 암호 및 기타 보안 매개 변수에 공급 업체에서 제공하는 기본값을 사용하지 마십시오.**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

악의적 사용자(개체 외부 및 내부)가 종종 공급업체 기본 암호 및 기타 공급업체 기본 설정을 사용하여 시스템을 손상시킵니다. 이러한 암호와 설정은 해커 커뮤니티에서 잘 알려져 있으며 공개 정보를 통해 쉽게 파악할 수 있습니다.

## <a name="pci-dss-requirement-21"></a>PCI DSS 요구 사항 2.1
 
**2.1** 시스템을 네트워크에 설치하기 **전에** 항상 공급업체 제공 기본값을 변경하고 불필요한 기본 계정을 제거하거나 비활성화합니다.
이것은 운영 체제, 보안 서비스 제공 소프트웨어, 응용 프로그램 및 시스템 계정, POS(Point-of-sale) 터미널에서 사용하는 암호, SNMP(Simple Network Management Protocol) 커뮤니티 문자열을 망라한 모든 기본 암호에 적용됩니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | AADUX 포털 내에서 고객이 제공한 새 암호에 대해 Microsoft Azure Active Directory 암호 정책 요구 사항을 적용합니다. 고객이 시작한 셀프 서비스 암호 벼경에는 사전 암호 유효성 검사가 필요합니다. 관리자 재설정 암호는 다음 번 로그인에서 변경해야 합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에서는 모든 사용자에 대해 강력한 암호를 설정하도록 요구합니다. 데모에서 샘플 또는 게스트 계정을 사용하지 않습니다.<br /><br />무선 및 SNMP는 솔루션에서 구현되지 않습니다.|



### <a name="pci-dss-requirement-211"></a>PCI DSS 요구 사항 2.1.1

**2.1.1** 카드 소유자 데이터 환경에 연결되었거나 카드 소유자 데이터를 전송하는 무선 환경의 경우 설치 시 모든 무선 공급업체 기본값을 변경합니다. 여기에는 기본 무선 암호화 키, 암호, SNMP 커뮤니티 문자열을 망라한 모든 항목이 포함됩니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 무선 및 SNMP는 솔루션에서 구현되지 않습니다.|



## <a name="pci-dss-requirement-22"></a>PCI DSS 요구 사항 2.2

**2.2** 모든 시스템 구성 요소에 대한 구성 표준을 개발합니다. 이러한 표준이 모든 알려진 보안 취약성을 해결하며 업계 인정 시스템 강화 표준에 부합하는지 확인합니다.
업계 인정 시스템 강화 표준의 출처에는 다음을 망라한 여러 기관이 포함됩니다.
- CIS(인터넷 보안 센터)
- ISO(국제 표준화 기구)
- SANS(SysAdmin Audit Network Security Institute)
- NIST(National Institute of Standards)

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure의 경우 OSSC 기술 보안 서비스 팀이 업계 승인 강화 표준에 부합하는 Microsoft Azure 환경 내 시스템을 위한 보안 구성 표준을 개발하고 있습니다. 이러한 구성은 시스템 기준에서 문서화되며 관련 구성 변경을 해당 팀에게 알립니다(예: IPAK 팀). 보안 구성 표준에 대해 규정 준수를 모니터링하기 위한 절차가 구현됩니다. Microsoft Azure 환경의 시스템에 대해 보안 구성 표준이 업계 승인 강화 표준에 부합하며 매년 한 번 이상 검토됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 CDE(카드 소유자 데이터 환경)에 대해 범위 내 모든 서비스의 강화를 제공합니다. <br /><br />또한 Contoso Webstore는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 배포하여 모든 Azure 리소스의 보안 상태에 대한 중앙화된 보기를 제공합니다. 적합한 보안 제어를 구현하고 올바르게 구성했는지 한눈에 확인하고 주의가 필요한 리소스를 신속히 확인할 수 있습니다.<br /><br />Contoso Webstore는 Operations Management Suite를 사용하여 모든 시스템 변경 내용을 기록합니다. Log Analytics는 광범위한 변경 내용 로깅을 제공합니다. 변경을 검토하고 정확도를 확인할 수 있습니다. 보다 구체적인 지침은 [PCI 지침 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)를 참조하세요.|



### <a name="pci-dss-requirement-221"></a>PCI DSS 요구 사항 2.2.1

**2.2.1** 서버당 하나의 기본 기능만 구현하여 서로 다른 보안 수준이 필요한 기능이 같은 서버에 동시에 존재하지 않게 합니다 (예: 웹 서버, 데이터베이스 서버, DNS는 별도의 서버에 있어야 함). 

> [!NOTE]
> 가상화 기술을 사용 중인 경우 가상 시스템 구성 요소당 하나의 기본 기능을 구현합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 서비스로 배포됩니다. 모든 서비스는 네트워크 구분을 통해 격리 및 분할됩니다.<br /><br />또한 Contoso Webstore는 [ASE(App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro)를 사용하여 키 업무를 적용합니다. 자세한 내용은 [PCI 지침 - App Service Environment](payment-processing-blueprint.md#app-service-environment)를 참조하세요.|



### <a name="pci-dss-requirement-222"></a>PCI DSS 요구 사항 2.2.2

**2.2.2** 시스템의 기능에 대해 필요한 서비스, 프로토콜, 디먼만 사용하도록 설정합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 불필요한 기능, 포트, 프로토콜 및 서비스를 식별하여 제거할 수 있게 Microsoft Azure 소프트웨어 및 하드웨어 구성을 분기에 한 번 이상 검토합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 서비스로 배포됩니다. 모든 서비스는 네트워크 구분을 통해 격리 및 분할됩니다.<br /><br />또한 Contoso Webstore는 [ASE(App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro)를 사용하여 키 업무를 적용합니다. 자세한 내용은 [PCI 지침 - App Service Environment](payment-processing-blueprint.md#app-service-environment)를 참조하세요.|



### <a name="pci-dss-requirement-223"></a>PCI DSS 요구 사항 2.2.3

**2.2.3** 안전하지 않다고 간주되는 필수 서비스, 프로토콜 및 디먼에 대해 추가적인 보안 기능을 구현합니다. 

> [!NOTE]
> SSL/초기 TLS를 사용하는 경우 부록 A2의 요구 사항을 완료해야 합니다.


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 서비스로 배포됩니다. 모든 서비스는 네트워크 구분을 통해 격리 및 분할됩니다. 이 배포에서는 CDE의 범위에서 모든 서비스의 보안 강화를 제공합니다. <br /><br />또한 Contoso Webstore는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 배포하여 모든 Azure 리소스의 보안 상태에 대한 중앙화된 보기를 제공합니다. 적절한 보안 제어가 준비되고 올바르게 구성되었는지와 주의가 필요한 리소스를 한눈에 빠르게 확인할 수 있습니다.<br /><br />또한 Contoso Webstore는 [ASE(App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro)를 사용하여 키 업무를 적용합니다. 자세한 내용은 [PCI 지침 - App Service Environment](payment-processing-blueprint.md#app-service-environment)를 참조하세요.|



### <a name="pci-dss-requirement-224"></a>PCI DSS 요구 사항 2.2.4

**2.2.4** 잘못 사용되지 않게 시스템 보안 매개 변수를 구성합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure에서는 다단계 액세스 제어와 문서화된 비즈니스 요구를 통해 권한이 있는 개인만 Azure 플랫폼 보안 제어를 구성할 수 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 AAD 및 AD RBAC를 사용하여 보안 매개 변수가 올바르게 배포되도록 관리합니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)를 참조하세요.|



### <a name="pci-dss-requirement-225"></a>PCI DSS 요구 사항 2.2.5

**2.2.5** 스크립트, 드라이버, 기능, 하위 시스템, 파일 시스템 및 불필요한 웹 서버 등 모든 불필요한 기능을 제거합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 경계 설정 방법에 대한 설명서를 제공합니다. Contoso의 위협 모델 및 데이터 흐름 다이어그램에서는 사용된 모든 서비스 및 제어를 보여 줍니다.|



## <a name="pci-dss-requirement-23"></a>PCI DSS 요구 사항 2.3

**2.3** 강력한 암호화를 사용하여 모든 비 콘솔 관리 액세스를 암호화합니다. 

> [!NOTE]
> SSL/초기 TLS를 사용할 경우 부록 A2의 요구 사항을 완료해야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure에서는 하이퍼바이저 인프라에 액세스할 때 강력한 암호화의 사용을 적용합니다. 또한 Microsoft Azure는 Microsoft Azure Management Portal을 사용하는 고객이 강력한 암호화를 통해 자신의 서비스/IaaS 콘솔에 액세스할 수 있도록 합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에서는 솔루션에서 강력한 암호화를 구현하는 방법을 보여 줍니다. 또한 모든 테스트를 수행하여 솔루션 전체에서 암호화가 구현되었는지 확인할 수 있습니다.<br /><br />또한 Contoso Webstore는 [ASE(App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro)를 사용하여 키 업무를 적용합니다. 자세한 내용은 [PCI 지침 - App Service Environment](payment-processing-blueprint.md#app-service-environment)를 참조하세요.|



## <a name="pci-dss-requirement-24"></a>PCI DSS 요구 사항 2.4

**2.4** PCI DSS에 대해 범위 안에 있는 시스템 구성 요소의 인벤토리를 유지 관리합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 제공된 설명서에서 Contoso Webstore 데모 PaaS 솔루션 인벤토리를 검토할 수 있습니다. 자세한 내용은 [PCI 지침 - 미리 설치된 관리 솔루션](payment-processing-blueprint.md#management-solutions)을 참조하세요.|



## <a name="pci-dss-requirement-25"></a>PCI DSS 요구 사항 2.5

**2.5** 공급업체 기본값 및 기타 보안 매개 변수 관리에 대한 보안 정책 및 운영 절차가 문서화되고, 사용되고, 영향을 받는 모든 당사자에게 알려지도록 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 보안 매개 변수에 대한 정보를 제공하는 설명서를 제공하고 서비스 요소를 문서화합니다. |



## <a name="pci-dss-requirement-26"></a>PCI DSS 요구 사항 2.6

**2.6** 공유 호스팅 공급자는 각 개체의 호스팅 환경과 카드 소유자 데이터를 보호해야 합니다. 이러한 공급자는 *부록 A: 공유 호스팅 공급자용 추가 PCI DSS 요구 사항*에서 상세히 설명한 특정 요구 사항에 부합해야 합니다.

**책임:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. Microsoft Azure는 공유 호스팅 공급자가 아닙니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다. Microsoft Azure는 공유 호스팅 공급자가 아닙니다.|




