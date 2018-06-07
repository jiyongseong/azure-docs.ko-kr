---
title: B2B 모니터링을 위한 X12 추적 스키마 - Azure Logic Apps | Microsoft Docs
description: X12 추적 스키마를 사용하여 Azure 통합 계정의 트랜잭션에서 B2B 메시지를 모니터링합니다.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5a43b9bdf522b6b26f27c082f5cb623f7a76a8b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>X12 메시지의 추적을 시작 또는 사용하여 성공, 오류 및 메시지 속성을 모니터링합니다.
기업 간(B2B) 트랜잭션을 모니터링하기 위해 Azure 통합 계정에서 이러한 X12 추적 스키마를 사용할 수 있습니다.

* X12 트랜잭션 집합 추적 스키마
* X12 트랜잭션 집합 승인 추적 스키마
* X12 교환 추적 스키마
* X12 교환 승인 추적 스키마
* X12 기능 그룹 추적 스키마
* X12 기능 그룹 승인 추적 스키마

## <a name="x12-transaction-set-tracking-schema"></a>X12 트랜잭션 집합 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| 자산 | type | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | 문자열 | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | 문자열 | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | 문자열 | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | 문자열 | 수신 파트너 한정자 (필수) |
| receiverQualifier | 문자열 | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | 문자열 | 교환 컨트롤 번호 (선택 사항) |
| functionalGroupControlNumber | 문자열 | 기능 컨트롤 번호 (선택 사항) |
| transactionSetControlNumber | 문자열 | 트랜잭션 집합 컨트롤 번호 (선택 사항) |
| CorrelationMessageId | 문자열 | 상관 관계 메시지 ID {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}의 조합입니다. (선택 사항) |
| messageType | 문자열 | 트랜잭션 집합 또는 문서 형식 (선택 사항) |
| isMessageFailed | BOOLEAN | X12 메시지 실패 여부 필수 사항입니다. |
| isTechnicalAcknowledgmentExpected | BOOLEAN | X12 규약에서 기술 승인이 구성되었는지 여부 필수 사항입니다. |
| isFunctionalAcknowledgmentExpected | BOOLEAN | X12 규약에서 기능 승인이 구성되었는지 여부 필수 사항입니다. |
| needAk2LoopForValidMessages | BOOLEAN | 유효한 메시지에 AK2 루프가 필요한지 여부 필수 사항입니다. |
| segmentsCount | Integer | X12 트랜잭션 집합의 세그먼트의 수 (선택 사항) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 트랜잭션 집합 승인 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| 자산 | type | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | 문자열 | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | 문자열 | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | 문자열 | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | 문자열 | 수신 파트너 한정자 (필수) |
| receiverQualifier | 문자열 | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | 문자열 | 기능 승인의 교환 컨트롤 번호. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| functionalGroupControlNumber | 문자열 | 기능 승인의 기능 그룹 컨트롤 번호. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| isaSegment | 문자열 | 메시지의 ISA 세그먼트. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| gsSegment | 문자열 | 메시지의 GS 세그먼트. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| respondingfunctionalGroupControlNumber | 문자열 | 응답 교환 컨트롤 번호 (선택 사항) |
| respondingFunctionalGroupId | 문자열 | 승인에서 AK101에 매핑되는 응답 기능 그룹 ID (선택 사항) |
| respondingtransactionSetControlNumber | 문자열 | 응답 트랜잭션 집합 컨트롤 번호 (선택 사항) |
| respondingTransactionSetId | 문자열 | 승인에서 AK201에 매핑되는 응답 트랜잭션 집합 ID (선택 사항) |
| statusCode | BOOLEAN | 트랜잭션 집합 승인 상태 코드 필수 사항입니다. |
| segmentsCount | 열거형 | 승인 상태 코드. 허용되는 값은 **Accepted**, **Rejected** 또는 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| processingStatus | 열거형 | 승인의 처리 상태. 허용되는 값은 **Received**, **Generated** 및 **Sent**입니다. 필수 사항입니다. |
| CorrelationMessageId | 문자열 | 상관 관계 메시지 ID {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}의 조합입니다. (선택 사항) |
| isMessageFailed | BOOLEAN | X12 메시지 실패 여부 필수 사항입니다. |
| ak2Segment | 문자열 | 수신된 기능 그룹 내의 트랜잭션 집합에 대한 승인 (선택 사항) |
| ak3Segment | 문자열 | 데이터 세그먼트의 오류를 보고합니다. (선택 사항) |
| ak5Segment | 문자열 | AK2 세그먼트에 식별된 트랜잭션 집합이 허용되는지 또는 거부되는지와 그 이유를 보고합니다. (선택 사항) |

