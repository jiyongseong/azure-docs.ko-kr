---
title: Java를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs
description: Java를 사용하여 Event Hubs로 전송 시작
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: 5dd0c88dab9ff4b7073a9acf6872b4c3ff085586
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Java를 사용하여 Azure Event Hubs로 이벤트 전송

Event Hubs는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [Event Hubs 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 Java 언어의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 이벤트를 전송하는 방법을 보여 줍니다. Java 이벤트 프로세스 호스트 라이브러리를 사용하여 이벤트를 수신하려면 [이 문서](event-hubs-java-get-started-receive-eph.md)를 참조하거나 목차 왼쪽에서 해당하는 수신 언어를 클릭합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.
* 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만듭니다.

이 자습서의 코드는 전체 작동 중인 응용 프로그램을 확인하기 위해 검사할 수 있는 [GitHub 샘플 전송](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/Send)을 기반으로 합니다.

## <a name="send-events-to-event-hubs"></a>Event Hubs에 이벤트 보내기

Event Hubs에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)에서 Maven 프로젝트에 사용할 수 있습니다. Maven 프로젝트 파일 안에 다음 종속성 선언을 사용하여 이 라이브러리를 참조할 수 있습니다. 현재 버전은 1.0.0입니다.    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

다양한 형식의 빌드 환경을 위해, [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)에서 최근에 릴리스된 JAR 파일을 명시적으로 가져올 수 있습니다.  

단순 이벤트 게시자의 경우 Event Hubs 클라이언트 클래스에 대한 *com.microsoft.azure.eventhubs* 패키지와 유틸리티 클래스(예: Azure Service Bus 메시징 클라이언트와 공유되는 일반적인 예외)에 대한 *com.microsoft.azure.servicebus* 패키지를 가져옵니다. 

### <a name="declare-the-send-class"></a>송신 클래스 선언

다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 응용 프로그램에 대한 새 Maven 프로젝트를 만듭니다. 클래스 `Send`의 이름을 지정합니다.     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>연결 문자열 생성

ConnectionStringBuilder 클래스를 사용하여 Event Hubs 클라이언트 인스턴스로 전달하는 연결 문자열 값을 생성합니다. 자리 표시자를 네임스페이스 및 이벤트 허브를 만들 때 얻은 값으로 바꿉니다.

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>이벤트 보내기

그런 다음, 문자열을 UTF-8 바이트 인코딩으로 전환하여 단일 이벤트를 만듭니다. 그런 다음, 연결 문자열에서 새 Event Hubs 클라이언트 인스턴스를 만들고 메시지를 보냅니다.   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [를 사용하여 이벤트 수신](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs 개요][Event Hubs overview]
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[무료 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

