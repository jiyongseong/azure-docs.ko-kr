---
title: "Azure Logic Apps에서 Dynamics 365(온라인) 연결 | Microsoft Docs"
description: "Dynamics 365 커넥터가 제공하는 API를 통해 Dynamics 365(온라인) 엔터티를 관리하는 논리 앱 워크플로 만들기"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>논리 앱 워크플로에서 Dynamics 365에 연결

Logic Apps를 사용하여 Dynamics 365(온라인)에 연결하고 레코드를 만들고 항목을 업데이트하거나 레코드 목록을 반환하는 유용한 비즈니스 흐름을 만들 수 있습니다. Dynamics 365 커넥터를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* Dynamics 365(온라인)에서 가져온 데이터에 기반한 비즈니스 흐름을 구축합니다.
* 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Dynamics 365(온라인)에서 항목이 업데이트되면 Office 365를 사용하여 메일을 보낼 수 있습니다.

이 항목에서는 Dynamics 365에서 새 리드가 생성될 때마다 Dynamics 365에서 작업을 생성하는 논리 앱 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
* Azure 계정.
* Dynamics 365(온라인) 계정.

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Dynamics 365에서 새 리드가 생성될 작업 만들기

1.  [Azure에 로그인](https://portal.azure.com)합니다.

2.  Azure 검색 상자에 `Logic apps`를 입력하고 ENTER 키를 누릅니다.

      ![Logic Apps 찾기](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  **논리 앱** 아래에서 **추가**를 클릭합니다.

      ![LogicApp 추가](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  논리 앱을 만들려면 **이름**, **구독**, **리소스 그룹** 및 **위치** 필드를 작성하고 **만들기**를 클릭합니다.

5.  새 논리 앱을 선택합니다. **배포 성공** 알림이 수신되면 **새로 고침**을 클릭합니다.

6.  **배포 도구** 아래에서 **논리 앱 디자이너**를 클릭합니다. 템플릿 목록에서 **빈 논리 앱**을 클릭합니다.

7.  검색 상자에서 `Dynamics 365`를 입력합니다. Dynamics 365 트리거 목록에서 **Dynamics 365 – 레코드가 만들어지는 경우**를 선택합니다.

8.  Dynamics 365에 로그인하라는 메시지가 나타나면 지금 로그인합니다.

9.  트리거 세부 정보에서 다음 정보를 입력합니다.

  * **조직 이름**. 논리 앱에서 수신하려는 Dynamics 365 인스턴스를 선택합니다.

  * **엔터티 이름**. 수신 대기할 엔터티를 선택합니다. 이 이벤트는 논리 앱을 시작하는 트리거 역할을 합니다. 
  이 연습에서는 **리드**를 선택합니다.

  * **얼마나 자주 항목을 확인하시겠습니까?** 이 값은 논리 앱이 트리거와 관련된 업데이트를 확인하는 빈도를 설정합니다. 기본 설정은 3분마다 업데이트를 확인하는 것입니다.

    * **빈도**. 초, 분, 시간 또는 일을 선택합니다.

    * **간격**. 다음 확인 때까지 경과할 초, 분, 시간, 일수를 입력합니다.

      ![논리 앱 트리거 세부 정보](./media/connectors-create-api-crmonline/trigger-details.png)

10. **새 단계**를 클릭한 다음 **작업 추가**를 클릭합니다.

11. 검색 상자에서 `Dynamics 365`를 입력합니다. 작업 목록에서 **Dynamics 365 – 새 레코드 만들기**를 선택합니다.

12. 다음 정보를 입력합니다.

    * **조직 이름**. 흐름에서 레코드를 만들려는 Dynamics 365 인스턴스를 선택합니다. 
    이 인스턴스는 이벤트가 트리거되는 인스턴스와 같을 필요는 없습니다.

    * **엔터티 이름**. 이벤트가 트리거될 때 레코드를 만들려는 엔터티를 선택합니다. 
    이 연습에서는 **작업**을 선택합니다.

13. 나타나는 **주제** 상자를 클릭합니다. 나타나는 동적 콘텐츠 목록에서 이러한 필드 중 하나를 선택할 수 있습니다.

    * **성**. 이 필드를 선택하면 작업 레코드가 만들어질 때 리드의 성이 작업의 [제목] 필드에 삽입됩니다.
    * **토픽**. 이 필드를 선택하면 작업 레코드가 만들어질 때 리드의 토픽 필드가 작업의 [제목] 필드에 삽입됩니다. 
    **토픽**을 클릭하여 **제목** 상자에 추가합니다.

      ![논리 앱 새 레코드 만들기 세부 정보](./media/connectors-create-api-crmonline/create-record-details.png)

14. 논리 앱 디자이너 도구 모음에서 **저장**을 클릭합니다.

    ![논리 앱 디자이너 도구 모음 저장](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. 논리 앱을 시작하려면 **실행**을 클릭합니다.

    ![논리 앱 디자이너 도구 모음 저장](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. 이제 Dynamics 365 for Sales에서 리드 레코드를 만들고 흐름이 어떻게 진행되는지 확인하세요!

## <a name="set-advanced-options-for-a-logic-app-step"></a>논리 앱 단계에 대한 고급 옵션 설정

논리 앱 단계에서 데이터를 필터링하는 방법을 지정하려면 해당 단계에서 **고급 옵션 표시**를 클릭한 후 쿼리로 필터 또는 주문을 추가합니다.

예를 들어 필터 쿼리를 사용하여 계정 이름으로 활성 계정 및 주문만 가져올 수 있습니다. 이 작업을 수행하려면 OData 필터 쿼리 `statuscode eq 1`을 입력하고 동적 콘텐츠 목록에서 **계정 이름**을 선택합니다. 자세한 정보: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) 및 [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2)

![논리 앱 고급 옵션](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>고급 옵션을 사용하는 경우 모범 사례

필드에 값을 추가할 경우 값을 입력하거나 표시된 동적 콘텐츠 목록에서 선택하거나 어떤 방법에서든, 해당 필드 형식과 일치해야 합니다.

필드 형식  |사용 방법  |찾는 위치  |Name  |데이터 형식  
---------|---------|---------|---------|---------
텍스트 필드|텍스트 필드에는 한 줄의 텍스트 또는 텍스트 형식 필드인 동적 콘텐츠가 필요합니다. 이러한 예로 범주, 하위 범주 필드가 있습니다.|설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 작업 > 필드 |카테고리 |한 줄의 텍스트        
정수 필드 | 일부 필드에는 정수 또는 정수 형식 필드인 동적 콘텐츠가 필요합니다. 이러한 예로 완료율, 기간이 있습니다. |설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 작업 > 필드 |percentcomplete |정수         
날짜 필드 | 일부 필드에 mm/dd/yyyy 형식 또는 날짜 형식 필드인 동적 콘텐츠가 필요합니다. 이러한 예로 만든 날짜, 시작 날짜, 실제 시작, 마지막 보류 시간, 실제 종료, 기한 날짜가 있습니다. | 설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 작업 > 필드 |createdon |날짜 및 시간
레코드 ID와 조회 유형이 모두 필요한 필드입니다. |다른 엔터티 레코드를 참조하는 일부 필드에는 레코드 ID와 조회 유형이 모두 필요합니다. |설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 계정 > 필드  | accountid  | 기본 키

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>레코드 ID와 조회 유형이 모두 필요한 필드의 추가 예
이전 테이블을 확장하면 동적 콘텐츠 목록에서 선택한 값으로 작동하지 않는 필드의 예가 더 있습니다. 대신, 이러한 필드에는 PowerApps의 필드에 입력된 레코드 ID와 조회 형식이 모두 필요합니다.  
* 소유자 및 소유자 유형. [소유자] 필드에는 유효한 사용자 또는 팀 레코드 ID가 있어야 합니다. [소유자 유형]은 **시스템 사용자** 또는 **팀**이어야 합니다.
* 고객 및 고객 유형. [고객] 필드는 유효한 계정 또는 연락처 레코드 ID여야 합니다. 소유자 유형은 **계정** 또는 **연락처**이어야 합니다.
* 관련 항목 및 관련 유형. [관련 항목] 필드는 계정 또는 연락처 레코드 ID 등의 유효한 레코드 ID여야 합니다. [관련 항목 유형]은 **계정** 또는 **연락처** 등의 레코드에 대한 조회 유형이어야 합니다.

다음 작업 만들기 예에서는 작업의 관련 항목 필드에 추가하여 레코드 ID에 해당하는 계정 레코드를 추가합니다.

![흐름 recordId 및 계정 유형](./media/connectors-create-api-crmonline/recordid-type-account.png)

이 예제에서는 사용자의 레코드 ID에 따라 특정 사용자에게 작업을 할당합니다.

![흐름 recordId 및 계정 유형](./media/connectors-create-api-crmonline/recordid-type-user.png)

레코드의 ID를 찾으려면 아래의 *레코드 ID 찾기* 섹션을 참조하세요.

## <a name="find-the-record-id"></a>레코드 ID 찾기

1. 계정 레코드 등의 레코드를 엽니다.

2. 작업 도구 모음에서 **팝아웃** ![팝아웃 레코드](./media/connectors-create-api-crmonline/popout-record.png)를 클릭합니다.
또는, 작업 도구 모음에서 전체 URL을 기본 전자 메일 프로그램에 복사하려면 **전자 메일로 링크 보내기**를 클릭합니다.

   레코드 ID는 URL의 %7b ~ %7d개의 인코딩 문자로 표시됩니다.

   ![흐름 recordId 및 계정 유형](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>문제 해결
논리 앱에서 실패한 단계의 문제를 해결하려면 이벤트의 상태 세부 정보를 확인하세요.

1. **Logic Apps** 아래에서 논리 앱을 선택한 다음 **개요**를 클릭합니다. 

   [요약] 영역이 표시되고 논리 앱에 대한 실행 상태를 제공합니다. 

   ![논리 앱 실행 상태](./media/connectors-create-api-crmonline/tshoot1.png)

2. 실패한 실행에 대한 자세한 정보를 보려면 실패한 이벤트를 클릭합니다. 실패한 단계를 확장하려면 해당 단계를 클릭합니다.

   ![실패한 단계 확장](./media/connectors-create-api-crmonline/tshoot2.png)

   단계의 세부 정보가 표시되고 실패 원인의 문제 해결에 도움이 될 수 있습니다.

   ![실패한 단계 세부 정보](./media/connectors-create-api-crmonline/tshoot3.png)

논리 앱 문제 해결에 대한 자세한 내용은 [논리 앱 오류 진단](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/crm/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.
