---
title: Azure Active Directory B2B 공동 작업 사용자 속성 | Microsoft Docs
description: Azure Active Directory B2B 공동 작업 사용자 속성은 구성 가능합니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 65fd23ecf81aebdb1fe74f800d20df6414c18317
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 공동 작업 사용자 속성

Azure AD(Azure Active Directory) B2B 공동 작업 사용자는 UserType이 Guest인 사용자입니다. 이 게스트 사용자는 일반적으로 파트너 조직에 소속되어 있으며, 기본적으로 초대 디렉터리에서 제한된 권한을 가집니다.

초대 조직의 요구에 따라 Azure AD B2B 공동 작업 사용자는 다음 계정 상태 중 하나일 수 있습니다.

- 상태 1: Azure AD의 외부 인스턴스에 속하며 초대하는 조직의 게스트 사용자로 표시됩니다. 이 경우 B2B 사용자는 초대된 테넌트에 속한 Azure AD 계정을 사용하여 로그인합니다. 파트너 조직이 Azure AD를 사용하지 않는 경우에도 Azure AD에서 게스트 사용자는 여전히 생성됩니다. 단, 본인의 초대를 사용해야 합니다. Azure AD에서는 해당 사용자의 전자 메일 주소를 확인합니다. 이것을 JIT(Just In Time) 테넌트 또는 “바이럴” 테넌트라고도 합니다.

- 상태 2: Microsoft 계정에 속하고 호스트 조직의 게스트 사용자로 표시됩니다. 이 경우 게스트 사용자는 Microsoft 계정으로 로그인합니다. 제안 상환 중에 초대된 사용자의 비 Microsoft 소셜 ID(google.com 또는 유사한 ID)가 Microsoft 계정으로 만들어집니다.

- 상태 3: 호스트 조직의 온-프레미스 Active Directory에 속하며 호스트 조직의 Azure AD와 동기화와 동기화됩니다. 이 릴리스에서는 PowerShell을 사용하여 수동으로 클라우드에 있는 이러한 사용자의 UserType을 변경해야 합니다.

- 상태 4: UserType이 Guest이고 호스트 조직에서 관리하는 자격 증명을 사용하는 호스트 조직의 Azure AD에 속합니다.

  ![초대자의 이니셜 표시](media/user-properties/redemption-diagram.png)


이제 Azure AD에서 상태 1의 Azure AD B2B 공동 작업 사용자가 어떻게 보이는지 살펴보겠습니다.

### <a name="before-invitation-redemption"></a>초대 상환 전

![제안 상환 전](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>초대 상환 후

![제안 상환 후](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B 공동 작업 사용자의 주요 속성
### <a name="usertype"></a>UserType
이 속성은 사용자와 호스트 테넌트 사이의 관계를 나타냅니다. 이 속성에는 다음 두 가지 값이 사용될 수 있습니다.
- 구성원: 이 값은 호스트 조직의 직원과 조직의 급여 부서에 있는 사용자를 나타냅니다. 예를 들어 이 사용자는 내부 전용 사이트에 대한 액세스 권한을 요구합니다. 이 사용자는 외부 공동 작업자로 간주되지 않습니다.

- 게스트: 이 값은 회사의 내부인으로 간주되지 않는 사용자(예: 외부 공동 작업자, 파트너, 고객 또는 유사한 사용자)를 나타냅니다. 이러한 사용자는 CEO의 내부 메모를 수신하거나 회사 혜택을 받을 것으로 예상되지 않습니다.

  > [!NOTE]
  > UserType은 사용자가 로그인하는 방법, 사용자의 디렉터리 역할 등과 관계가 없습니다. 이 속성은 단순히 사용자와 호스트 조직 사이의 관계를 나타내며, 조직에서 이 속성에 속한 모든 정책을 시행할 수 있게 합니다.

### <a name="source"></a>원본
이 속성은 사용자가 로그인하는 방법을 나타냅니다.

- 초대된 사용자: 이 사용자는 초대되었지만 초대를 아직 상환하지 않았습니다.

- 외부 Active Directory: 이 사용자는 외부 조직에 속하며 다른 조직에 속한 Azure AD 계정을 사용하여 인증합니다. 이 유형의 로그인은 상태 1에 해당합니다.

- Microsoft 계정: 이 사용자는 Microsoft 계정에 속하며 Microsoft 계정을 사용하여 인증합니다. 이 유형의 로그인은 상태 2에 해당합니다.

- Windows Server Active Directory: 이 사용자는 이 조직에 속한 온-프레미스 Active Directory에서 로그인됩니다. 이 유형의 로그인은 상태 3에 해당합니다.

- Azure Active Directory: 이 사용자는 이 조직에 속한 Azure AD 계정을 사용하여 인증합니다. 이 유형의 로그인은 상태 4에 해당합니다.
  > [!NOTE]
  > Source와 UserType은 독립적인 속성입니다. Source의 값은 UserType에 대한 특정 값을 의미하지 않습니다.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B 사용자를 게스트 대신 구성원으로 추가할 수 있습니까?
일반적으로 Azure AD B2B 사용자와 게스트 사용자는 동의어입니다. 따라서 Azure AD B2B 공동 작업 사용자는 기본적으로 UserType = Guest인 사용자로 추가됩니다. 그러나 경우에 따라 파트너 조직은 호스트 조직이 속한 더 큰 조직의 구성원일 수 있습니다. 만약 그렇다면 호스트 조직에서 파트너 조직의 사용자를 게스트가 아닌 구성원으로 처리하려 할 수 있습니다. Azure AD B2B 초대 관리자 API를 사용하여 호스트 조직에 파트너 조직의 사용자를 구성원으로 추가하거나 초대합니다.

## <a name="filter-for-guest-users-in-the-directory"></a>디렉터리의 게스트 사용자 필터링

![게스트 사용자 필터링](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType 변환
현재 사용자는 PowerShell을 사용하여 UserType을 Member에서 Guest로 또는 그 반대로 변환할 수 있습니다. 그러나 UserType 속성은 사용자와 조직 사이의 관계를 나타냅니다. 따라서 이 속성의 값은 사용자와 조직의 관계가 변할 때에만 변해야 합니다. 사용자의 관계가 변경되면 UPN(사용자 계정 이름)을 변경해야 합니까? 사용자가 같은 리소스에 대한 액세스 권한을 계속 가져야 합니까? 사서함을 할당해야 합니까?와 같은 다른 질문에 답변해야 합니다. 따라서 PowerShell을 사용하여 UserType을 원자성 작업으로 변경하지 않는 것이 좋습니다. 뿐만 아니라 PowerShell을 사용하여 이 속성을 변경 불가능으로 만드는 경우 이 값에 의존하지 않는 것이 좋습니다.

## <a name="remove-guest-user-limitations"></a>게스트 사용자 제한 제거
게스트 사용자에게 더 높은 권한을 부여하려는 경우가 있을 수 있습니다. 게스트 사용자를 모든 역할에 추가하고 디렉터리에서 기본 게스트 사용자 제한을 제거하여 사용자에게 구성원과 동일한 권한을 부여할 수 있습니다.

기본 게스트 사용자 제한을 해제하여 회사 디렉터리의 게스트 사용자에게 구성원과 동일한 권한을 부여할 수 있습니다.

![게스트 사용자 제한 제거](media/user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
* [B2B 공동 작업 사용자 토큰](user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](claims-mapping.md)
