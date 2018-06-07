---
title: "시나리오 - Azure Functions 및 Azure Service Bus를 사용하여 논리 앱 트리거 | Microsoft Docs"
description: "Azure Functions 및 Azure Service Bus를 사용하여 논리 앱을 트리거하는 함수 만들기"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 088f10bc32dd492f82f0a10a7e5829e76f588758
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>시나리오: Azure Functions 및 Azure Service Bus를 사용하여 논리 앱 트리거

Azure Functions로 장기 실행 수신기 또는 작업을 배포하는 데 필요한 논리 앱용 트리거를 만들 수 있습니다. 예를 들어, 큐에서 수신 대기하고 있다가 푸시 트리거로 즉시 논리 앱을 실행하는 함수를 만들 수 있습니다.

## <a name="build-the-logic-app"></a>논리 앱 빌드
이 예에는 트리거할 필요가 있는 각 논리 앱에 대해 실행되는 함수가 있습니다. 먼저, HTTP 요청 트리거가 있는 논리 앱을 만듭니다. 이 함수는 큐 메시지를 수신할 때마다 끝점을 호출합니다.  

1. 논리 앱을 만듭니다.
2. **수동 – HTTP 요청을 받은 경우** 트리거를 선택합니다.
   또는, [jsonschema.net](http://jsonschema.net)과 같은 도구로 큐 메시지와 함께 사용할 JSON 스키마를 지정할 수 있습니다. 트리거에 스키마를 붙여넣습니다. 스키마는 디자이너가 데이터의 형태를 이해하고 워크플로에서 속성이 쉽게 흐를 수 있도록 도와줍니다.
2. 큐 메시지를 수신한 후 실행하기를 원하는 추가 절차를 추가합니다. 예를 들어, Office 365로 이메일을 전송합니다.  
3. 논리 앱을 저장하여 이 논리 앱에 트리거에 대한 콜백 URL을 생성합니다. URL은 트리거 카드에 표시됩니다.

![트리거 카드에 나타나는 콜백 URL][1]

## <a name="build-the-function"></a>함수 빌드
다음으로 트리거로 작동하고 큐에 수신 대기하는 함수를 만들어야 합니다.

1. [Azure Functions 포털](https://functions.azure.com/signin)에서 **새 함수**를 선택한 다음 **ServiceBusQueueTrigger - C#** 템플릿을 선택합니다.
   
    ![Azure Functions 포털][2]
2. Service Bus 큐에 대한 연결이 Azure Service Bus SDK `OnMessageReceive()` 수신기를 사용하도록 구성합니다.
3. 큐 메시지를 트리거로 사용하여 (앞서 만든) 논리 앱 끝점을 호출하는 기본 함수를 작성합니다. 함수의 전체 예제는 다음과 같습니다. 이 예제는 `application/json` 메시지 콘텐츠 형식을 사용하지만 필요에 따라 변경할 수 있습니다.
   
   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
   
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

테스트를 하려면 [서비스 버스 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer)등의 도구로 큐 메시지를 추가합니다. 함수가 메시지를 받는 즉시 논리 앱이 실행되는지 확인합니다.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png
