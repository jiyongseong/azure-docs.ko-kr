---
title: Privileged Identity Management의 Azure 리소스 역할에 대한 승인 워크플로 | Microsoft Docs
description: Azure 리소스에 대한 승인 워크플로 프로세스에 대해 설명합니다.
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
ms.openlocfilehash: 7781c858a5c0e4db8593df0cf77b868b6fd23622
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151096"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management의 Azure 리소스 역할에 대한 승인 워크플로

관리자는 Azure 리소스 역할에 대한 PIM(Privileged Identity Management)의 승인 워크플로를 통해 중요한 리소스에 대한 액세스 권한을 추가로 보호하거나 제한할 수 있습니다. 즉, 관리자는 역할 할당을 활성화하기 위해 승인을 요구할 수 있습니다. 

리소스 계층의 개념은 Azure 리소스 역할에 고유한 개념입니다. 이 계층을 사용하면 부모 리소스 개체에서 부모 컨테이너 내의 모든 자식 리소스로 역할 할당을 상속할 수 있습니다. 

예를 들어, Bob의 경우 리소스 관리자가 PIM을 사용하여 Alice를 Contoso 구독의 소유자 역할에 대한 적격 멤버로 할당합니다. 이 할당에서 Alice는 Contoso 구독 내 모든 리소스 그룹 컨테이너의 적격 소유자입니다. 또한 Alice는 구독의 각 리소스 그룹 내에서 가상 머신과 같은 모든 리소스의 적격 소유자이기도 합니다. 

Contoso 구독에 Fabrikam Test, Fabrikam Dev 및 Fabrikam Prod라는 세 개의 리소스 그룹이 있다고 가정하겠습니다. 이러한 각 리소스 그룹에는 단일 가상 머신이 포함됩니다.

PIM 설정은 리소스의 각 역할에 대해 구성됩니다. 할당과 달리 이 설정은 상속되지 않고 리소스 역할에 엄격히 적용됩니다. [적격 할당 및 리소스 표시 유형에 대해 자세히 알아보기](pim-resource-roles-eligible-visibility.md)

계속 이의 예제로 설명하면, Bob에서는 Contoso 구독 요청 승인의 소유자 역할을 포함하는 모든 멤버를 활성화하도록 요구하는 데 PIM을 사용합니다. Fabrikam Prod 리소스 그룹 내에 포함된 리소스를 보호하려면 Bob에는 이 리소스의 소유자 역할 멤버에 대한 승인이 필요합니다. Fabrikam Test 및 Dev Fabrikam의 소유자 역할은 승인을 활성화하지 않아도 됩니다.

Alice가 Contoso 구독에 대해 소유자 역할을 활성화하도록 요청하는 경우 승인자는 역할이 활성 상태가 되기 전에 해당 요청을 승인하거나 거부해야 합니다. Alice가 Fabrikam Prod 리소스 그룹으로 [활성화 범위를 지정](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices)하려는 경우에도 승인자는 이 요청을 승인하거나 거부해야 합니다. 그러나 Alice가 Fabrikam Test 또는 Fabrikam Dev로 활성화 범위를 지정하려는 경우 승인이 필요하지 않습니다.

승인 워크플로는 역할의 일부 멤버에게만 필요할 수 있습니다. Azure 구독에서 실행될 응용 프로그램 개발을 지원하기 위해 조직이 일부 계약 직원을 채용하는 시나리오를 가정합니다. 사용자가 리소스 관리자인 경우, 승인할 필요 없이 적격 액세스 권한이 있는 직원을 선호하지만 계약 직원은 승인을 요청해야 합니다. 계약 직원에 대해서만 승인 워크플로를 구성하려면 지원에게 할당된 역할과 동일한 권한으로 사용자 지정 역할을 만들 수 있습니다. 사용자 정의 역할을 활성화하려면 승인이 필요할 수 있습니다. [사용자 지정 역할을 자세히 알아보세요](pim-resource-roles-custom-role-policy.md).

승인 워크플로를 구성하고 요청을 승인하거나 거부할 수 있는 사용자를 지정하려면 다음 단계를 따릅니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

1. Azure Portal에서 PIM으로 이동하고, 목록에서 리소스를 선택합니다.

   ![리소스가 선택된 “Azure 리소스” 창](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. 왼쪽 창에서 **역할 설정**을 선택합니다.

3. 역할을 검색하여 선택한 후 **편집**을 선택하여 설정을 수정합니다.

   ![운영자 역할 “편집” 단추](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. **활성화** 섹션에서 **Require approval to activate**(활성화하려면 승인 필요) 확인란을 선택합니다.

   ![역할 설정의 “활성화” 섹션](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>승인자 지정

**승인자 선택**을 클릭하여 **사용자 또는 그룹 선택** 창을 엽니다.

>[!NOTE]
>설정을 업데이트할 하나 이상의 사용자 또는 그룹을 선택해야 합니다. 기본 승인자가 없습니다.

리소스 관리자는 사용자 및 그룹의 조합을 승인자 목록에 추가할 수 있습니다. 

![사용자가 선택된 “사용자 또는 그룹 선택” 창](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>활성화할 승인 요청

승인을 요청하면 활성화하기 위해 멤버가 수행해야 하는 프로시저에 영향을 주지 않습니다. [역할을 활성화하는 단계를 검토합니다](pim-resource-roles-activate-your-roles.md).

멤버가 승인이 필요한 역할의 활성화를 요청하고 역할이 더 이상 필요하지 않은 경우, 멤버는 PIM의 요청을 취소할 수 있습니다.

취소하려면 PIM로 이동하고, **내 요청**을 선택합니다. 요청을 찾아 **취소를** 선택합니다.

![“내 요청” 창](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>요청 승인 또는 거부

요청을 승인하거나 거부하려면 승인자는 목록의 멤버여야 합니다. 

1. PIM에 있는 왼쪽 메뉴의 탭에서 **요청 승인**을 선택하고 요청을 찾습니다.

   ![“요청 승인” 창](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. 요청을 선택하고, 결정에 대한 근거를 입력하고, **승인** 또는 **거부**를 선택합니다. 그러면 요청이 해결됩니다.

   ![자세한 정보가 포함된 선택된 요청](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>워크플로 알림

다음은 워크플로 알림에 대한 몇 가지 사실입니다.

- 역할에 대한 요청이 해당 검토를 보류할 때 승인자 목록의 모든 멤버는 이메일을 통해 알림을 받습니다. 메일 알림에는 승인자를 승인하거나 거부할 수 있는 요청에 대한 직접 링크가 포함됩니다.
- 요청은 승인하거나 거부하는 목록의 첫 번째 멤버에 의해 확인됩니다. 
- 승인자가 요청에 응답하는 경우 승인자 목록의 모든 멤버에게 작업의 알림이 표시됩니다. 
- 승인된 멤버가 해당 역할에서 활성화될 때 리소스 관리자에게 알림이 표시됩니다. 

>[!Note]
>승인된 멤버를 활성화하지 않아야 한다고 판단하는 경우 리소스 관리자는 PIM의 활성 역할 할당을 제거할 수 있습니다. 리소스 관리자가 승인자 목록의 멤버가 아니면 보류 중인 요청의 알림을 받지 않지만 PIM에서 보류 중인 요청을 확인하여 모든 사용자의 보류 중인 요청을 보고 취소할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[PIM 설정을 고유 사용자 그룹에 적용](pim-resource-roles-custom-role-policy.md)
