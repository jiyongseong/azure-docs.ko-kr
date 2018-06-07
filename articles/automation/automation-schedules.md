---
title: Azure Automation의 일정
description: Automation 일정은 Azure Automation에서 Runbook이 자동으로 시작되도록 예약하는 데 사용됩니다. 특정 시간 또는 되풀이 일정에 따라 Runbook을 자동으로 시작할 수 있도록 일정을 만들고 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c7bd4d4249d304ee7c1df4ae4b8fc0af476b99c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192074"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Azure Automation에서 Runbook 예약

Azure Automation에서 Runbook이 지정된 시간에 시작되도록 예약하려면 해당 Runbook을 하나 이상의 일정에 연결합니다. Azure Portal에서 일정이 Runbook에 대해 한 번 실행되거나 매시간 또는 매일 반복되도록 구성할 수 있습니다. 매주, 매월, 주의 특정 요일이나 매월 특정 요일에 또는 특정 날짜에 실행되도록 예약할 수도 있습니다. Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다.

> [!NOTE]
> 일정은 현재 Azure Automation DSC 구성을 지원하지 않습니다.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet

다음 테이블의 cmdlet은 Azure Automation에서 Windows PowerShell을 사용하여 일정을 만들고 관리하는 데 사용됩니다. 이러한 cmdlet은 [Azure PowerShell 모듈](/powershell/azure/overview)의 일부로 제공됩니다.

| Cmdlet | 설명 |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |일정을 검색합니다. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |새 일정을 만듭니다. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |일정을 제거합니다. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |기존 일정에 대한 속성을 설정합니다. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |예약된 Runbook을 검색합니다. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Runbook을 일정에 연결합니다. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |일정에서 Runbook을 분리합니다. |

## <a name="creating-a-schedule"></a>일정 만들기

Azure Portal에서 또는 Windows PowerShell을 사용하여 Runbook에 대한 새 일정을 만들 수 있습니다.

> [!NOTE]
> Azure Automation은 예약된 새 작업이 실행될 때 Automation 계정의 최신 모듈을 사용합니다.  자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 먼저 테스트 전용 Automation 계정으로 일정을 연결한 모든 Runbook을 테스트해야 합니다.  이렇게 하면 예약된 Runbook이 제대로 계속 작동하는지 확인하고, 그렇지 않은 경우 프로덕션 환경에 업데이트된 Runbook 버전을 마이그레이션하기 전에 추가적으로 문제를 해결하고 필요한 모든 변경 내용을 적용할 수 있습니다.
> **모듈**에서 [Azure 모듈 업데이트](automation-update-azure-modules.md) 옵션을 선택하여 수동으로 업데이트하지 않는 한 Automation 계정에서 새 버전의 모듈을 자동으로 가져오지 않습니다.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Azure 포털에서 새 일정을 만들려면

1. Azure Portal의 Automation 계정에서 왼쪽의 **공유 리소스** 섹션 아래에 있는 **일정**을 선택합니다.
1. 페이지의 위쪽에서 **일정 추가**를 클릭합니다.
1. **새 일정** 창에서 **이름**을 입력하고, 선택적으로 새 일정에 대한 **설명**을 입력합니다.
1. **한 번** 또는 **되풀이**를 선택하여 일정을 한 번 실행할지 또는 되풀이 일정에 따라 실행할지를 선택합니다. **한 번**을 선택하는 경우 **시작 시간**을 지정한 다음, **만들기**를 클릭합니다. **되풀이**를 선택하는 경우 **시작 시간**을 지정하고 얼마나 자주 runbook을 반복할지 빈도를 **시간**, **일**, **주** 또는 **달**로 지정합니다. 드롭다운 목록에서 **주** 또는 **월**을 선택하면 **되풀이 옵션**이 창에 표시됩니다. 선택하는 즉시 **되풀이 옵션** 창이 표시되며 **주**를 선택한 경우 요일을 선택할 수 있습니다. **월**을 선택한 경우 **요일** 또는 달력에서 월의 특정한 날짜를 선택하고 마지막으로 월의 마지막 날에 실행할지 여부를 선택할 수 있습니다. 그런 다음 **확인**을 클릭합니다.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 일정을 만들려면

