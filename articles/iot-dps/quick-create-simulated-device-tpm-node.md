---
title: Node.js를 사용하여 Azure IoT Hub에 시뮬레이션된 TPM 장치 프로비전 | Microsoft Docs
description: Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service용 Node.js 장치 SDK를 사용하여 시뮬레이션된 TPM 장치 만들기 및 프로비전
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo;bryanla
ms.date: 04/09/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7728937e73ed9d375926ad30db89585244d06b22
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service용 Node.js 장치 SDK를 사용하여 시뮬레이션된 TPM 장치 만들기 및 프로비전

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

다음 단계에서는 Windows OS를 실행 중인 개발 컴퓨터에서 시뮬레이션된 장치를 만들고 Windows TPM 시뮬레이터를 장치의 [HSM(하드웨어 보안 모듈)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)으로 실행하며 코드 샘플을 사용하여 시뮬레이션된 장치를 Device Provisioning Service 및 IoT Hub와 연결하는 방법을 보여 줍니다. 

자동 프로비전 프로세스에 익숙하지 않은 경우 [자동 프로비전 개념](concepts-auto-provisioning.md)도 검토하세요. 계속하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다. 

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>환경 준비 

1. 컴퓨터에 [Node.js v4.0 이상](https://nodejs.org)이 설치되어 있는지 확인합니다.

1. 컴퓨터에 `git`이 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 **Git Bash**가 포함됩니다. 


## <a name="simulate-a-tpm-device"></a>TPM 장치 시뮬레이션

1. 명령 프롬프트 또는 Git Bash를 엽니다. `azure-utpm-c` GitHub 리포지토리를 복제합니다.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git
    ```

1. GitHub 루트 폴더로 이동하고 [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) 시뮬레이터를 실행합니다. 포트 2321 및 2322에서 소켓을 수신 대기합니다. 이 명령 창을 닫지 마세요. 이 빠른 시작 가이드가 끝날 때까지 이 시뮬레이터를 계속 실행하고 있어야 합니다. 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. **registerdevice**라는 빈 폴더를 새로 만듭니다. **registerdevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. `npm`에서 요청하는 모든 질문에 답변하거나 사용자에게 적합한 경우 기본값을 적용합니다.
   
    ```cmd/sh
    npm init
    ```

1. 선도적인 다음 패키지를 설치합니다.

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > 위 패키지를 설치하는 데 알려진 몇 가지 문제가 있습니다. 이러한 문제를 해결하려면 **관리자 권한으로 실행** 모드에서 명령 프롬프트를 사용하여 `npm install --global --production windows-build-tools`를 실행하고, 경로를 설치된 버전으로 바꾼 후에 `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140`을 실행한 다음, 위의 설치 명령을 다시 실행합니다.
    >

1. 등록 시 사용한 구성 요소가 포함된 다음 패키지를 설치합니다.

    - TPM에서 작동하는 보안 클라이언트: `azure-iot-security-tpm`
    - Device Provisioning Service에 연결하는 장치에 대한 전송: `azure-iot-provisioning-device-http` 또는 `azure-iot-provisioning-device-amqp`
    - 전송 및 보안 클라이언트를 사용하는 클라이언트: `azure-iot-provisioning-device`

    장치가 등록되면 등록 시 제공된 자격 증명을 사용하여 장치를 연결하기 위해 일반적인 IoT Hub Device Client 패키지를 사용할 수 있습니다. 필요한 항목은 다음과 같습니다.

    - 장치 클라이언트: `azure-iot-device`
    - 전송: `azure-iot-device-amqp`, `azure-iot-device-mqtt`, 또는 `azure-iot-device-http` 중 하나
    - 이미 설치한 보안 클라이언트: `azure-iot-security-tpm`

    > [!NOTE]
    > 아래 샘플에서는 `azure-iot-provisioning-device-http` 및 `azure-iot-device-mqtt` 전송을 사용합니다.
    > 

    **registerdevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 이러한 패키지를 한 번에 모두 설치할 수 있습니다.

        ```cmd/sh
        npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
        ```

1. 텍스트 편집기를 사용하여 **registerdevice** 폴더에 새 **ExtractDevice.js** 파일을 만듭니다.

1. **ExtractDevice.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. 메서드를 구현하는 다음 함수를 추가합니다.
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. **ExtractDevice.js** 파일을 저장하고 닫습니다. 샘플을 실행합니다.

    ```cmd/sh
    node ExtractDevice.js
    ```

1. 출력 창에 장치 등록에 필요한 **_인증 키_** 와 **_등록 ID_** 가 표시됩니다. 이러한 값을 기록해 둡니다. 


## <a name="create-a-device-entry"></a>장치 항목 만들기

1. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning Service를 엽니다.

1. Device Provisioning Service 요약 블레이드에서 **등록 관리**를 선택합니다. **개별 등록** 탭을 선택하고 맨 위에서 **추가** 단추를 클릭합니다. 

1. **등록 목록 항목 추가** 아래에 다음 정보를 입력합니다.
    - ID 증명 *메커니즘*으로 **TPM**을 선택합니다.
    - TPM 장치에 대한 *등록 ID* 및 *인증 키*를 입력합니다.
    - 필요에 따라 다음 정보를 입력합니다.
        - 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
        - 고유한 장치 ID를 입력합니다. 장치 이름을 지정할 때 중요한 데이터가 포함되지 않도록 합니다.
        - 장치에 대해 원하는 초기 구성으로 **초기 장치 쌍 상태**를 업데이트합니다.
    - 완료되면 **저장** 단추를 클릭합니다. 

    ![포털 블레이드에 장치 등록 정보 입력](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   성공적으로 등록되면 장치의 *등록 ID*가 *개별 등록* 탭 아래의 목록에 표시됩니다. 


## <a name="register-the-device"></a>장치 등록

1. Azure Portal에서 Device Provisioning Service에 대한 **개요** 블레이드를 선택하고, **_전역 장치 엔드포인트_** 및 **_ID 범위_** 값을 적어 둡니다.

    ![포털 블레이드에서 DPS 끝점 정보 추출](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. 텍스트 편집기를 사용하여 **simDevice** 폴더에 새 **simDevice.js** 파일을 만듭니다.

1. **SimulatedDevice.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **Node.js용 Azure IoT SDK**는 _AMQ_, _AMQP WS_ 및 _MQTT WS_와 같은 추가 프로토콜을 지원합니다.  더 많은 예제는 [Node.js 샘플용 Device Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples)를 참조하세요.
    > 

1. **globalDeviceEndpoint** 및 **idScope** 변수를 추가하고, 이러한 변수를 사용하여 **ProvisioningDeviceClient** 인스턴스를 만듭니다. **{globalDeviceEndpoint}** 및 **{idScope}** 를 **1단계**의 **_전역 장치 엔드포인트_** 및 **_ID 범위_** 값으로 바꿉니다.
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. 장치에서 메서드를 실행하도록 다음 함수를 추가합니다.
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. **RegisterDevice.js** 파일을 저장하고 닫습니다. 샘플을 실행합니다.

    ```cmd/sh
    node RegisterDevice.js
    ```

1. 장치를 부팅하고 IoT Hub 정보를 얻기 위해 Device Provisioning Service에 연결하는 과정을 시뮬레이션하는 메시지를 확인합니다. 프로비전 서비스와 연결된 IoT Hub에 시뮬레이션된 장치를 성공적으로 프로비전하면 장치 ID가 허브의 **IoT 장치** 블레이드에 표시됩니다. 

    ![장치가 IoT Hub에 등록됨](./media/quick-create-simulated-device/hub-registration.png) 

    장치에 대한 등록 항목의 기본값으로부터 *초기 장치 쌍 상태*를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 장치 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

장치 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 장치 클라이언트 샘플 출력 창을 닫습니다.
1. 컴퓨터에서 TPM 시뮬레이터 창을 닫습니다.
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 Device Provisioning Service를 선택합니다. 서비스에 대한 **등록 관리** 블레이드를 연 다음, **개별 등록** 탭을 클릭합니다. 이 빠른 시작에서 등록한 장치의 *등록 ID*를 선택하고, 위쪽의 **삭제** 단추를 클릭합니다. 
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. 허브에 대한 **IoT 장치** 블레이드를 열고, 이 빠른 시작에서 등록한 장치의 *장치 ID*를 선택한 다음, 위쪽의 **삭제** 단추를 클릭합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 시뮬레이션된 TPM 장치를 컴퓨터에 만들고, IoT Hub Device Provisioning Service를 사용하여 IoT Hub에 이 장치를 프로비전했습니다. 프로그래밍 방식으로 TPM 장치를 등록하는 방법을 알아보려면 프로그래밍 방식으로 TPM 장치를 등록하는 빠른 시작으로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service에 TPM 장치 등록](quick-enroll-device-tpm-node.md)
