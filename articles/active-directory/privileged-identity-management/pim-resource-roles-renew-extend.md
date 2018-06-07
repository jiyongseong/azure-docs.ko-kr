---
title: Azure 리소스용 Privileged Identity Management - 역할 연장 및 갱신 | Microsoft Docs
description: 이 문서에서는 PIM 리소스에 대해 Azure 리소스 역할을 연장하고 갱신하는 방법을 설명합니다.
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
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management - 리소스 역할 - 연장 또는 갱신

Azure 리소스용 PIM은 Azure 리소스에 대한 액세스 및 할당 주기를 관리하는 새로운 컨트롤을 소개합니다. 관리자는 시작 및 종료 날짜-시간 속성을 사용하여 멤버 자격을 할당할 수 있습니다. 할당 종료에 도달하면 PIM은 영향을 받는 멤버(사용자 또는 그룹)와 리소스 관리자에게 적절한 액세스가 유지되도록 이메일 알림을 보냅니다. 활동이 없어서 액세스가 연장되지 않으면 할당이 갱신되어 만료된 상태에서 최대 30일 동안 표시됩니다.

## <a name="who-can-extend-and-renew"></a>연장 및 갱신할 수 있는 사람

리소스 관리자만 역할 할당을 연장하거나 갱신할 수 있습니다. 영향을 받는 멤버는 만료 예정인 역할을 연장하도록 요청하고 이미 만료된 역할의 갱신을 요청할 수 있습니다.

## <a name="when-are-notifications-sent"></a>알림이 전송되는 시기

PIM은 만료되기 14일 전 및 하루 전에 관리자 및 역할 만료의 영향을 받는 멤버에게 이메일 알림을 보냅니다. 할당이 공식적으로 만료되면 추가 이메일이 전송됩니다. 

만료될 예정이거나 만료된 역할의 멤버가 연장이나 갱신을 요청하면 관리자에게 알림이 전송됩니다. 특정 관리자가 요청을 해결하면 다른 모든 관리자에게 해결 결정(승인 또는 거부)이 통지되고 요청한 멤버에게 결정이 통지됩니다. 

## <a name="extend-role-assignments"></a>역할 할당 연장

아래 단계에서는 역할 할당의 연장 또는 갱신을 요청, 해결 또는 관리하는 작업과 관련된 단계와 사용자 인터페이스를 간략하게 설명합니다. 

### <a name="member-extend"></a>멤버 연장

역할 할당의 멤버는 리소스의 "내 역할" 페이지에 있는 "적격" 또는 "활성" 탭과 PIM 포털의 최상위 내 역할에서 직접 만료 예정인 역할 할당을 연장하도록 요청할 수 있습니다. 멤버는 앞으로 14일 이내에 만료될 적격 및 활성(할당됨) 역할을 연장하도록 요청할 수 있습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

할당 종료 날짜-시간이 14일 이내이면 "연장" 단추가 사용자 인터페이스에서 활성 링크가 됩니다. 아래 예제에서는 현재 날짜가 3월 27일이라고 가정합니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

이 역할 할당의 연장을 요청하려면 "연장"을 클릭하여 요청 양식을 엽니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

원래 할당에 대한 정보를 보려면 "할당 세부 정보"를 펼칩니다. 연장 요청의 이유를 입력하고 "연장"을 클릭합니다.

>[!Note]
>연장이 필요한 이유와 필요한 연장 기간(알려진 경우)에 대한 세부 정보를 포함시키는 것이 좋습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

잠시 후 리소스 관리자는 연장 요청을 검토하도록 요청하는 이메일 알림을 받게 됩니다. 연장 요청이 이미 제출된 경우에는 Azure Portal 위쪽에 오류를 설명하는 알림 메시지가 표시됩니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

왼쪽 탐색 메뉴의 "보류 중인 요청" 탭에서 요청 상태를 보거나 취소할 수 있습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>관리자 승인

멤버가 역할 할당을 연장하도록 요청을 제출하면 리소스 관리자에게 요청자가 제공한 이유와 원래 할당의 세부 정보가 포함된 이메일 알림이 전송됩니다. 알림에는 관리자가 요청을 승인하거나 거부할 수 있는 직접 링크가 포함됩니다. 

