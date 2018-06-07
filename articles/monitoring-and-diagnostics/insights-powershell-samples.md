---
title: Azure Monitor PowerShell 빠른 시작 샘플. | Microsoft Docs
description: PowerShell을 사용하여 자동 크기 조정, 경고, webhook 및 활동 로그 검색 등의 Azure Monitor 기능에 액세스합니다.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2018
ms.author: robb
ms.openlocfilehash: a67edd4372cff70a3948e85033e806749d585eb5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure Monitor PowerShell 빠른 시작 샘플
이 문서에서는 Azure Monitor 기능에 액세스할 수 있는 샘플 PowerShell 명령을 보여 줍니다.

> [!NOTE]
> Azure Monitor는 2016년 9월 25일까지는 "Azure Insights"로 지칭했던 제품의 새로운 이름입니다. 하지만 네임스페이스와 다음 명령에서는 단어 "insights"를 계속 포함합니다.
> 
> 

## <a name="set-up-powershell"></a>PowerShell 설정
아직 PowerShell이 컴퓨터에서 실행되도록 설정하지 않았으면 지금 설정합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.

## <a name="examples-in-this-article"></a>이 문서의 예
문서의 예에서는 Azure Monitor cmdlet을 사용하는 방법을 보여 줍니다. [Azure Monitor(Insights) Cmdlet](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx)에서 Azure Monitor PowerShell cmdlet의 전체 목록을 살펴볼 수도 있습니다.

## <a name="sign-in-and-use-subscriptions"></a>로그인 후 구독 사용
먼저 Azure 구독에 로그인합니다.

```PowerShell
Connect-AzureRmAccount
```

로그인 화면이 표시됩니다. 로그인하면 계정, 테넌트 ID 및 기본 구독 ID가 표시됩니다. 모든 Azure cmdlet은 기본 구독의 컨텍스트에서 작동합니다. 액세스할 수 있는 구독 목록을 보려면 다음 명령을 사용합니다.

```PowerShell
Get-AzureRmSubscription
```

작업 컨텍스트를 다른 구독으로 변경하려면 다음 명령을 사용합니다.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>구독에 대한 활동 로그 검색
`Get-AzureRmLog` cmdlet을 사용합니다.  다음은 몇 가지 일반적인 예입니다.

이 날짜/시간부터 현재까지의 로그 항목을 가져옵니다.

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

이 날짜/시간 범위의 로그 항목을 가져옵니다.

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 리소스 그룹에서 로그 항목을 가져옵니다.

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

특정 리소스 공급자에서 이 날짜/시간 범위의 로그 항목을 가져옵니다.

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 호출자의 모든 항목을 가져옵니다.

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

다음 명령은 활동 로그에서 마지막 1,000개 이벤트를 검색합니다.

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` 명령은 여러 다른 매개 변수를 지원합니다. 자세한 내용은 `Get-AzureRmLog` 참조를 확인하세요.

> [!NOTE]
> `Get-AzureRmLog` 명령은 15일 간의 기록만 제공합니다. **-MaxEvents** 매개 변수를 사용하면 15일 이후의 N개 이벤트를 쿼리할 수 있습니다. 15일이 지난 이벤트에 액세스하려면 REST API 또는 SDK(SDK를 사용하는 C# 샘플)을 사용합니다. **StartTime**을 포함하지 않으면 **EndTime**에서 1시간을 뺀 값이 기본값입니다. **EndTime**을 포함하지 않으면 현재 시간이 기본값입니다. 모든 시간은 UTC입니다.
> 
> 

## <a name="retrieve-alerts-history"></a>경고 기록 검색
모든 경고 이벤트를 보려면 다음 예제를 사용하여 Azure Resource Manager 로그를 쿼리하면 됩니다.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

특정 경고 규칙에 대한 기록을 보려면 `Get-AzureRmAlertHistory` cmdlet을 사용하여 경고 규칙의 리소스 ID를 전달하면 됩니다.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` cmdlet은 다양한 매개 변수를 지원합니다. 자세한 내용은 [Get AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)를 참조하세요.

## <a name="retrieve-information-on-alert-rules"></a>경고 규칙에 대한 정보 검색
다음 명령은 모두 "montest"라는 리소스 그룹에 적용됩니다.

경고 규칙의 모든 속성을 보려면:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

리소스 그룹에 대한 모든 경고를 검색하려면:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

