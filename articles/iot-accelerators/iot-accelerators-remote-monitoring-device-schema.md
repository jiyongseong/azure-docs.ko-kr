---
title: 원격 모니터링 솔루션의 장치 스키마 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션에서 시뮬레이션된 장치를 정의하는 JSON 스키마에 대해 설명합니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 186eaee952435573a861d144195c3165e4940cc1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="understand-the-device-model-schema"></a>장치 모델 스키마 이해

원격 모니터링 솔루션에서 시뮬레이션된 장치를 사용하여 해당 동작을 테스트할 수 있습니다. 원격 모니터링 솔루션을 배포하면 시뮬레이션된 장치 컬렉션이 자동으로 프로비전됩니다. 기존 시뮬레이션된 장치를 사용자 지정하거나 고유한 시뮬레이션된 장치를 만들 수 있습니다.

이 문서에서는 시뮬레이션된 장치의 기능 및 동작을 지정하는 장치 모델 스키마에 대해 설명합니다. 장치 모델은 JSON 파일에 저장됩니다.

다음 문서는 현재 문서와 관련이 있습니다.

* [장치 모델 동작 구현](../iot-suite/iot-suite-remote-monitoring-device-behavior.md)에서는 시뮬레이션된 장치의 동작을 구현하는 데 사용하는 JavaScript 파일에 대해 설명합니다.
* [새 시뮬레이션된 장치 만들기](iot-accelerators-remote-monitoring-test.md)에서는 장치를 만들고 새 시뮬레이션된 장치 유형을 솔루션에 배포하는 방법을 보여 줍니다.

이 문서에서는 다음 방법을 설명합니다.

>[!div class="checklist"]
> * JSON 파일을 사용하여 시뮬레이션된 장치 모델 정의
> * 시뮬레이션된 장치의 속성 지정
> * 시뮬레이션된 장치가 전송하는 원격 분석 지정
> * 장치가 응답하는 클라우드-장치 메서드 지정

## <a name="the-parts-of-the-device-model-schema"></a>장치 모델 스키마 파트

냉각기 또는 트럭과 같은 각 장치 모델은 원격 모니터링 솔루션에 연결할 시뮬레이션된 장치 유형을 정의합니다. 각 장치 모델은 다음 최상위 스키마를 사용하여 JSON 파일에 저장됩니다.

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

GitHub의 [devicemodels 폴더](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels)에서 기본 시뮬레이션된 장치의 스키마 파일을 볼 수 있습니다.

다음 표에서는 최상위 스키마 항목에 대해 설명합니다.

| 스키마 항목 | 설명 |
| -- | --- |
| `SchemaVersion` | 스키마 버전은 항상 `1.0.0`이며 이 파일의 형식에 고유합니다. |
| `Id` | 이 장치 모델의 고유 ID입니다. |
| `Version` | 장치 모델의 버전을 식별합니다. |
| `Name` | 장치 모델의 식별 이름입니다. |
| `Description` | 장치 모델에 대한 설명입니다. |
| `Protocol` | 장치가 사용하는 연결 프로토콜입니다. `AMQP`, `MQTT` 및 `HTTP` 중 하나일 수 있습니다. |

다음 섹션에서는 JSON 스키마의 다른 섹션에 대해 설명합니다.

## <a name="simulation"></a>시뮬레이션

`Simulation` 섹션에서는 시뮬레이션된 장치의 내부 상태를 정의합니다. 장치에서 전송하는 모든 원격 분석 값은 이 장치 상태의 일부여야 합니다.

장치 상태의 정의에는 다음 두 가지 요소가 있습니다.

* `InitialState`는 장치 상태 개체의 모든 속성에 대한 초기 값을 정의합니다.
* `Script`는 장치 상태를 업데이트하기 위해 일정에 따라 실행되는 JavaScript 파일을 식별합니다. 이 스크립트 파일을 사용하여 장치에서 전송되는 원격 분석 값을 임의로 지정할 수 있습니다.

장치 상태 개체를 업데이트하는 JavaScript 파일에 대한 자세한 내용은 [장치 모델 동작 이해](iot-accelerators-remote-monitoring-device-behavior.md)를 참조하세요.

다음 예제에서는 시뮬레이션된 냉각기 장치에 대한 장치 상태 개체의 정의를 보여 줍니다.

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

