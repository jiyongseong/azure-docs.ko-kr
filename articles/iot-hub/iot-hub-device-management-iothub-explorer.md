---
title: iothub-explorer를 사용하여 Azure IoT 장치 관리 | Microsoft Docs
description: Direct 메서드와 Twin의 desired 속성 관리 옵션을 제공하는 iothub-explorer CLI 도구를 사용하여 Azure IoT Hub 장치를 관리합니다.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: Azure IoT 장치 관리, Azure IoT Hub 장치 관리, 장치 관리 IoT, IoT Hub 장치 관리
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 26e08c3d6b1c96e2d508c87f188118aec02bab6a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>iothub-explorer를 사용하여 Azure IoT Hub 장치 관리

![종단 간 다이어그램](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer)는 호스트 컴퓨터에서 실행하여 IoT Hub 레지스트리의 장치 ID를 관리하는 CLI 도구입니다. 다양한 작업을 수행하는 데 사용할 수 있는 관리 옵션이 제공됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 관리 옵션          | Task                                                                                                                            |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------|
| 직접 메서드             | 메시지 보내기 시작 또는 중지, 장치 다시 부팅 등의 장치 작업을 수행합니다.                                        |
| Twin desired 속성    | 장치를 특정 상태(예: LED를 녹색으로 설정 또는 원격 분석 전송 간격을 30 분으로 설정)로 지정합니다.         |
| Twin reported 속성   | 장치의 보고된 상태를 가져옵니다. 예를 들어 장치에서 지금 LED가 깜박이고 있다고 보고합니다.                                    |
| Twin tags                  | 클라우드에서 장치 전용 메타데이터를 저장합니다. 예를 들어 자동 판매기의 배포 위치입니다.                         |
| 클라우드-장치 메시지   | 장치에 알림을 보냅니다. 예를 들어 "오늘 비가 올 가능성이 매우 높습니다. 반드시 우산을 챙기세요."              |
| 장치 쌍 쿼리        | 모든 장치 쌍을 쿼리하여 사용할 수 있는 장치를 식별하는 등 임의의 조건에 해당하는 장치를 검색합니다. |

이러한 옵션을 사용하는 방법에 대한 차이점과 지침에 대한 자세한 내용은 [장치-클라우드 통신 지침](iot-hub-devguide-d2c-guidance.md) 및 [클라우드-장치 통신 지침](iot-hub-devguide-c2d-guidance.md)을 참조하세요.

장치 쌍은 장치의 상태 정보(메타데이터, 상태 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 여기에 연결하는 각 장치에 대해 하나의 장치 쌍을 유지합니다. 장치 쌍에 대한 자세한 내용은 [장치 쌍 시작](iot-hub-node-node-twin-getstarted.md)을 참조하세요.

## <a name="what-you-learn"></a>학습 내용

배포 컴퓨터에서 다양한 관리 옵션으로 iothub-explorer를 사용하는 방법에 대해 알아봅니다.

## <a name="what-you-do"></a>수행할 작업

다양한 관리 옵션으로 iothub-explorer를 실행합니다.

## <a name="what-you-need"></a>필요한 항목

- 다음 요구 사항을 다루는 자습서 [장치 설정](iot-hub-raspberry-pi-kit-node-get-started.md) 완료:
- 활성 Azure 구독.
- 구독 중인 Azure IoT Hub
- 메시지를 Azure IoT Hub로 보내는 클라이언트 응용 프로그램
- 이 자습서를 진행하는 동안 장치가 클라이언트 응용 프로그램을 사용해서 실행되고 있어야 합니다.
- iothub-explorer, 개발 컴퓨터에 [iothub-explorer를 설치](https://github.com/azure/iothub-explorer)합니다.

## <a name="connect-to-your-iot-hub"></a>IoT Hub에 연결

다음 명령을 실행하여 IoT Hub에 로그인합니다.

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>직접 메서드로 iothub-explorer 사용

다음 명령으로 장치 앱에서 `start` 메서드를 호출하여 IoT Hub로 메시지를 보냅니다.

```bash
iothub-explorer device-method <your device Id> start
```

다음 명령으로 장치 앱에서 `stop` 메서드를 호출하여 IoT Hub로의 메시지 전송을 중지합니다.

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>twin의 desired 속성으로 iothub-explorer 사용

다음 명령을 실행하여 desired 속성의 간격(interval = 3000)을 설정합니다.

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

이 속성은 장치에서 읽을 수 있습니다.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>twin의 reported 속성으로 iothub-explorer 사용

다음 명령을 실행하여 장치의 reported 속성을 가져옵니다.

```bash
iothub-explorer get-twin <your device id>
```

속성 중 하나인 $metadata.$lastUpdated는 이 장치에서 메시지를 보내거나 받은 마지막 시간을 보여 줍니다.

## <a name="use-iothub-explorer-with-twins-tags"></a>twin의 tags로 iothub-explorer 사용

다음 명령을 실행하여 장치의 태그와 속성을 표시합니다.

```bash
iothub-explorer get-twin <your device id>
```

다음 명령을 실행하여 장치에 역할(role = temperature&humidity) 필드를 추가합니다.

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"
```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>클라우드-장치 메시지로 iothub-explorer 사용

다음 명령을 실행하여 장치에 "Hello World" 메시지를 보냅니다.

```bash
iothub-explorer send <device-id> "Hello World"
```

이 명령을 사용하는 실제 시나리오는 [iothub-explorer를 사용하여 장치와 IoT Hub 간에 메시지 보내기 및 받기](iot-hub-explorer-cloud-device-messaging.md)를 참조하세요.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>장치 쌍 쿼리로 iothub-explorer 사용

다음 명령을 실행하여 role = 'temperature & humidity' 태그가 있는 장치를 쿼리합니다.

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

다음 명령을 실행하여 role = 'temperature & humidity' 태그가 있는 장치를 제외한 모든 장치를 쿼리합니다.

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>다음 단계

다양한 관리 옵션으로 iothub-explorer를 사용하는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
