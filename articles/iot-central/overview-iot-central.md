---
title: Azure IoT Central이란? | Microsoft Docs
description: Azure IoT Central은 사용자 지정 IoT 솔루션을 구축하고 관리하는 데 사용할 수 있는 종단 간 SaaS 솔루션입니다. 이 문서에서는 Azure IoT Central의 기능에 대한 개요를 제공합니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 41eb9049b45c6835dd866c988af486a4975c4831
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201169"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Azure IoT Central이란?

Microsoft Azure IoT Central은 실제 세계와 디지털 세계를 연결하는 제품을 쉽게 만들 수 있게 해주며, 완벽하게 관리되는 IoT SaaS(Software-as-a-Service) 솔루션입니다. 다음을 통해 연결된 제품 비전을 실현할 수 있습니다.

- 고객에게 더 나은 제품과 경험을 제공할 수 있도록 연결된 장치에서 새로운 인사이트 유도.
- 조직을 위한 새로운 비즈니스 기회 창출.

Azure IoT Central은 일반적인 IoT 프로젝트와 비교하여 다음과 같은 방법으로 IoT 솔루션 관리의 번거로움을 덜어줍니다.

- 관리 부담 경감.
- 운영 비용 및 오버헤드 경감.
- 응용 프로그램을 쉽게 사용자 지정하는 동시에 다음을 활용할 수 있습니다.
  - [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 및 [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)와 같은 업계 최고의 기술.
  - 종단 간 암호화와 같은 엔터프라이즈급 보안 기능.

다음 비디오는 Azure IoT Central의 개요를 보여 줍니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

이 문서의 나머지 부분에서는 Azure IoT Central에 대해 개략적으로 설명합니다.

- 프로젝트와 관련된 일반적인 가상 사용자.
- 응용 프로그램을 만드는 방법.
- 장치를 응용 프로그램에 연결하는 방법
- 응용 프로그램을 관리하는 방법.

## <a name="personas"></a>가상 사용자

Azure IoT Central 문서는 Azure IoT Central 응용 프로그램과 상호 작용하는 4명의 일반적인 가상 사용자를 나타냅니다.

- _개발자_는 응용 프로그램에 연결하는 장치 유형을 정의하고 운영자에 맞게 응용 프로그램을 사용자 지정하는 역할을 담당합니다.
- _운영자_는 응용 프로그램에 연결된 장치를 관리합니다.
- _관리자_는 응용 프로그램 내 사용자 및 역할 관리와 같은 관리 작업을 담당합니다.
- _장치 개발자_는 응용 프로그램에 연결된 장치에서 실행되는 코드를 만듭니다.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램 만들기

개발자는 Azure IoT Central을 사용하여 조직을 위한 사용자 지정 클라우드 호스팅 IoT 솔루션을 만들 수 있습니다. 사용자 지정 IoT 솔루션은 일반적으로 다음과 같이 구성됩니다.

- 장치에서 원격 분석을 수신하고 해당 장치를 관리할 수 있게 해주는 클라우드 기반 응용 프로그램.
- 클라우드 기반 응용 프로그램에 연결된 사용자 지정 코드를 실행하는 여러 장치.

새로운 Azure IoT Central 응용 프로그램을 빠르게 배포한 다음, 브라우저에서 특정 요구 사항에 맞게 사용자 지정할 수 있습니다. Azure IoT Central 개발자는 웹 기반 도구를 사용하여 응용 프로그램에 연결하는 장치에 대한 _장치 템플릿_을 만들 수 있습니다. 장치 템플릿은 장치 템플릿으로 생성되는 모든 장치가 공유하는 장치 모델의 청사진입니다. 장치 템플릿은 다음과 같은 장치 유형의 특성 및 동작을 정의합니다.

- 전송하는 원격 분석.
- 운영자가 수정할 수 있는 비즈니스 속성.
- 장치에서 설정하고 응용 프로그램에서 읽기 전용인 장치 속성.
- 응용 프로그램이 응답하는 임계값.
- 장치의 동작을 결정하는 설정.

Azure IoT Central에서 생성하는 시뮬레이션 데이터를 사용하여 장치 템플릿과 응용 프로그램을 즉시 테스트할 수 있습니다.

또한 개발자는 응용 프로그램의 일상적인 사용을 담당하는 운영자를 위해 Azure IoT Central 응용 프로그램 UI를 사용자 지정할 수 있습니다. 개발자가 수행할 수 있는 사용자 지정은 다음과 같습니다.

- 장치 템플릿의 속성 및 설정 레이아웃 정의.
- 운영자가 인사이트를 발견하고 문제를 더 빨리 해결할 수 있도록 사용자 지정 대시보드 구성.
- 연결된 장치에서 시계열 데이터를 탐색하는 사용자 지정 분석 구성.

## <a name="connect-your-devices"></a>사용자 장치 연결

개발자가 응용 프로그램에 연결할 수 있는 장치 유형을 정의한 후 장치 개발자는 장치에서 실행할 코드를 만듭니다. 장치 개발자는 Microsoft의 오픈 소스를 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)를 사용하여 장치 코드를 만듭니다. 이 SDK는 광범위한 언어, 플랫폼 및 프로토콜 지원을 통해 Azure IoT Central 응용 프로그램에 장치를 연결하는 데 필요한 사항을 충족합니다. SDK를 사용하면 Azure IoT Central에 연결된 장치에서 다음 작업을 수행할 수 있습니다.

