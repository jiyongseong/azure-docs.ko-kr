---
title: Privileged Identity Management를 사용하여 Azure 리소스 검색 및 관리 | Microsoft Docs
description: PIM을 사용하여 Azure 리소스를 보호하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 51a10ea164e8bd7650ad2823281d9ed6a4c91915
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151283"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management를 사용하여 Azure 리소스 검색 및 관리

Azure AD(Azure Active Directory)에서 PIM(Privileged Identity Management)을 사용하는 경우 Azure 리소스를 검색하고 관리하는 방법에 대해 알아봅니다. 이 내용은 관리자 리소스 보호에 이미 PIM을 사용하고 있는 조직 및 프로덕션 리소스를 보호하려는 구독 소유자에게 유용할 수 있습니다.

먼저 Azure 리소스에 대한 PIM을 설정할 때 PIM에서 보호할 리소스를 검색하고 선택해야 합니다. PIM으로 관리할 수 있는 리소스 수에는 제한이 없습니다. 그러나 가장 중요한 리소스, 즉 프로덕션 리소스부터 시작하는 것이 좋습니다.

> [!NOTE]
> PIM을 사용하여 관리할 구독 리소스를 검색하고 선택할 수 있습니다. PIM에서 구독을 관리할 때 구독의 자식 리소스를 관리할 수도 있습니다.

## <a name="discover-resources"></a>리소스 검색

Azure Portal에서 **Privileged Identity Management** 창으로 이동합니다. 왼쪽 메뉴의 **관리** 섹션에서 **Azure 리소스**를 선택합니다.

![“Privileged Identity Management - Azure 리소스” 창](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

처음으로 Azure 리소스에 대해 PIM을 사용하는 경우에는 먼저 검색을 실행하여 관리할 리소스를 찾습니다. **리소스 검색** 창에서 **리소스 검색** 단추를 선택하여 검색 환경을 실행합니다.

![“리소스 검색” 창](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

조직에서 다른 리소스나 디렉터리 관리자가 이미 PIM을 사용하여 Azure 리소스를 관리하고 있거나 리소스에 대한 적격 역할 할당이 있는 경우 **리소스를 검색하거나 계속할 적격 역할 할당을 활성화합니다.** 라는 메시지가 목록 보기에 표시됩니다. 

![“Privileged Identity Manager - Azure 리소스” 창의 “리소스 검색” 단추](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

상단 메뉴나 창 중간에서 **리소스 검색** 단추를 선택하면 관리할 수 있는 구독의 목록이 표시됩니다. 강조 표시된 구독은 이미 PIM에 의해 이미 보호되는 구독입니다.

> [!NOTE]
> 다른 리소스 관리자가 PIM 설정을 제거하지 않도록 방지하기 위해 구독을 관리 상태로 설정하고 나면 미관리로 전환할 수 없게 됩니다.

![“Azure 리소스 - 검색” 창](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

**리소스** 열에서 PIM으로 보호할 구독 위에 마우스를 놓습니다. 그런 다음, 리소스 이름 왼쪽에 있는 확인란을 선택합니다. 한 번에 여러 구독을 선택할 수 있습니다.

![“Azure 리소스 - 검색” 창의 리소스 목록](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

온보딩 프로세스를 시작하려면 상단 메뉴에서 **리소스 관리**를 선택합니다.

![“Azure 리소스 - 검색” 창의 “리소스 관리” 단추](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

선택한 리소스는 이제 PIM에 의해 관리됩니다. 검색 화면을 닫으려면 오른쪽 상단 모서리에서 **X**를 선택합니다. PIM 설정 관리 및 멤버 할당을 시작하려면 **Privileged Identity Management - Azure 리소스** 창 상단에 있는 메뉴에서 **새로 고침** 단추를 선택합니다.

![“Privileged Identity Management - Azure 리소스” 창 상단 메뉴의 “새로 고침” 단추](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>다음 단계

- [역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [PIM에서 역할 할당](pim-resource-roles-assign-roles.md)
