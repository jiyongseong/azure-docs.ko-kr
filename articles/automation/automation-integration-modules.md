---
title: Azure Automation 통합 모듈 만들기
description: Azure Automation의 통합 모듈을 만들고 테스트하며 예제를 사용하는 과정을 안내하는 자습서입니다.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e7135e620ab799131f772c16f6799ed80be312e0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-integration-modules"></a>Azure Automation 통합 모듈
PowerShell은 Azure Automation의 기본 기술입니다. Azure Automation은 PowerShell을 기반으로 하기 때문에 PowerShell 모듈은 Azure Automation의 확장성에 대한 키입니다. 이 문서에서는 "통합 모듈"이라고 하는 PowerShell 모듈에서 Azure Automation을 만드는 세부 정보 및 Azure Automation 내에서 통합 모듈로 작동하도록 고유한 PowerShell 모듈을 만드는 모범 사례를 안내합니다. 

## <a name="what-is-a-powershell-module"></a>PowerShell 모듈이란?
PowerShell 모듈은 WindowsFeature 또는 파일과 같은 PowerShell 콘솔, 스크립트, 워크플로, Runbook 및 PowerShell DSC 리소스에서 사용할 수 있고 PowerShell DSC 구성에서 사용할 수 있는 **Get-Date** 또는 **Copy-Item**와 같은 PowerShell cmdlet의 그룹입니다. 모든 PowerShell의 기능은 cmdlet 및 DSC 리소스를 통해 노출되고 모든 cmdlet/DSC 리소스는 PowerShell 모듈에서 지원됩니다. 이 대부분은 PowerShell 자체와 함께 지원됩니다. 예를 들어 **Get-Date** cmdlet은 Microsoft.PowerShell.Utility PowerShell 모듈의 일부이며 **Copy-Item** cmdlet은 Microsoft.PowerShell.Management PowerShell 모듈의 일부이고 패키지 DSC 리소스는 PSDesiredStateConfiguration PowerShell 모듈의 일부입니다. 이러한 모듈은 모두 PowerShell과 함께 제공됩니다. 하지만 많은 PowerShell 모듈은 PowerShell의 일부로 제공되지 않으며 대신 System Center 2012 Configuration Manager에서 자사 또는 타사 제품으로 배포되거나 PowerShell 갤러리와 같은 곳의 방대한 PowerShell 커뮤니티에서 배포됩니다. 모듈은 캡슐화된 기능을 통해 복잡한 작업을 간단하게 만들 수 있기 때문에 유용합니다.  [MSDN의 PowerShell 모듈](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx)에 대해 자세히 알아볼 수 있습니다. 

## <a name="what-is-an-azure-automation-integration-module"></a>Azure Automation 통합 모듈이란?
통합 모듈은 PowerShell 모듈과 다르지 않습니다. 단순히 필요에 따라 추가 파일(Runbook에서 모듈의 cmdlet과 함께 사용되는 Azure Automation 연결 형식을 지정하는 메타데이터 파일)을 포함하는 PowerShell 모듈입니다. 선택 사항 파일인지 여부와 상관 없이 이러한 PowerShell 모듈은 Azure Automation으로 가져와서 DSC 구성 내에서 사용할 수 있는 runbook 및 DSC 리소스 내에서 해당 cmdlet을 사용할 수 있도록 합니다. 배후에서 Azure Automation는 이러한 모듈을 저장하고 runbook 작업 및 DSC 컴파일 작업 실행 시 runbook을 실행하고 DSC 구성을 컴파일하는 Azure Automation 샌드박스에 로드합니다. 또는 모듈의 DSC 리소스는 DSC 구성을 적용하려는 컴퓨터에서 가져올 수 있도록 자동으로 Automation DSC 끌어오기 서버에 배치됩니다.  

