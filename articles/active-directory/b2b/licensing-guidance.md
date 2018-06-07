---
title: Azure Active Directory B2B 공동 작업 라이선스 지침 | Microsoft Docs
description: Azure Active Directory B2B 공동 작업에는 유료 Azure AD 라이선스가 필요하지 않지만 B2B 게스트 사용자를 위한 유료 기능도 얻을 수 있습니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 08/09/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: beeb7801465d38743fdd654496c7b1b8e3ba1cc3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267198"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 공동 작업 라이선스 지침

Azure AD B2B 공동 작업 기능을 사용하여 게스트 사용자를 Azure AD 테넌트로 초대하여 이들이 Azure AD 서비스 및 조직의 다른 리소스에 액세스할 수 있도록 합니다. 유료 Azure AD 기능에 대한 액세스 권한을 제공하려면 해당 B2B 공동 작업 게스트 사용자가 적절한 Azure AD 라이선스로 사용 허가를 받아야 합니다. 

구체적으로 살펴보면 다음과 같습니다.
* Azure AD Free 기능은 추가 라이선싱 없이 게스트 사용자에게 제공됩니다.
* B2B 사용자에게 유료 Azure AD 기능에 대한 액세스를 제공하려는 경우 해당 B2B 게스트 사용자를 지원하는 데 충분한 라이선스가 있어야 합니다.
* Azure AD 유료 라이선스가 있는 초대하는 테넌트는 테넌트에 초대된 5명의 추가 B2B 게스트 사용자에 대한 B2B 공동 작업 사용 권한을 보유합니다.
* 초대하는 테넌트를 소유한 고객이 유료 Azure AD 기능이 필요한 B2B 공동 작업 사용자 수를 확인해야 합니다. 게스트 사용자에게 제공하려는 유료 Azure AD 기능에 따라 B2B 공동 작업 사용자를 동일한 5:1 비율로 처리하기에 충분한 Azure AD 유료 라이선스가 있어야 합니다.

B2B 공동 작업 게스트 사용자는 조직의 직원 또는 대기업에서 다른 비즈니스 직원이 아닌, 파트너 회사의 사용자로 추가됩니다. B2B 게스트 사용자는 이 문서에 설명된 것처럼 외부 자격 증명 또는 조직이 소유한 자격 증명으로 로그인할 수 있습니다. 

즉, B2B 라이선싱은 사용자 인증 방법이 아닌 사용자와 조직의 관계에 의해 설정됩니다. 이러한 사용자가 파트너가 아닌 경우 라이선스 조건에서 다르게 처리됩니다. 해당 UserType이 “게스트”로 표시된 경우에도 라이선싱 용도의 B2B 공동 작업 사용자로 간주되지 않습니다. 이들은 사용자당 하나의 라이선스로 정상적인 사용 허가를 받아야 합니다. 이러한 사용자는 다음과 같습니다.
* 직원
* 외부 ID를 사용하여 로그인하는 직원
* 대기업에서 다른 비즈니스의 직원


## <a name="licensing-examples"></a>라이선스 예제
- 고객이 해당 Azure AD 테넌트에 100명의 B2B 공동 작업 사용자를 초대하려고 합니다. 고객이 모든 사용자에 대해 액세스 관리 및 프로비저닝을 할당하지만 50명의 사용자는 MFA 및 조건부 액세스도 필요합니다. 이 고객은 해당 B2B 사용자를 제대로 처리하기 위해 Azure AD 기본 라이선스 10개와 Azure AD Premium P1 라이선스 10개를 구입해야 합니다. 고객이 B2B 사용자에게 ID 보호 기능을 사용하려는 경우 초대받은 B2B 사용자를 5:1 비율로 처리하려면 Azure AD Premium P2 라이선스가 있어야 합니다.
- 한 명의 고객에게 Azure AD Premium P1으로 사용이 허가된 10명의 직원이 있습니다. 이제 모두 MFA(다단계 인증)가 필요한 60명의 B2B 사용자를 초대하려고 합니다. 5:1 라이선스 규칙에 따라 고객은 60명의 모든 B2B 공동 작업 사용자를 처리하기 위해 12개 이상의 Azure AD Premium P1 라이선스가 있어야 합니다. 직원 10명에 대한 Premium P1 라이선스 10개가 이미 있으므로 MFA 등의 Premium P1 기능을 사용하여 50명의 B2B 사용자를 초대할 권한이 있습니다. 그런 다음 이 예제에서는 나머지 10명의 B2B 공동 작업 사용자를 처리하기 위해 Premium P1 라이선스 2개를 추가로 구입해야 합니다.

