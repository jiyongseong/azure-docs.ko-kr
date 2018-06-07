---
title: Azure IoT 솔루션 가속기 개요 | Microsoft Docs
description: Azure IoT 솔루션 가속기 및 추가 리소스에 대한 링크와 해당 아키텍처에 대한 설명입니다.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: 80192b115f4c6c8f6961fdd4d9fb7f3876fda898
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362127"
---
# <a name="what-are-the-iot-solution-accelerators"></a>IoT 솔루션 가속기란?

Azure IoT _솔루션 가속기_는 다음과 같은 솔루션의 집합입니다.

* 몇 분 안에 배포합니다.
* 빨리 시작할 수 있도록 지원합니다.
* 특정 요구 사항에 맞게 사용자 지정할 수 있습니다.

솔루션 가속기는 모두 동일한 원칙과 목표에 따라 설계되었습니다.

다음 비디오는 원격 모니터링 솔루션 가속기에 대한 개요를 제공합니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="solution-accelerators-overview"></a>솔루션 가속기 개요

솔루션 가속기는 구독을 사용하여 Azure에 배포할 수 있는 일반적인 IoT 솔루션 패턴의 오픈 소스 구현입니다. 각 솔루션 가속기는 사용자 지정 코드와 Azure 서비스를 결합하여 특정 IoT 시나리오 또는 일반 시나리오를 구현합니다. 특정 요구 사항에 맞게 시나리오를 사용자 지정할 수 있습니다. 이 시나리오에는 다음이 포함됩니다.

* 깊은 통찰력과 솔루션 상태를 제공하기 위해 다양한 대시보드에서 데이터를 시각화합니다.
* 라이브 IoT 장치 원격 분석에 대한 규칙 및 경보를 구성합니다.
* 장치 관리 작업(예: 소프트웨어 및 구성에 대한 업데이트)을 예약합니다.
* 사용자 고유의 사용자 지정 물리적 또는 시뮬레이션된 장치를 프로비전합니다.
* IoT 장치 그룹 내의 문제를 해결하고 재구성합니다.

각 솔루션 가속기는 시뮬레이션된 장치 또는 물리적 장치를 사용하여 원격 분석을 생성할 수 있는 완전한 종단 간 구현입니다. 솔루션 가속기를 솔루션 가속기로 사용하여 다음을 수행할 수 있습니다.

* 사용자 고유의 IoT 솔루션에 대한 시작점을 제공합니다.
* IoT 솔루션 설계 및 개발의 일반적인 패턴에 대해 알아봅니다.

세 가지 솔루션 가속기는 현재 다음을 사용할 수 있습니다.

* [원격 모니터링](iot-accelerators-remote-monitoring-explore.md)
* [예측 유지 관리](../iot-suite/iot-suite-predictive-overview.md)
* [연결된 팩터리](iot-accelerators-connected-factory-overview.md)

다음 표에서 솔루션을 특정 IoT 기능에 매핑하는 방법을 보여 줍니다.

| 해결 방법 | 데이터 수집 | 장치 ID | 장치 관리 | 에지 처리 | 명령 및 컨트롤 | 규칙 및 동작 | 예측 분석 |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [원격 모니터링](iot-accelerators-remote-monitoring-explore.md)  |예 |예 |예 |-   |예 |예 |-   |
| [예측 유지 관리](../iot-suite/iot-suite-predictive-overview.md)   |예 |예 |-   |-   |예 |예 |예 |
| [연결된 팩터리](iot-accelerators-connected-factory-overview.md) |예 |- |- |예 |예 |예 |-   |

* *데이터 수집*: 클라우드에 대한 대규모 데이터의 수신입니다.
* *장치 ID*: 고유한 장치 ID를 관리하고 솔루션에 대한 장치 액세스를 제어합니다.
* *장치 관리*: 장치 메타데이터를 관리하고 장치 재부팅 및 펌웨어 업그레이드 등의 작업을 수행합니다.
* *명령 및 컨트롤*: 장치가 작업을 수행하도록 하기 위해 클라우드에서 장치에 메시지를 보냅니다.
* *규칙 및 동작*: 특정 장치-클라우드 데이터에서 작동하도록 하기 위해 솔루션 백 엔드가 규칙을 사용합니다.
* *예측 분석*: 솔루션 백 엔드는 장치-클라우드 데이터를 분석하여 특정 작업이 수행될 때를 예측합니다. 예를 들어 항공기 엔진 원격 분석을 분석하여 엔진 유지 관리가 만료될 때를 확인합니다.

