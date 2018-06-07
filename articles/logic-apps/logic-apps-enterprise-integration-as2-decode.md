---
title: "AS2 메시지 디코딩 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps의 엔터프라이즈 통합 팩에 포함된 AS2 디코더를 사용하는 방법"
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
ms.date: 01/27/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4acae9f1837069c494985ff1456979490485f609
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에 대한 AS2 메시지 디코딩 

메시지를 전송하는 동안 보안 및 안정성을 설정하려면 AS2 메시지 디코딩 커넥터를 사용합니다. 이 커넥터에서는 MDN(메시지 처리 알림)을 통해 디지털 서명, 암호 해독 및 승인을 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

필요한 항목은 다음과 같습니다.

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 이미 정의되고 Azure 구독과 연결되었습니다. AS2 메시지 디코딩 커넥터를 사용하는 통합 계정이 있어야 합니다.
* 통합 계정에 이미 정의된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md)
* 통합 계정에 이미 정의된 [AS2 규약](logic-apps-enterprise-integration-as2.md)

## <a name="decode-as2-messages"></a>AS2 메시지 디코딩

1. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)

2. AS2 메시지 디코딩 커넥터에는 트리거가 없으므로 요청 트리거와 마찬가지로 논리 앱을 시작하는 트리거를 추가해야 합니다. Logic App Designer에서 트리거를 추가하고 작업을 논리 앱에 추가합니다.

3.  검색 상자에서 필터에 "AS2"를 입력합니다. **AS2 – AS2 메시지 디코딩**을 선택합니다.
   
    !["AS2" 검색](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. 이전에 통합 계정에 연결을 만들지 않은 경우 이제 해당 연결을 만들라는 메시지가 나타납니다. 연결의 이름을 지정하고 연결하려는 통합 계정을 선택합니다.
   
    ![통합 연결 만들기](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    별표가 있는 속성은 필수 사항입니다.

    | 자산 | 세부 정보 |
    | --- | --- |
    | 연결 이름 * |연결의 이름을 입력합니다. |
    | 통합 계정 * |통합 계정의 이름을 입력합니다. 통합 계정 및 논리 앱이 동일한 Azure 위치에 있어야 합니다. |

5.  완료되면 연결 정보는 이 예제와 유사해야 합니다. 연결 만들기를 완료하려면 **만들기**를 선택합니다.

    ![통합 연결 세부 정보](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. 이 예제와 같이 연결을 만든 후에 요청 출력에서 **본문** 및 **헤더**를 선택합니다.
   
    ![통합 연결 생성](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    예: 

    ![요청 출력에서 본문 및 헤더를 선택합니다.](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>AS2 디코더 세부 정보

AS2 디코딩 커넥터는 다음과 같은 태스크를 수행합니다. 

* AS2/HTTP 헤더 처리
* 서명 확인(구성된 경우)
* 메시지 암호 해독(구성된 경우)
* 메시지 압축 해제(구성된 경우)
* 원본 아웃바운드 메시지와 함께 수신된 MDN 조정
* 부인 방지 데이터베이스에서 레코드 업데이트 및 연결
* AS2 상태 보고에 대한 레코드 작성
* 출력 페이로드 콘텐츠는 base64로 인코딩됨
* MDN이 필요한지 여부 및 MDN을 AS2 규약의 구성에 따라 동기화해야 하는지 또는 비동기화해야 하는지 결정
* (규약 구성에 따라)동기 또는 비동기 MDN 생성
* MDN에 대한 상관 관계 토큰과 속성 설정

## <a name="try-this-sample"></a>이 샘플 사용해보기

완벽하게 작동하는 논리 앱 및 샘플 AS2 시나리오를 배포하려면 [AS2 논리 앱 템플릿 및 시나리오](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)를 참조하세요.

## <a name="view-the-swagger"></a>swagger 보기
[swagger 정보](/connectors/as2/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md) 

