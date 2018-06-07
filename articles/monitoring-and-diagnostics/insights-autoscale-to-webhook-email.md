---
title: "크기 자동 조정 작업을 사용하여 전자 메일 및 webhook 경고 알림 보내기 | Microsoft Docs"
description: "Azure Monitor에서 크기 자동 조정 작업을 사용하여 웹 URL을 호출하거나 전자 메일을 보내는 방법에 대해 알아봅니다. "
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: eb9a4c98-0894-488c-8ee8-5df0065d094f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: ancav
ms.openlocfilehash: 16caf14028494800e9259f0296c292b606d0210a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>크기 자동 조정 작업을 사용하여 Azure Monitor에서 전자 메일 및 webhook 경고 알림 보내기
이 문서에서는 Azure에서 크기 자동 조정 작업을 기준으로 특정 웹 URL을 호출하거나 전자 메일을 보낼 수 있도록 트리거를 설정하는 방법을 설명합니다.  

## <a name="webhooks"></a>Webhook
Webhook를 사용하면 사후 처리 또는 사용자 지정 알림을 위해 Azure 경고 알림을 다른 시스템으로 라우팅할 수 있습니다. 예를 들어 수신 웹 요청을 처리할 수 있는 서비스로 경고를 라우팅하여 SMS를 보내거나 버그를 기록하거나 채팅 또는 메시징 서비스를 사용해 팀에게 알리는 등의 작업을 수행할 수 있습니다. Webhook URI는 유효한 HTTP 또는 HTTPS 끝점이어야 합니다.

## <a name="email"></a>Email
모든 유효한 전자 메일 주소로 전자 메일을 보낼 수 있습니다. 규칙이 실행되고 있는 구독의 관리자와 공동 관리자도 알림을 받습니다.

## <a name="cloud-services-and-web-apps"></a>클라우드 서비스 및 웹 앱
Azure 포털에서 클라우드 서비스 및 서버 팜(웹 앱)에 대해 옵트인(opt in)할 수 있습니다.

* **배율 기준** 메트릭을 선택합니다.

![배율 기준](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>가상 컴퓨터 확장 집합
Resource Manager(가상 컴퓨터 확장 집합)로 만든 새 가상 컴퓨터의 경우 REST API, Resource Manager 템플릿, PowerShell 및 CLI를 사용하여 구성할 수 있습니다. 포털 인터페이스는 아직 제공되지 않습니다.
REST API 또는 Resource Manager 템플릿을 사용하는 경우 다음 옵션으로 알림 요소를 포함합니다.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
| 필드 | 필수? | 설명 |
| --- | --- | --- |
| operation |yes |값은 "Scale"이어야 합니다. |
| sendToSubscriptionAdministrator |yes |값은 "true" 또는 "false"여야 합니다. |
| sendToSubscriptionCoAdministrators |yes |값은 "true" 또는 "false"여야 합니다. |
| customEmails |yes |값에 null [] 또는 전자 메일 문자열 배열을 사용할 수 있습니다. |
| Webhook |yes |값은 null이거나 올바른 URI일 수 있습니다. |
| serviceUri |yes |유효한 https URI |
| properties |yes |값은 비어 있거나{} 키-값 쌍을 포함할 수 있습니다. |

## <a name="authentication-in-webhooks"></a>Webhook의 인증
웹후크는 토큰 ID를 쿼리 매개 변수로 사용해서 웹후크 URI를 저장하는 토큰 기반 인증을 사용하여 인증할 수 있습니다. 예를 들어 https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue입니다.

## <a name="autoscale-notification-webhook-payload-schema"></a>크기 자동 조정 알림 Webhook 페이로드 스키마
크기 자동 조정 알림이 생성될 때는 다음 메타데이터가 Webhook 페이로드에 포함됩니다.

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| 필드 | 필수? | 설명 |
| --- | --- | --- |
| status |yes |크기 자동 조정 작업이 생성되었음을 나타내는 상태입니다. |
| operation |yes |인스턴스가 증가하면 "규모 확장"되고 인스턴스가 감소하면 "규모 감축"됩니다. |
| context |yes |크기 자동 조정 작업 컨텍스트입니다. |
| timestamp |yes |크기 자동 조정 작업이 트리거된 타임스탬프입니다. |
| id |yes |자동 크기 조정 설정의 Resource Manager ID |
| name |yes |크기 자동 조정 설정의 이름입니다. |
| 세부 정보 |yes |크기 자동 조정 서비스가 수행한 작업에 대한 설명 및 인스턴스 수의 변경 내용입니다. |
| subscriptionId |yes |크기 조정 중인 대상 리소스의 구독 ID입니다. |
| resourceGroupName |yes |크기 조정 중인 대상 리소스의 리소스 그룹 이름입니다. |
| resourceName |yes |크기 조정 중인 대상 리소스의 이름입니다. |
| resourceType |예 |다음의 세 값이 지원됩니다. "microsoft.classiccompute/domainnames/slots/roles" - Cloud Service 역할/"microsoft.compute/virtualmachinescalesets" - 가상 컴퓨터 확장 집합/"Microsoft.Web/serverfarms" - Web App |
| resourceId |yes |크기 조정 중인 대상 리소스의 Resource Manager ID |
| portalLink |yes |대상 리소스의 요약 페이지에 대한 Azure 포털 링크입니다. |
| oldCapacity |yes |크기 자동 조정에서 크기 조정 작업을 수행한 현재(이전) 인스턴스 수입니다. |
| newCapacity |yes |크기 자동 조정에서 리소스 크기를 조정한 새 인스턴스 수입니다. |
| properties |아니요 |선택 사항입니다. <키, 값> 쌍 집합(예: Dictionary <문자열, 문자열>). 속성 필드는 선택 사항입니다. 사용자 지정 사용자 인터페이스 또는 논리 앱 기반 워크플로에서는 페이로드를 사용하여 전달할 수 있는 키와 값을 입력할 수 있습니다. Webhook URI 자체를 쿼리 매개 변수로 사용하여 발신 Webhook 호출로 사용자 지정 속성을 다시 전달할 수도 있습니다. |