[New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용하여 일정을 만듭니다. 일정의 시작 시간 및 실행 빈도를 지정합니다.

다음 샘플 명령에서는 Azure Resource Manager cmdlet을 사용하여 매월 15일 및 30일에 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Runbook에 일정 연결

Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다. Runbook에 매개 변수가 있는 경우 값을 제공할 수 있습니다. 필수 매개 변수의 값은 반드시 제공해야 하며, 선택적 매개 변수의 값은 필요에 따라 제공할 수 있습니다. 이러한 값은 Runbook이 이 일정에 따라 시작할 때마다 사용됩니다. 동일한 Runbook을 다른 일정에 연결하고 다른 매개 변수 값을 지정할 수 있습니다.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Azure 포털을 사용하여 Runbook에 일정을 연결하려면

1. Azure Portal의 Automation 계정에서 왼쪽의 **프로세스 자동화** 섹션 아래에 있는 **Runbook**을 선택합니다.
1. 예약할 Runbook의 이름을 클릭합니다.
1. 현재 Runbook이 일정에 연결되지 않은 경우 새 일정을 만들거나 기존 일정에 연결하는 옵션이 제공됩니다.
1. Runbook에 매개 변수가 있는 경우 **실행 설정 수정(기본값: Azure)** 옵션을 선택할 수 있고, 이에 따라 적절하게 정보를 입력할 수 있는 **매개 변수** 창이 표시됩니다.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook에 일정을 연결하려면

일정과 마찬가지로 [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet을 사용합니다. Parameters 매개 변수를 사용하여 Runbook의 매개 변수 값을 지정할 수 있습니다. 매개 변수 값 지정에 대한 자세한 내용은 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
다음 샘플 명령에서는 매개 변수를 포함하는 Azure Resource Manager cmdlet을 사용하여 runbook에 일정을 연결하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Runbook을 보다 빈번하게 예약하기

Azure Automation에서 일정에 구성할 수 있는 가장 빈번한 간격은 1시간입니다. 일정을 이보다 더 자주 실행해야 하는 경우 두 가지 옵션이 있습니다.

* Runbook에 대한 [웹후크](automation-webhooks.md)를 만들고 [Azure Scheduler](../scheduler/scheduler-get-started-portal.md)를 사용하여 웹후크를 호출합니다. Azure Scheduler는 일정을 정의할 때 보다 세분화된 세분성을 제공합니다.

* 한 시간에 한 번씩 실행되는 일정을 4개 만들어서 서로 15분 이내에 시작되도록 설정합니다. 이 시나리오를 적용하면 Runbook을 다른 일정으로 15분마다 실행할 수 있습니다.

## <a name="disabling-a-schedule"></a>일정 해제

일정을 사용하지 않도록 설정하면 연결된 모든 Runbook이 해당 일정에서 더 이상 실행되지 않습니다. 일정을 수동으로 해제하거나, 일정을 만들 때 빈도를 사용하여 일정에 대한 만료 시간을 설정할 수 있습니다. 만료 시간에 도달하면 일정이 사용되지 않습니다.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Azure 포털에서 일정을 비활성화하려면

1. Azure Portal의 Automation 계정에서 왼쪽의 **공유 리소스** 섹션 아래에 있는 **일정**을 선택합니다.
1. 일정 이름을 클릭하여 해당 세부 정보 창을 엽니다.
1. **사용**을 **아니오**로 변경합니다.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Windows PowerShell을 사용하여 일정을 해제하려면

[Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) cmdlet을 사용하여 기존 일정의 속성을 변경할 수 있습니다. 일정을 해제하려면 **IsEnabled** 매개 변수에 대해 **false**를 지정합니다.

다음 샘플 명령에서는 Azure Resource Manager cmdlet을 사용하여 runbook에 일정을 비활성화하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 Runbook을 시작하려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)