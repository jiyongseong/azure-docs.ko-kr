---
title: "Azure Application Insights를 사용하여 런타임 예외 진단 | Microsoft Docs"
description: "Azure Application Insights를 사용하여 응용 프로그램에서 런타임 예외를 찾고 진단하는 자습서입니다."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 115611c5d4eeffb0f0600dd0a792ee9f80247e36
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2018
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Azure Application Insights를 사용하여 런타임 예외 찾기 및 진단

Azure Application Insights는 응용 프로그램에서 원격 분석을 수집하여 런타임 예외를 식별하고 진단하도록 돕습니다.  이 자습서에서는 응용 프로그램을 사용하여 이 프로세스를 안내합니다.  다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 예외 추적을 활성화하도록 프로젝트를 수정
> * 응용 프로그램의 다양한 구성 요소에 대한 예외 식별
> * 예외 세부 정보 보기
> * 디버깅을 위해 Visual Studio에 예외에 대한 스냅숏 다운로드
> * 쿼리 언어를 사용하여 실패한 요청의 세부 정보 분석
> * 잘못된 코드를 수정하도록 새 작업 항목 만들기


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발
- [Visual Studio 스냅숏 디버거](http://aka.ms/snapshotdebugger)를 다운로드 및 설치합니다.
- [Visual Studio 스냅숏 디버거](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) 사용
- Azure에 .NET 응용 프로그램을 배포하고 [Application Insights SDK를 사용하도록 설정](app-insights-asp-net.md)합니다. 
- 자습서는 응용 프로그램에서 예외의 ID를 추적하여 개발 또는 테스트 환경에서 코드를 수정하여 예외를 생성합니다. 

## <a name="log-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.


## <a name="analyze-failures"></a>실패 분석
Application Insights는 응용 프로그램에서 모든 오류를 수집하고 여러 작업 간에 빈도를 볼 수 있도록 하여 가장 높은 영향으로 해당 내용에 집중할 수 있도록 돕습니다.  그런 다음 이러한 실패의 세부 정보를 드릴다운하여 근본 원인을 식별할 수 있습니다.   

1. **Application Insights**를 선택한 다음 구독을 선택합니다.  
2. **실패** 패널을 열려면 **조사** 메뉴 아래의 **실패**를 선택하거나 **실패한 요청** 그래프를 클릭합니다.

    ![실패한 요청](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

3. **실패한 요청** 패널은 실패한 요청 수 및 응용 프로그램의 각 작업에 대해 영향을 받는 사용자 수를 표시합니다.  사용자가 이 정보를 정렬하여 사용자에게 가장 큰 영향을 주는 이러한 오류를 식별할 수 있습니다.  이 예제에서는 **직원 가져오기/만들기** 및 **고객/세부 정보 가져오기**는 큰 실패 수 및 영향을 받는 사용자로 인해 조사할 후보일 가능성이 있습니다.  작업을 선택하면 이 작업에 대한 자세한 정보가 오른쪽 패널에 표시됩니다.

    ![실패한 요청 패널](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

4. 창을 축소하여 실패율이 급증을 나타내는 기간에 대해 확대합니다.

    ![실패한 요청 창](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

5. **세부 정보 보기**를 클릭하여 작업에 대한 세부 정보를 확인합니다.  이는 완료되는 데 전체적으로 거의 0.5초가 소요되는 두 개의 실패한 종속성을 보여 주는 Gantt 차트를 포함합니다.  자습서 [Azure Application Insights를 사용하여 성능 문제 찾기 및 진단](app-insights-tutorial-performance.md)을 완료하여 성능 문제 분석에 대한 자세한 내용을 확인할 수 있습니다.

    ![실패한 요청 세부 정보](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

6. 또한 작업 세부 정보는 실패를 야기한 것으로 보이는 FormatException을 보여 줍니다.  예외 또는 **상위 3개 예외 유형** 수를 클릭하여 해당 세부 정보를 볼 수 있습니다.  잘못된 우편 번호 때문임을 확인할 수 있습니다.

    ![예외 세부 정보](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)

> [!NOTE]
“통합된 세부 정보: E2E 트랜잭션 진단” [미리 보기 환경](app-insights-previews.md)을 사용하도록 설정하여 요청, 종속성, 예외, 추적, 이벤트 등과 같은 모든 관련 서버 쪽 원격 분석을 하나의 전체 화면 보기에서 확인할 수 있습니다. 

미리 보기를 사용하도록 설정하면 통합된 환경에서 오류 또는 예외와 함께 종속성 호출에 소요된 시간을 볼 수 있습니다. 구성 요소 간 트랜잭션의 경우 세부 정보 창 함께 Gantt 차트를 통해 근본 원인 구성 요소, 종속성 또는 예외를 신속하게 진단할 수 있습니다. 아래쪽 섹션을 확장하여 선택한 구성 요소 작업에 수집된 모든 추적 또는 이벤트의 시간 시퀀스를 확인할 수 있습니다. [새 환경에 대해 자세히 알아보세요](app-insights-transaction-diagnostics.md).  

![트랜잭션 진단](media/app-insights-tutorial-runtime-exceptions/e2e-transaction-preview.png)

## <a name="identify-failing-code"></a>실패 코드 식별
스냅숏 디버거는 응용 프로그램에서 가장 빈번한 예외의 스냅숏을 수집하여 프로덕션에서 해당 근본 원인을 진단하는 데 도움을 줍니다.  포털에서 디버그 스냅숏을 확인하여 호출 스택을 보고 각 호출 스택 프레임에서 변수를 검사할 수 있습니다. 그런 다음 스냅숏을 다운로드하고 Visual Studio 2017에서 열어 소스 코드를 디버깅할 수 있습니다.

1. 예외 속성에서 **디버그 스냅숏 열기**를 클릭합니다.
2. **디버그 스냅숏** 패널이 요청에 대한 호출 스택과 함께 열립니다.  메서드를 클릭하여 요청 시 모든 지역 변수의 값을 봅니다.  이 예제에서는 맨 위 메서드에서부터 시작하여 값이 없는 지역 변수를 볼 수 있습니다.

    ![디버그 스냅숏](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. 유효한 값을 가진 첫 번째 호출은 **ValidZipCode**이며 우편 번호가 정수로 변환할 수 없는 문자와 함께 제공된 것을 볼 수 있습니다.  이는 코드에서 수정되어야 하는 오류로 보입니다.

    ![디버그 스냅숏](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. 수정되어야 하는 실제 코드를 찾을 수 있는 Visual Studio로 이 스냅숏을 다운로드하려면 **스냅숏 다운로드**를 클릭합니다.
6. 스냅숏이 Visual Studio로 로드됩니다.
7. 이제 Visual Studio에서 예외를 발생시킨 코드 줄을 신속하게 식별하는 디버그 세션을 실행할 수 있습니다.

    ![코드의 예외](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>분석 데이터 사용
Application Insights에 의해 수집된 모든 데이터는 여러 가지 방법으로 데이터를 분석할 수 있도록 하는 다양한 쿼리 언어를 제공하는 Azure Log Analytics에 저장됩니다.  이 데이터를 사용하여 조사 중인 예외를 생성한 요청을 분석할 수 있습니다. 

8. 코드 위의 CodeLens 정보를 클릭하여 Application Insights에서 제공하는 원격 분석을 봅니다.

    ![코드](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. **영향 분석**을 클릭하여 Application Insights Analytics를 엽니다.  영향을 받는 사용자, 브라우저 및 지역과 같은 실패한 요청의 세부 정보를 제공하는 여러 쿼리로 채워집니다.<br><br>![분석](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>작업 항목 추가
Application Insights를 Visual Studio Team Services 또는 GitHub와 같은 추적 시스템에 연결하는 경우 Application Insights에서 직접 작업 항목을 만들 수 있습니다.

1. Application Insights의 **예외 속성** 패널로 돌아옵니다.
2. **새 작업 항목**을 클릭합니다.
3. **새 작업 항목** 패널이 이미 채워진 예외에 대한 세부 정보와 함께 열립니다.  저장하기 전에 추가 정보를 추가할 수 있습니다.

    ![새 작업 항목](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>다음 단계
이제 런타임 예외를 식별하는 방법을 배웠으므로 성능 문제를 식별하고 진단하는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [성능 문제 식별](app-insights-tutorial-performance.md)