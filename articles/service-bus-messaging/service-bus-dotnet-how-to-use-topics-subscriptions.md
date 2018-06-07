---
title: "Azure Service Bus 항목 및 구독 시작 | Microsoft Docs"
description: "Service Bus 메시징 항목 및 구독을 사용하는 C# .NET Core 콘솔 응용 프로그램을 작성합니다."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/6/2017
ms.author: sethm
ms.openlocfilehash: aa75ac48d650f28d4aaeb612f2900d705cf71b5b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-service-bus-topics"></a>Service Bus 큐 항목 시작

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 자습서에서 다루는 단계는 다음과 같습니다.

1. Azure Portal을 사용하여 Service Bus 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 Service Bus 항목을 만듭니다.
3. Azure Portal을 사용하여 해당 항목에 Service Bus 구독을 만듭니다.
4. 항목에 일련의 메시지를 보내도록 .NET Core 콘솔 응용 프로그램을 작성합니다.
5. 구독에서 해당 메시지를 수신하도록 .NET Core 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건

1. [Visual Studio 2017 업데이트 3(버전 15.3, 26730.01)](http://www.visualstudio.com/vs) 이상
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상
2. Azure 구독.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Azure Portal을 사용하여 네임스페이스 만들기

> [!NOTE] 
> [PowerShell](/powershell/azure/get-started-azureps)을 사용하여 Service Bus 네임스페이스 및 메시징 엔터티를 만들 수도 있습니다. 자세한 내용은 [PowerShell을 사용하여 Service Bus 리소스 관리](service-bus-manage-with-ps.md)를 참조하세요.

Service Bus 메시징 네임스페이스를 이미 만든 경우 [Azure Portal을 사용하여 큐 만들기](#2-create-a-topic-using-the-azure-portal) 섹션으로 이동합니다.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Azure Portal을 사용하여 항목 만들기

1. [Azure Portal][azure-portal]에 로그인합니다.
2. 포털의 왼쪽 탐색 창에서 **Service Bus**(**Service Bus**가 표시되지 않으면 **모든 서비스** 클릭)를 클릭하거나 **모든 리소스**를 클릭합니다. 항목을 만들려는 네임스페이스를 클릭합니다. 
3. 네임스페이스 개요 창이 열립니다. **항목**을 클릭합니다.
   
    ![토픽 만들기][createtopic1]
4. **+ 항목**을 클릭합니다.
   
    ![항목 선택][createtopic2]
5. 항목의 이름을 입력합니다. 다른 옵션은 기본값 그대로 둡니다.
   
    ![새로 만들기 선택][createtopic3]
6. 대화 상자 아래쪽에서 **만들기**를 클릭합니다.

## <a name="3-create-a-subscription-to-the-topic"></a>3. 항목에 대한 구독 만들기

1. 포털 리소스 창에서 1단계에서 만든 네임스페이스를 클릭하고, **항목**을 클릭하고, 2단계에서 만든 항목의 이름을 클릭합니다.
2. 개요 창의 맨 위에서 **+ 구독**을 클릭하여 이 항목에 구독을 추가합니다.

    ![구독 만들기][createtopic4]

3. 구독의 이름을 입력합니다. 다른 옵션은 기본값 그대로 둡니다.

## <a name="4-send-messages-to-the-topic"></a>4. 토픽에 메시지 보내기

항목에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio를 시작하고 새로운 **콘솔 앱(.NET Core)** 프로젝트를 만듭니다.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**를 검색한 다음 **Microsoft.Azure.ServiceBus** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
   
    ![NuGet 패키지 선택][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>코드를 작성하여 토픽에 메시지 보내기

1. 클래스 선언 전에 Program.cs의 네임스페이스 정의 위쪽에서 다음 `using` 문을 추가합니다.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` 클래스 내에서 다음 변수를 선언합니다. `ServiceBusConnectionString` 변수를 네임스페이스를 만들 때 가져온 연결 문자열로 설정하고, `TopicName`을 항목을 만들 때 사용된 이름으로 설정합니다.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. `Main()`이라는 기본 내용을 다음 코드줄로 바꿉니다.

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. `Main()`의 바로 뒤에 메시지 전송 메서드를 호출하는 다음 비동기 `MainAsync()` 메서드를 추가합니다.

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. `MainAsync()` 메서드의 바로 뒤에 `numberOfMessagesToSend`(현재 10으로 설정됨)으로 지정된 메시지 수를 전송하는 작업을 수행하는 다음 `SendMessagesAsync()` 메서드를 추가합니다.

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. 보낸 사람 Program.cs 파일은 다음과 같아야 합니다.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. 프로그램을 실행하고 Azure Portal을 확인합니다. 네임스페이스 **개요** 창에서 항목 이름을 클릭합니다. 항목 **Essentials** 화면이 표시됩니다. 이제 창의 맨 아래 가까이에 나열된 구독에서 각 구독에 대한 **메시지 수** 값은 **10**입니다. 메시지를 검색하지 않고 보낸 사람 응용 프로그램을 실행할 때마다(다음 섹션에서 설명) 이 값이 10만큼 증가합니다. 또한 앱이 항목에 메시지를 추가할 때마다 항목의 현재 크기는 **Essentials** 창에 있는 **현재** 값을 증가시킵니다.
   
      ![메시지 크기][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. 구독에서 메시지 받기

방금 보낸 메시지를 받으려면 다른 .NET Core 콘솔 응용 프로그램을 만들고 이전의 보낸 사람 응용 프로그램과 유사한 **Microsoft.Azure.ServiceBus** NuGet 패키지를 설치합니다.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>코드를 작성하여 구독에서 메시지 받기

1. 클래스 선언 전에 Program.cs의 네임스페이스 정의 위쪽에서 다음 `using` 문을 추가합니다.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` 클래스 내에서 다음 변수를 선언합니다. `ServiceBusConnectionString` 변수는 네임스페이스를 만들 때 가져온 연결 문자열로 설정하고, `TopicName`을 항목을 만들 때 사용된 이름으로 설정하고, `SubscriptionName`을 항목에 구독을 만들 때 사용된 이름으로 설정합니다.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. `Main()`이라는 기본 내용을 다음 코드줄로 바꿉니다.

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. `Main()`의 바로 뒤에 `RegisterOnMessageHandlerAndReceiveMessages()` 메서드를 호출하는 다음 비동기 `MainAsync()` 메서드를 추가합니다.

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. `MainAsync()` 메서드의 바로 뒤에 메시지 처리기를 등록하고 보낸 사람 응용 프로그램에서 보낸 메시지를 수신하는 다음 메서드를 추가합니다.

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. 이전 메서드의 바로 뒤에 수신된 메시지를 처리하는 다음 `ProcessMessagesAsync()` 메서드를 추가합니다.
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. 마지막으로 발생할 수 있는 모든 예외를 처리하는 다음 메서드를 추가합니다.
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. 받는 사람 Program.cs 파일은 다음과 같아야 합니다.
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. 프로그램을 실행하고 포털을 다시 확인합니다. 이제 **메시지 수**와 **현재** 값이 **0**이 됩니다.
   
    ![항목 길이][topic-message-receive]

축하합니다! 이제 .NET Standard 라이브러리를 사용하여 토픽 및 구독을 만들고, 10개의 메시지를 보내고, 해당 메시지를 수신했습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징의 많은 고급 기능 중 일부를 보여 주는 [샘플이 있는 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
