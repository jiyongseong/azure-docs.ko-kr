---
title: Kafka 에코시스템용 Azure Event Hubs | Microsoft Docs
description: Kafka 사용 Azure Event Hubs 개요 및 소개
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077609"
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Kafka 에코시스템용 Event Hubs

Kafka 에코시스템용 Event Hubs는 기존 Kafka 기반 응용 프로그램이 사용자 고유의 Kafka 클러스터 실행에 대한 대안으로 사용할 수 있는 Kafka 엔드포인트를 제공합니다. Kafka 에코시스템용 Event Hubs는 [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) 및 최신 클라이언트 버전을 지원하고 MirrorMaker를 포함하여 기존 Kafka 응용 프로그램과 작동합니다. Kafka 프로토콜을 Event Hubs에 사용하는 응용 프로그램에서 스트리밍 이벤트를 시작하고 연결 문자열을 변경합니다.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>Kafka 에코시스템용 Event Hubs는 무엇을 제공합니까?

Kafka 에코시스템용 Event Hubs는 Kafka 버전 1.0과 호환가능한 이진이며 나중에 Kafka 토픽에서 읽기 및 쓰기를 위한 Azure Event Hubs에 프로토콜 헤드를 제공합니다. 개념적으로 Kafka와 Event Hubs는 거의 동일합니다. 둘 다 스트리밍 데이터를 위해 빌드한 분할된 로그입니다. 다음 표에서 Kafka 및 Event Hubs 간 개념을 매핑합니다.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 및 Event Hubs의 개념적 매핑

| Kafka 개념 | Event Hubs 클러스터|
| --- | --- |
| 프로비전 | 네임스페이스 |
| 항목 | Event Hubs |
| 파티션 | 파티션|
| 소비자 그룹 | 소비자 그룹 |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka 및 Event Hubs의 주요 차이점

[Apache Kafka](https://kafka.apache.org/)는 선택한 어디서든 실행할 수 있는 소프트웨어인 반면 Event Hubs는 Azure Blob Storage와 유사한 클라우드 서비스입니다. 관리할 네트워크나 서버가 없으며 구성할 브로커가 없습니다. 항목이 포함된 FQDN인 네임스페이스를 만든 다음, 해당 네임스페이스 내에 항목 또는 Event Hubs를 만듭니다. Event Hubs 및 네임스페이스에 대한 자세한 내용은 [Event Hubs란?](event-hubs-what-is-event-hubs.md)을 참조하세요. 클라우드 서비스로서 Event Hubs는 안정적인 단일 가상 IP 주소를 엔드포인트로 사용하므로 클라이언트는 클러스터 내에서 브로커 또는 컴퓨터에 대해 알 필요가 없습니다. 

Event Hubs에서 확장은 구매한 처리량 단위 수에 의해 제어되며, 각 처리량 단위는 초당 1MB 또는 초당 이벤트 1000개를 수신할 수 있습니다. 기본적으로 Event Hubs는 [자동 확장](event-hubs-auto-inflate.md) 기능으로 한계에 도달할 경우 처리량 단위를 확장합니다. 이 기능은 Kafka 에코시스템용 Event Hubs와 함께 작동합니다. 

### <a name="security-and-authentication"></a>보안 및 인증

Azure Event Hubs는 모든 통신에 SSL 또는 TLS를 필요로 하며 인증을 위해 SAS(공유 액세스 서명)를 사용합니다. 이 요구 사항은 Event Hubs 내의 Kafka 엔드포인트에 대해서도 마찬가지입니다. Kafka와 호환성을 위해 Event Hubs는 인증에 SASL PLAIN 그리고 전송 보안에 SASL SSL을 사용합니다. Event Hubs의 보안에 대한 자세한 내용은 [Event Hubs 인증 및 보안](event-hubs-authentication-and-security-model-overview.md)을 참조합니다.

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka에 사용할 수 있는 기타 Event Hubs 기능

Kafka 에코시스템용 Event Hubs를 사용하면 한 프로토콜로 읽고 다른 프로토콜로 쓸 수 있으므로 현재 Kafka 생산자는 Kafka를 통해 게시를 계속할 수 있으며 Azure Stream Analytics 또는 Azure Functions 같은 Event Hubs를 사용하여 판독기를 추가할 수도 있습니다. 또한 [캡처](event-hubs-capture-overview.md) 및 [지역 재해 복구](event-hubs-geo-dr.md) 같은 Event Hubs 기능은 Kafka 에코시스템용 Event Hubs와 함께 작동합니다.

## <a name="features-that-are-not-supported-in-the-preview"></a>미리 보기에서 지원되지 않는 기능

Kafka 에코시스템 통합용 Event Hubs의 공개 미리 보기의 경우 다음과 같은 Kafka 기능이 지원되지 않습니다.

*   Idempotent 생산자
*   트랜잭션
*   압축
*   크기 기반 보존
*   로그 압축
*   파티션을 기존 항목에 추가
*   HTTP Kafka API 지원
*   Kafka 연결
*   Kafka 스트림

## <a name="next-steps"></a>다음 단계

이 문서는 Kafka 에코시스템용 Event Hubs에 대한 소개를 제공합니다. 자세한 알아보려면 다음 링크를 참조하세요.

* [Kafka 사용 Event Hubs 만드는 방법](event-hubs-create-kafka-enabled.md)
* [Kafka 응용 프로그램에서 이벤트 허브로 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Event Hubs 자습서](event-hubs-dotnet-standard-getstarted-send.md) 시작
* [Event Hubs FAQ](event-hubs-faq.md)

 
 

