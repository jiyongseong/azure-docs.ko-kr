---
title: Azure IoT Hub 빠른 시작에서 장치 제어(Python) | Microsoft Docs
description: 이 빠른 시작에서는 두 개의 Python 응용 프로그램 샘플을 실행합니다. 하나의 응용 프로그램은 허브에 연결된 장치를 원격으로 제어할 수 있는 백 엔드 응용 프로그램입니다. 또 다른 응용 프로그램은 원격으로 제어할 수 있는 허브에 연결된 장치를 시뮬레이션 합니다.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 42d70fe28b07f81f4f417612e323359c6dec9468
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>빠른 시작: IoT 허브에 연결된 장치 제어(Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub는 IoT 장치에서 클라우드로 다량의 원격 분석 데이터를 수집하고 클라우드에서 장치를 관리할 수 있게 해주는 Azure 서비스입니다. 이 빠른 시작에서는 *직접 메서드*를 사용하여 IoT 허브에 연결된 시뮬레이션된 장치를 제어합니다. 직접 메서드를 사용하여 IoT 허브에 연결된 장치의 동작을 원격으로 변경할 수 있습니다.

빠른 시작에서는 미리 작성된 두 개의 Python 응용 프로그램을 사용합니다.

* 백 엔드 응용 프로그램에서 호출된 직접 메소드에 응답하는 시뮬레이션된 장치 응용 프로그램입니다. 직접 메서드 호출을 수신하기 위해 이 응용 프로그램을 IoT 허브의 장치별 엔드포인트에 연결합니다.
* 시뮬레이션된 장치에서 직접 메서드를 호출하는 백 엔드 응용 프로그램입니다. 장치에서 직접 메서드를 호출하려면 이 응용 프로그램을 IoT 허브의 서비스 측 엔드포인트에 연결합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 실행하는 두 개의 샘플 응용 프로그램은 Python을 사용하여 작성되었습니다. 개발 컴퓨터에 Python 2.7.x 또는 3.5.x가 필요합니다.

[Python.org](https://www.python.org/downloads/)에서 여러 플랫폼용 Python을 다운로드할 수 있습니다.

다음 명령 중 하나를 사용하여 개발 컴퓨터에서 Python의 현재 버전을 확인할 수 있습니다.

```python
python --version
```

```python
python3 --version
```

아직 수행하지 않은 경우 https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip에서 Python 프로젝트를 샘플을 다운로드하고 ZIP 보관 파일을 추출합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 [빠른 시작: 원격 분석을 장치에서 IoT 허브로 전송](quickstart-send-telemetry-python.md)을 완료한 경우 이 단계를 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>장치 등록

이전 [빠른 시작: 원격 분석을 장치에서 IoT 허브로 전송](quickstart-send-telemetry-python.md)을 완료한 경우 이 단계를 건너뛸 수 있습니다.

연결을 위해 장치를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure CLI를 사용하여 시뮬레이션된 장치를 등록합니다.

1. IoT Hub CLI 확장을 추가하고 장치 ID를 만듭니다. `{YourIoTHubName}`을 IoT 허브 이름으로 바꿉니다.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName}--device-id MyPythonDevice
    ```

1. 방금 등록한 장치의 _장치 연결 문자열_을 가져오려면 다음 명령을 실행합니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    `Hostname=...=`과 같은 장치 연결 문자열을 기록해 둡니다. 이 값은 빠른 시작의 뒷부분에서 사용합니다.

1. 또한 백 엔드 응용 프로그램을 IoT 허브에 연결하여 메시지를 검색할 수 있게 하려면 _서비스 연결 문자열_이 필요합니다. 다음 명령은 IoT Hub에 대한 서비스 연결 문자열을 검색합니다.

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    `Hostname=...=`과 같은 서비스 연결 문자열을 기록해 둡니다. 이 값은 빠른 시작의 뒷부분에서 사용합니다. 서비스 연결 문자열은 장치 연결 문자열과는 다릅니다.

## <a name="listen-for-direct-method-calls"></a>직접 메서드 호출 수신 대기

시뮬레이션된 장치 응용 프로그램은 IoT 허브의 장치별 엔드포인트에 연결하고, 시뮬레이션된 원격 분석을 전송하고, 허브에서 직접 메서드 호출을 수신 대기합니다. 이 빠른 시작에서 허브의 직접 메서드 호출은 장치에 원격 분석을 보내는 간격을 변경하도록 지시합니다. 시뮬레이션된 장치는 직접 메서드를 실행한 후 승인을 다시 허브로 보냅니다.

1. 터미널 창에서 Python 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음 **Quickstarts\simulated-device-2** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **SimulatedDevice.py** 파일을 엽니다.

    `CONNECTION_STRING` 변수의 값을 이전에 적어둔 장치 연결 문자열로 바꿉니다. 그런 다음 변경 사항을 **SimulatedDevice.py** 파일에 저장합니다.

1. 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 장치 응용 프로그램에 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 장치 응용 프로그램을 실행합니다.

    ```cmd/sh
    python SimulatedDevice.py
    ```

    다음 스크린샷에서는 시뮬레이션된 장치 응용 프로그램에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 장치 실행](media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>직접 메서드 호출

백 엔드 응용 프로그램은 IoT Hub의 서비스 측 엔드포인트에 연결합니다. 응용 프로그램은 IoT 허브를 통해 장치에 직접 메서드 호출을 하고 승인을 수신 대기합니다. IoT Hub 백 엔드 응용 프로그램은 일반적으로 클라우드에서 실행됩니다.

1. 다른 터미널 창에서 Python 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음 **Quickstarts\back-end-application** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **BackEndApplication.py** 파일을 엽니다.

    `CONNECTION_STRING` 변수의 값을 이전에 적어둔 서비스 연결 문자열로 바꿉니다. 그런 다음 변경 내용을 **BackEndApplication.py** 파일에 저장합니다.

1. 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 장치 응용 프로그램에 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. 터미널 창에서 다음 명령을 실행하여 백 엔드 응용 프로그램을 실행합니다.

    ```cmd/sh
    python BackEndApplication.py
    ```

    다음 스크린샷에서는 응용 프로그램에서 장치에 직접 메서드를 호출하고 승인을 받을 때의 출력을 보여 줍니다.

    ![백 엔드 응용 프로그램 실행](media/quickstart-control-device-python/BackEndApplication.png)

    백 엔드 응용 프로그램을 실행한 후 시뮬레이션된 장치를 실행하는 콘솔 창에 메시지가 표시되고 메시지를 보내는 속도가 변경됩니다.

    ![시뮬레이션된 클라이언트에서 변경](media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>리소스 정리

다른 자습서로 진행하려는 경우 나중에 다시 사용하기 위해 리소스 그룹과 IoT 허브를 그대로 둡니다.

더 이상 IoT Hub가 필요하지 않으면 포털에서 IoT Hub와 리소스 그룹을 삭제합니다. 이렇게 하려면 IoT Hub가 포함된 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 백 엔드 응용 프로그램에서 장치에 직접 메서드를 호출하고, 시뮬레이션된 장치 응용 프로그램에서 직접 메서드 호출에 응답했습니다.

장치-클라우드 메시지를 클라우드의 다른 대상으로 라우팅하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 처리를 위해 다른 엔드포인트로 원격 분석 라우팅](iot-hub-python-python-process-d2c.md)