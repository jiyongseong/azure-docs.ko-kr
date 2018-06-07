---
title: Azure 리소스용 Privileged Identity Management - MFA | Microsoft Docs
description: 이 문서에서는 PIM 리소스에 Multi-Factor Authentication을 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - 리소스 역할 - MFA

Azure 리소스 역할용 PIM을 사용하면 리소스 관리자와 ID 관리자가 시간 제한 멤버 자격 및 Just-In-Time 액세스를 통해 중요한 Azure 인프라를 보호할 수 있습니다. 또한 PIM은 서로 다른 두 가지 시나리오에 대해 Azure MFA(Multi-Factor Authentication)의 선택적 적용을 제공합니다.

## <a name="require-mfa-to-activate"></a>활성화하려면 MFA 필요

리소스 관리자가 활성화하려면 Azure MFA에 성공한 역할의 적격 멤버를 요구할 수 있습니다. 이 프로세스를 통해 활성화를 요청한 사용자가 합당한 확신이 있는 당사자 본인임을 확인할 수 있습니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다. 

이 요구 사항을 적용하려면 관리되는 리소스 목록에서 리소스를 선택합니다. [개요 대시보드](pim-resource-roles-overview-dashboards.md)의 화면 오른쪽 아래에 있는 역할 목록에서 역할을 선택합니다.

또한 왼쪽 탐색 메뉴의 "역할" 또는 "역할 설정" 탭에서 역할 설정을 가져올 수 있습니다.

>[!Note]
>왼쪽 탐색 메뉴의 옵션이 회색으로 표시되고 페이지 맨 위에 "You have eligible roles that can be activated"(활성화에 적합한 역할이 있습니다.)라는 배너가 표시되는 경우에는 활성 관리자가 아니며 계속하기 전에 [활성화](pim-resource-roles-activate-your-roles.md)해야 합니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

역할의 멤버 자격이 표시되면 화면 맨 위의 표시줄에서 "역할 설정"을 선택하여 "역할 설정 정보"를 엽니다.

위쪽에서 **편집** 단추를 클릭하여 역할 설정을 수정합니다.

**활성화** 아래 섹션에서 **Require Multi-Factor Authentication to activate**(활성화하려면 Multi-Factor Authentication 필요) 확인란을 선택하고 저장을 클릭합니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>할당에 MFA 필요

경우에 따라 리소스 관리자는 멤버를 짧은 기간(예: 하루) 동안 역할에 할당하고 할당된 멤버가 활성화를 요청할 필요가 없도록 설정할 수 있습니다. 이 시나리오에서는 멤버가 자신의 역할 할당을 사용하는 경우 PIM이 MFA를 적용할 수 없습니다. 해당 멤버가 할당된 순간부터 역할에서 이미 활성 상태이기 때문입니다.

할당을 수행하는 리소스 관리자가 자신이 말하는 본인이 맞는지 확인하기 위해 할당에 MFA를 적용할 수 있습니다.

동일한 역할 설정 세부 정보 화면에서 "Require Multi-Factor Authentication on assignment"(할당 시 Multi-Factor Authentication 필요) 확인란을 선택합니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>다음 단계

[활성화하려면 승인 필요](pim-resource-roles-approval-workflow.md)

[감사 로그 사용](pim-resource-roles-use-the-audit-log.md)



