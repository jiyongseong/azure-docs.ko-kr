---
title: 사용자 역할을 추가하거나 제거하는 방법| Microsoft Azure
description: Azure Active Directory Privileged Identity Management 응용 프로그램을 사용하여 권한 있는 ID에 역할을 추가하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 01/03/2018
ms.author: curtand
ms.openlocfilehash: 6024016d7c59551e5678069cb6a6e1e7ab45ad6e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: 사용자 역할을 추가 또는 제거하는 방법
Azure AD(Active Directory)와 전역 관리자(또는 회사 관리자)는 사용자가 Azure AD에서 **영구적으로** 역할에 할당되도록 업데이트할 수 있습니다. 이 작업은 `Add-MsolRoleMember` 및 `Remove-MsolRoleMember` 등 PowerShell cmdlet을 사용하여 완료됩니다. 또는 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles.md)에 설명된 대로 Azure Portal을 사용할 수 있습니다.

Azure AD Privileged Identity Management 응용 프로그램을 통해 권한 있는 역할 관리자가 영구 역할 할당을 수행할 수도 있습니다. 또한 권한이 있는 역할 관리자는 사용자의 관리자 역할을 **적격**으로 만들 수 있습니다. 적격인 관리자는 필요할 때 역할을 활성화할 수 있으며 작업을 완료하고 나면 권한이 만료됩니다.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Azure 포털에서 PIM으로 역할 관리
조직에서 Azure AD, Office 365 및 기타 Microsoft 서비스 및 응용 프로그램의 다른 관리 역할에 사용자를 할당할 수 있습니다.  사용 가능한 역할에 대한 자세한 내용은 [Azure AD PIM의 역할](active-directory-privileged-identity-management-roles.md)에 있습니다.

Privileged Identity Management를 사용하여 역할에 사용자를 추가하거나 제거하려면 PIM 대시보드로 전환합니다. 그런 다음 **관리자 역할의 사용자** 단추를 클릭하거나 역할 테이블에서 특정 역할(예: 전역 관리자)를 선택합니다.

> [!NOTE]
> Azure 포털에서 아직 PIM을 사용하도록 설정하지 않은 경우 [Azure AD PIM 시작](active-directory-privileged-identity-management-getting-started.md) 에서 자세한 내용을 참조하세요.

다른 사용자가 PIM에 액세스할 수 있도록 하려는 경우 PIM에 필요한 사용자 역할은 [PIM에 대한 액세스 권한을 제공하는 방법](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)에 자세히 설명되어 있습니다.

## <a name="add-a-user-to-a-role"></a>역할에 사용자 추가
1. [Azure 포털](https://portal.azure.com/)에서 대시보드의 **Azure AD Privileged Identity Management** 타일을 선택합니다.
2. **권한 있는 역할 관리**를 선택합니다.
3. **역할 요약** 테이블에서 관리하려는 역할을 선택합니다.
4. 역할 블레이드에서 **추가**를 선택합니다.
5. **사용자 선택**을 클릭하고 **사용자 선택** 블레이드에서 사용자를 검색합니다.  
6. 검색 결과 목록에서 사용자를 선택하고 **완료**를 클릭합니다.
7. **확인** 을 클릭하여 선택 내용을 저장합니다. 선택한 사용자가 그 역할에 대해 적합하다고 목록에 표시됩니다.

> [!NOTE]
> 역할에 포함된 새 사용자는 기본적으로 그 역할에 대해서만 자격이 있습니다. 역할을 영구적으로 지정하려면 목록에서 사용자를 클릭합니다. 사용자의 정보가 새 블레이드에 표시됩니다. 사용자 정보 메뉴에서 **make perm** (영구 지정)을 선택합니다.  
> 사용자가 Azure MFA(Multi-Factor Authentication)를 등록할 수 없거나 Microsoft 계정(일반적으로 @outlook.com)을 사용하고 있는 경우 사용자 정보는 모든 역할에서 영구적으로 유지되도록 해야 합니다. 활성화 과정에서 적격 관리자에게 MFA를 등록하도록 요청하는 메시지가 표시됩니다.

사용자가 역할을 부여받을 자격이 있으므로 [역할을 활성화 또는 비활성화하는 방법](active-directory-privileged-identity-management-how-to-activate-role.md)의 지침에 따라 역할을 활성화할 수 있음을 알려 줍니다.

## <a name="remove-a-user-from-a-role"></a>역할에서 사용자 제거
적격 역할 할당에서 사용자를 제거할 수 있지만 영구 전역 관리자인 사용자가 최소 한 명은 항상 있어야 합니다.

특정 사용자를 역할에서 제거하려면 다음 단계를 수행합니다.

1. Azure AD PIM 대시보드에서 역할을 선택하거나 **관리자 역할의 사용자** 단추를 클릭하여 역할 목록의 역할로 이동합니다.
2. 사용자 목록에서 사용자를 클릭합니다.
3. **제거**를 클릭합니다. 메시지에서 확인을 요청합니다.
4. 사용자로부터 역할을 제거하려면 **예** 를 클릭합니다.

사용자에게 해당 역할 할당이 여전히 필요한지 확실하지 않은 경우에는 [역할에 대한 액세스 권한을 검토하기 시작](active-directory-privileged-identity-management-how-to-start-security-review.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

