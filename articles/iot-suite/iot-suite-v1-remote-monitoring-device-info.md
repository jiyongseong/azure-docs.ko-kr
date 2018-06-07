---
title: "원격 모니터링 솔루션의 장치 정보 메타데이터 | Microsoft Docs"
description: "Azure IoT에 대한 설명은 원격 모니터링 솔루션 및 해당 아키텍처를 미리 구성합니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 5ada9413b1bb0923df96cfd93c3a0a875e80dd28
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>미리 구성된 원격 모니터링 솔루션의 장치 정보 메타데이터

미리 구성된 Azure IoT Suite 원격 모니터링 솔루션은 장치 메타데이터를 관리하기 위한 방식을 보여 줍니다. 이 문서에서는 이 솔루션이 취하는 방식을 이해할 수 있도록 설명합니다.

* 솔루션이 저장한 장치 메타데이터입니다.
* 솔루션이 장치 메타데이터를 관리하는 방법입니다.

## <a name="context"></a>Context

미리 구성된 원격 모니터링 솔루션은 [Azure IoT Hub][lnk-iot-hub]를 사용하여 장치가 클라우드로 데이터를 보낼 수 있도록 합니다. 솔루션은 세 개의 서로 다른 위치에 장치에 대한 정보를 저장합니다.

| 위치 | 저장된 정보 | 구현 |
| -------- | ------------------ | -------------- |
| ID 레지스트리 | 장치 ID, 인증 키, 사용 상태 | IoT Hub에 기본 제공 |
| 장치 쌍 | 메타데이터: reported 속성, desired 속성, 태그 | IoT Hub에 기본 제공 |
| Cosmos DB | 명령 및 메서드 기록 | 솔루션에 대한 사용자 지정 |

IoT Hub는 IoT Hub에 대한 액세스를 관리하는 [장치 ID 레지스트리][lnk-identity-registry]를 포함하고 [장치 쌍][lnk-device-twin]을 사용하여 장치 메타데이터를 관리합니다. 명령 및 메서드 기록을 저장하는 원격 모니터링 솔루션 특정 *장치 레지스트리*도 있습니다. 원격 모니터링 솔루션은 [Cosmos DB][lnk-docdb] 데이터베이스를 사용하여 명령 및 메서드 기록에 대한 사용자 지정 저장소를 구현합니다.

> [!NOTE]
> 미리 구성된 원격 모니터링 솔루션은 Cosmos DB 데이터베이스의 정보와 동기화된 장치 ID 레지스트리를 유지합니다. 둘 다 동일한 장치 ID를 사용하여 IoT Hub에 연결된 각 장치를 고유하게 식별합니다.

## <a name="device-metadata"></a>장치 메타데이터

IoT Hub는 원격 모니터링 솔루션에 연결된 각 시뮬레이션된 장치 및 물리적 장치에 대한 [장치 쌍][lnk-device-twin]을 유지 관리합니다. 솔루션은 장치 쌍을 사용하여 장치와 연결된 메타데이터를 관리합니다. 장치 쌍은 IoT Hub에서 유지 관리하는 JSON 문서이며 솔루션은 IoT Hub API를 사용하여 장치 쌍과 상호 작용합니다.

장치 쌍은 세 가지 유형의 메타데이터를 저장합니다.

- 장치에서 *Reported 속성*이 IoT Hub로 전송됩니다. 원격 모니터링 솔루션에서 시뮬레이션된 장치는 시작 시와 **장치 상태 변경** 명령 및 메서드에 대한 응답으로 reported 속성을 전송합니다. 솔루션 포털의 **장치 목록** 및 **장치 세부 정보**에서 reported 속성을 볼 수 있습니다. reported 속성은 읽기 전용입니다.
- 장치에 의해 *desired 속성*이 IoT Hub에서 검색됩니다. 장치에 대한 모든 필요한 구성을 변경하는 것은 장치의 책임입니다. reported 속성으로 허브에 변경 내용을 보고하는 것도 장치의 책임입니다. 솔루션 포털을 통해 desired 속성 값을 설정할 수 있습니다.
- *태그*는 장치 쌍에만 존재하며 장치와 동기화되지 않습니다. 솔루션 포털에서 태그 값을 설정하고 장치 목록을 필터링 할 때 사용할 수 있습니다. 또한 솔루션은 태그를 사용하여 솔루션 포털에서 장치에 대해 표시할 아이콘을 식별합니다.

시뮬레이션된 장치의 예제 reported 속성은 제조업체, 모델 번호, 위도 및 경도를 포함합니다. 시뮬레이션된 장치는 reported 속성으로 지원되는 메서드의 목록도 반환합니다.

> [!NOTE]
> 시뮬레이션된 장치 코드는 **Desired.Config.TemperatureMeanValue** 및 **Desired.Config.TelemetryInterval** desired 속성만을 사용하여 IoT Hub로 다시 전송된 reported 속성을 업데이트합니다. 다른 모든 desired 속성 변경 요청은 무시됩니다.

