---
title: Privileged Identity Management를 사용하여 Azure 리소스에 대한 역할 할당 | Microsoft Docs
description: PIM의 역할을 할당하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 501f063992d2f5c7769a5c9059b346aa2b5c2bb4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149375"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management를 사용하여 Azure 리소스에 대한 역할 할당

## <a name="assign-roles"></a>역할 할당

**역할** 창을 볼 때 역할에 사용자 또는 그룹을 할당하려면 역할을 선택한 후 **사용자 추가**를 선택합니다. 

![“사용자 추가” 단추가 있는 “역할” 창](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

**멤버** 창에서 **사용자 추가**를 선택할 수도 있습니다.

![“사용자 추가” 단추가 있는 “멤버” 창](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


**멤버** 창에서 사용자 또는 그룹을 추가하는 경우 다음을 수행해야 합니다. 

1. 사용자 또는 그룹을 선택하기 전에 **역할 선택** 창에서 역할을 선택합니다.

   ![“역할 선택” 창](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. 디렉터리에서 사용자 또는 그룹을 선택합니다.

3. 드롭다운 메뉴에서 적절한 할당 유형을 선택합니다. 

   - **Just-In-Time:** 지정된 기간 동안 또는 무기한(역할 설정에서 구성한 경우)으로 사용자 또는 그룹 구성원에게 역할에 대해 적격하지만 비영구적인 액세스 권한을 제공합니다. 
   - **직접:** 사용자 또는 그룹 구성원이 역할 할당을 활성화할 필요가 없습니다(영구적 액세스라고 함). 작업 완료 시 액세스가 더 이상 필요하지 않은 단기적인 사용 사례에서는 직접 할당을 사용하는 것이 좋습니다. 대기 근무 또는 시간이 중요한 활동을 예로 들 수 있습니다.

4. 할당이 영구적이어야 하는 경우(Just-In-Time 할당에 대해 영구적으로 자격이 있거나 직접 할당이 영구적으로 활성화된) **할당 유형** 상자 아래의 확인란을 선택합니다.

   ![“할당 유형” 상자 및 관련 확인란이 있는 “멤버 자격 설정” 창](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >다른 관리자가 역할 설정에서 각 할당 유형에 대해 최대 할당 기간을 지정한 경우에는 해당 확인란을 수정할 수 없습니다.

   특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 상자를 수정합니다.

   ![시작 날짜, 시작 시간, 종료 날짜, 종료 시간을 지정하는 상자가 있는 “멤버 자격 설정” 창](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>역할 할당 관리

관리자는 왼쪽 창에서 **역할** 또는 **멤버**를 선택하여 역할 할당을 관리할 수 있습니다. **역할**을 선택하면 관리자가 자신의 관리 작업을 특정 역할에 지정할 수 있습니다. **멤버**를 선택하면 리소스에 대한 모든 사용자 및 그룹 역할 할당이 표시됩니다.

![“역할” 창](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![“멤버” 창](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
활성화가 보류 중인 역할이 있는 경우 멤버 자격을 볼 때 알림 배너가 창의 위쪽에 표시됩니다.


## <a name="modify-existing-assignments"></a>기존 할당 수정

사용자/그룹 세부 정보 보기에서 기존 할당을 수정하려면 작업 모음에서 **설정 변경**을 선택합니다. 할당 유형을 **Just-In-Time** 또 **직접**으로 변경합니다.

![“설정 변경” 단추가 있는 “사용자 세부 정보” 창](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