> [!NOTE]
> 솔루션 가속기를 배포하고 이를 사용자 지정하는 방법에 대해 자세히 알아보려면 [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsuite.com/)를 방문하세요.

## <a name="azure-services"></a>Azure 서비스

솔루션 가속기를 배포하면 프로비전 프로세스에서 다양한 Azure 서비스를 구성합니다. 다음 표에서는 솔루션 가속기에서 사용되는 서비스를 보여 줍니다.

|                      | 원격 모니터링  | 예측 유지 관리 | 연결된 팩터리 |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | 예                | 예                    | 예               |
| Event Hubs           |                    | 예                    |                   |
| Time Series Insights |                    |                        | 예               |
| Container Services   | 예                |                        |                   |
| Stream Analytics     |                    | 예                    |                   |
| Web Apps             | 예                | 예                    | 예               |
| Cosmos DB            | 예                | 예                    |                    |
| Azure Storage         |                    | 예                    | 예               |

> [!NOTE]
> 원격 모니터링 솔루션 가속기에 배포된 리소스에 대한 자세한 내용은 GitHub에서 이 [문서](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments)를 참조하세요.

* [Azure IoT Hub](../iot-hub/index.yml) - 이 서비스는 장치-클라우드 및 클라우드-장치 메시징 기능을 제공하고 클라우드 및 다른 핵심 솔루션 가속기 서비스에 대한 게이트웨이의 역할을 합니다. 이 서비스를 사용하면 장치로부터 대규모 메시지를 수신하고 장치로 명령을 보낼 수 있습니다. 서비스를 통해 [장치를 관리](../iot-hub/iot-hub-device-management-overview.md)할 수도 있습니다. 예를 들어 허브에 연결된 하나 이상의 장치에서 공장 재설정을 구성, 다시 부팅 또는 수행할 수 있습니다.
* [Azure Event Hubs](../active-directory-domain-services/index.md) - 클라우드에 대규모 이벤트 수집을 제공합니다. [Azure IoT Hub 및 Azure Event Hubs의 비교](../iot-hub/iot-hub-compare-event-hubs.md)를 참조하세요.
* [Azure Time Series Insights](../time-series-insights/index.yml) - 솔루션 가속기에서 이 서비스를 사용하여 장치의 원격 분석 데이터를 분석하고 표시합니다.
* [Azure Container Service](../container-service/index.yml) - 이 서비스는 솔루션 가속기에서 마이크로 서비스를 호스팅하고 관리합니다.
* [Azure Cosmos DB](../cosmos-db/index.yml) 및 [Azure Storage](../storage/index.yml)(데이터 저장용)
* [Azure Stream Analytics](../stream-analytics/index.yml) - 미리 구성된 예측 유지 관리 솔루션에서 이 서비스를 사용하여 들어오는 원격 분석을 처리하고, 집계를 수행하며, 이벤트를 검색합니다. 또한 미리 구성된 솔루션에서 Stream Analytics를 사용하여 장치의 메타데이터 또는 명령 응답과 같은 데이터가 포함된 정보 메시지를 처리합니다.
* [Azure Web Apps](../app-service/index.yml) - 미리 구성된 솔루션에서 사용자 지정 응용 프로그램 코드를 호스팅합니다.

일반적인 IoT 솔루션의 아키텍처에 대한 개요는 [Microsoft Azure 및 IoT(사물 인터넷)](iot-accelerators-what-is-azure-iot.md)를 참조하세요.

## <a name="whats-new-in-solution-accelerators"></a>솔루션 가속기의 새로운 기능은?

