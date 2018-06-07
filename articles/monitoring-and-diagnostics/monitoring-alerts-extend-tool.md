---
title: OMS 포털에서 Azure로 경고를 확장(복사)하는 방법 | Microsoft Docs
description: OMS에서 Azure Alerts로 경고를 확장하기 위한 도구 및 API는 고객이 자발적으로 사용할 수 있습니다.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 241ac027a0606f901f51d6a20b9a48a2cf7a9fcf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166185"
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>OMS에서 Azure로 경고를 확장(복사)하는 방법
**2018년 5월14일**부터 [Microsoft OMS(Operations Management Suite)](../operations-management-suite/operations-management-suite-overview.md)에 구성된 경고를 사용하는 모든 고객은 Azure로 확장됩니다. Azure로 확장되는 경고는 OMS의 경우와 동일하게 동작합니다. 모니터링 기능은 그대로 유지됩니다. OMS에서 생성된 경고를 Azure로 확장하면 많은 이점이 있습니다. OMS에서 Azure로 경고를 확장할 때의 이점 및 확장 프로세스에 대한 자세한 내용은 [OMS에서 Azure로 경고 확장](monitoring-alerts-extend.md)을 참조하세요.

> [!NOTE]
> 2018년 5월 14일부터 Microsoft는 경고를 Azure로 확장하는 프로세스를 자동으로 시작합니다. 모든 작업 영역 및 경고가 확장되지는 않습니다. 대신 앞으로 몇 주 안에 Microsoft에서 전면적으로 경고를 자동으로 확장하기 시작할 것입니다. 따라서 OMS 포털의 경고는 2018년 5월 14일에 바로 Azure로 자동 확장되지 않으며, 사용자가 아래 옵션 세부 정보를 사용하여 경고를 수동으로 확장할 수 있습니다.

OMS에서 Azure로 경고를 즉시 이동하려는 고객은 설명된 옵션 중 하나를 사용하여 이 작업을 수행할 수 있습니다.

## <a name="option-1---using-oms-portal"></a>옵션 1 - OMS 포털 사용
OMS 포털에서 Azure로의 경고 확장을 자발적으로 시작하려면 아래 나열된 단계를 수행합니다.

1. OMS 포털 개요 페이지에서 설정 및 경고 섹션으로 이동합니다. 아래 그림에 강조 표시된 "Azure로 확장" 레이블이 지정된 단추를 클릭합니다.

    ![확장 옵션이 있는 OMS 포털 경고 설정 페이지](./media/monitor-alerts-extend/ExtendInto.png)

2. 이 단추를 클릭하면 3단계 마법사가 표시되며, 첫 번째 단계에서는 프로세스의 세부 정보를 표시합니다. 다음을 클릭하여 계속 진행합니다.

    ![OMS 포털에서 Azure로 경고 확장 - 1단계](./media/monitor-alerts-extend/ExtendStep1.png)

3. 두 번째 단계에서 시스템은 OMS 포털의 경고에 대해 해당 [작업 그룹](monitoring-action-groups.md)을 나열하여 제안된 변경 사항의 요약을 표시합니다. 둘 이상의 경고에서 유사한 작업이 표시되면 시스템은 모든 작업을 단일 작업 그룹으로 연결할 것을 제안합니다.  제안된 작업 그룹은 명명 규칙 *WorkspaceName_AG_#Number*를 따릅니다. 계속하려면 다음을 클릭합니다.
샘플 화면은 아래와 같습니다.

    ![OMS 포털에서 Azure로 경고 확장 - 2단계](./media/monitor-alerts-extend/ExtendStep2.png)


4. 마법사의 마지막 단계에서는 이전 화면에 표시된 것처럼 새 작업 그룹을 만들고 경고에 연결하여 OMS 포털에 모든 경고를 Azure로 확장하는 작업을 예약하도록 요청할 수 있습니다. 계속하려면 마침을 클릭하고 프로세스 시작 프롬프트에서 확인합니다. 필요에 따라, 고객이 처리 완료 시 OMS 포털에서 보고서를 전송할 이메일 주소를 제공할 수도 있습니다.

    ![OMS 포털에서 Azure로 경고 확장 - 3단계](./media/monitor-alerts-extend/ExtendStep3.png)

