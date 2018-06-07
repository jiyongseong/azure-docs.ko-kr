---
title: 'M0에서 클라우드로: Feather M0 WiFi를 Azure IoT Hub에 연결 | Microsoft Docs'
description: 이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내기 위해 Adafruit Feather M0 WiFi를 설정하고 Azure IoT Hub에 연결하는 방법을 알아봅니다.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2a6a65a3c4a69a49788ce9799ceed53d53edcd77
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather M0 WiFi를 클라우드의 Azure IoT Hub에 연결
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![BME280, Feather M0 WiFi 및 IoT Hub 간의 연결](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

이 자습서에서는 Arduino 보드를 실행하는 작업의 기초부터 학습합니다. 그런 다음 [Azure IoT Hub](iot-hub-what-is-iot-hub.md)를 사용하여 장치를 클라우드에 원활하게 연결하는 방법을 알아봅니다.

## <a name="what-you-do"></a>수행할 작업

사용자가 만든 IoT Hub에 Adafruit Feather M0 WiFi를 연결합니다. 그런 다음 M0 WiFi에서 샘플 응용 프로그램을 실행하여 BME280에서 온도 및 습도 데이터를 수집합니다. 마지막으로 센서 데이터를 IoT Hub로 보냅니다.


## <a name="what-you-learn"></a>학습 내용

* IoT Hub를 만들고 Feather M0 WiFi용 장치를 등록하는 방법
* Feather M0 WiFi를 센서와 컴퓨터에 연결하는 방법
* Feather M0 WiFi에서 샘플 응용 프로그램을 실행하여 센서 데이터를 수집하는 방법
* IoT Hub로 센서 데이터를 보내는 방법

## <a name="what-you-need"></a>필요한 항목

![이 자습서에 필요한 부품](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

이 작업을 완료하려면 Feather M0 WiFi 시작 키트에서 다음 부품이 필요합니다.

* Feather M0 WiFi 보드
* Micro USB - A형 USB 케이블

또한 개발 환경에는 다음 사항도 필요합니다.

* 활성 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
* Windows 또는 Ubuntu를 실행하는 Mac 또는 PC
* Feather M0 WiFi를 연결할 무선 네트워크
* 구성 도구를 다운로드하기 위한 인터넷 연결
* [Arduino IDE](https://www.arduino.cc/en/main/software) 버전 1.6.8 이상 이전 버전은 Azure IoT Hub 라이브러리와 작동하지 않습니다.

센서가 없는 경우 다음 항목은 선택 사항입니다. 시뮬레이트된 센서 데이터를 사용할 수도 있습니다.

* BME280 온도 및 습도 센서
* 실험용 회로판
* M/M 점퍼 와이어

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Feather M0 WiFi를 센서와 컴퓨터에 연결
이 섹션에서는 센서를 보드에 연결합니다. 그런 다음 장치를 나중에 사용할 컴퓨터에 연결합니다.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Feather M0 WiFi에 DHT22 온도 및 습도 센서 연결

브레드보드와 점퍼 와이어를 사용하여 연결합니다. 센서가 없는 경우 시뮬레이션된 센서 데이터를 사용할 수 있으므로 이 섹션을 건너뜁니다.

![연결 참조](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


센서 핀의 경우 다음 배선을 사용합니다.


| 시작(센서)           | 끝(보드)            | 케이블 색   |
| -----------------------  | ---------------------- | ------------: |
| VDD(27A 핀)            | 3V(3A 핀)            | 빨간색 케이블     |
| GND(29A 핀)            | GND(6A 핀)           | 검은색 케이블   |
| SCK(30A 핀)            | SCK(12A 핀)          | 노란색 케이블  |
| SDO(31A 핀)            | MI(14A 핀)           | 흰색 케이블   |
| SDI(32A 핀)            | M0(13A 핀)           | 파란색 케이블    |
| CS(33A 핀)             | GPIO 5(15J 핀)       | 주황색 케이블  |

자세한 내용은 [Adafruit BME280 Humidity + Barometric Pressure + Temperature Sensor Breakout](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all)(Adafruit BME280 습도 + 기압 + 온도 센서 혁신 세션) 및 [Adafruit Feather M0 WiFi pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts)(Adafruit Feather M0 WiFi 핀 배열)를 참조하세요.



이제 Feather M0 WiFi를 작동 센서와 연결해야 합니다.

![DHT22를 Feather Huzzah와 연결](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Feather M0 WiFi를 컴퓨터에 연결

다음과 같이 Micro USB - A형 USB 케이블을 사용하여 Feather M0 WiFi를 컴퓨터에 연결합니다.

![Feather Huzzah를 컴퓨터에 연결](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>직렬 포트 권한 추가(Ubuntu만 해당)

Ubuntu를 사용하는 경우 Feather M0 WiFi의 USB 포트에서 작동할 권한이 있는지 확인합니다. 직렬 포트 권한을 추가하려면 다음 단계를 수행합니다.


1. 터미널에서 다음 명령을 실행합니다.

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   다음 출력 중 하나가 표시됩니다.

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   출력에서 `uucp` 또는 `dialout`이 USB 포트의 그룹 소유자 이름인지 확인합니다.

2. 그룹에 사용자를 추가하려면 다음 명령을 실행합니다.

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   이전 단계에서 그룹 소유자 이름 `<group-owner-name>`을 얻었습니다. Ubuntu 사용자 이름은 `<username>`입니다.

3. 변경 내용이 표시되도록 Ubuntu에서 로그아웃한 다음 다시 로그인합니다.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>센서 데이터를 수집하여 IoT Hub에 보내기

이 섹션에서는 Feather M0 WiFi에서 샘플 응용 프로그램을 배포하고 실행합니다. 응용 프로그램 예제는 Feather M0 WiFi에서 LED가 깜박이게 합니다. 그런 다음 BME280 센서에서 수집된 온도 및 습도 데이터를 IoT Hub에 보냅니다.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>GitHub에서 응용 프로그램 예제를 가져오고 Arduino IDE 준비

샘플 응용 프로그램은 GitHub에서 호스팅됩니다. GitHub에서 응용 프로그램 예제가 포함된 샘플 리포지토리를 복제합니다. 샘플 리포지토리를 복제하려면 다음 단계를 수행합니다.

1. 명령 프롬프트 또는 터미널 창을 엽니다.

2. 응용 프로그램 예제를 저장하려는 폴더로 이동합니다.
3. 다음 명령 실행:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Arduino IDE에 Feather M0 WiFi 패키지 설치

1. 응용 프로그램 예제가 저장된 폴더를 엽니다.

2. Arduino IDE의 앱 폴더에서 app.ino 파일을 엽니다.

   ![Arduino IDE에서 샘플 응용 프로그램 열기](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. **파일** > **기본 설정**(Windows/Linux) 또는 **Arduino** > **환경설정**(Mac)을 클릭하고 아래 링크를 복사하여 Arduino IDE 기본 설정의 **Additional Boards Manager URLs**(추가 보드 관리자 URL) 옵션에 붙여넣습니다.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. **도구** > **보드** > **보드 관리자**를 클릭한 다음 `Arduino SAMD Boards` 버전 `1.6.2` 이상을 설치합니다. 

1. 그런 다음 같은 창에서 `Adafruit SAMD Boards` 패키지를 설치하여 보드 파일 정의를 추가합니다.

   ![esp8266 패키지가 설치됨](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. **도구** > **보드** > **Adafruit M0 WiFi**를 클릭합니다.

5. 드라이버를 설치합니다(Windows만 해당). Feather M0 WiFi를 연결하면 드라이버를 설치해야 할 수 있습니다. [웹 페이지의 다운로드 링크](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe)를 클릭하여 드라이버 설치 관리자를 다운로드합니다. 단계에 따라 원하는 드라이버를 설치합니다.

### <a name="install-necessary-libraries"></a>필요한 라이브러리 설치

1. Arduino IDE에서 **스케치** > **라이브러리 포함** > **라이브러리 관리**를 클릭합니다.

2. 다음 라이브러리 이름을 하나씩 검색합니다. 검색한 각 라이브러리에 대해 **설치**를 클릭합니다.

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. 수동으로 `Adafruit_WINC1500`을 설치합니다. [이 웹 사이트](https://github.com/adafruit/Adafruit_WINC1500)로 이동하고 **Clone or download**(복제 또는 다운로드) > **Download ZIP**(ZIP 다운로드)을 클릭합니다. 그런 다음 Arduino IDE에서 **Sketch**(스케치) > **Include Library**(라이브러리 포함) > **Add .zip Library**(.zip 라이브러리 추가)로 이동하고 zip 파일을 추가합니다.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>실제 BME280 센서가 없는 경우 응용 프로그램 예제 사용

실제 BME280 센서가 없는 경우 응용 프로그램 예제에서 온도 및 습도 데이터를 시뮬레이트할 수 있습니다. 시뮬레이션된 데이터를 사용하도록 샘플 응용 프로그램을 설정하려면 다음 단계를 수행합니다.

1. `app` 폴더에서 `config.h` 파일을 엽니다.

2. 다음 코드 줄을 찾아 값을 `false`에서 `true`로 변경합니다.

   ```c
   define SIMULATED_DATA true
   ```
   ![시뮬레이션된 데이터를 사용하도록 샘플 응용 프로그램 구성](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. `Control-s`로 파일을 저장합니다.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Feather M0 WiFi에 샘플 응용 프로그램 배포

1. Arduino IDE에서 **도구** > **포트**를 클릭한 후 Feather M0 WiFi에 대한 직렬 포트를 클릭합니다.

2. **스케치** > **업로드**를 클릭하여 샘플 응용 프로그램을 빌드하고 Feather M0 WiFi에 배포합니다.

### <a name="enter-your-credentials"></a>자격 증명 입력

업로드를 성공적으로 완료했으면 다음 단계에 따라 자격 증명을 입력합니다.

1. Arduino IDE에서 **도구** > **직렬 모니터**를 클릭합니다.

2. 직렬 모니터 창의 오른쪽 아래 모서리에서 왼쪽에 있는 드롭다운 목록에서 **No line ending**(줄 끝 없음)을 선택합니다.
3. 오른쪽에 있는 드롭다운 목록에서 **115200 baud**(115200보드)를 선택합니다.
4. 위쪽에 있는 입력 상자에 다음 정보를 입력하고(제공하라는 메시지가 표시되는 경우) **Send**(보내기)를 클릭합니다.

   * Wi-Fi SSID
   * Wi-Fi 암호
   * 장치 연결 문자열

> [!Note]
> 자격 증명 정보는 Feather M0 WiFi의 EEPROM에 저장됩니다. Feather M0 WiFi 보드의 리셋 단추를 클릭하면 샘플 응용 프로그램에서 정보를 지울 것인지 묻는 메시지를 표시합니다. `Y`를 입력하여 정보를 지웁니다. 정보를 다시 제공하라는 메시지가 표시됩니다.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>응용 프로그램 예제가 실행되고 있는지 확인

직렬 모니터 창에 다음 출력이 표시되고 Feather M0 WiFi의 LED가 깜박이면 응용 프로그램 예제가 실행되고 있는 것입니다.

![Arduino IDE의 최종 출력](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>다음 단계

IoT Hub에 Feather M0 WiFi를 연결하고 캡처된 센서 데이터를 IoT Hub로 보냈습니다. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

