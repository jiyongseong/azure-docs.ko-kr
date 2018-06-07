---
title: 미리 구성된 Azure IoT 솔루션 | Microsoft Docs
description: 미리 구성된 Azure IoT 솔루션에 대한 솔루션 및 추가 리소스에 대한 링크와 해당 아키텍처에 대한 설명입니다.
services: ''
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d860c768a73737e6c8c52a8652d6b43434a3a07d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361991"
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>미리 구성된 Azure IoT Suite 솔루션은 무엇입니까?

미리 구성된 Azure IoT Suite 솔루션은 구독을 사용하여 Azure에 배포할 수 있는 일반적인 IoT 솔루션 패턴의 구현입니다. 미리 구성된 솔루션을 다음으로 사용할 수 있습니다.

* 사용자 고유의 IoT 솔루션에 대한 시작점.
* IoT 솔루션 설계 및 개발에서 일반적인 패턴에 대해 알아보려면

미리 구성된 각 솔루션은 시뮬레이션된 장치를 사용하여 원격 분석을 생성하는 완전한 종단 간 구현입니다.

전체 소스 코드를 다운로드하여 특정 IoT 요구 사항에 맞게 솔루션을 사용자 지정 및 확장할 수 있습니다.

> [!NOTE]
> 미리 구성된 솔루션 중 하나를 배포하려면 [Microsoft Azure IoT Suite][lnk-azureiotsuite]를 방문합니다. [미리 구성된 IoT 솔루션 시작][lnk-getstarted-preconfigured] 문서에서는 솔루션 중 하나를 배포 및 실행하는 방법에 대한 정보를 제공합니다.

다음 표에서 솔루션을 특정 IoT 기능에 매핑하는 방법을 보여 줍니다.

| 해결 방법 | 데이터 수집 | 장치 ID | 장치 관리 | 명령 및 컨트롤 | 규칙 및 동작 | 예측 분석 |
| --- | --- | --- | --- | --- | --- | --- |
| [원격 모니터링][lnk-getstarted-preconfigured] |예 |예 |예 |예 |예 |- |
| [예측 유지 관리][lnk-predictive-maintenance] |예 |예 |- |예 |예 |예 |
| [연결된 공장][lnk-getstarted-factory] |예 |예 |예 |예 |예 |- |

* *데이터 수집*: 클라우드에 대한 대규모 데이터의 수신입니다.
* *장치 ID*: 고유한 장치 ID를 관리하고 솔루션에 대한 장치 액세스를 제어합니다.
* *장치 관리*: 장치 메타데이터를 관리하고 장치 재부팅 및 펌웨어 업그레이드 등의 작업을 수행합니다.
* *명령 및 컨트롤*: 장치가 작업을 수행하도록 하기 위해 클라우드에서 장치에 메시지를 보냅니다.
* *규칙 및 동작*: 특정 장치-클라우드 데이터에서 작동하도록 하기 위해 솔루션 백 엔드가 규칙을 사용합니다.
* *예측 분석*: 솔루션 백 엔드는 장치-클라우드 데이터를 분석하여 특정 작업이 수행될 때를 예측합니다. 예를 들어 항공기 엔진 원격 분석을 분석하여 엔진 유지 관리가 만료될 때를 확인합니다.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>미리 구성된 원격 모니터링 솔루션 개요

이 문서에서는 다른 솔루션에서 공유하는 여러 가지 일반적인 디자인 요소를 보여주는 미리 구성된 원격 모니터링 솔루션에 대해 설명합니다.

다음 다이어그램에서는 원격 모니터링 솔루션의 핵심 요소를 보여줍니다. 다음 섹션에서는 이러한 요소에 대한 자세한 정보를 제공합니다.

![미리 구성된 원격 모니터링 솔루션 아키텍처][img-remote-monitoring-arch]

## <a name="devices"></a>장치

원격 모니터링 미리 구성 된 솔루션을 배포하는 경우, 4개의 시뮬레이션된 장치가 냉각 장치를 시뮬레이션하는 솔루션에 미리 프로비전됩니다. 이러한 시뮬레이션된 장치에는 원격 분석을 내보내는 기본 제공 온도 및 습도 모델이 있습니다. 이러한 시뮬레이션된 장치에 다음이 포함되어 있습니다.

- 솔루션을 통해 데이터의 종단 간 흐름을 보여 줍니다.
- 원격 분석의 편리한 원본을 제공합니다.
- 사용자 지정 구현을 위해 시작 지점으로 솔루션을 사용하는 백 엔드 개발자인 경우 메서드 또는 명령에 대한 대상을 제공합니다.

솔루션에서 시뮬레이션된 장치는 다음과 같은 클라우드-장치 통신에 응답할 수 있습니다.

