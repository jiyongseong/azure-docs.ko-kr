---
title: Azure Automation의 내 첫 번째 PowerShell Runbook
description: 자습서는 간단한 PowerShell Runbook의 생성, 테스트, 게시를 단계별로 안내합니다.
keywords: azure powershell, powershell 스크립트 자습서, powershell 자동화
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fe9d98b694b8c42f3342e615d92fae9824dca26
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="my-first-powershell-runbook"></a>내 첫 번째 PowerShell Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

이 자습서는 Azure Automation에서 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적하는 방법을 알아보면서 테스트하고 게시하는 간단한 Runbook으로 시작합니다. 그런 다음 실제로 Azure 리소스를 관리하도록 Runbook을 수정합니다. 이 경우에 Azure Virtual Machine을 시작합니다. 마지막으로 Runbook 매개 변수를 추가하여 Runbook을 더욱 강력하게 개선합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-offering-get-started.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 프로덕션 VM이 되지 않도록 이 가상 머신을 중지하고 시작합니다.

## <a name="step-1---create-new-runbook"></a>1 단계-새 runbook 만들기
먼저 *헬로 월드*라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

   Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 대부분의 자산들은 새 Automation 계정에 자동적으로 포함되 있는 모듈입니다. 또한 사용자는 [필수 구성 요소](#prerequisites)에서 언급된 자격 증명 자산이 있어야 합니다.

1. **프로세스 자동화** 아래에서 **Runbook**을 클릭하여 Runbook 목록을 엽니다.
2. **+ Runbook 추가** 단추를 클릭하고 **새 Runbook 만들기**를 클릭하여 새 Runbook을 만듭니다.
3. Runbook 이름을 *MyFirstRunbook-PowerShell*로 지정합니다.
4. 이 경우 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)을 만들어 **Runbook 형식**에 대해 **Powershell**을 선택합니다.
5. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가
runbook에 직접 코드를 입력하거나 라이브러리 컨트롤에서 cmdlet, Runbook 및 자산을 선택한 후 관련 매개 변수와 함께 Runbook에 추가되도록 할 수 있습니다. 이 연습에서는 Runbook에 직접 입력합니다.

1. Runbook은 현재 비어 있습니다. 스크립트의 본문에 *Write-Output "Hello World"* 를 입력합니다.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트
프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 제대로 작동하는지 확인하기 위해 테스트합니다. Runbook을 테스트할 때 **초안** 버전을 실행하고 해당 출력을 대화형으로 봅니다.

1. **테스트 창** 을 클릭하여 테스트 창을 엽니다.
2. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
3. 이 창에서 [runbook 작업](automation-runbook-execution.md) 이 생성되고 상태를 보여줍니다.

   작업 상태는 클라우드의 Runbook worker를 사용할 수 있을 때까지 기다리고 있음을 나타내는 *대기 중*으로 시작됩니다. 작업자가 작업을 요구한 경우 *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  

