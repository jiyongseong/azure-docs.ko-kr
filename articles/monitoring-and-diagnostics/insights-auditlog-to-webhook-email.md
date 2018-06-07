---
title: "Azure 활동 로그 경고에 대한 웹후크 호출 | Microsoft Docs"
description: "활동 로그 이벤트를 사용자 지정 작업에 대한 다른 서비스로 라우팅하는 방법을 알아봅니다. 예를 들어 SMS 메시지를 전송하거나, 버그를 기록하거나, 채팅/메시징 서비스를 통해 팀에 알릴 수 있습니다."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 9872c30d123f0a7443e28dc58ee0d4e16572a390
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Azure 활동 로그 경고에 대한 웹후크 호출
웹후크를 사용하면 사후 처리 또는 사용자 지정 작업을 위해 Azure 경고 알림을 다른 시스템으로 라우팅할 수 있습니다. SMS 메시지 보내기, 버그 기록, 채팅/메시징 서비스를 통한 팀 알림 또는 다양한 다른 작업 수행 등을 처리하는 서비스에 라우팅하도록 웹후크를 경고에 사용할 수 있습니다. 또한 경고가 활성화될 때 이메일을 보내도록 활동 로그 경고를 설정할 수도 있습니다.

이 문서에서는 Azure 활동 로그 경고가 발생될 때 호출되도록 웹후크를 설정하는 방법을 설명합니다. Webhook에 대한 HTTP POST의 페이로드 형태도 보여 줍니다. Azure 메트릭 경고의 설정 및 스키마에 대한 자세한 내용은 [Azure 메트릭 경고에 대한 웹후크 구성](insights-webhooks-alerts.md)을 참조하세요. 

> [!NOTE]
> 현재 Azure 활동 로그 경고에서 웹후크 호출을 지원하는 기능은 미리 보기 상태입니다.
>
>

[Azure PowerShell cmdlet](insights-powershell-samples.md#create-metric-alerts), [플랫폼 간 CLI](insights-cli-samples.md#work-with-alerts) 또는 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)를 사용하여 활동 로그 경고를 설정할 수 있습니다. 현재는 Azure Portal을 사용하여 활동 로그 경고를 설정할 수 없습니다.

## <a name="authenticate-the-webhook"></a>웹후크 인증
웹후크는 다음 방법 중 하나를 사용하여 인증할 수 있습니다.

* **토큰 기반 인증**. 웹후크 URI는 토큰 ID를 사용하여 저장됩니다. 예: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **기본 권한 부여**. 사용자 이름과 암호를 사용하여 웹후크 URI를 저장합니다. 예를 들면 다음과 같습니다. 예: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>페이로드 스키마
POST 작업에는 모든 활동 로그 기반 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다. 이 스키마는 메트릭 기반 경고에서 사용하는 것과 비슷합니다.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| 요소 이름 | 설명 |
| --- | --- |
| status |메트릭 경고에 사용됩니다. 활동 로그 경고에 대해서는 항상 Activated로 설정합니다.|
| context |이벤트에 대한 컨텍스트입니다. |
| activityLog | 이벤트의 로그 속성입니다.|
| 권한 부여 |이벤트의 역할 기반 액세스 제어(RBAC) 속성입니다. 이러한 속성에는 일반적으로 **action**, **role** 및 **scope**이 포함됩니다. |
| action | 경고에 의해 캡처되는 작업입니다. |
| scope | 경고(즉, 리소스)의 범위입니다.|
| channels | 작업입니다. |
| claims | 클레임과 관련된 정보 컬렉션입니다. |
| caller |가용성을 기반으로 한 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 이름 또는 GUID입니다. 특정 시스템 호출의 경우 null 값일 수 있습니다. |
| CorrelationId |일반적으로 문자열 형식의 GUID. **correlationId**가 있는 이벤트는 동일한 상위 작업에 속합니다. 일반적으로 **correlationId** 값이 동일합니다. |
| description |경고를 만들 때 설정하는 경고 설명입니다. |
| eventSource |이벤트를 생성한 Azure 서비스 또는 인프라의 이름입니다. |
| eventTimestamp |이벤트가 발생한 시간입니다. |
| eventDataId |이벤트의 고유한 식별자입니다. |
| level |Critical, Error, Warning, Informational 또는 Verbose 값 중 하나입니다. |
| operationName |작업의 이름입니다. |
| operationId |일반적으로 이벤트 간에 공유되는 GUID입니다. 이 GUID는 일반적으로 단일 작업에 해당합니다. |
| ResourceId |영향을 받는 리소스의 리소스 ID입니다. |
| resourceGroupName |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| resourceProviderName |영향을 받는 리소스의 리소스 공급자입니다. |
| status |작업의 상태를 나타내는 문자열 값입니다. 일반적인 값으로 Started, In Progress, Succeeded, Failed, Active 및 Resolved가 포함됩니다. |
| subStatus |일반적으로 해당 REST 호출의 HTTP 상태 코드를 포함합니다. 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 일반적인 하위 상태 값으로 OK (HTTP Status Code: 200), Created (HTTP Status Code: 201), Accepted (HTTP Status Code: 202), No Content (HTTP Status Code: 204), Bad Request (HTTP Status Code: 400), Not Found (HTTP Status Code: 404), Conflict (HTTP Status Code: 409), Internal Server Error (HTTP Status Code: 500), Service Unavailable (HTTP Status Code: 503) 및 Gateway Timeout (HTTP Status Code: 504)이 있습니다. |
| subscriptionId |Azure 구독 ID입니다. |
| submissionTimestamp |요청을 처리하는 Azure 서비스에서 이벤트를 생성한 시간입니다. |
| resourceType | 이벤트를 생성한 리소스의 형식입니다.|
| properties |이벤트에 대한 세부 정보를 포함하는 키/값 쌍의 집합입니다. 예: `Dictionary<String, String>` |

## <a name="next-steps"></a>다음 단계
* [활동 로그](monitoring-overview-activity-logs.md)에 대해 자세히 알아보세요.
* [Azure 경고에 대한 Azure Automation 스크립트(Runbook) 실행](http://go.microsoft.com/fwlink/?LinkId=627081) 방법을 알아봅니다.
* [논리 앱을 사용하여 Azure 경고에서 Twilio를 통해 SMS 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) 방법을 알아봅니다. 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Slack 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app) 방법을 알아봅니다. 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Azure 큐에 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app) 방법을 알아봅니다. 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
