---
title: "Microsoft Flow를 사용하여 Azure Application Insights 프로세스 자동화"
description: "Microsoft Flow를 통해 Application Insights 커넥터를 사용하여 반복 가능한 프로세스를 신속하게 자동화하는 방법을 알아봅니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: a1d2787626ed8fa71e3e4e9921ffb8a4680014cb
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow용 커넥터를 사용하여 Azure Application Insights 프로세스 자동화

서비스가 제대로 작동하는지 확인하기 위해 원격 분석 데이터에 대해 같은 쿼리를 반복적으로 실행하고 있나요? 추세와 비정상을 찾기 위해 이러한 쿼리를 자동화하고 여기에 관련된 고유한 워크플로를 빌드하기를 원하나요? Microsoft Flow용 Azure Application Insights 커넥터(미리 보기)가 이러한 용도에 적합한 도구입니다.

이 통합 덕분에 이제 코드 한 줄 작성하지 않고도 수많은 프로세스를 자동화할 수 있습니다. Application Insights 작업을 사용하여 흐름을 만들고 나면 이 흐름에서 Application Insights Analytics 쿼리가 자동으로 실행됩니다. 

작업을 더 추가할 수도 있습니다. Microsoft Flow는 수백 개의 작업을 사용할 수 있게 해줍니다. 예를 들어 Microsoft Flow를 사용하여 자동으로 메일 알림을 보내거나 Visual Studio Team Services에서 버그를 만들 수 있습니다. Microsoft Flow용 커넥터에 사용할 수 있는 많은 [템플릿](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) 중 하나를 사용할 수 있습니다. 이러한 템플릿을 사용하면 흐름 만들기 프로세스를 빠르게 진행할 수 있습니다. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Application Insights 흐름 만들기

이 자습서에서는 Analytics autocluster 알고리즘을 사용하여 웹 응용 프로그램에 대한 데이터에서 특성을 그룹화하는 흐름을 만드는 방법을 알아봅니다. 흐름은 자동으로 메일을 통해 결과를 보내며, 이는 Microsoft Flow 및 Application Insights Analytics를 함께 사용하는 방법의 한 가지 예일 뿐입니다. 

### <a name="step-1-create-a-flow"></a>1단계: 흐름 만들기
1. [Microsoft Flow](http://flow.microsoft.com)에 로그인한 다음 **My Flows**(내 흐름)를 선택합니다.
2. **빈 곳에서 흐름 만들기**를 클릭합니다.

### <a name="step-2-create-a-trigger-for-your-flow"></a>2단계: 흐름에 대한 트리거 만들기
1. **일정**을 선택한 다음, **일정 - 되풀이**를 선택합니다.
2. **빈도** 상자에서 **일**을 선택하고 **간격** 상자에 **1**을 입력합니다.

    ![Microsoft Flow 트리거 대화 상자](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>3단계: Application Insights 작업 추가
1. **새 단계**를 클릭한 다음 **작업 추가**를 클릭합니다.
2. **Azure Application Insights**를 검색합니다.
3. **Azure Application Insights – Analytics 쿼리 시각화 미리 보기**를 클릭합니다.

    ![Analytics 쿼리 실행 창](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4단계: Application Insights 리소스에 연결

이 단계를 완료하려면 리소스의 응용 프로그램 ID 및 API 키가 필요합니다. 다음 다이어그램에 표시된 것처럼 Azure Portal에서 리소스의 응용 프로그램 ID 및 API 키를 검색할 수 있습니다.

![Azure Portal의 응용 프로그램 ID](./media/app-insights-automate-with-flow/appid.png) 

- 응용 프로그램 ID 및 API 키와 함께 연결의 이름을 제공합니다.

    ![Microsoft Flow 연결 창](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5단계: Analytics 쿼리 및 차트 종류 지정
이 예제 쿼리에서는 어제 중에 실패한 요청을 선택하여 작업의 일부로 발생한 예외와 상관 관계를 지정합니다. Analytics에서는 operation_Id 식별자를 기반으로 하여 상관 관계를 지정합니다. 그런 다음 쿼리는 autocluster 알고리즘을 사용하여 결과를 세그먼트화합니다. 

사용자 고유의 쿼리를 만드는 경우 이러한 쿼리를 흐름에 추가하기 전에 먼저 Analytics에서 제대로 작동하는지 확인합니다.

- 다음 Analytics 쿼리를 추가하고 HTML 테이블 차트 종류를 선택합니다. 

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
    
    ![Analytics 쿼리 구성 창](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>6단계: 메일을 보내도록 흐름 구성

1. **새 단계**를 클릭한 다음 **작업 추가**를 클릭합니다.
2. **Office 365 Outlook**을 검색합니다.
3. **Office 365 Outlook – 메일 보내기**를 클릭합니다.

    ![Office 365 Outlook 선택 창](./media/app-insights-automate-with-flow/flow2b.png)

4. **메일 보내기** 창에서 다음을 수행합니다.

   a. 받는 사람의 이메일 주소를 입력합니다.

   b. 이메일의 제목을 입력합니다.

   c. **본문** 상자의 임의의 위치를 클릭한 다음, 오른쪽에서 열리는 동적 콘텐츠 메뉴에서 **본문**을 선택합니다.

   d. **고급 옵션 표시**를 클릭합니다.

    ![Office 365 Outlook 구성](./media/app-insights-automate-with-flow/flow5.png)

5. 동적 콘텐츠 메뉴에서 다음을 수행합니다.

    a. **첨부 파일 이름**을 선택합니다.

    b. **첨부 파일 콘텐츠**를 선택합니다.
    
    c. **HTML임** 상자에서 **예**를 선택합니다.

    ![Office 365 메일 구성 창](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>7단계: 흐름 저장 및 테스트
- **흐름 이름** 상자에 흐름 이름을 추가하고 **흐름 만들기**를 클릭합니다.

    ![흐름 만들기 창](./media/app-insights-automate-with-flow/flow8.png)

트리거가 이 작업을 실행하도록 기다리거나 [요청 시 트리거를 실행](https://flow.microsoft.com/blog/run-now-and-six-more-services/)하여 흐름을 즉시 실행할 수 있습니다.

흐름이 실행되면 메일 목록에 지정한 받는 사람이 다음과 같은 메일 메시지를 받습니다.

![샘플 메일](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>다음 단계

- [Analytics 쿼리](app-insights-analytics-using.md) 만들기에 대해 자세히 알아봅니다.
- [Microsoft Flow](https://ms.flow.microsoft.com)에 대해 자세히 알아봅니다.



<!--Link references-->