- *메서드([직접 메서드][lnk-direct-methods])*: 연결된 장치가 즉시 응답하도록 예상되는 양방향 통신 메서드입니다.
- *명령(클라우드-장치 메시지)*: 장치가 지속형 큐에서 명령을 검색하는 단방향 통신 메서드입니다.

서로 다른 방법에 대한 비교는 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.

장치를 미리 구성된 솔루션에 있는 IoT Hub에 처음으로 연결하는 경우, 허브에 장치 정보 메시지를 보냅니다. 이 메시지는 장치가 응답할 수 메서드를 열거합니다. 미리 구성된 원격 모니터링 솔루션에서 시뮬레이션된 장치는 이러한 메서드를 지원합니다.

* *펌웨어 업데이트 시작*: 이 메서드는 장치에서 비동기 작업을 시작하여 펌웨어 업데이트를 수행합니다. 비동기 작업은 reported 속성을 사용하여 솔루션 대시보드에 상태 업데이트를 제공합니다.
* *다시 부팅*: 이 메서드를 사용하면 시뮬레이션된 장치가 다시 부팅합니다.
* *FactoryReset*: 이 메서드는 시뮬레이션된 장치에서 공장 재설정을 트리거합니다.

장치를 미리 구성된 솔루션에 있는 IoT Hub에 처음으로 연결하는 경우, 허브에 장치 정보 메시지를 보냅니다. 이 메시지는 장치가 응답할 수 명령을 열거합니다. 미리 구성된 원격 모니터링 솔루션에서 시뮬레이션된 장치는 이러한 명령을 지원합니다.

* *Ping 장치*: 장치가 승인을 사용하여 이 명령에 응답합니다. 이 명령은 장치가 여전히 활성 상태로 수신하고 있는지 확인하는 데 유용합니다.
* *원격 분석 시작*: 장치가 원격 분석 보내기를 시작하도록 지시합니다.
* *원격 분석 중지*: 장치가 원격 분석 보내기를 중지하도록 지시합니다.
* *설정 지점 온도 변경*: 장치가 전송하는 시뮬레이션된 온도 원격 분석 값을 제어합니다. 이 명령은 백 엔드 논리를 테스트하는 데 유용합니다.
* *진단 원격 분석*: 장치가 외부 온도를 원격 분석으로 보내야 할지 여부를 제어합니다.
* *장치 상태 변경*.: 장치가 보고하는 장치 상태 메타데이터 속성을 설정합니다. 이 명령은 백 엔드 논리를 테스트하는 데 유용합니다.

동일한 원격 분석을 내보내고 동일한 메서드 및 명령에 응답하는 솔루션에 시뮬레이션된 장치를 더 추가할 수 있습니다.

명령 및 메서드에 응답하는 것 외에도 솔루션은 [장치 쌍][lnk-device-twin]을 사용합니다. 장치는 장치 쌍을 사용하여 솔루션 백 엔드에 속성 값을 보고합니다. 솔루션 대시보드는 장치 쌍을 사용하여 장치에 새로운 desired 속성 값을 설정합니다. 예를 들어 펌웨어 업데이트 프로세스 동안 시뮬레이션된 장치는 reported 속성을 사용하여 업데이트의 상태를 보고합니다.

## <a name="iot-hub"></a>IoT Hub

미리 구성된 솔루션에서 IoT Hub 인스턴스는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *클라우드 게이트웨이*에 해당됩니다.

IoT hub는 단일 끝점의 장치에서 원격 분석을 수신합니다. 또한 IoT hub는 각 장치가 보내 명령을 검색할 수 있는 장치 특정 끝점을 관리합니다.

IoT hub를 사용하면 서비스 쪽 원격 분석 읽기 끝점을 통해 수신한 원격 분석을 사용할 수 있습니다.

IoT Hub의 장치 관리 기능을 통해 다음과 같은 작업을 수행하는 솔루션 포털 및 일정 작업에서 장치 속성을 관리할 수 있습니다.

- 장치 재부팅
- 장치 상태 변경
- 펌웨어 업데이트

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

미리 구성된 솔루션은 세 가지 [Azure Stream Analytics][lnk-asa](ASA) 작업을 사용하여 장치에서 원격 분석 스트림을 필터링합니다.

