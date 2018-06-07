---
title: Azure IoT Central 응용 프로그램에 실제 장치 추가 | Microsoft Docs
description: 운영자로서 Azure IoT Central 응용 프로그램에 실제 장치를 추가합니다.
services: iot-central
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: f95d9ec8cf22c287169a8de077ff9eb5907a8e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201934"
---
# <a name="add-a-real-device-to-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에 실제 장치 추가

이 자습서에서는 Microsoft Azure IoT Central 응용 프로그램에 실제 장치를 추가하고 구성하는 방법을 알려줍니다.

이 자습서는 2부로 구성되어 있습니다.

1. 먼저 운영자로서 Azure IoT Central 응용 프로그램에서 실제 장치를 추가하고 구성하는 방법을 알아봅니다. 1부의 끝에 2부에서 사용할 연결 문자열을 검색합니다.
1. 그런 다음, 장치 개발자로서 실제 장치에서 코드에 대해 알아봅니다. 1부의 연결 문자열을 샘플 코드에 추가합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 실제 장치 추가
> * 새 장치 구성
> * 응용 프로그램에서 실제 장치에 대한 연결 문자열 가져오기
> * 클라이언트 코드가 응용 프로그램에 매핑되는 방법 이해
> * 실제 장치에 대한 클라이언트 코드 구성

## <a name="prerequisites"></a>필수 조건

시작하기 전에 빌더는 최소 첫 번째 작성기 자습서를 완료해야 Azure IoT Central 응용 프로그램을 만듭니다.

* [새 장치 유형 정의](tutorial-define-device-type.md)(필수)
* [장치에 대한 규칙 및 작업 구성](tutorial-configure-rules.md)(선택 사항)
* [연산자의 뷰 사용자 지정](tutorial-customize-operator.md)(선택 사항)

## <a name="add-a-real-device"></a>실제 장치 추가

응용 프로그램에 실제 장치를 추가하려면 [새 장치 유형을 정의](tutorial-define-device-type.md) 자습서에서 만든 **연결 된 공조 장치** 템플릿을 사용합니다.

1. 운영자로서 새 장치를 추가하려면 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다.

    ![연결된 공조 장치를 표시하는 장치 탐색기 페이지](media/tutorial-add-device/explorer.png)

    **Device Explorer**는 작성기가 장치 템플릿을 만들 때 자동으로 생성된 시뮬레이션된 장치 및 **연결된 공조 장치**를 표시합니다.

1. 실제 연결된 공조 장치에 연결을 시작하려면 **새로 만들기** 및 **실제 장치**를 차례로 선택합니다.

    ![실제 연결된 새 공조 장치 추가 시작](media/tutorial-add-device/newreal.png)

