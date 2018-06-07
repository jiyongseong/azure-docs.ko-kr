---
title: Azure Portal에서 B2B 공동 작업 사용자 - Azure Active Directory | Microsoft Docs
description: 관리자가 Azure AD(Azure Active Directory) B2B 공동 작업을 사용하여 파트너 조직에서 해당 디렉터리에 게스트 사용자를 추가할 수 있는 방법을 보여줍니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7dddc41d35ae466f7c1392450fbda3c86a72a538
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267234"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B 공동 작업 사용자를 Azure Portal에 추가

전역 관리자 또는 제한된 관리자 디렉터리 역할 중 하나에 할당된 사용자로서 Azure Portal을 사용하여 B2B 공동 작업 사용자를 초대할 수 있습니다. 게스트 사용자를 디렉터리, 그룹 또는 응용 프로그램에 초대할 수 있습니다. 다음 방법 중 하나를 통해 사용자를 초대한 후 초대된 사용자의 계정은 *게스트*의 사용자 유형으로 Azure AD(Azure Active Directory)에 추가됩니다. 게스트 사용자는 리소스에 액세스하려면 해당 초대를 사용해야 합니다.

게스트 사용자를 디렉터리에 추가한 후에 게스트 사용자에게 공유 앱에 대한 직접 링크를 보낼 수 있습니다. 또는 게스트 사용자는 초대 이메일에서 상환 URL을 클릭할 수 있습니다. 상환 프로세스에 대한 자세한 내용은 [B2B 공동 작업 초대 상환](redemption-experience.md)을 참조하세요.

> [!IMPORTANT]
> [방법: Azure Active Directory에서 조직의 개인 정보를 추가](https://aka.ms/adprivacystatement)의 단계를 수행하여 조직 개인정보취급방침의 URL을 추가해야 합니다. 첫 번째 초대 상환 프로세스의 일환으로 초대된 사용자는 계속하여 개인정보취급방침 사용 약관에 동의해야 합니다. 

## <a name="add-guest-users-to-the-directory"></a>디렉터리에 게스트 사용자 추가

B2B 공동 작업 사용자를 디렉터리에 추가하려면 다음 단계를 수행합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **사용자 및 그룹** > **모든 사용자**를 선택합니다.
4. **새 게스트 사용자**를 선택합니다.

   ![새 게스트 사용자의 UI 내 위치 표시](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
7. **게스트 초대**에서 외부 사용자의 이메일 주소를 입력합니다. 필요에 따라 환영 메시지가 포함됩니다. 예: 

   ![새 게스트 사용자의 UI 내 위치 표시](./media/add-users-administrator/InviteGuest.png) 

8. **초대**를 선택하여 게스트 사용자에게 자동으로 초청을 발송합니다. **알림** 영역에서 **성공적으로 초대된 사용자** 메시지를 찾습니다. 
 
초대를 발송한 후 사용자 계정이 디렉터리에 게스트로 자동 추가됩니다.


![게스트 사용자 유형의 B2B 사용자 표시](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>그룹에 게스트 사용자 추가
B2B 공동 작업 사용자를 Azure AD 관리자로 그룹에 수동으로 추가할 필요가 있는 경우 다음 단계를 수행합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **사용자 및 그룹** > **모든 그룹**을 선택합니다.
4. 그룹을 선택합니다(또는 **새 그룹**을 클릭하여 새 그룹을 만듭니다). B2B 게스트 사용자가 그룹에 포함된다는 것을 그룹 설명에 포함하는 것이 좋습니다.
5. **멤버** > **멤버 추가**를 선택합니다. 
6. 다음 중 하나를 수행합니다.
   - 게스트 사용자가 이미 디렉터리에 있으면 B2B 사용자를 검색합니다. 사용자 선택 > **선택** 클릭하여 사용자를 그룹에 추가합니다.
   - 게스트 사용자가 아직 디렉터리에 있지 않은 경우 **초대**를 선택합니다.
   ![게스트 구성원을 추가하려면 초대 단추 추가](./media/add-users-administrator/GroupInvite.png)
   
      **게스트 초대**에서 이메일 주소 및 선택적 개인 메시지 입력 > **초대**를 선택합니다. **선택**을 클릭하여 사용자를 그룹에 추가합니다.

      초대가 초대된 사용자에게 자동으로 이동합니다. **알림** 영역에서 성공적으로 **초대된 사용자** 메시지를 찾습니다. 

또한 Azure AD B2B 공동 작업을 통해 동적 그룹을 사용할 수 있습니다. 자세한 내용은 [동적 그룹 및 Azure Active Directory B2B 공동 작업](use-dynamic-groups.md)을 참조합니다.

## <a name="add-guest-users-to-an-application"></a>응용 프로그램에 게스트 사용자 추가

B2B 공동 작업 사용자를 Azure AD 관리자로 응용 프로그램에 추가하려면 다음 단계를 수행합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.
4. 게스트 사용자를 추가하려는 응용 프로그램을 선택 합니다.
5. **관리**에서 **사용자 및 그룹**을 선택합니다.
6. **사용자 추가**를 선택합니다.
7. **할당 추가**에서 **사용자 및 그룹**을 선택합니다.
8. 다음 중 하나를 수행합니다.
   - 게스트 사용자가 이미 디렉터리에 있으면 B2B 사용자를 검색합니다. 사용자를 선택한 다음, **선택**을 클릭하여 사용자를 앱에 추가합니다.
   - 게스트 사용자가 아직 디렉터리에 있지 않은 경우 **초대**를 선택합니다.
   ![게스트 구성원을 추가하려면 초대 단추 추가](./media/add-users-administrator/AppInviteUsers.png)
   
      **게스트 초대**에서 이메일 주소 및 선택적 개인 메시지 입력 > **초대**를 선택합니다. **선택**을 클릭하여 사용자를 앱에 추가합니다.

      초대가 초대된 사용자에게 자동으로 이동합니다. **알림** 영역에서 성공적으로 **초대된 사용자** 메시지를 찾습니다.

9. **할당 추가**에서 **역할 선택** 클릭 > 선택된 사용자에게 적용할 역할 선택(해당하는 경우) > **확인**을 선택합니다.
10. **할당**을 클릭합니다.
 
## <a name="resend-invitations-to-guest-users"></a>게스트 사용자에게 초대 다시 보내기

게스트 사용자가 초대를 아직 사용하지 않은 경우 초대 이메일을 다시 보낼 수 있습니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **사용자 및 그룹**을 선택합니다.
4. **모든 사용자**를 선택합니다.
5. 사용자 계정을 선택합니다.
6. **관리**에서 **프로필**을 선택합니다.
7. 사용자가 초대를 아직 수락하지 않은 경우 **초대 다시 보내기** 옵션을 사용할 수 있습니다. 다시 보내려면 이 단추를 선택합니다.

   ![사용자 프로필에서 초대 옵션 다시 보내기](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> 사용자를 특정 앱으로 인도하는 초대를 다시 보내는 경우 새 초대에 담긴 링크는 대신에 사용자를 최상위 액세스 패널로 이동시킨다는 것을 이해합니다.

## <a name="next-steps"></a>다음 단계

- 비 Azure AD 관리자가 B2B 게스트 사용자를 추가할 수 있는 방법을 알려면 [정보 작업자가 B2B 공동 작업 사용자를 추가하는 방법은?](add-users-information-worker.md)을 참조합니다.
- 초대 이메일에 대한 내용은 [B2B 공동 작업 이메일 요소](invitation-email-elements.md)를 참조합니다.

