---
title: 관리 이벤트에 대한 Azure 경고를 활동 로그 경고로 마이그레이션 | Microsoft Docs
description: 관리 이벤트에 대한 경고는 10월 1일에 제거됩니다. 기존 경고를 마이그레이션하여 대비합니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 1cc02664a345da49c276a6876d5cdfaf5bdb8477
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>관리 이벤트에 대한 Azure 경고를 활동 로그 경고로 마이그레이션


> [!WARNING]
> 관리 이벤트에 대한 경고는 10월 1일 이후로 해제됩니다. 아래의 지침을 사용하여 이러한 경고가 있는지 파악하고 마이그레이션하세요(있는 경우).
>
> 

## <a name="what-is-changing"></a>변경 사항

Azure Monitor(이전의 Azure Insights)에서는 관리 이벤트를 트리거하고 웹후크 URL 또는 이메일 주소에 대한 알림을 생성하는 경고를 만드는 기능을 제공했습니다. 다음 방법 중 하나로 이러한 경고를 만들었을 수 있습니다.
* 특정 리소스 종류에 대한 Azure Portal에서 [모니터링] -> [경고] -> [경고 추가] 선택(여기서 "에 대한 경고"가 "이벤트"로 설정됨)
* Add-AzureRmLogAlertRule PowerShell cmdlet 실행
* odata.type = "ManagementEventRuleCondition" 및 dataSource.odata.type = "RuleManagementEventDataSource"가 포함된 [경고 REST API](http://docs.microsoft.com/rest/api/monitor/alertrules) 직접 사용
 
다음 PowerShell 스크립트에서는 구독에 포함된 관리 이벤트에 대한 모든 경고 목록과 각 경고에 설정된 조건 집합을 반환합니다.

```powershell
Connect-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

관리 이벤트에 대한 경고가 없는 경우 위의 PowerShell cmdlet에서는 다음과 같은 일련의 경고 메시지를 출력합니다.

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

이러한 경고 메시지는 무시할 수 있습니다. 관리 이벤트에 대한 경고가 있는 경우 이 PowerShell cmdlet의 출력은 다음과 같습니다.

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

각 경고는 점선으로 구분되며, 세부 정보에는 경고의 리소스 ID와 모니터링되는 특정 규칙이 포함됩니다.

이 기능은 [Azure Monitor 활동 로그 경고](monitoring-activity-log-alerts.md)로 전환되었습니다. 이러한 새 경고를 사용하면 활동 로그 이벤트에 대한 조건을 설정하고 새 이벤트가 조건과 일치할 때 알림을 받을 수 있습니다. 또한 관리 이벤트에 대한 경고에서 향상된 몇 가지 기능도 제공합니다.
* [작업 그룹](monitoring-action-groups.md)을 사용하여 많은 경고에서 알림 수신자 그룹("작업")을 다시 사용할 수 있으므로 경고 수신자 변경에 대한 복잡성이 줄어듭니다.
* 작업 그룹이 있는 SMS를 사용하여 휴대폰에서 알림을 직접 받을 수 있습니다.
* [Resource Manager 템플릿을 사용하여 활동 로그 경고를 만들](monitoring-create-activity-log-alerts-with-resource-manager-template.md) 수 있습니다.
* 특정 요구를 충족하도록 더 효율적인 유연성과 복잡성으로 조건을 만들 수 있습니다.
* 알림이 더 빨리 전달됩니다.
 
## <a name="how-to-migrate"></a>마이그레이션하는 방법
 
새로운 활동 로그 경고를 만들려면 다음 중 하나를 수행할 수 있습니다.
* [Azure Portal에서 경고를 만드는 방법에 대한 지침](monitoring-activity-log-alerts.md)을 따릅니다.
* [Resource Manager 템플릿을 사용하여 경고를 만드는 방법](monitoring-create-activity-log-alerts-with-resource-manager-template.md)을 알아봅니다.
 
이전에 만든 관리 이벤트에 대한 경고는 자동으로 활동 로그 경고로 마이그레이션되지 않습니다. 위의 PowerShell 스크립트를 사용하여 현재 구성한 관리 이벤트에 대한 경고를 나열하고 이를 활동 로그 경고로 수동으로 다시 만들어야 합니다. 이 작업은 10월 1일 전에 완료해야 합니다. 그 후에는 더 이상 관리 이벤트에 대한 경고를 Azure 구독에서 볼 수 없습니다. Azure Monitor 메트릭 경고, Application Insights 경고 및 Log Analytics 경고를 포함한 다른 유형의 Azure 경고는 이 변경으로 인해 영향을 받지 않습니다. 질문이 있으시면 아래에서 의견을 게시해 주세요.


## <a name="next-steps"></a>다음 단계

* [활동 로그](monitoring-overview-activity-logs.md)에 대해 자세히 알아보기
* [Azure Portal을 통해 활동 로그 경고](monitoring-activity-log-alerts.md) 구성
* [Resource Manager를 통해 활동 로그 경고](monitoring-create-activity-log-alerts-with-resource-manager-template.md) 구성
* [활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md) 검토
* [서비스 알림](monitoring-service-notifications.md)에 대해 자세히 알아보기
* [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기