시뮬레이션 서비스는 5초마다 **chiller-01-state.js** 파일을 실행하여 장치 상태를 업데이트합니다. GitHub의 [scripts 폴더](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)에서 기본 시뮬레이션된 장치의 JavaScript 파일을 볼 수 있습니다. 규칙에 따라 이러한 JavaScript 파일에는 메서드 동작을 구현하는 파일과 구분하기 위해 **-state** 접미사가 있습니다.

## <a name="properties"></a>properties

스키마의 `Properties` 섹션에서는 장치가 솔루션에 보고하는 속성 값을 정의합니다. 예: 

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

솔루션이 시작되면 모든 시뮬레이션된 장치를 쿼리하여 UI에서 사용할 `Type` 값 목록을 작성합니다. 솔루션은 `Latitiude` 및 `Longitude` 속성을 사용하여 대시보드의 맵에 장치 위치를 추가합니다.

## <a name="telemetry"></a>원격 분석

`Telemetry` 배열은 시뮬레이션된 장치가 솔루션에 전송하는 모든 원격 분석 유형을 나열합니다.

다음 예제에서는 엘리베이터 센서의 `floor`, `vibration` 및 `temperature` 데이터를 사용하여 10초마다 JSON 원격 분석 메시지를 전송합니다.

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate`은 시뮬레이션된 장치가 전송하는 JSON 메시지의 구조를 정의합니다. `MessageTemplate`의 자리 표시자는 `${NAME}` 구문을 사용합니다. 여기서 `NAME`은 [장치 상태 개체](#simulation)의 키입니다. 문자열은 따옴표로 묶어야 하고 숫자는 따옴표로 묶으면 안 됩니다.

`MessageSchema`는 시뮬레이션된 장치가 전송하는 메시지의 스키마를 정의합니다. 또한 백 엔드 응용 프로그램이 정보를 다시 사용하여 들어오는 원격 분석을 해석할 수 있도록 메시지 스키마가 IoT Hub에도 게시됩니다.

현재, JSON 메시지 스키마만 사용할 수 있습니다. 스키마에 나열된 필드는 다음 형식일 수 있습니다.

* 개체 - JSON을 사용하여 직렬화됨
* 이진 - base64를 사용하여 직렬화됨
* 텍스트
* BOOLEAN
* 정수 
* Double
* Datetime

다른 간격으로 원격 분석 메시지를 전송하려면 `Telemetry` 배열에 여러 원격 분석 유형을 추가합니다. 다음 예제에서는 10초마다 온도 및 습도 데이터를 전송하고 1분마다 광원 상태를 전송합니다.

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

시뮬레이션된 장치는 원격 모니터링 솔루션에서 호출된 클라우드-장치 메서드에 응답할 수 있습니다. 장치 모델 스키마 파일의 `CloudToDeviceMethods` 섹션은 다음을 수행합니다.

* 시뮬레이션된 장치가 응답할 수 있는 메서드를 정의합니다.
* 실행할 논리를 포함하는 JavaScript 파일을 식별합니다.

시뮬레이션된 장치는 지원하는 메서드 목록을 원격 모니터링 솔루션에 전송합니다.

장치의 동작을 구현하는 JavaScript 파일에 대한 자세한 내용은 [장치 모델 동작 이해](iot-accelerators-remote-monitoring-device-behavior.md)를 참조하세요.

다음 예제에서는 세 가지 지원되는 메서드 및 해당 메서드를 구현하는 JavaScript 파일을 지정합니다.

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

GitHub의 [scripts 폴더](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)에서 기본 시뮬레이션된 장치의 JavaScript 파일을 볼 수 있습니다. 규칙에 따라 이러한 JavaScript 파일에는 상태 동작을 구현하는 파일과 구분하기 위해 **-method** 접미사가 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 고유한 사용자 지정 시뮬레이션된 장치 모델을 만드는 방법을 설명했습니다. 이 문서에서는 다음을 수행하는 방법을 보여 주었습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * JSON 파일을 사용하여 시뮬레이션된 장치 모델 정의
> * 시뮬레이션된 장치의 속성 지정
> * 시뮬레이션된 장치가 전송하는 원격 분석 지정
> * 장치가 응답하는 클라우드-장치 메서드 지정

이제 JSON 스키마에 대해 학습했으므로 다음 단계에서는 [시뮬레이션된 장치의 동작을 구현](iot-accelerators-remote-monitoring-device-behavior.md)하는 방법을 알아보는 것이 좋습니다.

원격 모니터링 솔루션에 대한 자세한 개발자 정보는 다음을 참조하세요.

* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [개발자 문제 해결 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->