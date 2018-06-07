---
title: Azure 서비스에 대한 경고 만들기 - PowerShell | Microsoft 문서
description: 지정한 조건이 충족될 경우 전자 메일, 알림, 웹 사이트 URL 호출(webhook) 또는 자동화를 트리거합니다.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: ca9df36c642334d9323736abb51a931a6610fd3e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Azure 서비스에 대한 Azure Monitor에서 클래식 메트릭 경고 만들기 - PowerShell
> [!div class="op_single_selector"]
> * [포털](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>개요

> [!NOTE]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. Azure Monitor는 이제 [더 새롭고 더 나은 메트릭 경고](monitoring-near-real-time-metric-alerts.md)를 지원합니다. 이러한 경고는 여러 메트릭을 모니터링하고 차원 메트릭에 대한 경고를 허용할 수 있습니다. 더 새로운 메트릭 경고를 위한 Powershell 지원이 곧 제공됩니다.
>
>

이 문서에서는 PowerShell을 사용하여 Azure 클래식 메트릭 경고를 설정하는 방법을 보여줍니다.  

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** - 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.    
* **활동 로그 이벤트** - *모든* 이벤트에 대해 또는 특정 이벤트가 발생했을 때만 경고를 트리거할 수 있습니다 활동 로그 경고에 대해 자세히 알아보려면 [여기를 클릭](monitoring-activity-log-alerts.md)하세요.

트리거되면 다음을 수행하도록 클래식 메트릭 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 이메일 알림을 보냅니다.
* 사용자가 지정한 추가 이메일 주소로 이메일을 보냅니다.
* webhook 호출
* Azure runbook 실행 시작(현재는 Azure 포털에서만 가능)

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [명령줄 인터페이스(CLI)](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

```Get-Help``` 입력 후 도움말이 필요한 PowerShell 명령을 입력하면 항상 추가 정보를 얻을 수 있습니다.

## <a name="create-alert-rules-in-powershell"></a>PowerShell에서 경고 규칙 만들기
1. Azure에 로그인합니다.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. 사용 가능한 구독 목록을 가져옵니다. 올바른 구독에서 작업 중인지 확인합니다. 그렇지 않은 경우 `Get-AzureRmSubscription`의 출력을 사용하여 올바른 항목을 선택합니다.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. 리소스 그룹에 대한 기존 규칙을 나열하려면 다음 명령을 사용합니다.

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. 규칙을 만들려면 먼저 몇 가지 중요한 정보가 필요합니다.

  * 경고를 설정할 리소스의 **리소스 ID**
  * 리소스에 대해 사용 가능한 **메트릭 정의**

     리소스 ID를 가져오는 한 가지 방법은 Azure 포털을 사용하는 것입니다. 리소스를 이미 만들었다고 가정하고 포털에서 선택합니다. 이 후 다음 블레이드에서 *설정* 섹션의 *속성*을 선택합니다. **리소스 ID**는 다음 블레이드의 필드입니다. 또 다른 방법은 [Azure Resource Explorer](https://resources.azure.com/)를 사용하는 것입니다.

     웹앱에 대한 예제 리소스 ID

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     `Get-AzureRmMetricDefinition` 을 사용하여 특정 리소스에 대한 모든 메트릭 정의를 볼 수 있습니다.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     다음은 해당 메트릭에 대한 메트릭 이름 및 단위를 사용하여 테이블을 생성하는 예제입니다.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Get-AzureRmMetricDefinition에 사용 가능한 모든 옵션 목록은 `Get-Help Get-AzureRmMetricDefinition -Detailed`를 실행하여 확인할 수 있습니다.
5. 다음 예제에서는 웹 사이트 리소스에 대한 경고를 설정합니다. 이 경고는 어느 트래픽이나 5분 동안 계속 수신되면 항상 트리거되며, 트래픽이 5분 동안 수신되지 않으면 다시 트리거됩니다.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. 경고가 트리거되었을 때 Webhook를 만들거나 이메일을 보내려면 먼저 이메일 및/또는 Webhook를 만듭니다. 그런 다음 즉시 다음 예제에서처럼 -Actions 태그를 사용하여 이후의 규칙을 만듭니다. Webhook나 이메일을 PowerShell을 통해 이미 생성된 규칙과 연결할 수 없습니다.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. 개별 규칙을 검토하여 경고가 제대로 만들어졌는지 확인합니다.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. 경고를 삭제합니다. 이 명령은 이 문서의 앞에서 만든 규칙을 삭제합니다.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>다음 단계
* [Azure 모니터링 개요](monitoring-overview.md) 를 확인합니다.
* [경고에서 웹후크 구성](insights-webhooks-alerts.md)을 알아봅니다.
* [활동 로그 이벤트에 대한 경고 구성](monitoring-activity-log-alerts.md)을 알아봅니다.
* [Azure Automation Runbook](../automation/automation-starting-a-runbook.md)에 대해 자세히 알아봅니다.
* 서비스의 상세 고빈도 메트릭을 수집하기 위한 [진단 로그 수집](monitoring-overview-of-diagnostic-logs.md) 의 개요를 살펴봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
