---
title: Azure Logic Apps와 통합하는 함수 만들기 | Microsoft Docs
description: Azure Logic Apps 및 Azure Cognitive Services와 통합하는 함수를 만들어 트윗 감정을 분류하고 감정이 나쁠 경우 알림을 보냅니다.
services: functions, logic-apps, cognitive-services
keywords: 워크플로, 클라우드 앱, 클라우드 서비스, 비즈니스 프로세스, 시스템 통합, Enterprise Application Integration, EAI
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 16a46b4c49687186e25c399dcc2c5c168e7c5004
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Azure Logic Apps와 통합하는 함수 만들기

Azure Functions는 논리 앱 디자이너에서 Azure Logic Apps와 통합합니다. 이 통합을 통해 다른 Azure 및 타사 서비스와 함께 오케스트레이션에서 함수의 컴퓨팅 기능을 사용할 수 있습니다. 

이 자습서에서는 Azure의 Logic Apps 및 Microsoft Cognitive Services와 함께 함수를 사용하여 Twitter 게시물에서 감정을 분석하는 방법을 보여 줍니다. HTTP 트리거된 함수는 감정 점수를 기준으로 트윗을 녹색, 노랑 또는 빨강으로 분류합니다. 불량 감정이 감지되면 전자 메일이 전송됩니다. 

