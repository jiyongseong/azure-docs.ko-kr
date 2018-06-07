---
title: Azure IoT Hub 메시징 이해 | Microsoft 문서
description: 개발자 가이드 - IoT Hub를 사용한 장치-클라우드 및 클라우드-장치 메시징 메시지 형식 및 지원되는 통신 프로토콜에 대한 정보가 포함됩니다.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 50f95dc1af334468db25bce68f2ca00e0965a28b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>IoT Hub를 사용한 장치-클라우드 및 클라우드-장치 메시징

IoT Hub 메시징을 사용하여 다음과 같은 방법으로 장치와 통신합니다.

* 장치에서 솔루션 백 엔드로 [장치-클라우드][lnk-d2c] 메시지를 보냅니다.
* 솔루션 백 엔드에서 장치로 [클라우드-장치][lnk-c2d] 메시지를 보냅니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub 메시징 기능의 핵심 속성은 메시지의 안정성 및 내구성입니다. 이 속성을 사용하여 장치 쪽에서 일시적인 연결 및 클라우드 쪽에서 이벤트 처리에 급증한 부하를 복원합니다. IoT Hub는 장치-클라우드 및 클라우드-장치 메시징 모두에 대해 *한 번 이상* 전달 보증을 구현합니다.

IoT Hub 기능에 대한 소개는 [Azure IoT Hub 서비스 개요][lnk-iot-hub-overview]를 참조하세요.

## <a name="when-to-use-iot-hub-messaging"></a>IoT Hub 메시징을 사용하는 경우

장치 앱에서 시계열 원격 분석 및 경고를 보내려면 장치-클라우드 메시지를 사용하고 장치 앱에 단방향 알림을 보내려면 클라우드-장치 메시지를 사용합니다.

* 장치-클라우드 메시지, reported 속성 또는 파일 업로드 사용에 대해 궁금한 점이 있으면 [장치-클라우드 통신 지침][lnk-d2c-guidance]을 참조하세요.
* 클라우드-장치 메시지, desired 속성 또는 직접 메서드 사용에 대해 궁금한 점이 있으면 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.

## <a name="next-steps"></a>다음 단계

* IoT Hub [장치-클라우드 메시징][lnk-d2c]에 대해 알아봅니다.
* IoT Hub [클라우드-장치 메시징][lnk-c2d]에 대해 알아봅니다.

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md