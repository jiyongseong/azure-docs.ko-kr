---
title: Automation 계정 및 리소스 마이그레이션
description: 이 문서에서는 Azure Automation의 Automation 계정 및 관련 리소스를 구독 간에 이동하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 75d973388bb8a37cc0b796b1621f847b047d70f9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="migrate-automation-account-and-resources"></a>Automation 계정 및 리소스 마이그레이션
Automation 계정 및 연결된 리소스 (즉, 자산, Runbook, 모듈 등)를 Azure Portal에서 만든 다른 또는 하나의 구독에서 다른 한 리소스 그룹에서 마이그레이션에 대해 손쉽게 수행할 수 있습니다 [리소스 이동](../azure-resource-manager/resource-group-move-resources.md) Azure Portal에서 사용할 수 있는 기능입니다. 그러나 이 작업을 계속하기 전에 다음 [리소스를 이동하기 전의 검사 목록](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources)과 다음 Automation 관련 목록을 검토해야 합니다.  

1. 대상 구독/리소스 그룹은 원본이 있는 동일한 지역에 있어야 합니다. 즉, Automation 계정을 여러 지역 간에 이동할 수 없습니다.
2. 리소스(예: Runbook, 작업 등)를 이동할 때 원본 그룹과 대상 그룹은 작업 기간 동안 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 그룹에서 차단됩니다. 
3. 기존 구독의 리소스 또는 구독 ID를 참조하는 모든 Runbook 또는 변수는 마이그레이션이 완료된 후에 업데이트해야 합니다.  

> [!NOTE]
> 이 기능은 클래식 자동화 리소스 이동을 지원하지 않습니다.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>포털을 사용하여 Automation 계정을 이동하려면
1. Automation 계정에서 페이지 위쪽에 있는 **이동**을 클릭합니다.<br> ![이동 옵션](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. 자동화 계정을 다른 리소스 그룹 또는 다른 구독으로 이동하도록 선택합니다.
3. **리소스 이동** 창에는 Automation 계정 및 리소스 그룹 둘 다에 관련된 리소스가 표시됩니다. 드롭다운 목록에서 **구독** 및 **리소스 그룹**을 선택하거나 **새 리소스 그룹 만들기** 옵션을 선택한 후 제공된 필드에 새 리소스 그룹 이름을 입력합니다. 
4. 검토한 후 *리소스를 이동한 후 새 리소스 ID를 사용하려면 도구 및 스크립트를 업데이트해야 한다는 사실을 이해*했음을 확인하는 확인란을 선택한 후 **확인**을 클릭합니다.<br> ![리소스 이동 창](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

이 작업을 완료하는 데는 몇 분 정도 걸릴 수 있습니다. **알림**에 유효성 검사 및 마이그레이션을 비롯한 각 작업의 상태와 최종 완료 시기가 표시됩니다.    

## <a name="to-move-the-automation-account-using-powershell"></a>PowerShell을 사용하여 Automation 계정을 이동하려면
기존 자동화 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 **Get-AzureRmResource** cmdlet을 사용하여 특정 Automation 계정을 가져온 다음 **Move-AzureRmResource** cmdlet을 사용하여 이동을 수행합니다.

첫 번째 예제는 Automation 계정을 새 리소스 그룹으로 이동하는 방법을 보여 줍니다.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

위의 코드 예제를 실행하면 이 작업을 수행할지 묻는 메시지가 나타납니다. **예**를 클릭하고 스크립트가 진행되도록 허용하면 마이그레이션을 수행하는 동안 알림이 수신되지 않습니다. 

새 구독으로 이동하려면 *DestinationSubscriptionId* 매개 변수 값을 포함합니다.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

앞의 예제에서와 마찬가지로 이동할지 묻는 메시지가 표시됩니다. 

## <a name="next-steps"></a>다음 단계
* 리소스를 새 리소스 그룹이나 구독으로 이동하는 방법에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)
* Azure Automation의 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure Automation에서 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.
* 구독을 관리하기 위한 PowerShell cmdlet에 대한 자세한 내용은 [Resource Manager에서 Azure PowerShell 사용](../azure-resource-manager/powershell-azure-resource-manager.md)
* 구독을 관리하기 위한 포털 기능에 대한 자세한 내용은 [Azure 포털을 사용하여 리소스 관리](../azure-resource-manager/resource-group-portal.md)를 참조하세요.