Azure 관리를 즉시 자동화하기 시작할 수 있도록 Azure Automation에서 다양한 Azure PowerShell 모듈을 기본적으로 제공하지만 통합하려는 시스템, 서비스 또는 도구가 무엇이든 PowerShell 모듈을 가져올 수 있습니다. 

> [!NOTE]
> 특정 모듈은 Automation 서비스에서 "전역 모듈"로 제공됩니다. 이러한 전역 모듈은 Automation 계정을 만들 경우에 제공되고 때로 업데이트되어 Automation 계정에 자동으로 푸시됩니다. 자동 업데이트하지 않으려면 언제든지 같은 모듈을 직접 가져올 수 있습니다. 그렇게 하면 서비스에 제공된 해당 모듈의 전역 모듈 버전보다 우선적으로 적용됩니다. 

통합 모듈 패키지를 가져온 형식은 모듈 및.zip 확장명과 동일한 이름을 가진 압축된 파일입니다. Windows PowerShell 모듈 및 모듈에 있는 매니페스트 파일(.psd1)를 포함한 모든 지원 파일을 포함합니다.

모듈이 Azure Automation 연결 형식을 포함해야 하는 경우 연결 유형 속성을 지정하는 `<ModuleName>-Automation.json`이라는 이름의 파일도 포함해야 합니다. 압축된.zip 파일의 모듈 폴더 내에 배치된 json 파일이며 모듈이 나타내는 시스템 또는 서비스에 연결하는 데 필요한 "연결"의 필드를 포함합니다. 결국 Azure Automation의 연결 형식을 만들게 됩니다. 이 파일을 사용하여 필드 이름을 설정할 수 있고 필드에 관계 없이 모듈의 연결 형식에 대한 암호화 및/또는 선택적이어야 합니다. 다음은 json 파일 형식인 템플릿입니다.

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

서비스 관리 Automation을 배포하고 Automation Runbook에 통합 모듈 패키지를 만든 경우 분명히 익숙하게 느껴질 것입니다. 

## <a name="authoring-best-practices"></a>작성 모범 사례
통합 모듈이 기본적으로 PowerShell 모듈임에도 불구하고 Azure Automation에서도 유용하게 만들려면 PowerShell 모듈을 작성하는 동안 고려해야 할 점이 많습니다. 그 중 일부는 Azure Automation에 특정되고 일부는 Automation을 사용하는지 여부에 관계 없이 PowerShell 워크플로에서 모듈이 잘 작동할 수 있도록 합니다. 