대상 리소스에 대해 설정된 모든 경고 규칙을 검색합니다. 예를 들어 VM에 설정된 모든 경고 규칙을 검색할 수 있습니다.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` 명령은 다른 매개 변수를 지원합니다. 자세한 내용은 [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) 을 참조하세요.

## <a name="create-metric-alerts"></a>메트릭 경고 만들기
`Add-AlertRule` cmdlet을 사용하여 경고 규칙을 만들고, 업데이트하고, 비활성화할 수 있습니다.

각각 `New-AzureRmAlertRuleEmail` 및 `New-AzureRmAlertRuleWebhook`를 사용하여 전자 메일 및 webhook 속성을 만들 수 있습니다. 경고 규칙 cmdlet에서, 이러한 속성을 경고 규칙의 **동작** 속성에 동작으로 할당합니다.

다음은 메트릭을 사용하여 경고를 만드는 데 사용되는 매개 변수 및 값을 설명하는 테이블입니다.

| 매개 변수 | 값 |
| --- | --- |
| Name |simpletestdiskwrite |
| 이 경고 규칙의 위치 |미국 동부 |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| 생성된 경고의 MetricName |\PhysicalDisk(_Total)\Disk Writes/sec. 정확한 메트릭 이름을 검색하는 방법은 `Get-MetricDefinitions` cmdlet를 참조하세요. |
| operator |GreaterThan |
| 임계값(이 메트릭의 경우 수/초) |1 |
| WindowSize(h:mm:ss 형식) |00:05:00 |
| 집계(이 경우, 평균 횟수를 사용하는 메트릭 통계) |평균 |
| 사용자 지정 전자 메일(문자열 배열) |'foo@example.com','bar@example.com' |
| 소유자, 참가자 및 일기 권한자에게 전자 메일 보내기 |-SendToServiceOwners |

전자 메일 동작 만들기

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

webhook 동작 만들기

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

클래식 VM에 CPU% 메트릭에 대한 경고 규칙 만들기

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

경고 규칙 검색

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

지정된 속성에 대한 경고 규칙이 이미 있으면 경고 추가 cmdlet도 규칙을 업데이트합니다. 경고 규칙을 비활성화하려면 **-DisableRule**매개 변수를 포함하세요.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>경고에 사용 가능한 메트릭 목록 가져오기
`Get-AzureRmMetricDefinition` cmdlet을 사용하여 특정 리소스에 대한 모든 메트릭 목록을 볼 수 있습니다.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

다음은 메트릭 이름 및 단위를 사용하여 테이블을 생성하는 예제입니다.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

`Get-AzureRmMetricDefinition` 에 사용 가능한 옵션 전체 목록은 [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx)에 있습니다.

## <a name="create-and-manage-activity-log-alerts"></a>활동 로그 경고 만들기 및 관리
`Set-AzureRmActivityLogAlert` cmdlet을 사용하여 활동 로그 경로를 설정할 수 있습니다. 활동 로그 경고는 먼저 조건 사전으로 조건을 정의한 다음, 이러한 조건을 사용하는 경로를 만들어야 합니다.

```PowerShell

$condition1 = New-AzureRmActivityLogAlertCondition -Field 'category' -Equals 'Administrative'
$condition2 = New-AzureRmActivityLogAlertCondition -Field 'operationName' -Equals 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzureRmActionGroup -ActionGroupId 'actiongr1' -WebhookProperties $dict
Set-AzureRmActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/' -Action $actionGrp1 -Condition $condition1, $condition2