> [!NOTE]
> 이러한 B2B 공동 작업 사용자 권한을 사용할 수 있도록 B2B 사용자에게 직접 라이선스를 할당하는 방법은 아직 없습니다.

초대하는 테넌트를 소유한 고객이 유료 Azure AD 기능이 필요한 B2B 공동 작업 사용자 수를 확인해야 합니다. 게스트 사용자에게 제공하려는 유료 Azure AD 기능에 따라 B2B 공동 작업 사용자를 5:1 비율로 처리하기에 충분한 Azure AD 유료 라이선스가 있어야 합니다. 

## <a name="additional-licensing-details"></a>추가 라이선스 정보
- 실제로 B2B 사용자 계정에 라이선스를 할당할 필요는 없습니다. 5:1 비율에 따라, 라이선스가 자동으로 계산 및 보고됩니다.
- 테넌트에 유료 Azure AD 라이선스가 없는 경우 초대받은 모든 사용자는 Azure AD Free 버전에서 제공하는 권한을 갖게 됩니다.
- B2B 공동 작업 사용자가 해당 조직의 유료 Azure AD 라이선스를 이미 갖고 있는 경우에는 초대하는 테넌트의 B2B 공동 작업 라이선스 중 하나를 사용하지 않습니다.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>고급 토론: API를 사용하여 대기업 조직의 사용자를 “구성원”으로 추가할 때 라이선싱에 대해 고려할 사항은 무엇인가요?
B2B 게스트 사용자는 호스트 조직과 함께 작업하도록 파트너 조직에서 초대된 사용자입니다. 일반적으로 다른 모든 경우는 B2B 기능을 사용하더라도 B2B로 인정되지 않습니다. 특히 다음 두 가지 경우를 살펴보겠습니다.

1. 호스트가 소비자 주소를 사용하는 직원을 초대하는 경우
  * 이 시나리오는 라이선싱 정책을 준수하지 않으며 권장되지 않습니다.

2. 호스트 조직이 다른 대기업 조직의 사용자를 추가하는 경우
  1. 이 경우 사용자는 B2B API를 사용하여 초대되지만 일반적으로 B2B가 아닙니다. 이상적으로는 이러한 조직이 다른 조직의 사용자를 구성원으로 초대하도록 해야 합니다(API에서 이를 허용함). 이 경우 초대하는 조직의 리소스에 액세스하려면 이러한 구성원에게 라이선스를 할당해야 합니다.

  2. 일부 조직에서는 다른 조직의 사용자를 정책에 “게스트”로 추가해야 할 수 있습니다. 다음 두 가지 경우가 있습니다.
      * 대규모 조직에서 이미 Azure AD를 사용하고 있고 초대받은 사용자가 다른 조직에서 사용이 허가된 경우: 이 경우 초대받은 사용자가 이 문서의 앞부분에서 설명한 1:5 수식을 따를 필요는 없습니다. 

      * 대기업 조직에서 Azure AD를 사용하고 있지 않거나 해당 조직에 적절한 라이선스가 없는 경우: 이 경우 이 문서의 앞부분에 있는 1:5 수식을 따릅니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다음 문서를 참조하세요.

* [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](faq.md)
