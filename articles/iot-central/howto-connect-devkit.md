---
title: Azure IoT Central 응용 프로그램에 DevKit 장치 연결 | Microsoft Docs
description: 장치 개발자로서 Azure IoT Central 응용 프로그램에 MXChip IoT DevKit 장치를 연결하는 방법을 알아봅니다.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 4c7074e5e7d3858919f3fc17005fea4f8dce1560
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200744"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에 MXChip IoT DevKit 장치 연결

이 문서에서는 장치 개발자로서 MXChip IoT DevKit(DevKit) 장치를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

1. **샘플 Devkits** 응용 프로그램 템플릿으로 만든 Azure IoT Central 응용 프로그램. 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](howto-create-application.md)를 참조하세요.
1. DevKit 장치. DevKit 장치를 구매하려면 [MXChip IoT DevKit](http://mxchip.com/az3166)를 방문하세요.

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **MXChip** 장치가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | Units  | 최소 | 최대 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | Units | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 전압      | setVoltage | 볼트 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 암페어  | 0              | 0       | 100     | 0       |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

설정 전환

| 표시 이름 | 필드 이름 | 온 텍스트 | 오프 텍스트 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 켜기      | 끄기      | 꺼짐     |

### <a name="properties"></a>properties

| type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 장치 속성 | 다이 번호   | dieNumber  | number    |
| 텍스트            | 위치     | location   | 해당 없음       |

### <a name="states"></a>상태 

| Name          | 표시 이름   | 정상 | 주의 | 위험 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | 장치 상태   | 녹색  | 주황색  | 빨강    | 

### <a name="events"></a>이벤트 

| Name             | 표시 이름      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | 단추 B 누름  | 

### <a name="add-a-real-device"></a>실제 장치 추가

Azure IoT Central 응용 프로그램에서 **MXChip** 장치 템플릿으로 실제 장치를 추가하고 장치 연결 문자열을 기록해 둡니다. 자세한 내용은 [Azure IoT Central 응용 프로그램에 실제 장치 추가](tutorial-add-device.md)를 참조하세요.

## <a name="prepare-the-devkit-device"></a>DevKit 장치 준비

> [!TIP]
> DevKit 장치 문제 해결 지침은 [IoT DevKit 시작](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/)을 참조하세요.

DevKit 장치를 준비하려면:

1. GitHub의 [릴리스](https://github.com/Azure/iot-central-firmware/releases) 페이지에서 MXChip의 최신 사전 빌드 Azure IoT Central 펌웨어를 다운로드합니다. 릴리스 페이지의 다운로드 파일 이름은 `AZ3166-IoT-Central-X.X.X.bin` 형식입니다.

1. USB 케이블을 사용하여 DevKit 장치를 개발 컴퓨터에 연결합니다. Windows에서, DevKit 장치의 저장소에 매핑된 드라이브에서 파일 탐색기 창이 열립니다. 예를 들어 드라이브 이름이 **AZ3166 (D:)** 일 수 있습니다.

1. **iotCentral.bin** 파일을 드라이브 창으로 끌어다 놓습니다. 복사가 완료되면 새 펌웨어를 사용하여 장치가 다시 부팅됩니다.

1. DevKit 장치가 다시 시작되면 다음 화면이 표시됩니다.

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 화면에 다른 내용이 표시되면 장치의 **재설정** 단추를 누릅니다. 

1. 이제 장치가 AP(액세스 지점) 모드에 있습니다. 컴퓨터 또는 모바일 장치에서 이 WiFi 액세스 지점에 연결할 수 있습니다.

1. 컴퓨터, 휴대폰 또는 태블릿에서 장치 화면에 표시된 WiFi 네트워크 이름에 연결합니다. 이 네트워크에 연결하면 인터넷에 액세스할 수 없습니다. 이 상태는 예상된 동작이며, 장치를 구성할 때까지 잠깐 동안 이 네트워크에만 연결됩니다.

1. 웹 브라우저를 열고 [http://192.168.0.1/start](http://192.168.0.1/start)로 이동합니다. 다음 웹 페이지가 표시됩니다.

    ![장치 구성 페이지](media/howto-connect-devkit/configpage.png)

    웹 페이지에서 다음을 추가합니다. 
    - WiFi 네트워크 이름 
    - WiFi 네트워크 암호 
    - LCD 장치에 표시된 PIN 코드 
    - 장치의 연결 문자열. 
      `https://apps.iotcentral.com` -> `Device Explorer` -> `Device` -> `Select or Create a new Real Device` -> `Connect this device`에서(오른쪽 위) 연결 문자열을 찾을 수 있습니다. 
    - 사용 가능한 모든 원격 분석 측정값을 선택합니다. 

1. **장치 구성**을 선택하면 이 페이지가 표시됩니다.

    ![구성된 장치](media/howto-connect-devkit/deviceconfigured.png)

1. 장치의 **재설정** 단추를 누릅니다.

> [!NOTE]
> 다른 WiFi 네트워크, 연결 문자열 또는 원격 분석 측정값을 사용하도록 장치를 다시 구성하려면 보드의 **A** 및 **B** 단추를 동시에 누릅니다. 이 방법이 작동하지 않으면 **재설정** 단추를 눌러 다시 시도합니다. 

## <a name="view-the-telemetry"></a>원격 분석 보기

DevKit 장치가 다시 시작되면 장치에 다음과 같은 화면이 표시됩니다.

* 전송된 원격 분석 메시지 수.
* 실패 횟수.
* 받은 desired 속성 수 및 보낸 reported 속성 수.

장치를 흔들면 보낸 reported 속성 수가 증가합니다. 장치에서 **다이 번호** 장치 속성으로 임의의 숫자를 보냅니다.

원격 분석 측정값 및 reported 속성 값을 확인하고, Azure IoT Central에서 설정을 구성할 수 있습니다.

1. **Device Explorer**를 사용하여 추가한 실제 MXChip 장치에 대한 **측정값** 페이지로 이동합니다.

    ![실제 장치로 이동](media/howto-connect-devkit/realdevice.png)

1. **측정값** 페이지에서 MXChip 장치에서 보낸 원격 분석 데이터를 볼 수 있습니다.

    ![실제 장치의 원격 분석 데이터 보기](media/howto-connect-devkit/realtelemetry.png)

1. **속성** 페이지에서 장치에서 전송된 마지막 다이 번호를 볼 수 있습니다.

    ![장치 속성 보기](media/howto-connect-devkit/deviceproperties.png)

1. **설정** 페이지에서 MXChip 장치의 설정을 업데이트할 수 있습니다.

    ![장치 설정 보기](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>소스 코드 다운로드

장치 코드를 탐색하고 수정하려는 경우 GitHub에서 장치 코드를 다운로드하면 됩니다. 코드를 수정하려는 경우 다음 지침에 따라 사용 중인 데스크톱 운영 체제의 [개발 환경을 준비](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)해야 합니다.

소스 코드를 다운로드하려면 데스크톱 컴퓨터에서 다음 명령을 실행합니다.

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

이전 명령은 `iot-central-firmware` 폴더에 소스 코드를 다운로드합니다. 

> [!NOTE]
> 개발 환경에 **git**이 설치되지 않은 경우 [https://git-scm.com/download](https://git-scm.com/download)에서 다운로드할 수 있습니다.

## <a name="review-the-code"></a>코드 검토

Visual Studio Code를 사용하여(개발 환경을 준비할 때 설치했음) `iot-central-firmware` 폴더 안에 있는 `AZ3166` 폴더를 엽니다. 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Azure IoT Central 응용 프로그램으로 원격 분석 데이터가 전송되는 원리를 살펴보려면 원본 폴더의 **main_telemetry.cpp** 파일을 여세요.

`buildTelemetryPayload` 함수는 장치의 센서 데이터를 사용하여 JSON 원격 분석 페이로드를 만듭니다.

`sendTelemetryPayload` 함수는 **iotHubClient.cpp**에서 Azure IoT Central 응용 프로그램이 사용하는 IoT Hub에 JSON 페이로드를 보내는 `sendTelemetry`를 호출합니다.

Azure IoT Central 응용 프로그램으로 속성 값이 보고되는 원리를 살펴보려면 원본 폴더의 **main_telemetry.cpp** 파일을 여세요.

`telemetryLoop` 함수는 가속도계가 두 번 탭을 감지하면 **doubleTap** reported 속성을 보냅니다. **iotHubClient.cpp** 원본 파일의 `sendReportedProperty` 함수를 사용합니다.

**iotHubClient.cpp** 원본 파일의 코드는 [C용 Microsoft Azure IoT SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-c)의 함수를 사용하여 IoT Hub와 상호 작용합니다.

샘플 코드를 수정하고, 빌드하고, 장치에 업로드하는 방법은 `AZ3166` 폴더에 있는 **readme.md** 파일을 참조하세요.

## <a name="next-steps"></a>다음 단계

DevKit 장치를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

* [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md)