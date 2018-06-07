---
title: ".NET Framework를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs"
description: ".NET Framework를 사용하여 Event Hubs로 이벤트 전송 시작"
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
ms.openlocfilehash: feb4332f8f6b5ab26067b5c80a376cdee62c7739
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>.NET Framework를 사용하여 Azure Event Hubs로 이벤트 전송

## <a name="introduction"></a>소개

Event Hubs는 연결된 장치 및 응용 프로그램에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. Event Hubs에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 응용 프로그램 아키텍처의 핵심 구성 요소입니다.

이 자습서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hub를 만드는 방법을 보여 줍니다. 또한 .NET Framework를 사용하여 C#으로 작성된 콘솔 응용 프로그램을 사용하여 Event Hub에 이벤트를 전송하는 방법을 보여 줍니다. .NET Framework를 사용하여 이벤트를 수신하려면 [.NET Framework를 사용하여 이벤트 수신](event-hubs-dotnet-framework-getstarted-receive-eph.md) 문서를 참조하거나 목차 왼쪽에서 해당하는 수신 언어를 클릭합니다.

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Microsoft Visual Studio 2015 이상](http://visualstudio.com). 이 자습서의 스크린샷에서는 Visual Studio 2017을 사용합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/free/)을 참조하세요.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 Event Hub 만들기

첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 Event Hub를 만들려면 [이 문서](event-hubs-create.md)의 프로시저를 따른 후 이 자습서의 다음 단계를 진행합니다.

## <a name="create-a-sender-console-application"></a>보낸 사람 콘솔 응용 프로그램 만들기

이 섹션에서는 이벤트 허브로 이벤트를 보내는 Windows 콘솔 앱을 작성합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 응용 프로그램 프로젝트를 새로 만듭니다. 프로젝트의 이름을 **Sender**로 지정합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. 솔루션 탐색기에서 **Sender** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **솔루션에 대한 NuGet 패키지 관리**를 클릭합니다. 
3. **찾아보기** 탭을 클릭한 다음 `WindowsAzure.ServiceBus`를 검색합니다. **설치**를 클릭하고 사용 약관에 동의합니다. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio는 [Azure Service Bus 라이브러리 NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus)에 대한 참조를 다운로드, 설치 및 추가합니다.
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
  ```csharp
  using System.Threading;
  using Microsoft.ServiceBus.Messaging;
  ```
5. **Program** 클래스에 다음 필드를 추가하고, 이전 섹션에서 만든 Event Hub 이름과 이전에 저장한 네임스페이스 수준 연결 문자열로 위치 지정자 값을 대체합니다.
   
  ```csharp
  static string eventHubName = "{Event Hub name}";
  static string connectionString = "{send connection string}";
  ```
6. **Program** 클래스에 다음 메서드를 추가합니다.
   
  ```csharp
  static void SendingRandomMessages()
  {
      var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
      while (true)
      {
          try
          {
              var message = Guid.NewGuid().ToString();
              Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
              eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
          }
          catch (Exception exception)
          {
              Console.ForegroundColor = ConsoleColor.Red;
              Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
              Console.ResetColor();
          }
   
          Thread.Sleep(200);
      }
  }
  ```
   
  이 메서드는 200ms 지연과 함께 Event Hub에 이벤트를 지속적으로 보냅니다.
7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
  ```csharp
  Console.WriteLine("Press Ctrl-C to stop the sender process");
  Console.WriteLine("Press Enter to start now");
  Console.ReadLine();
  SendingRandomMessages();
  ```
8. 프로그램을 실행하고 오류가 없는지 확인합니다.
  
축하합니다! 이제 Event Hub에 메시지를 보냈습니다.

## <a name="next-steps"></a>다음 단계
이제 Event Hub를 만들고 데이터를 보내는 작업 중인 응용 프로그램을 구축했으므로 다음 시나리오로 진행할 수 있습니다.

* [이벤트 프로세서 호스트를 사용하여 이벤트 수신](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [이벤트 프로세서 호스트 참조](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

