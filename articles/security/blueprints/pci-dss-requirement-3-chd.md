---
title: Azure Payment Processing Blueprint - CHD 요구 사항
description: PCI DSS 요구 사항 3
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 7ff8a412b16025afe2640b73b51d9b2a2fdca354
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 CHD 요구 사항
## <a name="pci-dss-requirement-3"></a>PCI DSS 요구 사항 3

**저장된 카드 소유자 데이터 보호**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

암호화, 잘림, 마스킹 및 해시 등의 보호 방법은 카드 소유자 데이터 보호의 중요한 구성 요소입니다. 적합한 암호화 키 없이 침입자가 다른 보안 제어를 우회하고 암호화된 데이터에 대한 액세스를 확보할 경우 해당 사용자가 데이터를 읽거나 사용할 수 없습니다. 저장된 데이터를 보호하는 다른 효과적인 방법도 잠재적인 위험 완화 수단으로 고려되어야 합니다. 절대적으로 필요하지 않은 한 카드 소유자 데이터를 저장하지 않고, 전체 PAN이 필요하지 않은 경우 카드 소유자 데이터를 분리하며, 이메일이나 인스턴트 메시징 같은 최종 사용자 메시징 기술을 사용하여 보호되지 않는 PAN을 보내지 않는 등의 방법을 예로 들 수 있습니다.
"강력한 암호화" 및 기타 PCI DSS 용어는 PCI DSS 및 PA-DSS 용어집 및 약어를 참조하세요.

## <a name="pci-dss-requirement-31"></a>PCI DSS 요구 사항 3.1

**3.1** 모든 카드 소유자 데이터(CHD) 저장에 대해 최소한 다음을 포함하는 최소 데이터 보존 및 폐기 정책, 절차 및 프로세스를 구현하여 카드 소유자 데이터 저장을 최소 수준으로 유지합니다.
- 데이터 저장 크기와 보존 기간을 법률, 규정 및 업무 요구 사항에서 요구하는 수준으로 제한
- 카드 소유자 데이터에 대한 특정 보존 요구 사항
- 필요 없는 데이터의 안전한 삭제를 위한 프로세스
- 정의된 기간을 초과한 저장된 카드 소유자 데이터를 식별하고 안전하게 삭제하기 위한 분기별 프로세스

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure는 삭제 대상으로 지정된 고객 데이터가 보안 폐기 정책에서 지정한 NIST 800-88 규격 보안 프로토콜을 사용하여 안전하게 폐기되도록 확인할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 저장된 CHD를 삭제하거나 파기하지 않습니다. 그러나 모든 데이터는 암호화되며 PAN(기본 계정 번호) 데이터는 저장되지 않습니다.<br /><br />|



## <a name="pci-dss-requirement-32"></a>PCI DSS 요구 사항 3.2

**3.2** (암호화되었다 하더라도) 권한 부여 후에는 중요 인증 데이터를 저장하지 않습니다. 중요 인증 데이터를 받으면 인증 프로세스 완료 시점에 모든 데이터를 복구 불가능하게 렌더링합니다. 
- 비즈니스 근거가 있으며 
- 데이터를 안전하게 저장합니다.
중요 인증 데이터는 다음 요구 사항 3.2.1~3.2.3에서 언급한 데이터를 포함합니다.


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 저장된 CHD를 삭제하거나 파기하지 않습니다. 샘플 데이터는 오직 데모용으로만 저장됩니다. 그러나 모든 데이터는 암호화되며 PAN(기본 계정 번호) 데이터는 저장되지 않습니다.<br /><br />|



### <a name="pci-dss-requirement-321"></a>PCI DSS 요구 사항 3.2.1

**3.2.1** 권한 부여 후 트랙(카드 뒷면의 마그네틱 띠, 칩 또는 기타 항목에 포함된 유사 데이터)의 전체 데이터를 저장하지 않습니다. 이 데이터는 전체 트랙, 트랙, 트랙 1, 트랙 2, 마그네틱 띠 데이터 등이라고도 합니다. 

> [!NOTE]
> 정상적인 업무 과정에서 마그네틱 띠의 다음 데이터 요소를 보존해야 할 수 있습니다. 
> - 카드 소유자의 이름 
> - PAN(기본 계정 번호) 
> - 만료 날짜 
> - 서비스 코드 
>
> 위험을 최소화하기 위해 업무에 필요한 데이터 요소만 저장합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 CHD의 전체 콘텐츠를 저장하지 않습니다.|



### <a name="pci-dss-requirement-322"></a>PCI DSS 요구 사항 3.2.2

