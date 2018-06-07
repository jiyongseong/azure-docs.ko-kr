---
title: 원격 모니터링 솔루션의 아키텍처 - Azure | Microsoft Docs
description: 원격 모니터링 솔루션 가속기의 아키텍처 둘러보기.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 3effde81dfa48e9544d89153d40c160ff972d047
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="remote-monitoring-solution-accelerator-architecture"></a>원격 모니터링 솔루션 가속기 아키텍처

원격 모니터링 [솔루션 가속기](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)는 원격 위치에서 여러 컴퓨터에 대한 종단간 모니터링 솔루션을 구현합니다. 솔루션은 비즈니스 시나리오의 제네릭 구현을 제공하는 핵심 Azure 서비스를 결합합니다. 솔루션을 고유한 구현을 위한 시작점으로 사용하고 사용자의 특정 비즈니스 요구 사항에 맞게 [사용자 지정](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)할 수 있습니다.

이 문서는 작동 방식을 이해할 수 있도록 원격 모니터링 솔루션의 핵심 요소 중 일부를 안내합니다. 이 정보는 다음 항목을 도울 수 있습니다.

* 솔루션의 문제를 해결합니다.
* 솔루션을 사용자 지정하여 고유한 특정 요구 사항을 충족하는 방법을 계획합니다.
* Azure 서비스를 사용하는 고유한 IoT 솔루션을 디자인합니다.

## <a name="logical-architecture"></a>논리 아키텍처

다음 다이어그램은 [IoT 아키텍처](../iot-accelerators/iot-accelerators-what-is-azure-iot.md)에 오버레이된 원격 모니터링 솔루션 가속기의 논리적 구성 요소에 대해 설명합니다.

