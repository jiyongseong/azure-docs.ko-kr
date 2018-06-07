---
title: Azure IoT Hub 클라우드-장치 옵션 | Microsoft Docs
description: 개발자 가이드 - 직접 메서드, 장치 쌍의 desired 속성 또는 클라우드-장치 통신을 위한 클라우드-장치 메시지를 사용하는 경우에 대한 지침입니다.
services: iot-hub
documentationcenter: ''
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 144bd8e0a954e54cf17fb88105759d0e000454fb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="cloud-to-device-communications-guidance"></a>클라우드-장치 통신 지침
IoT Hub는 백 엔드 앱에 기능을 공개하는 세 가지 옵션을 장치 앱에 제공합니다.

* [직접 메서드][lnk-methods] 결과를 즉시 확인해야 하는 통신의 경우 대화형 장치 제어(예: 선풍기 켜기)에 대해 직접 메서드를 자주 사용합니다.
* [쌍의 원하는 속성][lnk-twins] 장치를 원하는 특정 상태로 만들려는 장기 실행 명령의 경우 예를 들어 원격 분석 송신 간격을 30분으로 설정합니다.
* [클라우드-장치 메시지][lnk-c2d] 장치 앱에 대한 단방향 알림의 경우

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

다음은 다양한 클라우드-장치 통신 옵션에 대해 자세히 비교하고 있습니다.

|  | 직접 메서드 | 쌍의 desired 속성 | 클라우드-장치 메시지 |
| ---- | ------- | ---------- | ---- |
| 시나리오 | 즉각적인 확인이 필요한 명령(예: 팬 작동) | 장치를 원하는 상태로 만들려는 장기 실행 명령. 예를 들어 원격 분석 송신 간격을 30분으로 설정합니다. | 장치 앱에 대한 단방향 알림 |
| 데이터 흐름 | 양방향. 장치 앱에서 메서드에 즉시 응답할 수 있습니다. 솔루션 백 엔드에서 컨텍스트에 따라 요청에 대한 결과를 수신합니다. | 단방향. 장치 앱에서 속성 변경 알림을 수신합니다. | 단방향. 장치 앱에서 메시지를 수신합니다.
| 내구성 | 연결이 끊긴 장치는 연결되지 않습니다. 장치가 연결되어 있지 않다고 솔루션 백 엔드에 알립니다. | 속성 값은 장치 쌍에 유지됩니다. 다음에 다시 연결할 때 장치에서 이 알림을 읽습니다. 속성 값은 [IoT Hub 쿼리 언어][lnk-query]로 검색할 수 있습니다. | 메시지는 최대 48시간 동안 IoT Hub에 보관될 수 있습니다. |
| 대상 | **deviceId**를 사용하는 단일 장치 또는 [jobs][lnk-jobs]를 사용하는 여러 장치 | **deviceId**를 사용하는 단일 장치 또는 [jobs][lnk-jobs]를 사용하는 여러 장치 | **deviceId**를 사용하는 단일 장치 |
| 크기 | 최대 8KB 요청 및 8KB 응답. | 최대 희망 속성 크기는 8KB입니다. | 최대 64KB 메시지 |
| Frequency(빈도) | 높음. 자세한 내용은 [IoT Hub 제한][lnk-quotas]을 참조하세요. | 중간. 자세한 내용은 [IoT Hub 제한][lnk-quotas]을 참조하세요. | 낮음. 자세한 내용은 [IoT Hub 제한][lnk-quotas]을 참조하세요. |
| 프로토콜 | MQTT 또는 AMQP를 통해 사용 가능합니다. | MQTT 또는 AMQP를 통해 사용 가능합니다. | 모든 프로토콜에서 사용할 수 있습니다. HTTPS를 사용할 경우 장치에서 폴링해야 합니다. |

다음 자습서에서 직접 메서드, desired 속성 및 클라우드-장치 메시지를 사용하는 방법에 대해 알아보세요.

* [직접 메서드 사용][lnk-methods-tutorial]
* 장치 쌍의 desired 속성에 대해 [desired 속성을 사용하여 장치 구성][lnk-twin-properties] 
* [클라우드-장치 메시지 보내기][lnk-c2d-tutorial]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
