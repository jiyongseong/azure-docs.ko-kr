---
title: Azure Analysis Services에 대한 진단 로깅 | Microsoft Docs
description: Azure Analysis Services에 진단 로깅을 설정하는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 396ee2fc132abb5fbfd29bf5fffe9b1388b9a964
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="setup-diagnostic-logging"></a>진단 로깅 설정

Analysis Services 솔루션의 중요한 기능은 서버가 작동하는 방법을 모니터링하는 것입니다. [Azure 리소스 진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)에서 로그를 모니터링하여 [Azure Storage](https://azure.microsoft.com/services/storage/)로 전송하고, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)로 스트리밍하고, [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite)의 서비스인 [Log Analytics](https://azure.microsoft.com/services/log-analytics/)로 내보낼 수 있습니다. 

![Storage, Event Hubs 또는 Log Analytics에 대한 진단 로깅](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>다음이 로깅됩니다.

**엔진**, **서비스** 및 **메트릭** 범주를 선택할 수 있습니다.

### <a name="engine"></a>엔진

**엔진**을 선택하면 모든 [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events)를 기록합니다. 개별 이벤트를 선택할 수 없습니다. 

|XEvent 범주 |이벤트 이름  |
|---------|---------|
|보안 감사    |   로그인 감사      |
|보안 감사    |   로그아웃 감사      |
|보안 감사    |   서버 시작 및 중지 감사      |
|진행률 보고서     |   진행률 보고 시작      |
|진행률 보고서     |   진행률 보고 종료      |
|진행률 보고서     |   진행률 보고 현재      |
|쿼리     |  쿼리 시작       |
|쿼리     |   쿼리 종료      |
|명령     |  명령 시작       |
|명령     |  명령 종료       |
|오류 및 경고     |   오류      |
|검색     |   검색 종료      |
|알림     |    알림     |
|세션     |  세션 초기화       |
|잠금    |  교착 상태       |
|쿼리 처리     |   VertiPaq SE 쿼리 시작      |
|쿼리 처리     |   VertiPaq SE 쿼리 종료      |
|쿼리 처리     |   VertiPaq SE 쿼리 캐시 일치      |
|쿼리 처리     |   직접 쿼리 시작      |
|쿼리 처리     |  직접 쿼리 종료       |

### <a name="service"></a>서비스

|작업 이름  |발생 시기  |
|---------|---------|
|CreateGateway     |   사용자가 서버에서 게이트웨이 구성      |
|ResumeServer     |    서버 다시 시작     |
|SuspendServer    |   서버 일시 중지      |
|DeleteServer     |    서버 삭제     |
|RestartServer    |     사용자가 SSMS 또는 PowerShell을 통해 서버 다시 시작    |
|GetServerLogFiles    |    사용자가 PowerShell을 통해 서버 로그 내보내기     |
|ExportModel     |   사용자가 Visual Studio에서 열기를 사용하여 포털에서 모델 내보내기     |

### <a name="all-metrics"></a>모든 메트릭

메트릭 범주는 메트릭에서 표시된 동일한 [서버 메트릭](analysis-services-monitor.md#server-metrics)을 기록합니다.

## <a name="setup-diagnostics-logging"></a>진단 로깅 설정

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) > 서버의 왼쪽 탐색 영역에서 **진단 로그**를 클릭한 다음 **진단 로그 켜기**를 클릭합니다.

    ![Azure Portal에서 Azure Cosmos DB에 대한 진단 로깅 설정](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. **진단 설정**에서 다음 옵션을 지정합니다. 

    * **이름**. 만들 로그에 대한 이름을 입력합니다.

    * **저장소 계정에 보관**. 이 옵션을 사용하려면 연결할 기존 저장소 계정이 필요합니다. [저장소 계정 만들기](../storage/common/storage-create-storage-account.md)를 참조하세요. 지침에 따라 리소스 관리자와 범용 계정을 만든 다음 포털에서 이 페이지로 돌아가 해당 저장소 계정을 선택합니다. 새로 만든 저장소 계정이 드롭다운 메뉴에 나타나기까지 몇 분 정도 걸릴 수 있습니다.
    * **이벤트 허브로 스트림**. 이 옵션을 사용하려면 연결할 기존 Event Hub 네임스페이스 및 이벤트 허브가 필요합니다. 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조하세요. 그런 다음 포털의 이 페이지로 돌아가 Event Hub 네임스페이스 및 정책 이름을 선택합니다.
    * **Log Analytics에 보내기**. 이 옵션을 사용하려면 기존 작업 영역을 사용하거나 포털에서 [새 작업 영역 만들기](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) 단계를 따라 새 Log Analytics 작업 영역을 만듭니다. Log Analytics에서 로그를 보는 방법에 대한 자세한 내용은 [Log Analytics에서 로그 보기](#view-in-loganalytics)를 참조하세요.

    * **엔진**. xEvents를 기록하려면 이 옵션을 선택합니다. 저장소 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.
    * **서비스**. 서비스 수준 이벤트를 기록하려면 이 옵션을 선택합니다. 저장소 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.
    * **메트릭**. [Metrics](analysis-services-monitor.md#server-metrics)에 자세한 데이터를 저장하려면 이 옵션을 선택합니다. 저장소 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.

3. **저장**을 클릭합니다.

    “\<작업 영역 이름>의 진단을 업데이트하지 못했습니다. \<subscription id> 구독은 microsoft.insights를 사용하도록 등록되지 않았습니다.” 오류를 수신하는 경우 [Azure 진단 문제 해결](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) 지침을 따라 계정을 등록한 다음 이 절차를 다시 시도합니다.

    나중에 진단 로그를 저장하는 방법을 변경하려면 이 페이지로 돌아와서 설정을 수정할 수 있습니다.

### <a name="powershell"></a>PowerShell

계속할 기본 명령은 다음과 같습니다. PowerShell을 사용하여 저장소 계정에 대한 로깅을 설정하는 방법에 대한 단계별 도움말을 보려면 이 문서의 뒷부분에 나오는 자습서를 참조하세요.

PowerShell을 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

- 이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus 규칙 ID는 다음 형식의 문자열입니다.

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 이 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 다음 명령을 사용하여 Log Analytics 작업 영역의 리소스 ID를 가져올 수 있습니다.

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="rest-api"></a>REST API

[Azure Monitor REST API를 사용하여 진단 설정을 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)하는 방법을 알아봅니다. 

### <a name="resource-manager-template"></a>Resource Manager 템플릿

[Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정을 활성화](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)하는 방법을 알아봅니다. 

## <a name="manage-your-logs"></a>로그 관리

로그는 일반적으로 로깅을 설정하는 몇 시간 내에 사용할 수 있습니다. 저장소 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 저장소 계정에 유지하지 않으려는 로그를 삭제합니다.
* 이전 로그가 저장소 계정에서 삭제되도록 보존 기간을 설정해야 합니다.

## <a name="view-logs-in-log-analytics"></a>Log Analytics에서 로그 보기

메트릭 및 서버 이벤트는 병렬 분석을 위해 Log Analytics에서 xEvents와 통합됩니다. 아키텍처에서 진단 로깅 데이터에 대한 전체적인 뷰를 제공하는 다른 Azure 서비스에서 이벤트를 수신하도록 Log Analytics를 구성할 수도 있습니다.

Log Analytics에서 진단 데이터를 보려면 아래에 표시된 대로 왼쪽 메뉴의 로그 검색 페이지 또는 관리 영역을 엽니다.

![Azure Portal에서 로그 검색 옵션](./media/analysis-services-logging/aas-logging-open-log-search.png)

데이터 컬렉션을 사용했으므로 **로그 검색**에서 **수집된 모든 데이터**를 클릭합니다.

**형식**에서 **AzureDiagnostics**를 클릭하고 **적용**을 클릭합니다. AzureDiagnostics에는 엔진 및 서비스 이벤트가 포함됩니다. Log Analytics 쿼리는 즉석에서 생성됩니다. EventClass\_의 필드에는 xEvent 이름이 포함됩니다. 온-프레미스 로깅에 xEvents를 사용한 경우 익숙해 보일 수 있습니다.

**EventClass\_s** 또는 이벤트 이름 중 하나를 클릭하면 Log Analytics가 계속 쿼리를 구성합니다. 나중에 다시 사용하도록 쿼리를 저장해야 합니다.

Log Analytics를 확인하세요. 여기에서는 수집된 데이터에 대한 향상된 쿼리, 대시보드 및 경고 기능이 포함된 웹 사이트를 제공합니다.

### <a name="queries"></a>쿼리

수백 개의 쿼리를 사용할 수 있습니다. 시작하기 위한 몇 가지 지침은 다음과 같습니다.
새 로그 검색 쿼리 언어를 사용하는 방법에 대한 자세한 내용은 [Log Analytics에서 로그 검색 이해](../log-analytics/log-analytics-log-search-new.md)를 참조하세요. 

* 쿼리는 완료되는데 5분(300,000밀리초) 넘게 걸린 Azure Analysis Services에 제출된 쿼리를 반환합니다.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* 규모 확장 복제본을 식별합니다.

    ```
    search * | summarize count() by ServerName_s
    ```
    규모 확장을 사용하는 경우 ServerName\_의 필드 값이 이름에 추가된 복제본 인스턴스 수를 포함하기 때문에 읽기 전용 복제본을 식별할 수 있습니다. 자원 필드에는 Azure 리소스 이름이 포함됩니다. 이것은 사용자에게 표시되는 서버 이름과 일치합니다. 복제본의 경우 IsQueryScaleoutReadonlyInstance_s 필드는 true와 같습니다.



> [!TIP]
> 공유하려는 Log Analytics 쿼리가 있으십니까? GitHub 계정이 있는 경우 이 문서에 추가할 수 있습니다. 이 페이지의 상단 오른쪽에서 **편집**을 클릭하기만 하면 됩니다.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>자습서 - PowerShell을 사용하여 로깅 켜기
이 빠른 자습서에서는 Analysis Service 서버와 동일한 구독 및 리소스 그룹에서 저장소 계정을 만듭니다. 그러면 Set-AzureRmDiagnosticSetting을 사용하여 진단 로깅을 설정하고, 출력을 새 저장소 계정에 전송합니다.

### <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 리소스가 필요합니다.

* 기존 Azure Analysis Services 서버 서버 리소스를 만드는 방법에 대한 지침은 [Azure Portal에서 서버 만들기](analysis-services-create-server.md) 또는 [PowerShell을 사용하여 Azure Analysis Services 서버 만들기](analysis-services-create-powershell.md)를 참조하세요.

### <a name="aconnect-to-your-subscriptions"></a></a>구독에 연결

Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.  

```powershell
Connect-AzureRmAccount
```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell은 이 계정과 연관된 모든 구독을 가져와서 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Azure Key Vault을 만드는 데 사용된 특정된 하나를 지정해야 합니다. 계정에 대한 구독을 보려면 다음을 입력합니다.

```powershell
Get-AzureRmSubscription
```

그런 다음 로깅하려는 Azure Analysis Services 계정과 연결된 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 사용자 계정에 여러 구독이 연결된 경우 구독을 지정하는 것이 중요합니다.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>로그에 대한 새 저장소 계정 만들기

서버와 동일한 구독에서 제공되는 로그에 기존 저장소 계정을 사용할 수 있습니다. 이 자습서의 경우 Analysis Services 로그에 전용인 새 저장소 계정을 만듭니다. 편의를 위해 저장소 계정 세부 정보를 **sa**라는 변수에 저장합니다.

또한 Analysis Services 서버를 포함하는 것과 동일한 리소스 그룹을 사용합니다. 또한 `awsales_resgroup`, `awsaleslogs` 및 `West Central US`의 값을 고유한 값으로 바꿉니다.

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>로그에 대한 서버 계정을 식별합니다

계정 이름을 **account**라는 변수로 설정합니다. 여기서 ResourceName은 계정의 이름입니다.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>로깅 사용

로깅을 사용하려면 새 저장소 계정, 서버 계정 및 범주의 변수와 함께 Set-AzureRmDiagnosticSetting cmdlet을 사용합니다. 다음 명령을 실행하고 **-Enabled** 플래그를 **$true**로 설정합니다.

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

출력은 다음과 비슷합니다.

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

저장소 계정에 정보를 저장하는 서버에 로깅을 사용할 수 있는지 확인합니다.

이전 로그를 자동으로 삭제하도록 로그에 대한 보존 정책을 설정할 수도 있습니다. 예를 들어 **-RetentionEnabled** 플래그를 사용하는 보존 정책을 **$true**로 설정하고 **-RetentionInDays** 매개 변수를 **90**으로 설정합니다. 90일보다 오래된 로그는 자동으로 삭제됩니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>다음 단계

[Azure 리소스 진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)에 대해 자세히 알아보기

PowerShell 도움말은 [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting)을 참조하세요.
