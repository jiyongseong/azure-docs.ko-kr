---
title: Log Analytics 경고에서 Azure Automation Runbook 호출
description: 이 문서는 Azure의 Log Analytics 경고에서 Automation Runbook을 호출하는 방법에 대한 개요를 제공합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a3b14bd8409226772d210f60dadd525960f7890
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Log Analytics 경고에서 Azure Automation Runbook 호출

Azure Log Analytics에서 경고를 구성하여 결과가 조건에 일치하면 경고 레코드를 만들 수 있습니다. 그런 다음 문제를 자동으로 해결하기 위해, 경고가 Azure Automation Runbook을 자동으로 실행할 수 있습니다. 

예를 들어 경고가 장기간에 걸친 프로세서 사용률의 급증을 나타낼 수 있습니다. 또는 비즈니스 응용 프로그램의 기능에 중요한 응용 프로그램 프로세스가 실패하는 시기를 나타낼 수 있습니다. Runbook은 그런 다음 Windows 이벤트 로그에 해당 이벤트를 기록할 수 있습니다.  

경고 구성에서 Runbook을 호출하는 옵션은 두 가지입니다.

* 웹후크를 사용합니다.
   * Log Analytics 작업 영역이 Automation 계정에 연결되지 않은 경우 이 옵션만 사용할 수 있습니다.
   * Log Analytics 작업 영역에 연결된 Automation 계정이 이미 있는 경우에도 이 옵션을 계속 사용할 수 있습니다.  

* Runbook을 직접 선택합니다.
   * 이 옵션은 Log Analytics 작업 영역이 Automation 계정에 연결된 경우에만 사용할 수 있습니다.

## <a name="calling-a-runbook-by-using-a-webhook"></a>웹후크를 사용하여 Runbook 호출

