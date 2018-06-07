---
title: Azure Automation에서 Runbook 실행
description: Azure Automation의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ff58e22f8b9b837ec272cd2cd6193da80a7b718e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195423"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation에서 Runbook 실행

Azure Automation에서 Runbook을 시작하면 작업이 생성됩니다. 작업은 Runbook의 단일 실행 인스턴스입니다. 각 작업을 실행하기 위해 Azure Automation 작업자가 할당됩니다. 작업자는 여러 Azure 계정에서 공유하지만 여러 Automation 계정의 작업은 서로 격리됩니다. 사용자는 작업에 대한 요청을 처리할 작업자를 제어할 수 없습니다. 단일 Runbook에서 동시에 여러 작업을 실행할 수 있습니다. 동일한 Automation 계정의 작업 실행 환경은 다시 사용할 수 있습니다. Azure Portal에서 Runbook 목록을 확인하면 각 Runbook에 대해 시작된 모든 작업의 상태가 나열됩니다. 각 Runbook에 대한 작업 목록을 확인하여 각 작업의 상태를 추적할 수 있습니다. 다양한 작업 상태에 대한 설명은 [작업 상태](#job-statuses)를 참조하세요.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

다음 다이어그램은 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks) 및 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)에 대한 Runbook 작업의 수명 주기를 보여 줍니다.

![작업 상태 - PowerShell 워크플로](./media/automation-runbook-execution/job-statuses.png)

다음 다이어그램은 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)에 대한 Runbook 작업의 수명 주기를 보여 줍니다.

![작업 상태 - PowerShell 스크립트](./media/automation-runbook-execution/job-statuses-script.png)

Azure 구독에 연결하면 작업에서 Azure 리소스에 액세스할 수 있습니다. 단, 공용 클라우드에서 액세스할 수 있는 데이터 센터의 리소스에만 액세스할 수 있습니다.

## <a name="job-statuses"></a>작업 상태

다음 표에서는 작업의 가능한 여러 상태를 설명합니다.

| 상태 | 설명 |
|:--- |:--- |
| Completed |작업이 완료되었습니다. |
| 실패 |[그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md)의 경우 Runbook을 컴파일하지 못했습니다. [PowerShell 스크립트 Runbook](automation-runbook-types.md)의 경우 Runbook을 시작하지 못했거나 작업에서 예외가 발생했습니다. |
| Failed, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다. |
| Queued |작업이 시작될 수 있도록 Automation 작업자의 리소스가 사용 가능한 상태가 되기를 기다리고 있습니다. |
| 시작 중 |작업이 작업자에게 할당되었으며 시스템이 시작하는 중입니다. |
| Resuming |시스템이 일시 중단된 후 작업을 다시 시작하는 중입니다. |
| 실행 중 |작업이 실행 중입니다. |
| Running, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 도달했기 때문에 언로드되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다. |
| 중지됨 |작업이 완료되기 전에 사용자에 의해 중지되었습니다. |
| 중지 중 |시스템이 작업을 중지하는 중입니다. |
| 일시 중단 |작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. 일시 중단된 작업은 다시 시작할 수 있으며, 마지막 검사점에서 다시 시작되거나, 검사점이 없을 경우 Runbook의 처음부터 다시 시작됩니다. Runbook은 예외가 발생하는 경우에만 시스템에 의해 일시 중단됩니다. 기본적으로 ErrorActionPreference는 **Continue**로 설정되며, 이는 오류 발생 시 작업이 계속 실행된다는 의미입니다. 이 기본 설정 변수가 **Stop** 으로 설정된 경우 오류 발생 시 작업이 일시 중단됩니다. [그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md) 에만 적용됩니다. |
| Suspending |시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점을 지난 경우 완료되어야만 일시 중단할 수 있습니다. [그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md) 에만 적용됩니다. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal에서 작업 상태 보기

Azure Portal에서 또는 Runbook 작업 상태 및 작업 스트림을 전달하도록 Log Analytics 작업 영역과의 통합을 구성하여 모든 Runbook 작업의 요약된 상태를 보거나 특정 Runbook 작업에 대한 세부 정보를 확인할 수 있습니다. Log Analytics와의 통합에 대한 자세한 내용은 [Automation에서 Log Analytics로 작업 상태 및 작업 스트림 전달](automation-manage-send-joblogs-log-analytics.md)을 참조하세요.

### <a name="automation-runbook-jobs-summary"></a>Automation runbook 작업 요약

선택한 Automation 계정 오른쪽의 **작업 통계** 타일 아래에는 선택한 Automation 계정에 대한 모든 runbook 작업의 요약을 볼 수 있습니다.

![작업 통계 타일](./media/automation-runbook-execution/automation-account-job-status-summary.png)에서도 확인할 수 있습니다.

이 타일은 실행된 모든 작업의 개수 및 작업 상태를 그래픽으로 표시합니다.

타일을 클릭하면 상태, 작업 실행 시작 및 완료 시간과 함께 실행된 모든 작업의 요약된 목록이 포함된 **작업** 블레이드가 나타납니다.

