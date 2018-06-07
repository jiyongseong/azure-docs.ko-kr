---
title: 시뮬레이션된 Raspberry Pi-클라우드(Node.js) - Azure IoT Hub에 Raspberry Pi 웹 시뮬레이터 연결 | Microsoft Docs
description: Raspberry Pi가 Azure 클라우드에 데이터를 보내도록 Raspberry Pi 웹 시뮬레이터를 Azure IoT Hub에 연결합니다.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timtl
tags: ''
keywords: raspberry pi 시뮬레이터, azure iot raspberry pi, raspberry pi iot hub, raspberry pi에서 클라우드로 데이터 전송, raspberry pi-클라우드
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: fe953b56e0a4dbbf7aaaa81d5b8ac6e31bb6bc4d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Azure IoT Hub에 Raspberry Pi 온라인 시뮬레이터 연결(Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

이 자습서에서는 Raspberry Pi 온라인 시뮬레이터 작업의 기초부터 학습합니다. 그런 다음 [Azure IoT Hub](iot-hub-what-is-iot-hub.md)를 사용하여 Pi 시뮬레이터를 클라우드에 원활하게 연결하는 방법을 알아봅니다. 

물리적 장치가 있는 경우 시작하려면 [Azure IoT Hub에 Raspberry Pi 연결](iot-hub-raspberry-pi-kit-node-get-started.md)을 방문하세요. 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>수행할 작업

* Raspberry Pi 온라인 시뮬레이터의 기본 사항을 알아봅니다.
* IoT Hub를 만듭니다.
* IoT Hub에 Pi용 장치를 등록합니다.
* Pi에서 샘플 응용 프로그램을 실행하여 IoT Hub로 시뮬레이션된 센서 데이터를 보냅니다.

앞에서 만든 IoT Hub에 시뮬레이션된 Raspberry Pi를 연결합니다. 그런 다음 시뮬레이터에서 샘플 응용 프로그램을 실행하여 센서 데이터를 생성합니다. 마지막으로 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-learn"></a>학습 내용

* Azure IoT Hub를 만들고 새 장치 연결 문자열을 가져오는 방법 Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
* Raspberry Pi 온라인 시뮬레이터를 사용하는 방법
* IoT Hub로 센서 데이터를 보내는 방법.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi 웹 시뮬레이터 개요

단추를 클릭하여 Raspberry Pi 온라인 시뮬레이터를 시작합니다.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Raspberry Pi 시뮬레이터 시작</a>

웹 시뮬레이터에는 세 가지 영역이 있습니다.
1. 어셈블리 영역 - 기본 회로에서는 Pi가 BME280 센서 및 LED에 연결됩니다. 미리 보기 버전에서는 이 영역이 잠겨 있으므로 지금은 사용자 지정을 수행할 수 없습니다.
2. 코딩 영역 - Raspberry Pi를 사용하여 코딩할 수 있는 온라인 코드 편집기입니다. 기본 샘플 응용 프로그램을 사용하여 BME280 센서에서 센서 데이터를 손쉽게 수집한 후 Azure IoT Hub로 보낼 수 있습니다. 응용 프로그램은 실제 Pi 장치와 완벽하게 호환됩니다. 
3. 통합 콘솔 창 - 코드의 출력을 표시합니다. 이 창의 맨 위에는 세 개의 단추가 있습니다.
   * **실행** - 코딩 영역에서 응용 프로그램을 실행합니다.
   * **다시 설정** - 기본 샘플 응용 프로그램으로 코딩 영역을 다시 설정합니다.
   * **접기/확장** - 오른쪽에 있는 단추를 사용하여 콘솔 창 접기/확장을 수행할 수 있습니다.

> [!NOTE] 
Raspberry Pi 웹 시뮬레이터는 현재 미리 보기 버전으로 제공됩니다. [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator)에서 사용자 의견을 듣고 싶습니다. 소스 코드는 [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator)에 공개되어 있습니다.

![Pi 온라인 시뮬레이터 개요](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Pi 웹 시뮬레이터에서 샘플 응용 프로그램 실행

1. 코딩 영역에서 기본 샘플 응용 프로그램으로 작업 중인지 확인합니다. 15행의 자리 표시자를 Azure IoT Hub 장치 연결 문자열로 대체합니다.
   ![장치 연결 문자열 바꾸기](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. **실행**을 클릭하거나 `npm start`를 입력하여 응용 프로그램을 실행합니다.


IoT Hub로 전송되는 센서 데이터와 메시지를 보여 주는 다음 출력이 표시됩니다. ![출력 - Raspberry Pi에서 IoT Hub로 전송된 센서 데이터](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>다음 단계

샘플 응용 프로그램을 실행하여 센서 데이터를 수집하고 IoT Hub로 전송했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
