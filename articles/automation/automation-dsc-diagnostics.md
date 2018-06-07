---
title: Log Analytics에 Azure Automation DSC 보고 데이터 전달
description: 이 문서에서는 통찰력 및 관리를 강화할 수 있도록 DSC(필요한 상태 구성) 보고 데이터를 Log Analytics로 보내는 방법을 알아봅니다.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d01042a02f2339f039f23d4f6e021de503dc3815
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="forward-azure-automation-dsc-reporting-data-to-log-analytics"></a>Log Analytics에 Azure Automation DSC 보고 데이터 전달

Automation에서는 DSC 노드 상태 데이터를 Log Analytics 작업 영역으로 보낼 수 있습니다.  
Azure Portal에서 또는 PowerShell을 사용하여 노드 및 노드 구성의 개별 DSC 리소스에 대해 준수 상태를 볼 수 있습니다. Log Anaytics를 사용하여 다음을 수행할 수 있습니다.

* 관리되는 노드 및 개별 리소스에 대한 준수 정보 가져오기
* 준수 상태를 기준으로 전자 메일 또는 경고 트리거
* 관리되는 노드에서 고급 쿼리 작성
* Automation 계정에서 준수 상태 상호 연관
* 시간에 따른 노드 준수 내역 시각화

## <a name="prerequisites"></a>필수 조건

Automation DSC 보고서를 Log Analytics로 보내려면 다음이 필요합니다.

* [Azure PowerShell](/powershell/azure/overview)의 2016년 11월(v2.3.0) 이후 릴리스
* Azure Automation 계정. 자세한 내용은 [Azure Automation 시작](automation-offering-get-started.md)을 참조하세요.
* **Automation & Control** 서비스가 제공되는 Log Analytics 작업 공간 자세한 내용은 [Log Analytics 시작](../log-analytics/log-analytics-get-started.md)을 참조하세요.
* 하나 이상의 Azure Automation DSC 노드. 자세한 내용은 [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)를 참조하세요.

## <a name="set-up-integration-with-log-analytics"></a>Log Analytics와의 통합 설정

Azure Automation DSC의 데이터를 Log Analytics로 가져오려면 다음 단계를 완료합니다.

1. PowerShell에서 Azure 계정에 로그인합니다. [Azure PowerShell을 사용하여 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)을 참조하세요.
1. 다음 PowerShell 명령을 실행하여 Automation 계정의 _ResourceId_를 가져옵니다(Automation 계정이 2개 이상 있으면 구성하려는 계정의 _ResourceID_ 선택).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. 다음 PowerShell 명령을 실행하여 Log Analytics 작업 영역의 _ResourceId_를 가져옵니다(작업 영역이 2개 이상 있으면 구성하려는 작업 영역의 _ResourceID_ 선택).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. 다음 PowerShell 명령을 실행하고 `<AutomationResourceId>` 및 `<WorkspaceResourceId>`를 각 이전 단계의 _ResourceId_ 값으로 바꿉니다.

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Azure Automation DSC에서 Log Analytics로의 데이터 가져오기를 중지하려면 다음 PowerShell 명령을 실행합니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>DSC 로그 보기

Automation DSC 데이터에 대해 Log Analytics과의 통합을 설정한 후에는 Automation 계정의 **DSC 노드** 블레이드에 **로그 검색** 단추가 표시됩니다. **로그 검색** 단추를 클릭하여 DSC 노드 데이터에 대한 로그를 확인합니다.

![로그 검색 단추](media/automation-dsc-diagnostics/log-search-button.png)

