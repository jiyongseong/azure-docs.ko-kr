---
title: Azure IoT Hub 메시징 형식 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub 메시지의 형식 및 예상된 콘텐츠를 설명합니다.
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
ms.date: 03/20/2018
ms.author: dobett
ms.openlocfilehash: 5979c0d79a0abd9897fd3970ccea1d9ba07f9dfd
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub 메시지 만들기 및 읽기

프로토콜을 통해 원활한 상호 운용성을 지원하기 위해 IoT Hub는 모든 장치 지향 프로토콜에 대한 일반적인 메시지 형식을 정의합니다. 이 메시지 형식은 [장치-클라우드][lnk-d2c] 및 [클라우드-장치][lnk-c2d] 메시지 둘 다에 사용됩니다. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[IoT Hub 메시지][lnk-messaging]는 다음으로 구성됩니다.

* *시스템 속성*집합. IoT Hub가 해석하거나 설정하는 속성입니다. 이 집합은 미리 결정됩니다.
* *응용 프로그램 속성*집합. 메시지 본문을 역직렬화할 필요 없이 응용 프로그램이 정의하고 액세스할 수 있는 문자열 속성 사전입니다. IoT Hub는 절대로 이러한 속성을 수정하지 않습니다.
* 불투명한 이진 본문.

속성 이름과 값은 다음과 같은 경우에 ASCII 영숫자 문자와 ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}```를 포함할 수 있습니다.  

* HTTPS 프로토콜을 사용하여 장치-클라우드 메시지를 보냅니다.
* 클라우드-장치 메시지를 보냅니다.

다른 프로토콜을 사용하여 보낸 메시지를 인코딩하고 디코딩하는 방법에 대한 자세한 내용은 [Azure IoT SDK][lnk-sdks]를 참조하세요.

다음 테이블에는 IoT Hub 메시지의 시스템 속성 집합이 나열되어 있습니다.

| 자산 | 설명 |
| --- | --- |
| MessageId |사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 사용됩니다. 형식: ASCII 7 비트 영숫자 문자 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| 시퀀스 번호 |숫자(장치 큐 별로 고유함)는 IoT Hub에서 각 클라우드-장치 메시지에 할당됩니다. |
| 받는 사람 |[클라우드-장치][lnk-c2d] 메시지에 지정된 대상입니다. |
| ExpiryTimeUtc |메시지 만료 날짜 및 시간입니다. |
| EnqueuedTime |IoT Hub에서 [클라우드-장치][lnk-c2d] 메시지를 수신한 날짜 및 시간입니다. |
| CorrelationId |일반적으로 요청-응답 패턴으로 요청의 MessageId가 포함된 응답 메시지의 String 속성입니다. |
| UserId |메시지의 원본을 지정하는 데 사용되는 ID입니다. 메시지가 IoT Hub에서 생성되면 `{iot hub name}`로 설정합니다. |
| Ack |피드백 메시지 생성기입니다. 이 속성은 장치에서 소비하는 메시지의 결과로 피드백 메시지를 생성하는 IoT Hub를 요청하기 위해 클라우드-장치 메시지에서 사용됩니다. 가능한 값: **none**(기본값): 피드백 메시지가 생성되지 않습니다. **positive**: 메시지가 완료된 경우 피드백 메시지를 받습니다. **negative**: 장치에서 완료되지 않고 메시지가 만료(또는 최대 전달 횟수에 도달)되면 피드백 메시지를 받습니다. **full**: positive 및 negative 모두입니다. 자세한 내용은 [메시지 피드백][lnk-feedback]을 참조하세요. |
| ConnectionDeviceId |IoT Hub에서 장치-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 장치의 **deviceId** 를 포함합니다. |
| ConnectionDeviceGenerationId |IoT Hub에서 장치-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 장치의 **generationId**([장치 ID 속성][lnk-device-properties]당)를 포함합니다. |
| ConnectionAuthMethod |IoT Hub에서 장치-클라우드 메시지에 설정하는 인증 방법입니다. 이 속성에는 메시지를 보내는 장치를 인증하는 데 사용되는 인증 방법에 대한 정보가 포함됩니다. 자세한 내용은 [장치-클라우드 스푸핑 방지][lnk-antispoofing]를 참조하세요. |
| CreationTimeUtc | 장치에서 메시지를 만든 날짜 및 시간입니다. 장치는 명시적으로 이 값을 설정해야 합니다. |

## <a name="message-size"></a>메시지 크기

IoT Hub는 실제 페이로드만을 고려하여 프로토콜 진단 방식으로 메시지 크기를 측정합니다. 크기(바이트 단위)는 다음의 합계로 계산됩니다.

* 본문 크기(바이트)입니다.
* 모든 메시지 시스템 속성 값의 크기(바이트)입니다.
* 모든 사용자 속성 이름 및 값의 크기(바이트 단위)

속성 이름과 값은 ASCII 문자로 제한되므로 문자열의 길이는 바이트 단위의 크기와 같습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub의 메시지 크기 제한에 대한 자세한 내용은 [IoT Hub 할당량 및 제한][lnk-quotas]을 참조하세요.

다양한 프로그래밍 언어로 IoT Hub 메시지를 만들고 읽는 방법을 배우려면 [시작하기][lnk-get-started] 자습서를 참조하세요.

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
