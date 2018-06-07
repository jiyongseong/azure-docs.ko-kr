---
title: "Azure Privileged Identity Management 승인 워크플로 | Microsoft Docs"
description: "PIM(Privileged Identity Management)의 승인 워크플로에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: f30dd06417d9ce4d71c88ba170a4f3169b5fccd7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="approvals"></a>승인

## <a name="overview"></a>개요

PIM(Privileged Identity Management)에 대한 승인을 사용하면, 활성화에 대한 승인이 필요한 역할을 구성하고 하나 이상의 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다. 역할을 구성하고 승인자를 선택하는 방법을 알아보려면 계속 진행하세요.


## <a name="new-terminology"></a>새 용어

*적격 역할 사용자* – 적법한 Azure AD 역할(역할에 대한 활성화 필요)로 할당된 조직 내 사용자입니다.

*위임된 승인자* – Azure AD 내에서 역할을 활성화하도록 요청을 승인하는 작업을 담당하는 하나 이상의 개인 또는 그룹입니다.

## <a name="scenarios"></a>시나리오

비공개 미리 보기에서 지원하는 시나리오는 다음과 같습니다.

**PRA(권한 있는 역할 관리자)는 다음을 수행할 수 있습니다.**

-   [특정 역할에 대한 승인을 사용하도록 설정](#enable-approval-for-specific-roles)

-   [요청을 승인할 승인자 사용자 및/또는 그룹 지정](#specify-approver-users-and/or-groups-to-approve-requests)

-   [모든 권한 있는 역할에 대한 요청 및 승인 기록 보기](#view-request-and-approval-history-for-all-privileged-roles)

**지정된 승인자는 다음을 수행할 수 있습니다.**

-   [보류 중인 승인(요청) 보기](#view-pending-approvals-requests)

-   [역할 상승 요청(단일 및/또는 대량) 승인 또는 거부](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [승인/거부에 대한 근거 제공](#provide-justification-for-my-approval/rejection) 

**적격 역할 사용자는 다음을 수행할 수 있습니다.**

-   [승인이 필요한 역할의 활성화 요청](#request-activation-of-a-role-that-requires-approval)

-   [활성화 요청 상태 보기](#view-the-status-of-your-request-to-activate)

-   [활성화가 승인되면 Azure AD에서 작업 수행](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>탐색

승인을 지원하도록 탐색을 업데이트했습니다.

![](media/azure-ad-pim-approval-workflow/image001.png)

PIM 및 새로운 승인 설명서에 대한 정보는 기본 방문 페이지에서 편리하게 액세스할 수 있습니다.

![](media/azure-ad-pim-approval-workflow/image002.png)

또한 PIM의 모든 사용자를 위해 새로운 섹션, 즉 '내 감사 기록'을 추가했습니다. 여기서는 ID와 관련된 모든 정보를 찾을 수 있습니다. 여기에는 모든 보류 중 및 완료된 요청, 해결 요청에 대한 모든 결정 및 과거의 모든 역할 활성화가 편리하게 한 곳에 모여 있습니다.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>특정 역할에 대한 승인을 사용하도록 설정

특정 역할에 대한 승인을 사용하려면 먼저 왼쪽 탐색 영역에서 [디렉터리 역할]을 선택합니다.

![](media/azure-ad-pim-approval-workflow/image004.png)

[디렉터리 역할] 왼쪽 탐색 영역에서 설정을 찾아서 선택합니다.

![](media/azure-ad-pim-approval-workflow/image006.png)

[권한 있는 역할]을 선택합니다.

![](media/azure-ad-pim-approval-workflow/image009.png)

[승인 필요] 섹션에서 "사용"을 선택합니다.

![](media/azure-ad-pim-approval-workflow/image011.png)

활성화되면 블레이드가 확장되어 다음 세부 정보를 표시합니다.

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
승인자를 지정하지 않으면 PRA가 기본 승인자가 됩니다. PRA는 이 역할에 대한 모든 활성화 요청을 승인해야 합니다.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>요청을 승인할 승인자 사용자 및/또는 그룹 지정

승인을 위임하려면 "승인자 선택" 옵션을 클릭합니다.

![](media/azure-ad-pim-approval-workflow/image015.png)

[승인자 선택] 블레이드가 로드되면 위쪽의 검색 표시줄을 사용하거나 미리 채워진 목록에서 선택하여 특정 사용자 또는 그룹을 검색한 다음 완료되면 "선택"을 클릭하면 됩니다.

![](media/azure-ad-pim-approval-workflow/image017.png)

참고: 한 번에 여러 사용자 또는 그룹을 선택할 수 있습니다.

선택한 항목이 아래와 같이 선택한 승인자 목록에 표시됩니다.

![](media/azure-ad-pim-approval-workflow/image019.png)

승인자를 제거하려면 해당 이름 옆에 있는 [제거] 단추를 클릭하기만 하면 됩니다.

추가 승인자를 추가하려면 프로세스를 반복합니다.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>모든 권한 있는 역할에 대한 요청 및 승인 기록 보기

모든 권한 있는 역할에 대한 요청 및 승인 기록을 보려면 대시보드에서 [감사 기록]을 선택합니다.

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
[작업]별로 데이터를 정렬하고 "활성화 승인됨"을 찾습니다.

### <a name="view-pending-approvals-requests"></a>보류 중인 승인(요청) 보기

요청 승인이 보류 중일 때는 위임된 승인자가 전자 메일 알림을 받게 됩니다. PIM 포털에서 이러한 요청을 보려면 새로운 탐색 창의 대시보드에서 왼쪽 탐색 모음의 "보류 중 승인 요청" 탭을 선택합니다.

![](media/azure-ad-pim-approval-workflow/image023.png)

여기에서 승인 보류 중인 요청 목록이 표시됩니다.

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>역할 상승 요청(단일 및/또는 대량) 승인 또는 거부

승인하거나 거부하려는 요청을 선택하고 결정에 해당하는 작업 모음의 단추를 클릭합니다.

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>승인/거부에 대한 근거 제공

이 경우 한 번에 여러 요청을 승인하거나 거부할 수 있는 새 블레이드가 열립니다. 결정에 대한 근거를 입력하고, 아래쪽 또는 블레이드에서 승인(또는 거부)를 클릭합니다.

![](media/azure-ad-pim-approval-workflow/image029.png)

요청 프로세스가 완료되면 상태 기호에 사용자가 내린 결정이 반영됩니다(이 예에서는 승인 결정임).

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>승인이 필요한 역할의 활성화 요청

승인이 필요한 역할을 활성화하도록 요청하면 역할 활성화 프로세스가 동일하게 유지되기 때문에 이전 PIM 탐색 또는 새 탐색에서 시작할 수 있습니다. 활성화할 역할 목록에서 역할을 선택하기만 하면 됩니다.

![](media/azure-ad-pim-approval-workflow/image033.png)

권한 있는 역할에 Multi-Factor Authentication이 필요한 경우 먼저 해당 작업을 완료하도록 요구하는 메시지가 표시됩니다.

![](media/azure-ad-pim-approval-workflow/image035.png)

해당 작업이 완료되면 [활성화]를 클릭하고 필요에 따라 근거를 제공합니다.

![](media/azure-ad-pim-approval-workflow/image037.png)

승인 보류 중인 요청이라는 알림이 요청자에게 표시됩니다.

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>활성화 요청 상태 보기

보류 중인 활성화 요청의 상태 보기는 새 탐색에서 액세스해야 합니다. 왼쪽 탐색 모음에서 '내 요청' 탭을 선택합니다.

![](media/azure-ad-pim-approval-workflow/image041.png)

요청 상태는 기본적으로 "보류 중"이지만 모든 요청 또는 거부된 요청을 표시하도록 전환할 수 있습니다.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>활성화가 승인되면 Azure AD에서 작업 수행

요청이 승인되면 해당 역할이 활성화되고 이 역할을 요구하는 작업을 진행할 수 있습니다.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>다음 단계

Microsoft는 사용자의 의견을 소중하게 생각합니다. 언제든지 자유롭게 사용자의 의견이나 피드백을 Microsoft와 함께 공유해 주세요!
