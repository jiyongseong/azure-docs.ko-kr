---
title: Intel Edison - 클라우드(Node.js) - Azure IoT Hub에 Intel Edison 연결 | Microsoft Docs
description: 이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내기 위해 Intel Edison을 설정하고 Intel Edison용 Azure IoT Hub에 연결하는 방법을 알아봅니다.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: azure iot intel edison, intel edison iot hub, intel edison에서 클라우드로 데이터 보내기, intel edison - 클라우드
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78dd623d021daa7f8cb0d52dd4e438aebe81e924
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>Azure IoT Hub에 Intel Edison 연결(Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

이 자습서에서는 Intel Edison을 실행하는 작업의 기초부터 학습합니다. 그런 다음 [Azure IoT Hub](iot-hub-what-is-iot-hub.md)를 사용하여 장치를 클라우드에 원활하게 연결하는 방법을 알아봅니다.

아직 키트가 없으세요? [여기](https://azure.microsoft.com/develop/iot/starter-kits)에서 시작하세요.

## <a name="what-you-do"></a>수행할 작업

* Intel Edison 및 Grove 모듈을 설정합니다.
* IoT Hub를 만듭니다.
* IoT Hub에 Edison용 장치를 등록합니다.
* Edison에서 샘플 응용 프로그램을 실행하여 IoT Hub로 센서 데이터를 보냅니다.

앞에서 만든 IoT Hub에 Intel Edison을 연결합니다. 그런 다음 Edison에서 샘플 응용 프로그램을 실행하여 Grove 온도 센서에서 온도 및 습도 데이터를 수집합니다. 마지막으로 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-learn"></a>학습 내용

* Azure IoT Hub를 만들고 새 장치 연결 문자열을 가져오는 방법.
* Edison을 Grove 온도 센서에 연결하는 방법
* Edison에서 샘플 응용 프로그램을 실행하여 센서 데이터를 수집하는 방법
* IoT Hub로 센서 데이터를 보내는 방법

## <a name="what-you-need"></a>필요한 항목

![필요한 항목](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* Intel Edison 보드
* Arduino 확장 보드
* 활성 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
* Windows 또는 Linux를 실행하는 Mac 또는 PC
* 인터넷 연결
* Micro B-A형 USB 케이블
* DC(직류) 전원 공급 장치 전원 공급 장치 정격은 다음과 같아야 합니다.
  - 7-15V DC
  - 1500mA 이상
  - 중앙/내부 핀은 전원 공급 장치의 양극이어야 합니다.

다음 항목은 선택 사항입니다.

* Grove Base Shield V2
* Grove - 온도 센서
* Grove 케이블
* 포장에 포함된 스페이서 바 또는 나사(모듈을 확장 보드에 고정하기 위한 2개의 나사와 4개의 나사 세트 및 플라스틱 스페이서 포함)

> [!NOTE] 
코드 샘플이 시뮬레이션된 센서 데이터를 지원하므로 이러한 항목은 선택 사항입니다.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Intel Edison 설정

### <a name="assemble-your-board"></a>보드 조립

이 섹션에는 Intel® Edison 모듈을 확장 보드를 연결하는 단계가 포함됩니다.

1. 확장 보드의 흰색 윤곽 안에 Intel® Edison 모듈을 놓고 모듈의 구멍과 확장 보드의 나사를 맞춥니다.

2. 딸깍 소리가 느껴질 때까지 `What will you make?` 아래에서 모듈을 누릅니다.

   ![보드 2 조립](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. 2개의 육각 너트(포장에 포함)를 사용하여 모듈을 확장 보드에 고정합니다.

   ![보드 3 조립](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. 확장 보드의 구석에 있는 4개 구멍 중 하나에 나사를 끼웁니다. 흰색 플라스틱 스페너 중 하나를 돌려 나사를 조입니다.

   ![보드 4 조립](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. 나머지 3개 구석의 스페이서에 대해 같은 작업을 반복합니다.

   ![보드 5 조립](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

이제 보드가 조립됩니다.

   ![조립된 보드](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Grove Base Shield 및 온도 센서 연결

1. 보드에 Grove Base Shield를 놓습니다. 모든 핀이 보드에 꽂혀 있는지 확인합니다.
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. Grove 케이블을 사용하여 Grove 온도 센서를 Grove Base Shield **A0** 포트에 연결합니다.

   ![온도 센서에 연결](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Edison 및 센서 연결](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

이제 센서가 준비되었습니다.

### <a name="power-up-edison"></a>Edison 전원 켜기

1. 전원 공급 장치를 꽂습니다.

   ![전원 공급 장치 꽂기](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. 녹색 LED(Arduino* 확장 보드에 DS1으로 레이블 지정)가 켜진 후 계속 켜져 있습니다.

3. 보드가 부팅을 마칠 때까지 1분 정도 대기합니다.

   > [!NOTE]
   > DC 전원 공급 장치가 없으면 USB 포트를 통해 여전히 보드의 전원을 켤 수 있습니다. 자세한 내용은 `Connect Edison to your computer` 섹션을 참조하세요. 이러한 방식으로 보드를 켜면 보드에서 예기치 않은 동작이 나타날 수 있습니다. 이러한 현상은 Wi-Fi 또는 구동 모터를 사용할 때 더욱 두드러집니다.

### <a name="connect-edison-to-your-computer"></a>Edison을 컴퓨터에 연결

1. Edison이 장치 모드가 되도록 2개의 마이크로 USB 포트 쪽으로 마이크로 스위치를 내립니다. 장치 모드와 호스트 모드 간 차이점에 대해서는 [여기](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode)를 참조하세요.

   ![마이크로 스위치 내리기](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. 마이크로 USB 포트를 위쪽 마이크로 USB 케이블에 꽂습니다.

   ![위쪽 마이크로 USB 포트](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. USB 케이블의 다른 쪽을 컴퓨터에 연결합니다.

   ![컴퓨터 USB](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. 컴퓨터에 새 장치를 탑재(컴퓨터에 SD 카드를 꽂는 것과 매우 유사함)할 때 보드가 완전히 초기화된다는 것을 알 수 있습니다.

## <a name="download-and-run-the-configuration-tool"></a>구성 도구 다운로드 및 실행
`Installers` 제목 아래 나열된 [이 링크](https://software.intel.com/en-us/iot/hardware/edison/downloads)에서 최신 구성 도구를 가져옵니다. 도구를 실행하고 화면 지시를 따른 다음 필요할 때 다음을 클릭합니다.

### <a name="flash-firmware"></a>펌웨어 플래시
1. `Set up options` 페이지에서 `Flash Firmware`를 클릭합니다.
2. 다음 중 하나를 수행하여 보드에서 플래시할 이미지를 선택합니다.
   - Intel에서 사용 가능한 최신 펌웨어 이미지를 다운로드한 후 이 이미지로 보드를 플래시하려면 `Download the latest image version xxxx`를 선택합니다.
   - 컴퓨터에 이미 저장한 이미지로 보드를 플래시하려면 `Select the local image`를 선택합니다. 보드에 플래시할 이미지를 찾아 선택합니다.
3. 설치 도구는 보드를 플래시하려고 합니다. 전체 플래시 프로세스는 10분 정도 걸릴 수 있습니다.

### <a name="set-password"></a>암호 설정
1. `Set up options` 페이지에서 `Enable Security`를 클릭합니다.
2. Intel® Edison 보드에 대한 사용자 지정 이름을 설정할 수 있습니다. 선택 사항입니다.
3. 보드에 대한 암호를 입력한 후 `Set password`를 클릭합니다.
4. 나중에 사용되는 암호에 표시합니다.

### <a name="connect-wi-fi"></a>Wi-Fi 연결
1. `Set up options` 페이지에서 `Connect Wi-Fi`를 클릭합니다. 컴퓨터가 사용할 수 있는 Wi-Fi 네트워크를 검색하므로 최대 1분 정도 대기합니다.
2. `Detected Networks` 드롭다운 목록에서 네트워크를 선택합니다.
3. `Security` 드롭다운 목록에서 네트워크의 보안 유형을 선택합니다.
4. 로그인 및 암호 정보를 입력한 다음 `Configure Wi-Fi`를 클릭합니다.
5. 나중에 사용되는 IP 주소에 표시합니다.

> [!NOTE]
> Edison이 컴퓨터와 같은 네트워크에 연결되어 있는지 확인합니다. 해당 IP 주소를 사용하여 컴퓨터가 Edison에 연결됩니다.

   ![온도 센서에 연결](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

축하합니다! Edison 구성을 마쳤습니다.

## <a name="run-a-sample-application-on-intel-edison"></a>Intel Edison에 샘플 응용 프로그램 실행

### <a name="prepare-the-azure-iot-device-sdk"></a>Azure IoT 장치 SDK 준비

1. 호스트 컴퓨터에서 다음 SSH 클라이언트 중 하나를 사용하여 Intel Edison에 연결합니다. IP 주소는 구성 도구에서 가져오며, 암호는 해당 도구에서 설정한 것입니다.
    - Windows용 [PuTTY](http://www.putty.org/)
    - Ubuntu 또는 macOS에 있는 기본 제공 SSH 클라이언트

2. 장치에 샘플 클라이언트 앱을 복제합니다. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. 그런 다음 repo 폴더로 이동하고 다음 명령을 실행하여 모든 패키지를 설치합니다. 완료하는 데 몇 분 정도 걸릴 수 있습니다.
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>샘플 응용 프로그램 구성 및 실행

1. 다음 명령을 실행하여 config 파일을 엽니다.

   ```bash
   nano config.json
   ```

   ![Config 파일](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   이 파일에는 사용자가 구성할 수 있는 두 개의 매크로가 있습니다. 첫 번째는 클라우드로 전송되는 두 메시지 사이의 간격을 정의하는 `INTERVAL`입니다. 두 번째는 시뮬레이션된 센서 데이터의 사용 여부에 대한 부울 값인 `simulatedData`입니다.

   **센서가 없는 경우** `simulatedData` 값을 `true`로 설정하여 샘플 응용 프로그램에서 시뮬레이션된 센서 데이터를 만들어서 사용하게 합니다.

1. Control-O > Enter > Control-X를 눌러 저장하고 종료합니다.


1. 다음 명령을 실행하여 샘플 응용 프로그램을 실행합니다.

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   장치 연결 문자열을 복사하여 작은따옴표 안에 붙여 넣습니다.

IoT Hub로 전송되는 센서 데이터와 메시지를 보여 주는 다음 출력이 표시됩니다.

![출력 - Intel Edison에서 IoT Hub로 전송된 센서 데이터](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>다음 단계

샘플 응용 프로그램을 실행하여 센서 데이터를 수집하고 IoT Hub로 전송했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