1. 모듈에서 모든 cmdlet에 대한 개요, 설명 및 도움말 URI를 포함합니다. PowerShell에서는 cmdlet에 대한 도움말 정보를 정의하여 사용자가 **Get-Help** cmdlet과 해당 정보를 통해 도움을 받을 수 있습니다. 예를 들어 .psm1 파일에 기록된 PowerShell 모듈에 대한 개요 및 도움말 URI를 정의할 수 있는 방법은 다음과 같습니다.<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
   <br> 이 정보를 제공하면 PowerShell 콘솔에서 **Get-Help** cmdlet을 사용하는 도움말을 보여 줄 뿐만 아니라 Azure Automation 내에서 이 도움말 기능을 노출할 수도 있습니다.  예를 들어, runbook을 작성하는 동안 활동을 삽입하는 경우입니다. "자세한 도움말 보기"를 클릭하면 Azure Automation에 액세스하는 데 사용하는 웹 브라우저의 다른 탭에서 도움말 URI가 열립니다.<br>![통합 모듈 도움말](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. 원격 시스템에 대해 모듈을 실행하는 경우

    a. 해당 원격 시스템 즉, 파일 연결 형식에 연결하는 데 필요한 정보를 정의하는 통합 모듈 메타데이터 파일을 포함해야 합니다.  
    나. 모듈의 각 cmdlet은 매개 변수로 연결 개체(해당 연결 형식의 인스턴스)를 포함해야 합니다.  

    연결 형식의 필드가 포함된 개체를 cmdlet에 대한 매개 변수로 전달하도록 허용하는 경우 모듈의 cmdlet은 Azure Automation에서 쉽게 사용할 수 있게 됩니다. 이 방식으로 사용자는 cmdlet를 호출할 때마다 cmdlet의 해당 매개 변수에 연결 자산의 매개 변수를 매핑할 필요가 없습니다. 위의 runbook 예제에 따라 CorpTwilio라는 Twilio 연결 자산을 사용하여 Twilio에 액세스하고 계정에서 모든 전화 번호를 반환합니다.  cmdlet의 매개 변수에 연결의 필드를 매핑하는 방법을 알고 있나요?<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    이에 접근하는 더 쉽고 나은 방법은 cmdlet에 직접 연결 개체를 전달하는 것입니다 -
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    매개 변수에 대한 연결 필드 대신에 매개 변수인 연결 개체를 직접 수용할 수 있도록 하여 cmdlet에 다음과 같은 동작을 설정할 수 있습니다. 일반적으로 각각에 대해 매개 변수를 설정하여 Azure Automation을 사용하는 사용자가 hashtable을 생성하지 않고 cmdlet를 호출하여 연결 개체의 역할을 할 수 있도록 합니다. 연결 필드 속성을 하나씩 전달하는 데 다음 매개 변수 집합 **SpecifyConnectionFields** 를 사용합니다. **UseConnectionObject** 를 사용하면 연결을 직접 전달할 수 있습니다. 보시다시피 [Twilio PowerShell 모듈](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) 의 Send-TwilioSMS cmdlet을 사용하면 한 쪽을 전달할 수 있습니다. 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```
   <br>
3. 모듈에서 모든 cmdlet에 대한 출력 형식을 정의합니다. cmdlet에 대한 출력 형식을 정의하면 디자인 타임 IntelliSense에서 작성 중에 사용하기 위한 cmdlet의 출력 속성을 확인할 수 있습니다. Automation runbook 그래픽을 작성하는 동안 특히 유용하며 이 경우 디자인 타임 지식은 모듈을 사용하는 쉬운 사용자 환경의 키입니다.<br><br> ![그래픽 Runbook 출력 형식](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> PowerShell ISE에서 cmdlet의 출력을 실행하지 않는 "사전 입력" 기능과 비슷합니다.<br><br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. 모듈의 Cmdlet은 매개 변수에 복잡한 개체 유형을 사용하지 않아야 합니다. PowerShell 워크플로는 복합 형식을 역직렬화된 형태로 저장한다는 점에서 PowerShell과 다릅니다. 기본 형식은 기본 요소로 유지되지만 복합 형식은 역직렬화된 버전으로 변환되며 이는 기본적으로 속성 모음입니다. 예를 들어 runbook(또는 문제에 대한 PowerShell 워크플로)에서 **Get-Process** cmdlet을 사용하는 경우 예상된 [System.Diagnostic.Process] 형식이 아닌 [Deserialized.System.Diagnostic.Process] 형식의 개체를 반환합니다. 이 형식에는 역직렬화된 형식으로 동일한 속성이 포함되지만 메서드는 포함되지 않습니다. 이 값을 cmdlet에 대한 매개 변수로 전달하려는 경우 cmdlet에서 이 매개 변수에 대한 [System.Diagnostic.Process] 값이 필요하면 다음 오류가 표시됩니다. *'프로세스' 매개 변수에서 인수 변환을 처리할 수 없습니다. 오류: "Deserialized.System.Diagnostics.Process" 형식의 "System.Diagnostics.Process(CcmExec)" 값을 "System.Diagnostics.Process" 형식으로 변환할 수 없습니다.*   예상된 [System.Diagnostic.Process] 형식 및 지정된 [Deserialized.System.Diagnostic.Process] 형식 간의 형식이 일치하지 않기 때문입니다. 이 문제를 해결하는 방법은 모듈의 cmdlet이 매개 변수에 복합 형식을 사용하지 않도록 하는 것입니다. 작업을 수행하는 잘못된 방법은 다음과 같습니다.
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    또한 복잡한 개체를 선택하고 사용하는 cmdlet이 내부적으로 사용할 수 있는 기본 요소를 가져오는 올바른 방법은 다음과 같습니다. cmdlet이 PowerShell 워크플로가 아닌 PowerShell의 컨텍스트에서 실행되기 때문에 cmdlet 내에서 $process는 잘못된 [System.Diagnostic.Process] 형식이 됩니다.  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Runbook의 연결 자산은 복합 형식인 hashtable이며 이러한 hashtable은 캐스트 예외 없이 해당 –Connection 매개 변수에 대한 cmdlet에 전달될 수 있습니다. 기술적으로 일부 PowerShell 형식은 직렬화된 형식에서 역직렬화된 형식으로 제대로 캐스팅할 수 있으며 따라서 역직렬화되지 않은 형식을 허용하는 매개 변수에 대한 cmdlet에 전달될 수 있습니다. Hashtable은 다음 중 하나입니다. 모듈 작성자가 정의한 형식이 올바르게 역직렬화되는 방식으로 구현될 수 있지만 고려해야 할 장단점이 있습니다. 형식에는 기본 생성자, 모든 공용 속성 및 는 PSTypeConverter가 있어야 합니다. 그러나 모듈 작성자가 소유하지 않은 미리 정의된 형식의 경우 "수정"할 방법이 없습니다. 따라서 매개 변수 모두에 대한 복합 형식을 방지하는 것이 좋습니다. Runbook 작성 팁: 어떤 이유로든 cmdlet이 복합 형식 매개 변수를 사용해야 하거나 사용자가 복합 형식 매개 변수가 필요한 다른 사람의 모듈을 사용하는 경우 로컬 PowerShell의 PowerShell 워크플로 runbook 및 PowerShell 워크플로 해결 방법은 복합 형식을 생성하는 cmdlet 및 동일한 InlineScript 작업에서 복합 형식을 사용하는 cmdlet을 래핑하는 것입니다. InlineScript가 해당 콘텐츠를 PowerShell 워크플로 대신 PowerShell으로 실행하기 때문에 복합 형식을 생성하는 cmdlet에서는 역직렬화된 복합 형식이 아닌 올바른 형식을 생성합니다.
5. 모듈의 모든 cmdlet을 상태 비저장으로 만듭니다. PowerShell 워크플로는 다른 세션에서 워크플로 호출하는 모든 cmdlet을 실행합니다. 즉, 동일한 모듈의 다른 cmdlet에서 생성/수정된 세션 상태에 의존하는 cmdlet은 PowerShell 워크플로 runbook에서 작동하지 않습니다.  다음은 수행해서는 안되는 작업의 예제입니다.
   
    ```
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```
   <br>
6. 모듈은 Xcopy 가능 패키지에 완전히 포함되어야 합니다. runbook을 실행해야 하는 경우 Azure Automation 모듈이 Automation 샌드박스에 분산되어 있기 때문에 실행 중인 호스트와 독립적으로 작동해야 합니다. 즉, 모듈 패키지를 압축하고 동일하거나 최신 PowerShell 버전인 다른 호스트에 이동하며 해당 호스트의 PowerShell 환경으로 가져오는 경우 정상적으로 작동할 수 있도록 해야 합니다. 이를 위해서 모듈은 모듈 폴더(Azure Automation으로 가져올 때 압축된 폴더) 외부의 파일 또는 호스트의 고유한 레지스트리 설정(제품의 설정으로 설정됨)에 의존해서 안됩니다. 이 모범 사례를 따르지 않으면 모듈은 Azure Automation에서 사용할 수 없습니다.  

## <a name="next-steps"></a>다음 단계

* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)
* PowerShell 모듈을 만드는 자세한 내용은 [Windows PowerShell 모듈 작성](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)

