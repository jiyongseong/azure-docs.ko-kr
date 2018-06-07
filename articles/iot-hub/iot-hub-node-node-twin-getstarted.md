---
title: "Azure IoT Hub 장치 쌍 시작(노드) | Microsoft Docs"
description: "Azure IoT Hub 장치 쌍을 사용하여 태그를 추가한 다음 IoT Hub 쿼리를 사용하는 방법입니다. Node.js용 Azure IoT SDK를 사용하여 시뮬레이션된 장치 앱 및 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: df49f054b5eb26c3d68f088bc05f5209cf2ebccf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-node"></a>장치 쌍 시작(노드)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서의 끝 부분에 다음 두 개의 Node.js 콘솔 앱이 제공됩니다.

* **AddTagsAndQuery.js**, 태그를 추가하고 장치 쌍을 쿼리하는 Node.js 백 엔드 앱입니다.
* **TwinSimulatedDevice.js** - 앞에서 만든 장치 ID와 IoT Hub를 연결하고 연결 상태를 보고하는 장치를 시뮬레이션하는 Node.js 앱입니다.

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 문서는 장치 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 4.0.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기
이 섹션에서는 **myDeviceId**와 연결된 장치 쌍에 위치 메타데이터를 추가하는 Node.js 콘솔 앱을 만듭니다. 그런 다음 IoT Hub에 저장된 장치 쌍을 쿼리하여 미국에 있는 장치를 선택한 다음 셀룰러 연결을 보고하는 장치를 선택합니다.

1. **addtagsandqueryapp**라는 빈 폴더를 새로 만듭니다. **addtagsandqueryapp** 폴더의 명령 프롬프트에 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **addtagsandqueryapp** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.
   
    ```
    npm install azure-iothub --save
    ```
3. 텍스트 편집기를 사용하여 **addtagsandqueryapp** 폴더에 새 **AddTagsAndQuery.js** 파일을 만듭니다.
4. 다음 코드를 **AddTagsAndQuery.js** 파일에 추가하고 허브를 만들 때 복사한 IoT Hub 연결 문자열을 사용해 **{iot hub 연결 문자열}** 자리 표시자를 대체합니다.
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    **레지스트리** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드는 **Registry** 개체를 초기화한 다음 **myDeviceId**에 대한 장치 쌍을 검색하고, 마지막으로 원하는 위치 정보를 사용해 태그를 업데이트합니다.
   
    태그를 업데이트한 후에는 **queryTwins** 함수를 호출합니다.
5. 다음 코드를 **queryTwins** 함수를 구현할 **AddTagsAndQuery.js** 끝 부분에 추가합니다.
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    이전 코드는 두 개의 쿼리를 실행합니다. 첫 번째는 **Redmond43** 공장에 위치한 장치의 장치 쌍만을 선택하고, 두 번째는 또한 셀룰러 네트워크를 통해서 연결된 장치만을 선택하기 위해 쿼리를 구체화합니다.
   
    이전 코드는 **쿼리** 개체를 만들 때 반환되는 최대 문서 수를 지정합니다. **query** 개체에는 모든 결과를 검색하기 위해 여러번 **nextAsTwin** 메서드를 호출하는 데 사용할 수 있는 **hasMoreResults** 부울 속성이 들어 있습니다. **next**라는 메서드는 장치 쌍이 아닌 결과(예: 집계 쿼리의 결과)에 대해 사용할 수 있습니다.
6. 키를 눌러 응용 프로그램을 실행합니다.
   
        node AddTagsAndQuery.js
   
    **Redmond43**에 위치한 모든 장치를 요청하는 쿼리에 대한 결과로는 하나의 장치를 보고 셀룰러 네트워크를 사용하는 장치에 대해서는 결과를 제한하는 쿼리에 대한 결과로는 아무 장치도 볼 수 없어야 합니다.
   
    ![][1]

다음 섹션에서는 연결 정보를 보고하고 이전 섹션의 쿼리 결과를 변경하는 장치 앱을 만듭니다.

## <a name="create-the-device-app"></a>장치 앱 만들기
이 섹션에서는 **myDeviceId**로 허브에 연결하는 Node.js 콘솔 앱을 만들고 셀룰러 네트워크를 사용하여 연결된 정보를 포함하도록 장치 쌍의 reported 속성을 업데이트합니다.


1. **reportconnectivity**라는 빈 폴더를 새로 만듭니다. **reportconnectivity** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **reportconnectivity** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **reportconnectivity** 폴더에 새 **ReportConnectivity.js** 파일을 만듭니다.
4. 다음 코드를 **ReportConnectivity.js** 파일에 추가하고 **myDeviceId** 장치 ID를 만들 때 복사한 장치 연결 문자열을 사용해 **{장치 연결 문자열}** 자리 표시자를 대체합니다.
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Client** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드에서는 **Client** 개체를 초기화한 다음 **myDeviceId**에 대한 장치 쌍을 검색하고, 연결 정보로 reported 속성을 업데이트합니다.
5. 장치 앱 실행
   
        node ReportConnectivity.js
   
    메시지 `twin state reported`이 표시되어야 합니다.
6. 장치가 연결 정보를 보고했으므로 두 쿼리 모두에 나타나야 합니다. **addtagsandqueryapp** 폴더로 돌아가 쿼리를 다시 실행합니다.
   
        node AddTagsAndQuery.js
   
    이번에는 **myDeviceId**가 두 쿼리 결과에 모두 나타나야 합니다.
   
    ![][3]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 백 엔드 앱에서 tags로 장치 메타데이터를 추가하고, 장치 쌍에서 장치 연결 정보를 보고하는 시뮬레이션된 장치 앱을 작성했습니다. 또한 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 이 정보를 쿼리하는 방법도 살펴보았습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작][lnk-iothub-getstarted] 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [desired 속성을 사용하여 장치 구성][lnk-twin-how-to-configure] 자습서를 참조하여 장치 쌍의 desired 속성을 사용하여 장치를 구성합니다.
* [직접 메서드 사용][lnk-methods-tutorial] 자습서를 참조하여 대화형으로(예: 사용자가 제어하는 앱에서 팬을 켬) 장치를 제어합니다.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
