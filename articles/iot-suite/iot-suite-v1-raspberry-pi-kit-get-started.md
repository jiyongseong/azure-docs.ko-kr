---
title: "Raspberry Pi를 Azure IoT Suite에 연결 | Microsoft Docs"
description: "Node.js 또는 C를 사용하여 Raspberry Pi 3용 Microsoft Azure IoT 스타터 키트 및 IoT Suite 원격 모니터링 솔루션을 사용하는 방법을 배우도록 도와주는 자습서입니다. 원격 분석을 시뮬레이트하거나, 실제 센서를 사용하거나, 원격 펌웨어 업데이트를 사용하도록 설정하는 자습서를 선택할 수 있습니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d9e737fcaaabd2088d2920b69fca96d6058f4b4a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-microsoft-azure-iot-raspberry-pi-3-starter-kit-to-the-remote-monitoring-solution"></a>Microsoft Azure IoT Raspberry Pi 3 스타터 키트를 원격 모니터링 솔루션에 연결

이 섹션의 자습서에서는 원격 모니터링 솔루션에 Raspberry Pi 3 장치를 연결하는 방법을 배울 수 있습니다. 원하는 프로그래밍 언어 및 Raspberry Pi에서 센서 하드웨어를 함께 사용할 수 있는지 여부에 따라 자습서를 선택하세요.

## <a name="the-tutorials"></a>자습서

| 자습서 | 참고 사항 | 언어 |
| -------- | ----- | --------- |
| 시뮬레이트된 원격 분석(기본)| 센서 데이터를 시뮬레이트합니다. 독립 실행형 Raspberry Pi를 사용합니다. | [C][lnk-c-simulator], [Node.js][lnk-node-simulator] |
| 실제 센서(중간) | Raspberry Pi에 연결된 BME280 센서의 데이터를 사용합니다. | [C][lnk-c-basic], [Node.js][lnk-node-basic] |
| 펌웨어 업데이트 구현(고급)| Raspberry Pi에 연결된 BME280 센서의 데이터를 사용합니다. Raspberry Pi에서 원격 펌웨어 업데이트를 사용하도록 설정합니다. | [C][lnk-c-advanced], [Node.js][lnk-node-advanced] |

## <a name="next-steps"></a>다음 단계

Azure IoT에 대한 추가 샘플 및 설명서를 보려면 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot/)를 방문하세요.

[lnk-node-simulator]: iot-suite-v1-raspberry-pi-kit-node-get-started-simulator.md
[lnk-node-basic]: iot-suite-v1-raspberry-pi-kit-node-get-started-basic.md
[lnk-node-advanced]: iot-suite-v1-raspberry-pi-kit-node-get-started-advanced.md
[lnk-c-simulator]: iot-suite-v1-raspberry-pi-kit-c-get-started-simulator.md
[lnk-c-basic]: iot-suite-v1-raspberry-pi-kit-c-get-started-basic.md
[lnk-c-advanced]: iot-suite-v1-raspberry-pi-kit-c-get-started-advanced.md