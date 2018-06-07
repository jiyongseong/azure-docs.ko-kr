---
title: '자습서: Apache Kafka 생산자 및 소비자 API 사용 - Azure HDInsight | Microsoft Docs'
description: HDInsight의 Kafka에서 Apache Kafka 생산자 및 소비자 API를 사용하는 방법에 대해 알아봅니다. 이 자습서에서는 Java 응용 프로그램에서 HDInsight의 Kafka와 함께 이러한 API를 사용하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: larryfr
ms.openlocfilehash: b602f8bfe316e9c11dbff18273f37c99407c3da6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>자습서: Apache Kafka 생산자 및 소비자 API 사용

HDInsight의 Kafka에서 Kafka 생산자 및 소비자 API를 사용하는 방법을 알아봅니다.

Kafka 생산자 API를 통해 Kafka 클러스터에 데이터 스트림을 보낼 수 있습니다. Kafka 소비자 API를 통해 클러스터에서 데이터 스트림을 읽을 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 배포 환경 설정
> * 코드 이해
> * 응용 프로그램 빌드 및 배포
> * 클러스터에서 응용 프로그램 실행

API에 대한 자세한 내용은 [생산자 API](https://kafka.apache.org/documentation/#producerapi) 및 [소비자 API](https://kafka.apache.org/documentation/#consumerapi)에서 Apache 설명서를 참조하세요.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

개발 환경에 다음 구성 요소가 설치되어 있어야 합니다.

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 이와 동등한 프로그램(예: OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* SSH 클라이언트 및 `scp` 명령입니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

* 텍스트 편집기 또는 Java IDE

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* `JAVA_HOME` - JDK가 설치된 디렉터리를 가리켜야 합니다.
* `PATH` - 다음 경로를 포함해야 합니다.
  
    * `JAVA_HOME`(또는 이와 동등한 경로)
    * `JAVA_HOME\bin`(또는 이와 동등한 경로)
    * Maven이 설치된 디렉터리

## <a name="set-up-your-deployment-environment"></a>배포 환경 설정

이 자습서에서는 HDInsight 3.6의 Kafka가 필요합니다. HDInsight 클러스터에서 Kafka를 만드는 방법을 알아보려면 [HDInsight에서 Kafka 시작](apache-kafka-get-started.md) 설명서를 참조하세요.

## <a name="understand-the-code"></a>코드 이해

예제 응용 프로그램은 `Producer-Consumer` 하위 디렉터리의 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에 있습니다. 응용 프로그램은 주로 4개의 파일로 구성됩니다.

* `pom.xml`: 이 파일은 프로젝트 종속성, Java 버전 및 패키징 메서드를 정의합니다.
* `Producer.java`: 이 파일은 생산자 API를 사용하여 Kafka에 1백만(1,000,000)개의 임의 문장을 보냅니다.
* `Consumer.java`: 이 파일은 소비자 API를 사용하여 Kafka에서 데이터를 읽고 STDOUT에 내보냅니다.
* `Run.java`: 생산자 및 소비자 코드를 실행하는 데 사용되는 명령줄 인터페이스입니다.

### <a name="pomxml"></a>Pom.xml

`pom.xml` 파일에서 이해할 중요한 사항은 다음과 같습니다.

* 종속성: 이 프로젝트는 `kafka-clients` 패키지에서 제공하는 Kafka 생산자 및 소비자 API에 의존합니다. 다음 XML 코드는 이 종속성을 정의합니다.

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > `${kafka.version}` 항목은 `pom.xml`의 `<properties>..</properties>` 섹션에서 선언되며, HDInsight 클러스터의 Kafka 버전으로 구성됩니다.

* 플러그 인: Maven 플러그 인은 다양한 기능을 제공합니다. 이 프로젝트에서는 다음 플러그 인이 사용됩니다.

    * `maven-compiler-plugin`: 프로젝트에서 사용하는 Java 버전을 8로 설정하는 데 사용됩니다. HDInsight 3.6에서 사용하는 Java 버전입니다.
    * `maven-shade-plugin`: 이 응용 프로그램 및 모든 종속성을 포함하는 uber jar를 생성하는 데 사용됩니다. 또한 기본 클래스를 지정하지 않고 Jar 파일을 직접 실행할 수 있도록 응용 프로그램의 진입점을 설정하는 데 사용됩니다.

### <a name="producerjava"></a>Producer.java

생산자는 Kafka broker 호스트(작업자 노드)와 통신하여 Kafka 토픽에 데이터를 저장합니다. 다음 코드 조각은 `Producer.java` 파일에서 제공됩니다.

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

이 코드는 Kafka broker 호스트(작업자 노드)와 연결한 다음, 생산자 API를 사용하여 Kafka에 1,000,000개의 문장을 보냅니다.

### <a name="consumerjava"></a>Consumer.java

소비자는 Kafka broker 호스트(작업자 노드)와 통신하고, 루프에서 레코드를 읽습니다. 다음 코드 조각은 `Consumer.java` 파일에서 제공됩니다.

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
        KafkaConsumer<String, String> consumer;
        // Configure the consumer
        Properties properties = new Properties();
        // Point it to the brokers
        properties.setProperty("bootstrap.servers", brokers);
        // Set the consumer group (all consumers must belong to a group).
        properties.setProperty("group.id", groupId);
        // Set how to serialize key/value pairs
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        // When a group is first created, it has no offset stored to start reading from. This tells it to start
        // with the earliest record in the stream.
        properties.setProperty("auto.offset.reset","earliest");
        consumer = new KafkaConsumer<>(properties);

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

이 코드에서 소비자는 토픽의 시작에서 읽도록 구성됩니다(`auto.offset.reset`이 `earliest`로 설정됨.)

### <a name="runjava"></a>Run.java

`Run.java` 파일은 생산자 또는 소비자 코드를 실행하는 명령줄 인터페이스를 제공합니다. 매개 변수로 Kafka broker 호스트 정보를 제공해야 합니다. 필요에 따라 소비자 프로세스에서 사용되는 그룹 ID 값을 포함할 수 있습니다. 동일한 그룹 ID를 사용하여 여러 소비자 인스턴스를 만드는 경우 토픽에서 읽는 부하를 분산합니다.

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

## <a name="build-and-deploy-the-example"></a>예제 빌드 및 배포

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에서 예제를 다운로드합니다.

2. 디렉터리를 `Producer-Consumer` 디렉터리 위치로 변경한 후 다음 명령을 사용합니다.

    ```
    mvn clean package
    ```

    이 명령은 `kafka-producer-consumer-1.0-SNAPSHOT.jar`라는 파일이 포함된 `target`이라는 디렉터리를 만듭니다.

3. 다음 명령을 사용하여 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 파일을 HDInsight 클러스터에 복사합니다.
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자의 암호를 입력합니다.

## <a id="run"></a> 예제 실행

1. 클러스터에 대한 SSH 연결을 열려면 다음 명령을 사용합니다.

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다. HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 이 예제에서 사용되는 Kafka 토픽을 만들려면 다음 단계를 사용합니다.

    1. 변수에 클러스터 이름을 저장하고 유틸리티(`jq`)를 구문 분석하는 JSON을 설치하려면 다음 명령을 사용합니다. 메시지가 표시되면 Kafka 클러스터 이름을 입력합니다.
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Kafka broker 호스트와 Zookeeper 호스트를 가져오려면 다음 명령을 사용합니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. `test` 토픽을 만들려면 다음 명령을 사용합니다.

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```

3. 생산자를 실행하고 토픽에 데이터를 쓰려면 다음 명령을 사용합니다.

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. 생산자가 완료되면 다음 명령을 사용하여 토픽에서 읽습니다.
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    레코드 수와 함께 읽은 레코드가 표시됩니다.

5. __Ctrl+C__를 사용하여 소비자를 종료합니다.

### <a name="multiple-consumers"></a>여러 소비자

Kafka 소비자는 레코드를 읽을 때 소비자 그룹을 사용합니다. 여러 소비자와 같은 그룹을 사용하면 부하가 분산되어 토픽에서 읽습니다. 그룹의 각 소비자는 레코드의 일부를 받습니다.

소비자 응용 프로그램은 그룹 ID로 사용되는 매개 변수를 허용합니다. 예를 들어, 다음 명령은 그룹 ID `mygroup`을 사용하여 소비자를 시작합니다.
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

동작 중인 이 프로세스를 확인하려면 다음 명령을 사용합니다.

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; attach
```

이 명령은 `tmux`를 사용하여 터미널을 두 개의 열로 분할합니다. 소비자는 동일한 그룹 ID 값으로 각 열에서 시작됩니다. 소비자가 읽기를 완료하면 각 읽기는 레코드의 일부입니다. __Ctrl + C __를 두 번 사용하여 `tmux`를 종료합니다.

동일한 그룹 내에서 클라이언트에 의한 소비는 토픽에 대한 파티션을 통해 처리됩니다. 앞에서 만든 `test` 토픽에는 8개의 파티션이 있습니다. 8명의 소비자를 시작하는 경우 각 소비자는 토픽에 대한 단일 파티션에서 레코드를 읽습니다.

> [!IMPORTANT]
> 소비자 그룹에는 파티션보다 더 많은 소비자 인스턴스가 있을 수 없습니다. 이 예제에서 하나의 소비자 그룹은 토픽의 파티션 수이기 때문에 최대 8개 소비자를 포함할 수 있습니다. 또는 소비자가 8개 이하인 소비자 그룹이 여러 개 있을 수 있습니다.

Kafka에 저장된 레코드는 파티션에서 받은 순서대로 저장됩니다. *파티션 내의* 레코드에 대해 순서대로 전달하려면 파티션 수와 일치하는 소비자 인스턴스가 있는 소비자 그룹을 만듭니다. *토픽 내의* 레코드에 대해 순서대로 전달하려면 하나의 소비자 인스턴스만 사용하는 소비자 그룹을 만듭니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight의 Kafka에서 Kafka 생산자 및 소비자 API를 사용하는 방법에 대해 알아보았습니다. Kafka 작업에 대해 자세히 알아보려면 다음을 사용하세요.

* [Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
* [Kafka 클러스터 간 데이터 복제](apache-kafka-mirroring.md)
* [HDInsight의 Kafka 스트림 API](apache-kafka-streams-api.md)