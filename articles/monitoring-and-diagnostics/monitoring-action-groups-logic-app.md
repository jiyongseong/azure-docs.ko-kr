---
title: Azure Monitor 경고 및 동작 그룹을 사용하여 복잡한 동작을 트리거하는 방법 | Microsoft Docs
description: Azure Monitor 경고를 처리하기 위해 논리 앱 작업을 만드는 방법에 대해 알아봅니다.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: dukek
ms.openlocfilehash: 0020f1475d52d01897320062edbd3a66c8acf751
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887453"
---
# <a name="create-a-logic-app-action"></a>논리 앱 작업 만들기
## <a name="overview"></a>개요 ##
Azure Monitor 경고가 트리거되면 [동작 그룹](monitoring-action-groups.md)을 호출합니다. 작업 그룹을 사용하면 사람들에게 경고를 알리도록 하나 이상의 작업을 트리거할 수 있으며, 수정도 가능합니다.

이 문서에서는 경고가 발생한 경우 Microsoft Teams에 대화를 만들도록 논리 앱을 설정하고 트리거하는 방법을 보여 줍니다.

일반적인 절차는 다음과 같습니다.

-   각 경고 유형에 대한 논리 앱 만들기

-   논리 앱에 해당 경고 유형에 대한 스키마 가져오기

-   논리 앱 동작 정의

-   Azure 작업 그룹에 논리 앱의 HTTP 엔드포인트 복사

논리 앱에서 다른 작업을 수행하려는 경우 프로세스는 비슷합니다.

## <a name="create-an-activity-log-alert--administrative"></a>활동 로그 경고 만들기 - 관리

1.  Azure Portal의 왼쪽 위에 있는 **리소스 만들기** 단추를 클릭합니다.

2.  **논리 앱**을 검색하여 선택합니다. **만들기** 단추를 클릭합니다.

3.  논리 앱 이름을 지정하고 리소스 그룹 등을 선택합니다.

    ![논리 앱 만들기 대화 상자](media/monitoring-action-groups/create-logic-app-dialog.png "논리 앱 만들기 대화 상자")

4.  만들기 단추를 클릭하여 논리 앱을 만듭니다. 논리 앱이 생성되면 팝업이 표시됩니다. 리소스 시작 단추를 클릭하여 Logic Apps 디자이너를 엽니다.

5.  **HTTP 요청을 받은 경우** 트리거를 선택합니다.

    ![논리 앱 트리거](media/monitoring-action-groups/logic-app-triggers.png "논리 앱 트리거")

