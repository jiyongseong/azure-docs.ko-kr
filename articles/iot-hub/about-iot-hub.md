---
title: Azure IoT Hub 소개 | Microsoft Docs
description: Azure IoT Hub에 대해 알아봅니다. 이 IoT 서비스는 확장성 있는 데이터 수집, 장치 관리 및 보안을 위해 구축되었습니다.
services: iot-hub
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 04/27/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 7ba74834e1c3aad5915959e0457b6e034e4a49ed
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-iot-hub"></a>Azure IoT Hub란?

IoT Hub는 클라우드에서 호스팅되는 관리 서비스이며, IoT 응용 프로그램과 이를 통해 관리하는 장치 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. Azure IoT Hub를 사용하여 수백만 개의 IoT 장치와 클라우드 호스팅 솔루션 백 엔드 간에 안정적이고 안전한 통신을 통해 IoT 솔루션을 구축할 수 있습니다. 거의 모든 장치를 IoT Hub에 연결할 수 있습니다.

IoT Hub는 장치에서 클라우드로, 클라우드에서 장치로의 통신을 모두 지원합니다. IoT Hub는 장치-클라우드 원격 분석, 장치에서 파일 업로드 및 클라우드에서 장치를 제어하는 요청-회신 방법과 같은 여러 메시징 패턴을 지원합니다. IoT Hub 모니터링을 사용하면 장치 만들기, 장치 오류 및 장치 연결과 같은 이벤트를 추적하여 솔루션의 상태를 유지 관리할 수 있습니다.

IoT Hub의 기능을 사용하면 제조에 사용된 산업 장비 관리, 의료 분야의 중요한 자산 추적 및 오피스 빌딩 사용 모니터링과 같이 모든 기능을 갖춘 확장성 있는 IoT 솔루션을 구축할 수 있습니다.

## <a name="scale-your-solution"></a>솔루션 확장

IoT Hub는 수백만 개의 동시 연결 장치와 초당 수백만 개의 이벤트까지 확장하여 IoT 작업을 지원합니다. IoT Hub는 확장성 요구 사항에 가장 적합한 여러 계층의 서비스를 제공합니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>통신 보안

IoT Hub는 장치에서 데이터를 보낼 수 있도록 안전한 통신 채널을 제공합니다.

* 장치별 인증을 사용하면 각 장치에서 IoT Hub에 안전하게 연결할 수 있으며 각 장치를 안전하게 관리할 수 있습니다.
* 장치 액세스를 완벽하게 제어하고, 장치별 수준에서 연결을 제어할 수 있습니다.
* 장치가 처음 부팅될 때 [IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)에서 해당 장치를 올바른 IoT Hub에 자동으로 프로비전합니다.
* 여러 인증 유형을 통해 다양한 장치 기능을 지원합니다.
  * IoT 솔루션을 빠르게 시작할 수 있는 SAS 토큰 기반 인증
  * 안전한 표준 기반 인증을 위한 개별 X.509 인증서 인증
  * 간단한 표준 기반 등록을 위한 X.509 CA 인증

## <a name="route-device-data"></a>장치 데이터 라우팅

기본 제공 메시지 라우팅 기능을 사용하면 자동 규칙 기반 메시지 팬아웃을 유연하게 설정할 수 있습니다.

* 메시지 라우팅을 사용하여 허브에서 장치 원격 분석을 보내는 위치를 제어합니다.
* 메시지를 여러 엔드포인트에 라우팅하는 데 추가 비용이 들지 않습니다.
* 코드 없는 라우팅 규칙이 사용자 지정 메시지 디스패처 코드 대신 사용됩니다.

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

IoT Hub를 다른 Azure 서비스와 통합하여 완전한 종단 간 솔루션을 구축할 수 있습니다. 예를 들어 이에 해당하는 서비스는 다음과 같습니다.

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)를 사용하여 비즈니스에서 안정적이고, 확장 가능하며, 안전한 방식으로 중요한 이벤트에 빠르게 대응할 수 있습니다.

* [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)를 사용하여 비즈니스 프로세스를 자동화합니다.

* [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/)을 사용하여 컴퓨터 학습 및 AI 모델을 솔루션에 추가합니다.

* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)를 사용하여 장치에서 스트림하는 데이터에 대해 실시간 분석 계산을 실행합니다.

## <a name="configure-and-control-your-devices"></a>장치 구성 및 제어

기본 제공 기능 배열을 사용하여 IoT Hub에 연결된 장치를 관리할 수 있습니다.

* 모든 장치에 대한 장치 메타데이터 및 상태 정보를 저장, 동기화 및 쿼리합니다.
* 장치별로 또는 장치의 일반적인 특성에 따라 장치 상태를 설정합니다.
* 메시지 라우팅 통합을 통해 장치에서 보고된 상태 변경에 자동으로 응답합니다.

## <a name="make-your-solution-highly-available"></a>고가용성 솔루션 만들기

[IoT Hub에 대한 99.9% 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/iot-hub/)이 있습니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

## <a name="connect-your-devices"></a>사용자 장치 연결

[Azure IoT 장치 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 라이브러리를 사용하여 장치에서 실행되고 IoT Hub와 상호 작용하는 응용 프로그램을 빌드합니다. 지원되는 플랫폼에는 여러 Linux 배포판, Windows 및 실시간 운영 체제가 포함됩니다. 지원되는 언어는 다음과 같습니다.

* C
* C#
* 자바
* 파이썬
* Node.js

장치 연결을 위해 IoT Hub 및 장치 SDK에서 지원하는 프로토콜은 다음과 같습니다.

* HTTPS
* AMQP
* Websocket 통한 AMQP
* MQTT
* WebSocket을 통한 MQTT

솔루션에서 장치 라이브러리를 사용할 수 없는 경우 장치에서 기본적으로 MQTT v3.1.1, HTTPS 1.1 또는 AMQP 1.0 프로토콜을 사용하여 허브에 연결할 수 있습니다.

솔루션에서 지원되는 프로토콜 중 하나를 사용할 수 없는 경우 사용자 지정 프로토콜을 지원하도록 IoT Hub를 확장할 수 있습니다.

* 에지에서 프로토콜 변환을 수행하는 필드 게이트웨이를 만들려면 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)를 사용합니다.
* 클라우드에서 프로토콜 변환을 수행하려면 [Azure IoT 프로토콜 게이트웨이](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)를 사용자 지정합니다.

## <a name="quotas-and-limits"></a>할당량 및 제한

Azure 구독마다 서비스 남용을 방지하기 위해 기본 할당량 한도가 적절히 설정되어 있으며, 이러한 제한은 IoT 솔루션의 범위에 영향을 줄 수 있습니다. 구독별 기준의 현재 제한은 구독당 10개 IoT 허브입니다. 지원 센터에 문의하여 할당량 증가를 요청할 수 있습니다. 할당량 제한에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 구독 및 서비스 제한](../azure-subscription-service-limits.md)
* [IoT Hub 제한 및 사용자](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="next-steps"></a>다음 단계

종단 간 IoT 솔루션을 사용해 보려면 다음 IoT Hub 빠른 시작을 확인해 보세요.

* [빠른 시작: 원격 분석을 장치에서 IoT 허브로 전송](iot-hub-get-started.md)