장치 레지스트리 Cosmos DB 데이터베이스에 저장된 장치 정보 메타데이터 JSON 문서는 다음과 같은 구조입니다.

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> 장치 정보는 장치가 IoT Hub에 전송하는 원격 분석을 설명하는 메타데이터를 포함할 수도 있습니다. 원격 모니터링 솔루션은 원격 분석 메타데이터를 사용하여 대시보드가 [동적 원격 분석][lnk-dynamic-telemetry]을 표시하는 방법을 사용자 지정합니다.

## <a name="lifecycle"></a>수명 주기

솔루션 포털에서 장치를 처음 만들 때 솔루션은 Cosmos DB 데이터베이스에 항목을 만들어 명령 및 메서드 기록을 저장합니다. 또한 이 시점에서 솔루션은 장치가 IoT Hub로 인증하는 데 사용할 키를 생성하는 장치 ID 레지스트리에서 장치에 대한 항목을 만듭니다. 또한 장치 쌍을 만듭니다.

장치가 솔루션에 처음 연결되면 reported 속성 및 장치 정보 메시지를 보냅니다. reported 속성 값은 장치 쌍에 자동으로 저장됩니다. reported 속성은 장치 제조업체, 모델 번호, 일련 번호 및 지원되는 메서드의 목록을 포함합니다. 장치 정보 메시지는 명령 매개 변수에 대한 정보를 비롯하여 장치에서 지원하는 명령 목록을 포함합니다. 솔루션이 이 메시지를 받으면 Cosmos DB 데이터베이스의 장치 정보를 업데이트합니다.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>솔루션 포털에서 장치 정보 보기 및 편집

솔루션 포털의 장치 목록은 기본적으로 **상태**, **DeviceId**, **제조업체**, **모델 번호**, **일련 번호**, **펌웨어**, **플랫폼**, **프로세서**, **설치된 RAM** 등의 장치 속성을 열로 표시합니다. **열 편집기**를 클릭하여 열을 사용자 지정할 수 있습니다. 장치 속성 **위도** 및 **경도**는 대시보드의 Bing 맵에서 위치를 결정합니다.

![장치 목록의 열 편집기][img-device-list]

솔루션 포털의 **장치 세부 정보** 창에서 desired 속성 및 태그를 편집할 수 있습니다(reported 속성은 읽기 전용).

![장치 세부 정보 창][img-device-edit]

솔루션 포털을 사용하여 솔루션에서 장치를 제거할 수 있습니다. 장치를 제거하면 솔루션은 ID 레지스트리에서 장치 항목을 제거한 다음 장치 쌍을 삭제합니다. 또한 솔루션은 Cosmos DB 데이터베이스에서 장치와 관련된 정보를 제거합니다. 장치를 사용하지 않도록 설정해야 제거할 수 있습니다.

![장치 제거][img-device-remove]

## <a name="device-information-message-processing"></a>장치 정보 메시지 처리

장치에서 보낸 장치 정보 메시지는 원격 분석 메시지와 구별됩니다. 장치 정보 메시지는 장치가 응답할 수 있는 명령 및 모든 명령 기록을 포함합니다. IoT Hub 자체에는 장치 정보 메시지에 포함된 메타데이터의 정보가 없으며 장치-클라우드 메시지를 처리하는 것과 동일한 방식으로 메시지를 처리합니다. 원격 모니터링 솔루션에서 [Azure Stream Analytics][lnk-stream-analytics](ASA) 작업은 IoT Hub의 메시지를 읽습니다. **DeviceInfo** Stream Analytics 작업은 **"ObjectType": "DeviceInfo"**를 포함하는 메시지를 필터링하고 웹 작업에서 실행되는 **EventProcessorHost** 호스트 인스턴스에 전달합니다. **EventProcessorHost** 인스턴스의 논리는 장치 ID를 사용하여 특정 장치에 대한 Cosmos DB 레코드를 찾아 레코드를 업데이트합니다.

> [!NOTE]
> 장치 정보 메시지는 표준 장치-클라우드 메시지입니다. 솔루션은 ASA 쿼리를 사용하여 장치 정보 메시지와 원격 분석 메시지를 구분합니다.

## <a name="next-steps"></a>다음 단계

지금까지 미리 구성된 솔루션은 사용자 지정하는 방법을 살펴보았으므로 IoT Suite의 미리 구성된 솔루션이 제공하는 기능 및 기타 특징에 대해 살펴보겠습니다.

* [예측 정비 사전 구성 솔루션 개요][lnk-predictive-overview]
* [IoT Suite에 대한 질문과 대답][lnk-faq]
* [처음부터 IoT 보안을 고려][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-v1-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-v1-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-v1-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