5. 마법사가 완료되면 경고 설정 페이지로 제어 권한이 반환되며 "Azure로 확장" 옵션이 제거됩니다. 백그라운드에서 OMS 포털은 Log Analytics의 경고가 Azure로 확장되도록 예약합니다. 이 작업에는 다소 시간이 걸리며, 짧은 기간 동안 이 작업이 시작되면 OMS 포털의 경고를 수정할 수 없게 됩니다. 현재 상태는 배너를 통해 표시되고, 4단계 동안 전자 메일 주소가 제공되면, 백그라운드 프로세스가 모든 경고를 Azure로 확장할 경우 알림이 제공됩니다. 

6. 경고는 Azure로 확장된 후에도 OMS 포털에 계속 나열됩니다.

    ![OMS 포털의 경고를 Azure로 확장한 후](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>옵션 2 - API 사용
OMS 포털의 경고를 Azure로 확장하는 프로세스를 프로그래밍 방식으로 제어 또는 자동화하려는 고객을 위해 Microsoft는 Log Analytics에 새로운 AlertsVersion API를 제공합니다.

Log Analytics AlertsVersion API는 RESTful이며 Azure Resource Manager REST API를 통해 액세스할 수 있습니다. 이 문서에서 API가 Azure Resource Manager API를 호출하여 단순화하는 공개 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용하여 PowerShell 명령줄에서 액세스하는 예제를 찾을 수 있습니다. ARMClient 및 PowerShell의 사용은 API에 액세스하는 다양한 옵션 중 하나입니다. API는 JSON 형식으로 결과를 출력하여 다양한 프로그래밍 방식으로 검색 결과를 사용하게 됩니다.

API에 대해 GET을 사용하여 제안된 변경 요약의 결과를 OMS 포털의 경고에 대한 [작업 그룹](monitoring-action-groups.md) 목록(JSON 형식)으로 얻을 수 있습니다. 둘 이상의 경고에서 유사한 작업이 표시되면 시스템은 모든 작업을 단일 작업 그룹으로 연결할 것을 제안합니다.  제안된 작업 그룹은 명명 규칙 *WorkspaceName_AG_#Number*를 따릅니다.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> API에 대해 GET 호출을 수행하면 OMS 포털의 경고가 Azure로 확장되지 않습니다. 제안되는 변경 요약만 응답으로 제공됩니다. 이러한 변경이 수행되어 경고를 Azure로 확장하기 위해서는 API에 대한 POST 호출을 수행해야 합니다.

API에 대한 GET 호출이 성공적으로 수행되고 200 정상 응답이 표시되면 제안된 작업 그룹과 JSON 경고 목록이 제공됩니다. 샘플 응답은 다음과 같습니다.

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
지정된 작업 영역에 알림이 없는 경우 GET 작업에 대해 200 정상 응답과 함께 다음과 같은 JSON이 제공됩니다.

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

지정된 작업 영역의 모든 경고가 이미 Azure로 확장된 경우 GET 호출에 대한 응답은 다음과 같습니다.
```json
{
    "version": 2
}
```

OMS 포털의 경고를 Azure로 확장하는 일정을 시작하려면 API에 대한 POST를 시작합니다. 이 호출/명령을 수행하면, OMS 포털의 경고를 Azure로 확장하고 API에 대한 GET 호출의 응답으로 지시된 것처럼 변경을 수행하는 데 대한 사용자 의도가 확인되고 동의가 이루어진 것으로 간주됩니다. 필요에 따라, OMS 포털의 경고가 Azure로 확장되는 예약된 백그라운드 프로세스가 성공적으로 완료되면 사용자는 OMS 포털이 보고서를 메일로 보내는 이메일 주소 목록을 제공할 수 있습니다.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> OMS 포털 경고를 Azure로 확장했을 때의 결과는 시스템에서 수행된 변경 내용 때문에, GET이 제공하는 요약과는 다를 수 있습니다. 일단 예약되면 새 경고를 만드는 동안 OMS 포털의 경고를 일시적으로 편집/수정할 수 없게 됩니다. 

POST가 성공하면 다음과 함께 200 정상 응답이 반환합니다.
```json
{
    "version": 2
}
```
버전 2에 지시된 대로 경고가 Azure로 확장되었다는 표시. 이 버전은 경고가 Azure로 확장되었는지만 확인하며, [Log Analytics 검색 API](../log-analytics/log-analytics-api-alerts.md)에서 사용되는지 여부에는 관여하지 않습니다. 경고가 성공적으로 Azure로 확장되면 GET 중 제공된 모든 이메일 주소는 수행된 변경 내용의 세부 정보가 있는 보고서를 전송합니다.

또한 마지막으로, 지정된 작업 영역의 모든 경고가 Azure로 확장되도록 이미 예약된 경우 POST에 대한 응답은 403 사용 권한 없음입니다. 확장 프로세스가 중단된 경우 오류 메시지를 보거나 이해하려면 GET 호출을 수행하고 요약과 함께 반환될 경우 오류 메시지를 볼 수 있습니다.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>문제 해결 
OMS에서 Azure로 경고를 확장하는 과정에서 시스템이 필요한 [작업 그룹](monitoring-action-groups.md)을 만들지 못하도록 하는 문제가 가끔 있을 수 있습니다. 이런 경우 경고 섹션의 배너 및 API에 대한 GET 호출을 통해 OMS 포털에 오류 메시지가 표시됩니다.

다음은 각 오류에 대한 수정 단계입니다.
1. **오류: 구독이 'microsoft.insights' 네임스페이스를 사용하도록 등록되어 있지 않습니다.** ![등록 오류 메시지가 있는 OMS 포털 경고 설정 페이지](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a. OMS 작업 영역과 관련된 구독 - Azure Monitor(microsoft.insights) 기능을 사용하도록 등록되지 않았습니다. 이로 인해 OMS는 Azure Monitor 및 Azure 경고로 경고를 확장할 수 없습니다.
    
    나. 해결하려면 PowerShell, Azure CLI 또는 Azure Portal을 사용하여 구독에 microsoft.insights(Azure 모니터 및 알림)를 등록하십시오. 자세히 알아보려면 [리소스 공급자 등록 시 오류 해결](../azure-resource-manager/resource-manager-register-provider-errors.md)에 대한 문서를 참조하세요.
    
    다. 문서에 설명된 단계에 따라 해결되면 OMS는 다음날 예정된 실행 시간 내에 Azure로 경로를 확장합니다. 작업이나 시작이 필요하지 않습니다.
2. **Error: Scope Lock is present at subscription/resource group level for write operations**(오류: 쓰기 작업에 대한 구독/리소스 그룹 수준에 범위 잠금이 있습니다.) ![범위 잠금 오류 메시지가 있는 OMS 포털 경고 설정 페이지](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. 범위 잠금이 활성화되어 Log Analytics(OMS) 작업 영역이 포함된 구독 또는 리소스 그룹의 새로운 변경이 제한되면, 시스템에서 경고가 Azure로 확장되지 못하고 필요한 작업 그룹이 만들어지지 않습니다.
    
    나. 해결하려면 Azure Portal, Powershell, Azure CLI 또는 API를 사용하여 작업 영역이 포함된 구독 또는 리소스 그룹에서 *ReadOnly* 잠금을 삭제하십시오. 자세히 알아보려면 [리소스 잠금 사용](../azure-resource-manager/resource-group-lock-resources.md)에 대한 문서를 참조하세요. 
    
    다. 문서에 설명된 단계에 따라 해결되면 OMS는 다음날 예정된 실행 시간 내에 Azure로 경로를 확장합니다. 작업이나 시작이 필요하지 않습니다.

3. **오류: 정책이 구독/리소스 그룹 수준에 있습니다.**: ![정책 오류 메시지가 있는 OMS 포털 경고 설정 페이지](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Log Analytics(OMS) 작업 영역이 포함된 구독 또는 리소스 그룹의 새 리소스를 제한하는 [Azure 정책](../azure-policy/azure-policy-introduction.md)이 적용되면, 시스템에서 경고를 Azure로 확장(복사)할 수 없고 필요한 작업 그룹을 만들 수 없습니다.
    
    나. 해결하려면 *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* 오류가 발생하는 정책을 편집하여 작업 영역이 포함된 구독 또는 리소스 그룹에 새 리소스를 만들 수 없도록 합니다. Azure Portal, Powershell, Azure CLI 또는 API를 사용하여 실패가 발생하는 해당 정책을 찾도록 작업을 감사할 수 있습니다. 자세한 내용은 [작업을 감사하기 위해 활동 로그 보기](../azure-resource-manager/resource-group-audit.md) 문서를 참조하세요. 
    
    다. 문서에 설명된 단계에 따라 해결되면 OMS는 다음날 예정된 실행 시간 내에 Azure로 경로를 확장합니다. 작업이나 시작이 필요하지 않습니다.


## <a name="next-steps"></a>다음 단계

* 새 [Azure 경고 환경](monitoring-overview-unified-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Alerts의 로그 경고](monitor-alerts-unified-log.md)에 대해 알아봅니다.
