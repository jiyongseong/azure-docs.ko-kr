---
title: ".NET Framework를 사용하여 Azure Event Hubs에서 이벤트 수신 | Microsoft Docs"
description: ".NET Framework를 사용하여 Azure Event Hubs에서 이벤트를 수신하려면 이 자습서를 따르세요."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: sethm
ms.openlocfilehash: 8fd70380dbb88f379789e1a4730934dcd38cac5a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>.NET Framework를 사용하여 Azure Event Hubs에서 이벤트 수신

## <a name="introduction"></a>소개

Event Hubs는 연결된 장치 및 응용 프로그램에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. Event Hubs에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 응용 프로그램 아키텍처의 핵심 구성 요소입니다.

이 자습서에서는 **[이벤트 프로세서 호스트][EventProcessorHost]**를 사용하여 Event Hub에서 메시지를 수신하는 .NET Framework 콘솔 응용 프로그램을 작성하는 방법을 보여 줍니다. .NET Framework를 사용하여 이벤트를 전송하려면 [.NET Framework를 사용하여 Azure Event Hubs로 이벤트 전송](event-hubs-dotnet-framework-getstarted-send.md) 문서를 참조하거나 목차 왼쪽에서 해당하는 전송 언어를 클릭합니다.

[이벤트 프로세서 호스트][EventProcessorHost]는 영구적 검사점을 관리하여 Event Hubs의 이벤트 수신을 간소화하고 이러한 Event Hubs에서 병렬 수신하는 .NET 클래스입니다. [이벤트 프로세서 호스트][Event Processor Host]를 사용하면 다른 노드에 호스트된 수신기를 비롯한 여러 수신기 간에 이벤트를 분할할 수 있습니다. 이 예제에서는 단일 수신기에 대해 [이벤트 프로세서 호스트][EventProcessorHost]를 사용하는 방법을 보여 줍니다. [확장된 이벤트 처리][Scale out Event Processing with Event Hubs] 샘플에서는 여러 수신기에서 [이벤트 프로세서 호스트][EventProcessorHost]를 사용하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Microsoft Visual Studio 2015 이상](http://visualstudio.com). 이 자습서의 스크린샷에서는 Visual Studio 2017을 사용합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/free/)을 참조하세요.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 Event Hub 만들기

첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 Event Hub를 만들려면 [이 문서](event-hubs-create.md)의 프로시저를 따른 후 이 자습서의 다음 단계를 진행합니다.

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

[이벤트 프로세서 호스트][EventProcessorHost]를 사용하려면 [Azure Storage 계정][Azure Storage account]이 있어야 합니다.

1. [Azure Portal][Azure portal]에 로그온하고 화면 왼쪽 위에서 **리소스 만들기**를 클릭합니다.
2. **저장소**를 클릭한 다음 **Storage 계정**을 클릭합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. **저장소 계정 만들기** 창에서 저장소 계정의 이름을 입력합니다. 리소스를 만들 Azure 구독, 리소스 그룹 및 위치를 선택합니다. 그런 다음 **Create**를 클릭합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. 저장소 계정 목록에서 새로 만든 저장소 계정을 클릭합니다.
5. 저장소 계정 창에서 **액세스 키**를 클릭합니다. 이 자습서에서 나중에 **key1** 값을 복사합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

## <a name="create-a-receiver-console-application"></a>수신기 콘솔 응용 프로그램을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 응용 프로그램 프로젝트를 새로 만듭니다. 프로젝트 이름을 **Receiver**로 지정합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
2. 솔루션 탐색기에서 **Receiver** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **솔루션에 대한 NuGet 패키지 관리**를 클릭합니다.
3. **찾아보기** 탭을 클릭한 다음 `Microsoft Azure Service Bus Event Hub - EventProcessorHost`를 검색합니다. **설치**를 클릭하고 사용 약관에 동의합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio는 [Azure Service Bus Event Hub - EventProcessorHost NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)에 대한 참조 및 해당하는 모든 종속성을 다운로드, 설치 및 추가합니다.
4. **Receiver** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다. 새 클래스의 이름을 **SimpleEventProcessor**로 지정하고 **추가**를 클릭하여 클래스를 생성합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
5. SimpleEventProcessor.cs 파일의 맨 위에 다음 구문을 추가합니다.
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  using System.Diagnostics;
  ```
    
  그런 다음 다음 코드를 클래스의 본문으로 대체합니다.
    
  ```csharp
  class SimpleEventProcessor : IEventProcessor
  {
    Stopwatch checkpointStopWatch;
    
    async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
    
    Task IEventProcessor.OpenAsync(PartitionContext context)
    {
        Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
        this.checkpointStopWatch = new Stopwatch();
        this.checkpointStopWatch.Start();
        return Task.FromResult<object>(null);
    }
    
    async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData eventData in messages)
        {
            string data = Encoding.UTF8.GetString(eventData.GetBytes());
    
            Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                context.Lease.PartitionId, data));
        }
    
        //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
        if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
        {
            await context.CheckpointAsync();
            this.checkpointStopWatch.Restart();
        }
    }
  }
  ```
    
  이 클래스는 Event Hub에서 받는 이벤트를 처리하기 위해 **EventProcessorHost**에 의해 호출됩니다. `SimpleEventProcessor` 클래스는 초시계를 사용하여 **EventProcessorHost** 컨텍스트에서 검사점 메서드를 주기적으로 호출합니다. 이 처리로 인해 수신기가 다시 시작되는 경우 5분 이하의 처리 작업은 손실됩니다.
6. **Program** 클래스에서 파일 맨 위에 다음 `using` 문을 추가합니다.
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  ```
    
  그런 다음 `Program` 클래스의 `Main` 메서드를 다음 코드로 바꾸고 이전에 저장한 Event Hub 이름 및 네임스페이스 수준 연결 문자열과 이전 섹션에서 복사한 저장소 계정 및 키를 대체합니다. 
    
  ```csharp
  static void Main(string[] args)
  {
    string eventHubConnectionString = "{Event Hubs namespace connection string}";
    string eventHubName = "{Event Hub name}";
    string storageAccountName = "{storage account name}";
    string storageAccountKey = "{storage account key}";
    string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
    
    string eventProcessorHostName = Guid.NewGuid().ToString();
    EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
    Console.WriteLine("Registering EventProcessor...");
    var options = new EventProcessorOptions();
    options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
    eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
    
    Console.WriteLine("Receiving. Press enter key to stop worker.");
    Console.ReadLine();
    eventProcessorHost.UnregisterEventProcessorAsync().Wait();
  }
  ```

