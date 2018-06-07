---
title: Azure Automation의 Runbook 설정
description: Azure Automation의 Runbook에 대한 구성 설정 및 Azure Portal과 Windows PowerShell을 사용하여 이를 변경하는 방법에 대해 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 951e59333ab1a1e982386c5c71f79b86f5e62440
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="runbook-settings"></a>Runbook 설정
Azure Automation의 각 Runbook에는 해당 로깅 동작을 쉽게 식별하고 변경하는 데 유용한 여러 설정이 있습니다. 이러한 각 설정은 아래에서 해당 설정을 수정하는 방법에 대한 절차 다음에 설명되어 있습니다.

## <a name="settings"></a>설정
### <a name="name-and-description"></a>이름 및 설명
Runbook을 만든 후에는 이름을 변경할 수 없습니다. 이 설명은 선택 사항이며, 최대 512자일 수 있습니다.

### <a name="tags"></a>태그들
태그를 사용하면 Runbook을 쉽게 식별할 수 있는 고유한 단어 및 구를 할당할 수 있습니다. 예를 들어 [PowerShell 갤러리](https://www.powershellgallery.com/)에 Runbook을 제출할 때 Runbook이 나열되어야 하는 범주를 식별하는 특정 태그를 지정합니다. 쉼표로 구분하여 Runbook에 대한 여러 태그를 지정할 수 있습니다.

### <a name="logging"></a>로깅
기본적으로 자세한 정보 표시 및 진행률 레코드 작업 기록에 기록되지 않습니다. 이러한 레코드를 기록하려면 특정 Runbook에 대한 설정을 변경하면 됩니다. 이러한 레코드에 대한 자세한 내용은 [Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조하세요.

## <a name="changing-runbook-settings"></a>Runbook 설정 변경

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Azure Portal을 사용하여 Runbook 설정 변경
Azure Portal의 Runbook에 대한 **설정** 블레이드에서 해당 Runbook의 설정을 변경할 수 있습니다.

1. Azure Portal에서 **Automation**을 선택한 다음 자동화 계정의 이름을 클릭합니다.
2. **Runbook** 탭을 선택합니다.
3. Runbook의 이름을 클릭하면 해당 runbook에 대한 설정 블레이드로 이동합니다. 여기에서 태그 또는 runbook 설명을 지정하거나 수정하고, 로깅 및 추적 설정을 구성하고, 문제 해결을 도와주는 지원 도구에 액세스할 수 있습니다.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook 설정 변경
[Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) cmdlet을 사용하여 Runbook의 설정을 변경할 수 있습니다. 여러 태그를 지정하려면 Tags 매개 변수에 배열 또는 쉼표로 구분된 값이 있는 단일 문자열을 제공합니다. [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx)을 사용하여 현재 태그를 가져올 수 있습니다.

다음 명령 예제에서는 Rnbook에 대한 속성을 설정하는 방법을 보여 줍니다. 이 예제에서는 기존 태그에 세 개의 태그를 추가하고 자세한 정보 표시 레코드가 기록되도록 지정합니다.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>다음 단계
* Runbook에서 출력 및 오류 메시지를 만들고 검색하는 방법은 [Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조하세요. 
* 커뮤니티 또는 다른 출처에서 이미 개발된 runbook을 추가하거나 사용자 고유의 runbook을 만드는 방법은 [Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)를 참조하세요. 

