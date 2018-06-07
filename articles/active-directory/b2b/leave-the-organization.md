---
title: 게스트 사용자로써 조직 나가기 - Azure Active Directory | Microsoft Docs
description: Azure AD B2B 게스트 사용자가 액세스 패널을 사용하여 조직을 나갈 수 있는 방법을 보여줍니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 3415989cb6cec68f95e7d317671c31ba9bd231e9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267192"
---
# <a name="leave-an-organization-as-a-guest-user"></a>게스트 사용자로써 조직 나가기

Azure AD(Azure Active Directory) B2B 게스트 사용자가 더 이상 해당 조직의 앱을 사용하거나 연결을 유지하지 않아도 되는 경우 언제든지 조직을 나가도록 결정할 수 있습니다. 사용자는 관리자에게 문의하지 않고도 직접 조직을 나갈 수 있습니다.

## <a name="leave-an-organization"></a>조직 나가기

[액세스 패널](https://myapps.microsoft.com)에 로그인한 사용자로써 조직을 나가려면 다음을 수행합니다.

1. 나가려는 조직에 로그인되어 있지 않은 경우 오른쪽 위 모서리에서 이름을 선택하고 나가려는 조직을 클릭합니다.
2. 오른쪽 위 모서리에서 이름을 선택합니다.
3. **조직** 옆에 있는 설정 아이콘(톱니 모양)을 선택합니다.
 
   ![액세스 패널에서 사용자 설정을 보여주는 스크린샷](media/leave-the-organization/UserSettings.png) 

3. **조직**에서 나가려는 조직을 찾아 **조직 나가기**를 선택합니다.

   ![사용자 인터페이스에서 조직 나가기 옵션을 보여주는 스크린샷](media/leave-the-organization/LeaveOrg.png)

4. 확인을 묻는 메시지가 나타나면 **나가기**를 선택합니다. 

## <a name="account-removal"></a>계정 제거

사용자가 조직을 나가면 사용자 계정은 디렉터리에서 "일시 삭제"됩니다. 기본적으로 사용자 개체는 Azure AD에서 **삭제된 사용자** 영역으로 이동하지만 30일 동안 영구적으로 삭제되지 않습니다. 이 일시 삭제를 사용하면 사용자가 30일 기간 내에 계정을 복원하도록 요청하는 경우 관리자가 사용자 계정(그룹 및 사용 권한 포함)을 복원할 수 있습니다.

원하는 경우 테넌트 관리자는 30일의 기간 중 언제든지 계정을 영구적으로 삭제할 수 있습니다. 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**를 선택합니다.
2. **관리**에서 **사용자**를 선택합니다.
3. **삭제된 사용자**를 선택합니다.
4. 삭제된 사용자 옆의 확인란을 선택한 다음, **영구적으로 삭제**를 선택합니다.

사용자를 영구적으로 삭제하는 경우 이 작업은 되돌릴 수 없습니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>다음 단계

- Azure AD B2B의 개요는 [Azure AD B2B 공동 작업이란?](what-is-b2b.md)을 참조하세요.



