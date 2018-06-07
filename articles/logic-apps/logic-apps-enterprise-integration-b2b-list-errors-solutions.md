---
title: "Logic Apps B2B 오류 및 해결 방법 목록: Azure App Service | Microsoft Docs"
description: "Logic Apps B2B 오류 및 해결 방법 목록"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 1865d75f1b4c2aa18d5a3130f639572d19563b3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Logic Apps B2B 오류 및 해결 방법 목록  
이 문서에서는 Logic Apps B2B 시나리오에서 발생할 수 있는 오류를 해결하고 이러한 오류를 수정하기 위한 적절한 조치를 제안합니다.


## <a name="agreement-resolution"></a>규약 확인

### <a name="no-agreement-found"></a>* 규약을 찾을 수 없음 

|   |   |  
|---|---|
| 오류 설명 | 규약 확인 매개 변수가 있는 규약을 찾을 수 없습니다.|    
| 사용자 조치 | 동의한 비즈니스 ID로 통합 계정에 규약을 추가해야 합니다.</br> 비즈니스 ID는 입력 메시지 ID와 일치해야 합니다.|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* ID가 있는 규약을 찾을 수 없음

|   |   | 
|---|---|
| 오류 설명 | ID가 'AS2Identity'::'Partner1' 및 'AS2Identity'::'Partner3'인 규약을 찾을 수 없습니다.| 
| 사용자 조치 | 규약에 대해 구성된 AS2-From 또는 AS2-To가 잘못되었습니다. </br> AS2 메시지 헤더의 AS2 ID가 규약 구성과 일치하도록 AS2 메시지 AS2-From/AS2-To 헤더 또는 규약을 수정합니다. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* 누락된 AS2 메시지 헤더  

|   |   |  
|---|---|
| 오류 설명| AS2 헤더가 잘못되었습니다. 'AS2-To' 또는 'AS2-From' 헤더 중 하나가 비어 있습니다.| 
| 사용자 조치 | AS2-From, AS2-To 또는 두 헤더가 포함되지 않은 AS2 메시지를 받았습니다. </br> AS2 메시지 AS2-From 및 AS2-To 헤더를 확인하고 규약 구성에 따라 수정합니다. |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* 누락된 AS2 메시지 본문 및 헤더    

|   |   |  
|---|---|
| 오류 설명| 요청 콘텐츠가 null이거나 비어 있습니다. | 
| 사용자 조치 | 메시지 본문이 포함되지 않은 AS2 메시지를 받았습니다. |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* AS2 메시지 암호 해독 실패

|   |   | 
|---|---|
| 오류 설명 |  [처리됨/오류: 암호 해독 실패] | 
| 사용자 조치 | 파트너에게 보내기 전에 AS2Message에 @base64ToBinary를 추가합니다. 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* MDN 암호 해독 실패

|   |   | 
|---|---|
| 오류 설명 |  [처리됨/오류: 암호 해독 실패] | 
| 사용자 조치 | 파트너에게 보내기 전에 MDN에 @base64ToBinary를 추가합니다. 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* 누락된 서명 인증서

|   |   |  
|---|---|
| 오류 설명| AS2 파티에 대해 서명 인증서가 구성되지 않았습니다. </br> AS2-From: partner1 AS2-To: partner2 | 
| 사용자 조치 | 서명을 위한 올바른 인증서로 AS2 규약 설정을 구성합니다. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 및 EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* 선행 공백 또는 후행 공백이 있음    
    
|   |   | 
|---|---|
| 오류 설명 | 구문 분석 중에 오류가 발생했습니다. ID가 '987654'이고, 보낸 사람 ID가 'Partner1'이고, 받는 사람 ID가 'Partner2'인 교환(그룹 없음)에 포함된 ID가 '123456'인 Edifact 트랜잭션 집합이 다음 오류로 인해 일시 중단됩니다. "선행 분리 기호 또는 후행 분리 기호가 있습니다." |
| 사용자 조치 | 선행 공백 및 후행 공백을 허용하도록 구성할 규약 설정입니다. </br> 선행 공백 및 후행 공백을 허용하도록 규약 설정을 편집합니다. |
|   |   |

![공백 허용](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* 규약에서 중복 검사가 활성화됨

|   |   | 
|---|---| 
| 오류 설명 | 중복 컨트롤 번호가 있습니다. |
| 사용자 조치 | 이 오류는 받은 메시지에 중복 컨트롤 번호가 있음을 나타냅니다. </br> 컨트롤 번호를 수정하고 메시지를 다시 보냅니다. |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* 규약에서 누락된 스키마

|   |   | 
|---|---| 
| 오류 설명 | 구문 분석 중에 오류가 발생했습니다. ID가 '000056422'이고, 보낸 사람 ID가 '12345678       '이고, 받는 사람 ID가 '87654321       '인 교환에서 ID가 '56422'인 기능 그룹에 포함된 ID가 '564220001'인 X12 트랜잭션 집합이 다음 오류로 인해 일시 중단됩니다. "이 메시지의 문서 유형을 알 수 없으며 규약에 구성된 기존 스키마로 확인되지 않았습니다." |
| 사용자 조치 | 규약 설정에서 스키마를 구성합니다.  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* 규약에 잘못된 스키마가 있음

|   |   | 
|---|---| 
| 오류 설명 | 이 메시지의 문서 유형을 알 수 없으며 규약에 구성된 기존 스키마로 확인되지 않았습니다. |
| 사용자 조치 | 규약 설정에서 올바른 스키마를 구성합니다.  |
|   |   |

## <a name="flat-file"></a>플랫 파일

### <a name="-input-message-with-no-body"></a>* 본문이 없는 입력 메시지

|   |   | 
|---|---|
| 오류 설명 | 잘못된 템플릿(InvalidTemplate)입니다. 행 '1', 열 '1902'에 있는 'Flat_File_Decoding' 작업 입력의 템플릿 언어 식을 처리할 수 없습니다. ''content' 필수 속성은 값을 필요로 하지만 null을 받았습니다. 경로 ''.'. |
| 사용자 조치 | 이 오류는 입력 메시지에 본문이 포함되어 있지 않음을 나타냅니다. |
|   |   | 

## <a name="learn-more"></a>자세한 정보
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md)