1. Runbook 작업이 완료되면 해당 출력이 표시됩니다. 이 예제에서는 *Hello World*가 표시됩니다.<br><br> ![테스트 창 출력](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작
방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하려면 먼저 게시해야 합니다.  Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 예제에서는 Runbook을 방금 만들었으므로 게시된 버전이 아직 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.
2. 이제 **Runbooks** 창에서 Runbook을 보기 위해 왼쪽으로 스크롤하면 **작성 상태**가 **게시됨**으로 표시됩니다.
3. 다시 오른쪽으로 스크롤하면 **MyFirstRunbook-PowerShell**창이 표시됩니다.  
   위쪽에 표시되는 옵션을 사용하여 Runbook을 시작하거나 보고 미래의 특정 시간에 시작하도록 예약할 수 있으며 [웹후크](automation-webhooks.md)를 생성하여 HTTP 호출을 통해 시작할 수 있습니다.
4. Runbook을 시작하려면 Runbook 시작 페이지가 열릴 때 **시작**을 클릭하고 **확인**을 클릭합니다.
5. 만든 Runbook 작업에 대한 작업 페이지가 열립니다. 이 창을 닫아도 되지만, 이 예에서는 작업 진행 상황을 볼 수 있도록 열어 놓겠습니다.
6. Runbook을 테스트할 때와 동일한 작업 상태가 **작업 요약**에 표시됩니다.<br><br> ![작업 요약](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Runbook 상태가 *완료됨*으로 표시되면 **개요** 아래에서 **출력**을 클릭합니다. 출력 창이 열리고 *헬로 월드*가 표시됩니다.<br><br> ![작업 출력](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. 출력 페이지를 닫습니다.
9. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에 *Hello World*만이 표시되어야 하지만 Runbook이 자세한 정보 표시, 오류와 같은 Runbook 작업에 대한 다른 스트림에 작성된 경우 해당 스트림이 표시될 수 있습니다.<br><br> ![모든 로그](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. [스트림] 및 [작업] 페이지를 닫고 MyFirstRunbook-PowerShell 페이지로 돌아갑니다.
11. **세부 정보** 아래에서 **작업**을 클릭하여 이 Runbook에 대한 [작업] 창을 엽니다. runbook으로 만든 모든 작업을 나열합니다. 작업을 한 번만 실행했으므로 하나의 작업만 표시됩니다.<br><br> ![작업 목록](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Runbook을 시작했을 때 표시된 동일한 작업창을 열려면 이 작업을 클릭하면 됩니다. 이 기능을 사용하면 예전으로 돌아가 특정 runbook으로 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가
지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. [필수 구성 요소](#prerequisites)에서 언급한 자격 증명을 사용하여 인증하지 않은 경우 이러한 리소스를 관리할 수 없습니다. 이렇게 하려면 **Connect-AzureRmAccount** cmdlet을 사용합니다.

1. MyFirstRunbook-PowerShell 페이지에서 **편집**을 클릭하여 텍스트 편집기를 엽니다.
2. **Write-Output** 줄은 더 이상 필요하지 않으므로 계속 진행하여 삭제합니다.
3. Automation 실행 계정을 사용하여 인증을 처리하는 다음 코드를 입력하거나 복사하여 붙여 넣습니다.
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.
5. **시작** 을 클릭하여 테스트를 시작합니다. 일단 완료되면 다음과 비슷한 출력을 수신하여 계정의 기본 정보를 표시해야 합니다. 이를 통해 자격 증명이 유효한지 확인됩니다.<br><br> ![인증](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가
이제 Runbook에서 Azure 구독을 인증하므로 리소스를 관리할 수 있습니다. 가상 머신을 시작하는 명령을 추가합니다. Azure 구독에서 가상 머신을 선택할 수 있지만, 지금은 해당 이름을 Runbook에 하드 코딩합니다.

1. *Connect-AzureRmAccount* 다음에 *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* 을 입력하여 시작하려는 가상 머신의 이름과 리소스 그룹 이름을 입력합니다.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Runbook을 저장한 다음, 테스트할 수 있도록 **테스트 창**을 클릭합니다.
3. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면, 가상 머신이 시작되었다는 것을 확인합니다.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7 단계 - runbook에 입력 매개 변수를 추가 합니다.
Runbook은 현재 Runbook에 하드 코딩된 가상 머신을 시작하지만, Runbook이 시작될 때 가상 머신을 지정하면 더 유용합니다. Runbook에 입력 매개 변수를 추가하여 해당 기능을 제공합니다.

1. Runbook에 *VMName* 및 *ResourceGroupName*에 대한 매개 변수를 추가하고 다음 예제와 같이 **Start-AzureRmVM** cmdlet에 이러한 변수를 사용합니다.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Runbook을 저장하고 테스트 창을 엽니다. 이제 사용자는 테스트에 사용될 두 개의 입력 변수에 대한 값을 제공할 수 있습니다.
2. 창을 닫습니다.
3. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
4. 이전 단계에서 실행시킨 가상 머신을 중지합니다.
5. **확인**을 클릭하여 Runbook을 시작합니다. 시작하려는 가상 머신의 **VMName** 및 **ResourceGroupName**을 입력합니다.<br><br> ![매개 변수 전달](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Runbook이 완료되면 가상 머신이 시작되었다는 것을 확인합니다.

## <a name="differences-from-powershell-workflow"></a>PowerShell 워크플로의 차이점
PowerShell Runbook에는 PowerShell 워크플로 Runbook과 동일한 수명 주기, 기능 및 관리가 있지만 몇 가지 차이점 및 제한 사항이 있습니다.

1. 컴파일 단계가 없기 때문에 PowerShell 워크플로 Runbook에 비해 PowerShell Runbook이 빠르게 실행됩니다.
2. PowerShell 워크플로 Runbook은 검사점 및 검사점 사용을 지원합니다. PowerShell Runbook은 처음부터만 다시 시작할 수 있지만 PowerShell 워크플로 Runbook은 Runbook의 어느 지점에서든지 다시 시작할 수 있습니다.
3. PowerShell Runbook은 직렬로 명령을 실행할 수 있는 반면 PowerShell 워크플로 Runbook은 병렬 및 직렬 실행을 지원합니다.
4. PowerShell Runbook에서 스크립트의 모든 항목은 단일 runspace에서 실행되는 반면 PowerShell 워크플로 Runbook에서 활동, 명령 또는 스크립트 블록에는 자체 runspace가 있을 수 있습니다. 또한 네이티브 PowerShell Runbook과 PowerShell 워크플로 Runbook 간에 몇 가지 [구문 차이](https://technet.microsoft.com/magazine/dn151046.aspx) 가 있습니다.

## <a name="next-steps"></a>다음 단계
* 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md)
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)
* Runbook의 형식, 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)
* PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure Automation에서 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

