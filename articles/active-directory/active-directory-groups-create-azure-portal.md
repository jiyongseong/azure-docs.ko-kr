---
title: Azure Active Directory에서 사용자에 대한 그룹 만들기 | Microsoft Docs
description: Azure Active Directory에서 그룹을 만들고 그룹에 멤버를 추가하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f1722ab753287068e65e3d386e1d1a1bf40d742
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Azure Active Directory에서 그룹 만들기 및 멤버 추가
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

이 문서는 Azure Active Directory에서 새 그룹을 만들고 채우는 방법을 설명합니다. 그룹을 사용하여 한 번에 많은 사용자 또는 장치에 라이선스 또는 사용 권한을 할당하는 등 관리 작업을 수행합니다.

## <a name="how-do-i-create-a-group"></a>그룹을 만드는 방법
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음, **입력**을 선택합니다.

   ![사용자 관리 열기](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. **사용자 및 그룹** 블레이드에서 **모든 그룹**을 선택합니다.

   ![그룹 블레이드 열기](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. **사용자 및 그룹 - 모든 그룹** 블레이드에서 **추가** 명령을 선택합니다.

   ![추가 명령 선택](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. **그룹** 블레이드에서 그룹에 대한 이름 및 설명을 추가합니다.
6. 그룹에 추가할 멤버를 선택하려면 **멤버 자격 유형** 상자에서 **할당**을 선택한 다음 **멤버**를 선택합니다. 그룹의 구성원을 동적으로 관리하는 방법에 대한 자세한 내용은 [특성을 사용하여 그룹 멤버 자격에 대한 고급 규칙 만들기](active-directory-groups-dynamic-membership-azure-portal.md)를 참조하세요.

   ![추가할 멤버 선택](./media/active-directory-groups-create-azure-portal/select-members.png)
7. **멤버** 블레이드에서 그룹에 추가할 하나 이상의 사용자 또는 장치를 선택하고 블레이드 아래쪽의 **선택** 단추를 선택하여 그룹에 추가합니다. **사용자** 상자는 사용자 또는 장치 이름 부분에 대한 항목 일치를 기반으로 한 표시를 필터링합니다. 와일드카드 문자는 해당 상자에서 허용되지 않습니다.
8. 그룹에 멤버 추가를 마치면 **그룹** 블레이드에서 **만들기**를 선택합니다.    

   ![그룹 확인 만들기](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](active-directory-groups-view-azure-portal.md)
* [그룹의 설정 관리](active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 관리](active-directory-groups-members-azure-portal.md)
* [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-membership-azure-portal.md)
