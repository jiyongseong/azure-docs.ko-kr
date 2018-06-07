---
title: B2B 모니터링을 위한 AS2 추적 스키마 - Azure Logic Apps | Microsoft Docs
description: AS2 추적 스키마를 사용하여 Azure 통합 계정의 트랜잭션에서 B2B 메시지를 모니터링합니다.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8605950f3cc07730ee13b91999076e3a093e8d92
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>AS2 메시지 및 MDN의 추적을 시작 또는 사용하여 성공, 오류 및 메시지 속성을 모니터링
Azure 통합 계정에서 다음 AS2 추적 스키마를 사용하여 B2B(business-to-business) 트랜잭션을 모니터링할 수 있습니다.

* AS2 메시지 추적 스키마
* AS2 MDN 추적 스키마

## <a name="as2-message-tracking-schema"></a>AS2 메시지 추적 스키마
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| 자산 | 유형 | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | AS2 메시지 보낸 사람의 파트너 이름입니다. (선택 사항) |
| receiverPartnerName | 문자열 | AS2 메시지 받는 사람의 파트너 이름입니다. (선택 사항) |
| as2To | 문자열 | AS2 메시지의 헤더에서 AS2 메시지 받는 사람의 이름입니다. 필수 사항입니다. |
| as2From | 문자열 | AS2 메시지의 헤더에서 AS2 메시지 보낸 사람의 이름입니다. 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 AS2 규약의 이름입니다. 선택 사항입니다. |
| direction | 문자열 | 메시지 흐름, 수신 또는 송신 방향입니다. 필수 사항입니다. |
| messageId | 문자열 | AS2 메시지의 헤더에서 AS2 메시지 ID입니다. 선택 사항입니다. |
| dispositionType |문자열 | MDN(메시지 처리 알림) 처리 유형 값입니다. (선택 사항) |
| fileName | 문자열 | AS2 메시지의 헤더에서 파일 이름입니다. (선택 사항) |
| isMessageFailed |BOOLEAN | AS2 메시지의 실패 여부입니다. 필수 사항입니다. |
| isMessageSigned | BOOLEAN | AS2 메시지의 서명 여부입니다. 필수 사항입니다. |
| isMessageEncrypted | BOOLEAN | AS2 메시지의 암호화 여부입니다. 필수 사항입니다. |
| isMessageCompressed |BOOLEAN | AS2 메시지의 압축 여부입니다. 필수 사항입니다. |
| correlationMessageId | 문자열 | MDN과 메시지를 상호 연관 짓기 위한 AS2 메시지 ID입니다. (선택 사항) |
| incomingHeaders |JToken의 사전 | 들어오는 AS2 메시지 헤더 세부 정보입니다. (선택 사항) |
| outgoingHeaders |JToken의 사전 | 보내는 AS2 메시지 헤더 세부 정보입니다. (선택 사항) |
| isNrrEnabled | BOOLEAN | 값을 알 수 없는 경우 기본값을 사용합니다. 필수 사항입니다. |
| isMdnExpected | Boolean | 값을 알 수 없는 경우 기본값을 사용합니다. 필수 사항입니다. |
| mdnType | 열거형 | 허용되는 값은 **NotConfigured**, **Sync** 또는 **Async**입니다. 필수 사항입니다. |

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 추적 스키마
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| 자산 | 유형 | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | AS2 메시지 보낸 사람의 파트너 이름입니다. (선택 사항) |
| receiverPartnerName | 문자열 | AS2 메시지 받는 사람의 파트너 이름입니다. (선택 사항) |
| as2To | 문자열 | AS2 메시지를 받는 파트너 이름입니다. 필수 사항입니다. |
| as2From | 문자열 | AS2 메시지를 보내는 파트너 이름입니다. 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 AS2 규약의 이름입니다. 선택 사항입니다. |
| direction |문자열 | 메시지 흐름, 수신 또는 송신 방향입니다. 필수 사항입니다. |
| messageId | 문자열 | AS2 메시지 ID입니다. (선택 사항) |
| OriginalMessageId |문자열 | AS2 원래 메시지 ID입니다. (선택 사항) |
| dispositionType | 문자열 | MDN 처리 유형 값입니다. (선택 사항) |
| isMessageFailed |BOOLEAN | AS2 메시지의 실패 여부입니다. 필수 사항입니다. |
| isMessageSigned |BOOLEAN | AS2 메시지의 서명 여부입니다. 필수 사항입니다. |
| isNrrEnabled | BOOLEAN | 값을 알 수 없는 경우 기본값을 사용합니다. 필수 사항입니다. |
| statusCode | 열거형 | 허용되는 값은 **Accepted**, **Rejected** 또는 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| micVerificationStatus | 열거형 | 허용되는 값은 **NotApplicable**, **Succeeded** 또는 **Failed**입니다. 필수 사항입니다. |
| correlationMessageId | 문자열 | 상관 관계 ID입니다. 원래 메시지 ID(MDN이 구성된 메시지의 메시지 ID)입니다. (선택 사항) |
| incomingHeaders | JToken의 사전 | 들어오는 메시지 헤더 세부 정보를 나타냅니다. (선택 사항) |
| outgoingHeaders |JToken의 사전 | 보내는 메시지 헤더 세부 정보를 나타냅니다. (선택 사항) |

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 알아봅니다.    
* [B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md)에 대해 자세히 알아봅니다.   
* [B2B 사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md)에 대해 자세히 알아봅니다.   
* [X12 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md)에 대해 자세히 알아봅니다.   
* [Log Analytics에서 B2B 메시지 추적](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)에 대해 알아봅니다.
