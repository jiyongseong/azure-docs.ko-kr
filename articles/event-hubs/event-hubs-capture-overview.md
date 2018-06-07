---
title: Azure Event Hubs 캡처 개요 | Microsoft Docs
description: Event Hubs 캡처로 원격 분석 데이터 캡처
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 00eee302cc15d94ec62f5f3332e18ee2df24f5cd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="azure-event-hubs-capture"></a>Azure Event Hubs 캡처

Azure Event Hubs 캡처를 사용하면 시간 또는 크기 간격을 유연하게 지정하여 Event Hubs의 스트리밍 데이터를 선택한 [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 또는 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 계정에 자동으로 전달할 수 있습니다. 캡처는 빠르게 설정할 수 있으며 실행을 위한 관리 비용이 없고 Event Hubs [처리량 단위](event-hubs-features.md#capacity)에 따라 크기가 자동으로 조정됩니다. Event Hubs 캡처는 스트리밍 데이터를 Azure에 로드하는 가장 쉬운 방법이며 데이터 캡처보다 데이터 처리에 집중할 수 있게 해줍니다.

Event Hubs 캡처를 사용하면 동일한 스트림에서 실시간 및 일괄 처리 기반 파이프라인을 처리할 수 있습니다. 즉, 시간이 지나면서 요구에 따라 확장되는 솔루션을 빌드할 수 있습니다. 향후 실시간 처리를 염두에 두고 현재 일괄 처리 기반 시스템을 빌드 중이든, 기존의 실시간 솔루션에 효율적인 콜드 경로를 추가하려는 경우든 간에 Event Hubs 캡처를 통해 스트리밍 데이터 작업이 더 쉬워집니다.

## <a name="how-event-hubs-capture-works"></a>Event Hubs 캡처의 작동 방식

Event Hubs는 원격 분석 수신에 대한 시간 보존 지속형 버퍼이며 분산된 로그와 비슷합니다. Event Hubs 크기 조정의 핵심은 [분할된 소비자 모델](event-hubs-features.md#partitions)입니다. 각 파티션은 데이터의 독립적인 세그먼트이며 독립적으로 사용됩니다. 시간이 지나면서 이 데이터는 구성 가능한 보존 기간에 따라 에이징됩니다. 결과적으로 지정된 이벤트 허브는 "꽉 참" 상태가 되지 않습니다.

Event Hubs 캡처를 사용하면 캡처된 데이터를 저장하는 데 사용되는 고유한 Azure Blob Storage 계정 및 컨테이너 또는 Azure Data Lake Store 계정을 지정할 수 있습니다. 이러한 계정은 이벤트 허브와 동일한 지역에 있을 수도 있고 다른 지역에 있을 수도 있으므로 Event Hubs 캡처 기능의 유연성이 향상됩니다.

캡처된 데이터는 [Apache Avro][Apache Avro] 형식으로 작성되는데, 이는 인라인 스키마가 있는 풍부한 데이터 구조를 제공하는 간단하고 빠른 이진 형식입니다. 이 형식은 Hadoop 에코시스템, Stream Analytics 및 Azure Data Factory에서 널리 사용됩니다. Avro 작업에 대한 자세한 내용은 이 문서의 뒷부분을 참조하세요.

### <a name="capture-windowing"></a>캡처 기간 이동

Event Hubs 캡처를 사용하면 기간을 설정하여 캡처를 제어할 수 있습니다. 이 기간은 발견된 첫 번째 트리거에서 캡처 작업이 발생하도록 하는 "첫 번째 우선 정책"이 포함된 최소 크기 및 시간 구성입니다. 15분/100MB 캡처 기간을 사용하고 초당 1MB로 전송할 경우 크기 기간이 시간 기간보다 먼저 트리거됩니다. 각 파티션이 독립적으로 캡처하며, 캡처 시점에 완료된 블록 Blob을 작성하고 캡처 간격이 발생한 시간을 따라 명명합니다. 저장소 명명 규칙은 다음과 같습니다.

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

날짜 값은 0으로 채워집니다. 파일 이름의 예는 다음과 같습니다.

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>처리량 단위로 크기 조정

Event Hubs 트래픽은 [처리량 단위](event-hubs-features.md#capacity)로 제어됩니다. 단일 처리량 단위는 초당 1MB 또는 초당 1000개의 이벤트 수신을 허용하고 송신량은 그 두 배입니다. Standard Event Hubs는 1-20개의 처리량 단위로 구성할 수 있으며 할당량 증가 [지원 요청][support request]을 통해 더 구입할 수 있습니다. 구입한 처리량 단위 범위를 벗어나는 사용량은 제한됩니다. Event Hubs 캡처는 내부 Event Hubs 저장소에서 데이터를 직접 복사하여 처리량 단위 송신 할당량을 우회하고 Stream Analytics, Spark 등의 다른 처리 판독기를 위해 송신 내용을 저장합니다.

Event Hubs 캡처가 구성되면 첫 번째 이벤트를 전송하는 즉시 자동으로 실행되어 계속 실행됩니다. 다운스트림 처리 시 프로세스가 제대로 작동하는지 쉽게 알 수 있도록 Event Hubs는 데이터가 없을 경우 빈 파일을 작성합니다. 이 프로세스는 일괄 처리 프로세서에 제공할 수 있는 예측 가능한 주기와 표식을 제공합니다.

## <a name="setting-up-event-hubs-capture"></a>Event Hubs 캡처 설정

이벤트 허브 생성 시 [Azure Portal](https://portal.azure.com) 또는 Azure Resource Manager 템플릿을 사용하여 캡처를 구성할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](event-hubs-capture-enable-through-portal.md)
- [Azure Resource Manager 템플릿을 사용하여 하나의 이벤트 허브가 있는 Event Hubs 네임스페이스를 만들고 캡처를 사용하도록 설정](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>캡처된 파일 탐색 및 Avro 작업

Event Hubs 캡처는 구성된 기간에 지정된 대로 Avro 형식의 파일을 만듭니다. [Azure Storage 탐색기][Azure Storage Explorer]와 같은 도구에서 이러한 파일을 볼 수 있습니다. 파일을 로컬로 다운로드하여 작업할 수 있습니다.

Event Hubs 캡처에서 생성된 파일에는 다음과 같은 Avro 스키마가 있습니다.

![][3]

Avro 파일을 쉽게 탐색하는 방법은 Apache에서 [Avro 도구][Avro Tools] jar을 사용하는 것입니다. 이 jar을 다운로드한 후 다음 명령을 실행하여 특정 Avro 파일의 스키마를 확인할 수 있습니다.

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

이 명령은 다음을 반환합니다.

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

또한 Avro 도구를 사용하여 JSON 형식으로 파일을 변환하고 다른 처리를 수행할 수 있습니다.

고급 처리를 수행하려면 선택한 플랫폼에 대한 Avro를 다운로드하고 설치합니다. 이 문서 작성 시점에는 C, C++, C\#, Java, NodeJS, Perl, PHP, Python 및 Ruby에 사용 가능한 구현이 있습니다.

Apache Avro에는 [Java][Java] 및 [Python][Python]에 대한 전체 시작 가이드가 있습니다. [Event Hubs 캡처 시작](event-hubs-capture-python.md) 문서를 참조할 수도 있습니다.

## <a name="how-event-hubs-capture-is-charged"></a>Event Hubs 캡처의 요금 부과 방식

Event Hubs 캡처는 처리량 단위와 유사하게 시간당 요금으로 측정됩니다. 요금은 네임스페이스에 대해 구입한 처리량 단위 수에 정비례합니다. 처리량 단위가 증가 및 감소함에 따라 성능이 일치하도록 Event Hubs 캡처도 증가 및 감소합니다. 측정은 동시에 발생합니다. 가격 정보는 [Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

Event Hubs 캡처는 데이터를 Azure로 가져오는 가장 쉬운 방법입니다. Azure Data Lake, Azure Data Factory 및 Azure HDInsight를 통해 규모에 관계없이 선택한 도구 및 플랫폼을 사용하여 선택한 일괄 처리 및 기타 분석을 수행할 수 있습니다.

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [이벤트 전송 및 수신 시작](event-hubs-dotnet-framework-getstarted-send.md)
* [Event Hubs 개요][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