![논리 아키텍처](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>마이크로 서비스를 사용하는 이유는 무엇인가요?

클라우드 아키텍처는 Microsoft가 솔루션 가속기를 처음 발표한 이후 발전해 왔습니다. [마이크로 서비스](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)는 개발 속도를 저하시키지 않으면서 규모와 유연성을 달성하는 입증된 방법으로 부상했습니다. 일부 Microsoft 서비스는 뛰어난 안정성과 확장성을 제공하는 아키텍처 패턴을 내부적으로 사용합니다. 업데이트된 솔루션 가속기를 통해 혜택을 볼 수 있도록 이러한 내용을 실제로 활용할 수 있습니다.

> [!TIP]
> 마이크로 서비스 아키텍처에 대한 자세한 내용은 [.NET 응용 프로그램 아키텍처](https://www.microsoft.com/net/learn/architecture) 및 [마이크로 서비스: 클라우드에서 제공하는 응용 프로그램 혁명](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)을 참조하세요.

## <a name="device-connectivity"></a>장치 연결

이 솔루션은 논리적 구조의 장치 연결 부분에 다음과 같은 구성 요소를 포함합니다.

### <a name="simulated-devices"></a>시뮬레이션된 장치

이 솔루션에는 솔루션의 종단 간 흐름을 테스트하기 위해 시뮬레이션된 장치 풀을 관리할 수 있는 마이크로 서비스가 포함되어 있습니다. 시뮬레이션된 장치는:

* 장치-클라우드 원격 분석을 생성합니다.
* IoT Hub의 클라우드-장치 메서드 호출에 응답합니다.

마이크로 서비스는 시뮬레이션을 생성, 시작 및 중지할 수 있는 RESTful 끝점을 제공합니다. 각 시뮬레이션은 원격 분석을 보내고 메서드 호출에 응답하는 다양한 유형의 가상 장치 집합으로 구성됩니다.

시뮬레이션된 장치를 솔루션 포털의 대시보드에서 프로비전할 수 있습니다.

### <a name="physical-devices"></a>물리적 장치

물리적 장치를 솔루션에 연결할 수 있습니다. Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치의 동작을 구현할 수 있습니다.

물리적 장치를 솔루션 포털의 대시보드에서 프로비전할 수 있습니다.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub 및 IoT 관리자 마이크로 서비스

[IoT Hub](../iot-hub/index.yml)는 장치에서 클라우드로 전송된 데이터를 수집하고 `telemetry-agent` 마이크로 서비스에서 사용할 수 있도록 합니다.

또한 솔루션에서 IoT Hub는:

* 포털에 연결하도록 허용되는 모든 장치의 ID 및 인증 키를 저장하는 ID 레지스트리를 유지 관리합니다. ID 레지스트리를 통해 장치를 활성화 및 비활성화할 수 있습니다.
* 솔루션 포털을 대신하여 장치에서 메서드를 호출합니다.
* 등록된 모든 장치에 대한 장치 쌍을 유지 관리합니다. 장치 쌍은 장치에서 보고한 속성 값을 저장합니다. 또한 장치 쌍은 다음에 연결할 때 검색하는 장치에 대해 솔루션 포털에서 설정한 desired 속성을 저장합니다.
* 여러 장치에 대한 속성을 설정하거나 여러 장치에서 메서드를 호출하는 작업을 예약합니다.

솔루션에는 다음과 같이 IoT Hub와의 상호 작용을 처리하기 위한 `iot-manager` 마이크로 서비스가 포함됩니다.

* IoT 장치 만들기 및 관리.
* 장치 쌍 관리.
* 장치에 대한 메서드 호출.
* IoT 자격 증명 관리.

또한 이 서비스는 IoT Hub 쿼리를 실행하여 사용자 정의 그룹에 속하는 장치를 검색합니다.

마이크로 서비스는 장치와 장치 쌍을 관리하고 메서드를 호출하며 IoT Hub 쿼리를 실행하는 RESTful 끝점을 제공합니다.

## <a name="data-processing-and-analytics"></a>데이터 처리 및 분석

이 솔루션은 논리적 구조의 데이터 처리 및 분석 부분에 다음과 같은 구성 요소를 포함합니다.

### <a name="device-telemetry"></a>장치 원격 분석

이 솔루션에는 장치 원격 분석을 처리하는 마이크로 서비스가 2개 포함됩니다.

[telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) 마이크로 서비스:

* Cosmos DB에 원격 분석 데이터를 저장합니다.
* 장치에서 원격 분석 스트림을 분석합니다.
* 정의된 규칙에 따라 알람을 생성합니다.

알람은 Cosmos DB에 저장됩니다.

`telemetry-agent` 마이크로 서비스를 통해 솔루션 포털은 장치에서 전송된 원격 분석 데이터를 읽을 수 있습니다. 또한 솔루션 포털은 이 서비스를 사용하여 다음을 수행합니다.

* 알람을 트리거하는 임계값과 같은 모니터링 규칙을 정의합니다.
* 지난 알람 목록을 검색합니다.

마이크로 서비스에 제공되는 RESTful 끝점을 사용하여 원격 분석, 규칙 및 알람을 관리합니다.

### <a name="storage"></a>Storage

[storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) 마이크로 서비스는 솔루션 가속기에 사용되는 기본 저장소 서비스 앞에 있는 어댑터입니다. 간단한 컬렉션 및 키-값 저장소를 제공합니다.

솔루션 가속기의 표준 배포는 Cosmos DB를 기본 저장소 서비스로 사용합니다.

Cosmos DB 데이터베이스는 솔루션 가속기에 데이터를 저장합니다. **storage-adapter** 마이크로 서비스는 저장소 서비스에 액세스하는 솔루션의 다른 마이크로 서비스에 대한 어댑터 역할을 합니다.

## <a name="presentation"></a>프레젠테이션

이 솔루션은 논리적 구조의 프레젠테이션 부분에 다음과 같은 구성 요소를 포함합니다.

[웹 사용자 인터페이스는 React Javascript 응용 프로그램입니다](https://github.com/Azure/pcs-remote-monitoring-webui). 이 응용 프로그램은:

* Javascript React만 사용하고 브라우저에서 완전히 실행됩니다.
* CSS 스타일이 적용됩니다.
* AJAX 호출을 통해 공용 마이크로 서비스와 상호 작용합니다.

사용자 인터페이스는 모든 솔루션 가속기 기능을 제공하고 다음과 같은 다른 서비스와 상호 작용합니다.

* 사용자 데이터를 보호하는 [인증](https://github.com/Azure/pcs-auth-dotnet) 마이크로 서비스.
* IoT 장치를 나열 및 관리하는 [iothub-관리자](https://github.com/Azure/iothub-manager-dotnet) 마이크로 서비스.

[ui-config](https://github.com/Azure/pcs-config-dotnet) 마이크로 서비스를 사용하면 사용자 인터페이스로 구성 설정을 저장 및 검색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

소스 코드와 개발자 문서를 탐색하려면 다음 두 가지 주요 GitHub 리포지토리 중 하나를 시작하십시오.

* [Azure IoT(.NET)로 원격 모니터링을 위한 솔루션 가속기](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/)
* [Azure IoT(Java)로 원격 모니터링을 위한 솔루션 가속기](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [원격 모니터링 아키텍처를 위한 솔루션 가속기](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [솔루션 가속기 사용자 지정](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)을 참조하세요.
