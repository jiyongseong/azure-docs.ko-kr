---
title: "Logic Apps를 사용하여 Azure Application Insights 프로세스를 자동화합니다."
description: "논리 앱에 Application Insights 커넥터를 추가하여 반복 가능한 프로세스를 신속하게 자동화하는 방법을 알아봅니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: e17d8076a00cab2cf608fe1a690e4a780a69d56f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Logic Apps를 사용하여 Application Insights 프로세스 자동화

서비스가 제대로 작동하는지 확인하기 위해 원격 분석 데이터에 대해 같은 쿼리를 반복적으로 실행하고 있나요? 추세와 비정상을 찾기 위해 이러한 쿼리를 자동화하고 여기에 관련된 고유한 워크플로를 빌드하기를 원하나요? Logic Apps용 Azure Application Insights 커넥터(미리 보기)가 이러한 용도에 적합한 도구입니다.

이 통합 덕분에 코드를 전혀 작성하지 않고도 수많은 프로세스를 자동화할 수 있습니다. Application Insights 커넥터로 논리 앱을 만들어 Application Insights 프로세스를 신속하게 자동화할 수 있습니다. 

작업을 더 추가할 수도 있습니다. Azure App Service의 Logic Apps를 사용하면 수백 개의 작업을 수행할 수 있습니다. 예를 들어 논리 앱을 사용하여 이메일 알림을 자동으로 보내거나 Visual Studio Team Services에서 버그를 만들 수 있습니다. 여러 사용 가능한 [템플릿](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) 중 하나를 사용하여 더 신속하게 논리 앱을 만들 수도 있습니다. 

## <a name="create-a-logic-app-for-application-insights"></a>Application Insights에 대한 논리 앱 만들기

이 자습서에서는 Analytics 자동 클러스터 알고리즘을 사용하여 웹 응용 프로그램에 대한 데이터에서 특성을 그룹화하는 논리 앱을 만드는 방법을 알아봅니다. 흐름은 자동으로 메일을 통해 결과를 보내며, 이는 Application Insights Analytics와 Logic Apps를 함께 사용하는 방법의 한 가지 예일 뿐입니다. 

### <a name="step-1-create-a-logic-app"></a>1단계: 논리 앱 만들기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 만들기**를 클릭하고 **웹 + 모바일**을 선택한 다음, **논리 앱**을 선택합니다.

    ![새 논리 앱 창](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>2단계: 논리 앱에 대한 트리거 만들기
1. **논리 앱 디자이너** 창의 **일반적인 트리거로 시작**에서 **되풀이**를 선택합니다.

    ![논리 앱 디자이너 창](./media/automate-with-logic-apps/logicapp2.png)

2. **빈도** 상자에서 **일**을 선택하고 **간격** 상자에 **1**을 입력합니다.

    ![논리 앱 디자이너 "되풀이" 창](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>3단계: Application Insights 작업 추가
1. **새 단계**를 클릭한 다음 **작업 추가**를 클릭합니다.

2. **작업 선택** 검색 상자에서 **Azure Application Insights**를 입력합니다.

3. **작업**에서 **Azure Application Insights – Analytics 쿼리 시각화 미리 보기**를 클릭합니다.

    ![논리 앱 디자이너 "작업 선택" 창](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4단계: Application Insights 리소스에 연결

이 단계를 완료하려면 리소스의 응용 프로그램 ID 및 API 키가 필요합니다. 다음 다이어그램에 표시된 것처럼 Azure Portal에서 리소스의 응용 프로그램 ID 및 API 키를 검색할 수 있습니다.

![Azure Portal의 응용 프로그램 ID](./media/automate-with-logic-apps/appid.png) 

연결의 이름, 응용 프로그램 ID 및 API 키를 제공합니다.

![논리 앱 디자이너 흐름 연결 창](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5단계: Analytics 쿼리 및 차트 종류 지정
다음 예제에서는 쿼리가 전날 실패한 요청을 선택하여 작업의 일부로 발생한 예외와 상관 관계를 지정합니다. Analytics에서는 operation_Id 식별자를 기반으로 하여 실패한 요청에 상관 관계를 지정합니다. 그런 다음 쿼리는 autocluster 알고리즘을 사용하여 결과를 세그먼트화합니다. 

사용자 고유의 쿼리를 만드는 경우 이러한 쿼리를 흐름에 추가하기 전에 먼저 Analytics에서 제대로 작동하는지 확인합니다.

1. **쿼리** 상자에서 다음 Analytics 쿼리를 추가합니다. 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

2. **차트 종류** 상자에서 **Html 테이블**을 선택합니다.

    ![Analytics 쿼리 구성 창](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>6단계: 논리 앱이 이메일을 보내도록 구성

1. **새 단계**를 클릭한 다음 **작업 추가**를 선택합니다.

2. 검색 상자에 **Office 365 Outlook**을 입력합니다.

3. **Office 365 Outlook - 이메일 보내기**를 클릭합니다.

    ![Office 365 Outlook 선택](./media/automate-with-logic-apps/flow2b.png)

4. **이메일 보내기** 창에서 다음을 수행합니다.

   a. 받는 사람의 이메일 주소를 입력합니다.

   나. 이메일의 제목을 입력합니다.

   다. **본문** 상자의 임의의 위치를 클릭한 다음, 오른쪽에서 열리는 동적 콘텐츠 메뉴에서 **본문**을 선택합니다.

   d. **고급 옵션 표시**를 클릭합니다.

      ![Office 365 Outlook 구성](./media/automate-with-logic-apps/flow5.png)

5. 동적 콘텐츠 메뉴에서 다음을 수행합니다.

    a. **첨부 파일 이름**을 선택합니다.

    나. **첨부 파일 콘텐츠**를 선택합니다.
    
    다. **HTML임** 상자에서 **예**를 선택합니다.

      ![Office 365 메일 구성 화면](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>7단계: 논리 앱 저장 및 테스트
* **저장**을 클릭하여 변경 내용을 저장합니다.

트리거가 논리 앱을 실행할 때까지 기다리거나, **실행**을 선택하여 즉시 논리 앱을 실행할 수 있습니다.

![논리 앱 만들기 화면](./media/automate-with-logic-apps/step7.png)

논리 앱이 실행되면 메일 목록에 지정한 받는 사람이 다음과 같은 메일을 받습니다.

![논리 앱 이메일 메시지](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>다음 단계

- [Analytics 쿼리](app-insights-analytics-using.md) 만들기에 대해 자세히 알아봅니다.
- [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)에 대해 자세히 알아봅니다.



<!--Link references-->





