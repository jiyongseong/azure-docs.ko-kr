---
title: Azure IoT Hub(Python)를 사용한 클라우드-장치 메시지 | Microsoft Docs
description: Python용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 장치로 클라우드-장치 메시지를 보내는 방법입니다. 클라우드-장치 메시지를 수신하도록 시뮬레이션된 장치 앱을 수정하고 클라우드-장치 메시지를 보내도록 백 엔드 앱을 수정합니다.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 236758f1ff83ec37c39a44068879bea80f2ffdb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub(Python)를 사용하여 클라우드-장치 메시지 보내기
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>소개
Azure IoT Hub는 수백만 개의 장치와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [IoT Hub 시작하기] 자습서에서는 IoT Hub를 만들고 그 안에 장치 ID를 프로비전하고 장치-클라우드 메시지를 보내는 시뮬레이션된 장치 앱을 코딩하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [IoT Hub 시작하기]를 토대로 작성되었습니다. 이 항목에서는 다음 방법을 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-장치 메시지를 단일 장치로 보냅니다.
* 장치에서 클라우드-장치 메시지를 받습니다.
* 솔루션 백 엔드에서, IoT Hub에서 장치로 보낸 메시지에 대한 배달 확인(*피드백*)을 요청합니다.

클라우드-장치 메시지에 자세한 내용은 [IoT Hub 개발자 가이드][IoT Hub developer guide - C2D]에서 찾아볼 수 있습니다.

이 자습서의 끝 부분에서 다음의 두 Python 콘솔 앱을 실행합니다.

* **SimulatedDevice.py** - [IoT Hub 시작하기]에서 만든 앱의 수정된 버전으로서 IoT Hub에 연결하고 클라우드-장치 메시지를 수신합니다.
* **SendCloudToDeviceMessage.py** - IoT Hub를 통해 시뮬레이션된 장치 앱에 클라우드-장치 메시지를 보낸 다음 배달 승인을 수신합니다.

> [!NOTE]
> IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 장치 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.
> 

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

> [!NOTE]
> `azure-iothub-service-client` 및 `azure-iothub-device-client`에 대한 *pip* 패키지는 현재 Windows OS에만 사용할 수 있습니다. Linux/Mac OS의 경우 [Python용 개발 환경 준비][lnk-python-devbox] 게시물에서 Linux 및 Mac OS에 해당하는 섹션을 참조하세요.
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 장치 앱에서 메시지 수신
이 섹션에서는 장치를 시뮬레이션하고 IoT Hub에서 클라우드-장치 메시지를 수신하는 Python 콘솔 앱을 만듭니다.

1. 텍스트 편집기를 사용하여 **SimulatedDevice.py** 파일을 만듭니다.

1. **SimulatedDevice.py** 파일 앞에 다음 `import` 문 및 변수를 추가합니다.
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. 다음 코드를 **SimulatedDevice.py** 파일에 추가합니다. "{deviceConnectionString}" 자리 표시자 값을 [IoT Hub 시작하기] 자습서에서 만든 장치에 대한 장치 연결 문자열로 대체합니다.
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. 다음 함수를 추가하여 수신한 메시지를 콘솔에 인쇄합니다.
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. 다음 코드를 추가하여 클라이언트를 초기화하고 클라우드-장치 메시지를 수신하기 위해 대기합니다.
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. 다음 main 함수를 추가합니다.
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. **SimulatedDevice.py** 파일을 저장하고 닫습니다.


## <a name="send-a-cloud-to-device-message"></a>클라우드-장치 메시지 보내기
이 섹션에서는 클라우드-장치 메시지를 시뮬레이션된 장치 앱으로 보내는 Python 콘솔 응용 프로그램을 만듭니다. [IoT Hub 시작하기] 자습서에서 추가한 장치의 장치 ID가 필요합니다. [Azure Portal]에서 찾을 수 있는 허브에 대한 IoT Hub 연결 문자열도 필요합니다.

1. 텍스트 편집기를 사용하여 **SendCloudToDeviceMessage.py** 파일을 만듭니다.

1. **SendCloudToDeviceMessage.py** 파일 앞에 다음 `import` 문 및 변수를 추가합니다.
   
    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. **SendCloudToDeviceMessage.py** 파일에 다음 코드를 추가합니다. “{IoTHubConnectionString}” 자리 표시자 값을 [IoT Hub 시작하기] 자습서에서 만든 허브에 대한 IoT Hub 연결 문자열로 바꿉니다. “{deviceId}” 자리 표시자 값을 [IoT Hub 시작하기] 자습서에서 추가한 장치의 장치 ID로 바꿉니다.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. 다음 함수를 추가하여 피드백 메시지를 콘솔에 출력합니다.
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. 다음 코드를 추가하여 장치에 메시지를 보내고 장치가 클라우드-장치 메시지를 승인할 때 피드백 메시지를 처리합니다.
   
    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

1. 다음 main 함수를 추가합니다.
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. **SendCloudToDeviceMessage.py** 파일을 저장한 후 닫습니다.


## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. 명령 프롬프트를 열고 **Python용 Azure IoT Hub 장치 SDK**를 설치합니다.

    ```
    pip install azure-iothub-device-client
    ```

1. 명령 프롬프트에서 다음 명령을 실행하여 클라우드-장치 메시지를 수신합니다.
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![시뮬레이션된 장치 앱 실행][img-simulated-device]

1. 새 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK**를 설치합니다.

    ```
    pip install azure-iothub-service-client
    ```

1. 명령 프롬프트에서 다음 명령을 실행하여 클라우드-장치 메시지를 보내고 메시지 피드백을 대기합니다.
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![앱을 실행하여 클라우드-장치 명령 보내기][img-send-command]
   
1. 장치에서 수신한 메시지를 기록해 둡니다.

    ![수신된 메시지][img-message-recieved]


## <a name="next-steps"></a>다음 단계
이 자습서에서 클라우드-장치 메시지를 보내고 받는 방법을 알아보았습니다. 

IoT Hub를 사용하는 전체 종단 간 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기]를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드]를 참조하세요.

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[IoT Hub 시작하기]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[Azure IoT 개발자 센터]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Azure IoT 원격 모니터링 솔루션 가속기]: https://azure.microsoft.com/documentation/suites/iot-suite/
