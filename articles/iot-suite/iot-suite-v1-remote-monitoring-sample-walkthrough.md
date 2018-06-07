---
title: 미리 구성된 원격 모니터링 솔루션 연습 | Microsoft Docs
description: Azure IoT에 대한 설명은 원격 모니터링 솔루션 및 해당 아키텍처를 미리 구성합니다.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 3aa9bb9c785bb69c80d9bb33e595393a5a1d220a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>미리 구성된 원격 모니터링 솔루션 연습

[미리 구성된 IoT Suite 원격 모니터링 솔루션][lnk-preconfigured-solutions]은 원격 위치에서 실행되는 여러 컴퓨터에 대한 종단간 모니터링 솔루션을 구현합니다. 솔루션은 비즈니스 시나리오의 제네릭 구현을 제공하는 핵심 Azure 서비스를 결합합니다. 솔루션을 고유한 구현을 위한 출발점으로 사용하고 사용자의 특정 비즈니스 요구 사항을 충족하도록 [사용자 지정][lnk-customize]할 수 있습니다.

이 문서는 작동 방식을 이해할 수 있도록 원격 모니터링 솔루션의 핵심 요소 중 일부를 안내합니다. 이 정보는 다음 항목을 도울 수 있습니다.

* 솔루션의 문제를 해결합니다.
* 솔루션을 사용자 지정하여 고유한 특정 요구 사항을 충족하는 방법을 계획합니다. 
* Azure 서비스를 사용하는 고유한 IoT 솔루션을 디자인합니다.

## <a name="logical-architecture"></a>논리 아키텍처

다음 다이어그램에서는 미리 구성된 솔루션의 논리적 구성 요소를 간략히 보여줍니다.

![논리 아키텍처](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture-updated.png)

## <a name="microservices--docker-containers"></a>마이크로 서비스 및 Docker 컨테이너
원격 모니터링은 마이크로 서비스 아키텍처를 활용하도록 미리 구성된 첫 번째 솔루션입니다. 솔루션은 [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) 및 [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) 모두에서 사용할 수 있습니다.
마이크로 서비스는 개발 속도를 손상시키지 않고 확장성 및 유연성을 달성하기 위해(컨테이너를 개별적으로 크기 조정하도록 허용하여) 널리 알려진 패턴으로 부상했습니다.
마이크로 서비스는 코드를 구분하고 솔루션을 이해하기 쉽고 덜 모놀리식이 되도록 하는 잘 정의된 인터페이스를 제공합니다. 또한 수익화될 수 있는 완성된 솔루션을 빌드하도록 현재 솔루션을 확장하려는 파트너를 위해 추가로 옵션을 확장합니다.

