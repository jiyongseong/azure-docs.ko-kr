---
title: 활동 로그 경고에 사용된 웹후크 스키마 이해 | Microsoft Docs
description: 활동 로그 경고가 활성화될 때 웹후크 URL에 게시되는 JSON 스키마에 대해 알아봅니다.
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: f71714774d7ad54d7eb2132e8c20c87f972157ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure 활동 로그 경고에 대한 웹후크
작업 그룹 정의의 일부로 활동 로그 경고 알림을 받도록 웹후크 끝점을 구성할 수 있습니다. 웹후크를 사용하면 사후 처리 또는 사용자 지정 작업을 위해 이러한 알림을 다른 시스템으로 라우팅할 수 있습니다. 이 문서는 Webhook에 대한 HTTP POST의 페이로드 형태를 보여 줍니다.

활동 로그 경고에 대한 자세한 내용은 [Azure 활동 로그 알림을 만드는](monitoring-activity-log-alerts.md) 방법을 참조하세요.

작업 그룹에 대한 자세한 내용은 [작업 그룹을 만드는](monitoring-action-groups.md) 방법을 참조하세요.

## <a name="authenticate-the-webhook"></a>웹후크 인증
웹후크는 인증을 위해 토큰 기반 인증을 선택적으로 사용할 수 있습니다. 웹후크 URI는 토큰 ID를 통해 저장됩니다. 예: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>페이로드 스키마
POST 작업에 포함된 JSON 페이로드는 페이로드 data.context.activityLog.eventSource 필드에 따라 다릅니다.

### <a name="common"></a>일반
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
### <a name="administrative"></a>관리
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
### <a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
    "status": "Activated",
    "context": {
        "activityLog": {
        "channels": "Admin",
        "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "description": "Active: Virtual Machines - Australia East",
        "eventSource": "ServiceHealth",
        "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
        "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
        "level": "Informational",
        "operationName": "Microsoft.ServiceHealth/incident/action",
        "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "properties": {
            "title": "Virtual Machines - Australia East",
            "service": "Virtual Machines",
            "region": "Australia East",
            "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "incidentType": "Incident",
            "trackingId": "0NIH-U2O",
            "impactStartTime": "2017-10-18T02:48:00.0000000Z",
            "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
            "defaultLanguageTitle": "Virtual Machines - Australia East",
            "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "stage": "Active",
            "communicationId": "636439673646212912",
            "version": "0.1.1"
        },
        "status": "Active",
        "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
        "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

서비스 상태 알림 활동 로그 경고에 대한 특정 스키마 세부 정보는 [서비스 상태 알림](monitoring-service-notifications.md)을 참조하세요. 또한 [기존 문제 관리 솔루션으로 서비스 상태 웹후크 알림 구성](../service-health/service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.

다른 모든 활동 로그 경고에 대한 특정 스키마 세부 정보는 [Azure 활동 로그 개요](monitoring-overview-activity-logs.md)를 참조하세요.

| 요소 이름 | 설명 |
| --- | --- |
| status |메트릭 경고에 사용됩니다. 활동 로그 경고에 대해서는 항상 “activated”로 설정합니다. |
| context |이벤트에 대한 컨텍스트입니다. |
| resourceProviderName |영향을 받는 리소스의 리소스 공급자입니다. |
| conditionType |항상 "Event"입니다. |
| 이름 |경고 규칙의 이름입니다. |
| id |경고의 리소스 ID입니다. |
| description |경고를 만들 때 설정하는 경고 설명입니다. |
| subscriptionId |Azure 구독 ID입니다. |
| timestamp |요청을 처리하는 Azure 서비스에서 이벤트를 생성한 시간입니다. |
| ResourceId |영향을 받는 리소스의 리소스 ID입니다. |
| resourceGroupName |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| properties |이벤트에 대한 세부 정보를 포함하는 `<Key, Value>` 쌍의 집합(즉, `Dictionary<String, String>`)입니다. |
| event |이벤트에 대한 메타데이터가 포함된 요소입니다. |
| 권한 부여 |이벤트의 역할 기반 Access Control 속성입니다. 이러한 속성에는 일반적으로 action, role 및 scope이 포함됩니다. |
| 카테고리 |이벤트의 범주. 지원되는 값으로 Administrative, Alert, Security, ServiceHealth, Recommendation이 있습니다. |
| caller |가용성에 기반한 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소입니다. 특정 시스템 호출의 경우 null일 수 있습니다. |
| CorrelationId |일반적으로 문자열 형식의 GUID. correlationId가 있는 이벤트는 동일한 상위 작업에 속하며 일반적으로 correlationId를 공유합니다. |
| eventDescription |이벤트에 대한 정적 텍스트 설명입니다. |
| eventDataId |이벤트에 대한 고유 식별자입니다. |
| eventSource |이벤트를 생성한 Azure 서비스 또는 인프라의 이름입니다. |
| httpRequest |요청은 일반적으로 clientRequestId, clientIpAddress 및 HTTP 메서드(예: PUT)를 포함합니다. |
| level |Critical, Error, Warning 및 Informational 값 중 하나입니다. |
| operationId |일반적으로 이벤트 간에 공유되는 GUID로서 단일 작업에 해당합니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트의 속성입니다. |
| status |문자열입니다. 작업의 상태입니다. 일반적인 값으로 Started, In Progress, Succeeded, Failed, Active 및 Resolved가 포함됩니다. |
| subStatus |일반적으로 해당 REST 호출의 HTTP 상태 코드를 포함합니다. 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 일반적인 하위 상태 값으로 OK (HTTP Status Code: 200), Created (HTTP Status Code: 201), Accepted (HTTP Status Code: 202), No Content (HTTP Status Code: 204), Bad Request (HTTP Status Code: 400), Not Found (HTTP Status Code: 404), Conflict (HTTP Status Code: 409), Internal Server Error (HTTP Status Code: 500), Service Unavailable (HTTP Status Code: 503) 및 Gateway Timeout (HTTP Status Code: 504)이 있습니다. |

## <a name="next-steps"></a>다음 단계
* [활동 로그에 대해 자세히 알아보세요](monitoring-overview-activity-logs.md).
* [Azure 경고에 대한 Azure Automation 스크립트(Runbook)를 실행하세요](http://go.microsoft.com/fwlink/?LinkId=627081).
* [논리 앱을 사용하여 Azure 경고에서 Twilio를 통해 SMS를 보냅니다](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Slack 메시지를 보냅니다](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Azure Queue에 메시지를 보냅니다](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
