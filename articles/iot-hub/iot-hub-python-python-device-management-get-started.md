---
title: Azure IoT Hub 장치 관리 시작(Python) | Microsoft Docs
description: IoT Hub 장치 관리를 사용하여 원격 장치 재부팅을 시작하는 방법입니다. Python용 Azure IoT SDK를 사용하여 직접 메서드를 포함한 시뮬레이션된 장치 앱 및 직접 메서드를 호출하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 001a5d180e2b97894174ba7c63ec3947c9968364
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-device-management-python"></a>장치 관리 시작(Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 장치 ID를 만듭니다.
* 장치를 다시 시작하는 직접 메서드가 포함된 시뮬레이트된 장치 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.
* IoT Hub를 통해 시뮬레이션된 장치 앱에서 재부팅 직접 메서드를 호출하는 Python 콘솔 앱을 만듭니다.

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

**dmpatterns_getstarted_device.py**, 이전에 만든 장치 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이션하며 마지막 재부팅 시간을 보고합니다.

**dmpatterns_getstarted_service.py**, 시뮬레이션된 장치 앱에 직접 메서드를 호출하고 응답을 표시하고 업데이트된 reported 속성을 표시합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
    * 다음 명령을 사용하여 [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) 패키지를 설치합니다. `pip install azure-iothub-device-client`
    * 다음 명령을 사용하여 [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) 패키지를 설치합니다. `pip install azure-iothub-service-client`
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Python 콘솔 앱 만들기
* 장치 다시 부팅 시뮬레이션
* reported 속성을 사용하여 장치 및 해당 장치가 마지막으로 재부팅한 시간을 확인하는 장치 쌍 쿼리를 사용하도록 설정

1. 텍스트 편집기를 사용하여 **dmpatterns_getstarted_device.py** 파일을 만듭니다.

1. 다음 `import` 문을 **dmpatterns_getstarted_device.py** 파일의 시작 부분에 추가합니다.
   
    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

1. **CONNECTION_STRING** 변수 및 클라이언트 초기화를 포함하여 변수를 추가합니다.  연결 문자열을 장치 연결 문자열로 바꿉니다.  
   
    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

1. 장치에서 직접 메서드를 구현하도록 다음 함수 콜백을 추가합니다.
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS
    
        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
        
            time.sleep(20)
        
            print ( "Device rebooted." )
        
            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
            print ( "Updating device twins: rebootTime" )
            
        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200
    
        return device_method_return_value
    ```

1. 직접 메서드 수신기를 시작하고 기다립니다.
   
    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
        
    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. **dmpatterns_getstarted_device.py** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 재부팅 트리거
이 섹션에서는 장치에서 직접 메서드를 사용하여 원격 다시 부팅을 시작하는 Python 콘솔 앱을 만듭니다. 앱은 장치 쌍 쿼리를 사용하여 해당 장치에 대한 마지막 다시 시작 시간을 검색합니다.

1. 텍스트 편집기를 사용하여 **dmpatterns_getstarted_service.py** 파일을 만듭니다.

1. 다음 `import` 문을 **dmpatterns_getstarted_service.py** 파일의 시작 부분에 추가합니다.
   
    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

1. 다음 변수 선언을 추가합니다. _IoTHubConnectionString_ 및 _deviceId_에 대한 자리 표시자 값만 바꿉니다.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. 장치 메서드를 호출하여 대상 장치를 다시 부팅한 다음, 장치 쌍을 쿼리하고 마지막 다시 부팅 시간을 가져오도록 다음 함수를 추가합니다.
   
    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )
        
            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                
                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

1. **dmpatterns_getstarted_service.py** 파일을 저장하고 닫습니다.


## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 다시 부팅 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    python dmpatterns_getstarted_device.py
    ```

1. 다른 명령 프롬프트에서 다음 명령을 실행하여 원격 다시 부팅을 트리거하고 장치 쌍을 쿼리하여 마지막 다시 부팅 시간을 찾습니다.
   
    ```
    python dmpatterns_getstarted_service.py
    ```

1. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
