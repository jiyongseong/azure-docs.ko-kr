---
title: 'IoT DevKit에서 클라우드로: Azure IoT Hub에 IoT DevKit AZ3166 연결 | Microsoft Docs'
description: 이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내도록 DevKit AZ3166을 설정하고 Azure IoT Hub에 연결하는 방법을 알아봅니다.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 3d83875171c7c097bac5a70852f049a6ce4219b3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>클라우드에서 Azure IoT Hub에 IoT DevKit AZ3166 연결

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션을 개발하고 프로토타입하는 데 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)를 사용할 수 있습니다. 풍부한 주변 장치 및 센서가 있는 Arduino 호환 보드, 오픈 소스 보드 패키지 및 증가하는 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)를 포함합니다.

## <a name="what-you-do"></a>수행할 작업
사용자가 만드는 Azure IoT Hub에 [DevKit](https://microsoft.github.io/azure-iot-developer-kit/)을 연결하고 센서에서 온도 및 습도 데이터를 수집하고 데이터를 IoT Hub로 보냅니다.

DevKit가 아직 없으세요? [DevKit 시뮬레이터](https://azure-samples.github.io/iot-devkit-web-simulator/)를 시도해보거나 [구매하세요](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>학습 내용

* IoT DevKit를 무선 액세스 지점에 연결하고 개발 환경을 준비하는 방법
* IoT Hub를 만들고 MXChip IoT DevKit용 장치를 등록하는 방법
* MXChip IoT DevKit에서 샘플 응용 프로그램을 실행하여 센서 데이터를 수집하는 방법
* IoT Hub로 센서 데이터를 보내는 방법

## <a name="what-you-need"></a>필요한 항목

* 마이크로 USB 케이블을 사용하는 MXChip IoT DevKit 보드 [지금 사용해 보세요](https://aka.ms/iot-devkit-purchase).
* Windows 10 또는 macOS 10.10+을 실행하는 컴퓨터
* 활성 Azure 구독. [30일 평가판 Microsoft Azure 계정](https://azureinfo.microsoft.com/us-freetrial.html) 활성화
  

## <a name="prepare-your-hardware"></a>하드웨어 준비

컴퓨터에 하드웨어를 연결합니다.

이 하드웨어가 필요합니다.

* DevKit 보드
* 마이크로 USB 케이블

![필수 하드웨어](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit를 컴퓨터에 연결하려면:

1. USB 끝을 컴퓨터에 연결합니다.
2. 마이크로 USB 끝을 DevKit에 연결합니다.
3. 전원의 녹색 LED는 연결을 확인합니다.

![하드웨어 연결](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Wi-Fi 구성

IoT 프로젝트는 인터넷 연결을 사용합니다. 다음 지침을 사용하여 DevKit를 Wi-Fi에 연결하도록 구성합니다.

### <a name="enter-ap-mode"></a>AP 모드로 전환

단추 B를 길게 누른 다음 다시 설정 단추를 눌러서 놓고 단추 B를 놓습니다. DevKit은 Wi-Fi 구성을 위해 AP 모드로 전환합니다. 화면은 DevKit의 SSID(서비스 설정 식별자) 및 구성 포털 IP 주소를 표시합니다.

![단추, 단추 B 및 SSID 다시 설정](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>DevKit AP에 연결

이제 다른 Wi-Fi 사용 장치(컴퓨터 또는 휴대폰)를 사용하여 DevKit SSID(위의 이미지에 강조 표시됨)에 연결합니다. 암호를 비워 둡니다.

![네트워크 정보 및 연결 단추](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>DevKit에 Wi-Fi 구성

컴퓨터 또는 모바일 폰 브라우저에서 DevKit 화면에 표시된 IP 주소를 열고, DevKit에서 연결하려는 Wi-Fi 네트워크를 선택한 다음 암호를 입력합니다. **연결**을 선택합니다.

![암호 상자 및 연결 단추](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

연결에 성공하는 경우 DevKit은 몇 초 내에 다시 부팅됩니다. 그러면 화면에 Wi-Fi 이름 및 IP 주소가 표시됩니다.

![Wi-Fi 이름 및 IP 주소](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> 사진에 표시된 IP 주소는 DevKit 화면에 할당되고 표시되는 실제 IP 주소와 일치하지 않을 수 있습니다. Wi-Fi가 DHCP를 사용하여 동적으로 IP를 할당하므로 이는 일반적입니다.

Wi-Fi를 구성한 후 장치가 분리되는 경우에도 자격 증명은 해당 연결에 대해 장치에서 유지됩니다. 예를 들어 집에서 Wi-Fi에 대해 DevKit를 구성한 다음 DevKit를 사무실에 가져간 경우 AP 모드를 다시 구성하여("AP 모드로 전환" 섹션의 단계에서 시작) DevKit를 사무실 Wi-Fi에 연결해야 합니다. 

## <a name="start-using-the-devkit"></a>DevKit를 사용하여 시작

DevKit에서 실행 중인 기본 앱은 최신 버전의 펌웨어를 확인하고 일부 센서 진단 데이터를 표시합니다.

### <a name="upgrade-to-the-latest-firmware"></a>최신 펌웨어로 업그레이드

> [!NOTE] 
> v1.1부터 DevKit은 부팅 로더에서 ST-SAFE를 활성화합니다. v1.1에서 실행 중인 경우 제대로 작동하려면 펌웨어를 업그레이드해야 합니다.

펌웨어를 업그레이드해야 하는 경우 화면에는 최신 펌웨어 버전이 표시됩니다. 업그레이드하려면 [펌웨어 업그레이드](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) 가이드를 따릅니다.

![최신 펌웨어 버전 표시](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> 이는 일회성 작업입니다. DevKit 개발을 시작한 후에 앱을 업로드하면 앱과 함께 최신 펌웨어가 제공됩니다.

### <a name="test-various-sensors"></a>다양한 센서 테스트

센서를 테스트하려면 단추 B를 누릅니다. 단추 B를 계속해서 눌렀다 놓아 각 센서를 순환합니다.

![단추 B 및 센서 표시](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이제 IoT 응용 프로그램을 빌드하도록 개발 환경(도구 및 패키지)을 설정할 시간입니다. 운영 체제에 따라 Windows 또는 macOS 버전을 선택할 수 있습니다.

### <a name="windows"></a>Windows

설치 패키지를 사용하여 개발 환경을 준비하는 것이 좋습니다. 문제가 발생하는 경우 [수동 단계](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)를 따라 수행할 수 있습니다.

#### <a name="download-the-latest-package"></a>최신 패키지 다운로드

다운로드한 .zip 파일은 DevKit 개발에 필요한 모든 필요한 도구 및 패키지를 포함합니다.

> [!div class="button"]
[다운로드](https://aka.ms/devkit/prod/installpackage/latest)

.zip 파일은 다음 도구 및 패키지를 포함합니다. 이미 일부 구성 요소를 설치한 경우 스크립트에서 이를 검색하고 건너뜁니다.

* Node.js 및 Yarn: 설치 스크립트 및 자동화된 작업에 대한 런타임
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): Azure 리소스를 관리하기 위한 플랫폼 간 명령줄 환경입니다. MSI는 종속 Python 및 PIP를 포함합니다.
* VS Code([Visual Studio Code](https://code.visualstudio.com/)): DevKit 개발을 위한 간단한 코드 편집기입니다.
* [Arduino용 Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): Visual Studio Code에서 Arduino 개발을 사용하는 확장입니다.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): Arduino에 대한 확장을 사용하는 도구입니다.
* DevKit 보드 패키지: DevKit에 대한 도구 체인, 라이브러리 및 프로젝트입니다.
* ST 링크 유틸리티: 필수 도구 및 드라이버입니다.

#### <a name="run-the-installation-script"></a>설치 스크립트 실행

Windows 파일 탐색기에서 .zip 파일을 찾고 압축을 풉니다. `install.cmd`를 찾아 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

![파일 탐색기](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

설치하는 동안 각 도구 또는 패키지의 진행 상태를 볼 수 있습니다.

![설치 진행률](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> 환경에 따라 Arduino IDE를 설치할 때 오류가 발생하는 경우가 있습니다. 이런 경우 [Arduino IDE를 개별적으로 설치](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows)하고 install.cmd를 다시 실행할 수 있습니다. 아니면 [수동 단계](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows)에 따라 필요한 모든 도구와 패키지를 설치하십시오.

#### <a name="install-drivers"></a>드라이버 설치

Arduino 확장에 대한 VS Code는 Arduino IDE에 의존합니다. Arduino IDE를 처음 설치하는 경우 관련 드라이버를 설치하라는 메시지가 표시됩니다.

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

인터넷 속도에 따라 설치에는 약 10분이 걸립니다. 설치가 완료되면 바탕 화면에 Visual Studio Code 및 Arduino IDE 바로 가기가 표시됩니다.

> [!NOTE] 
> 경우에 따라서 VS Code를 시작할 때 Arduino IDE 또는 관련된 보드 패키지를 찾을 수 없다는 오류와 함께 메시지가 표시됩니다. 해결하려면 VS Code를 닫고 Arduino IDE를 다시 시작합니다. VS Code는 Arduino IDE 경로를 제대로 찾아야 합니다.

### <a name="macos"></a>macOS

One-Click 설치 환경을 사용하여 개발 환경을 준비하는 것이 좋습니다. 문제가 발생하는 경우 [수동 단계](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)를 따라 수행할 수 있습니다.

#### <a name="install-homebrew"></a>Homebrew 설치

> [!NOTE] 
> Homebrew를 설치한 경우 이 단계를 건너뛸 수 있습니다.

[Homebrew 설치 지침](https://docs.brew.sh/Installation.html)에 따라 설치합니다.

#### <a name="download-the-latest-package"></a>최신 패키지 다운로드
다운로드한 .zip 파일은 DevKit 개발에 필요한 모든 필요한 도구 및 패키지를 포함합니다.

> [!div class="button"]
[다운로드](https://aka.ms/devkit/prod/installpackage/mac/latest)

.zip 파일은 다음 도구 및 패키지를 포함합니다. 이미 일부 구성 요소를 설치한 경우 스크립트에서 이를 검색하고 건너뜁니다.

* Node.js 및 Yarn: 설치 스크립트 및 자동화된 작업에 대한 런타임
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): Azure 리소스를 관리하기 위한 플랫폼 간 명령줄 환경입니다.
* VS Code([Visual Studio Code](https://code.visualstudio.com/)): DevKit 개발을 위한 간단한 코드 편집기입니다.
* [Arduino용 Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): Visual Studio Code에서 Arduino 개발을 사용하는 확장입니다.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): Arduino에 대한 확장을 사용하는 도구입니다.
* DevKit 보드 패키지: DevKit에 대한 도구 체인, 라이브러리 및 프로젝트입니다.
* ST 링크 유틸리티: 필수 도구 및 드라이버입니다.

#### <a name="run-the-installation-script"></a>설치 스크립트 실행

파인더에서 .zip을 찾아서 압축을 풉니다.

![macOS 찾기](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

터미널 앱을 실행하고 zip 파일을 추출한 폴더를 찾아 실행합니다.

```bash
./install.sh
```

![macOS 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Homebrew 권한 오류가 발생하면 `brew doctor`를 실행하여 해결합니다. 자세한 내용은 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos)를 참조하세요.

이제 macOS에 대해 필요한 모든 도구 및 패키지를 설치했습니다.


## <a name="open-the-project-folder"></a>프로젝트 폴더 열기

### <a name="start-vs-code"></a>VS Code 시작

DevKit이 연결되어 있지 않은지 확인합니다. VS Code를 먼저 시작하고 DevKit를 컴퓨터에 연결합니다. VS Code는 자동으로 DevKit를 찾아서 소개 페이지를 엽니다.

![소개 페이지](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> 경우에 따라서 VS Code를 시작할 때 Arduino IDE 또는 관련된 보드 패키지를 찾을 수 없다는 오류와 함께 메시지가 표시됩니다. VS Code를 닫고 Arduino IDE를 다시 시작합니다. VS Code는 Arduino IDE 경로를 제대로 찾아야 합니다.


### <a name="open-the-arduino-examples-folder"></a>Arduino 예제 폴더 열기

**Arduino 예제** 탭에서 **MXCHIP AZ3166에 대한 예제** > **AzureIoT**로 이동하고 **GetStarted**를 선택합니다.

![Arduino 예제 탭](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

창을 닫은 경우 다시 열면 됩니다. `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음 **Arduino: 예제**를 찾아서 선택합니다.

## <a name="provision-azure-services"></a>Azure 서비스 프로비전

솔루션 창에서 `task cloud-provision`을 입력하여 `Ctrl+P`(macOS: `Cmd+P`)를 통해 해당 작업을 실행합니다.

VS Code 터미널에서 대화형 명령줄은 필요한 Azure 서비스를 프로비전하는 과정을 안내합니다.

![대화형 명령줄](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Arduino 스케치 빌드 및 업로드

### <a name="windows"></a>Windows

1. `Ctrl+P`를 사용하여 `task device-upload`를 실행합니다.
2. 터미널에서 구성 모드를 입력하라는 메시지를 표시합니다. 이렇게 하려면 단추 A를 누르고 있다가 다시 설정 단추를 밀어서 놓습니다. 화면에 DevKit ID와 '구성'이 표시됩니다.

이는 `task cloud-provision` 단계에서 검색하는 연결 문자열을 설정하기 위한 것입니다.

그러면 VSCode가 Arduino 스케치를 확인하고 업로드하기 시작합니다.

![Arduino 스케치 확인 및 업로드](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

> [!NOTE] 
> 경우에 따라 "오류: AZ3166: 알 수 없는 패키지" 오류가 발생합니다. 이는 보드 패키지 인덱스가 새로 고쳐지지 않기 때문입니다. 이 문제를 해결하려면 [FAQ 단계](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 확인하십시오.

### <a name="macos"></a>macOS

1. DevKit을 구성 모드로 전환: 단추 A를 누르고 있다가 다시 설정 단추를 밀어서 놓습니다. 화면에는 '구성'이 표시됩니다.
2. `Cmd+P`를 사용하여 `task device-upload`를 실행합니다.

이는 `task cloud-provision` 단계에서 검색하는 연결 문자열을 설정하기 위한 것입니다.

그러면 VSCode가 Arduino 스케치를 확인하고 업로드하기 시작합니다.

![device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

> [!NOTE] 
> 경우에 따라 "오류: AZ3166: 알 수 없는 패키지" 오류가 발생합니다. 이는 보드 패키지 인덱스가 새로 고쳐지지 않기 때문입니다. 이 문제를 해결하려면 [FAQ 단계](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 확인하십시오.


## <a name="test-the-project"></a>프로젝트 테스트

VS Code에서 다음 단계에 따라 직렬 모니터를 열고 설정합니다.

1. 상태 표시줄에서 `COM[X]` 단어를 클릭하여 오른쪽 COM 포트를 `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)로 설정합니다.

2. 상태 표시줄의 전원 플러그 아이콘을 클릭하여 직렬 모니터: ![serial-monitor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)를 엽니다.

3. 상태 표시줄에서 전송 속도를 나타내는 숫자를 클릭하고 `115200`: ![baud-rate](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)으로 설정합니다.

다음과 같은 결과가 표시되면 샘플 응용 프로그램은 성공적으로 실행됩니다.

* 직렬 모니터는 아래 스크린샷의 내용으로 동일한 정보를 표시합니다.
* MXChip IoT DevKit의 LED가 깜박입니다.

![VS Code의 최종 출력](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생할 경우 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 찾을 수 있습니다. 또한 이 페이지에서 의견을 남겨 피드백을 제공해 주실 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 MXChip IoT DevKit를 성공적으로 연결하고 캡처된 센서 데이터를 IoT Hub에 전송했습니다.

계속해서 Azure IoT Hub를 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

- [iothub-explorer를 사용하여 클라우드 장치 메시지 관리](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [IoT Hub 메시지를 Azure 데이터 저장소에 저장](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Web Apps를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Azure Machine Learning에서 IoT Hub의 센서 데이터를 사용한 날씨 예측](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [iothub-explorer를 사용하여 장치 관리](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Logic Apps를 사용하여 원격 모니터링 및 알림](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