```

추가적인 웹후크 속성은 선택 사항입니다. `Get-AzureRmActivityLogAlert`를 사용하여 활동 로그 경고의 콘텐츠를 다시 가져올 수 있습니다.

## <a name="create-and-manage-autoscale-settings"></a>자동 크기 조정 설정 및 관리
웹앱, VM, 클라우드 서비스 또는 Virtual Machine Scale Set 같은 리소스는 자동 크기 조정 설정을 하나만 구성할 수 있습니다.
그러나 각 자동 크기 조정 설정이 여러 개의 프로필을 가질 수 있습니다. 예를 들어 하나는 성능 기반 규모 프로필이고, 다른 하나는 일정 기반 프로필일 수 있습니다. 각 프로필에 여러 규칙을 구성할 수 있습니다. 자동 크기 조정에 대한 자세한 내용은 [응용 프로그램의 크기 자동 조정 방법](../cloud-services/cloud-services-how-to-scale-portal.md)을 참조하세요.

사용할 단계는 다음과 같습니다.

1. 규칙을 만듭니다.
2. 이전에 만든 규칙을 프로필에 매핑하는 프로필을 만듭니다.
3. 선택 사항: webhook 및 전자 메일 속성을 구성하여 자동 크기 조정에 대한 알림을 만듭니다.
4. 이전 단계에서 만든 프로필과 알림을 매핑하여 대상 리소스에 대한 자동 크기 조정 설정과 이름을 만듭니다.

다음은 CPU 사용률 메트릭을 사용하여 Windows 운영 체제 기반 Virtual Machine Scale Set에 대한 자동 크기 조정 설정을 만드는 방법을 보여 주는 예입니다.

먼저, 인스턴스 수가 증가하는 규모 확장 규칙을 만듭니다.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

다음으로, 인스턴스 수가 감소하는 규모 감축 규칙을 만듭니다.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

그런 다음, 규칙에 대한 프로필을 만듭니다.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

webhook 속성을 만듭니다.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

앞에서 만든 전자 메일 및 webhook을 포함하여 자동 크기 조정 설정에 대한 알림 속성을 만듭니다.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

마지막으로, 이전에 만든 프로필을 추가하는 자동 크기 조정 설정을 만듭니다. 

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

자동 크기 조정 설정을 관리하는 방법에 대한 자세한 내용은 [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)을 참조하세요.

## <a name="autoscale-history"></a>자동 크기 조정 기록
다음은 최근에 있었던 자동 크기 조정 및 경고 이벤트를 보는 방법을 알려주는 예입니다. 활동 로그 검색을 사용하여 자동 크기 조정 기록을 봅니다.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

`Get-AzureRmAutoScaleHistory` cmdlet을 사용하여 자동 크기 조정 기록을 검색합니다.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

자세한 내용은 [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)를 참조하세요.

### <a name="view-details-for-an-autoscale-setting"></a>자동 크기 조정 설정에 대한 세부 사항 보기
`Get-Autoscalesetting` cmdlet을 사용하여 자동 크기 조정 설정에 대한 자세한 정보를 검색할 수 있습니다.

다음은 리소스 그룹 'myrg1'의 모든 자동 크기 조정 설정에 대한 세부 정보를 보여주는 예입니다.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

다음은 리소스 그룹 'myrg1'의 모든 자동 크기 조정 설정과 특히 'MyScaleVMSSSetting'라는 자동 크기 조정 설정에 대한 세부 정보를 보여주는 예입니다.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>자동 크기 조정 설정 제거
`Remove-Autoscalesetting` cmdlet을 사용하여 자동 크기 조정 설정을 삭제할 수 있습니다.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>활동 로그에 대한 로그 프로필 관리
*로그 프로필*을 만들어 활동 로그에서 저장소 계정으로 데이터를 내보내고 해당 데이터의 보존 기간을 구성할 수 있습니다. 필요에 따라 이벤트 허브로 데이터를 스트리밍할 수도 있습니다. 이 기능은 현재 미리 보기 버전이며 구독당 로그 프로필을 하나만 만들 수 있습니다. 현재 구독과 함께 다음 cmdlet을 사용하여 로그 프로필을 만들고 관리할 수 있습니다. 또한 특정 구독을 선택할 수 있습니다. PowerShell이 현재 구독의 기본값이지만 언제든지 `Set-AzureRmContext`명령을 사용하여 변경할 수 있습니다. 해당 구독 내의 저장소 계정 또는 이벤트 허브로 데이터를 라우팅하도록 활동 로그를 구성할 수 있습니다. 데이터는 JSON 형식의 blob 파일로 기록됩니다.

### <a name="get-a-log-profile"></a>로그 프로필 가져오기
기존 로그 프로필을 가져오려면 `Get-AzureRmLogProfile` cmdlet을 사용합니다.

### <a name="add-a-log-profile-without-data-retention"></a>데이터를 보존하지 않는 로그 프로필 추가
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>로그 프로필 제거
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>데이터를 보존하는 로그 프로필 추가
**-RetentionInDays** 속성을 양의 정수로 지정하여 데이터가 보존되는 일 수를 지정할 수 있습니다.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>보존 및 이벤트 허브를 사용하여 로그 프로필 추가
데이터를 저장소 계정으로 라우팅하는 방법 외에도 데이터를 이벤트 허브에 스트리밍할 수 있습니다. 이 미리 보기 릴리스에서 저장소 계정 구성은 필수지만 이벤트 허브 구성은 선택 사항입니다.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>진단 로그 구성
여러 Azure 서비스가 다음 중 하나 이상을 수행할 수 있는 추가 로그 및 원격 분석을 제공합니다. 
 - Azure Storage 계정에 데이터를 저장하도록 구성
 - Event Hubs로 전송
 - Log Analytics 작업 영역으로 전송 

이 작업은 리소스 수준에서만 수행할 수 있습니다. 저장소 계정 또는 이벤트 허브가 진단 설정이 구성되는 대상 리소스와 같은 지역에 있어야 합니다.

### <a name="get-diagnostic-setting"></a>진단 설정 가져오기
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

진단 설정 비활성화

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

데이터를 보존하지 않도록 진단 설정 활성화

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

데이터를 보존하도록 진단 설정 활성화

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

특정 로그 범주에 대한 데이터를 보존하도록 진단 설정 활성화

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Event Hubs에 대한 진단 설정 활성화

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Log Analytics에 대한 진단 설정 활성화

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

WorkspaceId 속성은 작업 영역의 *리소스 ID*를 사용합니다. 다음 명령을 사용하여 Log Analytics 작업 공간의 리소스 ID를 가져올 수 있습니다.

```PowerShell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId

```

이 명령은 데이터를 여러 대상으로 보내도록 결합될 수 있습니다.