**3.2.2** 권한 부여 후 카드 확인 코드나 값(실제 카드를 제시하지 않는 거래의 확인을 위해 사용되는 결제 카드 뒷면 또는 앞면에 인쇄된 3자리 또는 4자리 숫자)을 저장하지 않습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 CVV 샘플을 비롯한 모든 데이터를 암호화합니다.|



### <a name="pci-dss-requirement-323"></a>PCI DSS 요구 사항 3.2.3

**3.2.3** PIN(개인 식별 번호) 또는 암호화된 PIN 블록을 권한 부여 후 저장하지 않습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PIN 정보를 저장하지 않습니다.|



## <a name="pci-dss-requirement-33"></a>PCI DSS 요구 사항 3.3

**3.3** 합당한 업무상 필요가 있는 사용자만 전체 PAN을 볼 수 있게 표시할 때 PAN을 마스킹합니다(표시하는 최대 숫자는 처음 6자리 및 마지막 4자리). 

> [!NOTE]
> 이 요구 사항은 카드 소유자 데이터의 표시에 관하여 더 엄격한 요구 사항(예: POS(Point of Sales)에 대한 법률 또는 결제 카드 브랜드의 요구 사항)을 대체하지 않습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 투명한 데이터 암호화, Always Encrypted 열, 동적 데이터 마스킹을 사용하여 PAN(기본 계정 번호)을 마스킹합니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



## <a name="pci-dss-requirement-34"></a>PCI DSS 요구 사항 3.4

**3.4** (이동식 디지털 미디어, 백업 미디어, 로그 등) 저장된 모든 위치에서 다음 방법 중 하나를 통해 PAN을 읽을 수 없게 렌더링합니다.
- 강력한 암호화를 기반으로 하는 단방향 해시(해시가 전체 PAN의 것이어야 함)
- 자름(해시를 사용하여 PAN의 잘린 부분을 바꿀 수 없음)
- 인덱스 토큰 및 패드(패드를 안전하게 저장해야 함)
- 연결된 키 관리 프로세스 및 절차가 있는 강력한 암호화 

> [!NOTE]
> 악의적인 사용자가 PAN의 잘린 버전과 해시 처리된 버전 모두에 대해 액세스를 갖는 경우 원래 PAN 데이터를 상대적으로 쉽게 재구성할 수 있습니다. 동일한 PAN의 해시 및 분할된 버전이 엔터티 환경에 있으면 해시 및 분할된 버전을 상관하여 원래의 PAN을 재구성하지 못하도록 추가적인 제어를 갖추어야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 모든 신용 카드 데이터를 암호화하며 Azure Key Vault를 사용하여 키를 관리하여 CHD 검색을 방지합니다.<br /><br />자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-341"></a>PCI DSS 요구 사항 3.4.1

**3.4.1** 디스크 암호화를 사용할 경우(파일 또는 열 기반 데이터베이스 암호화 아님) 논리적 액세스는 기본 운영 체제 인증 및 액세스 제어 메커니즘과 별도 및 독립적으로 관리해야 합니다(예: 로컬 사용자 계정 데이터베이스나 일반 네트워크 로그인 자격 증명 사용 안 함). 암호 해독 키는 사용자 계정과 연결되지 않아야 합니다. 