1. 필요에 따라 장치 이름을 선택하고 값을 편집하여 새 장치 이름을 바꿀 수 있습니다.

    ![장치 이름 바꾸기](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>실제 장치 구성

**연결된 공조 장치** 템플릿에서 실제 장치를 만듭니다. 빌더로서 장치에 대한 정보를 기록하려면 **설정**을 사용하여 장치를 구성하고 속성 값을 설정합니다.

1. **설정** 페이지에서 **온도 설정** 설정 상태가 **업데이트가 되지 않습니다**. 실제 장치가 연결되고 설정 대로 작동하는 것을 인식할 때까지 이 상태를 계속 유지합니다.

    ![설정 동기화 표시](media/tutorial-add-device/settingssyncing.png)

1. 실제 연결된 새 공조 장치 장치에 대한 **속성** 페이지에서 **일련 번호**를 **rcac0010**로 설정하고 **펌웨어 버전**을 9.75로 설정합니다. 그런 다음, **저장**을 선택합니다.

    ![실제 장치에 대한 속성 설정](media/tutorial-add-device/setproperties.png)

1. 빌더로서 실제 장치에 대한 **측정**, **규칙** 및 **대시보드** 페이지를 볼 수 있습니다.

## <a name="get-connection-string-for-real-device-from-application"></a>응용 프로그램에서 실제 장치에 대한 연결 문자열 가져오기

장치 개발자는 장치에서 실행되는 코드에서 실제 장치에 대한 _연결 문자열_을 포함해야 합니다. 연결 문자열을 사용하면 장치를 Azure IoT Central 응용 프로그램에 안전하게 연결할 수 있습니다. 모든 장치 인스턴스에는 고유 연결 문자열이 있습니다. 다음 단계에서는 응용 프로그램에서 인스턴스 장치에 대한 연결 문자열을 찾는 방법을 보여 줍니다.

1. 실제 연결된 공조 장치에 대한 **장치** 화면에서 **이 장치에 연결**을 선택합니다.

    ![연결 정보 링크를 보여주는 장치 페이지](media/tutorial-add-device/connectionlink.png)

1. **연결** 페이지에서 **기본 연결 문자열**을 복사하고 저장합니다. 이 자습서의 2부 중간 부분에서 이 값을 사용합니다. 장치 개발자는 장치에서 실행되는 클라이언트 응용 프로그램에서 이 값을 사용합니다.

    ![연결 문자열 값](media/tutorial-add-device/connectionstring.png)

## <a name="prepare-the-client-code"></a>클라이언트 코드 준비

이 문서의 예제 코드는 [Node.js](https://nodejs.org/)에서 작성되고 다음을 하기에 충분한 코드를 보여줍니다.

* 장치로서 Azure IoT Central 응용 프로그램에 연결합니다.
* 연결된 공조 장치로서 온도 원격 분석을 보냅니다.
* **온도 설정** 설정을 사용하는 운영자에게 응답합니다.

[다음 단계](#next-steps)에서 참조하는 "방법" 문서는 더 완벽한 샘플을 제공하고 다른 프로그래밍 언어의 사용을 보여줍니다. 장치를 Azure IoT Central에 연결하는 방법에 대한 자세한 내용은 [장치 연결](concepts-connectivity.md) 문서를 참조합니다.

다음 단계에서는 [Node.js](https://nodejs.org/) 샘플을 준비하는 방법을 보여줍니다.

1. 컴퓨터에 [Node.js](https://nodejs.org/) 버전 4.0.x 이상을 설치합니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다.

1. 컴퓨터에 `connectedairconditioner`이라는 폴더를 만듭니다.

1. 명령줄 환경에서 만든 `connectedairconditioner` 폴더로 이동합니다.

1. Node.js 프로젝트를 초기화하려면 모든 기본값을 허용하는 다음 명령을 실행합니다.

    ```cmd/sh
    npm init
    ```

1. 필요한 패키지를 설치하려면 다음 명령을 실행하십시오.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 텍스트 편집기를 사용하여 `connectedairconditioner` 폴더에서 **ConnectedAirConditioner.js**이라는 파일을 만듭니다.

1. **ConnectedAirConditioner.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.

    ```javascript
    'use strict';

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

    > [!NOTE]
    > 이후 단계에서 자리 표시자 `{your device connection string}`을 업데이트합니다.

1. 지금까지 한 변경을 저장하지만 파일을 열어 놓습니다.

## <a name="understand-how-client-code-maps-to-the-application"></a>클라이언트 코드가 응용 프로그램에 매핑되는 방법 이해

이전 섹션에서는 Azure IoT Central 응용 프로그램에 연결하는 응용 프로그램에 대한 기본 Node.js 프로젝트를 만들었습니다. 이 섹션에서는 다음에 코드를 추가합니다.

* Azure IoT Central 응용 프로그램에 연결합니다.
* Azure IoT Central 응용 프로그램에 원격 분석을 보냅니다.
* Azure IoT Central 응용 프로그램에서 설정을 받습니다.

1. Azure IoT Central 응용 프로그램에 온도 원격 분석을 보내려면 다음 코드를 **ConnectedAirConditioner.js** 파일에 추가합니다.

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    보내는 JSON의 필드 이름이 장치 템플릿에서 온도 원격 분석에 대해 지정한 필드 이름과 일치해야 합니다. 이 예제에서는 필드 이름이 **온도**합니다.

1. **setTemperature**와 같이 장치가 지원하는 설정을 정의하려면 다음 정의를 추가합니다.

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Azure IoT Central에서 전송된 설정을 처리하려면 적절한 장치 코드를 찾아 실행하는 다음 함수를 추가합니다.

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

    이 함수:

    * 원하는 속성을 보내는 Azure IoT Central을 지켜봅니다.
    * 설정 변경 처리를 위해 호출하려면 적절한 함수를 찾습니다.
    * Azure IoT Central 응용 프로그램으로 승인을 돌려 보냅니다.

1. Azure IoT Central에 대한 연결을 완료하고 클라이언트 코드의 함수를 연결하려면 다음 코드를 추가합니다.

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
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. 지금까지 한 변경을 저장하지만 파일을 열어 놓습니다.

## <a name="configure-client-code-for-real-device"></a>실제 장치에 대한 클라이언트 코드 구성

<!-- Add the connection string to the sample code, build, and run -->
Azure IoT Central 응용 프로그램에 연결할 클라이언트 코드를 구성하려면 이 자습서의 앞부분에서 언급한 실제 장치에 대한 연결 문자열을 추가해야 합니다.

1. **ConnectedAirConditioner.js** 파일에서 다음 코드 줄을 찾습니다.

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. `{your device connection string}`을 실제 장치의 연결 문자열로 바꿉니다. "응용 프로그램에서 실제 장치에 대한 연결 문자열 가져오기" 섹션의 끝에서 연결 문자열을 기록했습니다.

1. **ConnectedAirConditioner.js** 파일에 변경 내용을 저장합니다.

1. 샘플을 실행하려면 명령줄 환경에서 다음 명령을 실행합니다.

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > 이 명령을 실행하는 경우 `connectedairconditioner` 폴더에 있는지 확인합니다.

1. 응용 프로그램은 출력을 콘솔에 인쇄합니다.

    ![클라이언트 응용 프로그램 출력](media/tutorial-add-device/output.png)

1. 약 30초 후 장치 **측정** 페이지에서 원격 분석을 확인합니다.

    ![실제 원격 분석](media/tutorial-add-device/realtelemetry.png)

1. **설정** 페이지에서 설정이 동기화됐는지 확인할 수 있습니다. 장치가 처음 연결된 경우 설정 값을 수신하고 변경 내용을 승인했습니다.

    ![동기화됨 설정](media/tutorial-add-device/settingsynced.png)

1. **설정** 페이지에서 장치 온도를 **95**로 설정하고 **장치 업데이트**를 선택합니다. 샘플 응용 프로그램이 이 변경 내용을 수신하고 처리합니다.

    ![수신 및 처리 설정](media/tutorial-add-device/receivesetting.png)

    > [!NOTE]
    > 두 개의 "업데이트 설정" 메시지가 있습니다. `pending` 상태가 전송될 경우의 메시지 및 `completed` 상태가 전송될 경우의 메시지입니다.

1. **측정** 페이지에서 장치가 더 높은 온도 값을 전송하는 것을 확인할 수 있습니다.

    ![온도 원격 분석이 더 높습니다.](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="nextstepaction"]
> * 새 실제 장치 추가
> * 새 장치 구성
> * 응용 프로그램에서 실제 장치에 대한 연결 문자열 가져오기
> * 클라이언트 코드가 응용 프로그램에 매핑되는 방법 이해
> * 실제 장치에 대한 클라이언트 코드 구성

Azure IoT Central 응용 프로그램에 실제 장치를 연결했으므로 제안된 다음 단계는 다음과 같습니다.

운영자로서 방법을 알아볼 수 있습니다.

* [장치 관리](howto-manage-devices.md)
* [장치 설정 사용](howto-use-device-sets.md)
* [사용자 지정 분석 만들기](howto-create-analytics.md)

장치 개발자로서 방법을 알아볼 수 있습니다.

* [DevKit 준비 및 연결](howto-connect-devkit.md)
* [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md)
* [Azure IoT Central 응용 프로그램에 일반 Node.js 클라이언트 연결](howto-connect-nodejs.md)