웹후크를 사용하여 단일 HTTP 요청을 통해 Azure Automation에서 특정 Runbook을 시작할 수 있습니다. 웹후크를 경고 작업으로 사용하여 Runbook을 호출하도록 [Log Analytics 경고](../log-analytics/log-analytics-alerts.md#alert-rules)를 구성하기 전에 이 메서드를 통해 호출되는 Runbook에 대한 [웹후크를 만들어야](automation-webhooks.md#creating-a-webhook)합니다. 경고 규칙을 구성하는 동안 웹후크 URL을 참조할 수 있도록 웹후크 URL을 기록해야 합니다.   

## <a name="calling-a-runbook-directly"></a>Runbook 직접 호출

Log Analytics 작업 영역에서 Automation and Control 제품을 설치 및 구성할 수 있습니다. 경고에 대한 Runbook 작업 옵션을 구성하는 동안, **Runbook 선택** 드롭다운 목록에서 모든 Runbook을 볼 수 있고 경고에 대한 응답으로 실행할 특정 Runbook을 선택할 수 있습니다. 선택한 Runbook은 Azure 작업 영역 또는 Hybrid Runbook Worker에서 실행할 수 있습니다. 

Runbook 옵션을 사용하여 경고를 만들면 웹후크가 Runbook에 대해 생성됩니다. Automation 계정으로 이동하고 선택한 Runbook의 웹후크 창을 열면 웹후크를 볼 수 있습니다. 

경고를 삭제해도 웹후크는 삭제되지 않습니다. 이것은 문제가 되지 않습니다. 웹후크는 구성된 Automation 계정을 유지 관리하기 위해 결국 수동으로 삭제해야 하는 고아 항목이 됩니다.  

## <a name="characteristics-of-a-runbook"></a>Runbook의 특징

Log Analytics 경고에서 Runbook을 호출하기 위한 두 가지 방법에는 경고 규칙을 구성하기 전에 이해할 필요가 있는 특징이 있습니다. 

경고 데이터는 **SearchResult**라는 단일 속성에서 JSON 형식입니다. 이 형식은 표준 페이로드를 포함하는 Runbook 및 웹후크 작업용입니다. 사용자 지정 페이로드(**RequestBody**의 **IncludeSearchResults:True**를 비롯한)를 포함하는 웹후크 작업에서 속성은 **SearchResults**입니다.

**개체** 형식의 **WebhookData**라는 Runbook 입력 매개 변수가 있어야 합니다. 이는 필수 사항이거나 선택 사항일 수 있습니다. 경고는 이 입력 매개 변수를 사용하여 검색 결과를 Runbook으로 전달합니다.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
또한 **WebhookData**를 PowerShell 개체로 변환하는 코드가 있어야 합니다.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult**는 개체의 배열입니다. 각 개체는 하나의 검색 결과 값이 있는 필드를 포함합니다.


## <a name="example-walkthrough"></a>연습 예

다음 그래픽 Runbook 예제는 이 프로세스가 작동하는 방식을 보여줍니다. Windows 서비스를 시작합니다.

![Windows 서비스를 시작하는 그래픽 Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Runbook에는 **WebhookData**라는 **개체** 형식의 입력 매개 변수가 하나 있습니다. 여기에는 **SearchResult**를 포함하는 경고로부터 전달된 웹후크 데이터가 포함됩니다.

![Runbook 입력 매개 변수](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

이 예제에서는 Log Analytics에서 **SvcDisplayName_CF** 및 **SvcState_CF**라는 두 가지 사용자 지정 필드를 만듭니다. 이들 필드는 시스템 이벤트 로그에 기록된 이벤트로부터 서비스 표시 이름과 서비스 상태(즉, 실행 중 또는 중지됨)를 추출합니다. 그런 다음 Windows 시스템에서 인쇄 스풀러 서비스가 중지될 때 이를 감지할 수 있도록 다음 검색 쿼리로 경고 규칙을 만듭니다.

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

아무 서비스나 가능합니다. 이 예제에서는 Windows OS에 포함된 기존 서비스 중 하나를 참조합니다. 경고 작업은 이 예제에서 사용된 Runbook을 실행하고 대상 시스템에서 사용할 수 있는 Hybrid Runbook Worker에서 실행하도록 구성됩니다.   

Runbook 코드 활동인 **LA에서 서비스 이름 가져오기**는 JSON 형식의 문자열을 개체 형식으로 변환하고 **SvcDisplayName_CF** 항목을 필터링합니다. Windows 서비스의 표시 이름을 추출하여 이 값을 다음 활동으로 전달합니다. 이 활동은 서비스를 다시 시작하기 전에 서비스가 중지되었는지 확인합니다. **SvcDisplayName_CF**는 이 예제를 보여주기 위해 Log Analytics에서 생성한 [사용자 지정 필드](../log-analytics/log-analytics-custom-fields.md)입니다.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

서비스가 중지되면 Log Analytics의 경고 규칙은 일치 항목을 검색하고 Runbook을 트리거하고 경고 컨텍스트를 Runbook에 보냅니다. Runbook은 서비스가 중지되었는지 확인하려고 시도합니다. 확인이 되면 Runbook은 서비스를 다시 시작하려고 시도하고, 제대로 시작되었는지 확인하고, 결과를 표시합니다.     

또는, Log Analytics 작업 영역에 연결된 Automation 계정이 없는 경우, 웹후크 작업으로 경고 규칙을 구성할 수 있습니다. 웹후크 작업은 Runbook을 트리거합니다. 또한 앞서 언급한 지침에 따라 JSON 형식의 문자열을 변환하고 **SearchResult**를 필터링하도록 Runbook을 구성합니다.    

>[!NOTE]
> 작업 영역을 [새 Log Analytics 쿼리 언어](../log-analytics/log-analytics-log-search-upgrade.md)로 업그레이드한 경우에는 웹후크 페이로드가 변경됩니다. 형식의 세부 내용은 [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse)에 있습니다.

## <a name="next-steps"></a>다음 단계

* Log Analytics의 경고와 생성 방법에 대한 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md)를 참조하세요.

* 웹후크를 사용하여 Runbook을 트리거하는 방법을 이해하려면 [Azure Automation 웹후크](automation-webhooks.md)를 참조하세요.