![논리 앱 디자이너에서 앱의 처음 2단계를 보여 주는 이미지](media/functions-twitter-email/designer1.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Cognitive Services API 리소스를 만듭니다.
> * 트윗 감정을 분류하는 함수를 만듭니다.
> * Twitter에 연결하는 논리 앱을 만듭니다.
> * 논리 앱에 감정 검색을 추가합니다. 
> * 함수에 논리 앱을 연결합니다.
> * 함수의 응답에 따라 전자 메일을 보냅니다.

## <a name="prerequisites"></a>필수 조건

+ 활성 [Twitter](https://twitter.com/) 계정. 
+ [Outlook.com](https://outlook.com/) 계정(알림 전송용).
+ 이 항목에서는 [Azure Portal에서 첫 번째 함수 만들기](functions-create-first-azure-function.md)에서 만든 리소스를 시작점으로 사용합니다.  
아직 만들지 않았다면 지금 이러한 단계를 수행하여 함수 앱을 만듭니다.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

Cognitive Services API는 Azure에서 개별 리소스로 사용할 수 있습니다. 텍스트 분석 API를 사용하여 모니터링 중인 트윗의 감정을 검색합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

3. **AI + Analytics** > **텍스트 분석 API**를 클릭합니다. 그런 다음 테이블에 지정된 설정을 사용하고 약관에 동의하고 **대시보드에 고정**을 선택합니다.

    ![Cognitive 리소스 만들기 페이지](media/functions-twitter-email/cog_svcs_resource.png)

    | 설정      |  제안 값   | 설명                                        |
    | --- | --- | --- |
    | **Name** | MyCognitiveServicesAccnt | 고유한 계정 이름을 선택합니다. |
    | **위치**: | 미국 서부 | 가장 가까운 위치를 사용합니다. |
    | **가격 책정 계층** | F0 | 가장 낮은 계층으로 시작합니다. 호출에서 실행하는 경우 더 높은 계층으로 확장합니다.|
    | **리소스 그룹** | myResourceGroup | 이 자습서에서 모든 서비스에 대해 동일한 리소스 그룹을 사용합니다.|

4. **만들기**를 클릭하여 리소스를 만듭니다. 만들어진 후에 대시보드에 고정된 새로운 Cognitive Services 리소스를 선택합니다. 

5. 왼쪽 탐색 열에서 **키**를 클릭한 다음 **키1**의 값을 복사하고 저장합니다. 이 키를 사용하여 논리 앱을 Cognitive Services API에 연결합니다. 
 
    ![구성](media/functions-twitter-email/keys.png)

## <a name="create-the-function-app"></a>함수 앱 만들기

함수는 논리 앱 워크플로에서 처리 작업을 오프로드하는 훌륭한 방법을 제공합니다. 이 자습서는 HTTP 트리거된 함수를 사용하여 Cognitive Services에서 트윗 감정 점수를 처리하고 범주 값을 반환합니다.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>HTTP 트리거 함수 만들기  

1. 함수 앱을 확장한 후 **함수** 옆의 **+** 단추를 클릭합니다. 함수 앱에서 첫 번째 함수이면 **사용자 지정 함수**를 선택합니다. 그러면 함수 템플릿의 전체 집합이 표시됩니다.

    ![Azure Portal에서 함수 빨리 시작하기 페이지](media/functions-twitter-email/add-first-function.png)

2. 검색 필드에 `http`를 입력한 다음, HTTP 트리거 템플릿에 대해 **C#**을 선택합니다. 

    ![HTTP 트리거 선택](./media/functions-twitter-email/select-http-trigger-portal.png)

3. 함수의 **이름**을 입력하고 **[인증 수준](functions-bindings-http-webhook.md#http-auth)**에 대해 `Function`을 선택한 후 **만들기**를 선택합니다. 

    ![HTTP 트리거 함수 만들기](./media/functions-twitter-email/select-http-trigger-portal-2.png)

    이는 HTTP 트리거 템플릿을 사용하여 C# 스크립트 함수를 만듭니다. 코드가 새 창에 `run.csx`로 표시됩니다.

4. `run.csx` 파일의 콘텐츠를 다음 코드로 바꾼 다음 **저장**을 클릭합니다.

    ```csharp
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
        if (score < .3)
        {
            category = "RED";
        }
        else if (score < .6)
        {
            category = "YELLOW";
        }
        return req.CreateResponse(HttpStatusCode.OK, category);
    }
    ```
    이 함수 코드는 요청에서 받은 감정 점수를 기준으로 색 범주를 반환합니다. 

4. 함수를 테스트하려면 오른쪽 끝의 **테스트**를 클릭하여 테스트 탭을 확장합니다. **요청 본문**에 `0.2` 값을 입력한 다음 **실행**을 클릭합니다. 응답의 본문에 **빨강** 값이 반환됩니다. 

    ![Azure Portal에서 함수 테스트](./media/functions-twitter-email/test.png)

이제 감정 점수를 분류하는 함수가 있습니다. 다음으로 Twitter 및 Cognitive Services API와 함수를 통합하는 논리 앱을 만듭니다. 

## <a name="create-a-logic-app"></a>논리 앱 만들기   

1. Azure Portal에서 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **엔터프라이즈 통합** > **논리 앱**을 클릭합니다. 그런 다음 테이블에 지정된 설정을 사용하고 **대시보드에 고정**을 선택하고 **만들기**를 클릭합니다.
 
4. 그런 다음 `TweetSentiment`와 같이 **이름**을 입력하고 테이블에 지정된 설정을 사용하고 약관에 동의하고 **대시보드에 고정**을 선택합니다.

    ![Azure Portal에서 논리 앱 만들기](./media/functions-twitter-email/new_logic_app.png)

    | 설정      |  제안 값   | 설명                                        |
    | ----------------- | ------------ | ------------- |
    | **Name** | TweetSentiment | 앱에 적절한 이름을 선택합니다. |
    | **리소스 그룹** | myResourceGroup | 이전과 동일한 기존 리소스 그룹을 선택합니다. |
    | **위치**: | 미국 동부 | 가까운 위치를 선택합니다. |    

4. **대시보드에 고정**을 선택한 다음 **만들기**를 클릭하여 논리 앱을 만듭니다. 

5. 앱이 만들어지면 대시보드에 고정된 새 논리 앱을 클릭합니다. 그런 다음 Logic Apps 디자이너에서 아래로 스크롤하고 **빈 논리 앱** 템플릿을 클릭합니다. 

    ![빈 Logic Apps 템플릿](media/functions-twitter-email/blank.png)

이제 Logic Apps 디자이너를 사용하여 App Service 및 트리거를 앱에 추가할 수 있습니다.

## <a name="connect-to-twitter"></a>Twitter에 연결

먼저 Twitter 계정에 대한 연결을 만듭니다. 논리 앱은 앱이 실행되도록 트리거하는 트윗에 대해 폴링합니다.

1. 디자이너에서 **Twitter** 서비스를 클릭하고 **새 트윗이 게시된 경우** 트리거를 클릭합니다. Twitter 계정에 로그인하고 계정을 사용하도록 Logic Apps에 권한을 부여합니다.

2. 표에 지정된 것처럼 Twitter 트리거 설정을 사용합니다. 

    ![Twitter 커넥터 설정](media/functions-twitter-email/azure_tweet.png)

    | 설정      |  제안 값   | 설명                                        |
    | ----------------- | ------------ | ------------- |
    | **검색 텍스트** | #Azure | 선택한 간격으로 새 트윗을 생성할 만큼 충분히 인기 있는 해시태그를 사용합니다. 무료 계층을 사용하고 사용자 해시태그가 너무 많이 사용되면 Cognitive Services API에서 트랜잭션 할당량을 빠르게 사용할 수 있습니다. |
    | **Frequency(빈도)** | 분 | Twitter 폴링에 사용되는 빈도 단위입니다.  |
    | **간격** | 15 | 빈도 단위에서 Twitter 요청 간 경과된 시간입니다. |

3.  **저장**을 클릭하여 Twitter 계정에 연결합니다. 

이제 앱이 Twitter에 연결되었습니다. 다음으로 텍스트 분석에 연결하여 수집된 트윗의 감정을 검색합니다.

## <a name="add-sentiment-detection"></a>감정 검색 추가

1. **새 단계**를 클릭한 다음 **작업 추가**를 선택합니다.

    ![새 단계 후 작업 추가](media/functions-twitter-email/new_step.png)

2. **작업 선택**에서 **텍스트 분석**을 클릭한 다음 **감정 검색** 작업을 클릭합니다.

    ![감정 검색](media/functions-twitter-email/detect_sent.png)

3. `MyCognitiveServicesConnection`과 같은 연결 이름을 입력하고 Cognitive Services API에 저장한 키를 붙여 넣고 **만들기**를 클릭합니다.  

4. **분석할 텍스트** > **트윗 텍스트**를 클릭한 다음 **저장**을 클릭합니다.  

    ![감정 검색](media/functions-twitter-email/ds_tta.png)

감정 검색을 구성했으므로 감정 점수 출력을 사용하는 함수에 연결을 추가할 수 있습니다.

## <a name="connect-sentiment-output-to-your-function"></a>함수에 감정 출력 연결

1. Logic Apps 디자이너에서 **새 단계** > **작업 추가**를 클릭한 다음 **Azure Functions**를 클릭합니다. 

2. **Azure 함수 선택**을 클릭하고 앞에서 만든 **CategorizeSentiment** 함수를 선택합니다.  

    ![Azure 함수 선택을 보여 주는 Azure 함수 상자](media/functions-twitter-email/choose_fun.png)

3. **요청 본문**에서 **점수**, **저장**을 차례로 클릭합니다.

    ![Score](media/functions-twitter-email/trigger_score.png)

이제 논리 앱에서 감정 점수를 보낼 때 함수가 트리거됩니다. 색으로 지정된 범주는 함수에 의해 논리 앱에 반환됩니다. 다음으로 **빨강** 감정 값이 함수에서 반환될 때 전송되는 전자 메일 알림을 추가합니다. 

## <a name="add-email-notifications"></a>전자 메일 알림 추가

워크플로의 마지막 부분은 감정이 _빨강_으로 점수가 매겨질 때 전자 메일을 트리거하는 것입니다. 이 항목에서는 Outlook.com 커넥터를 사용합니다. 비슷한 단계를 수행하여 Gmail 또는 Office 365 Outlook 커넥터를 사용할 수 있습니다.   

1. Logic Apps 디자이너에서 **새 단계** > **조건 추가**를 클릭합니다. 

2. **값 선택**을 클릭한 다음 **본문**을 클릭합니다. **같음**을 선택하고 **값 선택**을 클릭하고 `RED`를 입력하고 **저장**을 클릭합니다. 

    ![논리 앱에 조건을 추가합니다.](media/functions-twitter-email/condition.png)

3. **True인 경우**에서 **작업 추가**를 클릭하고, `outlook.com`을 검색하고, **전자 메일 보내기**를 클릭하고, Outlook.com 계정에 로그인합니다.
    
    ![조건에 대한 작업을 선택합니다.](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Outlook.com 계정이 없는 경우 Gmail 또는 Office 365 Outlook과 같은 다른 커넥터를 선택할 수 있습니다.

4. **전자 메일 보내기** 작업에서 테이블에 지정된 대로 전자 메일 설정을 사용합니다. 

    ![전자 메일 보내기 작업에 대한 전자 메일을 구성합니다.](media/functions-twitter-email/send_email.png)

    | 설정      |  제안 값   | 설명  |
    | ----------------- | ------------ | ------------- |
    | **To** | 메일 주소 입력 | 알림을 받는 전자 메일 주소입니다. |
    | **제목** | 부정적인 트윗 감정 검색  | 전자 메일 알림의 제목 줄입니다.  |
    | **본문** | 트윗 텍스트, 위치 | **트윗 텍스트** 및 **위치** 매개 변수를 클릭합니다. |

5.  **저장**을 클릭합니다.

이제 워크플로를 완료했으므로 논리 앱을 활성화하고 작업 시 함수를 확인할 수 있습니다.

## <a name="test-the-workflow"></a>워크플로 테스트

1. 논리 앱 디자이너에서 **실행**을 클릭하여 앱을 시작합니다.

2. 왼쪽 열에서 **개요**를 클릭하여 논리 앱의 상태를 봅니다. 
 
    ![논리 앱 실행 상태](media/functions-twitter-email/over1.png)

3. (선택 사항) 실행 중 하나를 클릭하여 실행의 세부 정보를 봅니다.

4. 함수로 이동하고 로그를 보고 감정 값이 수신 및 처리되었는지 확인합니다.
 
    ![함수 로그 보기](media/functions-twitter-email/sent.png)

5. 잠재적으로 부정적인 감정이 검색되면 전자 메일을 수신합니다. 전자 메일을 수신하지 않은 경우 항상 빨강을 반환하도록 함수 코드를 변경할 수 있습니다.

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    전자 메일 알림을 확인한 후 원래 코드로 다시 변경합니다.

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > 이 자습서를 완료 한 후 논리 앱을 비활성화해야 합니다. 앱을 비활성화하여 Cognitive Services API에서 실행에 대한 요금 부과 및 트랜잭션 소모를 방지할 수 있습니다.

이제 Logic Apps 워크플로로 함수를 통합하는 것이 얼마나 쉬운지 살펴보았습니다.

## <a name="disable-the-logic-app"></a>논리 앱 비활성화

논리 앱을 비활성화하려면 **개요** 클릭한 다음 화면 맨 위의 **비활성화**를 클릭합니다. 그러면 앱을 삭제하지 않고 논리 앱의 실행 및 요금 청구를 중지합니다. 

![함수 로그](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Cognitive Services API 리소스를 만듭니다.
> * 트윗 감정을 분류하는 함수를 만듭니다.
> * Twitter에 연결하는 논리 앱을 만듭니다.
> * 논리 앱에 감정 검색을 추가합니다. 
> * 함수에 논리 앱을 연결합니다.
> * 함수의 응답에 따라 전자 메일을 보냅니다.

함수에 대한 서버 없는 API를 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"] 
> [Azure Functions를 사용하여 서버 없는 API 만들기](functions-create-serverless-api.md)

Logic Apps에 대해 자세히 알아보려면 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 참조하세요.

