---
title: Azure Automation에서 Runbook 만들기 또는 가져오기
description: 이 문서에서는 Azure Automation에서 새 Runbook을 만들거나 파일에서 가져오는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ac7b050bf96401d33254dedad5035e43850ecc52
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Azure Automation에서 Runbook 만들기 또는 가져오기
[새로 만들거나](#creating-a-new-runbook)[파일 또는Runbook 갤러리에서 기존 Runbook](automation-runbook-gallery.md)을 가져와서 Azure Automation에 Runbook을 추가할 수 있습니다. 이 문서에서는 파일로부터 Runbook을 만들고 가져오는 것과 관련한 정보를 제공합니다.  [Azure Automation에 대한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)에서 커뮤니티 Runbook과 모듈 액세스에 대한 모든 상세 정보를 확인할 수 있습니다.

## <a name="creating-a-new-runbook"></a>새 Runbook 만들기
Azure Portal 또는 Windows PowerShell 중 하나를 사용하여 Azure Automation에서 새 Runbook을 만들 수 있습니다. Runbook를 만든 후에는 [PowerShell 워크플로 학습](automation-powershell-workflow.md) 및 [Azure Automation에서 그래픽 제작](automation-graphical-authoring-intro.md)의 정보를 사용하여 편집할 수 있습니다.

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Azure Portal에서 새 Azure Automation  Runbook을 만들려면
1. Azure Portal에서 Automation 계정을 엽니다.
2. Hub에서 **Runbook**을 클릭하여 Runbook 목록을 엽니다.
3. **Runbook 추가** 단추를 클릭하고 **새 Runbook 만들기**를 클릭합니다.
4. Runbook의 **이름** 을 입력하고 [유형](automation-runbook-types.md)을 선택합니다. Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 언더바, 대시 등이 포함될 수 있습니다.
5. **만들기** 를 클릭하여 Runbook을 만들고 편집기를 엽니다.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Windows PowerShell에서 새 Azure Automation Runbook을 만들려면
[New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) cmdlet을 사용하여 빈 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만들 수 있습니다. **Name** 매개 변수를 지정하여 나중에 편집할 빈 Runbook을 만들거나, **Path** 매개 변수를 지정하여 Runbook 파일을 가져올 수 있습니다. 네 가지 Runbook 형식 중 하나를 지정하려면 **Type** 매개 변수도 포함해야 합니다.

다음 명령 예제에서는 새로운 빈 Runbook을 만드는 방법을 보여줍니다.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>파일의 Runbook을 Azure Automation으로 가져오기
PowerShell 스크립트나 PowerShell 워크플로(.ps1 확장명), 내보낸 그래픽 Runbook(.graphrunbook) 또는 Python 2 스크립트(.py extension)를 Azure Automation에 가져와서 새 Runbook을 만들 수 있습니다.  다음 사항을 고려하여 가져오기 동안 만들어지는 [Runbook 유형](automation-runbook-types.md)을 지정해야 합니다.

* .graphrunbook 파일은 새 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)에만 가져올 수 있으며 그래픽 Runbook은 .graphrunbook 파일을 통해서만 만들 수 있습니다.
* PowerShell 워크플로를 포함하는.ps1 파일은 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)에만 가져올 수 있습니다.  파일에 여러 PowerShell 워크플로 있으면 가져오기가 실패합니다. 각 워크플로를 자체 파일에 저장하 고 각각 개별적으로 가져와야 합니다.
* 워크플로를 포함하지 않은 .ps1 파일은 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [PowerShell Workflow Runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다.  PowerShell 워크플로 Runbook에 가져오면 워크플로로 변환되며 Runbook에 적용된 변경 내용을 명시하는 메모가 포함됩니다.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Azure Portal을 사용하여 파일에서 Runbook을 가져오려면
Azure Automation에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다.  

> [!NOTE]
> 이 포털을 사용하는 PowerShell 워크플로 Runbook에만 .ps1 파일을 가져올 수 있습니다.
> 
> 

1. Azure Portal에서 Automation 계정을 엽니다.
2. Hub에서 **Runbook**을 클릭하여 Runbook 목록을 엽니다.
3. **Runbook 추가** 단추를 클릭하고 **가져오기**를 클릭합니다.
4. **Runbook 파일** 을 클릭하여 가져올 파일을 선택합니다.
5. **이름** 필드가 활성화된 경우 변경이 가능합니다.  Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 언더바, 대시 등이 포함될 수 있습니다.
6. [Runbook 형식](automation-runbook-types.md)이 자동으로 선택되지만 해당 제한을 고려한 후에 형식을 변경할 수 있습니다. 
7. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다.
8. 실행에 앞서 [Runbook을 게시](#publishing-a-runbook) 해야 합니다.

> [!NOTE]
> 그래픽 Runbook 또는 그래픽 PowerShell 워크플로 Runbook을 가져온 후 필요에 따라 다른 형식으로 변환할 수도 있습니다. 텍스트 Runbook으로는 변환할 수 없습니다.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Windows PowerShell을 사용하여 스크립트 파일에서 Runbook을 가져오려면
[Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) cmdlet을 사용하여 스크립트 파일을 PowerShell 워크플로 Runbook 초안으로 가져올 수 있습니다. 해당 Runbook이 이미 있는 경우 *-Force* 매개 변수를 사용하지 않으면 가져오기에 실패합니다. 

다음 명령 예제는 Runbook에 스크립트 파일을 가져오는 방법을 보여 줍니다.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Runbook 게시
새 Runbook을 만들거나 가져올 때는 게시해야 실행할 수 있습니다.  Automation의 각 Runbook에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있게 되면 이를 게시합니다. 그러면 초안 버전이 게시된 버전을 덮어씁니다.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Azure Portal을 사용하여 Runbook을 게시하려면
1. Azure Portal에서 Runbook을 엽니다.
2. **Edit** 단추를 클릭합니다.
3. **게시** 단추를 클릭한 다음 확인 메시지에서 **예**를 클릭합니다.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook을 게시하려면
[Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) cmdlet을 사용하여 Windows PowerShell에서 Runbook을 게시할 수 있습니다. 다음 명령 예제에서는 샘플 Runbook을 게시하는 방법을 보여줍니다.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>다음 단계
* Runbook 및 PowerShell 모듈 갤러리를 활용하는 방법을 알아보려면 [Azure Automation에 대한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)
* 텍스트 편집기를 사용하여 PowerShell 및 PowerShell 워크플로 Runbook을 편집하는 방법을 알아보려면 [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)
* 그래픽 Runbook 작성에 대한 자세한 내용은 [Azure Automation에서 그래픽 제작](automation-graphical-authoring-intro.md)