- 보안 연결 생성.
- 원격 분석 전송.
- 상태 보고.
- 구성 업데이트 수신.

자세한 내용은 [Azure IoT SDK 사용 시 이점과 그렇지 않은 경우 방지해야 하는 문제](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)라는 블로그 게시물을 참조하세요.

## <a name="manage-your-application"></a>응용 프로그램 관리

Azure IoT Central 응용 프로그램은 Microsoft에서 완벽하게 호스트하므로 응용 프로그램 관리 부담이 줄어듭니다.

운영자는 Azure IoT Central 응용 프로그램을 사용하여 Azure IoT Central 솔루션의 장치를 관리합니다. 운영자는 다음과 같은 작업을 수행할 수 있습니다.

- 응용 프로그램에 연결된 장치 모니터링.
- 장치 관련 문제 해결 및 수정.
- 새 장치 프로비저닝.

개발자는 장치 템플릿 수준에서 스트리밍 데이터에 적용되는 사용자 지정 규칙 및 작업을 정의할 수 있습니다. 운영자는 장치 수준에서 이러한 규칙을 사용하거나 사용하지 않도록 설정하여 응용 프로그램 내에서 작업을 제어하고 자동화할 수 있습니다.

관리자는 [사용자 역할 및 사용 권한](howto-administer.md)을 사용하여 응용 프로그램에 대한 액세스를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central에 대한 개요를 살펴보았으니, 권장되는 다음 단계는 아래와 같습니다.

- [Azure IoT Central과 Azure IoT Suite](overview-iot-options.md)의 차이점을 이해합니다.
- [Azure IoT Central UI](overview-iot-central-tour.md)를 숙지합니다.
- [Azure IoT Central 응용 프로그램을 생성](quick-deploy-iot-central.md)하여 시작합니다.
- 다음과 같은 방법을 보여주는 일련의 자습서를 따릅니다.
  - [개발자가 장치 템플릿 생성](tutorial-define-device-type.md)
  - [개발자가 솔루션 자동화를 위한 규칙 추가](tutorial-configure-rules.md)
  - [개발자가 운영자에 맞게 응용 프로그램 사용자 지정](tutorial-customize-operator.md)
  - [운영자가 장치 모니터링](tutorial-monitor-devices.md)
  - [운영자가 솔루션에 실제 장치 추가](tutorial-add-device.md)
  - [장치 개발자가 장치용 코드 생성](tutorial-add-device.md#prepare-the-client-code)
