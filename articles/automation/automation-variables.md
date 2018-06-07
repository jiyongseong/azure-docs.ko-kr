---
title: Azure Automation의 변수 자산
description: 변수 자산은 Azure Automation의 모든 runbook과 DSC 구성에서 사용할 수 있는 값입니다.  이 문서에서는 변수에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 변수를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ea6aae349bfbec0d1b6538010df42e7a0fb22d8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="variable-assets-in-azure-automation"></a>Azure Automation의 변수 자산

변수 자산은 자동화 계정의 모든 runbook 및 DSC 구성에 사용할 수 있는 값입니다. 이 값을 Azure 포털, Windows PowerShell 및 runbook 또는 DSC 구성 내에서 생성, 수정 및 검색할 수 있습니다. Automation 변수는 다음과 같은 시나리오에 유용합니다.

- 여러 runbook 또는 DSC 구성 간에 값을 공유합니다.

- 동일한 runbook의 여러 작업 또는 DSC 구성 간에 값을 공유합니다.

- 포털에서 값을 관리하거나 runbook 또는 DSC 구성에 사용되는 Windows PowerShell 명령줄에서 값을 관리합니다(예: 특정 VM 이름 목록, 특정 리소스 그룹, AD 도메인 이름 등과 같은 공통 구성 항목 집합).  

runbook 또는 DSC 구성이 실패할지라도 계속 사용 가능할 수 있도록 Automation 변수는 유지됩니다. 값은 또 다른 runbook에 의해 사용되었던 runbook 또는 동일한 runbook에 사용되거나 다음에 실행되는 DSC 구성에 의해 설정됩니다.

변수를 만들 때 암호화된 상태로 저장되도록 지정할 수 있습니다. 변수를 암호화하면 Azure Automation에 안전하게 저장되며 Azure PowerShell 모듈의 일부로 제공되는 [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) cmdlet에서 해당 값을 검색할 수 없습니다. 오직 runbook 또는 DSC 구성의 **Get-AutomationVariable** 에서만 암호화 된 값을 검색할 수 있습니다.

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. 이 키는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다.

## <a name="variable-types"></a>변수 형식

Azure Portal에서 변수를 만들 때 드롭다운 목록에서 해당 데이터 형식을 지정해야 합니다. 그래야 포털에서 변수 값을 입력할 수 있는 적절한 컨트롤을 표시할 수 있습니다. 변수는 이 데이터 형식으로 제한되지 않으며 다른 형식의 값을 지정하려면 Windows PowerShell을 사용하여 변수를 설정해야 합니다. **정의되지 않음**을 지정하면 변수 값이 **$null**로 설정되므로 [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet 또는 **Set-AutomationVariable** 활동을 사용하여 값을 설정해야 합니다. 포털에서는 복잡한 변수 형식의 값을 만들거나 변경할 수 없지만 Windows PowerShell에서는 모든 형식의 값을 제공할 수 있습니다. 복잡한 형식은 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)로 반환됩니다.

배열 또는 해시 테이블을 만들어 변수에 저장하여 여러 값을 단일 변수에 저장할 수 있습니다.

다음은 Automation에서 사용할 수 있는 변수 형식의 목록입니다.

* 문자열
* 정수 
* Datetime
* BOOLEAN
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet
AzureRM에 대해 다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 자격 증명 자산을 만들고 관리하는 데 사용됩니다. Automation runbook과 DSC 구성에 사용할 수 있는 [AzureRM.Automation 모듈](/powershell/azure/overview)의 일부로 전송됩니다.

| Cmdlet | 설명 |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|기존 변수의 값을 검색합니다.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|새 변수를 만들고 해당 값을 설정합니다.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|기존 변수를 제거합니다.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|기존 변수의 값을 설정합니다.|

## <a name="activities"></a>활동
다음 표의 활동은 runbook과 DSC 구성의 자격 증명에 액세스하는데 사용됩니다.

| 활동 | 설명 |
|:---|:---|
|Get-AutomationVariable|기존 변수의 값을 검색합니다.|
|Set-AutomationVariable|기존 변수의 값을 설정합니다.|

> [!NOTE] 
> **Get-AutomationVariable**의 Name 매개변수에서는 변수를 사용하면 안 됩니다. runbook 또는 DSC 구성과 design time의 자격 증명 간에 종속성이 발견되어 복잡해질 수 있기 때문입니다.

다음 테이블의 함수는 Python2 Runbook의 변수 액세스 및 검색에 사용됩니다. 

|Python2 함수|설명|
|:---|:---|
|automationassets.get_automation_variable|기존 변수의 값을 검색합니다. |
|automationassets.set_automation_variable|기존 변수의 값을 설정합니다. |

> [!NOTE] 
> 자산 함수에 액세스하려면 Python Runbook 맨 위에서 "automationassets" 모듈을 가져와야 합니다.

## <a name="creating-a-new-automation-variable"></a>새 Automation 변수 만들기

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Azure 포털을 사용하여 새 자격 증명을 만들려면

