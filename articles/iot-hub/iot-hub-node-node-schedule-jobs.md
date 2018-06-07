---
title: Azure IoT Hub(노드)를 사용하여 작업 예약 | Microsoft 문서
description: 여러 장치에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하는 방법입니다. Node.js용 Azure IoT SDK를 사용하여 시뮬레이션된 장치 앱 및 작업을 실행하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: e7e724de68e7078782e90a3f2eda9f7261638ea5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-node"></a>작업 예약 및 브로드캐스트(노드)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub는 백 엔드 앱에서 수백만 개의 장치를 예약 및 업데이트하는 작업을 만들고 추적할 수 있게 하는 완전히 관리되는 서비스입니다.  작업(job)은 다음과 같은 작업(action)에 사용될 수 있습니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

개념적으로 작업(job)은 이러한 작업(action) 중 하나를 래핑하고 장치 쌍 쿼리로 정의된 장치 집합에 대해 실행 진행 상태를 추적합니다.  예를 들어 백 엔드 앱은 작업을 사용하여 장치 쌍 쿼리로 지정되고 향후에 예약된 10,000개 장치에서 다시 부팅 메서드를 호출할 수 있습니다.  그런 다음 응용 프로그램은 해당하는 각 장치에서 재부팅 메서드를 수신 및 실행함에 따라 진행 상태를 추적할 수 있습니다.

이러한 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

* 장치 쌍 및 속성: [장치 쌍 시작][lnk-get-started-twin] 및 [자습서: 장치 쌍 속성을 사용하는 방법][lnk-twin-props]
* 직접 메서드: [IoT Hub 개발자 가이드 - 직접 메서드][lnk-dev-methods] 및 [자습서: 직접 메서드][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 솔루션 백 엔드에서 **lockDoor**를 호출할 수 있는 직접 메서드가 포함된 Node.js로 시뮬레이션된 장치 앱을 만듭니다.
* 작업을 사용하여 시뮬레이션된 장치 앱에서 **lockDoor** 직접 메서드를 호출하고, 장치 작업을 사용하여 desired 속성을 업데이트하는 Node.js 콘솔 앱을 만듭니다.

이 자습서를 마치면 두 가지 Node.js 앱이 만들어집니다.

**simDevice.js** - 장치 ID로 IoT Hub에 연결하고 **lockDoor** 직접 메서드를 수신합니다.

**scheduleJobService.js**는 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 작업을 사용하여 장치 쌍의 desired 속성을 업데이트합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 4.0.x 이상 <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 클라우드에서 호출한 메서드에 응답하는 Node.js 콘솔 앱을 만듭니다. 이 메서드는 시뮬레이션된 **lockDoor** 메서드를 트리거합니다.

1. **simDevice**라는 빈 폴더를 새로 만듭니다.  **simDevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **simDevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **simDevice** 폴더에 새 **simDevice.js** 파일을 만듭니다.
4. **simDevice.js** 파일 앞에 다음 'require' 문을 추가합니다.
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 다음 함수를 추가하여 **lockDoor** 메서드를 처리합니다.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. 다음 코드를 추가하여 **lockDoor** 메서드에 대한 처리기를 등록합니다.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. **simDevice.js** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>직접 메서드를 호출하고 장치 쌍의 속성을 업데이트하기 위한 작업 예약
이 섹션에서는 직접 메서드를 사용하여 장치에서 원격 **lockDoor**를 시작하는 Node.js 콘솔 앱을 만들고 장치 쌍의 속성을 업데이트합니다.

1. **scheduleJobService**라는 빈 폴더를 새로 만듭니다.  **scheduleJobService** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **scheduleJobService** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iothub uuid --save
    ```
3. 텍스트 편집기를 사용하여 **scheduleJobService** 폴더에 새 **scheduleJobService.js** 파일을 만듭니다.
4. 다음 'require' 문을 **dmpatterns_gscheduleJobServiceetstarted_service.js** 파일의 시작 부분에 추가합니다.
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. 다음 변수 선언을 추가하고 자리 표시자 값을 바꿉니다.
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. 작업 실행을 모니터링하는 데 사용되는 다음 함수를 추가합니다.
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. 장치 메서드를 호출하는 작업을 예약하도록 다음 코드를 추가합니다.
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. 장치 쌍을 업데이트하는 작업을 예약하도록 다음 코드를 추가합니다.
   
    ```
    var twinPatch = {
       etag: '*', 
       properties: {
           desired: {
               building: '43', 
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. **scheduleJobService.js** 파일을 저장하고 닫습니다.

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. **simDevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node simDevice.js
    ```
2. **scheduleJobService** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 도어를 잠그고 쌍을 업데이트하는 작업을 트리거합니다.
   
    ```
    node scheduleJobService.js
    ```
3. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 장치에 대한 직접 메서드를 예약하고 장치 쌍의 속성을 업데이트하는 데 작업을 사용했습니다.

IoT Hub 및 장치 관리 패턴(예: 원격 무선 펌웨어 업데이트)을 계속 시작하려면 다음을 참조하세요

[자습서: 펌웨어 업데이트를 수행하는 방법][lnk-fwupdate]

계속해서 IoT Hub를 시작하려면 [Azure IoT Edge 시작][lnk-iot-edge]을 참조하세요.

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
