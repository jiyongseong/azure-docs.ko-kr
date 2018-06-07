---
title: Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결(Python) | Microsoft Docs
description: 장치 개발자로서 Python을 사용하여 Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아봅니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 23ab31d6d2357bfcb184e5b3022155bef5ace658
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199880"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Azure IoT Central 응용 프로그램에 Raspberry Pi 연결(Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

이 문서에서는 장치 개발자로서 Python 프로그래밍 언어를 사용하여 Raspberry Pi를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

* **샘플 Devkits** 응용 프로그램 템플릿으로 만든 Azure IoT Central 응용 프로그램. 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](howto-create-application.md)를 참조하세요.
* Raspbian 운영 체제를 실행하는 Raspberry Pi 장치. GUI 환경에 액세스하려면 Raspberry Pi에 연결된 모니터, 키보드 및 마우스가 필요합니다. Raspberry Pi가 [인터넷에 연결](https://www.raspberrypi.org/learning/software-guide/wifi/)할 수 있어야 합니다.
* 필요에 따라 Raspberry Pi용 [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) 추가 항목 보드를 사용할 수도 있습니다. 이 보드는 다양한 센서에서 원격 분석 데이터를 수집하여 Azure IoT Central 응용 프로그램에 보냅니다. **Sense Hat** 보드가 없는 경우 에뮬레이터를 대신 사용해도 됩니다.

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **Raspberry Pi** 장치가 포함됩니다.

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

### <a name="add-a-real-device"></a>실제 장치 추가

Azure IoT Central 응용 프로그램에서 **Raspberry Pi** 장치 템플릿으로 실제 장치를 추가하고 장치 연결 문자열을 기록해 둡니다. 자세한 내용은 [Azure IoT Central 응용 프로그램에 실제 장치 추가](tutorial-add-device.md)를 참조하세요.

## <a name="configure-the-raspberry-pi"></a>Raspberry Pi 구성

다음 단계에서는 GitHub에서 샘플 Python 응용 프로그램을 다운로드하고 구성하는 방법을 설명합니다. 이 응용 프로그램 예제는 다음과 같은 일을 합니다.

* Azure IoT Central에 원격 분석 데이터 및 속성 값을 보냅니다.
* Azure IoT Central에서 수행된 설정 변경에 응답합니다.

> [!NOTE]
> Raspberry Pi Python 샘플에 대한 자세한 내용은 GitHub의 [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) 파일을 참조하세요.

1. Raspberry Pi 데스크톱에서 웹 브라우저를 사용하여 [Azure IoT Central 펌웨어 릴리스](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) 페이지로 이동합니다.

1. 최신 펌웨어가 들어 있는 Zip 파일을 Raspberry Pi의 홈 폴더에 다운로드합니다. 파일 이름은 `RaspberryPi-IoTCentral-X.X.X.zip` 형식입니다.

1. 펌웨어 파일의 압축을 풀려면 Raspberry Pi 데스크톱에서 **파일 관리자**를 사용합니다. Zip 파일을 마우스 오른쪽 단추로 클릭하고 **여기에 압축 풀기**를 선택합니다. 이 작업은 홈 폴더에 `RaspberryPi-IoTCentral-X.X.X` 폴더를 만듭니다.

1. Raspberry Pi에 연결된 **Sense Hat** 보드가 없는 경우 에뮬레이터를 사용해야 합니다.
    1. **파일 관리자**의 `RaspberryPi-IoTCentral-X.X.X` 폴더에서 **config.iot** 파일을 마우스 오른쪽 단추로 클릭하고 **텍스트 편집기**를 선택합니다.
    1. `"simulateSenseHat": false,` 줄을 `"simulateSenseHat": true,`로 변경합니다.
    1. 변경 내용을 저장하고 **텍스트 편집기**를 닫습니다.

1. **터미널** 세션을 시작하고 `cd` 명령을 사용하여 이전 단계에서 만든 폴더로 이동합니다.

1. 응용 프로그램 예제를 실행하려면 **터미널** 창에 `./start.sh`를 입력합니다. **Sense HAT 에뮬레이터**를 사용하는 경우 GUI가 표시됩니다. GUI를 사용하여 Azure IoT Central 응용 프로그램으로 전송되는 원격 분석 값을 변경할 수 있습니다.

1. **터미널** 창에는 `Device information being served at http://192.168.0.60:8080` 메시지와 비슷한 메시지가 표시됩니다. URL은 환경에 따라 달라질 수 있습니다. URL을 복사하고 웹 브라우저를 사용하여 구성 페이지로 이동합니다.

    ![장치 구성](media/howto-connect-raspberry-pi-python/configure.png)

1. Azure IoT Central 응용 프로그램에 실제 장치를 추가할 때 기록해 둔 장치 연결 문자열을 입력합니다. **장치 구성**을 선택합니다. **장치가 구성되었습니다. 잠시 후 장치에서 Azure IoT Central로 데이터를 보내기 시작할 것입니다** 메시지가 표시됩니다.

1. Azure IoT Central 응용 프로그램에서, Raspberry Pi에서 실행되는 코드가 응용 프로그램과 상호 작용하는 방식을 살펴봅니다.

    * 실제 장치의 **측정값** 페이지에서, Raspberry Pi가 보낸 원격 분석 데이터를 볼 수 있습니다. **Sense HAT 에뮬레이터**를 사용하는 경우 Raspberry Pi의 GUI에서 원격 분석 값을 수정할 수 있습니다.
    * **속성** 페이지에서, 보고된 **다이 번호** 속성 값을 볼 수 있습니다.
    * **설정** 페이지에서, 전압, 팬 속도 등 Raspberry Pi의 다양한 설정을 변경할 수 있습니다. Raspberry Pi가 변경 내용을 확인하면 Azure IoT Central에서 설정이 **동기화됨**으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

* [Azure IoT Central에 일반 Node.js 클라이언트 응용 프로그램 연결](howto-connect-nodejs.md)