7. 프로그램을 실행하고 오류가 없는지 확인합니다.
  
축하합니다! 이제 이벤트 프로세서 호스트를 사용하여 Event Hub에서 메시지를 받았습니다.


> [!NOTE]
> 이 자습서에서는 [EventProcessorHost][EventProcessorHost]의 단일 인스턴스를 사용합니다. 처리량을 늘리려면 [EventProcessorHost][EventProcessorHost]의 여러 인스턴스를 실행하는 것이 좋습니다. [확장된 이벤트 처리][확장된 이벤트 처리] 샘플을 참조하세요. 이러한 경우 다양한 인스턴스가 자동으로 서로 조정되어 수신된 이벤트의 부하를 분산합니다. 여러 수신기가 각각 이벤트를 *모두* 처리하도록 하려면 **ConsumerGroup** 개념을 사용해야 합니다. 서로 다른 컴퓨터에서 이벤트를 수신하는 경우 [EventProcessorHost][EventProcessorHost] 인스턴스의 이름을 해당 인스턴스가 배포된 컴퓨터 또는 역할을 기준으로 지정하면 유용할 수 있습니다. 이러한 항목에 대한 자세한 내용은 [Event Hubs 개요][Event Hubs overview] 및 [Event Hubs 프로그래밍 가이드][Event Hubs Programming Guide] 항목을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계

이제 Event Hub를 만들고 데이터를 보내고 받는 작업 중인 응용 프로그램을 구축했으므로 다음 링크를 방문하여 더 자세히 알아볼 수 있습니다.

* [이벤트 프로세서 호스트][Event Processor Host]
* [Event Hubs 개요][Event Hubs overview]
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com
