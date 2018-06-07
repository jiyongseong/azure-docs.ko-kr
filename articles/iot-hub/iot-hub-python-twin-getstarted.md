---
title: Azure IoT Hub 장치 쌍 시작(Python) | Microsoft Docs
description: Azure IoT Hub 장치 쌍을 사용하여 태그를 추가한 다음 IoT Hub 쿼리를 사용하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 장치 앱 및 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2017
ms.author: kgremban
ms.openlocfilehash: 44b913d5f30a8465219a6793f48d82cfe39d12b2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-device-twins-python"></a>장치 쌍 시작(Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

* **AddTagsAndQuery.py**는 태그를 추가하고 장치 쌍을 쿼리하는 Python 백 엔드 앱입니다.
* **ReportConnectivity.py**는 앞에서 만든 장치 ID와 IoT Hub를 연결하고 연결 상태를 보고하는 장치를 시뮬레이트하는 Python 앱입니다.

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 문서는 장치 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

> [!NOTE]
> `azure-iothub-service-client` 및 `azure-iothub-device-client`에 대한 *pip* 패키지는 현재 Windows OS에만 사용할 수 있습니다. Linux/Mac OS의 경우 [Python용 개발 환경 준비][lnk-python-devbox] 게시물에서 Linux 및 Mac OS에 해당하는 섹션을 참조하세요.
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기
이 섹션에서는 **{장치 ID}**와 연결된 장치 쌍에 위치 메타데이터를 추가하는 Python 콘솔 앱을 만듭니다. 그런 다음 IoT Hub에 저장된 장치 쌍을 쿼리하여 Redmond에 있는 장치를 선택한 다음 셀룰러 연결을 보고하는 장치를 선택합니다.

1. 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK**를 설치합니다. SDK를 설치한 후 명령 프롬프트를 닫습니다.

    ```
    pip install azure-iothub-service-client
    ```

1. 텍스트 편집기를 사용하여 새 **AddTagsAndQuery.py** 파일을 만듭니다.

3. SDK 서비스에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. 다음 코드를 추가하고, `[IoTHub Connection String]` 및 `[Device Id]`의 자리 표시자를 이전 섹션에서 만든 IoT Hub의 연결 문자열 및 장치 ID로 바꿉니다.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. 다음 코드를 **AddTagsAndQuery.py** 파일에 추가합니다.
   
     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
        
            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)
        
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)
        
            print ( "Devices in Redmond: " )        
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
         
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )
        
            print ( "" )
        
            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
                
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```
   
    **레지스트리** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 코드는 먼저 **레지스트리** 개체를 초기화한 다음, **deviceId**에 대한 장치 쌍을 업데이트하고, 마지막으로 두 개의 쿼리를 실행합니다. 첫 번째는 **Redmond43** 공장에 위치한 장치의 장치 쌍만을 선택하고, 두 번째는 또한 셀룰러 네트워크를 통해서 연결된 장치만을 선택하도록 쿼리를 구체화합니다.
   
1. **AddTagsAndQuery.py**의 끝부분에 다음 코드를 추가하고 **iothub_service_sample_run** 함수를 구현합니다.
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. 키를 눌러 응용 프로그램을 실행합니다.
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    **Redmond43**에 위치한 모든 장치를 요청하는 쿼리에 대한 결과로는 하나의 장치를 보고 셀룰러 네트워크를 사용하는 장치에 대해서는 결과를 제한하는 쿼리에 대한 결과로는 아무 장치도 볼 수 없어야 합니다.
   
    ![첫 번째 쿼리][1]

다음 섹션에서는 연결 정보를 보고하고 이전 섹션의 쿼리 결과를 변경하는 장치 앱을 만듭니다.

## <a name="create-the-device-app"></a>장치 앱 만들기
이 섹션에서는 **{장치 ID}**로 허브에 연결하는 Python 콘솔 앱을 만들고 셀룰러 네트워크를 사용하여 연결된 정보를 포함하도록 장치 쌍의 reported 속성을 업데이트합니다.

1. 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK**를 설치합니다. SDK를 설치한 후 명령 프롬프트를 닫습니다.

    ```
    pip install azure-iothub-device-client
    ```

1. 텍스트 편집기를 사용하여 새 **ReportConnectivity.py** 파일을 만듭니다.

3. SDK 서비스에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. 다음 코드를 추가하고, `[IoTHub Device Connection String]`의 자리 표시자를 이전 섹션에서 만든 IoT Hub 장치의 연결 문자열로 바꿉니다.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. 다음 코드를 **ReportConnectivity.py** 파일에 추가하여 장치 쌍 기능을 구현합니다.

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```   

    **Client** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드에서는 **클라이언트** 개체를 초기화한 다음 장치에 대한 장치 쌍을 검색하고, 연결 정보로 reported 속성을 업데이트합니다.

1. **ReportConnectivity.py**의 끝부분에 다음 코드를 추가하고 **iothub_client_sample_run** 함수를 구현합니다.
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. 장치 앱 실행
   
    ```cmd/sh
    python ReportConnectivity.py
    ```
   
    장치 쌍이 업데이트되었다는 확인 메시지가 표시됩니다.

    ![업데이트 쌍][2]

6. 장치가 연결 정보를 보고했으므로 두 쿼리 모두에 나타나야 합니다. 뒤로 돌아가서 쿼리를 다시 실행합니다.
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    이번에는 **{장치 ID}**가 두 쿼리 결과에 모두 나타나야 합니다.
   
    ![두 번째 쿼리][3]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 백 엔드 앱에서 tags로 장치 메타데이터를 추가하고, 장치 쌍에서 장치 연결 정보를 보고하는 시뮬레이션된 장치 앱을 작성했습니다. 또한 레지스트리를 사용하여 이 정보를 쿼리하는 방법도 살펴보았습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작][lnk-iothub-getstarted] 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [desired 속성을 사용하여 장치 구성][lnk-twin-how-to-configure] 자습서를 참조하여 장치 쌍의 desired 속성을 사용하여 장치를 구성합니다.
* [직접 메서드 사용][lnk-methods-tutorial] 자습서를 참조하여 대화형으로(예: 사용자 제어 앱에서 팬 작동) 장치를 제어합니다.

<!-- images -->
[1]: media/iot-hub-python-twin-getstarted/1.png
[2]: media/iot-hub-python-twin-getstarted/2.png
[3]: media/iot-hub-python-twin-getstarted/3.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
