---
title: Resource Manager 템플릿을 사용하여 로그 경고 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿 및 API를 사용하여 로그 경고를 만드는 방법을 알아봅니다.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 14a2560d91fd0f8dcc729b32c7155c4b74aa8aa1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204161"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 로그 경고 만들기
이 문서에서는 [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) 및 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)를 통해 [Azure Resource Manager 템플릿](..//azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 Azure의 규모에서 프로그래밍 방식으로 [로그 경고](monitor-alerts-unified-log.md)를 관리하는 방법을 보여줍니다. 현재 Azure Alerts는 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md)와 [Azure Application Insights](../application-insights/app-insights-analytics-tour.md)에서 쿼리에 대한 로그 경고를 지원합니다.

## <a name="managing-log-alert-on-log-analytics"></a>Log Analytics에서 로그 경고 관리
Log Analytics API를 실행하고 [OMS 포털에서 경고](..//log-analytics/log-analytics-alerts-creating.md)를 관리하는 데 이전에 사용한 스키마와의 호환성을 유지하는 동안 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md)에 대한 로그 경고는 [새 Azure 경고 환경](monitoring-overview-unified-alerts.md)으로 통합됩니다.

> [!NOTE]
> 2018년 5월 14일부터 작업 영역의 모든 경고는 Azure로 자동 확장됩니다. 사용자는 2018년 5월 14일 전에 Azure로 경고 확장을 자발적으로 시작할 수 있습니다. 자세한 내용은 [OMS에서 Azure로 경고 확장](monitoring-alerts-extend.md)을 참조하세요. 

### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
Log Analytics에 대한 로그 경고는 일정한 간격으로 저장된 검색을 실행하는 경고 규칙에 의해 만들어집니다. 쿼리 결과가 지정된 기준과 일치하면 경고 레코드가 생성되고 하나 이상의 작업이 실행됩니다. 

[로그 분석 저장된 검색](../log-analytics/log-analytics-log-searches.md) 및 [로그 분석 경고](../log-analytics/log-analytics-alerts.md)에 대한 리소스 템플릿은 설명서의 Log Analytics 섹션에서 사용할 수 있습니다. 설명 목적의 샘플 및 스키마 세부 내용을 포함하는 [Log Analytics 저장된 검색 및 경고 추가](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)에 대해 자세히 알아봅니다.

### <a name="using-resource-template-via-apipowershell"></a>API/Powershell을 통해 리소스 템플릿 사용
Log Analytics Alert REST API는 RESTful이며 Azure Resource Manager REST API를 통해 액세스할 수 있습니다. 따라서 API는 PowerShell 명령줄에서 액세스할 수 있으며 JSON 형식으로 검색 결과를 출력하여 다양한 프로그래밍 방식으로 결과를 사용하게 됩니다.

Powershell에서 API에 액세스하는 예제를 포함하는 [REST API로 Log Analytics에서 경고 규칙 만들기 및 관리](../log-analytics/log-analytics-api-alerts.md)에 대해 자세히 알아봅니다.

## <a name="managing-log-alert-on-application-insights"></a>Application Insights에서 로그 경고 관리
Azure Application Insights에 대한 로그 경고는 Azure Monitor에서 새 Azure 경고의 일부로 도입되었습니다. 따라서 [예약된 쿼리 규칙](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) REST 작업 그룹으로 Azure Monitor API 아래에서 실행됩니다.

### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
Application Insights 리소스에 대한 로그 경고는 `Microsoft.Insights/scheduledQueryRules/`의 종류입니다. 이 리소스 종류에 대한 자세한 내용은 [Azure Monitor - 예약된 쿼리 규칙 API 참조](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/)를 참조하세요.

다음은 변수로 샘플 데이터 집합이 있는 [예약된 쿼리 규칙 만들기](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) 기반 리소스 템플릿에 대한 구조입니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> 대상 리소스에 대한 숨겨진 링크가 있는 태그 필드는 [예약된 쿼리 규칙](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) API 호출 또는 리소스 템플릿의 사용에서 필수입니다. 

위의 샘플 json은 이 연습의 목적을 위해 sampleScheduledQueryRule.json(예)으로 저장될 수 있으며 [Azure Portal에서 Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)를 사용하여 배포될 수 있습니다.

### <a name="using-resource-template-via-clipowershell"></a>CLI/Powershell을 통해 리소스 템플릿 사용
Azure Monitor - 예약된 쿼리 규칙 API는 REST API이며 Azure Resource Manager REST API와 완벽하게 호환됩니다. 따라서 Resource Manager cmdlet 뿐 아니라 Azure CLI를 사용하여 Powershell을 통해 사용할 수 있습니다.

이전에 표시된 샘플 리소스 템플릿(sampleScheduledQueryRule.json)에 대한 Azure Resource Manager PowerShell cmdlet을 통한 사용량은 아래에 나와 있습니다.
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
이전에 표시된 샘플 리소스 템플릿(sampleScheduledQueryRule.json)에 대한 Azure CLI의 Azure Resource Manager 명령을 통한 사용량은 아래에 나와 있습니다.

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
작업이 성공하면 201은 상태 새 경고 규칙 만들기로 반환되거나 200은 기존 경고 규칙이 수정된 경우 반환됩니다.


## <a name="next-steps"></a>다음 단계
* [로그 경고에 대한 웹후크 작업](monitor-alerts-unified-log-webhook.md) 이해
* 새 [Azure Alerts](monitoring-overview-unified-alerts.md)에 대해 자세히 알아보기
* [Application Insights](../application-insights/app-insights-analytics.md)에 대해 자세히 알아보기
* [Log Analytics](../log-analytics/log-analytics-overview.md)에 대해 자세히 알아보기   