> [!NOTE]
> 이 요구 사항은 다른 모든 PCI DSS 암호화 및 키 관리 요구 사항과 더불어 적용됩니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 저장된 데이터를 암호화하고 트래픽을 분리하여 DevOps 기능에 대한 권한 상승을 차단합니다.<br /><br />App Service 환경은 보호되고 잠겨 있으므로 Kudu를 사용한 웹앱 모니터링 기능처럼 필요한 DevOps 릴리스나 변경 내용을 허용하는 메커니즘이 필요합니다.<br /><br />다음 구성을 통해 가상 머신을 점프박스(요새 호스트)로 구축합니다.<br /><br /><ul><li>[맬웨어 방지 확장](/azure/security/azure-security-antimalware)</li><li>[Log Analytics 모니터링 확장](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[VM 진단 확장](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Bitlocker 암호화 디스크](/azure/security/azure-security-disk-encryption)</li></ul>Azure Key Vault를 사용하여 Azure Government, PCI DSS 및 HIPAA 요구 사항에 따릅니다.|



## <a name="pci-dss-requirement-35"></a>PCI DSS 요구 사항 3.5

**3.5** 저장된 카드 소유자 데이터가 공개 및 오용되지 않게 보호하기 위해 사용되는 키 보호 절차를 문서화 및 구현합니다. 

> [!NOTE]
> 이 요구 사항은 저장된 카드 소유자 데이터를 암호화하는 키에 적용되며, 데이터 암호화 키 보호에 사용되는 키 암호화 키에도 적용됩니다. 이러한 키 암호화 키는 데이터 암호화 키 이상으로 강력해야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />Microsoft Azure는 고객 키 자격 증명 모음이 서로 논리적으로 격리되고 Key Vault 서비스의 관리 평면과 논리적으로 격리되도록 합니다. Key Vault는 Microsoft가 고객의 키 자격 증명 모음에 대한 고정 액세스를 갖지 못하도록 설계되었습니다. <br /><br />키는 Microsoft Azure에서 업계 표준 알고리즘, 키 길이 및 HSM(하드웨어 보안 모듈)을 사용하여 보호됩니다.<br /><br />Microsoft Azure Key Vault에 저장된 키를 사용하여 다른 키를 보호할 수 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 데모 CHD 보호를 위한 보호 키 솔루션 배포를 설명 및 지원하는 문서를 제공합니다.|



### <a name="pci-dss-requirement-351"></a>PCI DSS 요구 사항 3.5.1

**3.5.1** *서비스 공급자 전용 추가 요구 사항:* 다음을 포함하는 암호화 아키텍처의 문서화된 설명을 유지 관리합니다.
- 키 강도 및 만료일을 포함하여 카드 소유자 데이터의 보호를 위한 모든 알고리즘, 프로토콜 및 키의 상세 정보
- 각 키에 대한 키 사용 설명
- 키 관리에 사용되는 HSM 및 기타 SCD 인벤토리 

> [!NOTE]
> 이 요구 사항은 2018년 1월 31일까지 모범 사례이고, 이후에는 요구 사항이 됩니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />Microsoft Azure는 고객 키 자격 증명 모음이 서로 논리적으로 격리되고 Key Vault 서비스의 관리 평면과 논리적으로 격리되도록 합니다. Key Vault는 Microsoft가 고객의 키 자격 증명 모음에 대한 고정 액세스를 갖지 못하도록 설계되었습니다. <br /><br />키는 Microsoft Azure에서 업계 표준 알고리즘, 키 길이 및 HSM(하드웨어 보안 모듈)을 사용하여 보호됩니다.<br /><br />Microsoft Azure Key Vault에 저장된 키를 사용하여 다른 키를 보호할 수 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 데모 CHD 보호를 위한 보호 키 솔루션 배포를 설명 및 지원하는 문서를 제공합니다.|



### <a name="pci-dss-requirement-352"></a>PCI DSS 요구 사항 3.5.2

**3.5.2** 암호화 키에 대한 액세스는 필요한 최소한의 보유자로 제한합니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />Key Vault는 특정 엔터티에 대해 특정 작업을 수행하기 위해 특정 기능에 대한 액세스를 Key Vault 소유자가 부여할 수 있는 세부 액세스 정책을 지원합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 키 관리는 단일 사용자 계정(admin##@contosowebstore.com)으로 격리됩니다.|



### <a name="pci-dss-requirement-353"></a>PCI DSS 요구 사항 3.5.3

**3.5.3** 항상 다음 중 하나 이상의 형태를 사용하여 카드 소유자 데이터를 암호화/암호 해독하는 데 사용되는 비밀 및 개인 키를 저장합니다.
- 데이터 암호화 키 이상으로 강력한 키 암호화 키로 암호화되며 데이터 암호화 키와는 별도로 저장 
- 안전한 암호화 장치 내부(HSM(하드웨어(호스트) 보안 모듈) 또는 PTS 승인 상호 작용 지점 장치 등)
- 업계에서 허용되는 방법에 따라 둘 이상의 전체 길이 키 구성 요소 또는 키 공유 

> [!NOTE]
> 공개 키를 이러한 형태 중 하나로 저장하는 것은 필수가 아닙니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />키는 고객이 식별한 특정 키 자격 증명 모음에 저장됩니다.<br /><br />Key Vault는 여러 응용 프로그램에서 동시에 전체적으로 액세스할 수 있으므로 여러 위치에 키를 복사하여 저장할 필요가 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-354"></a>PCI DSS 요구 사항 3.5.4

**3.5.4** 가장한 가장 적은 위치에 암호화 키를 저장합니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />키는 고객이 식별한 특정 키 자격 증명 모음에 저장됩니다. <br /><br />Key Vault는 여러 응용 프로그램에서 동시에 전체적으로 액세스할 수 있으므로 여러 위치에 키를 복사하여 저장할 필요가 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다. 자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



## <a name="pci-dss-requirement-36"></a>PCI DSS 요구 사항 3.6

**3.6** 다음을 포함하여 카드 소유자 데이터 암호화에 사용되는 암호화 키에 대해 모든 키 관리 프로세스와 절차를 완전히 문서화 및 구현합니다. 

> [!NOTE]
> NIST(http://csrc.nist.gov) 등, 여러 리소스에서 다양한 키 관리 업계 표준을 제공하고 있습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-361"></a>PCI DSS 요구 사항 3.6.1

**3.6.1** 강력한 암호화 키 생성

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:** <br /><br />Key Vault에서 키를 생성할 때 Azure는 고객의 규격에 따라 키를 생성할 책임이 있습니다. 키는 HSM을 사용하여 생성됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-362"></a>PCI DSS 요구 사항 3.6.2

**3.6.2** 암호화 키 배포 보안

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />BYOK(고유 키 사용) 도구는 고객 키를 캡슐화하고 특정 Azure 구독에 묶인 특정 보안 자격 증명 모음을 대상으로 지정합니다. 키는 지정된 지역에서 정의된 구독의 키 자격 증명 모음으로만 가져올 수 있습니다. 이 프로세스는 하드웨어 제조업체에서 제공한 암호화 프로시저를 사용합니다. 고객이 전송에 성공했음을 알림으로 받습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-363"></a>PCI DSS 요구 사항 3.6.3

**3.6.3** 암호화 키 저장 보안

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />키는 HSM에 저장되며 하드웨어 제조업체의 암호화 보안을 통해 보호됩니다. 키에 대한 메타데이터는 암호화된 상태로 Azure Storage에 저장되며 각 키 자격 증명 모음마다 고유합니다. <br /><br /> |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-364"></a>PCI DSS 요구 사항 3.6.4

**3.6.4** 연결된 응용 프로그램 공급업체나 키 소유자가 정의한 대로 업계 모범 사례 및 지침(예: NIST Special Publication 800-57)에 따라, 암호화 기간이 종료된 키에 대해 암호화 키가 바뀝니다(예: 일정 기간 경과 후 및/또는 해당 키가 일정 규모의 암호화 텍스트를 생성한 후).

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />Key Vault는 고객이 정의한 키 업데이트 또는 롤링 기능을 지원합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-365"></a>PCI DSS 요구 사항 3.6.5

**3.6.5** 키 무결성이 취약해졌거나(예: 일반 텍스트 키 구성 요소를 알고 있는 직원의 퇴사) 키 손상이 의심되는 경우 필요하다고 판단되는 키 사용 중지 또는 교체(예: 보관, 파기 및/또는 해지). 

> [!NOTE]
> 사용 중지되거나 바뀐 암호화 키를 보존해야 할 경우 이 키를 안전하게 보관해야 합니다(예: 키 암호화 키 사용). 보관된 암호화 키는 암호 해독/확인용으로만 사용해야 합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />Key Vault는 고객이 정의한 키 사용 중지 또는 바꾸기 기능을 지원합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-366"></a>PCI DSS 요구 사항 3.6.6

**3.6.6** 수동 일반 텍스트 암호화 키 작업이 필요한 경우 분할된 정보 및 이중 제어를 통해 이 작업을 관리해야 합니다. 

> [!NOTE]
> 수동 키 관리 작업에는 키 생성, 전송, 로드, 저장, 파기 등을 망라한 여러 사례가 있습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-367"></a>PCI DSS 요구 사항 3.6.7

**3.6.7** 암호화 키의 무단 대체를 방지합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | **Key Vault를 사용하는 고객:**<br /><br />Key Vault가 논리적으로 구분되며 디렉터리 교차 권한 부여를 지원하지 않습니다. 따라서 무단 대체가 차단됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|



### <a name="pci-dss-requirement-368"></a>PCI DSS 요구 사항 3.6.8

**3.6.8** 암호화 키 보유자가 키 보유자 책임을 이해하고 그에 동의함을 공식적으로 인정하도록 하는 요구 사항입니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 키 관리는 단일 사용자 계정(admin##@contosowebstore.com)으로 격리됩니다.|



## <a name="pci-dss-requirement-37"></a>PCI DSS 요구 사항 3.7

**3.7** 저장된 카드 소유자 데이터를 보호하기 위해 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Key Vault를 모든 키 관리에 사용합니다.  자세한 내용은 [PCI 지침 - 암호화](payment-processing-blueprint.md#encryption-and-secrets-management)를 참조하세요.|




