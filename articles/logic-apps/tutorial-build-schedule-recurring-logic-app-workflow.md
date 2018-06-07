---
title: "스케줄러 기반 자동화 워크플로 빌드 - Azure Logic Apps | Microsoft Docs"
description: "이 자습서에서는 Azure Logic Apps를 사용하여 스케줄러 기반의 되풀이 자동화 워크플로를 만드는 방법을 보여줍니다."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 4360249367632a01e4357c527f595f8416c7cc38
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2018
---
# <a name="check-traffic-with-a-scheduler-based-logic-app"></a>스케줄러 기반 논리 앱으로 트래픽 확인

Azure Logic Apps는 일정에 따라 실행되는 워크플로를 자동화하는 데 도움을 줍니다. 이 자습서에서는 평일 아침마다 실행되어 트래픽을 포함한 두 장소 간의 이동 시간을 확인하는 스케줄러 트리거를 사용하여 [논리 앱](../logic-apps/logic-apps-overview.md)을 빌드하는 방법을 보여줍니다. 시간이 특정 제한을 초과하면 논리 앱에서는 이동 시간 및 목적지에 도달하는 데 걸리는 추가 시간이 포함된 이메일을 보냅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 빈 논리 앱을 만듭니다. 
> * 논리 앱의 스케줄러로 작동하는 트리거를 추가합니다.
> * 경로의 이동 시간을 가져오는 작업을 추가합니다.
> * 변수를 만들고, 이동 시간을 초에서 분으로 변환하고, 결과를 변수에 저장하는 작업을 추가합니다.
> * 이동 시간을 지정된 제한과 비교하는 조건을 추가합니다.
> * 이동 시간이 제한을 초과하면 이메일을 보내는 작업을 추가합니다.

여기까지 모두 마치면 논리 앱이 이 워크플로와 비슷하게 보입니다.

