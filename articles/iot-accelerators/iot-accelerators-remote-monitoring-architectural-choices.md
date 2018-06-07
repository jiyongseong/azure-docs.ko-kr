---
title: 원격 모니터링 솔루션 아키텍처 선택 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링에서 이루어지는 아키텍처 및 기술 선택을 설명합니다.
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 607b8aeb2f986eebddf8fe13b88e7f3bc7b4494b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="remote-monitoring-architectural-choices"></a>원격 모니터링 아키텍처 선택

Azure IoT RM(원격 모니터링)은 고객이 개발 프로세스 속도를 높일 수 있도록 장치 연결, 장치 관리 및 스트림 처리와 같은 일반적인 IoT 시나리오를 소개하는 오픈 소스의 MIT 사용이 허가된 솔루션 가속기입니다.  RM은 [여기](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/) 게시된 권장 Azure IoT 참조 아키텍처를 따릅니다.  

이 문서에서는 RM용 각 하위 시스템에서 선택하는 아키텍처 및 기술을 설명하고 고려되는 대안에 대해 논의합니다.  RM에서 선택하는 기술은 원격 모니터링 IoT 솔루션을 구현하는 유일한 방법은 아닙니다.  기술 구현은 성공적인 응용 프로그램을 빌드하기 위한 기준이며 기술, 환경 및 고객 솔루션 구현에 대한 수직적 응용 프로그램 요구에 맞게 수정되어야 합니다.

## <a name="architectural-choices"></a>아키텍처 선택

### <a name="microservices-serverless-and-cloud-native"></a>마이크로 서비스, 서버리스 및 클라우드 네이티브

클라우드 네이티브, 마이크로 서비스 및 서버리스 조건을 충족하는 아키텍처를 IoT 응용 프로그램에 사용하는 것이 좋습니다.  IoT 응용 프로그램의 다른 하위 시스템은 독립적으로 배포 가능하고 독립적으로 확장할 수 있는 개별 서비스로 빌드해야 합니다.  이러한 특성 덕분에 더 크게 확장할 수 있고, 개별 하위 시스템 업데이트 시 유연성이 높아지며, 하위 시스템 기준마다 적합한 기술을 선택할 수 있는 융통성이 제공됩니다.  마이크로 서비스는 여러 기술을 통해 구현할 수 있습니다. 예를 들어 Azure Functions와 같은 서버리스 기술을 통해 Docker와 같은 컨테이너 기술을 사용하거나 Azure App Service와 같은 PaaS 서비스에 마이크로 서비스를 호스트합니다.

## <a name="core-subsystem-technology-choices"></a>핵심 하위 시스템 기술 선택

이 섹션에서는 각 핵심 하위 시스템에 대해 RM에서 선택한 기술을 자세히 설명합니다.

![코어 다이어그램](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>클라우드 게이트웨이
Azure IoT Hub는 RM 클라우드 게이트웨이로 사용됩니다.  IoT Hub는 장치와의 안전한 양방향 통신을 제공합니다. [여기](https://azure.microsoft.com/services/iot-hub/)에서 IoT Hub에 대해 더 자세히 알아볼 수 있습니다. IoT 장치 연결의 경우 .NET Core 및 Java IoT Hub SDK가 사용됩니다.  SDK는 다시 처리와 같은 처리 시나리오 및 IoT Hub REST API에 관한 래퍼를 제공합니다. 

### <a name="stream-processing"></a>스트림 처리
스트림 처리를 위해 RM은 복잡한 규칙 처리에 Azure Stream Analytics를 사용합니다.  단순한 규칙을 원하는 고객을 위해 간단한 규칙의 처리를 지원하는 사용자 지정 마이크로 서비스를 제공합니다. 이 설정은 특별한 배포의 일부는 아닙니다. 참조 아키텍처에서는 간단한 규칙 처리에 Azure Functions를 사용하고 복잡한 규칙 처리에는 ASA(Azure Stream Analytics)를 사용하는 것을 권장합니다.  

### <a name="storage"></a>Storage
저장소의 경우 Cosmos DB는 콜드 저장소, 웜 저장소, 규칙 저장소 및 경보와 같은 모든 저장소 요구에 사용됩니다. 현재 참조 아키텍처에 권장된 대로 Azure Blob Storage로 이동하는 중입니다.  Azure Time Series Insights 및 Azure Data Lake와 같은 솔루션이 많은 사용 사례에 적합하긴 하지만, Cosmos DB는 IoT 응용 프로그램에 권장되는 범용 웜 저장소 솔루션입니다.

### <a name="business-integration"></a>비즈니스 통합
RM의 비즈니스 통합은 웜 저장소에 배치되는 경보의 생성으로 제한됩니다. 추가 비즈니스 통합은 Azure Logic Apps를 사용한 솔루션을 통합하여 수행할 수 있습니다.

### <a name="user-interface"></a>사용자 인터페이스
웹 UI는 JavaScript React로 빌드됩니다.  React는 자주 사용되는 업계 웹 UI 프레임워크를 제공하고 Angular와 같은 다른 인기 있는 프레임워크와 비슷합니다.  

### <a name="runtime-and-orchestration"></a>런타임 및 오케스트레이션
RM에서 하위 시스템 구현을 위해 선택한 응용 프로그램 런타임은 수평적 확장을 위한 오케스트레이터로 Kubernetes(K8s)를 사용하는 Docker 컨테이너입니다.  이 아키텍처는 하위 시스템당 개별 크기 조정 정의를 허용하지만, 보안 측면에서 VM 및 컨테이너를 최신 상태로 유지하기 위한 DevOps 비용이 발생됩니다.  Docker 및 K8s에 대한 대안에는 PaaS 서비스에서 마이크로 서비스를 호스팅하거나(예: Azure App Service), 오케스트레이터로 Service Fabric, DCOS, Swarm 등을 사용하는 것입니다.

## <a name="next-steps"></a>다음 단계
* [여기](https://www.azureiotsuite.com/)에서 RM 솔루션을 배포합니다.
* [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) 및 [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)에서 GitHub 코드를 탐색합니다.  
* [여기](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)에서 IoT 참조 아키텍처에 대해 자세히 알아봅니다.
