---
title: B2B 엔터프라이즈 통합용 AS2 메시지 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps과 B2B 엔터프라이즈 통합용 AS2 메시지 교환
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: LADocs; divswa
ms.openlocfilehash: acc66f2cc88734b5c15fb9f4ace8339908418463
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>논리 앱과 엔터프라이즈 통합용 AS2 메시지 교환

Azure Logic Apps의 AS2 메시지를 교환하기 전에 AS2 규약을 만들고 통합 계정에 해당 규약을 저장해야 합니다. AS2 규약을 만드는 방법에 대한 단계는 다음과 같습니다.

## <a name="before-you-start"></a>시작하기 전에

필요한 항목은 다음과 같습니다.

* 이미 정의되고 Azure 구독과 연결된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-accounts.md)
* 통합 계정에 정의되고 **비즈니스 ID** 아래에서 AS2 한정자를 사용하여 구성된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md)

> [!NOTE]
> 규약을 만들 때 규약 파일의 내용이 규약 유형과 일치해야 합니다.    

[통합 계정을 만들고](../logic-apps/logic-apps-enterprise-integration-accounts.md) [파트너를 추가](logic-apps-enterprise-integration-partners.md)한 후에 다음과 같은 단계에 따라 AS2 규약을 만들 수 있습니다.

## <a name="create-an-as2-agreement"></a>AS2 규약 만들기

