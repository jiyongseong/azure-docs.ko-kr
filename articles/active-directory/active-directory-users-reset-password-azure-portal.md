---
title: "Azure Active Directory에서 암호 다시 설정 | Microsoft Docs"
description: "관리자가 Azure Active Directory에서 사용자 암호 다시 설정을 시작했습니다."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: 6d01dff567e49b602e98f717dace4dc75abecb4c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Azure Active Directory에서 사용자 암호 재설정

사용자의 암호를 잊어버리거나 잠김 등의 문제가 발생한 경우 관리자는 사용자 암호를 다시 설정해야 할 수 있습니다. 다음 단계는 사용자의 암호 다시 설정 과정을 안내합니다.

## <a name="how-to-reset-the-password-for-a-user"></a>사용자에 대한 암호를 다시 설정하는 방법

1. 사용자 암호 다시 설정을 위한 디렉터리 권한이 있는 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **사용자 및 그룹** > **모든 사용자**를 선택합니다.
3. 암호를 다시 설정할 사용자를 선택합니다.
2. 선택한 사용자에 대해 **암호 다시 설정**을 선택합니다.

    ![Azure AD에서 사용자 프로필의 사용자 암호 다시 설정](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. **암호 재설정**에서 **암호 재설정**을 선택합니다.
7. 사용자에게 제공할 수 있는 임시 암호가 표시됩니다. 다음에 로그온할 때 암호를 변경하라는 메시지가 사용자에게 표시됩니다. 

   > [!NOTE]
   > 이 임시 암호에는 만료 시간이 없으므로 로그인하여 암호를 변경할 때까지 유효합니다. 

## <a name="next-steps"></a>다음 단계
* [사용자 추가](active-directory-users-create-azure-portal.md)
* [사용자에게 관리자 역할 할당](active-directory-users-assign-role-azure-portal.md)
* [사용자 프로필 관리](active-directory-users-profile-azure-portal.md)
* [Azure AD에서 사용자 삭제](active-directory-users-delete-user-azure-portal.md)