**로그 검색** 블레이드가 열리고 각 DSC 노드에 대한 **DscNodeStatusData** 작업과 해당 노드에 적용된 노드 구성에서 호출된 각 [DSC 리소스](https://msdn.microsoft.com/powershell/dsc/resources)에 대한 **DscResourceStatusData** 작업이 표시됩니다.

**DscResourceStatusData** 작업에는 실패한 모든 DSC 리소스에 대한 오류 정보가 포함됩니다.

해당 작업에 대한 데이터를 보려면 목록에서 각 작업을 클릭합니다.

Log Analytics에서 검색하여 로그를 볼 수도 있습니다. [로그 검색을 사용하여 데이터 찾기](../log-analytics/log-analytics-log-searches.md)를 참조하세요.
다음 쿼리를 입력하여 DSC 로그를 파일을 찾습니다. `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

또한 작업 이름으로 쿼리 범위를 좁힐 수도 있습니다. 예: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus" OperationName = "DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>DSC 준수 확인이 실패할 때 전자 메일 보내기

고객이 자주 묻는 질문 중 하나는 DSC 구성에 문제가 발생한 경우 전자 메일 또는 텍스트를 보낼 수 있는지 여부입니다.   

경고 규칙을 만들려면 경고를 호출해야 하는 DSC 보고서 레코드에 대한 로그 검색을 만드는 것으로 시작합니다.  **경고** 단추를 클릭하여 경고 규칙을 만들고 구성합니다.

1. Log Analytics 개요 페이지에서 **로그 검색**을 클릭합니다.
1. 쿼리 필드에 다음 검색을 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다. `Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`.

  둘 이상의 Automation 계정 또는 구독에서 작업 영역으로의 로그를 설정한 경우 구독 또는 Automation 계정별로 경고를 그룹화할 수 있습니다.  
  Automation 계정 이름은 DscNodeStatusData 검색의 리소스 필드에서 파생될 수 있습니다.  
1. **경고 규칙 추가** 화면을 열려면 페이지 위쪽의 **경고**를 클릭합니다. 경고 구성 옵션에 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md#alert-rules)를 참조하세요.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>모든 노드에서 실패한 DSC 리소스 찾기

Log Analytics를 사용할 때의 한 가지 이점은 노드에서 실패한 검사를 검색할 수 있다는 것입니다.
실패한 DSC 리소스의 모든 인스턴스를 찾으려면

1. Log Analytics 개요 페이지에서 **로그 검색**을 클릭합니다.
1. 쿼리 필드에 다음 검색을 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다. `Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`.

### <a name="view-historical-dsc-node-status"></a>DSC 노드 상태 기록 보기

마지막으로 시간별 DSC 노드 상태 기록을 시각화할 수 있습니다.  
이 쿼리를 사용하여 시간이 지남에 따른 DSC 노드 상태를 검색할 수 있습니다.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

시간별 노드 상태 차트가 표시됩니다.

## <a name="log-analytics-records"></a>Log Analytics 레코드

Azure Automation의 진단은 Log Analytics에 두 가지 범주의 레코드를 만듭니다.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 자산 | 설명 |
| --- | --- |
| TimeGenerated |준수 확인이 실행된 날짜 및 시간입니다. |
| OperationName |DscNodeStatusData |
| ResultType |노드가 규정을 준수하는지 여부입니다. |
| NodeName_s |관리되는 노드의 이름입니다. |
| NodeComplianceStatus_s |노드가 규정을 준수하는지 여부입니다. |
| DscReportStatus |준수 검사가 성공적으로 실행되었는지 여부입니다. |
| ConfigurationMode | 구성이 노드에 적용되는 방식입니다. 사용 가능한 값은 __"ApplyOnly"__,__"ApplyandMonitior"__ 및 __"ApplyandAutoCorrect"__ 입니다. <ul><li>__ApplyOnly__: DSC는 구성을 적용하며, 새 구성이 대상 노드에 푸시되지 않거나 서버에서 새 구성을 가져올 때 아무 작업도 수행하지 않습니다. 새 구성이 초기에 적용된 후 DSC는 이전에 구성된 상태에서 달라졌는지 여부를 확인하지 않습니다. DSC는 __ApplyOnly__가 적용되기 전에 성공할 때까지 구성을 적용하려고 합니다. </li><li> __ApplyAndMonitor__: 기본값입니다. LCM는 새 구성을 적용합니다. 새 구성이 초기에 적용된 후 대상 노드가 원하는 상태에서 다른 상태로 바뀌면 DSC는 불일치 상황을 로그에 보고합니다. DSC는 __ApplyAndMonitor__가 적용되기 전에 성공할 때까지 구성을 적용하려고 합니다.</li><li>__ApplyAndAutoCorrect__: DSC는 모든 새 구성을 적용합니다. 새 구성이 초기에 적용된 후 대상 노드가 원하는 상태에서 다른 상태로 바뀌면 DSC는 불일치 상황을 로그에 보고하고 현재 구성을 다시 적용합니다.</li></ul> |
| HostName_s | 관리되는 노드의 이름입니다. |
| IPAddress | 관리되는 노드의 IPv4 주소입니다. |
| Category | DscNodeStatus |
| 리소스 | Azure Automation 계정의 이름입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| NodeId_g |관리되는 노드를 식별하는 GUID입니다. |
| DscReportId_g |보고서를 식별하는 GUID입니다. |
| LastSeenTime_t |보고서를 마지막으로 본 날짜 및 시간입니다. |
| ReportStartTime_t |보고서가 시작된 날짜 및 시간입니다. |
| ReportEndTime_t |보고서가 완료된 날짜 및 시간입니다. |
| NumberOfResources_d |노드에 적용된 구성에서 호출된 DSC 리소스의 수입니다. |
| SourceSystem | Log Analytics가 데이터를 수집한 방법입니다. Azure 진단의 경우 항상 *Azure*입니다. |
| ResourceId |Azure Automation 계정을 지정합니다. |
| ResultDescription | 이 작업에 대한 설명입니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 ID(GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |준수 보고서의 상관 관계 ID인 GUID입니다. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 자산 | 설명 |
| --- | --- |
| TimeGenerated |준수 확인이 실행된 날짜 및 시간입니다. |
| OperationName |DscResourceStatusData|
| ResultType |리소스가 규정을 준수하는지 여부입니다. |
| NodeName_s |관리되는 노드의 이름입니다. |
| Category | DscNodeStatus |
| 리소스 | Azure Automation 계정의 이름입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| NodeId_g |관리되는 노드를 식별하는 GUID입니다. |
| DscReportId_g |보고서를 식별하는 GUID입니다. |
| DscResourceId_s |DSC 리소스 인스턴스의 이름입니다. |
| DscResourceName_s |DSC 리소스의 이름입니다. |
| DscResourceStatus_s |DSC 리소스가 규정을 준수하는지 여부입니다. |
| DscModuleName_s |DSC 리소스를 포함하는 PowerShell 모듈의 이름입니다. |
| DscModuleVersion_s |DSC 리소스를 포함하는 PowerShell 모듈의 버전입니다. |
| DscConfigurationName_s |노드에 적용되는 구성의 이름입니다. |
| ErrorCode_s | 리소스가 실패한 경우의 오류 코드입니다. |
| ErrorMessage_s |리소스가 실패한 경우의 오류 메시지입니다. |
| DscResourceDuration_d |DSC 리소스가 실행된 시간(초)입니다. |
| SourceSystem | Log Analytics가 데이터를 수집한 방법입니다. Azure 진단의 경우 항상 *Azure*입니다. |
| ResourceId |Azure Automation 계정을 지정합니다. |
| ResultDescription | 이 작업에 대한 설명입니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 ID(GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |준수 보고서의 상관 관계 ID인 GUID입니다. |

## <a name="summary"></a>요약

Automation DSC 데이터를 Log Analytics로 전송하면 다음과 같은 작업을 통해 Automation DSC 노드의 상태를 보다 정확히 파악할 수 있습니다.

* 문제가 발생할 때 알리도록 경고 설정
* 사용자 지정 보기와 검색 쿼리를 사용하여 runbook 결과, runbook 작업 상태 및 기타 관련된 핵심 지표 또는 메트릭 시각화.  

Log Analytics는 Automation DSC 데이터의 작동을 보다 정확히 이해하도록 하며 인시던트를 더 빠르게 해결하도록 도와줍니다.  

## <a name="next-steps"></a>다음 단계

* Log Analytics를 사용하여 여러 검색 쿼리를 작성하고 Automation DSC 로그를 검토하는 방법에 대한 자세한 내용은 [Log Analytics의 로그 검색](../log-analytics/log-analytics-log-searches.md)을 참조하세요.
* Azure Automation DSC 사용 방법에 대한 자세한 내용은 [Azure Automation DSC 시작하기](automation-dsc-getting-started.md)를 참조하세요.
* Log Analytics 및 데이터 수집 소스에 대한 자세한 내용은 [Log Analytics에서 Azure Storage 데이터 수집 개요](../log-analytics/log-analytics-azure-storage.md)를 참조하세요.

