---
title: Azure Active Directory에서 관리자 역할에 사용자 할당 | Microsoft Docs
description: Azure Active Directory에서 사용자 관리 정보를 변경하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 61662acb10a6f2085d297eae473e1330c619d48d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할에 사용자 할당
이 문서는 Azure AD(Azure Active Directory)에서 사용자에게 관리 역할을 할당하는 방법을 설명합니다. 조직 내에서 새 사용자 추가에 대한 자세한 내용은 [Azure Active Directory에 새 사용자 추가](active-directory-users-create-azure-portal.md)를 참조하세요. 기본적으로 추가된 사용자에게는 관리자 권한이 없지만 언제든 역할을 할당할 수 있습니다.

## <a name="assign-a-role-to-a-user"></a>사용자에게 역할 할당
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **사용자 및 그룹**을 선택합니다.

   ![사용자 관리 열기](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. **모든 사용자**를 선택합니다.
  
  ![모든 사용자 그룹 열기](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. 목록에서 사용자를 선택합니다.
5. 선택한 사용자에 대해 **디렉터리 역할**을 선택한 다음 **디렉터리 역할** 목록의 역할에 사용자를 할당합니다. 사용자 및 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md)을 참조하세요.

      ![역할에 사용자 할당](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계
* [빠른 시작: Azure Active Directory에서 사용자 추가 또는 삭제](add-users-azure-active-directory.md)
* [사용자 프로필 관리](active-directory-users-profile-azure-portal.md)
* [다른 디렉터리에서 게스트 사용자 추가](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Azure AD의 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md)
* [삭제된 사용자 복원](active-directory-users-restore.md)
