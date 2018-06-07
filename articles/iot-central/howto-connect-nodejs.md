---
title: Azure IoT Central에 일반 Node.js 클라이언트 응용 프로그램 연결 | Microsoft Docs
description: 장치 개발자로서 일반 Node.js 장치를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아봅니다.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8666a2db051cbd4a93c3e587aeaef3e1722b1b83
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199598"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Azure IoT Central 응용 프로그램에 일반 클라이언트 응용 프로그램 연결(Node.js)

이 문서에서는 장치 개발자로서 실제 장치를 나타내는 일반 Node.js 응용 프로그램을 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

1. Azure IoT Central 응용 프로그램. 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](howto-create-application.md)를 참조하세요.
1. [Node.js](https://nodejs.org/) 버전 4.0.0 이상이 설치된 개발 컴퓨터. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다.

Azure IoT Central 응용 프로그램에서 다음 측정값 및 장치 속성이 정의된 장치 템플릿이 필요합니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

**측정값** 페이지에서 다음 원격 분석 데이터를 추가합니다.

| 표시 이름 | 필드 이름  | Units | Min | max | 소수 자릿수 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 온도  | 온도 | F     | 60  | 110 | 0              |
| 습도     | humidity    | %     | 0   | 100 | 0              |
| 압력     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  원격 분석 측정값의 데이터 형식은 double입니다.

테이블에 표시된 필드 이름을 장치 템플릿에 똑같이 입력합니다. 필드 이름이 일치하지 않으면 응용 프로그램에서 원격 분석 데이터를 표시할 수 없습니다.

### <a name="state-measurements"></a>상태 측정값

**측정값** 페이지에서 다음 상태를 추가합니다.

| 표시 이름 | 필드 이름  | 값 1 | 표시 이름 | 값 2 | 표시 이름 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| 팬 모드     | fanmode     | 1       | 실행 중      | 0       | 중지됨      |

> [!NOTE]
  상태 측정값의 데이터 형식은 문자열입니다.

테이블에 표시된 필드 이름을 장치 템플릿에 똑같이 입력합니다. 필드 이름이 일치하지 않으면 응용 프로그램에서 상태를 표시할 수 없습니다.

### <a name="event-measurements"></a>이벤트 측정값

**측정값** 페이지에서 다음 이벤트를 추가합니다.

| 표시 이름 | 필드 이름  | 심각도 |
| ------------ | ----------- | -------- |
| 과열  | overheat    | 오류    |

> [!NOTE]
  이벤트 측정값의 데이터 형식은 문자열입니다.

### <a name="device-properties"></a>장치 속성

**속성 페이지**에서 다음 장치 속성을 추가합니다.

| 표시 이름        | 필드 이름        | 데이터 형식 |
| ------------------- | ----------------- | --------- |
| 일련 번호       | serialNumber      | text      |
| 장치 제조업체 | manufacturer      | text      |

테이블에 표시된 필드 이름을 장치 템플릿에 똑같이 입력합니다. 필드 이름이 일치하지 않으면 응용 프로그램에서 속성 값을 표시할 수 없습니다.

### <a name="settings"></a>설정

**설정 페이지**에서 다음** 숫자** 설정을 추가합니다.

| 표시 이름    | 필드 이름     | Units | 10진수 | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| 팬 속도       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| 온도 설정 | setTemperature | F     | 0        | 20  | 200  | 80      |

테이블에 표시된 필드 이름을 장치 템플릿에 똑같이 입력합니다. 필드 이름이 일치하지 않으면 장치에서 설정 값을 받을 수 없습니다.

### <a name="add-a-real-device"></a>실제 장치 추가

Azure IoT Central 응용 프로그램에서, 자신이 만든 장치 템플릿으로 실제 장치를 추가하고 장치 연결 문자열을 기록해 둡니다. 자세한 내용은 [Azure IoT Central 응용 프로그램에 실제 장치 추가](tutorial-add-device.md)를 참조하세요.

## <a name="create-a-nodejs-application"></a>Node.js 응용 프로그램 만들기

다음 단계에서는 응용 프로그램에 추가한 실제 장치를 구현하는 클라이언트 응용 프로그램을 만드는 방법을 보여줍니다.

1. 컴퓨터에 `connected-air-conditioner-adv`이라는 폴더를 만듭니다. 명령줄 환경에서 해당 폴더로 이동합니다.

1. Node.js 프로젝트를 초기화하기 위해 다음 명령을 실행합니다.

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. `connected-air-conditioner-adv` 폴더에 **connectedAirConditionerAdv.js**라는 파일을 만듭니다.

1. **connectedAirConditionerAdv.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. 다음 변수 선언을 파일에 추가합니다.

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    `{your device connection string}` 자리 표시자를 자신의 장치 연결 문자열로 업데이트합니다. 실제 장치를 추가할 때 연결 세부 정보 페이지에서 이 값을 복사했습니다. 이 샘플에서는 `targetTemperature`를 0으로 초기화할 것이며, 필요에 따라 장치의 현재 판독값 또는 장치 쌍의 값을 사용할 수 있습니다. 

1. Azure IoT Central 응용 프로그램에 원격 분석 데이터, 상태 및 이벤트 측정값을 보낼 수 있도록 다음 함수를 파일에 추가합니다.

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    1. Azure IoT Central 응용 프로그램에 장치 속성을 보낼 수 있도록 다음 함수를 파일에 추가합니다.

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. 장치가 응답하는 설정을 정의할 수 있도록 다음 정의를 추가합니다.

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Azure IoT Central 응용 프로그램에서 업데이트된 설정을 처리할 수 있도록 다음을 파일에 추가합니다.

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Azure IoT Central에 대한 연결을 완료하고 클라이언트 코드의 함수를 연결하려면 다음을 추가합니다.

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Node.js 응용 프로그램 실행

명령줄 환경에서 다음 명령을 실행합니다.

```cmd/sh
node connectedAirConditionerAdv.js
```

운영자로서 Azure IoT Central 응용 프로그램에 실제 장치에 대해 다음과 같은 일을 할 수 있습니다.

* **측정값** 페이지에서 원격 분석 데이터 보기:

    ![원격 분석 보기](media/howto-connect-nodejs/viewtelemetry.png)

* **속성** 페이지에서, 장치에서 보낸 장치 속성 값을 봅니다.

    ![장치 속성 보기](media/howto-connect-nodejs/viewproperties.png)

* **설정** 페이지에서 팬 속도 및 목표 온도를 설정합니다.

    ![팬 속도 설정](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>다음 단계

일반 Node.js 클라이언트를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.
* [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