6.  **편집**을 선택하여 HTTP 요청 트리거를 변경합니다.

    ![HTTP 요청 트리거 셰이프](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP 요청 트리거 셰이프")

7.  **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

    ![샘플 페이로드 단추 사용](media/monitoring-action-groups/use-sample-payload-button.png "샘플 페이로드 단추 사용")

8.  다음과 같은 샘플 스키마를 복사하여 대화 상자에 붙여넣습니다.

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. 논리 앱에 전송된 요청은 Content-Type 헤더를 application/json으로 설정해야 함을 사용자에게 알리는 참고가 논리 앱 디자이너에 표시됩니다. 계속 진행하고 대화 상자를 해제합니다. Azure Monitor 경고는 이를 올바르게 수행합니다.

    ![Content-Type 헤더](media/monitoring-action-groups/content-type-header.png "Content-Type 헤더")

10. **+ 새 단계**를 선택한 다음, **작업 추가**를 선택합니다.

    ![작업 추가](media/monitoring-action-groups/add-action.png "작업 추가")

11. Microsoft Teams 커넥터를 검색하고 선택합니다. **Microsoft Teams – 메시지 게시** 작업을 선택합니다.

    ![Microsoft Teams 작업](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams 작업")

12. Microsoft Teams 작업을 구성합니다. Logic Apps 디자이너에서 Office365 계정에 인증할 것을 요청합니다. 메시지를 전송할 **팀 ID** 및 **채널 ID**를 선택합니다.

13. 동적 콘텐츠의 \<fields\>에 정적 텍스트 및 참조의 조합을 사용하는 **메시지**를 구성합니다. 다음 텍스트를 잘라내어 메시지 필드에 붙여넣습니다.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    그런 다음, \<fields\>를 검색하여 동일한 이름의 동적 콘텐츠 태그로 바꿉니다.

    **[참고!]** **status**라는 두 개의 동적 필드가 있습니다. 메시지에 두 상태 필드를 모두 추가합니다. activityLog 속성 모음에서 하나를 사용하고 다른 하나는 삭제합니다. **status** 필드 위로 마우스를 가져가면 스크린샷에 표시된 것과 같이 정규화된 필드 참조가 나타납니다.

    ![팀 작업 - 메시지 게시](media/monitoring-action-groups/teams-action-post-message.png "팀 작업 - 메시지 게시")

14. 디자이너 맨 위에 있는 저장 단추를 클릭하여 논리 앱을 저장합니다.

15. HTTP 요청 셰이프를 클릭하여 확장합니다. HTTP POST URL을 복사합니다.

    ![HTTP POST URL](media/monitoring-action-groups/http-post-url.png "HTTP POST URL")

16. 기존 작업 그룹을 열고 논리 앱을 참조하는 작업을 추가합니다. 기존 작업 그룹이 없으면 <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups>를 참조하여 하나 만듭니다. 변경 내용은 반드시 저장해야 합니다.

    ![작업 그룹 업데이트](media/monitoring-action-groups/update-action-group.png "작업 그룹 업데이트")

다음에 경고가 사용자의 작업 그룹을 호출하면 논리 앱이 호출됩니다.

## <a name="create-a-service-health-alert"></a>Service Health 경고 만들기

서비스 상태 항목은 활동 로그의 일부이므로 프로세스는 다음 변경 사항과 유사합니다.

1.  1-7단계는 동일합니다.
2.  8단계에서 HTTP 트리거에 대한 다음 샘플 스키마를 사용합니다.

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  9-10단계는 동일합니다.
4.  11단계에서 아래 프로세스를 사용합니다.
5.  **+ 새 단계** 단추를 클릭하고 **조건 추가**를 선택합니다. 입력 데이터가 이러한 값과 일치하는 경우에만 논리 앱을 실행하도록 다음 조건을 설정합니다.
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    ![“서비스 상태 페이로드 조건”](media/monitoring-action-groups/service-health-payload-condition.png "서비스 상태 페이로드 조건")

6. **if true** 조건의 경우 앞의 예제에서 11-13단계를 사용하여 Microsoft Teams 작업을 추가합니다.

7. HTML과 [동적 콘텐츠]의 조합을 사용하여 메시지를 정의합니다. 아래 콘텐츠를 복사하여 메시지 필드에 붙여넣습니다. [incidentType], [trackingID], [title] 및 [communication] 필드를 동일한 이름의 동적 콘텐츠 태그로 바꿉니다.

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    ![“서비스 상태 true 조건 게시 작업”](media/monitoring-action-groups/service-health-true-condition-post-action.png "서비스 상태 true 조건 게시 작업")

8. **If false** 조건의 경우 유용한 메시지를 제공합니다.

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    ![“Service Health false 조건 게시 작업”](media/monitoring-action-groups/service-health-false-condition-post-action.png "Service Health false 조건 게시 작업")

9.  이전 예제의 15-16단계에 따라 논리 앱을 저장하고 작업 그룹을 업데이트합니다.

## <a name="metric-alert"></a>메트릭 경고

1.  1-7단계는 첫 번째 예제와 동일합니다.
2.  8단계에서 HTTP 트리거에 대한 다음 샘플 스키마를 사용합니다.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  9-10단계는 동일합니다.
4.  11단계에서 아래 프로세스를 사용합니다.
5.  **+ 새 단계** 단추를 클릭하고 **조건 추가**를 선택합니다. 입력 데이터가 이러한 값과 일치하는 경우에만 논리 앱을 실행하도록 다음 조건을 설정합니다.

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    ![“메트릭 경고 페이로드 조건”](media/monitoring-action-groups/metric-alert-payload-condition.png "메트릭 경고 페이로드 조건")

6.  **if true** 조건에서 **각** 셰이프 및 Microsoft Teams 작업을 추가하고 HTML과 [동적 콘텐츠]의 조합을 사용하여 메시지를 정의합니다.

    ![“메트릭 경고 true 조건 게시 작업”](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "메트릭 경고 true 조건 게시 작업")

7.  **If false** 셰이프에서는 메트릭 경고가 논리 앱의 예상과 일치하지 않는다고 통신하는 Microsoft Teams 작업을 정의하고 JSON 페이로드를 포함합니다. json() 식에서 triggerBody 동적 콘텐츠를 참조하는 방법을 확인하세요.

    ![“메트릭 경고 false 조건 게시 작업”](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "메트릭 경고 false 조건 게시 작업")

8.  첫 번째 예제의 15-16단계에 따라 논리 앱을 저장하고 작업 그룹을 업데이트합니다.

## <a name="next-steps"></a>다음 단계 ##
* [활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.  
* [서비스 상태 알림이 게시될 때마다 경고를 구성](monitoring-activity-log-alerts-on-service-notifications.md)하는 방법을 알아보세요.
* [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기