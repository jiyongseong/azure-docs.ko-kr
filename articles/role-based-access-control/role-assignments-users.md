---
title: Azure 리소스 액세스 권한 할당 보기 | Microsoft Docs
description: Azure Portal에서 모든 사용자 또는 그룹에 대한 모든 역할 기반 Access Control 할당 보기 및 관리
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: rolyon
ms.openlocfilehash: 94662c99ace749f50deccf4c8cbcd9d42fee75e1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>Azure Portal에서 사용자 및 그룹에 대한 액세스 권한 할당 보기
> [!div class="op_single_selector"]
> * [사용자 또는 그룹에 따른 액세스 관리](role-assignments-users.md)
> * [리소스에 따른 액세스 관리](role-assignments-portal.md)

Azure AD(Azure Active Directory)의 RBAC(역할 기반 액세스 제어)로 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 

두 가지 방법으로 사용 권한을 제한할 수 있기 때문에 RBAC를 사용하여 할당된 액세스 권한은 세분화됩니다.

* **범위:** RBAC 역할 할당은 특정 구독, 리소스 그룹 또는 리소스로 범위가 지정됩니다. 단일 리소스에 대한 액세스 권한을 가진 사용자는 동일한 구독에서 다른 리소스에 액세스할 수 없습니다.
* **역할:** 할당의 범위 내에서 역할을 할당하면 액세스 권한이 더 좁아집니다. 역할은 소유자와 같이 높은 수준일 수도 있고 가상 머신 판독기와 같이 특정될 수도 있습니다.

역할은 할당에 대한 범위인 구독, 리소스 그룹 또는 리소스 내에서만 할당될 수 있습니다. 하지만 지정된 사용자나 그룹에 대한 모든 액세스 권한 할당은 단일 위치에서만 볼 수 있습니다. 각 구독에서 최대 2000개의 역할 할당을 유지할 수 있습니다. 

[역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스를 관리](role-assignments-portal.md)하는 방법에 대해 자세히 알아봅니다.

## <a name="view-access-assignments"></a>액세스 권한 할당 보기
단일 사용자 또는 그룹에 대한 액세스 권한 할당을 살펴보려면 [Azure Portal](http://portal.azure.com)의 Azure Active directory에서 시작합니다.

1. **Azure Active Directory**를 선택합니다. 이 옵션이 탐색 목록에 표시되지 않는 경우 **모든 서비스**를 선택한 다음, 아래로 스크롤하여 **Azure Active Directory**를 찾습니다.
2. **사용자 및 그룹** 및 **모든 사용자** 또는 **모든 그룹**을 차례로 선택합니다. 이 예제에서는 개별 사용자에게 집중합니다.
    ![Azure Active Directory에서 사용자 및 그룹 관리 - 스크린샷](./media/role-assignments-users/rbac_users_groups.png)
3. 이름 또는 사용자 이름별로 사용자를 검색합니다.
4. 사용자 블레이드에서 **Azure 리소스**를 선택합니다. 해당 사용자에 대한 모든 액세스 권한 할당이 표시됩니다.

### <a name="read-permissions-to-view-assignments"></a>할당을 보는 읽기 사용 권한
이 페이지에서는 읽을 수 있는 사용 권한이 있는 액세스 권한 할당만 보여 줍니다. 예를 들어 구독 A에 대한 읽기 액세스 권한이 있으며 Azure 리소스 블레이드로 이동하여 사용자의 할당을 확인합니다. 구독 A에 대한 액세스 권한 할당을 확인할 수 있지만 구독 B에 대한 액세스 권한 할당이 있는지 표시되지 않습니다.

## <a name="delete-access-assignments"></a>액세스 권한 할당 삭제
이 블레이드에서 사용자 또는 그룹에 직접 할당된 액세스 권한 할당을 삭제할 수 있습니다. 액세스 권한 할당이 부모 그룹에서 상속된 경우 리소스 또는 구독으로 이동하고 거기에서 할당을 관리해야 합니다.

1. 사용자 또는 그룹에 대한 모든 액세스 권한 할당 목록에서 삭제하려는 항목을 선택합니다.
2. **제거** 및 **예**를 차례로 선택하고 확인합니다.
    ![액세스 권한 할당 제거 - 스크린샷](./media/role-assignments-users/delete_assignment.png)

## <a name="next-steps"></a>다음 단계

* [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스를 관리](role-assignments-portal.md)로 역할 기반 Access Control 시작
* [RBAC 기본 제공 역할](built-in-roles.md)

