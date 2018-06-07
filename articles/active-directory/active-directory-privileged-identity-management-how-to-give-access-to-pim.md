---
title: Privileged Identity Management에 대한 액세스 권한을 제공하는 방법 - Azure | Microsoft Docs
description: PIM을 관리할 수 있도록 Azure Active Directory Privileged Identity Management 확장을 사용하여 사용자에 역할을 추가하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: f7e08e35ce4575715a72b0880d038ce0db766b66
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management를 관리하기 위해 액세스 권한 제공
조직에 대한 Azure AD Privileged Identity Management (PIM)을 사용하는 전역 관리자는 자동적으로 역할 할당 및 PIM에 대한 액세스를 가집니다. 하지만, 기본적으로 다른 전역 관리자를 포함하여 아무도 쓰기 액세스 권한을 갖지 못합니다. 다른 전역 관리자, 보안 관리자 및 보안 판독기에는 Azure AD PIM에 대한 읽기 전용 액세스 권한이 있습니다. PIM에 대한 액세스를 제공하기 위해 첫 번째 사용자가 다른 사용자를 **권한 있는 역할 관리자** 역할에 할당할 수 있습니다. 이 할당은 PIM 자체 내에서 수행해야 하고 PowerShell 또는 다른 포털을 통해 변경할 수 없습니다.

> [!NOTE]
> Azure AD PIM 관리에 Azure MFA가 필요합니다. Azure MFA에 대해 Microsoft 계정을 등록할 수 없기 때문에 Microsoft 계정으로 로그인하는 사용자는 Azure AD PIM에 액세스할 수 없습니다.
> 
> 

한 명의 사용자가 잠긴 경우 또는 해당 계정이 삭제된 경우 최소한 두 명의 사용자가 항상 권한 있는 역할 관리자 역할에 있어야 합니다.

## <a name="give-another-user-access-to-manage-pim"></a>PIM을 관리하기 위해 다른 사용자에게 액세스 권한 제공
1. [Azure 포털](https://portal.azure.com/) 에 로그인하고 대시보드에서 **Azure AD Privileged Identity Management** 앱을 선택합니다.
2. **권한 있는 역할 관리** > **권한 있는 역할 관리자** > **추가**를 선택합니다.
   
    ![권한 있는 역할 관리자 추가 - 스크린샷][1]
3. 관리되는 사용자 추가 블레이드에서 1 단계는 이미 완료된 상태입니다. 2 단계를 선택하여, **사용자를 선택** 하고 추가할 사용자를 검색합니다.
   
    ![사용자 선택 - 스크린 샷][2]
4. 검색 결과에서 사용자를 선택하고 **완료**를 클릭합니다.
5. **확인** 을 클릭하여 선택 내용을 저장합니다. 선택한 사용자가 권한 있는 역할 관리자 목록에 나타납니다.
   
   * 다른 사람에게 새 역할을 할당할 때마다 자동으로 해당 역할을 활성화할 자격이 있는 것으로 설정됩니다. 영구적으로 역할에 지정하려면 목록에서 사용자를 클릭합니다. 사용자 정보 메뉴에서 **make perm** (영구 지정)을 선택합니다.
6. 사용자에게 [Azure AD Privileged Identity Management 시작](active-directory-privileged-identity-management-getting-started.md)링크를 보냅니다.

## <a name="remove-another-users-access-rights-for-managing-pim"></a>PIM 관리에 대한 다른 사용자의 액세스 권한 제거
권한 있는 역할 관리자 역할에서 사용자를 제거하기 전에 항상 두 명의 사용자가 이 역할에 할당되어 있는지 확인합니다.

1. PIM 대시보드에서 역할 **권한 있는 역할 관리자**를 클릭합니다.  현재 해당 역할 상태인 사용자 목록이 표시됩니다.
2. 사용자 목록에서 사용자를 클릭합니다.
3. **제거**를 클릭합니다.  확인 메시지가 나타납니다.
4. 역할로부터 사용자를 제거하려면 **예** 를 클릭합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
