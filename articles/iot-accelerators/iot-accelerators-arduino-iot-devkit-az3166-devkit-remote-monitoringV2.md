---
title: 'IoT DevKit에서 클라우드로: Azure IoT Suite Remote Monitoring v2에 IoT DevKit AZ3166 연결 | Microsoft Docs'
description: 이 자습서에서는 모니터링 및 시각화를 위해 IoT DevKit AZ3166의 센서 상태를 Azure IoT Suite에서 Remote Monitoring v2로 보내는 방법을 알아봅니다.
services: iot-hub
documentationcenter: ''
author: isabelcabezasm
manager: ''
tags: ''
keywords: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: 667e51acd5ac1367e185fb9bf9a7949a13e061af
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="connect-mxchip-iot-devkit-az3166-to-azure-iot-suite-for-remote-monitoring-v2"></a>원격 모니터링을 위해 MXChip IoT DevKit AZ3166을 원격 모니터링 v2에 대해 Azure IoT Suite에 연결


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서에서는 DevKit에서 샘플 앱을 실행하여 Azure IoT Suite에 센서 데이터를 보내는 방법을 알아봅니다.

[MXChip IoT DevKit](https://aka.ms/iot-devkit)은 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. [Arduino용 Visual Studio Code 확장](https://aka.ms/arduino)을 사용하여 개발할 수 있습니다. 그리고 Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션의 프로토타입을 안내하기 위해 확장 중인 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)가 제공됩니다.

## <a name="what-you-need"></a>필요한 항목

[시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 완료하여:

* DevKit을 Wi-Fi에 연결
* 개발 환경 준비


## <a name="open-the-remotemonitoring-sample"></a>RemoteMonitoring 샘플 열기

1. DevKit가 컴퓨터에 연결되어 있으면 연결을 끊습니다.

2. VS Code를 시작합니다.

3. DevKit를 컴퓨터에 연결합니다. VS Code가 DevKit를 자동으로 감지하고 다음 페이지를 엽니다.
  * DevKit 소개 페이지
  * Arduino 예제: DevKit를 시작하기 위한 실습 예제

4. 왼쪽의 **ARDUINO EXAMPLES** 섹션을 확장하고 **MXCHIP AZ3166에 대한 예제 > AzureIoT**로 이동하여 **RemoteMonitoringv2**를 선택합니다. 프로젝트 폴더가 있는 새 VS Code 창이 열립니다.

  ![원격 모니터링 프로젝트 열기](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > 창을 닫은 경우 다시 열면 됩니다. `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음 **Arduino: 예제**를 찾아서 선택합니다.

## <a name="add-a-new-physical-device"></a>새로운 물리적 장치 추가

포털에서 **Devices** 섹션으로 이동한 다음, 그곳에서 **+New Device** 버튼을 클릭합니다. 

![새 장치 추가](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

*새 장치 양식*을 작성해야 합니다.
1. *Device 유형* 섹션에서 **Physical**을 클릭합니다.
2. 고유한 Device ID(예를 들어 *MXChip* 또는 *AZ3166*)를 정의합니다.
3. *Authentication 키* 섹션에서 **자동 생성 키**를 선택합니다.
4. *적용* 단추를 클릭합니다.

![새 장치 양식 추가](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

포털이 새 장치의 프로비저닝을 완료할 때까지 기다립니다.

![새 장치 프로비저닝 ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


그런 다음, 새 장치의 구성이 다음과 같이 표시됩니다.
생성된 **연결 문자열**을 복사합니다.

![장치 연결 문자열](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


이 연결 문자열은 다음 섹션에서 사용됩니다.





## <a name="build-and-upload-the-device-code"></a>장치 코드 빌드 및 업로드

Visual Studio Code로 돌아가기: 

1. `Ctrl+P`(macOS: `Cmd + P`)를 사용하고 **task config-device-connection**을 입력합니다.

  ![Azure Subscription 및 IoT Hub 선택](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. 터미널은 IoT 장치의 연결 문자열을 사용하고자 하는지 여부를 묻습니다. *새로 만들기*를 선택하고 이제 붙여 넣습니다.

  ![연결 문자열 붙여 넣기](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. 때때로 터미널에서 구성 모드를 입력하라는 메시지를 표시합니다. 이렇게 하려면 단추 A를 누른 채 재설정 단추를 눌렀다가 놓은 다음, 단추 A를 놓습니다. 화면에 DevKit ID와 '구성'이 표시됩니다.

  ![Device DevKit 화면](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > 이 자습서의 마지막 부분을 따랐다면 연결 문자열이 클립 보드에 저장되어야 합니다. 그렇지 않다면 Azure Portal로 가서 Remote Monitoring 리소스 그룹의 IoT Hub를 찾아야 합니다. 여기서 IoT Hub 연결 장치를 보고 장치 연결 문자열을 복사할 수 있습니다.

  ![연결 문자열 찾기](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


이제 VS Code 섹션 "Azure IoT Hub Devices"에서 새 물리적 장치를 볼 수 있습니다.

![새 IoT Hub Device 알림](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>프로젝트 테스트

샘플 앱을 실행하면 DevKit에서 Wi-Fi를 통해 센서 데이터를 Azure IoT Suite로 보냅니다. 결과를 보려면 다음 단계를 수행합니다.

1. Azure IoT Suite로 이동하여 **대시보드**를 클릭합니다.

2. Azure IoT Suite 솔루션 콘솔에 DevKit 센서 상태가 표시됩니다. 

![Azure IoT Suite의 센서 데이터](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

센서 이름(AZ3166)을 클릭하면 대시 보드 오른쪽에 탭이 열리고, 실시간으로 MX Chip 센서 차트를 볼 수 있습니다.


## <a name="send-a-c2d-message"></a>C2D 메시지 보내기
Remote Monitoring v2를 사용하면 장치에서 원격 메소드를 호출할 수 있습니다.
MX Chip 예제 코드는 센서가 선택될 때 Method 섹션에서 볼 수 있는 세 가지 메서드를 게시합니다.

![Methods MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

"LedColor" 메서드를 사용하여 MX Chip led 중 하나의 색상을 변경할 수 있습니다. 이를 수행하려면 장치의 확인란을 선택하고 스케줄 버튼을 클릭합니다. 

![Methods MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

모든 메소드가 나타나는 드롭 다운에서 ChangeColor라는 메서드를 선택하고 이름을 쓰고 적용을 누릅니다.

![Dropdown MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

몇 초 후에 물리적 MX Chip이 RGB led(A 단추 아래)의 색상을 변경해야 합니다.

![Led MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>장치 ID 변경

[이 가이드](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)에 따라 IoT Hub의 장치 ID를 변경할 수 있습니다.


## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 채널을 통해 문의해주세요.

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

DevKit 장치를 Azure IoT Suite에 연결하고 센서 데이터를 시각화하는 방법을 알아보았습니다. 다음 제안 단계는 다음과 같습니다.

* [Azure IoT Suite 개요](https://docs.microsoft.com/azure/iot-suite/)
* [Microsoft IoT Central 응용 프로그램에 MXChip IoT DevKit 장치 연결](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
