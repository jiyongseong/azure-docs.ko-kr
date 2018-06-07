---
title: Log Analytics에 Azure Automation 작업 데이터 전달
description: 이 문서에서는 작업 상태 및 Runbook 작업 스트림을 Azure Log Analytics로 보내 통찰력 및 관리를 강화하는 방법을 알아봅니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 562b1f1371133a1da8d24ebbb9c588f0597dda7f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics"></a>Automation에서 Log Analytics로 작업 상태 및 작업 스트림 전달
Automation에서는 Log Analytics 작업 영역으로 Runbook 작업 상태 및 작업 스트림을 보낼 수 있습니다. 개별 작업에 대해 Azure Portal에서 또는 PowerShell을 사용하여 작업 로그 및 작업 스트림을 볼 수 있으며 이를 통해 보다 간단한 조사가 가능합니다. 이제 Log Anaytics를 사용하여 다음을 수행할 수 있습니다.

* Automation 작업에 대한 통찰력 확보
* Runbook 작업 상태(예: 실패 또는 일시 중단)를 기반으로 이메일 또는 경고 트리거
* 작업 스트림에서 고급 쿼리 작성
* Automation 계정 간에 작업 상호 연결
* 시간별 작업 기록 시각화

## <a name="prerequisites-and-deployment-considerations"></a>필수 구성 요소 및 배포 고려 사항
Automation 로그를 Log Analytics로 보내려면 다음이 필요합니다.

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)의 2016년 11월(v2.3.0) 이후 릴리스
* Log Analytics 작업 영역. 자세한 내용은 [Log Analytics 시작](../log-analytics/log-analytics-get-started.md)을 참조하세요. 
* Azure Automation 계정에 대한 ResourceId


Azure Automation 계정에 대한 ResourceId를 찾으려면:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Log Analytics 작업 영역에 대한 ResourceId를 찾으려면 다음 PowerShell을 실행합니다.

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

두 개 이상의 Automation 계정 또는 작업 영역이 있는 경우 이전 명령의 출력에서 구성해야 하는 *이름*을 찾고 *ResourceId* 값을 복사합니다.

Automation 계정의 *Name*을 찾으려면 Azure Portal의 **Automation 계정** 블레이드에서 Automation 계정을 선택한 다음 **모든 설정**을 선택합니다. **계정 설정** 아래에 있는 **모든 설정** 블레이드에서 **속성**을 선택합니다.  **속성** 블레이드에서 이들 값을 기록할 수 있습니다.<br> ![Automation 계정 속성](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)을 참조하세요.

## <a name="set-up-integration-with-log-analytics"></a>Log Analytics와의 통합 설정

1. 컴퓨터의 **시작** 화면에서 **Windows PowerShell**을 시작합니다.
2. 다음 PowerShell을 실행하고, 이전 단계의 값을 사용하여 `[your resource id]` 및 `[resource id of the log analytics workspace]`의 값을 편집합니다.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

이 스크립트를 실행한 후 작성 중인 새 JobLogs 또는 JobStreams의 10분 이내에 Log Analytics에 레코드가 표시됩니다.

로그를 보려면 Log Analytics 로그 검색에서 다음 쿼리를 실행합니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>구성 확인
Automation 계정이 Log Analytics 작업 영역으로 로그를 보내는지 확인하려면 다음 PowerShell을 사용하여 Automation 계정에서 진단이 올바르게 구성되었는지 확인합니다.

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

출력에서 다음을 확인합니다.
+ *로그*에서 *사용됨* 값이 *True*인지 여부
+ *WorkspaceId* 값이 Log Analytics 작업 영역의 ResourceId로 설정되어 있는지 여부

## <a name="log-analytics-records"></a>Log Analytics 레코드

Azure Automation의 진단은 Log Analytics에 두 가지 유형의 레코드를 만들고 **AzureDiagnostics**로 태그가 지정됩니다. 다음 쿼리는 Log Analytics에 업그레이드된 쿼리 언어를 사용합니다. 레거시 쿼리 언어와 새로운 Azure Log Analytics 쿼리 언어 간의 일반적인 쿼리에 대한 내용은 [새 Azure Log Analytics 쿼리 언어 치트 시트에 대한 레거시](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)를 방문하세요.

