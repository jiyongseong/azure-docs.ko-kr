---
title: "Java Service Bus API와 함께 AMQP 1.0을 사용하는 방법 | Microsoft Docs"
description: "Azure 서비스 버스 및 AMQP(Advanced Message Queuing Protocol) 1.0과 함께 JMS(Java Message Service)를 사용하는 방법."
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: 
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>서비스 버스 및 AMQP 1.0과 함께 JMS(Java Message Service) API를 사용하는 방법
AMQP(Advanced Message Queuing Protocol) 1.0은 강력한 크로스 플랫폼 메시징 응용 프로그램을 빌드하는 데 사용할 수 있는 효율성과 안정성이 뛰어난 유선 수준 메시징 프로토콜입니다.

서비스 버스에서 AMQP 1.0이 지원되므로 효율적인 이진 프로토콜을 사용하여 다양한 플랫폼에서 큐 및 게시/구독 조정된 메시징 기능을 이용할 수 있습니다. 뿐만 아니라 여러 언어, 프레임워크 및 운영 체제가 혼합되어 사용된 구성 요소로 이루어진 응용 프로그램을 만들 수 있습니다.

이 문서에서는 널리 사용되는 JMS(Java Message Service) API 표준을 통해 Java 응용 프로그램에서 Service Bus 메시징 기능(큐 및 게시/구독 토픽)을 사용하는 방법을 설명합니다. Service Bus .NET API를 사용하여 동일한 작업을 수행하는 방법을 설명하는 [동반 문서](service-bus-amqp-dotnet.md)가 있습니다. AMQP 1.0을 사용한 플랫폼 간 메시징에 대해 알아보려면 이 두 가지 가이드를 함께 사용할 수 있습니다.

