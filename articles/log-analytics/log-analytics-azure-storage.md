---
title: Log Analytics에서 Azure 서비스 로그 및 메트릭 수집 | Microsoft Docs
description: 로그 및 메트릭을 Log Analytics에 쓰도록 Azure 리소스에 대한 진단을 구성합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a748cb0e2a08ed5e8ada5db171d5ef12b2fe121e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Log Analytics에서 사용할 Azure 서비스 로그 및 메트릭 수집

Azure 서비스에 대한 로그 및 메트릭을 수집하는 방법에는 다음 네 가지가 있습니다.

1. Azure 진단에서 Log Analytics로 직접(다음 표에서 *진단*)
2. Azure 진단 -> Azure Storage -> Log Analytics(다음 표에서 *저장소*)
3. Azure 서비스에 대한 커넥터(다음 표에서 *커넥터*)
4. 데이터를 수집한 후 Log Analytics에 게시하기 위한 스크립트(다음 표에서 비어 있으며, 나열되지 않은 서비스에 해당)


| 서비스                 | 리소스 종류                           | 로그        | 메트릭     | 해결 방법 |
| --- | --- | --- | --- | --- |
| 응용 프로그램 게이트웨이    | Microsoft.Network/applicationGateways   | 진단 | 진단 | [Azure Application Gateway 분석](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application insights    |                                         | 커넥터   | 커넥터   | [Application Insights 커넥터](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)(미리 보기) |
| Automation 계정     | Microsoft.Automation/AutomationAccounts | 진단 |             | [자세한 정보](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch 계정          | Microsoft.Batch/batchAccounts           | 진단 | 진단 | |
| 클래식 Cloud Services  |                                         | Storage     |             | [자세한 정보](log-analytics-azure-storage-iis-table.md) |
| Cognitive Services      | Microsoft.CognitiveServices/accounts    |             | 진단 | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | 진단 |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | 진단 |             | |
| 이벤트 허브 네임스페이스     | Microsoft.EventHub/namespaces           | 진단 | 진단 | |
| IoT Hub                | Microsoft.Devices/IotHubs               |             | 진단 | |
| Key Vault               | Microsoft.KeyVault/vaults               | 진단 |             | [KeyVault 분석](log-analytics-azure-key-vault.md) |
| 부하 분산 장치          | Microsoft.Network/loadBalancers         | 진단 |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | 진단 | 진단 | |
| 네트워크 보안 그룹 | Microsoft.Network/networksecuritygroups | 진단 |             | [Azure 네트워크 보안 그룹 분석](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| 복구 자격 증명         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services 분석(미리 보기)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Search 서비스         | Microsoft.Search/searchServices         | 진단 | 진단 | |
| Service Bus 네임스페이스   | Microsoft.ServiceBus/namespaces         | 진단 | 진단 | [Service Bus 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric 분석(미리 보기)](log-analytics-service-fabric.md) |
| SQL(v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | 진단 | [Azure SQL Analytics(미리 보기)](log-analytics-azure-sql.md) |
| Storage                 |                                         |             | 스크립트      | [Azure Storage 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtual Machines        | Microsoft.Compute/virtualMachines       | 내선 번호   | 내선 번호 <br> 진단  | |
| Virtual Machines 확장 집합 | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | 진단 | |
| 웹 서버 팜        | Microsoft.Web/serverfarms               |             | 진단 | |
| 웹 사이트               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | 진단 | [Azure Web Apps 분석(미리 보기)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) |


> [!NOTE]
> Azure 가상 머신(Linux 및 Windows 모두)를 모니터링하려면 [Log Analytics VM 확장](log-analytics-azure-vm-extension.md)을 설치하는 것이 좋습니다. 이 에이전트는 가상 머신 내에서 수집된 통찰력을 제공합니다. 또한 가상 머신 확장 집합에 대한 확장을 사용할 수도 있습니다.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure 진단에서 Log Analytics로 직접
많은 Azure 리소스는 Log Analytics에 직접 진단 로그 및 메트릭을 쓸 수 있으며, 이러한 방식은 분석을 위해 데이터를 수집할 때 선호되는 방식입니다. Azure 진단을 사용하면 데이터가 Log Analytics에 직접 써지며, 먼저 데이터를 저장소에 쓸 필요가 없습니다.

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)를 지원하는 Azure 리소스는 해당 로그 및 메트릭을 Log Analytics으로 직접 보낼 수 있습니다.

> [!NOTE]
> 진단 설정을 통한 Log Analytics에 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

* 사용 가능한 메트릭에 대한 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](../monitoring-and-diagnostics/monitoring-supported-metrics.md)을 참조하세요.
* 사용 가능한 로그에 대한 자세한 내용은 [진단 로그에 지원되는 서비스 및 스키마](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md)를 참조하세요.

### <a name="enable-diagnostics-with-powershell"></a>PowerShell에서 진단 사용
[Azure PowerShell](/powershell/azure/overview)의 2016년 11월(v2.3.0) 이후 릴리스가 필요합니다.

다음 PowerShell 예제에서는 [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting)을 사용하여 네트워크 보안 그룹에 대해 진단을 사용하도록 설정하는 방법을 보여 줍니다. 지원되는 모든 리소스에 대해 같은 방법을 사용할 수 있습니다. `$resourceId`를 진단을 사용하도록 설정할 리소스의 리소스 ID로 설정하기만 하면 됩니다.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 진단 사용

리소스가 생성될 때 진단을 사용하도록 설정하고 진단이 Log Analytics 작업 영역에 진단이 전송되도록 하려면 아래와 비슷한 템플릿을 사용할 수 있습니다. 이 예제는 Automation 계정에 대한 것이지만 지원되는 모든 리소스 형식에 작동합니다.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure 진단 -> Azure Storage -> Log Analytics

일부 리소스에서 로그를 수집하기 위해서는 Azure Storage에 로그를 보낸 다음 저장소에서 로그를 읽도록 Log Analytics를 구성할 수 있습니다.

Log Analytics는 이 접근 방법을 사용하여 다음 리소스 및 로그에 대한 진단을 Azure Storage에서 수집할 수 있습니다.

| 리소스 | 로그 |
| --- | --- |
| Service Fabric |ETWEvent <br> 작업 데이터 <br> 신뢰할 수 있는 행위자 이벤트 <br> 신뢰할 수 있는 서비스 이벤트 |
| Virtual Machines |Linux Syslog <br> Windows 이벤트 <br> IIS 로그 <br> Windows ETWEvent |
| 웹 역할  <br> 작업자 역할 |Linux Syslog <br> Windows 이벤트 <br> IIS 로그 <br> Windows ETWEvent |

> [!NOTE]
> 진단을 계정으로 보내는 경우 저장소 및 트랜잭션에 대해, 그리고 Log Analytics가 저장소 계정에서 데이터를 읽는 경우에 일반 Azure 데이터 요금이 청구될 수 있습니다.
>
>

Log Analytics에서 이러한 로그를 수집하는 방법에 대한 자세한 내용은 [IIS에 대해 Blob Storage 사용 및 이벤트에 대해 Table Storage 사용](log-analytics-azure-storage-iis-table.md)을 참조하세요.

## <a name="connectors-for-azure-services"></a>Azure 서비스용 커넥터

Application Insights에서 수집된 데이터를 Log Analytics에 전송할 수 있도록 하는 Application Insights용 커넥터가 있습니다.

[Application Insights 커넥터](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)에 대해 자세히 알아보세요.

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>데이터를 수집한 후 Log Analytics에 게시하기 위한 스크립트

Log Analytics에 로그 및 메트릭을 전송하는 직접적인 방법을 제공하지 않는 Azure 서비스의 경우, Azure Automation 스크립트를 사용하여 로그 및 메트릭을 수집할 수 있습니다. 그러면 이 스크립트는 [데이터 수집기 API](log-analytics-data-collector-api.md)를 사용하여 Log Analytics로 데이터를 전송할 수 있습니다.

Azure 템플릿 갤러리에는 [Azure Automation을 사용하여](https://azure.microsoft.com/resources/templates/?term=OMS) 서비스에서 데이터를 수집한 후 Log Analytics로 전송하는 예제가 나와 있습니다.

## <a name="next-steps"></a>다음 단계

* [이벤트에 대해 IIS 및 테이블 저장소에 Blob Storage를 사용하여](log-analytics-azure-storage-iis-table.md) Table Storage에 진단을 기록하는 Azure 서비스나 Blob Storage에 기록된 IIS 로그에 대해 로그를 읽을 수 있습니다.
* [솔루션을 사용하도록 설정](log-analytics-add-solutions.md) 하여 데이터에 대한 정보를 제공합니다.
* [검색 쿼리를 사용](log-analytics-log-searches.md) 하여 데이터를 분석합니다.
