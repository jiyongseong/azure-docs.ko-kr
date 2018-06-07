---
title: Privileged Identity Management를 사용하여 Azure 리소스에 대한 역할 활성화 | Microsoft Docs
description: PIM의 역할을 활성화하는 방법을 설명합니다.
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
ms.openlocfilehash: a985e67cc566cc45b3ee6b8dc98e91a8f34abd1b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151113"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management를 사용하여 Azure 리소스에 대한 역할 활성화
PIM(Privileged Identity Management)은 Azure 리소스에 대한 역할을 활성화하는 새로운 환경을 소개합니다. 적격 역할 멤버는 미래의 날짜 및 시간에 대한 활성화를 예약할 수 있습니다. 또한 최대(관리자로 구성됨) 내에서 특정 활성화 기간을 선택할 수도 있습니다. 자세한 내용은 [Azure AD Privileged Identity Management에서 역할을 활성화하거나 비활성화하는 방법](../active-directory-privileged-identity-management-how-to-activate-role.md)을 참조하세요.

## <a name="activate-roles"></a>역할 활성화
왼쪽 창에서 **내 역할** 섹션으로 이동합니다. 활성화하려는 역할에 대해 **활성화**를 선택합니다.

!["내 역할" 창에서 "적격 역할" 탭입니다.](media/azure-pim-resource-rbac/rbac-roles.png)

**활성화** 메뉴에서 시작 날짜와 시간을 입력하여 해당 역할을 활성화합니다. 선택적으로 활성화 기간(역할이 활성 상태로 유지되는 기간)을 줄이고 필요한 경우 근거를 입력합니다. 그런 다음, **활성화**를 선택합니다.

시작 날짜와 시간이 수정되지 않는 경우 해당 역할이 몇 초 이내에 활성화됩니다. **내 역할** 창에서 배너 메시지는 역할이 활성화를 위해 대기 중임을 표시합니다. 이 메시지를 지우려면 새로 고침 단추를 선택합니다.

![승인 보류 중에 대한 배너 메시지 및 알림이 있는 "내 역할" 창](media/azure-pim-resource-rbac/rbac-activate-notification.png)

활성화가 미래의 날짜와 시간으로 예약되면 보류 중인 요청이 왼쪽 창의 **보류 중인 요청** 탭에 표시됩니다. 역할 활성화가 더 이상 필요하지 않는 경우 **취소** 단추를 선택하여 요청을 취소할 수 있습니다.

!["취소" 단추가 있는 보류 중인 요청의 목록](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Just Enough Administration 사례 적용

Azure 리소스용 PIM을 사용하면 리소스 역할 할당에 대한 JEA(Just Enough Administration) 모범 사례를 쉽게 적용할 수 있습니다. Azure 구독 또는 리소스 그룹에 할당된 사용자 및 그룹 구성원은 축소된 범위에서 기존 역할 할당을 활성화할 수 있습니다. 

검색 페이지에서 관리해야 하는 종속 리소스를 찾습니다.

![리소스 선택](media/azure-pim-resource-rbac/azure-resources-02.png)

왼쪽 창에서 **내 역할**을 선택하고 활성화할 적절한 역할을 선택합니다. 리소스 그룹이 아닌 구독에 역할이 할당되었으므로 할당 유형이 **상속됨**입니다.

![할당 형식이 강조 표시된 적격 역할 할당의 목록](media/azure-pim-resource-rbac/my-roles-02.png)
