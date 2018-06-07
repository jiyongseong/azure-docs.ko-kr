---
title: "Azure IoT Hub 고가용성 및 재해 복구 | Microsoft Docs"
description: "재해 복구 기능을 사용하여 고가용성 Azure IoT 솔루션을 빌드할 수 있도록 지원하는 Azure and IoT Hub 기능을 설명합니다."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: ecc5da8daf0f5c93dffc93798f40507f8eac48be
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub 고가용성 및 재해 복구
Azure 서비스로 IoT Hub는 Azure 지역 수준에서 중복을 사용하여 솔루션의 추가 작업 없이도 HA(고가용성)를 제공합니다. Microsoft Azure Platform에는 재해 복구(DR) 기능 또는 지역 간 가용성을 포함하는 솔루션을 빌드하도록 도와주는 기능도 포함되어 있습니다. 장치 또는 사용자에게 전역, 지역 간 고가용성을 제공하려면 이러한 Azure DR 기능을 활용합니다. [Azure 비즈니스 연속성 기술 지침](../resiliency/resiliency-technical-guidance.md) 문서에서는 비즈니스 연속성 및 DR에 대한 Azure의 기본 제공 기능을 설명합니다. [Azure 응용 프로그램에 대한 재해 복구 및 고가용성][Disaster recovery and high availability for Azure applications] 문서는 Azure 응용 프로그램에서 HA 및 DR을 수행하는 전략에 대한 아키텍처 지침을 제공합니다.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
역내 HA 외에도 IoT Hub는 사용자 개입이 필요 없는 재해 복구용 장애 조치(Failover) 메커니즘을 구현합니다. IoT Hub DR는 자체적으로 시작되고 2-26시간의 복구 시간 목표(RTO)과 다음 복구 지점 목표(RPO)를 가집니다.

| 기능 | RPO |
| --- | --- |
| 레지스트리 및 통신 작업에 대 한 서비스 가용성 |CName 손실 가능성 |
| ID 레지스트리의 ID 데이터 |0-5분 데이터 손실 |
| 장치-클라우드 메시지 |읽지 않은 메시지가 모두 손실됨 |
| 작업 모니터링 메시지 |읽지 않은 메시지가 모두 손실됨 |
| 클라우드-장치 메시지 |0-5분 데이터 손실 |
| 클라우드-장치 피드백 큐 |읽지 않은 메시지가 모두 손실됨 |
| 장치 쌍 데이터 |0-5분 데이터 손실 |
| 부모 및 장치 작업 |0-5분 데이터 손실 |

## <a name="regional-failover-with-iot-hub"></a>IoT Hub를 통한 국가별 장애 조치
IoT 솔루션으로 배포 토폴로지를 완벽하게 수행하는 것은 이 문서의 범위를 벗어납니다. 이 문서는 고가용성 및 재해 복구를 목적으로 하는 *국가별 장애 조치* 배포 모델을 설명합니다.

지역적 장애 조치(failover) 모델에서 솔루션 백 엔드는 기본적으로 하나의 데이터센터 위치에서 실행됩니다. 보조 IoT 허브 및 백 엔드는 다른 데이터 센터 위치에 배포됩니다. 기본 데이터센터의 IoT Hub에 중단이 발생하거나 장치에서 기본 데이터센터로의 네트워크 연결이 중단되는 경우, 장치는 보조 서비스 끝점을 사용합니. 단일 지역 내에 머무르지 않고 지역 간 장애 조치(failover) 모델을 구현하여 솔루션 가용성을 향상할 수 있습니다. 

높은 수준에서 IoT Hub로 국가별 장애 조치를 구현하려면 다음이 필요합니다.

* **보조 IoT Hub 및 장치 라우팅 논리**: 주 지역에서 서비스 중단이 발생하는 경우 장치는 보조 지역으로 연결을 시작해야 합니다. 관련된 대부분의 서비스가 상태를 인식하는 특성이 있으므로 일반적으로 솔루션 관리자는 국가 간 장애 조치 프로세스를 트리거합니다. 프로세스에 대한 제어를 유지하면서 새 끝점에서 장치로 통신하는 가장 좋은 방법은 현재 활성 끝점에 대해 *안내자* 서비스를 정기적으로 확인하는 것입니다. 안내자 서비스는 DNS-리디렉션 기술(예: [Azure Traffic Manager][Azure Traffic Manager])을 사용하여 복제되고 연결을 유지할 수 있는 웹 응용 프로그램입니다.
* **ID 레지스트리 복제**: 사용하려면 보조 IoT Hub가 솔루션에 연결할 수 있는 모든 장치 ID를 포함해야 합니다. 솔루션은 지역에서 복제된 장치 ID의 백업을 유지하고 이를 보조 IoT Hub로 업로드한 후 장치에 대한 활성 끝점을 전환해야 합니다. 이 경우 IoT Hub의 장치 ID 내보내기 기능은 유용합니다. 자세한 내용은 [IoT Hub 개발자 가이드 - ID 레지스트리][IoT Hub developer guide - identity registry]를 참조하세요.
* **논리 병합**: 주 지역을 다시 사용할 수 있게 된 경우 보조 사이트에 생성된 모든 상태 및 데이터를 주 지역으로 다시 마이그레이션해야 합니다. 이러한 상태 및 데이터는 주로 장치 ID 및 응용 프로그램 메타데이터와 관련되며 기본 IoT Hub 및 주 지역의 기타 가능한 응용 프로그램별 저장소에 병합되어야 합니다. 이러한 단계를 간소화하려면 멱등 연산을 사용해야 합니다. 멱등 연산은 최후의 일관적인 이벤트 배포 및 중복되거나 비순차적인 이벤트 배달로 인한 부작용을 최소화합니다. 또한 응용 프로그램 논리는 잠재적인 불일치 또는 "약간" 오래된 상태를 허용할 수 있도록 설계되어야 합니다. 이러한 상황은 시스템이 복구 지점 목표(RPO)에 기반하여 "치료"하는 데 추가로 소요되는 시간으로 인해 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure IoT Hub에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

* [IoT Hub 시작(자습서)][lnk-get-started]
* [Azure IoT Hub란?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
