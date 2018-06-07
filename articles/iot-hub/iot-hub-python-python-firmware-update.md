---
title: Azure IoT Hub를 사용하여 장치 펌웨어 업데이트(Python) | Microsoft Docs
description: 장치 펌웨어 업데이트를 시작하려면 Azure IoT Hub에서 장치 관리를 사용하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 장치 앱 및 펌웨어 업데이트를 트리거하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 4a042723e6bebebb891640950e579e031e27e61d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>장치 관리를 사용하여 장치 펌웨어 업데이트(Python/Python)를 시작합니다.
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

[장치 관리 시작][lnk-dm-getstarted] 자습서에서 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod] 기본 형식을 사용하여 장치를 원격으로 다시 부팅하는 방법을 살펴보았습니다. 이 자습서는 동일한 IoT Hub 기본 형식을 사용하며 종단 간 시뮬레이션된 펌웨어 업데이트를 수행하는 방법을 보여주고 지침을 제공합니다.  이러한 패턴이 Intel Edison 장치 샘플에 대한 펌웨어 업데이트 구현에 사용됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* IoT Hub를 통해 시뮬레이션된 장치 앱에서 firmwareUpdate 직접 메서드를 호출하는 Python 콘솔 앱을 만듭니다.
* **firmwareUpdate** 직접 메서드를 구현하는 시뮬레이트된 장치 앱을 만듭니다. 이 메서드는 펌웨어 이미지를 다운로드하기 위해 대기했다가 펌웨어 이미지를 다운로드하고, 마지막으로 펌웨어 이미지를 적용하는 다단계 프로세스를 시작합니다. 업데이트의 각 단계 동안, 장치는 보고된 속성을 사용하여 진행 상황을 보고합니다.

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

**dmpatterns_fwupdate_service.py** - 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 주기적으로(매 500밀리초) 표시합니다.

**dmpatterns_fwupdate_device.py** - 이전에 생성한 장치 ID를 사용하여 IoT Hub에 연결하고, firmwareUpdate 직접 메서드를 수신하고, 펌웨어 업데이트를 시뮬레이션하기 위한 다단계 프로세스를 실행합니다. 여기에는 이미지 다운로드를 위한 대기, 새 이미지 다운로드, 마지막으로 이미지 적용이 포함됩니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트 트리거
이 섹션에서는 장치에서 원격 펌웨어 업데이트를 시작하는 Python 콘솔 앱을 만듭니다. 앱은 직접 메서드를 사용하여 업데이트를 시작하고 장치 쌍 쿼리를 사용하여 정기적으로 활성 펌웨어 업데이트의 상태를 가져옵니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub-service-client** 패키지를 설치합니다.
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. 작업 디렉터리에서 텍스트 편집기를 사용하여 **dmpatterns_getstarted_service.py** 파일을 만듭니다.

1. **dmpatterns_getstarted_service.py** 파일의 시작 부분에 다음 ‘import’ 문과 변수를 추가합니다. `IoTHubConnectionString` 및 `deviceId`를 이전에 설명된 값으로 바꿉니다.
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. 직접 메서드를 호출하고 firmwareUpdate 보고된(reported) 속성의 값을 표시하도록 다음 함수를 추가합니다. `main` 루틴도 추가합니다.
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. **dmpatterns_fwupdate_service.py** 파일을 저장하고 닫습니다.


## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Python 콘솔 앱 만들기
* 시뮬레이션된 펌웨어 업데이트 트리거
* reported 속성을 사용하여 장치 및 해당 장치가 마지막으로 펌웨어 업데이트를 완료한 시간을 확인하는 장치 쌍 쿼리를 사용하도록 설정

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub-device-client** 패키지를 설치합니다.
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 텍스트 편집기를 사용하여 **dmpatterns_fwupdate_device.py** 파일을 만듭니다.

1. **dmpatterns_fwupdate_device.py** 파일의 시작 부분에 다음 ‘import’ 문과 변수를 추가합니다. IoT 허브의 장치 연결 문자열로 `deviceConnectionString`을 바꿉니다.
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. 보고된 속성 업데이트를 제공하고 직접 메서드를 구현하는 데 사용되는 다음 함수를 추가합니다.
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. 펌웨어 이미지의 다운로드 및 적용을 시뮬레이션하는 다음 함수를 추가합니다.
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. 장치 쌍의 보고된 속성을 초기화하는 다음 함수를 추가하고 직접 메서드가 호출될 때까지 대기합니다. `main` 루틴도 추가합니다.
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리](https://msdn.microsoft.com/library/hh675232.aspx)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 


## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 다시 부팅 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. 다른 명령 프롬프트에서 다음 명령을 실행하여 원격 다시 부팅을 트리거하고 장치 쌍을 쿼리하여 마지막 다시 부팅 시간을 찾습니다.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다. 그런 다음, 펌웨어 업데이트를 통해 보고된 속성의 변경 사항을 기록합니다.

    ![프로그램 출력][1]


## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 트리거하고, 보고된 속성을 사용하여 펌웨어 업데이트 프로세스의 진행 상황을 이해했습니다.

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
