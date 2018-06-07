---
title: Azure Stream Analytics에 입력으로 데이터 스트리밍
description: Azure Stream Analytics에서 데이터 연결을 설정하는 방법을 알아보세요. 입력에는 이벤트의 데이터 스트림과 참조 데이터가 포함되어 있습니다.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 2b2ef68622f96d87a25d203d3d67aa0877397072
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream Analytics에 입력으로 데이터 스트리밍

Stream Analytics는 세 종류 리소스의 입력으로 Azure 데이터 스트림과 최고급 통합을 수행합니다.
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 저장소](https://azure.microsoft.com/services/storage/blobs/) 

이러한 입력 리소스는 Stream Analytics 작업과 동일한 Azure 구독 또는 다른 구독에 존재할 수 있습니다.

### <a name="compression"></a>압축
Stream Analytics는 모든 데이터 스트림 입력 원본에서 압축을 지원합니다. 현재 지원되는 참조 형식은 None, GZip 및 Deflate 압축입니다. 참조 데이터에는 압축이 지원되지 않습니다. 입력 형식이 압축된 Avro 데이터인 경우 투명하게 처리됩니다. Avro serialization에서는 압축 형식을 지정할 필요가 없습니다. 

## <a name="create-or-edit-inputs"></a>입력 만들기 또는 편집
새 입력을 만들고 스트리밍 작업에서 기존 입력을 나열하거나 편집하려면 Azure Portal을 사용할 수 있습니다.
1. [Azure Portal](https://portal.azure.com)을 열어 Stream Analytics 작업을 찾아 선택합니다.
2. **SETTINGS** 제목 아래에서 **입력** 옵션을 선택합니다. 
4. **입력** 페이지에는 기존 입력이 나열됩니다. 
5. **입력** 페이지에서 **스트림 입력 추가** 또는 **참조 입력 추가**을 선택하여 세부 정보 페이지를 엽니다.
6. 기존 입력을 선택하여 세부 사항을 편집하고 **저장**을 선택하여 기존 입력을 편집합니다.
7. 입력 세부 사항 페이지에서 **테스트**를 선택하여 연결 옵션이 유효하고 작동하는지 확인합니다. 
8. 기존 입력의 이름을 마우스 오른쪽 단추로 클릭하고 추가 테스트를 위해 필요에 따라 **입력의 예제 데이터**를 선택합니다.


## <a name="stream-data-from-event-hubs"></a>이벤트 허브에서 데이터 스트리밍

Azure Event Hubs는 확장성 있는 게시-구독 이벤트 수집기를 제공합니다. 이벤트 허브는 초당 수백만 개의 이벤트를 수집할 수 있으므로 연결된 장치와 응용 프로그램이 생성하는 대량의 데이터를 처리하고 분석할 수 있습니다. 이벤트 허브 및 Stream Analytics는 실시간 분석을 위한 종단간 솔루션을 함께 제공합니다. 이벤트 허브를 사용하면 이벤트를 실시간으로 Azure에 공급할 수 있으며 Stream Analytics 작업은 해당 이벤트를 실시간으로 처리할 수 있습니다. 예를 들어 Event Hubs에 웹 클릭, 센서 판독값 또는 온라인 로그 이벤트를 보낼 수 있습니다. 그런 다음 실시간 필터링, 집계 및 상관 관계에 대한 입력 데이터 스트림으로 Event Hubs를 사용하도록 Stream Analytics 작업을 만들 수 있습니다.

Stream Analytics의 Event Hubs에서 오는 이벤트의 기본 타임스탬프가 `EventEnqueuedUtcTime`인 이벤트 허브에 이벤트가 도착한 타임스탬프입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

### <a name="consumer-groups"></a>소비자 그룹
각 Stream Analytics 이벤트 허브 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 셀프 조인이 포함되거나 입력이 여러 개인 경우 둘 이상의 판독기 다운스트림으로 일부 입력을 읽을 수 있습니다. 이러한 상황은 단일 소비자 그룹의 판독기 수에 영향을 줍니다. 파티션당 소비자 그룹마다 5개 판독기의 Event Hubs 한도 초과를 방지하려면 각 Stream Analytics 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다. 또한 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [이벤트 허브 수신기로 Azure Stream Analytics 문제 해결](stream-analytics-event-hub-consumer-groups.md)을 참조하세요.

### <a name="stream-data-from-event-hubs"></a>이벤트 허브에서 데이터 스트리밍
다음 표는 이벤트 허브에서 데이터 입력을 스트리밍하는 Azure Portal의 **새 입력** 페이지의 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** |이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
| **구독** | 이벤트 허브 리소스가 있는 구독을 선택합니다. | 
| **이벤트 허브 네임스페이스** | 이벤트 허브 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 네임스페이스도 만듭니다. |
| **이벤트 허브 이름** | 입력으로 사용할 이벤트 허브의 이름입니다. |
| **이벤트 허브 정책 이름** | 이벤트 허브에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 허브 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다.|
| **이벤트 허브 소비자 그룹**(권장) | 각 Stream Analytics 작업마다 고유한 소비자 그룹을 사용하는 것이 좋습니다. 이 문자열은 이벤트 허브에서 데이터를 수집하는 데 사용할 소비자 그룹입니다. 소비자 그룹이 지정되지 않으면 Stream Analytics 작업에서 $Default 소비자 그룹을 사용합니다.  |
| **이벤트 직렬화 형식** | 들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다.  JSON 형식이 사양을 준수하고 10진수 앞에 0이 없는지 확인하세요. |
| **Encoding** | 현재 유일하게 지원되는 인코딩 형식은 UTF-8입니다. |
| **이벤트 압축 유형** | 들어오는 데이터 스트림을 읽는 데 사용되는 압축 유형입니다(예: None(기본값), GZip 또는 Deflate). |

데이터가 이벤트 허브 스트림 입력에서 오는 경우 Stream Analytics 쿼리의 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **EventProcessedUtcTime** |이벤트가 Stream Analytics으로 처리되는 날짜 및 시간입니다. |
| **EventEnqueuedUtcTime** |Event Hubs에서 이벤트를 받은 날짜 및 시간입니다. |
| **PartitionId** |입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 이러한 필드를 사용하여 다음 예제와 같은 쿼리를 작성할 수 있습니다.

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Event Hub를 IoT Hub Route의 끝점으로 사용하는 경우에는 [GetMetadataPropertyValue 함수](https://msdn.microsoft.com/library/azure/mt793845.aspx)를 사용하여 IoT Hub 메타데이터에 액세스할 수 있습니다.
> 

## <a name="stream-data-from-iot-hub"></a>IoT Hub에서 데이터 스트리밍
Azure Iot 허브는 IoT 시나리오에 최적화된, 확장성이 뛰어난 게시-구독 이벤트 처리기입니다.

Stream Analytics의 IoT Hub에서 오는 이벤트의 기본 타임스탬프는 이벤트가 IoT Hub에 도착한 타임스탬프이며, 이는 `EventEnqueuedUtcTime`입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

> [!NOTE]
> `DeviceClient` 속성으로 전송된 메시지만 처리할 수 있습니다.
> 

### <a name="consumer-groups"></a>소비자 그룹
각 Stream Analytics IoT Hub 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 셀프 조인이 포함되거나 입력이 여러 개인 경우 둘 이상의 판독기 다운스트림으로 일부 입력을 읽을 수 있습니다. 이러한 상황은 단일 소비자 그룹의 판독기 수에 영향을 줍니다. 파티션당 소비자 그룹마다 5개 판독기의 Azure IoT Hub 한도 초과를 방지하려면 각 Stream Analytics 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>데이터 스트림 입력으로 IoT Hub 구성
다음 표에서는 스트림 입력으로 IoT Hub를 구성할 때 Azure Portal의 **새 입력** 페이지에서 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다.|
| **구독** | IoT Hub 리소스가 있는 구독을 선택합니다. | 
| **IoT 허브** | 입력으로 사용할 IoT Hub의 이름입니다. |
| **끝점** | IoT Hub에 대한 엔드포인트입니다.|
| **공유 액세스 정책 이름** | IoT Hub에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| **공유 액세스 정책 키** | IoT Hub에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다.  IoT Hub 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. |
| **소비자 그룹** | 각 Stream Analytics 작업마다 서로 다른 소비자 그룹을 사용하는 것이 좋습니다. 소비자 그룹은 IoT Hub에서 데이터를 수집하는 데 사용됩니다. Stream Analytics에서는 달리 지정하지 않는 한 $Default 소비자 그룹을 사용합니다.  |
| **이벤트 직렬화 형식** | 들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다.  JSON 형식이 사양을 준수하고 10진수 앞에 0이 없는지 확인하세요. |
| **Encoding** | 현재 유일하게 지원되는 인코딩 형식은 UTF-8입니다. |
| **이벤트 압축 유형** | 들어오는 데이터 스트림을 읽는 데 사용되는 압축 유형입니다(예: None(기본값), GZip 또는 Deflate). |


IoT Hub에서 스트림 데이터를 사용하는 경우 Stream Analytics 쿼리에서 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **EventProcessedUtcTime** | 이벤트가 처리되는 날짜 및 시간입니다. |
| **EventEnqueuedUtcTime** | IoT 허브에서 이벤트를 받은 날짜 및 시간입니다. |
| **PartitionId** | 입력 어댑터의 0부터 시작하는 파티션 ID입니다. |
| **IoTHub.MessageId** | IoT Hub에서 양방향 통신을 상호 연결하는 데 사용되는 ID입니다. |
| **IoTHub.CorrelationId** | IoT Hub에서 메시지 응답 및 피드백에 사용되는 ID입니다. |
| **IoTHub.ConnectionDeviceId** | 이 메시지를 보내는 데 사용된 인증 ID입니다. 이 값은 IoT Hub에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| **IoTHub.ConnectionDeviceGenerationId** | 이 메시지를 보내는 데 사용된 인증된 장치의 생성 ID입니다. 이 값은 IoT Hub에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| **IoTHub.EnqueuedTime** | IoT Hub에서 메시지가 수신된 시간입니다. |
| **IoTHub.StreamId** | 보낸 사람 장치에 의해 추가된 사용자 지정 이벤트 속성입니다. |


## <a name="stream-data-from-blob-storage"></a>Blob Storage에서 데이터 스트리밍
클라우드에 저장할 구조화되지 않은 대량 데이터가 있는 시나리오에서 Azure Blob Storage는 비용 효과적이고 확장성 있는 솔루션을 제공합니다. 일반적으로 Blob Storage의 데이터는 미사용 데이터로 간주됩니다. 그러나 Blob 데이터는 Stream Analytics에서 데이터 스트림으로 처리될 수 있습니다. 

로그 처리는 Stream Analytics와 함께 Blob Storage 입력을 사용하기 위해 일반적으로 사용되는 시나리오입니다. 이 시나리오에서는 시스템에서 원격 분석 데이터 파일이 캡처되고 유의미한 데이터를 추출하기 위해 구문 분석 및 처리되어야 합니다.

Stream Analytics에서 Blob Storage 이벤트의 기본 타임 스탬프는 Blob이 마지막으로 수정된 타임스탬프로 `BlobLastModifiedUtcTime`입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

CSV 형식의 입력은 데이터 집합용 필드를 정의하기 위해 헤더 행이 *필요*하며, 모든 헤드 행 필드는 고유해야 합니다.

Stream Analytics는 현재 이벤트 허브 캡처 또는 IoT Hub Azure Storage 컨테이너 사용자 지정 엔드포인트에 의해 생성된 AVRO 메시지의 비직렬화를 지원하지 않습니다.

> [!NOTE]
> Stream Analytics에서는 기존 blob 파일에 콘텐츠를 추가할 수 없습니다. Stream Analytics에서는 각 파일을 한 번만 보며 작업에서 데이터를 읽은 후 파일에서 발생한 모든 변경 내용은 처리되지 않습니다. Blob 파일에 대한 모든 데이터를 한 번에 업로드한 후 다른 새 Blob 파일에 최신 이벤트를 추가하는 것이 좋습니다.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>스트림 입력으로 Blob Storage 구성 

다음 표에서는 스트림 입력으로 Blob Storage를 구성할 때 Azure Portal의 **새 입력** 페이지에서 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
| **구독** | IoT Hub 리소스가 있는 구독을 선택합니다. | 
| **Storage 계정** | Blob 파일이 위치한 저장소 계정의 이름입니다. |
| **Storage 계정 키** | 저장소 계정과 연결된 비밀 키입니다. Blob Storage 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. |
| **컨테이너** | Blob 입력에 대한 컨테이너입니다. 컨테이너는 Microsoft Azure Blob service에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Azure Blob Storage 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다. **기존 컨테이너 사용** 또는 **새로 만들기**를 선택하여 새 컨테이너를 만들 수 있습니다.|
| **경로 패턴**(선택 사항) | 지정된 컨테이너 내에서 Blob을 찾는 데 사용되는 파일 경로입니다. 경로 내에서 세 변수(`{date}`, `{time}`, `{partition}`)의 인스턴스 중 하나 이상을 지정할 수도 있습니다.<br/><br/>예 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>예 2: `cluster1/logs/{date}`<br/><br/>`*` 문자는 경로 접두사에 대해 허용된 값이 아닙니다. 유효한 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob 문자</a>만 허용됩니다. |
| **날짜 형식**(선택 사항) | 경로에서 날짜 변수를 사용하는 경우 파일이 구성된 날짜 형식입니다. 예제: `YYYY/MM/DD` |
| **시간 형식**(선택 사항) |  경로에서 시간 변수를 사용하는 경우 파일이 구성된 시간 형식입니다. 현재 지원되는 유일한 값은 몇 시간 동안 `HH`입니다. |
| **이벤트 직렬화 형식** | 들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다.  JSON 형식이 사양을 준수하고 10진수 앞에 0이 없는지 확인하세요. |
| **Encoding** | CSV 및 JSON의 경우 UTF-8이 현재 지원되는 유일한 인코딩 형식입니다. |
| **압축** | 들어오는 데이터 스트림을 읽는 데 사용되는 압축 유형입니다(예: None(기본값), GZip 또는 Deflate). |

데이터를 Blob Storage 원본에서 가져온 경우 Stream Analytics 쿼리에서 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **BlobName** |이 이벤트가 발생한 입력 Blob의 이름입니다. |
| **EventProcessedUtcTime** |이벤트가 Stream Analytics으로 처리되는 날짜 및 시간입니다. |
| **BlobLastModifiedUtcTime** |Blob이 마지막으로 수정된 날짜 및 시간입니다. |
| **PartitionId** |입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 이러한 필드를 사용하여 다음 예제와 같은 쿼리를 작성할 수 있습니다.

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
