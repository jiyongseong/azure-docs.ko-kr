---
title: ".NET Standard 라이브러리를 사용하여 Azure Event Hubs에서 이벤트 수신 | Microsoft Docs"
description: ".NET Standard에서 EventProcessorHost를 사용하여 메시지 수신 시작"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: 5eb5c2d1f0b85c907f788fb6ac752488601f613a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>.NET Standard에서 이벤트 프로세서 호스트를 사용하여 메시지 수신 시작

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)에서 사용할 수 있습니다.

이 자습서에서는 **이벤트 프로세서 호스트** 라이브러리를 사용하여 Event Hub에서 메시지를 수신하는 .NET Core 콘솔 응용 프로그램을 작성하는 방법을 보여 줍니다. 문자열을 사용자의 이벤트 허브 및 저장소 계정 값으로 바꾸어 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) 솔루션을 있는 그대로 실행할 수 있습니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Microsoft Visual Studio 2015 또는 2017](http://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2017을 사용하지만 Visual Studio 2015도 지원됩니다.
* [.NET Core Visual Studio 2015 또는 2017 도구](http://www.microsoft.com/net/core).
* Azure 구독.
* Azure Event Hubs 네임스페이스.
* Azure Storage 계정.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 이벤트 허브 만들기  

1단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램이 이벤트 허브와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차에 따라 이 자습서를 진행합니다.  

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
2. 포털의 왼쪽 탐색 창에서 **리소스 만들기**, **Storage**, **Storage 계정**을 차례로 클릭합니다.  
3. 저장소 계정 창에서 필드를 완성한 후 **만들기**를 클릭합니다.

    ![저장소 계정 만들기][1]

4. **배포 성공** 메시지가 나타나면 새 저장소 계정의 이름을 클릭합니다. **Essentials** 창에서 **Blob**을 클릭합니다. **Blob service** 대화 상자가 열리면 맨 위의 **+ 컨테이너**를 클릭합니다. 컨테이너 이름을 지정한 다음 **Blob service**를 닫습니다.  
5. 왼쪽 창에서 **선택키**를 클릭하고 저장소 컨테이너 이름, 저장소 계정 및 **key1** 값을 복사합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 저장합니다.  

## <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio를 시작합니다. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다. .NET Core 콘솔 응용 프로그램을 만듭니다.

![새 프로젝트][2]

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

[**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 및 [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET 표준 라이브러리 NuGet 패키지를 다음 단계를 따라 사용자 프로젝트에 추가합니다. 

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **Microsoft.Azure.EventHubs**를 검색한 다음 **Microsoft.Azure.EventHubs** 패키지를 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
3. 1단계와 2단계를 반복하고 **Microsoft.Azure.EventHubs.Processor** 패키지를 설치합니다.

## <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor 인터페이스 구현

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 새 클래스 이름을 **SimpleEventProcessor**로 지정합니다.

2. SimpleEventProcessor.cs 파일을 열고 다음 `using` 문을 파일의 맨 위에 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. `IEventProcessor` 인터페이스를 구현합니다. `SimpleEventProcessor` 클래스의 전체 내용을 다음 코드로 바꿉니다.

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>SimpleEventProcessor 클래스를 사용하여 메시지를 수신하는 기본 콘솔 메서드 작성

1. Program.cs 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. 이벤트 허브 연결 문자열, 이벤트 허브 이름, 저장소 계정 컨테이너 이름, 저장소 계정 이름, 저장소 계정 키의 `Program` 클래스에 상수를 추가합니다. 자리 표시자를 해당 값으로 바꾸어 다음 코드를 추가합니다.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 다음과 같이 `MainAsync`라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` 메서드에 다음 코드 줄을 추가합니다.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 파일은 다음과 같아야 합니다.

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. 프로그램을 실행하고 오류가 없는지 확인합니다.

축하합니다! 이제 이벤트 프로세서 호스트를 사용하여 이벤트 허브에서 메시지를 수신했습니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
