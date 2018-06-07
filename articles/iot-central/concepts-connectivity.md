---
title: Azure IoT Central에서 장치 연결 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 장치 연결과 관련된 주요 개념을 소개합니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 00b621a4635ef1ceda26772ac5876fa2599b56f8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202648"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central의 장치 연결 | Microsoft Docs

이 문서에서는 Microsoft Azure IoT Central의 장치 연결과 관련된 주요 개념을 소개합니다.

Azure IoT Central 응용 프로그램에 연결하는 모든 장치는 Azure IoT Central에서 사용하는 IoT Hub를 통해 노출되는 [엔드포인트](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints)에 연결합니다. IoT Hub는 연결된 장치에서 확장 가능하고 안전하고 신뢰할 수 있는 연결을 지원합니다.

## <a name="sdk-support"></a>SDK 지원

Azure 장치 SDK는 장치에서 Azure IoT Central 응용 프로그램에 연결하는 코드를 구현하는 가장 쉬운 방법을 제공합니다. 현재 다음과 같은 장치 SDK를 사용할 수 있습니다.

- [C용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Java용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

각 장치는 장치를 식별하는 고유한 연결 문자열을 사용하여 연결합니다. 장치는 등록된 IoT Hub에만 연결할 수 있습니다. Azure IoT Central 응용 프로그램에 실제 장치를 만들면 응용 프로그램에서는 사용할 연결 문자열을 생성합니다.

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 기능 및 IoT Hub 연결

IoT Hub와의 모든 장치 통신에 다음 IoT Hub 연결 옵션이 사용됩니다.

- [장치-클라우드 메시지](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [장치 쌍](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

다음 표에는 Azure IoT Central 장치 기능이 IoT Hub 기능에 매핑되는 방식이 요약되어 있습니다.

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 측정값: 원격 분석 | 장치-클라우드 메시징 |
| 장치 속성 | 장치 쌍 reported 속성 |
| 설정 | 장치 쌍 desired 및 reported 속성 |

장치 SDK 사용에 대해 자세히 알아보려면 다음 문서 중 하나에서 코드 예제를 참조하세요.

- [Azure IoT Central 응용 프로그램에 일반 Node.js 클라이언트 연결](howto-connect-nodejs.md)
- [Azure IoT Central 응용 프로그램에 Raspberry Pi 장치 연결](howto-connect-raspberry-pi-python.md)
- [Azure IoT Central 응용 프로그램에 DevDiv 키트 장치 연결](howto-connect-devkit.md)

다음 비디오는 Azure IoT Central에 실제 장치를 연결하는 방법의 개요를 보여줍니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>프로토콜

장치 SDK는 IoT Hub에 연결하기 위한 다음과 같은 네트워크 프로토콜을 지원합니다.

- MQTT
- AMQP
- HTTPS

프로토콜 간 차이점 및 프로토콜 선택 방법에 대한 지침은 [통신 프로토콜 선택](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols)을 참조하세요.

지원되는 프로토콜을 장치에서 사용할 수 없는 경우 Azure IoT Edge를 사용하여 프로토콜을 변환하면 됩니다. IoT Edge는 Azure IoT Central 응용 프로그램의 에지로 프로세싱을 오프로드하는 에지의 인텔리전스 시나리오를 지원합니다.

## <a name="security"></a>보안

장치와 Azure IoT Central 간에 교환되는 모든 데이터는 암호화됩니다. IoT Hub는 장치 연결 IoT Hub 엔드포인트 중 하나에 연결하는 장치의 모든 요청을 인증합니다. 유선으로 자격 증명을 교환하지 못하도록 방지하기 위해 장치에서는 서명된 토큰을 사용하여 인증합니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)를 참조하세요.

> [!NOTE]
> 현재 Azure IoT Central에 연결하는 장치는 SAS 토큰을 사용해야 합니다. X.509 인증서는 Azure IoT Central에 연결하는 장치에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central의 장치 연결에 대해 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

- [DevKit 장치 준비 및 연결](howto-connect-devkit.md)
- [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md)
- [Azure IoT Central 응용 프로그램에 일반 Node.js 클라이언트 연결](howto-connect-nodejs.md)
