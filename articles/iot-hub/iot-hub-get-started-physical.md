---
title: "Azure IoT Hub에 실제 장치 연결 시작 | Microsoft Docs"
description: "물리적 장치와 보드를 Azure IoT Hub에 연결하는 방법을 알아봅니다. 장치는 원격 분석을 IoT Hub로 전송할 수 있고 Iot Hub는 사용자 장치를 모니터링하고 관리할 수 있습니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT Hub 자습서"
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: 26a2d63aef21bd470d65d79e1abf8cefd2828419
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2017
---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>실제 장치로 시작하는 Azure IoT Hub 자습서

이러한 자습서는 Azure IoT Hub 및 장치 SDK를 소개합니다. 이 자습서에서는 IoT Hub의 기능을 설명하기 위한 일반적인 IoT 시나리오를 다룹니다. 또한 IoT Hub를 다른 Azure 서비스 및 도구와 결합하여 좀 더 강력한 IoT 솔루션을 구축하는 방법도 보여 줍니다. 다음 테이블에 나열된 자습서는 실제 IoT 장치를 만드는 방법을 보여 줍니다.

| IoT 장치                       | 프로그래밍 언어 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                      | [VSCode의 Arduino][DevKit]     |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