* *DeviceInfo 작업* - 솔루션 장치 레지스트리에 장치 등록 특정 메시지를 라우팅하는 이벤트 허브에 데이터를 출력합니다. 이 장치 레지스트리는 Azure Cosmos DB 데이터베이스입니다. 장치를 처음으로 연결하거나 **장치 상태 변경** 명령에 대한 응답으로 이 메시지가 전송됩니다.
* *원격 분석 작업* - 콜드 저장소용 Azure Blob Storage에 모든 원시 원격 분석을 보내고 솔루션 대시보드에 표시하는 원격 분석 집계를 계산합니다.
* *규칙 작업* - 규칙 임계값을 초과하는 값에 대해 원격 분석 스트림을 필터링하고 이벤트 허브에 대한 데이터를 출력합니다. 규칙이 실행되면 솔루션 포털 대시보드 뷰는 경보 기록 테이블에 새 행으로 이 이벤트를 표시합니다. 이러한 규칙은 솔루션 포털의 **규칙** 및 **작업** 뷰에서 정의된 설정에 따라 작업을 트리거할 수도 있습니다.

미리 구성된 솔루션에서 ASA 작업은 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]에서 **IoT 솔루션 백 엔드**의 일부를 형성합니다.

## <a name="event-processor"></a>이벤트 프로세서

미리 구성된 솔루션에서 이벤트 프로세서는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]에서 **IoT 솔루션 백 엔드**의 일부를 형성합니다.

**DeviceInfo** 및 **규칙** ASA 작업은 다른 백 엔드 서비스에 배달하기 위해 Event Hubs에 해당 출력을 보냅니다. 솔루션은 [WebJob][lnk-web-job]에서 실행 중인 [EventProcessorHost][lnk-event-processor] 인스턴스를 사용하여 이러한 Event hubs에서 메시지를 읽습니다. **EventProcessorHost**는 다음을 사용합니다.
- Cosmos DB 데이터베이스에서 장치 데이터를 업데이트하는 **DeviceInfo** 데이터.
- 솔루션 포털에서 논리 앱을 호출하고 경고 표시를 업데이트하는 **규칙** 데이터.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>장치 ID 레지스트리, 장치 쌍 및 Cosmos DB

모든 IoT Hub는 장치 키를 저장하는 [장치 ID 레지스트리][lnk-identity-registry]를 포함합니다. IoT Hub는 이 정보를 사용하여 장치를 인증하며 허브에 연결하려면 장치를 등록해야 하고 유효한 키가 있어야 합니다.

[장치 쌍][lnk-device-twin]은 IoT Hub에서 관리하는 JSON 문서입니다. 장치에 대한 장치 쌍은 다음을 포함합니다.

- 장치에서 허브에 보낸 Reported 속성. 솔루션 포털에서 이러한 속성을 볼 수 있습니다.
- 장치에 전송하려는 Desired 속성. 솔루션 포털에서 이러한 속성을 설정할 수 있습니다.
- 장치가 아닌 장치 쌍에만 존재하는 태그. 이러한 태그를 사용하여 솔루션 포털에서 장치 목록을 필터링할 수 있습니다.

이 솔루션은 장치 쌍을 사용하여 장치 메타데이터를 관리합니다. 또한 솔루션은 Cosmos DB 데이터베이스를 사용하여 각 장치 및 명령 기록에서 지원하는 명령과 같은 추가 솔루션 특정 장치 데이터를 저장합니다.

또한 솔루션은 Cosmos DB 데이터베이스의 콘텐츠로 동기화된 장치 ID 레지스트리에 정보를 보관해야 합니다. **EventProcessorHost**는 **DeviceInfo** Stream Analytics 작업의 데이터를 사용하여 동기화를 관리합니다.

## <a name="solution-portal"></a>솔루션 포털

![솔루션 포털][img-dashboard]

솔루션 포털은 미리 구성된 솔루션의 일부로 클라우드에 배포된 웹 기반 UI입니다. 다음을 수행할 수 있습니다.

* 대시보드에서 원격 분석 및 경보 기록을 봅니다.
* 새 장치를 프로비전합니다.
* 장치를 관리 및 모니터링합니다.
* 특정 장치에 명령을 보냅니다.
* 특정 장치에서 메서드를 호출합니다.
* 규칙 및 작업을 관리합니다.
* 하나 이상의 장치에서 실행할 작업을 예약합니다.

미리 구성된 솔루션에서 솔루션 포털은 **IoT 솔루션 백 엔드**의 일부와 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]에서 **프로세스 및 비즈니스 연결**의 일부를 형성합니다.

## <a name="next-steps"></a>다음 단계

IoT 솔루션 아키텍처에 대한 자세한 내용은 [Microsoft Azure IoT 서비스: 참조 아키텍처][lnk-refarch]를 참조하세요.

이제 미리 구성된 솔루션을 알 수 있으므로 미리 구성된 *원격 모니터링* 솔루션: [미리 구성된 솔루션 시작][lnk-getstarted-preconfigured]을 배포하여 시작할 수 있습니다.

[img-remote-monitoring-arch]: ./media/iot-suite-v1-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-v1-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]:../iot-accelerators/iot-accelerators-connected-factory-overview.md