![상위 수준 논리 앱](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Azure 구독이 아직 없는 경우 시작하기 전에 <a href="https://azure.microsoft.com/free/" target="_blank">Azure 체험 계정에 등록합니다</a>.

## <a name="prerequisites"></a>필수 조건

* Office 365 Outlook, Outlook.com, Gmail 등 Logic Apps에서 지원되는 이메일 공급자의 이메일 계정. 다른 공급자에 대한 내용은 [여기서 커넥터 목록을 검토하세요](https://docs.microsoft.com/connectors/). 이 빠른 시작에서는 Outlook.com 계정을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다르게 표시될 수 있습니다.

* 경로의 이동 시간 정보를 가져오려면 Bing 지도 API에 대한 액세스 키가 필요합니다. 이 키를 가져오려면 <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Bing 지도 키를 가져오는 방법</a>의 단계를 수행합니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인합니다.

Azure 계정 자격 증명을 사용하여 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다.

## <a name="create-your-logic-app"></a>논리 앱 만들기

1. Azure 주 메뉴에서 **리소스 만들기** > **엔터프라이즈 통합** > **논리 앱**을 선택합니다.

   ![논리 앱 만들기](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. 그림에 설명된 대로 **논리 앱 만들기** 아래에서 논리 앱에 대한 정보를 제공합니다. 완료면 **대시보드에 고정** > **만들기**를 선택합니다.

   ![논리 앱 정보 제공](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | 설정 | 값 | 설명 | 
   | ------- | ----- | ----------- | 
   | **Name** | LA-TravelTime | 논리 앱의 이름 | 
   | **구독** | <*your-Azure-subscription-name*> | Azure 구독의 이름 | 
   | **리소스 그룹** | LA-TravelTime-RG | 관련 리소스를 구성하는 데 사용된 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름 | 
   | **위치**: | 미국 동부 2 | 논리 앱에 대한 정보를 저장할 지역 | 
   | **Log Analytics** | 꺼짐 | 진단 로깅에 대한 설정을 **끄기**로 유지합니다. | 
   |||| 

3. Azure가 앱을 배포한 후에 Logic Apps 디자이너가 열리고 일반적인 논리 앱 패턴에 대한 소개 비디오 및 템플릿이 포함된 페이지를 보여줍니다. **템플릿** 아래에서 **빈 Logic App**을 선택합니다.

   ![빈 논리 앱 템플릿을 선택합니다.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

다음으로, 지정된 일정에 따라 실행되는 되풀이 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 추가합니다. 모든 논리 앱은 특정 이벤트가 발생하거나 새 데이터가 특정 조건을 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 자세한 내용은 [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="add-scheduler-trigger"></a>스케줄러 트리거 추가

1. 디자이너에서 검색 상자에 "되풀이"를 입력합니다. **예약 - 되풀이** 트리거 선택

   !["예약-되풀이" 트리거를 찾아서 추가](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. **되풀이** 셰이프에서 **줄임표**(**...**) 단추를 선택하고 **이름 바꾸기**를 선택합니다. ```Check travel time every weekday morning``` 설명이 포함되도록 트리거 이름을 바꿉니다.

   ![트리거 이름 바꾸기](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. 트리거 내부에서 **고급 옵션 표시**를 선택합니다.

4. 그림에 설명된 대로 트리거의 일정 및 되풀이 세부 정보를 입력합니다.

   ![일정 및 되풀이 세부 정보 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | 설정 | 값 | 설명 | 
   | ------- | ----- | ----------- | 
   | **간격** | 1 | 검사 간에 대기하는 간격의 수 | 
   | **Frequency(빈도)** | 주 | 되풀이에 사용할 시간 단위 | 
   | **표준 시간대** | 없음 | 시작 시간을 지정하는 경우에만 적용됩니다. 비-현지 표준 시간대를 지정할 때 유용 합니다. | 
   | **시작 시간** | 없음 | 특정 날짜 및 시간이 될 때까지 되풀이를 지연합니다. 자세한 내용은 [정기적으로 실행되는 작업 및 워크플로 예약](../connectors/connectors-native-recurrence.md)을 참조하세요. | 
   | **요일 선택** | 월요일,화요일,수요일,목요일,금요일 | **빈도**가 "주"로 설정된 경우에만 사용 가능 | 
   | **시간 선택** | 7,8,9 | **빈도**가 "주" 또는 "일"로 설정된 경우에만 사용할 수 있습니다. 이 되풀이를 실행할 시간을 선택합니다. 이 예에서는 7, 8 및 9시 표시에 실행됩니다. | 
   | **분 선택** | 0,15,30,45 | **빈도**가 "주" 또는 "일"로 설정된 경우에만 사용할 수 있습니다. 이 되풀이를 실행할 분을 선택합니다. 이 예에서는 0시 표시에서 시작하여 15분마다 실행됩니다. | 
   ||||

   이 트리거는 평일 오전 7시부터 오전 9시 45분까지 15분마다 실행됩니다. 
   **미리 보기** 상자는 되풀이 일정을 표시합니다. 
   자세한 내용은 [작업 및 워크플로 예약](../connectors/connectors-native-recurrence.md) 및 [워크플로 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)를 참조하세요.

5. 지금은 트리거의 세부 정보를 숨기려면 셰이프의 제목 표시줄 내부를 클릭합니다.

   ![세부 정보를 숨기려면 셰이프 축소](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다. 

이제 논리 앱이 라이브 상태지만 되풀이 외에는 아무 것도 수행하지 않습니다. 따라서 트리거가 실행되면 응답하는 작업을 추가해 보겠습니다.

## <a name="get-the-travel-time-for-a-route"></a>경로의 이동 시간 가져오기

이제 트리거가 생겼으니, 두 장소 사이의 이동 시간을 가져오는 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)을 추가합니다. Logic Apps는 이 정보를 쉽게 얻을 수 있도록 Bing 지도 API에 대한 커넥터를 제공합니다. 이 작업을 시작하기 전에, 이 자습서의 필수 구성 섹션에서 설명한 Bing 지도 API 키를 갖고 있는지 확인합니다.

1. 논리 앱 디자이너의 트리거 아래에서 **+ 새 단계** > **작업 추가**를 선택합니다.

2. "지도"를 검색하고 **Bing 지도 - 경로 가져오기** 작업 선택

3. Bing 지도가 연결되지 않으면 연결을 만들라는 메시지가 나타납니다. 다음 연결 세부 정보를 입력하고 **만들기**를 선택합니다.

   !["Bing 지도 - 경로 가져오기" 작업](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | 설정 | 값 | 설명 |
   | ------- | ----- | ----------- |
   | **연결 이름** | BingMapsConnection | 연결 이름을 입력합니다. | 
   | **API 키** | <*your-Bing-Maps-key*> | 이전에 받은 Bing 지도 키를 입력합니다. Bing 지도 키가 없는 경우 <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">키를 가져오는 방법</a>을 알아보세요. | 
   | | | |  

4. ```Get route and travel time with traffic``` 설명이 포함되도록 작업 이름을 바꿉니다.

5. 여기에 설명된 것처럼 **경로 가져오기** 작업에 대한 세부 정보를 입력합니다. 예를 들어 다음과 같습니다.

   !["Bing 지도 - 경로 가져오기" 작업에 대한 정보 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | 설정 | 값 | 설명 |
   | ------- | ----- | ----------- |
   | **Waypoint 1** | <*시작 위치*> | 경로의 출발지 | 
   | **Waypoint 2** | <*마지막 위치*> | 경로의 도착지 | 
   | **Avoid** | 없음 | 고속도로, 톨게이트 등 경로에서 피해야 하는 항목 | 
   | **Optimize** | timeWithTraffic | 거리, 현재 트래픽 하에서의 이동 시간 등 경로를 최적화하기 위한 매개 변수. "timeWithTraffic" 매개 변수 선택 | 
   | **Distance unit** | <*원하는 단위*> | 경로의 거리 단위. 이 문서에서는 "마일" 단위 사용  | 
   | **Travel mode** | Driving | 경로의 이동 모드. "Driving" 모드 선택 | 
   | **Transit Date-Time** | 없음 | 수송 모드에만 적용 | 
   | **날짜 및 시간 형식** | 없음 | 수송 모드에만 적용 | 
   |||| 

   이러한 매개 변수에 대한 자세한 내용은 [경로 계산](https://msdn.microsoft.com/library/ff701717.aspx)을 참조하세요.

6. 논리 앱을 저장합니다.

다음으로, 현재 이동 시간을 초가 아닌 분으로 변환하여 저장할 수 있도록 변수를 만듭니다. 이렇게 하면 변환을 반복하지 않을 수 있으며 이후 단계에서 값을 좀 더 수월하게 사용할 수 있습니다. 

## <a name="create-variable-to-store-travel-time"></a>이동 시간을 저장하는 변수 만들기

워크플로에서 데이터에 대한 작업을 수행하고 그 결과를 이후 작업에 사용하려는 경우가 가끔 있습니다. 이러한 결과를 간편하게 재사용 또는 참조할 수 있도록 결과를 저장하려면 데이터 처리 후 결과를 저장하는 변수를 만들면 됩니다. 변수는 논리 앱의 최상위 수준에서만 만들 수 있습니다.

기본적으로 이전의 **경로 가져오기** 작업은 **이동 시간 트래픽** 필드를 통해 트래픽과 함께 현재 이동 시간을 초 단위로 반환합니다. 이 값을 분 단위로 변환하여 저장하면 나중에 변환 작업 없이 간편하게 값을 재사용할 수 있습니다.

1. **경로 가져오기** 작업에서 **+새 단계** > **작업 추가**를 선택합니다.

2. "변수"를 검색하고 **변수 - 변수 초기화** 작업을 선택합니다.

   !["변수 - 변수 초기화" 작업 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. ```Create variable to store travel time``` 설명이 포함되도록 이 작업의 이름을 바꿉니다.

4. 여기에 설명된 대로 변수에 대한 세부 정보를 입력합니다.

   | 설정 | 값 | 설명 | 
   | ------- | ----- | ----------- | 
   | **Name** | travelTime | 변수의 이름 | 
   | **형식** | 정수  | 변수의 데이터 형식 | 
   | **값** | 현재 이동 시간을 초 단위에서 분 단위로 변환하는 식입니다(이 표 아래의 단계 참조). | 변수의 초기 값 | 
   |||| 

   1. **값** 필드에 대한 식을 만들려면 동적 콘텐츠 목록이 표시되도록 필드 내부를 클릭합니다. 
   필요한 경우 목록이 나타날 때까지 브라우저를 확장합니다. 
   동적 콘텐츠 목록에서 **식**을 선택합니다. 

      !["변수 - 초기 변수" 작업에 대한 정보 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      일부 편집 상자 내부를 클릭하면 동적 콘텐츠 목록 또는 인라인 매개 변수 목록이 나타납니다. 이 목록에는 워크플로에 입력으로 사용할 수 있는 이전 작업의 매개 변수가 표시됩니다. 
      동적 콘텐츠 목록에는 작업을 수행하기 위한 함수를 선택할 수 있는 식 편집기가 있습니다. 
      이 식 편집기는 동적 콘텐츠 목록에만 나타납니다.

      브라우저 너비에 따라 표시되는 목록이 달라집니다. 
      브라우저가 넓으면 동적 콘텐츠 목록이 나타납니다. 
      브라우저가 좁으면 현재 포커스가 지정된 편집 상자 아래에 매개 변수 목록이 인라인으로 표시됩니다.

   2. 식 편집기에서 ```div(,60)``` 식을 입력합니다.

      !["div(,60)" 식 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. 왼쪽 괄호(**(**)와 쉼표(**,**) 사이의 식 내부에 커서를 놓습니다. 
   **동적 콘텐츠**를 선택합니다.

      ![커서 이동, "동적 콘텐츠" 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. 동적 콘텐츠 목록에서 **이동 시간 트래픽**을 선택합니다.

      !["이동 시간 트래픽" 필드 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. 필드에서 식 내부를 확인한 후 **확인**을 선택합니다.

      !["확인" 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      이제 다음과 같이 **값** 필드가 나타납니다.

      ![확인된 식과 함께 "값" 필드 표시](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. 논리 앱을 저장합니다.

다음으로, 현재 이동 시간이 특정 제한보다 큰지 확인하는 조건을 추가합니다.

## <a name="compare-travel-time-with-limit"></a>이동 시간을 제한과 비교

1. 이전 작업에서 **+ 새 단계** > **조건 추가**를 선택합니다. 

2. ```If travel time exceeds limit``` 설명이 포함되도록 조건 이름을 바꿉니다.

3. 여기에 설명된 것처럼 **travelTime**이 지정된 제한을 초과하는지 확인하는 조건을 작성합니다.

   1. 조건 내부에서 왼쪽(와이드 브라우저 보기인 경우) 또는 위쪽(좁은 브라우저 보기인 경우)에 있는 **값 선택** 상자의 내부를 클릭합니다.

   2. 동적 콘텐츠 목록 또는 매개 변수 목록에서 **변수** 아래에 있는 **travelTime** 필드를 선택합니다.

   3. 비교 상자에서 **보다 큼** 연산자를 선택합니다.

   4. 오른쪽(와이드 보기인 경우) 또는 아래쪽(좁은 보기인 경우)에 있는 **값 선택** 상자에서 제한으로 ```15```를 입력합니다.

   예를 들어 좁은 보기로 작업하는 경우 다음과 같은 방법으로 이 조건을 작성합니다.

   ![좁은 보기에서 조건 작성](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. 논리 앱을 저장합니다.

다음으로, 이동 시간이 제한을 초과할 때 수행할 작업을 추가합니다.

## <a name="send-email-when-limit-exceeded"></a>제한을 초과하면 이메일 발송

이제 이동 시간이 제한을 초과하면 이메일을 보내는 작업을 추가합니다. 이 이메일에는 현재 이동 시간 및 지정된 경로로 이동하는 데 필요한 추가 시간이 포함됩니다. 

1. 조건의 **true인 경우** 분기에서 **작업 추가**를 선택합니다.

2. "이메일 보내기"를 검색하고, 사용할 이메일 커넥터와 "이메일 보내기 작업"을 선택합니다.

   !["이메일 보내기" 작업을 찾아서 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Microsoft 개인 계정인 경우 **Outlook.com**을 선택합니다. 
   * Azure 회사 또는 학교 계정인 경우 **Office 365 Outlook**을 선택합니다.

3. 연결이 없는 경우 이메일 계정에 로그인하라는 메시지가 표시됩니다.

   Logic Apps는 전자 메일 계정에 대한 연결을 만듭니다.

4. ```Send email with travel time``` 설명이 포함되도록 작업 이름을 바꿉니다.

5. **받는 사람** 상자에 받는 사람의 이메일 주소를 입력합니다. 테스트를 위해 자신의 이메일 주소를 사용합니다.

6. **제목** 상자에서 이메일 제목을 지정하고, **travelTime** 변수를 포함합니다.

   1. ```Current travel time (minutes): ``` 텍스트를 입력하고 후행 공백을 하나 둡니다. 
   
   2. 매개 변수 목록 또는 동적 콘텐츠 목록에서 **변수** 아래에 있는 **travelTime**을 선택합니다. 
   
      예를 들어 브라우저가 좁은 보기인 경우:

      ![제목 텍스트 및 이동 시간을 반환하는 식 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. **본문** 상자에서 이메일 본문의 내용을 지정합니다. 

   1. ```Add extra travel time (minutes): ``` 텍스트를 입력하고 후행 공백을 하나 둡니다. 
   
   2. 필요한 경우 동적 콘텐츠 목록이 나타날 때까지 브라우저를 확장합니다. 
   동적 콘텐츠 목록에서 **식**을 선택합니다.

      ![이메일 본문에 대한 식 작성](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. 식 편집기에서 제한을 초과하는 시간(분)을 계산할 수 있도록 ```sub(,15)``` 식을 입력합니다.

      ![추가 이동 시간(분)을 계산하는 식 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. 왼쪽 괄호(**(**)와 쉼표(**,**) 사이의 식 내부에 커서를 놓습니다. **동적 콘텐츠**를 선택합니다.

      ![추가 이동 시간(분)을 계산하는 식을 계속 작성](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. **변수** 아래에서 **travelTime**을 선택합니다.

      ![식에 사용할 "travelTime" 필드 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. 필드에서 식 내부를 확인한 후 **확인**을 선택합니다.

      ![확인된 식이 포함된 "본문" 필드](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      이제 다음과 같이 **본문** 필드가 나타납니다.

      ![확인된 식이 포함된 "본문" 필드](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. 논리 앱을 저장합니다.

다음으로, 이 예제와 비슷하게 표시되는 논리 앱을 테스트합니다.

![완료된 논리 앱](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>논리 앱 실행

수동으로 논리 앱을 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다. 현재 이동 시간이 제한보다 낮게 유지되면 논리 앱은 아무 것도 하지 않고 기다리다가 다음 간격이 되면 다시 검사를 수행합니다.
하지만 현재 이동 시간이 제한을 초과하면 현재 이동 시간 및 제한을 초과한 시간(분)이 포함된 이메일을 받게 됩니다. 논리 앱에서 다음과 같은 이메일을 보냅니다.

![이동 시간이 포함된 이메일](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

이메일을 받지 못한 경우 이메일의 정크 폴더를 확인합니다. 이메일 정크 필터가 이러한 종류의 메일을 리디렉션할 수 있습니다. 그렇지 않으면 논리 앱이 올바르게 실행되는지 모르는 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

축하드립니다. 첫 번째 스케줄러 기반 되풀이 논리 앱을 만들고 실행하셨습니다. 

**일정 - 되풀이** 트리거를 사용하는 다른 논리 앱을 만들려면 논리 앱을 만든 후 사용할 수 있는 다음 템플릿을 살펴보세요.

* 매일 미리 알림 받기.
* 오래된 Azure Blob 삭제.
* Azure Storage 큐에 메시지 추가.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 논리 앱 및 관련 리소스가 포함된 리소스 그룹을 삭제하세요. 주 Azure 메뉴에서 **리소스 그룹**으로 이동하여 논리 앱의 리소스 그룹을 선택합니다. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 확인하고, **삭제**를 선택합니다.

!["개요" > "리소스 그룹 삭제"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 지정된 일정(평일 오전)에 따라 트래픽을 검사하고, 이동 시간이 지정된 제한을 초과하면 작업(이메일 보내기)을 수행하는 논리 앱을 만들었습니다. 지금부터는 Azure 서비스, Microsoft 서비스 및 기타 SaaS 앱을 통합하여 메일 그룹 요청을 보내 승인을 요청하는 논리 앱 빌드 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [메일 그룹 요청 관리](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