1. Automation 계정에서 **자산** 타일을 클릭하고 **자산** 블레이드에서 **변수**를 선택합니다.
2. **변수** 타일에서 **변수 추가**를 선택합니다.
3. **새 변수** 블레이드에서 옵션을 완료하고 **만들기**를 클릭하여 새 변수를 저장합니다.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 변수를 만들려면

[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet은 새 변수를 만들고 해당 초기 값을 설정합니다. [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)을 사용하여 값을 검색할 수 있습니다. 값이 단순한 형식이면 동일한 해당 형식이 반환되고, 복잡한 형식이면 **PSCustomObject**가 반환됩니다.

다음 명령 예제에서는 문자열 형식의 변수를 만들고 해당 값을 반환하는 방법을 보여 줍니다.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

다음 명령 예제에서는 복잡한 형식의 변수를 만들고 해당 속성을 반환하는 방법을 보여 줍니다. 이 예제에서는 **Get-AzureRmVm**의 가상 머신 개체가 사용되었습니다.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>runbook 또는 DSC 구성에서 변수 사용

**Set-AutomationVariable** 활동을 사용하여 PowerShell Runbook 또는 DSC 구성에서 Automation 변수의 값을 설정하고, **Get-AutomationVariable**을 사용하여 해당 변수를 검색합니다. 워크플로 활동보다 효율이 떨어지기 때문에 **Set-AzureRMAutomationVariable** 또는 **Get-AzureRMAutomationVariable** cmdlet을 runbook 및 DSC 구성에서 사용해서는 안됩니다 또한 **Get-AzureRMAutomationVariable**을 사용하여 보안 변수의 값을 검색할 수 없습니다. Runbook 또는 DSC 구성 내에서 새 변수를 만들 수는 유일한 방법은 [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet을 사용하는 것입니다.


### <a name="textual-runbook-samples"></a>텍스트 Runbook 샘플

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>변수에서 단순한 값 설정 및 검색

다음 명령 예제에서는 텍스트 Runbook에서 변수를 설정 및 검색하는 방법을 보여 줍니다. 이 예제에서는 *NumberOfIterations* 및 *NumberOfRunnings*라는 정수 형식의 변수와 *SampleMessage*라는 문자열 형식의 변수가 이미 만들어진 것으로 가정합니다.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>변수에서 복잡한 개체 설정 및 검색

다음 샘플 코드에서는 텍스트 Runbook에서 복잡한 값으로 변수를 업데이트하는 방법을 보여 줍니다. 이 샘플에서는 **Get-AzureVM** 을 사용하여 Azure 가상 머신을 검색하고 기존 Automation 변수에 저장합니다.  [변수 형식](#variable-types)에 설명된 대로 이 변수는 PSCustomObject로 저장됩니다.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

다음 코드에서는 변수에서 값을 검색하고 이를 사용하여 가상 머신을 시작합니다.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>변수에서 컬렉션 설정 및 검색

다음 샘플 코드에서는 텍스트 Runbook에서 복잡한 값 컬렉션과 함께 변수를 사용하는 방법을 보여 줍니다. 이 샘플에서는 **Get-AzureVM** 을 사용하여 여러 Azure 가상 머신을 검색하고 기존 Automation 변수에 저장합니다. [변수 형식](#variable-types)에 설명된 대로 이 변수는 PSCustomObject 컬렉션으로 저장됩니다.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

다음 코드에서는 변수에서 컬렉션을 검색하고 이를 사용하여 각 가상 머신을 시작합니다.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Python2에서 변수 설정 및 검색
다음 샘플 코드는 Python2 Runbook에서 변수를 사용 및 설정하고 없는 변수에 대한 예외를 처리하는 방법을 보여 줍니다.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a variable
    value = automationassets.get_automation_variable("test-variable")
    print value

    # set a variable (value can be int/bool/string)
    automationassets.set_automation_variable("test-variable", True)
    automationassets.set_automation_variable("test-variable", 4)
    automationassets.set_automation_variable("test-variable", "test-string")

    # handle a non-existent variable exception
    try:
        value = automationassets.get_automation_variable("non-existing variable")
    except AutomationAssetNotFound:
        print "variable not found"


### <a name="graphical-runbook-samples"></a>그래픽 Runbook 샘플

그래픽 Runbook에서는 그래픽 편집기의 라이브러리 창에서 변수를 마우스 오른쪽 단추로 클릭하고 원하는 활동을 선택하여 **Get-AutomationVariable** 또는 **Set-AutomationVariable**을 추가합니다.

![캔버스에 변수 추가](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>변수에서 값 설정
다음 그림에서는 그래픽 Runbook에서 단순한 값으로 변수를 업데이트하는 샘플 활동을 보여 줍니다. 이 샘플에서는 **Get-AzureRmVM**을 사용하여 단일 Azure 가상 머신을 검색하고 컴퓨터 이름을 문자열 형식의 기존 Automation 변수에 저장합니다. 출력에 단일 개체만 필요하므로 [링크가 파이프라인인지 시퀀스인지](automation-graphical-authoring-intro.md#links-and-workflow) 는 중요하지 않습니다.

![단순한 변수 설정](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에서 모든 연결 활동에 대해 자세히 알아보려면 [그래픽 작성 링크](automation-graphical-authoring-intro.md#links-and-workflow)
* 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md) 