1.  [Azure Portal](http://portal.azure.com "Azure Portal")에 로그인합니다.  

2. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "통합"을 입력한 다음, **통합 계정**을 선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > **모든 서비스**가 표시되지 않으면 먼저 메뉴를 확장해야 합니다. 축소된 메뉴의 맨 위에서 **텍스트 레이블 표시**를 선택합니다.

3. **통합 계정**에서 규약을 만들려는 통합 계정을 선택합니다.

   ![규약을 만들 통합 계정 선택](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. **규약** 타일을 선택합니다. 규약 타일이 없는 경우 먼저 타일을 추가합니다.

    !["규약" 타일 선택](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. **규약**에서 **추가**를 선택합니다.

    !["추가" 선택](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. **추가** 아래에서 규약의 **이름**을 입력합니다. **규약 유형**에 **AS2**를 선택합니다. 규약의 **호스트 파트너**, **호스트 ID**, **게스트 파트너** 및 **게스트 ID**를 선택합니다.

    ![규약 세부 정보 제공](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | 자산 | 설명 |
    | --- | --- |
    | Name |규약 이름 |
    | 규약 유형 | AS2여야 함 |
    | 호스트 파트너 |규약에는 호스트 및 게스트 파트너가 필요합니다. 호스트 파트너는 규약을 구성하는 조직을 나타냅니다. |
    | 호스트 ID |호스트 파트너의 식별자입니다. |
    | 게스트 파트너 |규약에는 호스트 및 게스트 파트너가 필요합니다. 게스트 파트너는 호스트 파트너와 함께 일하는 조직을 나타냅니다. |
    | 게스트 ID |게스트 파트너의 식별자입니다. |
    | 수신 설정 |규약에서 받은 모든 메시지에 이러한 속성을 적용합니다. |
    | 송신 설정 |규약에서 보낸 모든 메시지에 이러한 속성을 적용합니다. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>규약에서 수신된 메시지를 처리하는 방법 구성

규약 속성을 설정했으므로 규약이 이 계약을 통해 파트너로부터 받은 들어오는 메시지를 식별하고 처리하는 방법을 구성할 수 있습니다.

1.  **추가** 아래에서 **수신 설정**을 선택합니다.
사용자와 메시지를 교환하는 파트너와의 규약에 따라 이러한 속성을 구성합니다. 속성 설명은 이 섹션에 있는 테이블을 참조하세요.

    !["수신 설정" 구성](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. 필요에 따라 **메시지 속성 재정의**를 선택하여 들어오는 메시지의 속성을 재정의할 수 있습니다.

3. 들어오는 모든 메시지를 서명하도록 요구하려면 **메시지 서명 필요**를 선택합니다. [인증서](../logic-apps/logic-apps-enterprise-integration-certificates.md) 목록에서 기존 **게스트 파트너 공용 인증서**를 선택하여 메시지에 대한 서명의 유효성을 검사합니다. 또는 인증서가 하나도 없는 경우 인증서를 만듭니다.

4.  들어오는 모든 메시지를 암호화하도록 요구하려면 **메시지 암호화 필요**를 선택합니다. **인증서** 목록에서 기존 [호스트 파트너 개인 인증서](../logic-apps/logic-apps-enterprise-integration-certificates.md)를 선택하여 들어오는 메시지를 암호 해독합니다. 또는 인증서가 하나도 없는 경우 인증서를 만듭니다.

5. 메시지를 압축하도록 요구하려면 **메시지 압축 필요**를 선택합니다.

6. 받은 메시지에 대한 동기 MDN(메시지 처리 알림)을 보내려면 **MDN 보내기**를 선택합니다.

7. 받은 메시지에 대한 서명된 MDN을 보내려면 **서명된 MDN 보내기**를 선택합니다.

8. 받은 메시지에 대한 비동기 MDN을 보내려면 **비동기 MDN 보내기**를 선택합니다.

9. 작업을 마친 후에 **확인**을 선택하여 설정을 저장해야 합니다.

이제 규약은 선택한 설정에 맞는 들어오는 메시지를 처리할 준비가 되었습니다.

| 자산 | 설명 |
| --- | --- |
| 메시지 속성 재정의 |받은 메시지의 속성을 재정의할 수 있음을 나타냅니다. |
| 메시지 서명 필요 |메시지를 디지털로 서명하도록 요구합니다. 서명 확인을 위한 게스트 파트너 공용 인증서를 구성합니다.  |
| 메시지 암호화 필요 |메시지를 암호화하도록 요구합니다. 암호화되지 않은 메시지는 거부됩니다. 메시지 암호를 해독하기 위한 호스트 파트너 개인 인증서를 구성합니다.  |
| 메시지 압축 필요 |메시지를 압축하도록 요구합니다. 압축되지 않은 메시지는 거부됩니다. |
| MDN 텍스트 |메시지 보낸 사람에게 보낼 기본 MDN(메시지 처리 알림)입니다. |
| MDN 전송 |MDN을 보내도록 요구합니다. |
| 서명된 MDN 전송 |MDN을 서명하도록 요구합니다. |
| MIC 알고리즘 |메시지 서명에 사용할 알고리즘을 선택합니다. |
| 비동기 MDN 전송 | 메시지를 비동기적으로 보내도록 요구합니다. |
| URL | MDN을 보낼 URL을 지정합니다. |

## <a name="configure-how-your-agreement-sends-messages"></a>규약에서 메시지를 보내는 방법 구성

이 규약이 규약을 통해 파트너에게 보내는 메시지를 식별하고 처리하는 방법을 구성할 수 있습니다.

1.  **추가** 아래에서 **송신 설정**을 선택합니다.
사용자와 메시지를 교환하는 파트너와의 규약에 따라 이러한 속성을 구성합니다. 속성 설명은 이 섹션에 있는 테이블을 참조하세요.

    !["설정 보내기" 속성 설정](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. 파트너에게 서명된 메시지를 보내려면 **메시지 서명 사용**을 선택합니다. 메시지에 서명하려면 **MIC Algorithm** 목록에서 *호스트 파트너 개인 인증서 MIC 알고리즘*을 선택합니다. 그리고 **인증서** 목록에서 기존 [호스트 파트너 개인 인증서](../logic-apps/logic-apps-enterprise-integration-certificates.md)를 선택합니다.

3. 파트너에게 암호화된 메시지를 보내려면 **메시지 암호화 사용**을 선택합니다. 메시지를 암호화하기 위해 **암호화 알고리즘** 목록에서 *게스트 파트너 공용 인증서 알고리즘*을 선택합니다.
그리고 **인증서** 목록에서 기존 [게스트 파트너 공용 인증서](../logic-apps/logic-apps-enterprise-integration-certificates.md)를 선택합니다.

4. 메시지를 압축하려면 **메시지 압축 사용**을 선택합니다.

5. HTTP 콘텐츠 형식 헤더를 단일 줄로 펼치려면 **HTTP 헤더 펼침**을 선택합니다.

6. 보낸 메시지에 대한 동기 MDN을 받으려면 **MDN 요청**을 선택합니다.

7. 보낸 메시지에 대한 서명된 MDN을 받으려면 **서명된 MDN 요청**을 선택합니다.

8. 보낸 메시지에 대한 동기 MDN을 받으려면 **비동기 MDN 요청**을 선택합니다. 이 옵션을 선택하면 MDN을 보낼 URL을 입력합니다.

9. NRR(수신 거부 없음)을 요구하려면 **NRR 사용**을 선택합니다.  

10. MIC에서 사용할 알고리즘 형식을 지정하거나 AS2 메시지 또는 MDN의 나가는 헤더에서 서명하려면 **SHA2 알고리즘 형식**을 선택합니다.  

11. 작업을 마친 후에 **확인**을 선택하여 설정을 저장해야 합니다.

이제 규약은 선택한 설정에 맞는 보내는 메시지를 처리할 준비가 되었습니다.

| 자산 | 설명 |
| --- | --- |
| 메시지 서명 사용 |규약에서 보낸 모든 메시지를 서명하도록 요구합니다. |
| MIC 알고리즘 |메시지 서명에 사용할 알고리즘입니다. 메시지에 서명하기 위한 호스트 파트너 개인 인증서 MIC 알고리즘을 구성합니다. |
| 인증서 |메시지 서명에 사용할 인증서를 선택합니다. 메시지에 서명하기 위한 호스트 파트너 개인 인증서를 구성합니다. |
| 메시지 암호화 사용 |이 규약에서 보낸 모든 메시지를 암호화하도록 요구합니다. 메시지를 암호화하기 위한 게스트 파트너 공용 인증서 알고리즘을 구성합니다. |
| 암호화 알고리즘 |메시지 암호화에 사용할 암호화 알고리즘입니다. 메시지를 암호화하기 위한 게스트 파트너 공용 인증서를 구성합니다. |
| 인증서 |메시지를 암호화하는 데 사용할 인증서입니다. 메시지를 암호화하기 위한 게스트 파트너 개인 인증서를 구성합니다. |
| 메시지 압축 사용 |이 규약에서 보낸 모든 메시지를 압축하도록 요구합니다. |
| HTTP 헤더 펼침 |HTTP content-type 헤더를 한 줄에 배치합니다. |
| MDN 요청 |이 규약에서 보낸 모든 메시지에 대한 MDN을 요구합니다. |
| 서명된 MDN 요청 |이 규약으로 보내는 모든 MDN을 서명하도록 요구합니다. |
| 비동기 MDN 요청 |이 규약으로 비동기 MDN을 보내도록 요구합니다. |
| URL |MDN을 보낼 URL을 지정합니다. |
| NRR 사용 |지정된 대로 데이터를 받았다는 증명 정보를 제공하는 통신 특성인 NRR(수신 거부 없음)을 요구합니다. |
| SHA2 알고리즘 형식 |MIC에서 사용할 알고리즘 형식을 선택하거나 AS2 메시지 또는 MDN의 나가는 헤더에 서명합니다. |

## <a name="find-your-created-agreement"></a>생성된 규약 찾기

1. 모든 규약 속성 설정을 완료한 후에 **추가** 페이지에서 **확인**을 선택하여 규약 만들기를 완료하고 사용자 통합 계정으로 돌아갑니다.

    이제 새로 추가된 규약이 **규약** 목록에 표시됩니다.

2. 또한 통합 계정 개요에서 규약을 볼 수도 있습니다. 통합 계정 메뉴에서 **개요**를 선택한 다음, **규약** 타일을 선택합니다. 

   ![모든 규약을 보려면 "규약" 타일을 선택합니다](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>swagger 보기
[swagger 정보](/connectors/as2/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  
