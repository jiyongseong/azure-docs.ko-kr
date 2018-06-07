---
title: Azure IoT Hub를 사용하여 장치 펌웨어 업데이트(노드) | Microsoft Docs
description: 장치 펌웨어 업데이트를 시작하려면 Azure IoT Hub에서 장치 관리를 사용하는 방법입니다. Node.js용 Azure IoT SDK를 사용하여 시뮬레이션된 장치 앱 및 펌웨어 업데이트를 트리거하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e8ef556793534c9eb8300ee262a4b134960f862a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>장치 관리를 사용하여 장치 펌웨어 업데이트 시작(노드/노드)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

[장치 관리 시작][lnk-dm-getstarted] 자습서에서 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod] 기본 형식을 사용하여 장치를 원격으로 다시 부팅하는 방법을 살펴보았습니다. 이 자습서는 동일한 IoT Hub 기본 형식을 사용하며 종단 간 시뮬레이션된 펌웨어 업데이트를 수행하는 방법을 보여주고 지침을 제공합니다.  이러한 패턴이 Intel Edison 장치 샘플에 대한 펌웨어 업데이트 구현에 사용됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* IoT Hub를 통해 시뮬레이션된 장치 앱에서 firmwareUpdate 직접 메서드를 호출하는 Node.js 콘솔 앱을 만듭니다.
* **firmwareUpdate** 직접 메서드를 구현하는 시뮬레이트된 장치 앱을 만듭니다. 이 메서드는 펌웨어 이미지를 다운로드하기 위해 대기했다가 펌웨어 이미지를 다운로드하고, 마지막으로 펌웨어 이미지를 적용하는 다단계 프로세스를 시작합니다. 업데이트의 각 단계 동안, 장치는 보고된 속성을 사용하여 진행 상황을 보고합니다.

이 자습서를 마치면 두 가지 Node.js 콘솔 앱이 만들어집니다.

**dmpatterns_fwupdate_service.js** - 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 주기적으로(매 500밀리초) 표시합니다.

**dmpatterns_fwupdate_device.js**, 이전에 생성한 장치 ID를 사용하여 IoT hub 에 연결하고, firmwareUpdate 직접 메서드를 수신하고, 펌웨어 업데이트를 시뮬레이션하기 위한 다단계 프로세스를 실행합니다. 여기에는 이미지 다운로드를 위한 대기, 새 이미지 다운로드, 마지막으로 이미지 적용이 포함됩니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 4.0.x 이상 <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

IoT hub 허브를 만들고 IoT Hub 연결 문자열을 확보하려면 [장치 관리 시작](iot-hub-node-node-device-management-get-started.md) 문서의 내용을 수행하세요.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트 트리거
이 섹션에서는 장치에서 원격 펌웨어 업데이트를 시작하는 Node.js 콘솔 앱을 만듭니다. 앱은 직접 메서드를 사용하여 업데이트를 시작하고 장치 쌍 쿼리를 사용하여 정기적으로 활성 펌웨어 업데이트의 상태를 가져옵니다.

1. **triggerfwupdateondevice**라는 빈 폴더를 만듭니다.  **triggerfwupdateondevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **triggerfwupdateondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-hub** 패키지를 설치합니다.
   
    ```
    npm install azure-iothub --save
    ```
3. 텍스트 편집기를 사용하여 **triggerfwupdateondevice** 폴더에 **dmpatterns_getstarted_service.js** 파일을 만듭니다.
4. 다음 'require' 문을 **dmpatterns_getstarted_service.js** 파일의 시작 부분에 추가합니다.
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 다음 변수 선언을 추가하고 자리 표시자 값을 바꿉니다.
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. firmwareUpdate 보고된(reported) 속성의 값을 찾아서 표시하도록 다음 함수를 추가합니다.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. 대상 장치를 다시 시작하기 위해 firmwareUpdate 메서드를 호출하도록 다음 함수를 추가합니다.
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. 마지막으로 펌웨어 업데이트 시퀀스를 시작하고 reported 속성에 대한 주기적 표시를 시작하기 위해 다음 함수를 코드에 추가합니다.
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. **dmpatterns_fwupdate_service.js** 파일을 저장하고 닫습니다.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. **triggerfwupdateondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 원격 다시 시작을 트리거하고 마지막 다시 시작 시간을 찾기 위해 장치 쌍에 대한 쿼리를 수행합니다.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 트리거하고, 보고된 속성을 사용하여 펌웨어 업데이트 프로세스의 진행 상황을 이해했습니다.

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
