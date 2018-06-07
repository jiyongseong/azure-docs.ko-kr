---
title: "Azure Application Insights에서 경고 보내기 | Microsoft Docs"
description: "Azure Application Insights를 사용하여 응용 프로그램에서 오류에 대한 응답으로 경고를 보내는 자습서입니다."
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 39e2f136e30ebb6dcfc003c435382f3384af1052
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Azure Application Insights를 사용하여 응용 프로그램 상태에 대한 모니터링 및 경고

Azure Application Insights를 사용하여 응용 프로그램을 모니터링하고 사용할 수 없거나 오류가 발생하거나 성능 문제를 겪을 때 경고를 보낼 수 있습니다.  이 자습서에서는 응용 프로그램의 가용성을 지속적으로 확인하고 검색된 문제에 대한 응답으로 서로 다른 종류의 경고를 보내도록 테스트를 만드는 과정을 단계별로 안내합니다.  다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 응용 프로그램의 응답을 지속적으로 확인하는 가용성 테스트 만들기
> * 문제가 발생한 경우 관리자에게 메일 보내기
> * 성능 메트릭을 기반으로 하는 경고 만들기 
> * 논리 앱을 사용하여 일정에 따라 요약된 원격 분석을 전송합니다.


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발
    - Azure에 .NET 응용 프로그램을 배포하고 [Application Insights SDK를 사용하도록 설정](app-insights-asp-net.md)합니다. 


## <a name="log-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-availability-test"></a>가용성 테스트 만들기
Application Insights에서 가용성 테스트를 사용하여 전 세계 여러 위치에서 응용 프로그램을 자동으로 테스트할 수 있습니다.   이 자습서에서는 응용 프로그램을 사용할 수 있도록 하는 간단한 테스트를 수행합니다.  또한 해당 세부 작업을 테스트하는 전체 연습을 만들 수도 있습니다. 

1. **Application Insights**를 선택한 다음 구독을 선택합니다.  
1. **조사** 메뉴 아래에서 **가용성**을 선택한 다음 **테스트 추가**를 클릭합니다.
 
    ![가용성 테스트 추가](media/app-insights-tutorial-alert/add-test.png)

2. 테스트에 대한 이름을 입력하고 다른 기본값을 그대로 둡니다.  5분마다 5개의 다른 지리적 위치에서 응용 프로그램의 홈페이지를 요청합니다. 
3. **경고**를 선택하여 테스트가 실패하는 경우 대처 방법에 대한 세부 정보를 정의할 수 있는 **경고** 패널을 엽니다. 경고 조건이 충족할 때 전송할 전자 메일 주소를 입력합니다.  경고 조건이 충족할 때 호출할 웹후크의 주소에 선택적으로 입력할 수 있습니다.

    ![테스트 만들기](media/app-insights-tutorial-alert/create-test.png)
 
4. 테스트 패널로 돌아가고 몇 분 후에 가용성 테스트에서 결과를 보기 시작합니다.  테스트 이름을 클릭하여 각 위치에서 세부 정보를 봅니다.  분산형 차트에서는 각 테스트의 성공 및 기간을 보여 줍니다.

    ![테스트 세부 정보](media/app-insights-tutorial-alert/test-details.png)

5.  분산형 차트의 해당 점을 클릭하여 특정 테스트의 세부 정보로 드릴다운할 수 있습니다.  아래 예제에서는 실패한 요청에 대한 세부 정보를 보여 줍니다.

    ![테스트 결과](media/app-insights-tutorial-alert/test-result.png)
  
6. 경고 조건이 충족되면 아래에 있는 것과 유사한 메일이 지정한 주소로 전송됩니다.

    ![경고 메일](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>메트릭에서 경고 만들기
가용성 테스트에서 경고를 보내는 것 이외에도 응용 프로그램에 대해 수집되는 모든 성능 메트릭에서 경고를 만들 수 있습니다.

2. **구성** 메뉴에서 **경고**를 선택합니다.  Azure Alerts 패널이 열립니다.  다른 서비스에 대해 여기에서 구성된 다른 경고 규칙이 있을 수 있습니다.
3. **메트릭 경고 추가**를 클릭합니다.  새 경고 규칙을 만드는 패널이 열립니다.

    ![메트릭 경고 추가](media/app-insights-tutorial-alert/add-metric-alert.png)

4. 경고 규칙에 대한 **이름**을 입력하고 **리소스**에 대한 드롭다운에서 응용 프로그램을 선택합니다.
5. 샘플링할 **메트릭**을 선택합니다.  지난 24시간 동안 이 요청의 값을 나타내는 그래프가 표시됩니다.  메트릭에 대한 조건을 설정하도록 도와줍니다.

    ![경고 규칙 추가](media/app-insights-tutorial-alert/add-alert-01.png)

6. 경고에 대한 **조건** 및 **임계값**을 지정합니다. 만들 경고에 대해 메트릭이 초과되어야 하는 횟수입니다. 
6. **다음을 통해 알림** 아래에서 **소유자, 참가자 및 구독자에게 전자 메일 보내기** 상자를 선택하여 경고 조건이 충족될 때 이러한 사용자에게 전자 메일을 보내고 추가 받는 사람의 전자 메일 주소를 추가합니다.  또한 조건이 충족될 때 실행하는 웹후크 또는 논리 앱을 여기에서 지정할 수 있습니다.  검색된 문제를 완화하는 시도에 사용됩니다. 또는 

    ![경고 규칙 추가](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>사전 정보 보내기
경고는 응용 프로그램에서 식별된 특정 문제의 집합에 대한 응답으로 만들어지며 일반적으로 즉각적인 주의가 필요한 중요한 조건에 대한 경고를 예약합니다.  일정에 따라 자동으로 실행되는 논리 앱을 사용하여 응용 프로그램에 대한 정보를 사전에 받을 수 있습니다.  예를 들어 추가 평가가 필요한 요약 정보와 함께 매일 관리자에게 메일을 전송할 수 있습니다.

Application Insights를 사용하여 논리 앱 만들기에 대한 자세한 내용은 [Logic Apps를 사용하여 Application Insights 프로세스 자동화](automate-with-logic-apps.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 문제에 대한 경고를 설정하는 방법을 배웠으므로 사용자가 응용 프로그램과 상호 작용하는 방법을 분석하는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [사용자 이해](app-insights-tutorial-users.md)