## <a name="get-started-with-service-bus"></a>서비스 버스 시작
이 가이드에서는 사용자가 **queue1**이라는 큐가 포함된 Service Bus 네임스페이스를 이미 가지고 있다고 가정합니다. 가지고 있지 않은 사용자는 [Azure Portal](https://portal.azure.com)을 사용하여 [네임스페이스와 큐를 만들](service-bus-create-namespace-portal.md) 수 있습니다. Service Bus 네임스페이스와 큐를 만드는 방법에 대한 자세한 내용은 [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)을 참조하세요.

> [!NOTE]
> 분할된 큐 및 토픽은 또한 AMQP를 지원합니다. 자세한 내용은 [분할된 메시징 엔터티](service-bus-partitioning.md) 및 [Service Bus 분할 큐 및 토픽을 위한 AMQP 1.0 지원](service-bus-partitioned-queues-and-topics-amqp-overview.md)을 참조하세요.
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>AMQP 1.0 JMS 클라이언트 라이브러리 다운로드
최신 버전의 Apache Qpid JMS AMQP 1.0 클라이언트 라이브러리를 다운로드할 위치에 대한 자세한 내용은 [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)을 참조하세요.

서비스 버스를 사용하여 JMS 응용 프로그램을 빌드 및 실행할 때 Apache Qpid JMS AMQP 1.0 배포 보관에 포함된 다음 JAR 파일 4개를 Java CLASSPATH에 추가해야 합니다.

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-amqp-1-0-client-[version].jar
* qpid-amqp-1-0-client-jms-[version].jar
* qpid-amqp-1-0-common-[version].jar

## <a name="coding-java-applications"></a>Java 응용 프로그램 코딩
### <a name="java-naming-and-directory-interface-jndi"></a>JNDI(Java Naming and Directory Interface)
JMS는 JNDI(Java Naming and Directory Interface)를 사용하여 논리적 이름과 물리적 이름 간에 구분을 만듭니다. JNDI를 사용하여 두 유형의 JMS 개체인 ConnectionFactory와 Destination을 확인합니다. JNDI는 다양한 디렉터리 서비스를 연결할 수 있는 공급자 모델을 사용하여 이름 확인 책임을 처리합니다. Apache Qpid JMS AMQP 1.0 라이브러리에는 다음 형식의 속성 파일을 사용하여 구성된 간단한 속성 파일 기반 JNDI 공급자가 포함되어 있습니다.

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>ConnectionFactory 구성
Qpid 속성 파일 JNDI 공급자에서 **ConnectionFactory**를 정의하는 데 사용되는 항목의 형식은 다음과 같습니다.

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

여기서 **[jndi_name]** 및 **[ConnectionURL]**의 의미는 다음과 같습니다.

* **[jndi_name]**: ConnectionFactory의 논리적 이름입니다. JNDI IntialContext.lookup() 메서드를 사용하여 Java 응용 프로그램에서 확인되는 이름입니다.
* **[ConnectionURL]**: AMQP broker에 필요한 정보를 JMS 라이브러리에 제공하는 URL입니다.

**ConnectionURL**의 형식은 다음과 같습니다.

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
여기서 **[namespace]**, **[SASPolicyName]** 및 **[SASPolicyKey]**의 의미는 다음과 같습니다.

* **[namespace]**: Service Bus 네임스페이스입니다.
* **[SASPolicyName]**: 큐 공유 액세스 서명 정책 이름입니다.
* **[SASPolicyKey]**: 큐 공유 액세스 서명 정책 키입니다.

> [!NOTE]
> 수동으로 암호를 URL 인코딩해야 합니다. 유용한 URL 인코딩 유틸리티는 [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp)에서 사용할 수 있습니다.
> 
> 

#### <a name="configure-destinations"></a>destination 구성
Qpid 속성 파일 JNDI 공급자에서 destination을 정의하는 데 사용되는 항목의 형식은 다음과 같습니다.

```
queue.[jndi_name] = [physical_name]
```

또는

```
topic.[jndi_name] = [physical_name]
```

여기서 **[jndi\_name]**과 **[physical\_name]**의 의미는 다음과 같습니다.

* **[jndi_name]**: 대상의 논리적 이름입니다. JNDI IntialContext.lookup() 메서드를 사용하여 Java 응용 프로그램에서 확인되는 이름입니다.
* **[physical_name]**: 응용 프로그램이 메시지를 보내거나 받는 Service Bus 엔터티의 이름입니다.

> [!NOTE]
> 서비스 버스 토픽 구독에서 받는 경우 JNDI에 지정된 물리적 이름은 토픽 이름이어야 합니다. 구독 이름은 JMS 응용 프로그램 코드에서 지속형 구독을 만들 때 제공됩니다. [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)에서는 JMS의 Service Bus 토픽 작업에 대한 자세한 내용을 제공합니다.
> 
> 

### <a name="write-the-jms-application"></a>JMS 응용 프로그램 작성
서비스 버스와 함께 JMS를 사용할 때 필요한 특별한 API 또는 옵션은 없습니다. 하지만 나중에 다룰 몇 가지 제한은 있습니다. JMS 응용 프로그램과 마찬가지로 **ConnectionFactory** 및 대상을 확인하려면 먼저 JNDI 환경의 구성이 필요합니다.

#### <a name="configure-the-jndi-initialcontext"></a>JNDI InitialContext 구성
JNDI 환경은 구성 정보 해시 테이블을 javax.naming.InitialContext 클래스의 생성자에 전달하여 구성됩니다. 해시 테이블의 두 가지 필수 요소는 초기 컨텍스트 팩터리의 클래스 이름과 공급자 URL입니다. 다음 코드는 **servicebus.properties**라는 속성 파일과 함께 Qpid 속성 파일 기반 JNDI 공급자를 사용하도록 JNDI 환경을 구성하는 방법을 보여 줍니다.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>서비스 버스 큐를 사용하는 간단한 JMS 응용 프로그램
다음 예제 프로그램은 JNDI 논리적 이름이 QUEUE인 서비스 버스 큐에 JMS TextMessages를 보내고 메시지를 받습니다.

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>응용 프로그램 실행
응용 프로그램을 실행하면 양식의 출력이 생성됩니다.

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>JMS와 .NET 간의 크로스 플랫폼 메시징
이 가이드에서는 JMS를 사용하여 서비스 버스로 메시지를 보내고 받는 방법을 보여 줍니다. 그러나 AMQP 1.0의 주요 이점 중 하나는 다른 언어로 작성된 구성 요소로 응용 프로그램을 빌드하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있다는 것입니다.

위에서 설명한 샘플 JMS 응용 프로그램 및 동반 문서([AMQP 1.0을 사용하여 .NET에서 Service Bus 사용](service-bus-amqp-dotnet.md))에서 제공하는 유사한 .NET 응용 프로그램을 사용하여 .NET과 Java 간에 메시지를 교환할 수 있습니다. Service Bus 및 AMQP 1.0을 사용하는 크로스 플랫폼 메시징에 대한 자세한 내용은 이 문서를 참조하세요.

### <a name="jms-to-net"></a>JMS에서 .NET으로
JMS에서 .NET으로의 메시징을 시연하려면:

* 명령줄 인수 없이 .NET 샘플 응용 프로그램을 시작합니다.
* "sendonly" 명령줄 인수로 Java 샘플 응용 프로그램을 시작합니다. 이 모드에서는 응용 프로그램이 큐에서 메시지를 받지 않고 보내기만 합니다.
* Java 응용 프로그램 콘솔에서 **Enter** 키를 몇 번 누릅니다. 그러면 메시지가 전송됩니다.
* .NET 응용 프로그램에서 이러한 메시지를 수신합니다.

#### <a name="output-from-jms-application"></a>JMS 응용 프로그램의 출력
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>.NET 응용 프로그램의 출력
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET에서 JMS로
.NET에서 JMS로의 메시징을 시연하려면:

* "sendonly" 명령줄 인수로 .NET 샘플 응용 프로그램을 시작합니다. 이 모드에서는 응용 프로그램이 큐에서 메시지를 받지 않고 보내기만 합니다.
* 명령줄 인수 없이 Java 샘플 응용 프로그램을 시작합니다.
* .NET 응용 프로그램 콘솔에서 **Enter** 키를 몇 번 누릅니다. 그러면 메시지가 전송됩니다.
* Java 응용 프로그램에서 이러한 메시지를 수신합니다.

#### <a name="output-from-net-application"></a>.NET 응용 프로그램의 출력
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>JMS 응용 프로그램의 출력
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>지원되지 않는 기능 및 제한
서비스 버스와 함께 JMS over AMQP 1.0을 사용하는 경우 다음과 같은 제한 사항이 있습니다.

* **세션**당 하나의 **MessageProducer** 또는 **MessageConsumer**만 허용됩니다. 응용 프로그램에서 **MessageProducers** 또는 **MessageConsumers**를 여러 개 만들어야 하는 경우 각 항목에 대한 전용 **세션**을 만듭니다.
* 휘발성 토픽 구독은 현재 지원되지 않습니다.
* **MessageSelectors**는 현재 지원되지 않습니다.
* 예를 들어, 임시 대상인 **TemporaryQueue**, **TemporaryTopic**과 이러한 대상을 사용하는 **QueueRequestor** 및 **TopicRequestor** API는 현재 지원되지 않습니다.
* 트랜잭션 처리된 세션과 분산 트랜잭션은 지원되지 않습니다.

## <a name="summary"></a>요약
이 방법 가이드에서는 널리 사용되는 JMS API 및 AMQP 1.0을 통해 Java에서 Service Bus 조정된 메시징 기능(큐 및 게시/구독 토픽)에 액세스하는 방법을 설명했습니다.

.NET, C, Python, PHP 등의 다른 언어에서도 Service Bus AMQP 1.0을 사용할 수 있습니다. 이러한 언어로 빌드한 구성 요소는 서비스 버스의 AMQP 1.0 지원을 사용하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus .NET API와 함께 AMQP 1.0을 사용하는 방법](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Java 개발자 센터](https://azure.microsoft.com/develop/java/)

