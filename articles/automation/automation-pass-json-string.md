---
title: Azure Automation Runbook에 JSON 개체 전달
description: Runbook에 매개 변수를 JSON 개체로 전달하는 방법
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell,  runbook, json, azure automation
ms.openlocfilehash: b0eaa13baa3e787db14e7a6f915018c3a4f280a1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Azure Automation Runbook에 JSON 개체 전달

JSON 파일에서 Runbook에 전달하려는 데이터를 저장하는 것이 유용할 수 있습니다.
예를 들어 Runbook에 전달하려는 모든 매개 변수가 포함된 JSON 파일을 만들 수 있습니다.
이렇게 하려면 JSON을 문자열로 변환한 다음 해당 문자열을 PowerShell 개체로 변환한 후에 해당 내용을 Runbook에 전달해야 합니다.

이 예제에서는 [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx)을 호출하여 PowerShell Runbook을 시작하고 JSON의 내용을 Runbook에 전달하는 PowerShell 스크립트를 작성합니다.
PowerShell Runbook은 Azure VM을 시작하고 전달된 JSON에서 VM에 대한 매개 변수를 가져옵니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 계정이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 <a href="/pricing/free-account/" target="_blank">[무료 계정을 등록](https://azure.microsoft.com/free/)할 수 있습니다.
* [Automation 계정](automation-sec-configure-azure-runas-account.md) .  이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 프로덕션 VM이 되지 않도록 이 가상 컴퓨터를 중지하고 시작합니다.
* 로컬 컴퓨터에 설치된 Azure Powershell. Azure PowerShell을 얻는 방법에 대한 자세한 내용은 [Install and configure Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0)(Azure Powershell 설치 및 구성)을 참조하세요.

## <a name="create-the-json-file"></a>JSON 파일 만들기

텍스트 파일에 다음 테스트를 입력하고 로컬 컴퓨터의 특정 위치에 `test.json`으로 저장합니다.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Runbook 만들기

Azure Automation에서 "Test-Json"이라는 새 PowerShell Runbook을 만듭니다.
새 PowerShell Runbook을 만드는 방법을 알아보려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.

JSON 데이터를 허용하려면 Runbook에서 개체를 입력 매개 변수로 사용해야 합니다.

그러면 Runbook에서 JSON에 정의된 속성을 사용할 수 있습니다.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 이 Runbook을 Automation 계정에 저장하고 게시합니다.

## <a name="call-the-runbook-from-powershell"></a>PowerShell에서 Runbook 호출

이제 로컬 컴퓨터에서 Azure PowerShell을 사용하여 Runbook을 호출할 수 있습니다.
다음 PowerShell 명령을 실행합니다.

1. Azure에 로그인합니다.
   ```powershell
   Connect-AzureRmAccount
   ```
    Azure 자격 증명을 입력하라는 메시지가 표시됩니다.
1. JSON 파일의 내용을 가져와 문자열로 변환합니다.
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath`는 JSON 파일을 저장한 경로입니다.
1. `$json`의 문자열 내용을 PowerShell 개체로 변환합니다.
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. `Start-AzureRmAutomationRunbook`의 매개 변수에 대한 해시 테이블을 만듭니다.
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   `Parameters`의 값을 JSON 파일의 값이 포함된 PowerShell 개체로 설정하고 있는 것입니다. 
1. Runbook을 시작합니다.
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Runbook에서 JSON 파일의 값을 사용하여 VM을 시작합니다.

## <a name="next-steps"></a>다음 단계

* 텍스트 편집기를 사용하여 PowerShell 및 PowerShell 워크플로 Runbook을 편집하는 방법을 알아보려면 [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md) 
* Runbook을 만들고 가져오는 방법을 알아보려면 [Azure Automation에서 Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)를 참조하세요.


