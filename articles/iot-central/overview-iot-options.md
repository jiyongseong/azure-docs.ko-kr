---
title: Microsoft Azure IoT 옵션 | Microsoft Docs
description: Azure IoT Central, IoT Suite, 또는 IoT Hub를 사용하여 Azure IoT 솔루션을 구현하는 방법을 선택합니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8ed524e87f820f6c1e2e05da0bcbc7241bdd1ec1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201461"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Azure IoT Central과 Azure IoT 옵션 비교

IoT 솔루션을 구현하기 위해 Microsoft Azure IoT Central과 Azure IoT는 서로 다른 고객 요구 사항 집합에 적합한 몇 가지 옵션을 제공합니다.

* [Azure IoT Central](overview-iot-central.md)은 모델 기반 접근 방식을 사용하여 클라우드 솔루션 개발에 대한 전문 지식이 없어도 엔터프라이즈급 IoT 솔루션을 구축할 수 있는 SaaS(Software-as-a-Service) 솔루션입니다.

* [Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)는 사용자 지정 IoT 솔루션의 개발을 가속화하는 데 사용할 수 있는 Azure PaaS(Platform-as-a-Service)에 구축된 [미리 구성된 솔루션](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions)의 엔터프라이즈급 모음입니다.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub는 Azure IoT Central 및 Azure IoT Suite 모두에서 사용하는 Azure PaaS의 핵심입니다. IoT Hub는 수백만 개의 IoT 장치와 클라우드 솔루션 간의 안정적이고 안전한 양방향 통신을 지원할 수 있습니다. IoT Hub는 다음과 같은 IoT 구현 과제를 충족하는 데 도움을 줍니다.

* 대용량 장치 연결 및 관리
* 대용량 원격 분석 수집
* 장치 명령 및 제어
* 장치 보안 강화

## <a name="compare-azure-iot-central-and-azure-iot-suite"></a>Azure IoT Central과 Azure IoT Suite 비교

Azure IoT 제품을 선택하는 것은 IoT 솔루션 계획의 중요한 부분입니다. IoT Hub는 종단 간 IoT 솔루션을 자체적으로 제공하지 않는 개별 Azure 서비스입니다. IoT Hub는 모든 IoT 솔루션의 시작점으로 사용할 수 있으며, Azure IoT Suite 또는 Azure IoT Central을 사용할 필요가 없습니다. IoT Suite와 Azure IoT Central 모두 다른 Azure 서비스와 함께 IoT Hub를 사용합니다. 다음 표에는 요구 사항에 맞게 적절히 선택할 수 있도록 IoT Suite와 Azure IoT Central의 주요 차이점이 요약되어 있습니다.

|     | Azure IoT Central | Azure IoT Suite |
| --- | ----------- | --------- |
| 기본 사용                      | 자세한 서비스 사용자 지정이 필요하지 않은 간단한 IoT 솔루션의 출시 시간을 가속화합니다.                                                    | 최대한의 유연성이 필요한 사용자 지정 IoT 솔루션의 개발을 가속화합니다.                                                                                                                             |
| 기본 PaaS 서비스에 대한 액세스 | SaaS - 완벽하게 관리되는 솔루션이며, 기본 서비스가 노출되지 않습니다.                                                                                            | 기본 Azure 서비스에 액세스하여 이를 관리하거나 필요에 따라 바꿀 수 있습니다.                                                                                                                    |
| 유연성                        | 중간. 기본 제공 브라우저 기반 사용자 환경을 사용하여 솔루션 모델 및 UI 측면을 사용자 지정할 수 있습니다. 다른 구성 요소가 노출되지 않기 때문에 인프라는 사용자 지정할 수 없습니다. | 높음. 마이크로 서비스에 대한 코드는 오픈 소스이며, 원하는 대로 적절히 수정할 수 있습니다. 또한 배포 인프라를 사용자 지정할 수 있습니다.                                               |
| 기술 수준                        | 낮음. 솔루션을 사용자 지정하려면 모델링 기술이 필요합니다. 코딩 기술은 필요하지 않습니다.                                                                          | 약간 높음 - 솔루션 백 엔드를 사용자 지정하려면 Java 또는 .NET 기술이 필요합니다. 시각화를 사용자 지정하려면 JavaScript 기술이 필요합니다.                                                                       |
| 시작 환경             | 응용 프로그램 템플릿 및 장치 템플릿은 미리 빌드된 모델을 제공합니다. 몇 분 안에 배포할 수 있습니다.                                                                                                  | 미리 구성된 솔루션에서 일반적인 IoT 시나리오를 구현합니다. 몇 분 안에 배포할 수 있습니다.                                                                                                                            |
| 가격                            | 간단하고 예측 가능한 가격 책정 구조입니다.                                                                                                                           | 비용을 제어하기 위해 서비스를 자세히 튜닝할 수 있습니다.                                                                                                                                                            |

궁극적으로 IoT 솔루션을 빌드하는 데 사용할 제품은 다음 조건에 따라 결정할 수 있습니다.

* 비즈니스 요구 사항
* 빌드하려는 솔루션 유형
* 장기적으로 솔루션을 빌드하고 유지 관리할 수 있는 조직의 기술

## <a name="next-steps"></a>다음 단계

선택한 제품 및 접근 방식에 따라 제안되는 단계는 다음과 같습니다.

* **Azure IoT Central**: [Azure IoT Central](overview-iot-central.md).
* **IoT Suite**: [미리 구성된 Azure IoT Suite 솔루션은 무엇입니까?](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions)
* **IoT Hub**: [Azure IoT Hub 서비스 개요](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)