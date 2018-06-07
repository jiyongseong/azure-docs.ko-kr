---
title: iothub-explorer로 Azure IoT Hub 클라우드 장치 메시지 관리 | Microsoft Docs
description: iothub-explorer CLI 도구를 사용하여 Azure IoT Hub에서 D2C(장치-클라우드) 메시지를 모니터링하고 C2D(클라우드-장치) 메시지를 보내는 방법에 대해 알아봅니다.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: iothub explorer, 클라우드 장치 메시지, IoT Hub 클라우드-장치 메시지, 클라우드-장치 메시지
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: ed15f7749676d8a7f0c2ef8b23e238f7bc90e341
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>iothub-explorer를 사용하여 장치와 IoT Hub 간에 메시지 보내기 및 받기

![종단 간 다이어그램](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer)에는 IoT Hub를 더 쉽게 관리할 수 있는 몇 가지 명령이 있습니다. 이 자습서에서는 iothub-explorer를 사용하여 장치와 IoT Hub 간에 메시지를 보내고 받는 방법에 중점을 둡니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>알아볼 내용

iothub-explorer를 사용하여 장치-클라우드 메시지를 모니터링하고 클라우드-장치 메시지를 보내는 방법에 대해 알아봅니다. 장치-클라우드 메시지는 장치에서 수집한 다음 IoT Hub로 보내는 센서 데이터일 수 있습니다. 클라우드-장치 메시지는 IoT Hub에서 장치로 보내 사용자 장치에 연결된 LED를 깜박이는 명령일 수 있습니다.

## <a name="what-you-will-do"></a>수행할 사항

- iothub-explorer를 사용하여 장치-클라우드 메시지를 모니터링합니다.
- iothub-explorer를 사용하여 클라우드-장치 메시지를 보냅니다.

## <a name="what-you-need"></a>필요한 항목

- 다음 요구 사항을 다루는 자습서 [장치 설정](iot-hub-raspberry-pi-kit-node-get-started.md) 완료:
  - 활성 Azure 구독.
  - 구독 중인 Azure IoT Hub
  - 메시지를 Azure IoT Hub로 보내는 클라이언트 응용 프로그램
- iothub-explorer ([Install iothub-explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>장치-클라우드 메시지 모니터링

장치에서 IoT Hub로 보낸 메시지를 모니터링하려면 다음 단계를 수행합니다.

1. 콘솔 창을 엽니다.
1. 다음 명령 실행:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > IoT Hub에서 `<device-id>`과 `<IoTHubConnectionString>`을 가져옵니다. 이전 자습서를 완료했는지 확인합니다. 또는 `HostName`, `SharedAccessKeyName` 및 `SharedAccessKey`가 있는 경우 `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"`을(를) 사용할 수 있습니다.

## <a name="send-cloud-to-device-messages"></a>클라우드-장치 메시지 보내기

IoT Hub에서 장치로 메시지를 보내려면 다음 단계를 수행합니다.

1. 콘솔 창을 엽니다.
1. 다음 명령을 실행하여 IoT Hub에서 세션을 시작합니다.

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. 다음 명령을 실행하여 메시지를 장치로 보냅니다.

   ```bash
   iothub-explorer send <device-id> <message>
   ```

이 명령은 장치에 연결된 LED를 깜박이고 메시지를 장치로 보냅니다.

> [!Note]
> 장치에서 메시지를 받는 즉시 별도의 ack 명령을 IoT Hub로 다시 보내지 않아도 됩니다.

## <a name="next-steps"></a>다음 단계

IoT 장치와 Azure IoT Hub 간에 장치-클라우드 메시지를 모니터링하고 클라우드-장치 메시지를 보내는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
