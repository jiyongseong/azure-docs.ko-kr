---
title: Azure Automation의 내 첫 번째 그래픽 Runbook
description: 자습서는 간단한 그래픽 runbook의 생성, 테스트, 게시를 단계별로 안내합니다.
keywords: runbook, runbook 템플릿, runbook 자동화, azure runbook
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6a9e6bbf3bc507a4d45af03c791104c9490cd36f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195899"
---
# <a name="my-first-graphical-runbook"></a>내 첫 번째 그래픽 Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

이 자습서는 Azure Automation에서 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적하는 방법을 알아보면서 테스트하고 게시하는 간단한 Runbook을 시작합니다. 그런 다음 실제로 Azure 리소스를 관리하도록 Runbook을 수정합니다. 이 경우에 Azure Virtual Machine을 시작합니다. 그리고 Runbook 매개 변수와 조건부 링크를 추가하여 Runbook을 더욱 강력하게 만들고 자습서를 완료합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-offering-get-started.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 프로덕션 VM이 되지 않도록 이 가상 머신을 중지하고 시작합니다.

## <a name="create-runbook"></a>runbook 만들기

먼저 *Hello World*라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

   Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자는 일부 자산이 이미 있어야 합니다. 대부분의 해당 자산은 새 Automation 계정에 자동적으로 포함되어 있는 모듈입니다. 또한 사용자는 [필수 구성 요소](#prerequisites)에서 언급된 자격 증명 자산이 있어야 합니다.

2. **프로세스 관리** 아래에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.
3. **+ Runbook 추가**를 선택하고 **새 Runbook 만들기**를 클릭하여 새 Runbook을 만듭니다.
4. Runbook 이름을 *MyFirstRunbook-Graphical*로 지정합니다.
5. 이 경우에 **Runbook 형식**으로 **그래픽**을 선택하여 [그래픽 Runbook](automation-graphical-authoring-intro.md)을 만듭니다.<br> ![새 runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="add-activities"></a>활동 추가

편집기의 왼쪽에 있는 라이브러리 컨트롤은 사용자의 runbook에 추가할 활동을 선택할 수 있게 해줍니다. **Write-Output** cmdlet을 추가하여 Runbook의 텍스트를 출력합니다.

1. 라이브러리 컨트롤에서 검색 텍스트 상자를 클릭하고 **Write-Output**을 입력합니다. 검색 결과는 다음 이미지에 나와 있습니다. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 목록의 아래로 스크롤하십시오. **Write-Output**을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하거나, cmdlet 옆의 줄임표를 클릭한 다음 **캔버스에 추가**를 선택합니다.
1. 캔버스에서 **Write-Output** 활동을 클릭합니다. 이 작업을 통해 구성 제어 페이지가 열리고, 여기서 작업을 구성할 수 있습니다.
1. **레이블**은 기본적으로 cmdlet의 이름이지만 더 친숙하게 바꿀 수 있습니다. *Hello World 출력을 위해 쓰기*로 바꿉니다.
1. **매개 변수**를 클릭하여 cmdlet 매개 변수의 값을 제공합니다.

   일부 cmdlet에는 여러 매개 변수 집합이 있으며, 사용할 매개 변수 집합을 선택해야 합니다. 이 경우 **Write-Output**에는 매개 변수 집합이 하나만 있으므로 선택하지 않아도 됩니다.

1. **InputObject** 매개 변수를 선택합니다. 이는 출력 스트림으로 보낼 텍스트를 지정하는 매개 변수입니다.
1. **데이터 원본** 드롭다운에서 **PowerShell 식**을 선택합니다. **데이터 원본** 드롭다운은 매개 변수 값을 채우는 데 사용하는 다른 소스들을 제공합니다.

   다른 활동, Automation 자산, 혹은 Power Shell식 같은 특정 소스들로부터 출력을 사용할 수 있습니다. 이 경우에 바로 *Hello World*가 출력됩니다. PowerShell 식을 사용하고 문자열을 지정할 수 있습니다.<br>

1. **식** 상자에 *"Hello World"* 를 입력한 후 **확인**을 두 번 클릭하여 캔버스로 돌아갑니다.
1. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="test-the-runbook"></a>Runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 제대로 작동하는지 확인하기 위해 테스트합니다. Runbook을 테스트할 때 **초안** 버전을 실행하고 해당 출력을 대화형으로 봅니다.

1. **테스트 창**을 선택하여 테스트 페이지를 엽니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
1. 이 창에서 [Runbook 작업](automation-runbook-execution.md) 이 생성되고 해당 상태가 표시됩니다.

   작업 상태는 클라우드에서 runbook worker를 사용할 수 있을 때까지 기다리고 있음을 나타내는 *대기*로 시작됩니다. 작업자가 작업을 요구한 경우 *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.

1. Runbook 작업이 완료되면 해당 출력이 표시됩니다. 이 경우에 *Hello World*가 표시됩니다.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 캔버스로 돌아가려면 테스트 페이지를 닫습니다.

## <a name="publish-and-start-the-runbook"></a>Runbook 게시 및 시작

방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 선택하여 Runbook을 게시한 다음, 확인 메시지가 표시되면 **예**를 선택합니다.
1. **Runbooks** 페이지에서 Runbook을 보기 위해 왼쪽으로 스크롤하면 **작성 상태**가 **게시됨**으로 표시됩니다.
1. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook-Graphical** 페이지가 표시됩니다.

   위쪽에 표시되는 옵션을 사용하여 Runbook을 시작하거나, 미래의 특정 시간에 시작하도록 예약하거나, [webhook](automation-webhooks.md) 을 생성하여 HTTP 호출을 통해 시작할 수 있습니다.

1. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다.
1. 만든 Runbook 작업에 대한 작업 페이지가 열립니다. **작업 상태**가 **완료**로 표시되는지 확인합니다.
1. Runbook 상태가 *완료됨*으로 표시되면 **출력**을 클릭합니다. **출력** 페이지가 열리고 창에 *Hello World*가 표시됩니다.
1. 출력 페이지를 닫습니다.
1. **모든 로그**를 클릭하여 Runbook 작업에 대한 스트림 페이지를 엽니다. 출력 스트림에 *Hello World*만이 표시되어야 하지만 Runbook이 자세한 정보 표시, 오류와 같은 Runbook 작업에 대한 다른 스트림에 작성된 경우 해당 스트림이 표시될 수 있습니다.
1. 모든 로그 페이지와 작업 페이지를 닫고 MyFirstRunbook-Graphical 페이지로 돌아갑니다.
1. Runbook에 대한 모든 작업을 보려면 **작업** 페이지를 닫고 **리소스** 아래에서 **작업**을 선택합니다. 이 Runbook으로 만든 모든 작업이 나열됩니다. 작업을 한 번만 실행했으므로 하나의 작업만 표시됩니다.
1. Runbook을 시작했을 때 표시된 동일한 작업창을 열려면 이 작업을 클릭하면 됩니다. 이 기능을 사용하면 예전으로 돌아가 특정 runbook으로 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## <a name="create-variable-assets"></a>변수 자산 만들기

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. Runbook을 인증하도록 구성하기 전에 구독 ID를 보유할 변수를 만들고, 아래 6단계에서 인증할 작업을 설정한 후에 해당 항목을 참조합니다. 구독 컨텍스트에 대한 참조를 포함하면 여러 구독 간에서 쉽게 작업할 수 있습니다. 계속하기 전에 [탐색] 창에서 [구독] 옵션의 구독 ID를 복사합니다.

1. Automation 계정 페이지의 **공유 리소스** 아래에서 **변수**를 선택합니다.
1. **변수 추가**를 선택합니다.
1. 새 변수 페이지의 **이름** 상자에 **AzureSubscriptionId**를 입력하고 **값** 상자에 사용자의 구독 ID를 입력합니다. **형식**에 대한 *문자열* 및 **암호화**에 대한 기본 값을 유지합니다.
1. **만들기** 를 클릭하여 변수를 만듭니다.

## <a name="add-authentication"></a>인증 추가

이제 구독 ID를 보유한 변수가 있으므로 [필수 조건](#prerequisites)에 나온 실행 자격 증명을 사용하여 인증하도록 Runbook을 구성할 수 있습니다. Azure 실행 연결 **자산** 및 **Connect-AzureRmAccount** cmdlet을 캔버스에 추가하여 수행합니다.

1. Runbook으로 다시 이동하고 MyFirstRunbook-Graphical 페이지에서 **편집**을 선택합니다.
1. **출력에 Hello World 작성**이 더 이상 필요하지 않으므로 줄임표(...)를 클릭하고 **삭제**를 선택합니다.
1. 라이브러리 컨트롤에서 **자산**, **연결**을 확장하고 **캔버스에 추가**를 선택하여 **AzureRunAsConnection**을 캔버스에 추가합니다.
1. 라이브러리 컨트롤에서 검색 텍스트 상자에 **Connect-AzureRmAccount**를 입력합니다.

   > [!IMPORTANT]
   > **Add-AzureRmAccount**는 이제 **Connect-AzureRMAccount**에 대한 별칭입니다. 라이브러리를 항목을 검색할 때 **Connect-AzureRMAccount**가 표시되지 않는 경우 **Add-AzureRmAccount**를 사용하거나 Automation 계정에서 모듈을 업데이트할 수 있습니다.

1. 캔버스에 **Connect-AzureRmAccount**를 추가합니다.
1. 도형 아래에 원이 나타날 때까지 **실행 연결 가져오기** 를 마우스로 가리킵니다. 원을 클릭하고 화살표를 **Connect-AzureRmAccount**로 끌어 놓습니다. 만든 화살표는 *링크*입니다. Runbook에서 **실행 연결 가져오기**를 시작한 다음, **Connect-AzureRmAccount**를 실행합니다.<br> ![활동 간 링크 만들기](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 캔버스에서 **Connect-AzureRmAccount**를 선택하고 구성 제어판의 **레이블** 텍스트 상자에 **Azure에 로그인**을 입력합니다.
1. **매개 변수**를 클릭하면 활동 매개 변수 구성 페이지가 표시됩니다.
1. **Connect-AzureRmAccount**에는 여러 매개 변수 집합이 있으므로 매개 변수 값을 입력하기 전에 하나를 선택해야 합니다. **매개 변수 집합**을 클릭한 후 **ServicePrincipalCertificate** 매개 변수 집합을 선택합니다.
1. 매개 변수 집합을 선택하면, 활동 매개 변수 구성 페이지에 매개 변수가 표시됩니다. **APPLICATIONID**를 클릭합니다.<br> ![Azure RM 계정 매개 변수 추가](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. 매개 변수 값 페이지에서 **데이터 원본**에 대해 **활동 출력**을 선택하고 목록에서 **실행 연결 가져오기**를 선택한 다음 **필드 경로** 텍스트 상자에 **ApplicationId**를 입력한 다음 **확인**을 클릭합니다. 작업에서 여러 속성을 가진 개체를 출력하기 때문에 필드 경로에 대한 속성의 이름을 지정합니다.
1. **CERTIFICATETHUMBPRINT**를 클릭하고 매개 변수 값 페이지에서 **데이터 원본**에 대한 **활동 출력**을 선택합니다. 목록에서 **실행 연결 가져오기**를 선택하고 **필드 경로** 텍스트 상자에서 **CertificateThumbprint**를 입력한 다음 **확인**을 클릭합니다.
1. **SERVICEPRINCIPAL**을 클릭하고 매개 변수 값 페이지에서 **데이터 원본**에 대한 **ConstantValue**를 선택하고 **True** 옵션을 클릭한 다음 **확인**을 클릭합니다.
1. **TENANTID**를 클릭하고 매개 변수 값 페이지에서 **데이터 원본**에 대한 **활동 출력**을 선택합니다. 목록에서 **실행 연결 가져오기**를 선택하고 **필드 경로** 텍스트 상자에서 **TenantId**를 입력한 다음 **확인**을 두 번 클릭합니다.
1. 라이브러리 컨트롤에서 검색 텍스트 상자에 **Set-AzureRmContext** 을 입력합니다.
1. 캔버스에 **Set-AzureRmContext** 를 추가합니다.
1. 캔버스에서 **Set-AzureRmContext**를 선택하고 구성 제어판의 **레이블** 텍스트 상자에 **구독 ID 지정**을 입력합니다.
1. **매개 변수**를 클릭하면 활동 매개 변수 구성 페이지가 표시됩니다.
1. **Set-AzureRmContext**에는 여러 매개 변수 집합이 있으므로 매개 변수 값을 입력하기 전에 하나를 선택해야 합니다. **매개 변수 집합**을 클릭한 후 **SubscriptionId** 매개 변수 집합을 선택합니다.
1. 매개 변수 집합을 선택하면, 활동 매개 변수 구성 페이지에 매개 변수가 표시됩니다. **SubscriptionID**
1. 매개 변수 값 페이지에서 **데이터 원본**에 대해 **변수 자산**을 선택하고 목록에서 **AzureSubscriptionId**를 선택한 다음 **확인**을 두 번 클릭합니다.
1. 아래에 원 모양이 나타날 때까지 **Azure에 로그인** 을 마우스로 가리킵니다. 원을 클릭하고 화살표를 **구독 ID 지정**으로 끌어 놓습니다.

이 시점에서 runbook이 아래와 같이 표시됩니다. <br>![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>VM을 시작하기 위한 활동 추가

여기서는 가상 머신을 시작하기 위한 **Start-AzureRmVM** 작업을 추가합니다. Azure 구독에서 가상 머신을 선택할 수 있지만, 지금은 cmdlet에 해당 이름을 하드 코드합니다.

1. 라이브러리 컨트롤에서 검색 텍스트 상자에 **Start-AzureRm** 을 입력합니다.
2. **Start-AzureRmVM**을 캔버스에 추가한 다음 클릭하여 **구독 ID 지정** 아래로 끌어 놓습니다.
1. 도형 아래에 원이 나타날 때까지 **구독 ID 지정** 을 마우스로 가리킵니다. 원을 클릭하고 화살표를 **Start-AzureRmVM**으로 끌어 놓습니다.
1. **Start-AzureRmVM**을 선택합니다. **매개 변수**와 **매개 변수 집합**을 차례로 클릭하여 **Start-AzureRmVM**에 대한 집합을 확인합니다. **ResourceGroupNameParameterSetName** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **이름** 옆에는 느낌표가 있습니다. 이것은 매개 변수가 필요하다는 것을 나타냅니다. 또한, 양 쪽 모두에 문자열 값이 필요합니다.
1. **Name**을 선택합니다. **데이터 원본**에서 **PowerShell 식**을 선택하고, 이 Runbook으로 시작될 가상 머신의 이름을 큰따옴표로 묶어서 입력합니다. **확인**을 클릭합니다.
1. **ResourceGroupName**을 선택합니다. **PowerShell 식** 에 대해 **데이터 원본** 을 사용하고 리소스 그룹의 이름을 큰따옴표 안에 입력합니다. **확인**을 클릭합니다.
1. Runbook을 테스트할 수 있도록 테스트 창을 클릭합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면, 가상 머신이 시작되었다는 것을 확인합니다.

이 시점에서 runbook이 아래와 같이 표시됩니다. <br>![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>추가 입력 매개 변수 추가

현재 Runbook은 **Start-AzureRmVM** cmdlet에 지정한 리소스 그룹에서 가상 머신을 시작합니다. Runbook은 Runbook을 시작할 때 둘 다 지정하는 경우에 더욱 유용합니다. 이제 Runbook에 입력 매개 변수를 추가하여 해당 기능을 제공합니다.

1. **MyFirstRunbook-Graphical** 창에서 **편집**을 클릭하여 그래픽 편집기를 엽니다.
1. **입력 및 출력**과 **입력 추가**를 차례로 선택하여 Runbook 입력 매개 변수 창을 엽니다.
1. *Name* 에 대해 **VMName**을 지정합니다. **형식**에 대해 *문자열*을 유지하고 **필수**를 *예*로 변경합니다. **확인**을 클릭합니다.
1. *ResourceGroupName*이라는 두 번째 필수 입력 매개 변수를 만든 다음 **확인**을 클릭하여 **입력 및 출력** 창을 닫습니다.<br> ![Runbook 입력 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. **Start-AzureRmVM** 활동을 선택하고 **매개 변수**를 클릭합니다.
1. **이름**에 대한 **데이터 원본**을 **Runbook 입력**으로 변경한 다음 **VMName**을 선택합니다.
1. **ResourceGroupName**에 대한 **데이터 원본**을 **Runbook 입력**으로 변경한 다음 **ResourceGroupName**을 선택합니다.<br> ![Start-azurevm 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에서 사용하는 두 입력 변수에 대한 값을 제공할 수 있습니다.
1. 창을 닫습니다.
1. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
1. 이전 단계에서 실행시킨 가상 머신을 중지합니다.
1. **시작** 을 클릭하여 runbook을 시작합니다. 시작하려는 가상 머신의 **VMName** 및 **ResourceGroupName**을 입력합니다.
1. Runbook이 완료되면 가상 머신이 시작되었다는 것을 확인합니다.

## <a name="create-a-conditional-link"></a>조건부 링크 만들기

이제 Runbook을 수정하여 아직 시작하지 않은 경우에만 가상 머신을 시작하려고 시도합니다. 이렇게 하려면 가상 머신의 인스턴스 수준 상태를 가져오는 Runbook에 **Get-AzureRmVM** cmdlet을 추가합니다. 그런 다음 PowerShell 코드 조각과 함께 **Get Status**라는 PowerShell 워크플로 코드 모듈을 추가하여 가상 머신 상태가 실행 중이거나 중지되었는지 확인합니다. **상태 가져오기** 모듈의 조건부 링크는 현재 실행 중인 상태가 중지되는 경우에만 **Start-AzureRmVM**을 실행합니다. 마지막으로 VM이 성공적으로 시작되었거나 PowerShell Write-Output cmdlet을 사용하지 않았다고 알려주는 메시지를 출력합니다.

1. 그래픽 편집기에서 **MyFirstRunbook-Graphical**을 엽니다.
1. **구독 ID 지정**과 **Start-AzureRmVM** 간의 링크를 클릭하고 *삭제* 키를 눌러 제거합니다.
1. 라이브러리 컨트롤에서 검색 텍스트 상자에 **Get-AzureRm** 을 입력합니다.
1. 캔버스에 **Get-AzureRmVM** 을 추가합니다.
1. **Get-AzureRmVM**과 **매개 변수 집합**을 차례로 선택하여 **Get-AzureRmVM**에 대한 집합을 표시합니다. **GetVirtualMachineInResourceGroupNameParamSet** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **이름** 옆에는 느낌표가 있습니다. 이것은 매개 변수가 필요하다는 것을 나타냅니다. 또한, 양 쪽 모두에 문자열 값이 필요합니다.
1. **이름**에 대한 **데이터 원본**에서 **Runbook 입력**을 선택한 다음 **VMName**을 선택합니다. **확인**을 클릭합니다.
1. **ResourceGroupName**에 대한 **데이터 원본**에서 **Runbook 입력**을 선택한 다음 **ResourceGroupName**을 선택합니다. **확인**을 클릭합니다.
1. **상태**에 대한 **데이터 원본**에서 **상수 값**을 선택한 다음 **True**를 클릭합니다. **확인**을 클릭합니다.
1. **구독 ID 지정**에서 **Get-AzureRmVM**에 대한 링크를 만듭니다.
1. 라이브러리 컨트롤에서 **Runbook 컨트롤**을 확장하고 캔버스에 **코드**를 추가합니다.  
1. **Get-AzureRmVM**에서 **코드**에 대한 링크를 만듭니다.  
1. **코드**를 클릭하고 구성 창에서 레이블을 **상태 가져오기**로 변경합니다.
1. **코드** 매개 변수를 선택하면 **코드 편집기** 페이지가 나타납니다.  
1. 코드 편집기에 다음 코드 조각을 붙여 넣습니다.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. **상태 가져오기**에서 **Start-AzureRmVM**에 대한 링크를 만듭니다.<br> ![코드 모듈과 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 링크를 선택하고 구성 창에서 **조건 적용**을 **예**로 변경합니다. 링크가 True로 확인되는 조건에서만 대상 활동이 실행된다고 나타내는 파선으로 바뀝니다.  
1. **조건 식**에 *$ActivityOutput['Get Status'] -eq "Stopped"* 를 입력합니다. 이제 가상 머신이 중지된 경우에만 **Start-AzureRmVM**이 실행됩니다.
1. 라이브러리 컨트롤에서 **Cmdlet**과 **Microsoft.PowerShell.Utility**를 차례로 확장합니다.
1. 캔버스에 **Write-Output**을 두 번 추가합니다.
1. 첫 번째 **Write-Output** 컨트롤에서 **매개 변수**를 클릭하고 **레이블** 값을 *VM 시작 알림*으로 변경합니다.
1. **InputObject**에 대해, **데이터 원본**을 **PowerShell 식**으로 변경하고 *"$VMName successfully started"* 식을 입력합니다.
1. 두 번째 **Write-Output** 컨트롤에서 **매개 변수**를 클릭하고 **레이블** 값을 *VM 시작 실패 알림*으로 변경합니다.
1. **InputObject**에서 **데이터 원본**을 **PowerShell 식**으로 변경하고 *"$VMName could not start"* 식을 입력합니다.
1. **Start-AzureRmVM**에서 **VM 중지 알림** 및 **VM 시작 실패 알림**에 대한 링크를 만듭니다.
1. **VM 시작 알림** 링크를 선택하고 **조건 적용**을 **True**로 변경합니다.
1. **조건 식**에 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*를 입력합니다. 이제 가상 머신이 성공적으로 시작된 경우에만 이 Write-Output 컨트롤이 실행됩니다.
1. **VM 시작 실패 알림** 링크를 선택하고 **조건 적용**을 **True**로 변경합니다.
1. **조건 식**에 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*를 입력합니다. 이제 가상 머신이 성공적으로 시작되지 않은 경우에만 이 Write-Output 컨트롤이 실행됩니다. Runbook이 다음 이미지와 같이 표시됩니다. <br> ![쓰기 출력으로 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Runbook을 저장하고 테스트 창을 엽니다.
1. 중지된 가상 컴퓨터로 runbook을 시작합니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)

