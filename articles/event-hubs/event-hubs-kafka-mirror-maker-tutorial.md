---
title: Kafka 에코시스템용 Azure Event Hubs에서 Kafka MirrorMaker 사용 | Microsoft Docs
description: Kafka MirrorMaker를 사용하여 Event Hubs에서 Kafka 클러스터를 미러합니다.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 819071321d5609728e7c62abb5b25bf354107850
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941241"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Kafka 에코시스템용 Event Hubs에서 Kafka MirrorMaker 사용

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs)에서 사용할 수 있습니다.

최신 클라우드 크기 조정 앱에 대한 주요 고려 사항 중 하나는 서비스를 중단하지 않고 인프라를 업데이트, 개선 및 변경하는 기능입니다. 이 자습서에서는 Kafka 지원 Event Hub 및 Kafka MirrorMaker에서 Kafka 입력 스트림을 Event Hub 서비스에 "미러"하여 기존 Kafka 파이프라인을 Azure에 통합하는 방법을 보여 줍니다. 

Azure Event Hubs Kafka 엔드포인트를 사용하면 Kafka 프로토콜(즉, Kafka 클라이언트)을 사용하여 Azure Event Hubs에 연결할 수 있습니다. Kafka 응용 프로그램을 최소한으로 변경하면 Azure Event Hubs에 연결하여 Azure 에코시스템의 이점을 누릴 수 있습니다. Kafka 지원 Event Hubs는 현재 Kafka 버전 1.0 이상을 지원합니다.

다음 예에서는 Kafka MirrorMaker를 사용하여 Kafka 지원 이벤트 허브에 Kafka broker를 미러하는 방법을 보여 줍니다.

   ![Event Hubs를 사용하는 Kafka MirrorMaker](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

* Azure 구독. 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [JDK(Java Development Kit) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* Maven 이진 보관 파일을 [다운로드](http://maven.apache.org/download.cgi)하여 [설치](http://maven.apache.org/install.html)합니다.
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

Event Hubs 서비스와 통신하려면 Event Hubs 네임스페이스가 필요합니다. Event Hubs Kafka 엔드포인트를 가져오는 방법에 대한 지침은 [Kafka 지원 Event Hub 만들기](event-hubs-create.md)를 참조하세요. 나중에 사용하기 위해 Event Hubs 연결 문자열을 복사합니다.

## <a name="clone-the-example-project"></a>프로젝트 예제 복제

이제 Kafka 지원 Event Hubs 연결 문자열이 있으므로 Azure Event Hubs 리포지토리를 복제하고 `mirror-maker` 하위 폴더로 이동합니다.

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka 클러스터 설정

[Kafka 빠른 시작 가이드](https://kafka.apache.org/quickstart)를 사용하여 원하는 설정으로 클러스터를 설정하거나 기존 Kafka 클러스터를 사용합니다.

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker를 사용하면 스트림을 "미러"할 수 있습니다. 원본 및 대상 Kafka 클러스터가 지정되면 MirrorMaker는 원본 클러스터에 보낸 모든 메시지를 원본 및 대상 클러스터 모두에서 받을 수 있도록 합니다. 다음 예에서는 대상 Kafka 지원 Event Hub를 사용하여 원본 Kafka 클러스터를 미러하는 방법을 보여 줍니다. 이 시나리오는 데이터 흐름을 중단하지 않고 기존 Kafka 파이프라인에서 Event Hubs로 데이터를 보내는 데 사용할 수 있습니다. 

Kafka MirrorMaker에 대한 자세한 내용은 [Kafka 미러링/MirrorMaker 가이드](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 참조하세요.

### <a name="configuration"></a>구성

Kafka MirrorMaker를 구성하려면 Kafka 클러스터를 소비자/원본으로 지정하고 Kafka 지원 이벤트 허브를 생산자/대상으로 지정합니다.

#### <a name="consumer-configuration"></a>소비자 구성

MirrorMaker에 원본 Kafka 클러스터의 속성을 알려주는 `source-kafka.config` 소비자 구성 파일을 업데이트합니다.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>생산자 구성

이제 MirrorMaker에서 복제된(또는 "미러된") 데이터를 Event Hubs 서비스에 보내도록 지시하는 `mirror-eventhub.config` 생산자 구성 파일을 업데이트합니다. 특히 Event Hubs Kafka 엔드포인트를 가리키도록 `bootstrap.servers`과 `sasl.jaas.config`를 변경합니다. Event Hubs 서비스에는 다음 구성에서 마지막 세 가지 속성을 설정하여 얻을 수 있는 보안 통신(SASL)이 필요합니다. 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>MirrorMaker 실행

새로 업데이트된 구성 파일을 사용하여 루트 Kafka 디렉터리에서 Kafka MirrorMaker 스크립트를 실행합니다. 구성 파일을 루트 Kafka 디렉터리에 복사하거나 다음 명령에서 해당 경로를 업데이트해야 합니다.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

이벤트에서 Kafka 지원 이벤트 허브에 연결하는지 확인하려면 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)의 수신 통계를 검토하거나 이벤트 허브에 대해 소비자를 실행합니다.

MirrorMaker를 실행하면, Kafka 클러스터 및 미러된 Kafka 지원 이벤트 허브 서비스 모두에서 원본 Kafka 클러스터에 보낸 모든 이벤트를 받습니다. MirrorMaker 및 Event Hubs Kafka 엔드포인트를 사용하면, 기존 클러스터를 변경하거나 진행 중인 데이터 흐름을 중단하지 않고도 기존 Kafka 파이프라인을 관리되는 Azure Event Hubs 서비스로 마이그레이션할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Event Hubs에 대해 알아봅니다](event-hubs-what-is-event-hubs.md).
* [Kafka 에코시스템용 Event Hubs에 대해 알아봅니다](event-hubs-for-kafka-ecosystem-overview.md).
* [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 사용하여 온-프레미스 Kafka에서 클라우드의 Kafka 지원 Event Hubs로 이벤트를 스트림합니다.
