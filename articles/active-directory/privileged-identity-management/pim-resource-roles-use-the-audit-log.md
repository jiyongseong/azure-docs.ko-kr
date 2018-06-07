---
title: Azure 리소스에 대한 Privileged Identity Management - 리소스 감사 | Microsoft Docs
description: 지정된 리소스에 대한 모든 역할 작업의 보기를 표시하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - 리소스 역할 - 감사

리소스 감사에서는 리소스에 대한 모든 역할 활동에 대한 보기를 제공합니다. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 정보를 필터링할 수 있습니다.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

또한 빠른 액세스도 제공하여 사용자의 활동 세부 정보를 볼 수 있습니다. "감사 형식"에서 "활성화"를 선택합니다. Azure 리소스에서 해당 사용자의 작업을 확인하려면 "(작업)"을 클릭합니다.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>내 감사

내 감사에서는 사용자의 개인 역할 작업 보기를 제공합니다. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 정보를 필터링할 수 있습니다.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>활성화 및 Azure 리소스 활동 보기

특정 사용자가 다양한 리소스에서 수행한 작업을 확인해야 하는 경우 적격 사용자에 대해 지정된 활성화 기간과 관련된 Azure 리소스 활동을 검토할 수 있습니다. [구성원] 보기 또는 특정 역할의 구성원 목록에서 사용자를 선택하여 시작합니다. 결과에서는 Azure Resources의 날짜별 사용자 작업 및 동일한 기간 동안의 최근 역할 활성화를 그래픽으로 표시합니다.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

특정 역할 활성화를 선택하면 역할 활성화 세부 정보와 해당 사용자가 활성화된 동안 발생한 해당 Azure 리소스 활동이 표시됩니다.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

