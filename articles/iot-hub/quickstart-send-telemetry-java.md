---
title: Azure IoT Hub에 원격 분석 데이터 보내기 빠른 시작(Java) | Microsoft Docs
description: 이 빠른 시작에서는 두 개의 Java 응용 프로그램 샘플을 실행하여 시뮬레이션된 원격 분석을 IoT Hub에 보내고, 클라우드에서 처리할 원격 분석을 IoT Hub에서 읽습니다.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: d887c690a5f0bc8120daa74d6076083634da08f6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-java"></a>빠른 시작: 장치에서 IoT 허브로 원격 분석 데이터 보내기 및 백 엔드 응용 프로그램으로 허브에서 원격 분석 읽기(Java)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub는 저장 또는 처리를 위해 IoT 장치에서 클라우드로 다량의 원격 분석 데이터를 수집할 수 있게 해주는 Azure 서비스입니다. 이 빠른 시작에서는 시뮬레이션된 장치 응용 프로그램에서 IoT Hub를 통해 백 엔드 응용 프로그램으로 원격 분석을 처리를 위해 보냅니다.

여기서는 미리 작성된 두 개의 Java 응용 프로그램을 사용합니다. 하나는 원격 분석을 보내고, 다른 하나는 허브에서 원격 분석을 읽습니다. 이 두 응용 프로그램을 실행하기 전에 IoT 허브를 만들고 허브에 장치를 등록합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 실행하는 두 개의 샘플 응용 프로그램은 Java를 사용하여 작성되었습니다. 개발 컴퓨터에 Java SE 8 이상이 필요합니다.

[Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html)에서 여러 플랫폼용 Java를 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Java의 현재 버전을 확인할 수 있습니다.

```cmd/sh
java --version
```

샘플을 빌드하려면 Maven 3을 설치해야 합니다. [Apache Maven](https://maven.apache.org/download.cgi)에서 여러 플랫폼용 Maven을 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Maven의 현재 버전을 확인할 수 있습니다.

```cmd/sh
mvn --version
```

https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip에서 Java 프로젝트 샘플을 다운로드하고 ZIP 보관 파일을 추출합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>장치 등록

연결을 위해 장치를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure CLI를 사용하여 시뮬레이션된 장치를 등록합니다.

1. IoT Hub CLI 확장을 추가하고 장치 ID를 만듭니다. `{YourIoTHubName}`을 IoT 허브에 대해 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName}--device-id MyJavaDevice
    ```

1. 방금 등록한 장치의 _장치 연결 문자열_을 가져오려면 다음 명령을 실행합니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    `Hostname=...=`과 같은 장치 연결 문자열을 기록해 둡니다. 이 값은 빠른 시작의 뒷부분에서 사용합니다.

1. 또한 백 엔드 응용 프로그램이 IoT 허브에 연결하고 메시지를 검색할 수 있도록 하려면 IoT 허브에서 _Event Hubs 호환 엔드포인트_, _Event Hubs 호환 경로_ 및 _iothubowner 기본 키_가 필요합니다. 다음 명령은 IoT 허브에 대해 이 값을 검색합니다.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    빠른 시작의 뒷부분에서 사용하기 위해 이 세 개의 값을 적어 둡니다.

## <a name="send-simulated-telemetry"></a>시뮬레이션된 원격 분석 전송

시뮬레이션된 장치 응용 프로그램은 IoT 허브의 장치 관련 엔드포인트에 연결하고 시뮬레이션된 온도 및 습도 원격 분석을 전송합니다.

1. 터미널 창에서 Java 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음 **Quickstarts\simulated-device** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** 파일을 엽니다.

    `connString` 변수의 값을 이전에 적어둔 장치 연결 문자열로 바꿉니다. 그런 다음 변경 내용을 **SimulatedDevice.java** 파일에 저장합니다.

1. 터미널 창에서 다음 명령을 실행하여 필요한 라이브러리를 설치하고, 시뮬레이션된 장치 응용 프로그램을 빌드합니다.

    ```cmd/sh
    mvn clean package
    ```

1. 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 장치 응용 프로그램을 실행합니다.

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    다음 스크린샷에서는 시뮬레이션된 장치 응용 프로그램에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 장치 실행](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>허브에서 원격 분석 읽기

백 엔드 응용 프로그램은 IoT Hub의 서비스 측 **이벤트** 엔드포인트에 연결합니다. 응용 프로그램은 시뮬레이션된 장치에서 전송하는 장치-클라우드 메시지를 받습니다. IoT Hub 백 엔드 응용 프로그램은 일반적으로 클라우드에서 실행되며 장치-클라우드 메시지를 수신하고 처리합니다.

1. 다른 터미널 창에서 Java 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음 **Quickstarts\read-d2c-messages** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** 파일을 엽니다.

    `eventHubsCompatibleEndpoint` 변수의 값을 이전에 적어둔 Event Hubs 호환 엔드포인트로 바꿉니다.

    `eventHubsCompatiblePath` 변수의 값을 이전에 적어둔 Event Hubs 호환 경로로 바꿉니다.

    `iotHubSasKey` 변수의 값을 이전에 적어둔 iothubowner 기본 키로 바꿉니다. 그런 다음 변경 내용을 **ReadDeviceToCloudMessages.java** 파일에 저장합니다.

1. 터미널 창에서 다음 명령을 실행하여 필요한 라이브러리를 설치하고, 백 엔드 응용 프로그램을 빌드합니다.

    ```cmd/sh
    mvn clean package
    ```

1. 터미널 창에서 다음 명령을 실행하여 백 엔드 응용 프로그램을 실행합니다.

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    다음 스크린샷에서는 시뮬레이션된 장치에서 허브에 보낸 원격 분석을 백 엔드 응용 프로그램에서 받을 때의 출력을 보여 줍니다.

    ![백 엔드 응용 프로그램 실행](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 빠른 시작을 완료하려면 리소스 그룹과 IoT 허브를 그대로 두었다가 나중에 다시 사용합니다.

더 이상 IoT Hub가 필요하지 않으면 포털에서 IoT Hub와 리소스 그룹을 삭제합니다. 이렇게 하려면 IoT Hub가 포함된 **qs-iot-hub-rg** 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 허브를 설정하고, 장치를 등록하고, Java 응용 프로그램을 사용하여 허브에 시뮬레이션된 원격 분석 데이터를 보내고, 간단한 백 엔드 응용 프로그램을 사용하여 허브에서 원격 분석 데이터를 읽습니다.

백 엔드 응용 프로그램에서 시뮬레이션된 장치를 제어하는 방법을 알아보려면 다음 빠른 시작을 계속합니다.

> [!div class="nextstepaction"]
> [빠른 시작: IoT 허브에 연결된 장치 제어](quickstart-control-device-java.md)