**Docker 컨테이너에 대해 자세히 알아보기**
* [Docker 설치](https://docs.docker.com/engine/installation/)
* [원격 모니터링에 대한 일반적인 Docker 명령](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#common-docker-commands)
* [Docker 시작](https://docs.docker.com/get-started/)

## <a name="simulated-devices"></a>시뮬레이션된 장치

미리 구성된 솔루션에서 시뮬레이션된 장치가 냉각 장치(예: 건물 공조기 또는 시설 공기 처리 장치)를 나타냅니다. 미리 구성된 솔루션을 배포할 때 [Azure WebJob][lnk-webjobs]에서 실행되는 네 개의 시뮬레이션된 장치도 자동으로 프로비전합니다. 시뮬레이션된 장치를 사용하면 물리적 장치를 배포하지 않고도 솔루션의 동작을 쉽게 탐색할 수 있습니다. 실제 물리적 장치를 배포하려면 [미리 구성된 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm] 자습서를 참조하세요.

### <a name="device-to-cloud-messages"></a>장치-클라우드 메시지

시뮬레이션된 장치는 각각 IoT Hub에 다음과 같은 메시지 유형을 보낼 수 있습니다.

| Message | 설명 |
| --- | --- |
| 시작 |장치가 시작되면 백 엔드로 자체에 대한 정보를 포함하는 **장치 정보** 메시지를 보냅니다. 이 데이터에는 장치 ID와 장치에서 지원하는 명령 및 메서드 목록이 포함됩니다. |
| 현재 상태 |장치는 **현재 상태** 메시지를 정기적으로 전송하여 센서의 현재 상태를 감지할 수 있는지 여부를 보고합니다. |
| 원격 분석 |장치의 시뮬레이트된 센서에서 수집된 온도 및 습도에 대한 시뮬레이션된 값을 보고하는 **원격 분석** 메시지를 정기적으로 전송합니다. |

> [!NOTE]
> 솔루션은 장치에서 지원하는 명령의 목록을 장치 쌍이 아니라 Cosmos DB 데이터베이스에 저장합니다.

### <a name="properties-and-device-twins"></a>속성 및 장치 쌍

시뮬레이션된 장치는 *reported 속성*으로 IoT Hub의 [쌍][lnk-device-twins]에 다음 장치 속성을 보냅니다. 장치는 시작 시와 **장치 상태 변경** 명령 또는 메서드에 대한 응답으로 reported 속성을 보냅니다.

| 자산 | 목적 |
| --- | --- |
| Config.TelemetryInterval | 장치가 원격 분석을 전송하는 빈도(초) |
| Config.TemperatureMeanValue | 시뮬레이션된 온도 원격 분석에 대한 평균 값을 지정합니다. |
| Device.DeviceID |솔루션에서 장치를 만들 때 제공되거나 할당되는 ID |
| Device.DeviceState | 장치에서 보고한 상태 |
| Device.CreatedTime |솔루션에서 장치가 만들어진 시간 |
| Device.StartupTime |장치가 시작된 시간 |
| Device.LastDesiredPropertyChange |마지막 desired 속성 변경의 버전 번호 |
| Device.Location.Latitude |장치의 위도 위치 |
| Device.Location.Longitude |장치의 경도 위치 |
| System.Manufacturer |장치 제조업체 |
| System.ModelNumber |장치의 모델 번호 |
| System.SerialNumber |장치의 일련 번호 |
| System.FirmwareVersion |장치 펌웨어의 현재 버전 |
| System.Platform |장치의 플랫폼 아키텍처 |
| System.Processor |장치를 실행하는 프로세서 |
| System.InstalledRAM |장치에 설치된 RAM의 양 |

시뮬레이터는 샘플 값으로 시뮬레이션된 장치에 이러한 속성을 시드합니다. 시뮬레이터가 시뮬레이션된 장치를 초기화할 때마다 장치는 reported 속성으로 IoT Hub에 미리 정의된 메타데이터를 보고합니다. Reported 속성은 장치에 의해 업데이트될 수만 있습니다. reported 속성을 변경하려면 솔루션 포털에서 desired 속성을 설정합니다. 다음은 장치의 책임입니다.

1. IoT Hub에서 desired 속성을 주기적으로 검색합니다.
2. desired 속성 값으로 해당 구성을 업데이트합니다.
3. 새 값을 reported 속성으로 허브에 다시 보냅니다.

솔루션 대시보드에서 [장치 쌍][lnk-device-twins]을 사용하여 장치의 속성을 설정하는 데 *desired 속성*을 사용할 수 있습니다. 일반적으로 장치는 허브에서 desired 속성 값을 읽어 내부 상태를 업데이트하고 reported 속성으로 변경을 다시 보고합니다.

> [!NOTE]
> 시뮬레이션된 장치 코드는 **Desired.Config.TemperatureMeanValue** 및 **Desired.Config.TelemetryInterval** desired 속성만을 사용하여 IoT Hub로 다시 전송된 reported 속성을 업데이트합니다. 시뮬레이션된 장치에서 다른 모든 desired 속성 변경 요청은 무시됩니다.

### <a name="methods"></a>메서드

시뮬레이션된 장치는 IoT Hub를 통해 솔루션 포털에서 호출된 다음 메서드([직접 메서드][lnk-direct-methods])를 처리할 수 있습니다.

| 방법 | 설명 |
| --- | --- |
| InitiateFirmwareUpdate |펌웨어 업데이트를 수행하도록 장치를 지시합니다. |
| Reboot |다시 부팅하도록 장치를 지시합니다. |
| FactoryReset |공장 재설정을 수행하도록 장치를 지시합니다. |

일부 메서드는 reported 속성을 사용하여 진행 상태를 보고합니다. 예를 들어 **InitiateFirmwareUpdate** 메서드는 장치에서 업데이트 실행을 비동기적으로 시뮬레이션합니다. 메서드는 비동기 작업이 reported 속성을 사용하여 솔루션 대시보드에 상태 업데이트를 계속해서 다시 보내는 동안 장치에서 즉시 반환합니다.

### <a name="commands"></a>명령

또한 시뮬레이션된 장치는 IoT Hub를 통해 솔루션 포털에서 보낸 다음 명령(클라우드-장치 메시지)을 처리할 수 있습니다.

| 명령 | 설명 |
| --- | --- |
| PingDevice |장치에 *ping* 을 보내 활성 상태임을 확인합니다. |
| StartTelemetry |장치의 원격 분석 발신을 시작합니다. |
| StopTelemetry |장치의 원격 분석 발신을 차단합니다. |
| ChangeSetPointTemp |임의 데이터가 생성되는 지점 주위의 설정 지점 값을 변경합니다. |
| DiagnosticTelemetry |추가 원격 분석 값(externalTemp)을 보낼 장치 시뮬레이터를 트리거합니다. |
| ChangeDeviceState |장치의 확장된 상태 속성을 변경하고 장치로부터 받은 장치 정보 메시지를 전송합니다. |

> [!NOTE]
> 이러한 명령(클라우드-장치 메시지) 및 메서드(직접 메서드)의 비교는 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub][lnk-iothub]는 장치에서 클라우드로 전송된 데이터를 수집하고 ASA(Azure Stream Analytics) 작업에 사용할 수 있도록 합니다. 각 스트림 ASA 작업은 장치에서 메시지 스트림을 읽는 데 별도 IoT Hub 소비자 그룹을 사용합니다.

또한 솔루션에서 IoT Hub는:

- 포털에 연결하도록 허용되는 모든 장치의 ID 및 인증 키를 저장하는 ID 레지스트리를 유지 관리합니다. ID 레지스트리를 통해 장치를 활성화 및 비활성화할 수 있습니다.
- 솔루션 포털을 대신하여 명령을 장치에 보냅니다.
- 솔루션 포털을 대신하여 장치에서 메서드를 호출합니다.
- 등록된 모든 장치에 대한 장치 쌍을 유지 관리합니다. 장치 쌍은 장치에서 보고한 속성 값을 저장합니다. 또한 장치 쌍은 다음에 연결할 때 검색하는 장치에 대해 솔루션 포털에서 설정한 desired 속성을 저장합니다.
- 여러 장치에 대한 속성을 설정하거나 여러 장치에서 메서드를 호출하는 작업을 예약합니다.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

원격 모니터링 솔루션에서 ASA([Azure Stream Analytics][lnk-asa])는 처리 또는 저장을 위해 다른 백 엔드 구성 요소에 IoT Hub에서 수신한 장치 메시지를 디스패치합니다. 다른 ASA 작업은 메시지의 내용을 기반으로 특정 기능을 수행합니다.

**작업 1: 장치 정보** 는 들어오는 메시지 스트림에서 장치 정보 메시지를 필터링하고 이벤트 허브 끝점으로 보냅니다. 장치는 시작 시 그리고 **SendDeviceInfo** 명령에 반응하여 장치 정보 메시지를 보냅니다. 이 작업은 다음 쿼리 정의를 사용하여 **장치 정보** 메시지를 식별합니다.

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

이 작업은 추가 처리를 위해 이벤트 허브에 해당 출력을 보냅니다.

**작업 2: 규칙** 은 장치 단위 임계값에 대해 들어오는 온도 및 습도 원격 분석 값을 평가합니다. 임계값은 솔루션 포털에서 사용할 수 있는 규칙 편집기에서 설정됩니다. 각 장치/값 쌍은 **참조 데이터**로서 Stream Analytics에서 읽는 Blob의 타임스탬프에 의해 저장됩니다. 작업은 장치에 대해 설정한 임계값에 대해 비지 않은 값을 비교합니다. '>' 조건을 초과하면, 작업은 **경보** 이벤트를 출력하여 임계값을 초과했음을 나타내고 장치, 값 및 타임스탬프 값을 제공합니다. 이 작업은 다음 쿼리 정의를 사용하여 알람을 트리거해야 하는 원격 분석 메시지를 식별합니다.

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

작업은 추가 처리를 위해 이벤트 허브에 해당 출력을 전송하고 솔루션 포털이 경고 정보를 읽을 수 있는 위치에서 Blob 저장소에 각 경고의 세부 정보를 저장합니다.

**작업 3: 원격 분석** 은 두 가지 방식으로 들어오는 장치 원격 분석 스트림에서 작동합니다. 첫 번째 방식은 모든 원격 분석 메시지를 장치에서 장기 저장을 위한 영구 Blob 저장소로 보냅니다. 두 번째 방식은 5분짜리 슬라이딩 윈도우를 통해 평균, 최소 및 최대 습도 값을 계산하고 이 데이터를 Blob 저장소에 보냅니다. 솔루션 포털은 차트를 채우기 위해 Blob 저장소에서 원격 분석 데이터를 참고합니다. 이 작업은 다음과 같은 쿼리 정의를 사용합니다.

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Event Hubs

**장치 정보** 및 **규칙** ASA 작업은 Event Hubs에 해당 데이터를 출력하여 WebJob에서 실행되는 **이벤트 프로세서**에 안전하게 전달합니다.

## <a name="azure-storage"></a>Azure 저장소

솔루션은 Azure blob 저장소를 사용하여 솔루션의 장치에서 모든 원시 데이터 및 요약된 원격 분석 데이터를 유지합니다. 포털은 차트를 채우기 위해 Blob 저장소에서 원격 분석 데이터를 참고합니다. 경고를 표시하려면 솔루션 포털이 원격 분석 값이 구성된 임계값을 초과하는 시기를 기록한 Blob 저장소에서 데이터를 읽습니다. 또한 솔루션은 Blob 저장소를 사용하여 솔루션 포털에서 사용자가 설정한 임계값을 기록합니다.

## <a name="webjobs"></a>웹 작업

장치 시뮬레이터를 호스팅하는 것 외에도 솔루션의 WebJobs는 명령 응답을 처리하는 Azure WebJob에서 실행되는 **이벤트 프로세서**를 호스팅합니다. 명령 응답 메시지를 사용하여 장치 명령 기록(Cosmos DB 데이터베이스에 저장됨)을 업데이트합니다.

## <a name="cosmos-db"></a>Cosmos DB

솔루션은 Cosmos DB 데이터베이스를 사용하여 솔루션에 연결된 장치에 대한 정보를 저장합니다. 이 정보에는 솔루션 포털에서 장치에 전송된 명령 및 솔루션 포털에서 호출된 메서드의 기록이 포함됩니다.

## <a name="solution-portal"></a>솔루션 포털

솔루션 포털은 미리 구성된 솔루션의 일부로 배포된 웹앱입니다. 솔루션 포털의 키 페이지는 대시보드 및 장치 목록입니다.

### <a name="dashboard"></a>대시보드

웹앱의 이 페이지는 PowerBI javascript 컨트롤([PowerBI-시각 리포지토리](https://www.github.com/Microsoft/PowerBI-visuals) 참조)을 사용하여 장치에서 원격 분석 데이터를 시각화합니다. 솔루션은 ASA 원격 분석 작업을 사용하여 Blob 저장소에 원격 분석 데이터를 작성합니다.

### <a name="device-list"></a>장치 목록

솔루션 포털의 이 페이지에서 다음을 수행할 수 있습니다.

* 새 장치를 프로비전합니다. 이 작업은 고유 장치 ID를 설정하고 인증 키를 생성합니다. 장치에 대한 정보를 IoT Hub ID 레지스트리와 솔루션 관련 Cosmos DB 데이터베이스 모두에 기록합니다.
* 장치 속성을 관리합니다. 이 작업은 기본 속성 보기 및 새 속성으로 업데이트를 포함합니다.
* 명령을 장치로 보냅니다.
* 장치의 명령 기록을 봅니다.
* 장치를 활성화하고 비활성화합니다.

## <a name="next-steps"></a>다음 단계

다음 TechNet 블로그 게시물은 미리 구성된 원격 모니터링 솔루션에 대한 더 많은 정보를 제공합니다.

* [IoT 도구 모음 - 내부 살펴보기 - 원격 모니터링](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-v1-under-the-hood-remote-monitoring.aspx)
* [IoT 도구 모음 - 원격 모니터링 - 라이브 및 시뮬레이션된 장치 추가](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-v1-remote-monitoring-adding-live-and-simulated-devices.aspx)

다음 문서를 참조하여 IoT Suite 시작 작업을 계속할 수 있습니다.

* [미리 구성된 원격 모니터링 솔루션 연습][lnk-connect-rm]
* [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