### <a name="job-logs"></a>작업 로그
| 자산 | 설명 |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 사람입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| JobId_g |runbook 작업의 ID인 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 신규<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- 완료됨 |
| Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobLogs입니다. |
| OperationName | Azure에서 수행되는 작업 유형을 지정합니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | Automation 계정의 이름입니다. |
| SourceSystem | Log Analytics가 데이터를 수집한 방법입니다. Azure 진단의 경우 항상 *Azure*입니다. |
| ResultDescription |runbook 작업 결과 상태를 설명합니다. 가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다. |
| CorrelationId |runbook 작업의 상관 관계 ID인 GUID입니다. |
| ResourceId |Runbook의 Azure Automation 계정 리소스 ID를 지정합니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 ID(GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>작업 스트림
| 자산 | 설명 |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 사람입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| StreamType_s |작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>- 진행<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| JobId_g |runbook 작업의 ID인 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 진행 중 |
| Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobStreams입니다. |
| OperationName | Azure에서 수행되는 작업 유형을 지정합니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | Automation 계정의 이름입니다. |
| SourceSystem | Log Analytics가 데이터를 수집한 방법입니다. Azure 진단의 경우 항상 *Azure*입니다. |
| ResultDescription |runbook의 출력 스트림을 포함합니다. |
| CorrelationId |runbook 작업의 상관 관계 ID인 GUID입니다. |
| ResourceId |Runbook의 Azure Automation 계정 리소스 ID를 지정합니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 ID(GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Log Analytics에서 Automation 로그 보기
Automation 작업 로그를 Log Analytics로 보내기 시작했으므로 이제 Log Analytics 내에서 이러한 로그로 수행할 수 있는 작업을 살펴보겠습니다.

로그를 보려면 다음 쿼리를 실행합니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook 작업이 실패하거나 일시 중단된 경우 전자 메일 보내기
고객이 자주 묻는 질문 중 하나는 Runbook 작업에 문제가 발생한 경우 이메일 또는 텍스트를 보낼 수 있는지 여부입니다.   

경고 규칙을 만들려면 경고를 호출해야 하는 runbook 작업 레코드에 대한 로그 검색을 만드는 것으로 시작합니다. **경고** 단추를 클릭하여 경고 규칙을 만들고 구성합니다.

1. Log Analytics 개요 페이지에서 **로그 검색**을 클릭합니다.
2. 쿼리 필드에 다음 검색을 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` 다음을 사용하여 RunbookName별로 그룹화할 수도 있습니다.`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   둘 이상의 Automation 계정 또는 구독에서 작업 영역으로의 로그를 설정한 경우 구독 또는 Automation 계정별로 경고를 그룹화할 수 있습니다. Automation 계정 이름은 JobLogs 검색의 리소스 필드에서 찾을 수 있습니다.
1. **경고 규칙 추가** 화면을 열려면 페이지 위쪽의 **경고**를 클릭합니다. 경고 구성 옵션에 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md#alert-rules)를 참조하세요.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>오류와 함께 완료된 모든 작업 찾기
오류에 대한 경고 외에도, runbook 작업에 대해 비종료 오류가 발생하는 경우를 확인할 수 있습니다. 이러한 경우에 PowerShell은 오류 스트림을 생성하지만 비종료 오류가 발생해도 작업이 일시 중단되거나 실패하지 않습니다.    

1. Log Analytics 작업 영역에서 **로그 검색**을 클릭합니다.
2. 쿼리 필드에서 `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`를 입력하고 **검색** 단추를 클릭합니다.

### <a name="view-job-streams-for-a-job"></a>작업에 대한 작업 스트림 보기
작업을 디버깅할 때 작업 스트림을 살펴볼 수도 있습니다. 다음 쿼리는 GUID가 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0인 단일 작업의 모든 스트림을 보여 줍니다.   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>기록 작업 상태 보기
마지막으로 시간별 작업 기록을 시각화할 수 있습니다. 이 쿼리를 사용하여 시간이 지남에 따른 작업 상태를 검색할 수 있습니다.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Log Analytics 기록 작업 상태 차트](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>요약
Automation 작업 상태 및 스트림 데이터를 Log Analytics로 전송하면 다음과 같은 작업을 통해 Automation 작업의 상태를 보다 정확히 파악할 수 있습니다.
+ 문제가 발생할 때 알리도록 경고 설정
+ 사용자 지정 보기와 검색 쿼리를 사용하여 runbook 결과, runbook 작업 상태 및 기타 관련된 핵심 지표 또는 메트릭 시각화.  

Log Analytics는 Automation 작업의 작동을 보다 정확히 이해하도록 하며 인시던트를 더 빠르게 해결하도록 도와줍니다.  

## <a name="next-steps"></a>다음 단계
* Log Analytics를 사용하여 여러 검색 쿼리를 작성하고 Automation 작업 로그를 검토하는 방법에 대한 자세한 내용은 [Log Analytics의 로그 검색](../log-analytics/log-analytics-log-searches.md)을 참조하세요.
* Runbook에서 출력 및 오류 메시지를 만들고 검색하는 방법을 이해하려면 [Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조하세요.
* runbook 실행, runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [runbook 작업 추적](automation-runbook-execution.md)을 참조하세요.
* Log Analytics 및 데이터 수집 소스에 대해 자세히 알아보려면 [Log Analytics에서 Azure Storage 데이터 수집 개요](../log-analytics/log-analytics-azure-storage.md)를 참조하세요.