## <a name="x12-interchange-tracking-schema"></a>X12 교환 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| 자산 | type | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | 문자열 | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | 문자열 | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | 문자열 | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | 문자열 | 수신 파트너 한정자 (필수) |
| receiverQualifier | 문자열 | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | 문자열 | 교환 컨트롤 번호 (선택 사항) |
| isaSegment | 문자열 | 메시지 ISA 세그먼트 (선택 사항) |
| isTechnicalAcknowledgmentExpected | BOOLEAN | X12 규약에서 기술 승인이 구성되었는지 여부 필수 사항입니다. |
| isMessageFailed | BOOLEAN | X12 메시지 실패 여부 필수 사항입니다. |
| isa09 | 문자열 | X12 문서 교환 날짜 (선택 사항) |
| isa10 | 문자열 | X12 문서 교환 시간 (선택 사항) |
| isa11 | 문자열 | X12 교환 컨트롤 표준 식별자 (선택 사항) |
| isa12 | 문자열 | X12 교환 컨트롤 버전 번호 (선택 사항) |
| isa14 | 문자열 | X12 승인이 요청되었습니다. (선택 사항) |
| isa15 | 문자열 | 테스트 또는 프로덕션에 대한 표시기 (선택 사항) |
| isa16 | 문자열 | 요소 구분 기호 (선택 사항) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 교환 승인 추적 스키마
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| 자산 | type | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | 문자열 | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | 문자열 | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | 문자열 | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | 문자열 | 수신 파트너 한정자 (필수) |
| receiverQualifier | 문자열 | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | 문자열 | 파트너로부터 수신된 기술 승인의 교환 컨트롤 번호 (선택 사항) |
| isaSegment | 문자열 | 파트너로부터 수신된 기술 승인의 ISA 세그먼트 (선택) |
| respondingInterchangeControlNumber |문자열 | 파트너로부터 수신된 기술 승인의 교환 컨트롤 번호 (선택 사항) |
| isMessageFailed | BOOLEAN | X12 메시지 실패 여부 필수 사항입니다. |
| statusCode | 열거형 | 교환 승인 상태 코드. 허용되는 값은 **Accepted**, **Rejected** 및 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| processingStatus | 열거형 | 승인 상태. 허용되는 값은 **Received**, **Generated** 및 **Sent**입니다. 필수 사항입니다. |
| ta102 | 문자열 | 교환 날짜 (선택 사항) |
| ta103 | 문자열 | 교환 시간 (선택 사항) |
| ta105 | 문자열 | 교환 노트 코드 (선택 사항) |

## <a name="x12-functional-group-tracking-schema"></a>X12 기능 그룹 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| 자산 | type | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | 문자열 | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | 문자열 | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | 문자열 | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | 문자열 | 수신 파트너 한정자 (필수) |
| receiverQualifier | 문자열 | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | 문자열 | 교환 컨트롤 번호 (선택 사항) |
| functionalGroupControlNumber | 문자열 | 기능 컨트롤 번호 (선택 사항) |
| gsSegment | 문자열 | 메시지 GS 세그먼트 (선택 사항) |
| isTechnicalAcknowledgmentExpected | BOOLEAN | X12 규약에서 기술 승인이 구성되었는지 여부 필수 사항입니다. |
| isFunctionalAcknowledgmentExpected | BOOLEAN | X12 규약에서 기능 승인이 구성되었는지 여부 필수 사항입니다. |
| isMessageFailed | BOOLEAN | X12 메시지 실패 여부 필수 사항입니다.|
| gs01 | 문자열 | 기능 식별자 코드 (선택 사항) |
| gs02 | 문자열 | 응용 프로그램 보낸 사람 코드 (선택 사항) |
| gs03 | 문자열 | 응용 프로그램 받는 사람 코드 (선택 사항) |
| gs04 | 문자열 | 기능 그룹 날짜 (선택 사항) |
| gs05 | 문자열 | 기능 그룹 시간 (선택 사항) |
| gs07 | 문자열 | 담당 에이전시 코드 (선택 사항) |
| gs08 | 문자열 | 버전/릴리스/산업 식별자 코드 (선택 사항) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 기능 그룹 승인 추적 스키마
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| 자산 | type | 설명 |
| --- | --- | --- |
| senderPartnerName | 문자열 | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | 문자열 | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | 문자열 | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | 문자열 | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | 문자열 | 수신 파트너 한정자 (필수) |
| receiverQualifier | 문자열 | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | 문자열 | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | 문자열 | 기술 승인이 파트너로부터 수신될 때 송신 쪽에 대해 입력되는 교환 컨트롤 번호 (선택 사항) |
| functionalGroupControlNumber | 문자열 | 기술 승인이 파트너로부터 수신될 때 송신 쪽에 대해 입력되는 기술 승인의 기능 그룹 컨트롤 번호 (선택 사항) |
| isaSegment | 문자열 | 교환 컨트롤 번호와 같으나 특정 경우에만 채워집니다. (선택 사항) |
| gsSegment | 문자열 | 기능 그룹 컨트롤 번호와 같으나 특정 경우에만 채워집니다. (선택 사항) |
| respondingfunctionalGroupControlNumber | 문자열 | 원래 기능 그룹의 컨트롤 번호 (선택 사항) |
| respondingFunctionalGroupId | 문자열 | 승인 기능 그룹 ID에서 AK101에 매핑됩니다. (선택 사항) |
| isMessageFailed | BOOLEAN | X12 메시지 실패 여부 필수 사항입니다. |
| statusCode | 열거형 | 승인 상태 코드. 허용되는 값은 **Accepted**, **Rejected** 또는 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| processingStatus | 열거형 | 승인의 처리 상태. 허용되는 값은 **Received**, **Generated** 및 **Sent**입니다. 필수 사항입니다. |
| ak903 | 문자열 | 수신된 트랜잭션 집합 수 (선택 사항) |
| ak904 | 문자열 | 식별된 기능 그룹에서 승인된 트랜잭션 집합 수 (선택 사항) |
| ak9Segment | 문자열 | AK1 세그먼트에 식별된 기능 그룹이 허용되는지 또는 거부되는지와 그 이유 (선택 사항) |

## <a name="next-steps"></a>다음 단계
* [B2B 메시지 모니터링에 대해 자세히 알아보기](logic-apps-monitor-b2b-message.md)
* [AS2 추적 스키마에 대해 자세히 알아보기](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B 사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)에 대해 자세히 알아봅니다.
* [Log Analytics에서 B2B 메시지 추적](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)에 대해 알아봅니다.
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 알아봅니다.  