관리자는 이메일의 링크를 여는 것 외에도 PIM 관리 포털로 이동하여 왼쪽 탐색 메뉴에서 "요청 승인"을 선택하여 요청을 승인하거나 거부할 수 있습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

관리자가 승인 또는 거부를 선택하면 요청의 세부 정보가 감사 로그에 대한 근거를 제공하는 필드와 함께 표시됩니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

역할 할당 연장에 대한 요청을 승인할 때 리소스 관리자는 새로운 시작 및 종료 날짜-시간 및 할당 유형을 선택할 수 있습니다. 관리자가 특정 작업을 완료하도록 제한된 액세스를 제공하려는 경우 할당 유형 변경이 필요할 수 있습니다(예: 하루). 이 예제에서는 관리자가 적격에서 활성으로 할당을 변경하여 요청자에게 활성화를 요구하지 않고도 액세스 권한을 제공할 수 있습니다.

### <a name="admin-extend"></a>관리자 연장

역할 멤버가 역할 멤버 자격 연장을 잊었거나 요청할 수 없는 경우에는 관리자가 멤버를 대신하여 할당을 연장할 수 있습니다. 역할 멤버 자격을 관리자가 연장하면 승인이 필요하지 않지만 역할 연장이 완료되면 다른 모든 관리자에게 알림이 전송됩니다.

역할 멤버 자격을 연장하려면 PIM의 리소스 역할이나 멤버 보기로 이동합니다. 연장이 필요한 멤버를 찾아서 작업 열에서 "연장"을 클릭합니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>역할 할당 갱신

개념적으로는 유사하지만 만료된 역할 할당을 갱신하는 프로세스는 멤버 및 관리자 모두에 대해 연장을 요청하는 것과 다릅니다. 아래 단계를 사용하여 멤버와 관리자가 필요한 경우 만료된 역할에 대한 액세스를 갱신할 수 있습니다.

### <a name="member-renew"></a>멤버 갱신

리소스에 더 이상 액세스할 수 없는 멤버는 PIM의 왼쪽 탐색 영역에서 내 역할로 이동하고 Azure 리소스 역할 섹션에서 "Expired roles"(만료된 역할) 탭을 선택하면 최대 30일까지 만료된 할당 내역에 액세스할 수 있습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

표시되는 역할 목록의 기본 값은 적격 할당입니다. 드롭다운을 사용하여 적격 할당 역할과 활성 할당 역할을 전환합니다.

목록에서 역할 할당에 대한 갱신을 요청하려면 "갱신" 작업을 선택하고 요청 이유를 제공합니다. 리소스 관리자가 승인이나 거부를 결정하는 데 도움이 되도록 추가 컨텍스트 이외에 기간을 제공하는 것이 좋습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

요청을 제출하면 리소스 관리자에게 역할 할당을 갱신하도록 보류 중인 요청이 통지됩니다.

### <a name="admin-approves"></a>관리자 승인

리소스 관리자는 이메일 알림의 링크를 통해 또는 Azure Portal에서 PIM에 액세스하고 왼쪽 탐색 메뉴에서 "요청 승인"을 선택하여 갱신 요청에 액세스할 수 있습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

관리자가 승인 또는 거부를 선택하면 요청의 세부 정보가 감사 로그에 대한 근거를 제공하는 필드와 함께 표시됩니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

역할 할당 갱신에 대한 요청을 승인할 때 리소스 관리자는 새로운 시작 및 종료 날짜-시간 및 할당 유형을 입력해야 합니다. 

### <a name="admin-renew"></a>관리자 갱신

리소스 관리자는 리소스의 왼쪽 탐색 메뉴에 있는 멤버 탭에서 또는 리소스 역할의 만료된 역할 탭 내에서 만료된 역할 할당을 갱신할 수 있습니다.

멤버 화면에서 만료된 역할을 선택하면 만료된 역할 할당 목록을 모두 볼 수 있습니다.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>다음 단계

[활성화하려면 승인 필요](pim-resource-roles-approval-workflow.md)

[역할 활성화](pim-resource-roles-use-the-audit-log.md)