Microsoft는 솔루션 가속기를 새로운 마이크로 서비스 기반 아키텍처로 업데이트하고 있습니다. 다음 표에서는 솔루션 가속기의 현재 상태를 보여 줍니다.

| 솔루션 가속기 | 아키텍처  | 언어     |
| ---------------------- | ------------- | ------------- |
| 원격 모니터링      | 마이크로 서비스 | Java 및 .NET |
| 예측 유지 관리 | MVC           | .NET          |
| 연결된 팩터리      | MVC           | .NET          |

다음 섹션에서는 솔루션 가속기의 새로운 마이크로 서비스 기반 기능에 대해 설명합니다.

### <a name="microservices"></a>마이크로 서비스

원격 모니터링 솔루션 가속기의 새 버전에서는 마이크로 서비스 아키텍처를 사용합니다. 이 솔루션 가속기는 *IoT Hub 관리자* 및 *Storage 관리자*와 같은 여러 마이크로 서비스로 구성됩니다. Java 및 .NET 버전 모두의 각 마이크로 서비스는 관련된 개발자 설명서와 함께 다운로드할 수 있습니다. 마이크로 서비스에 대한 자세한 내용은 [원격 모니터링 아키텍처](iot-accelerators-remote-monitoring-sample-walkthrough.md)를 참조하세요.

이 마이크로 서비스 아키텍처는 클라우드 솔루션에 대해 다음과 같이 검증된 패턴입니다.

* 확장할 수 있습니다.
* 확장성을 사용하도록 설정합니다.
* 이해하기 쉽습니다.
* 개별 서비스를 대안으로 전환할 수 있게 합니다.

> [!TIP]
> 마이크로 서비스 아키텍처에 대한 자세한 내용은 [.NET 응용 프로그램 아키텍처](https://www.microsoft.com/net/learn/architecture) 및 [마이크로 서비스: 클라우드에서 제공하는 응용 프로그램 혁명](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)을 참조하세요.

새 버전의 원격 모니터링을 배포하는 경우 다음 배포 옵션 중 하나를 선택해야 합니다.

* **Basic:** 데모 또는 배포 테스트에 대한 비용을 절약할 수 있는 버전입니다. 모든 마이크로 서비스가 단일 Azure 가상 머신에 배포됩니다.
* **Standard:** 프로덕션 배포를 개발할 수 있도록 확장된 인프라 배포입니다. Azure Container Service는 마이크로 서비스를 여러 개의 Azure 가상 머신에 배포합니다. Kubernetes는 개별 마이크로 서비스를 호스팅하는 Docker 컨테이너를 오케스트레이션합니다.

### <a name="language-choices-java-and-net"></a>언어 선택 사항: Java 및 .NET

각 마이크로 서비스의 구현은 Java 및 .NET 모두에서 사용할 수 있습니다. .NET 코드와 마찬가지로 Java 소스 코드는 오픈 소스이며 특정 요구 사항에 맞게 사용자 지정할 수 있습니다.

* [.NET GitHub 리포지토리 원격 모니터링](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java GitHub 리포지토리 원격 모니터링](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

다른 언어 구현을 확인하려면 [Azure IoT 사용자 의견](https://feedback.azure.com/forums/321918-azure-iot)에 요청을 추가해 주세요.

### <a name="react-user-interface-framework"></a>React 사용자 인터페이스 프레임워크

UI는 [React](https://facebook.github.io/react/) JavaScript 라이브러리를 사용하여 빌드됩니다. 소스 코드는 오픈 소스이며, 다운로드하여 사용자 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 IoT 솔루션 가속기의 개요를 살펴보았으므로 각 솔루션 가속기에 제안되는 단계는 다음과 같습니다.

* [원격 모니터링 솔루션 탐색](iot-accelerators-remote-monitoring-explore.md).
* [예측 유지 관리 솔루션 가속기 개요](../iot-suite/iot-suite-predictive-overview.md).
* [연결된 팩터리 가속화 솔루션 시작](iot-accelerators-connected-factory-overview.md).

IoT 솔루션 아키텍처에 대한 자세한 내용은 [Microsoft Azure IoT 서비스: 참조 아키텍처](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf)를 참조하세요.
