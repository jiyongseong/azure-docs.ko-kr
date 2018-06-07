---
title: Azure Automation의 자격 증명 자산
description: Azure Automation의 자격 증명 자산은 runbook 또는 DSC 구성을 통해 액세스 되는 리소스를 인증하는 보안 자격 증명을 포함합니다. 이 문서에서는 자격 증명 자산을 만들고 runbook 또는 DSC 구성에 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d8055e6fdf8cc9af14d94177c9ffdeb142352d5a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="credential-assets-in-azure-automation"></a>Azure Automation의 자격 증명 자산

Automation 자격 증명 자산은 사용자 이름과 암호 등의 보안 자격 증명을 포함하는 [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 개체를 보유합니다. Runbook과 DSC 구성은 인증을 위해 PSCredential 개체를 허용하는 cmdlet를 사용할 수 있고, 일부 응용 프로그램 도는 인증이 필요한 서비스에 제공하기 위해 PScredential 개체의 사용자 이름과 암호를 추출할 수 있습니다. 자격 증명의 속성은 Azure Automation에 안전하게 저장되며 [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) 활동을 통해 runbook과 DSC 구성에서 액세스할 수 있습니다.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. 이 키는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다.

## <a name="azure-classic-powershell-cmdlets"></a>Azure 클래식 PowerShell cmdlet

다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 자격 증명 자산을 만들고 관리하는 데 사용됩니다.  Automation runbook과 DSC 구성에 사용할 수 있는 [Azure PowerShell 모듈](/powershell/azure/overview) 의 일부로 전송됩니다.

| Cmdlet | 설명 |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |자격 증명 자산에 대한 정보를 검색합니다. **Get-AutomationPSCredential** 활동에서는 자격 증명 자체만 검색할 수 있습니다. |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |새 Automation 자격 증명을 만듭니다. |
| [Remove- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Automation 자격 증명을 제거합니다. |
| [Set- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |기존 Automation 자격 증명에 대한 속성을 설정합니다. |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet

AzureRM에 대해 다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 자격 증명 자산을 만들고 관리하는 데 사용됩니다.  Automation runbook과 DSC 구성에 사용할 수 있는 [AzureRM.Automation 모듈](/powershell/azure/overview)의 일부로 전송됩니다.

| Cmdlet | 설명 |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |자격 증명 자산에 대한 정보를 검색합니다.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |새 Automation 자격 증명을 만듭니다. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Automation 자격 증명을 제거합니다. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |기존 Automation 자격 증명에 대한 속성을 설정합니다. |

## <a name="activities"></a>활동

다음 표의 활동은 runbook과 DSC 구성의 자격 증명에 액세스하는데 사용됩니다.

| 활동 | 설명 |
|:--- |:--- |
| Get-AutomationPSCredential |Runbook 또는 DSC 구성에 사용하는 자격 증명을 가져옵니다. [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 개체를 반환합니다. |

> [!NOTE]
> Get-AutomationPSCredential의 Name 매개변수에서는 변수를 사용하면 안 됩니다. runbook 또는 DSC 구성과 design time의 자격 증명 간에 종속성이 발견되어 복잡해질 수 있기 때문입니다.

## <a name="python2-functions"></a>Python2 함수

다음 표의 함수는 Python2 Runbook의 자격 증명에 액세스하는 데 사용됩니다.

| 함수 | 설명 |
|:---|:---|
| automationassets.get_automation_credential | 자격 증명 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 함수에 액세스하려면 Python Runbook 맨 위에서 "automationassets" 모듈을 가져와야 합니다.

## <a name="creating-a-new-credential-asset"></a>새 자격 증명 자산 만들기

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Azure 포털을 사용하여 새 자격 증명 자산을 만들려면

1. 자동화 계정에서 **자산** 파트를 클릭하여 **자산** 블레이드를 엽니다.
2. **자격 증명** 파트를 클릭하여 **자격 증명** 블레이드를 엽니다.
3. 블레이드의 위쪽에서 **자격 증명 추가** 를 클릭합니다.
4. 양식을 완료하고 **만들기** 를 클릭하여 새 자격 증명을 저장합니다.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 자격 증명 자산을 만들려면

다음 명령 예제에서는 새 자동화 자격 증명을 만드는 방법을 보여 줍니다. 먼저 이름 및 암호를 사용하여 PSCredential 개체를 만든 다음 이를 사용하여 자격 증명 자산을 만듭니다. 또는 **Get-Credential** cmdlet을 사용하여 이름 및 암호를 입력하라는 메시지를 표시할 수 있습니다.

```azurepowershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell 자격 증명 사용

**Get-AutomationPSCredential** 활동을 사용하여 runbook 또는 DSC 구성의 자격 증명 자산을 검색합니다. 그러면 PSCredential 매개 변수가 필요한 활동 또는 cmdlet에서 사용할 수 있는 [PSCredential 개체](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) 가 반환됩니다. 자격 증명 개체의 속성을 검색하여 개별적으로 사용할 수도 있습니다. 이 개체에는 사용자 이름 및 보안 암호에 대한 속성이 있으며, **GetNetworkCredential** 메서드를 사용하여 보안되지 않은 버전의 암호를 제공하는 [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) 개체를 반환할 수도 있습니다.

### <a name="textual-runbook-sample"></a>텍스트 Runbook 샘플

다음 명령 예제에서는 Runbook에서 PowerShell 자격 증명을 사용하는 방법을 보여 줍니다. 이 예제에서는 자격 증명을 검색하고 해당 사용자 이름 및 암호를 변수에 할당합니다.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>그래픽 Runbook 샘플

그래픽 편집기의 라이브러리 창에서 자격 증명을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 **Get-AutomationPSCredential**를 그래픽 Runbook에 추가합니다.

![캔버스에 자격 증명 추가](media/automation-credentials/credential-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 자격 증명을 사용하는 예제를 보여 줍니다.  이 예제에서는 [Azure AD 사용자 계정으로 Runbook 인증](automation-create-aduser-account.md)에 설명된 대로 자격 증명을 사용하여 Azure 리소스에 Runbook에 대한 인증을 제공합니다.  첫 번째 활동에서는 Azure 구독에 액세스할 수 있는 자격 증명을 검색합니다.  그런 다음 **Add-AzureAccount** 활동에서 이 자격 증명을 사용하여 이후의 모든 활동에 대한 인증을 제공합니다.  [Get-AutomationPSCredential](automation-graphical-authoring-intro.md#links-and-workflow) 에는 단일 개체가 필요하기 때문에 여기에서는 **파이프라인 링크** 를 사용합니다.  

![캔버스에 자격 증명 추가](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>DSC에서 PowerShell 자격 증명을 사용

Azure Automation에서 DSC 구성은 **Get-AutomationPSCredential**을 사용하여 자격 증명 자산을 참조할 수 있지만 원하는 경우 자격 증명 자산은 매개 변수를 통해 전달될 수 있습니다. 자세한 내용은 [Azure Automation DSC에서 구성을 컴파일](automation-dsc-compile.md#credential-assets)을 참조하세요.

## <a name="using-credentials-in-python2"></a>Python2에서 자격 증명 사용

다음 샘플에서는 Python2 Runbook의 자격 증명에 액세스하는 예제를 보여 줍니다.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>다음 단계

* 그래픽 작성 링크에 대해 자세히 알아보려면 [그래픽 작성 링크](automation-graphical-authoring-intro.md#links-and-workflow)
* 자동자동화가 포함된 다양한 메서드를 이해하려면 [Azure Automation 보안](automation-security-overview.md)
* 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md)
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md) 
* Python2 Runbook을 시작하려면 [내 첫 번째 Python2 Runbook](automation-first-runbook-textual-python2.md)을 참조하세요. 

