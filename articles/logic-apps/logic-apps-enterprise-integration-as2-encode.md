---
title: "AS2 메시지 인코딩 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps의 엔터프라이즈 통합 팩에 포함된 AS2 인코더를 사용하는 방법"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bccc8f81a3a664e090796ae8a3cbb38c890c0479
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에 대한 AS2 메시지 인코딩

메시지를 전송하는 동안 보안 및 안정성을 설정하려면 AS2 메시지 인코딩 커넥터를 사용합니다. 이 커넥터에서는 MDN(메시지 처리 알림)을 통해 디지털 서명, 암호화 및 승인을 제공하며 부인 방지에 대한 지원도 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

필요한 항목은 다음과 같습니다.

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 이미 정의되고 Azure 구독과 연결되었습니다. AS2 메시지 인코딩 커넥터를 사용하는 통합 계정이 있어야 합니다.
* 통합 계정에 이미 정의된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md)
* 통합 계정에 이미 정의된 [AS2 규약](logic-apps-enterprise-integration-as2.md)

## <a name="encode-as2-messages"></a>AS2 메시지 인코딩

1. [논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)

2. AS2 메시지 인코딩 커넥터에는 트리거가 없으므로 요청 트리거와 마찬가지로 논리 앱을 시작하는 트리거를 추가해야 합니다. Logic App Designer에서 트리거를 추가하고 작업을 논리 앱에 추가합니다.

3.  검색 상자에서 필터에 "AS2"를 입력합니다. **AS2 – AS2 메시지 인코딩**을 선택합니다.
   
    !["AS2" 검색](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. 이전에 통합 계정에 연결을 만들지 않은 경우 이제 해당 연결을 만들라는 메시지가 나타납니다. 연결의 이름을 지정하고 연결하려는 통합 계정을 선택합니다. 
   
    ![통합 계정에 연결 만들기](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    별표가 있는 속성은 필수 사항입니다.

    | 자산 | 세부 정보 |
    | --- | --- |
    | 연결 이름 * |연결의 이름을 입력합니다. |
    | 통합 계정 * |통합 계정의 이름을 입력합니다. 통합 계정 및 논리 앱이 동일한 Azure 위치에 있어야 합니다. |

5.  완료되면 연결 정보는 이 예제와 유사해야 합니다. 연결 만들기를 완료하려면 **만들기**를 선택합니다.
   
    ![통합 연결 세부 정보](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. 이 예제에 표시된 대로 연결을 만든 후에 규약에 구성된 대로 **AS2-From**, **AS2-To identifiers**에 대한 세부 정보를 제공하고 메시지 페이로드인 **본문**에 대한 세부 정보를 제공합니다.
   
    ![필수 필드 제공](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2 인코더 세부 정보

AS2 인코딩 커넥터는 다음과 같은 태스크를 수행합니다. 

* AS2/HTTP 헤더 적용
* 나가는 메시지 서명(구성된 경우)
* 나가는 메시지 암호화(구성된 경우)
* 메시지 압축(구성된 경우)

## <a name="try-this-sample"></a>이 샘플 사용해보기

완벽하게 작동하는 논리 앱 및 샘플 AS2 시나리오를 배포하려면 [AS2 논리 앱 템플릿 및 시나리오](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)를 참조하세요.

## <a name="view-the-swagger"></a>swagger 보기
[swagger 정보](/connectors/as2/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기") 

