---
title: Azure Service Bus-Event Grid 통합 예제 | Microsoft Docs
description: 이 아티클에서는 Service Bus 메시지 및 Event Grid 통합 예제를 제공합니다.
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 5d0ab8cf9e87fc13b78b00dbe77ec6f9fb38c4b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Azure Service Bus-Azure Event Grid 통합 예제

이 아티클에서는 Azure Event Grid의 이벤트를 기반으로 메시지를 수신하는 Azure 함수 및 논리 앱을 설정하는 방법을 배웁니다. 다음을 수행합니다.
 
* Event Grid의 이벤트 초기 흐름을 디버깅하고 살펴볼 간단한 [테스트 Azure 함수](#test-function-setup)를 만듭니다. 다른 단계 수행 여부에 관계 없이 이 단계를 수행 합니다.
* Event Grid 이벤트를 기반으로 [Azure Service Bus 메시지를 받아서 처리하는 Azure 함수](#receive-messages-using-azure-function)를 만듭니다.
* [Azure App Service의 Logic Apps 기능](#receive-messages-using-azure-logic-app)을 활용합니다.

만든 예제에서는 Service Bus 항목에 두 개의 구독이 있다고 가정합니다. 또한 이 예제에서는 Event Grid 구독이 하나의 Service Bus 구독에 대해서만 이벤트를 보내도록 만들어졌다고 가정합니다. 

예제에서 Service Bus 항목에 메시지를 보낸 다음, Service Bus 구독에 대한 이벤트가 생성됐는지 확인 합니다. 함수 또는 논리 앱은 Service Bus 구독에서 메시지를 받아 다음 작업을 완료합니다.

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음 두 섹션의 단계를 완료했는지 확인합니다.

### <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

Service Bus 프리미엄 네임스페이스를 만들고 두 개의 구독이 있는 Service Bus 항목을 만듭니다.

### <a name="send-a-message-to-the-service-bus-topic"></a>Service Bus 항목으로 메시지 보내기

모든 메서드를 사용하여 Service Bus 토픽에 메시지를 보낼 수 있습니다. 이 프로시저의 끝에 샘플 코드에서는 Visual Studio 2017을 사용하는 것으로 가정합니다.

1. [ GitHub azure-service-bus 리포지토리](https://github.com/Azure/azure-service-bus/)를 복제합니다.

2. Visual Studio에서 *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* 폴더로 이동하여 *SBEventGridIntegration.sln* 파일을 엽니다.

3. **MessageSender** 프로젝트로 이동한 다음, **Program.cs**를 선택합니다.

   ![8][]

4. 토픽 이름과 연결 문자열을 입력하고 다음 콘솔 응용 프로그램 코드를 실행합니다.

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>테스트 함수 설정

시나리오 전체를 수행하려면 적어도 현재 진행 중인 이벤트를 디버그하고 살펴볼 수 있는 작은 테스트 함수가 필요합니다.

1. Azure Portal에서 새 Azure Functions 응용 프로그램을 만듭니다. Azure 함수의 기본 사항을 알아보려면 [Azure Functions 설명서](https://docs.microsoft.com/azure/azure-functions/)를 참조합니다.

2. 새로 만든 함수에서 더하기 기호(+)를 선택하여 HTTP 트리거 함수를 추가합니다.

    ![2][]
    
    **미리 만들어진 함수로 빠르게 시작** 창이 열립니다.

    ![3][]

3. **웹후크 + API** 단추, **CSharp**, **이 함수 만들기**를 차례로 선택합니다.
 
4. 함수에 다음 코드를 붙여 넣습니다.

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
    IEnumerable<string> headerValues;
    if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
    {
    var validationHeaderValue = headerValues.FirstOrDefault();
    if(validationHeaderValue == "SubscriptionValidation")
    {
    var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
         var code = events[0].Data["validationCode"];
         return req.CreateResponse(HttpStatusCode.OK,
         new { validationResponse = code });
    }
    }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```

5. **저장 및 실행**을 선택합니다.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Event Grid를 통해 함수와 네임스페이스 연결

이 섹션에서 함수와 Service Bus 네임스페이스를 연결합니다. 이 예에서는 Azure Portal을 사용합니다. 이 프로시저를 수행하기 위해 PowerShell 또는 Azure CLI를 사용하는 방법을 알아보려면 [Azure Service Bus-Azure Event Grid 통합 개요](service-bus-to-event-grid-integration-concept.md)를 참조합니다.

Azure Event Grid 구독을 만들려면 다음을 수행합니다.
1. Azure Portal에서 네임스페이스로 이동한 다음, 왼쪽 창에서 **Event Grid**를 선택합니다.  
    오른쪽 창에 두 개의 Event Grid 구독이 표시되며 네임스페이스 창이 열립니다.

    ![20][]

2. **이벤트 구독**을 선택합니다.  
    **이벤트 구독** 창이 열립니다. 다음 이미지에서는 필터를 적용하지 않고 Azure 함수나 웹후크 구독에 대한 양식을 표시 합니다.

    ![21][]

3. 표시된 것처럼 양식을 작성하고 **접미사 필터** 상자에서 관련 필터를 입력해야 합니다.

4. **만들기**를 선택합니다.

5. “필수 구성 요소” 섹션에서 설명했듯이 Service Bus 토픽으로 메시지를 보내고 Azure Functions 모니터링 기능을 통해 이벤트가 흐르고 있는지 확인합니다.

그 다음 단계는 함수와 Service Bus 네임스페이스를 연결하는 것입니다. 이 예에서는 Azure Portal을 사용합니다. 이 단계를 수행하기 위해 PowerShell 또는 Azure CLI를 사용하는 방법을 알아보려면 [Azure Service Bus-Azure Event Grid 통합 개요](service-bus-to-event-grid-integration-concept.md)를 참조합니다.

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Azure Functions를 사용하여 메시지 받기

이전 섹션에서 간단한 테스트 및 디버깅 시나리오를 살펴보고 이벤트가 흐르는지 확인했습니다. 

이 섹션에서는 이벤트를 받은 후 메시지를 수신하고 처리하는 방법을 알아봅니다.

다음 예제에 설명된 대로 Azure 함수를 추가하는 이유는 Azure Functions 내부의 Service Bus 함수는 아직 새 Event Grid 통합을 기본적으로 지원하지 않기 때문입니다.

1. 필수 구성 요소 섹션에서 열었던 동일한 Visual Studio 솔루션을 열고 **ReceiveMessagesOnEvent.cs**를 선택합니다. 

    ![10][]

2. 다음 코드에서 연결 문자열을 입력합니다.

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

3. Azure Portal에서 "테스트 함수 설정" 섹션에서 만든 Azure 함수에 대한 게시 프로필을 다운로드합니다.

    ![11][]

4. Visual Studio에서 **SBEventGridIntegration**을 마우스 오른쪽 단추로 클릭하고 **Publish**를 선택합니다. 

5. 전에 다운로드한 게시 프로필에 대한 **게시** 창에서 **프로필 가져오기**를 선택한 다음, **게시**를 선택합니다.

    ![12][]

6. 새 Azure 함수를 게시한 후에는 새 Azure 함수를 가리키는 새 Azure Event Grid 구독을 만듭니다.  
    **끝 문자** 상자에서 Service Bus 구독 이름이 될 올바른 필터를 적용합니다.

7. 앞에서 만든 Azure Service Bus 토픽에 메시지를 보내고 Azure Portal의 Azure Functions 로그에서 이벤트가 흐르고 있으며 메시지가 수신되는지 모니터링합니다.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Logic Apps를 사용하여 메시지 받기

다음을 수행하여 Azure Service Bus 및 Azure Event Grid와 논리 앱을 연결합니다.

1. 시작 작업으로 Azure Portal에서 새 논리 앱을 만들고 **Event Grid**를 선택합니다.

    ![13][]

    Logic Apps 디자이너 창이 열립니다.

    ![14][]

2. 다음을 수행하여 정보를 추가합니다.

    a. **리소스 이름** 상자에서 고유한 네임스페이스 이름을 입력합니다. 

    나. **고급 옵션** 아래 **접미사 필터** 상자에서 구독에 대한 필터를 입력합니다.

3. 토픽 구독에서 메시지를 수신하려면 Service Bus 수신 작업을 추가합니다.  
    마지막 작업이 다음 이미지에 표시됩니다.

    ![15][]

4. 다음 이미지에 표시된 것처럼 완료 이벤트를 추가옵니다.

    ![16][]

5. “필수 구성 요소” 섹션에서 설명한 것처럼 논리 앱을 저장하고 Service Bus 토픽을 보냅니다.  
    논리 앱 실행을 관찰합니다. 실행에 대한 더 많은 데이터를 보려면 **개요**를 선택한 다음, **실행 기록** 아래에서 데이터를 봅니다.

    ![17][]

    ![18][]

## <a name="next-steps"></a>다음 단계

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)에 대해 자세히 알아봅니다.
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)에 대해 자세히 알아봅니다.
* [Azure App Service의 Logic Apps 기능](https://docs.microsoft.com/azure/logic-apps/)에 대해 자세히 알아봅니다.
* [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/)에 대해 자세히 알아보세요.


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
