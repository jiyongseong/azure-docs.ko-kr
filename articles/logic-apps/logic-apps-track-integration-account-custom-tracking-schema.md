---
title: B2B 모니터링을 위한 사용자 지정 추적 스키마 - Azure Logic Apps | Microsoft Docs
description: 사용자 지정 추적 스키마를 만들어 Azure 통합 계정의 트랜잭션에서 B2B 메시지를 모니터링합니다.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 94de6afcf3f9f95a3cb45207ef43e1d33b728d99
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>추적을 사용하도록 설정하여 종단 간, 전체 워크플로 모니터링
기업 간 워크플로의 다양한 부분에 대해 사용할 수 있는 기본 제공 추적(예: AS2 또는 X12 메시지 추적)이 있습니다. 논리 앱, BizTalk Server, SQL Server 또는 다른 계층을 포함하는 워크플로를 만들 경우 워크플로의 시작부터 끝까지 이벤트를 기록하는 사용자 지정 추적을 사용하도록 설정할 수 있습니다. 

이 항목에서는 논리 앱 외부의 계층에서 사용할 수 있는 사용자 지정 코드를 제공합니다. 

## <a name="custom-tracking-schema"></a>사용자 지정 추적 스키마
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| 자산 | type | 설명 |
| --- | --- | --- |
| sourceType |   | 실행 원본의 유형입니다. 허용되는 값은**Microsoft.Logic/workflows** 및 **custom**입니다. 필수 사항입니다. |
| 원본 |   | 소스 형식이 **Microsoft.Logic/workflows**이면 소스 정보는 이 스키마를 따라야 합니다. 소스 형식이 **custom**이면 스키마는 JToken입니다. 필수 사항입니다. |
| systemId | 문자열 | 논리 앱 시스템 ID입니다. 필수 사항입니다. |
| runId | 문자열 | 논리 앱 실행 ID입니다. 필수 사항입니다. |
| operationName | 문자열 | 작업의 이름(예: action 또는 trigger)입니다. 필수 사항입니다. |
| repeatItemScopeName | 문자열 | 작업이 `foreach`/`until` 루프에 있으면 아이템 이름을 반복합니다. 필수 사항입니다. |
| repeatItemIndex | 정수  | 작업이 `foreach`/`until` 루프에 있는지의 여부입니다. 반복된 항목 인덱스를 나타냅니다. 필수 사항입니다. |
| trackingId | 문자열 | 메시지에 상호 연결할 추적 ID입니다. (선택 사항) |
| CorrelationId | 문자열 | 메시지에 상호 연결할 상관 관계 ID입니다. (선택 사항) |
| clientRequestId | 문자열 | 클라이언트는 메시지에 상호 연결하기 위해 이 항목을 채울 수 있습니다. (선택 사항) |
| eventLevel |   | 이벤트의 수준입니다. 필수 사항입니다. |
| eventTime |   | UTC 형식 YYYY-MM-DDTHH:MM:SS.00000Z의 이벤트 시간입니다. 필수 사항입니다. |
| recordType |   | 트랙 레코드의 유형입니다. 허용되는 값은 **custom**입니다. 필수 사항입니다. |
| record |   | 사용자 지정 레코드 유형입니다. 허용된 형식은 JToken입니다. 필수 사항입니다. |

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마
스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.
* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>다음 단계
* [B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md)에 대해 자세히 알아봅니다.   
* [Log Analytics에서 B2B 메시지 추적](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)에 대해 알아봅니다.
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 알아봅니다.
