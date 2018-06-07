---
title: '자습서: Apache Kafka Streams API 사용 - Azure HDInsight | Microsoft Docs'
description: HDInsight의 Kafka에서 Apache Kafka Streams API를 사용하는 방법에 대해 알아봅니다. 이 API를 사용하면 Kafka에서 토픽 간 스트림 처리를 수행할 수 있습니다.
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
ms.date: 04/17/2018
ms.author: larryfr
ms.openlocfilehash: 8aff28079a0aaa7c02d8a187cb379ecdbedcd854
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-apache-kafka-streams-api"></a>자습서: Apache Kafka Streams API

Kafka Streams API를 사용하는 응용 프로그램을 만들고 HDInsight의 Kafka에서 이를 실행하는 방법을 알아봅니다. 

이 자습서에서 사용되는 응용 프로그램은 스트리밍 워드 카운트입니다. 이 응용 프로그램은 Kafka 토픽에서 텍스트 데이터를 읽고, 개별 단어를 추출한 다음, 워드 카운트를 다른 Kafka 토픽에 저장합니다.

> [!NOTE]
> Kafka 스트림 처리는 종종 Apache Spark 또는 Storm을 사용하여 수행됩니다. Kafka 0.10.0 버전(HDInsight 3.5 및 3.6)에 Kafka Streams API가 도입되었습니다. 이 API를 사용하면 입력 및 출력 토픽 간의 데이터 스트림을 변환할 수 있습니다. 경우에 따라 Spark 또는 Storm 스트리밍 솔루션을 만드는 대신 이 방법을 사용할 수 있습니다. 
>
> Kafka Streams에 대한 자세한 내용은 Apache.org의 [Streams 소개](https://kafka.apache.org/10/documentation/streams/) 문서를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 코드 이해
> * 응용 프로그램 빌드 및 배포
> * Kafka 토픽 구성
> * 코드 실행

## <a name="prerequisites"></a>필수 조건

* HDInsight 3.6 클러스터의 Kafka HDInsight 클러스터에서 Kafka를 만드는 방법을 알아보려면 [HDInsight에서 Kafka 시작](apache-kafka-get-started.md) 설명서를 참조하세요.

* [Kafka 소비자 및 생산자 API](apache-kafka-producer-consumer-api.md) 문서의 단계를 완료합니다. 이 문서의 단계는 이 자습서에서 만든 예제 응용 프로그램 및 토픽을 사용합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

개발 환경에 다음 구성 요소가 설치되어 있어야 합니다.

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 이와 동등한 프로그램(예: OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* SSH 클라이언트 및 `scp` 명령입니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

## <a name="understand-the-code"></a>코드 이해

예제 응용 프로그램은 `Streaming` 하위 디렉터리의 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에 있습니다. 응용 프로그램은 다음 두 파일로 구성되어 있습니다.

* `pom.xml`: 이 파일은 프로젝트 종속성, Java 버전 및 패키징 메서드를 정의합니다.
* `Stream.java`: 이 파일은 스트리밍 논리를 구현합니다.

### <a name="pomxml"></a>Pom.xml

`pom.xml` 파일에서 이해할 중요한 사항은 다음과 같습니다.

* 종속성: 이 프로젝트는 `kafka-clients` 패키지에서 제공하는 Kafka 스트림 API에 의존합니다. 다음 XML 코드는 이 종속성을 정의합니다.

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

    * `maven-compiler-plugin`: 프로젝트에서 사용하는 Java 버전을 8로 설정하는 데 사용됩니다. HDInsight 3.6에는 Java 8이 필요합니다.
    * `maven-shade-plugin`: 이 응용 프로그램 및 모든 종속성을 포함하는 uber jar를 생성하는 데 사용됩니다. 또한 기본 클래스를 지정하지 않고 Jar 파일을 직접 실행할 수 있도록 응용 프로그램의 진입점을 설정하는 데 사용됩니다.

### <a name="streamjava"></a>Stream.java

`Stream.java` 파일은 스트림 API를 사용하여 워드 카운트 응용 프로그램을 구현합니다. 이 파일은 `test`라는 Kafka 토픽에서 데이터를 읽고, `wordcounts`라는 토픽에 워드 카운트를 씁니다.

다음 코드는 워드 카운트 응용 프로그램을 정의합니다.

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>예제 빌드 및 배포

프로젝트를 빌드하고 HDInsight 클러스터의 Kafka에 배포하려면 다음 단계를 사용합니다.

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에서 예제를 다운로드합니다.

2. 디렉터리를 `Streaming` 디렉터리로 변경한 후 다음 명령을 사용하여 jar 패키지를 만듭니다.

    ```bash
    mvn clean package
    ```

    이 명령은 `target/kafka-streaming-1.0-SNAPSHOT.jar`에 패키지를 만듭니다.

3. 다음 명령을 사용하여 `kafka-streaming-1.0-SNAPSHOT.jar` 파일을 HDInsight 클러스터에 복사합니다.
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    `sshuser`은 클러스터의 SSH 사용자로, `clustername`은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다. HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="create-kafka-topics"></a>Kafka 토픽 만들기

1. 클러스터에 대한 SSH 연결을 열려면 다음 명령을 사용합니다.

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    `sshuser`은 클러스터의 SSH 사용자로, `clustername`은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다. HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 변수에 클러스터 이름을 저장하고 유틸리티(`jq`)를 구문 분석하는 JSON을 설치하려면 다음 명령을 사용합니다. 메시지가 표시되면 Kafka 클러스터 이름을 입력합니다.

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. Kafka broker 호스트와 Zookeeper 호스트를 가져오려면 다음 명령을 사용합니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다. 암호를 2번 입력하라는 메시지가 나타납니다.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. 스트리밍 작업에서 사용되는 토픽을 만들려면 다음 명령을 사용합니다.

    > [!NOTE]
    > `test` 토픽이 이미 있다는 오류가 표시될 수 있습니다. 생산자 및 소비자 API 자습서에서 토픽이 만들어졌을 수 있으므로 이것은 정상적인 것입니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    토픽은 다음과 같은 용도로 사용됩니다.

    * `test`: 이 토픽은 레코드가 수신되는 위치입니다. 여기에서 스트리밍 응용 프로그램이 읽습니다.
    * `wordcounts`: 이 토픽은 스트리밍 응용 프로그램이 출력을 저장하는 위치입니다.
    * `RekeyedIntermediateTopic`: 이 토픽은 `countByKey` 연산자에 의해 카운트가 업데이트되므로 데이터를 다시 분할하는 데 사용됩니다.
    * `wordcount-example-Counts-changelog`: 이 토픽은 `countByKey` 작업에서 사용되는 상태 저장소입니다.

    > [!IMPORTANT]
    > HDInsight의 Kafka를 자동으로 토픽을 만들도록 구성할 수도 있습니다. 자세한 내용은 [자동 토픽 만들기 구성](apache-kafka-auto-create-topics.md) 문서를 참조하세요.

## <a name="run-the-code"></a>코드 실행

1. 스트리밍 응용 프로그램을 백그라운드 프로세스로 시작하려면 다음 명령을 사용합니다.

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]
    > log4j에 대한 경고가 발생할 수 있습니다. 이 경고는 무시해도 됩니다.

2. `test` 토픽으로 레코드를 보내려면 다음 명령을 사용하여 생산자 응용 프로그램을 시작합니다.

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

3. 생산자가 완료되면 다음 명령을 사용하여 `wordcounts` 토픽에 저장된 정보를 확인합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]
    > `--property` 매개 변수는 콘솔 소비자에게 개수(값)와 함께 키(단어)를 인쇄하도록 지시합니다. 또한 이 매개 변수는 Kafka에서 이 값을 읽을 때 사용할 역직렬 변환기를 구성합니다.

    다음 텍스트와 유사하게 출력됩니다.
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > 매개 변수 `--from-beggining`은 토픽에 저장된 레코드의 시작 부분에서 소비자가 시작되도록 구성합니다. 단어를 만날 때마다 카운트가 증가하며 토픽은 각 단어에 대해 카운트를 늘리는 여러 항목을 포함합니다.

7. __Ctrl + C__를 사용하여 생산자를 종료합니다. __Ctrl + C__를 한 번 더 사용하여 응용 프로그램 및 소비자를 종료합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight의 Kafka에서 Kafka Streams API를 사용하는 방법에 대해 알아보았습니다. Kafka 작업에 대해 자세히 알아보려면 다음을 사용하세요.

* [Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
* [Kafka 클러스터 간 데이터 복제](apache-kafka-mirroring.md)