![Automation 계정 작업 블레이드](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

**작업 필터링**을 선택하여 작업 목록을 필터링할 수 있습니다. 특정 runbook이나 작업 상태를 필터링하거나 드롭다운 목록에서 검색할 날짜/시간 범위를 필터링할 수 있습니다.

![작업 상태 필터링](./media/automation-runbook-execution/automation-account-jobs-filter.png)

또는 Automation 계정의 **Runbook** 블레이드에서 runbook을 선택하여 특정 runbook에 대한 작업 요약 세부 정보를 확인한 다음 **작업** 타일을 선택할 수 있습니다. 그러면 **작업** 블레이드가 나타납니다. 여기에서 작업 레코드를 클릭하여 세부 정보 및 출력을 볼 수 있습니다.

![Automation 계정 작업 블레이드](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>작업 요약

특정 Runbook에 대해 생성된 모든 작업 및 해당 작업의 가장 최근 상태가 나와 있는 목록을 볼 수 있습니다. 이 목록에서 작업 상태 및 날짜 범위를 필터링하면 작업의 마지막 변경 사항을 볼 수 있습니다. 세부 정보 및 출력을 보려면 작업의 이름을 클릭합니다. 작업의 세부 보기에는 해당 작업에 제공된 Runbook 매개 변수의 값이 포함됩니다.

다음 단계를 수행하여 Runbook의 작업을 볼 수 있습니다.

1. Azure Portal에서 **Automation**을 선택한 다음 Automation 계정의 이름을 선택합니다.
2. 허브에서 **Runbook**을 선택하고 **Runbook** 블레이드의 목록에서 runbook을 선택합니다.
3. 선택한 runbook에 대한 블레이드에서 **작업** 타일을 클릭합니다.
4. 목록에 있는 작업 중 하나를 클릭하고 runbook 작업 세부 정보 블레이드에서 세부 정보 및 출력을 볼 수 있습니다.

## <a name="retrieving-job-status-using-windows-powershell"></a>Windows PowerShell을 사용하여 작업 상태 검색

[Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)을 사용하여 Runbook에 대해 생성된 작업 및 특정 작업을 검색할 수 있습니다. Windows PowerShell에서 [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx)을 사용하여 Runbook을 시작하는 경우 결과 작업이 반환됩니다. 작업의 출력을 얻으려면 [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)을 사용합니다.

다음 명령 예제는 샘플 Runbook에 대한 마지막 작업을 검색하고 작업의 상태, Runbook 매개 변수에 제공된 값, 작업의 출력을 표시합니다.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

다음 예제는 특정 작업에 대한 출력을 검색하고, 각 레코드를 반환합니다. 레코드 중 하나에 대한 예외가 발생한 경우 값 대신 예외가 쓰여집니다. 출력하는 동안 예외가 일반적으로 기록되지 않을 수 있는 추가 정보를 제공하므로 유용합니다.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>활동 로그에서 세부 정보 가져오기

Runbook을 시작한 사용자 또는 계정과 같은 기타 세부 정보는 Automation 계정의 활동 로그에서 검색할 수 있습니다. 다음 PowerShell 예에서는 문제의 Runbook을 실행하는 마지막 사용자를 제공합니다.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>공평 분배

Azure Automation에서는 클라우드의 모든 Runbook 간에 리소스를 공유할 수 있도록 3시간 동안 작업을 실행한 후 일시적으로 언로드합니다. 이 시간 동안 [PowerShell 기반 Runbook](automation-runbook-types.md#powershell-runbooks) 작업은 중지되며 다시 시작되지 않습니다. 작업 상태에는 **Stopped**이 표시됩니다. 이 유형의 Runbook은 검사점을 지원하지 않으므로 항상 처음부터 다시 시작됩니다.

[PowerShell 워크플로 기반 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)은 마지막 [검사점](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints)에서 다시 시작됩니다. 3시간을 실행한 후 runbook 작업은 서비스에 의해 일시 중단되고 해당 상태에 **Running, waiting for resources**가 표시됩니다. 샌드박스를 사용할 수 있게 되면 Runbook은 Automation 서비스에 의해 자동으로 다시 시작되고 마지막 검사점부터 계속됩니다. 이것은 일시 중단/다시 시작에 대한 정상적인 PowerShell 워크플로 동작입니다. Runbook이 3시간의 런타임을 초과하면 3회까지 프로세스가 반복됩니다. 세 번째 프로세스가 다시 시작된 후에도 runbook이 3시간 내에 완료되지 못하면 runbook 작업은 실패하고 작업 상태에 **Failed, waiting for resources**가 표시됩니다. 이 경우 오류와 함께 다음 예외가 발생합니다.

*작업이 동일한 검사점에서 반복적으로 제거되었기 때문에 실행을 계속할 수 없습니다. Runbook이 해당 상태를 유지하지 않고 시간이 오래 걸리는 작업을 수행하지 않는지 확인하세요.*

이는 Runbook이 완료되지 않고 무기한으로 실행되어 다시 언로드되지 않은 상태로 다음 검사점에 도달하지 못하도록 하기 위한 것입니다.

Runbook에 검사점이 없거나 작업이 언로드되기 전에 첫 번째 검사점에 도달하지 않은 경우에는 처음부터 다시 시작됩니다.

Runbook을 만들 때 두 검사점 간의 모든 활동을 실행할 시간을 3시간을 초과하지 않도록 해야 합니다. 이 3시간 제한에 도달하거나 오래 실행되는 작업이 중단되지 않도록 하기 위해 Runbook에 검사점을 추가해야 할 수도 있습니다. 예를 들어 Runbook에서 대용량 SQL 데이터베이스의 인덱스를 다시 작성할 수 있습니다. 이 단일 작업이 공평 분배 제한 내에 완료되지 않으면 작업이 언로드되고 처음부터 다시 시작됩니다. 이 경우 다시 인덱싱 작업을 여러 단계로 나눈 다음(예: 한 번의 하나의 테이블을 다시 인덱싱하도록) 작업이 완료할 마지막 작업 이후에 다시 시작될 수 있도록 각 작업 뒤에 검사점을 삽입해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 runbook을 시작하는 데 사용할 수 있는 여러 가지 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
