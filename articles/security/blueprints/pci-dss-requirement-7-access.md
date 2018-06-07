---
title: Azure Payment Processing Blueprint - 액세스 요구 사항
description: PCI DSS 요구 사항 7
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: fb16a7973022421525e13313f53f615120caa38a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 액세스 요구 사항 
## <a name="pci-dss-requirement-7"></a>PCI DSS 요구 사항 7

**비즈니스에서 알아야 할 카드 소유자 데이터에 대한 액세스 제한**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(지불 카드 업계) 보안 표준 협의회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

권한이 부여된 담당자만이 중요한 데이터에 액세스할 수 있도록 하려면 시스템 및 프로세스는 알아야 할 필요 및 직무에 따라 액세스를 제한하도록 해야 합니다.

"알아야 할 필요"는 작업을 수행하는 데 필요한 최소한의 데이터 및 권한에 대해서만 액세스 권한이 부여된다는 의미입니다.

## <a name="pci-dss-requirement-71"></a>PCI DSS 요구 사항 7.1

**7.1** 작업에 액세스해야 하는 개인에게만 시스템 구성 요소 및 카드 소유자 데이터에 대한 액세스를 제한합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Azure에서는 Azure 시스템 구성 요소에 대한 Azure 직원 액세스인 액세스 제어 효율성 확인에 따라 기존 ISMS 정책을 적용하여, Just-In-Time 관리 액세스를 제공하며, 더 이상 필요하지 않은 경우 액세스를 해제하고, 비즈니스 요구에 따라 직원이 Azure 플랫폼 환경에 액세스하도록 보장합니다. 고객 환경에 대한 Azure 액세스는 매우 제한되며 고객 승인이 있어야만 허용됩니다.<br /><br />권한이 부여된 직원, 공급 업체, 하청 업체 및 방문자에게 데이터 센터에 대한 물리적 액세스를 제한하도록 절차를 설정합니다. 보안 확인 및 체크 인은 담당자가 내부 데이터 센터 시설에 대한 임시 액세스를 요구하기 위해 필요합니다. 물리적 액세스 로그는 분기마다 Azure 팀에서 검토합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 작업에 액세스해야 하는 개인에게만 시스템 구성 요소 및 카드 소유자 데이터에 대한 액세스를 제한하는 일을 담당합니다. 여기에는 Azure 관리 포털에 대한 액세스를 제한하는 작업뿐만 아니라 PaaS 서비스를 생성, 수정 또는 삭제할 수 있는 사용 권한이 있는 계정 또는 역할을 지정하는 작업이 포함됩니다.|



### <a name="pci-dss-requirement-711"></a>PCI DSS 요구 사항 7.1.1

**7.1.1** 다음을 비롯한 각 역할에 필요한 액세스를 정의합니다.
- 각 역할이 해당 작업 함수에 액세스해야 하는 시스템 구성 요소 및 데이터 리소스
- 리소스에 액세스 하기 위해 필요한 권한 수준(예: 사용자, 관리자 등)

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 사용자 ID 승인 프로세스를 정의하고 문서화하며, 최소한의 권한을 정의하고, 카드 소유자 데이터에 대한 액세스를 제한하고, 고유 ID를 사용하고, 의무의 분리를 제공하고, 더 필요하지 않은 경우 사용자 액세스 권한을 취소하는 것을 담당합니다.|



### <a name="pci-dss-requirement-712"></a>PCI DSS 요구 사항 7.1.2

**7.1.2** 권한 있는 사용자 ID에 대한 액세스를 직무를 수행하는 데 필요한 최소한의 권한으로 제한합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 정보 보안 정책을 비롯하여 적용 가능한 기업 및 조직 보안 정책을 채택했습니다. 정책이 Windows Azure에 승인되고, 게시되고, 전달되었습니다. 정보 보안 정책은 자산 소유자 권한을 사용하여 비즈니스 근거에 따라 Microsoft Azure 자산에 대한 액세스를 부여하고 "알아야 할 필요" 및 "최소 권한" 원칙에 따라 제한해야 합니다. 또한 정책은 액세스 프로비전, 액세스 권한 부여, 액세스 권한에서 인증 제거 및 정기적 액세스 검토를 비롯한 액세스 관리 수명 주기에 대한 요구 사항을 해결합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중에 admin, sqladmin 및 edna(데모 실행 중에 웹앱에 로그인한 기본 사용자)라는 세 개의 계정을 만듭니다. 사용자 역할은 문서화된 데모 시나리오에 따라 의무로 제한됩니다.|



### <a name="pci-dss-requirement-713"></a>PCI DSS 요구 사항 7.1.3

**7.1.3** 개별 담당자의 작업 분류 및 함수에 따라 액세스 권한을 할당합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중에 admin, sqladmin 및 edna(데모 실행 중에 웹앱에 로그인한 기본 사용자)라는 세 개의 계정을 만듭니다. 사용자 역할은 문서화된 데모 시나리오에 따라 의무로 제한됩니다.|



### <a name="pci-dss-requirement-714"></a>PCI DSS 요구 사항 7.1.4

**7.1.4** 필수 권한을 지정하는 권한 있는 당사자에 의한 문서화된 승인이 필요합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 작업에 액세스해야 하는 개인에게만 시스템 구성 요소 및 카드 소유자 데이터에 대한 액세스를 제한하는 일을 담당합니다. 여기에는 Azure 관리 포털에 대한 액세스를 제한하는 작업뿐만 아니라 PaaS 서비스를 생성, 수정 또는 삭제할 수 있는 사용 권한이 있는 계정 또는 역할을 지정하는 작업이 포함됩니다.|



## <a name="pci-dss-requirement-72"></a>PCI DSS 요구 사항 7.2

**7.2** 사용자의 알아야 할 필요에 따라 액세스를 제한하고 특별히 허용되지 않으면 "모두 거부"로 설정하는 시스템 구성 요소의 액세스 제어 시스템을 설정합니다.
이 액세스 제어 시스템은 다음을 포함해야 합니다.
- 7.2.1 모든 시스템 구성 요소 포함
- 7.2.2 작업 분류 및 함수에 따라 개인에게 권한 할당
- 7.2.3 기본 "모두 거부" 설정

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure Active Directory를 사용하여 지정된 사용자에게만 액세스를 제한합니다. 자세한 내용은 [PCI 지침 - ID 관리](payment-processing-blueprint.md#identity-management)를 참조하세요.|



## <a name="pci-dss-requirement-73"></a>PCI DSS 요구 사항 7.3

**7.3** 카드 소유자 데이터에 대한 액세스를 제한하기 위해 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 설명서에서는 CHD 사용자 및 CHD 사용 방법에 대한 사용 사례 및 설명을 제공합니다.|




