---
title: "메트릭을 사용하여 Azure IoT Hub 모니터링 | Microsoft Docs"
description: "Azure IoT Hub 메트릭을 사용하여 IoT Hub의 전반적인 상태를 평가하고 모니터링하는 방법입니다."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cafe5009b8e96b147b5bbed1957024f6d96feb58
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2017
---
# <a name="understand-iot-hub-metrics"></a>IoT Hub 메트릭 이해
IoT Hub 메트릭은 Azure 구독의 Azure IoT 리소스 상태에 대한 더 나은 데이터를 제공합니다. IoT Hub 메트릭을 통해 IoT Hub 서비스와 연결된 장치의 전반적인 상태를 평가할 수 있습니다. 사용자 측 통계는 Azure 지원 센터에 문의할 필요 없이 IoT Hub의 상황을 파악하고 근본 원인을 해결할 수 있기 때문에 중요합니다.

메트릭은 기본적으로 사용하도록 설정됩니다. Azure Portal에서 IoT Hub 메트릭을 볼 수 있습니다.

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub 메트릭을 보는 방법
1. IoT Hub를 만듭니다. IoT Hub를 만드는 방법에 관한 지침은 [시작][lnk-get-started] 가이드에서 확인할 수 있습니다.
2. IoT Hub의 블레이드를 엽니다. 여기서 **메트릭**을 클릭합니다.
   
    ![][1]
3. 메트릭 블레이드에서 IoT Hub에 대한 메트릭을 보고 메트릭의 사용자 지정 뷰를 만들 수 있습니다. **진단 설정**을 클릭하여 Event Hubs 끝점 또는 Azure Storage 계정으로 메트릭 데이터를 보낼 수 있습니다.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub 메트릭 및 사용 방법
IoT Hub는 허브의 상태와 연결된 장치의 총 수에 대한 개요를 제공하는 몇 가지 메트릭을 제공합니다. IoT Hub 상태의 큰 그림을 그리기 위해 여러 가지 메트릭의 정보를 결합할 수 있습니다. 다음 테이블에는 각 IoT Hub가 추적하는 메트릭과 각 메트릭이 IoT hub의 전반적인 상태와 어떤 관련성이 있는지 설명합니다.

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 장치-클라우드 원격 분석 메시지 수|
|d2c.telemetry.ingress.success|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 장치-클라우드 원격 분석 메시지 수|
|c2d.commands.egress.complete.success|완료된 명령|개수|합계|장치에서 성공적으로 완료한 클라우드-장치 명령 수|
|c2d.commands.egress.abandon.success|중단된 명령|개수|합계|장치에서 중단한 클라우드-장치 명령 수|
|c2d.commands.egress.reject.success|거부된 명령|개수|합계|장치에서 거부한 클라우드-장치 명령 수|
|devices.totalDevices|총 장치|개수|합계|IoT 허브에 등록된 장치 수|
|devices.connectedDevices.allProtocol|연결된 장치|개수|합계|IoT 허브에 연결된 장치 수|
|d2c.telemetry.egress.success|배달된 원격 분석 메시지|개수|합계|메시지가 끝점에 성공적으로 작성된 횟수(전체)|
|d2c.telemetry.egress.dropped|삭제된 메시지|개수|합계|어떤 경로에도 일치하지 않고 대체(fallback) 경로가 사용되지 않도록 설정되었으므로 삭제된 메시지 수|
|d2c.telemetry.egress.orphaned|분리된 메시지|개수|합계|대체 경로를 포함하여 경로가 일치하지 않는 메시지 수|
|d2c.telemetry.egress.invalid|잘못된 메시지|개수|합계|끝점과 호환되지 않아서 배달되지 않은 메시지 수|
|d2c.telemetry.egress.fallback|대체(fallback) 조건과 일치하는 메시지|개수|합계|대체(fallback) 끝점에 작성된 메시지 수|
|d2c.endpoints.egress.eventHubs|이벤트 허브 끝점에 배달된 메시지|개수|합계|메시지가 이벤트 허브 끝점에 성공적으로 작성된 횟수|
|d2c.endpoints.latency.eventHubs|이벤트 허브 끝점에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초)|
|d2c.endpoints.egress.serviceBusQueues|Service Bus 큐 끝점에 배달된 메시지|개수|합계|메시지가 Service Bus 큐 끝점에 성공적으로 작성된 횟수|
|d2c.endpoints.latency.serviceBusQueues|Service Bus 큐 끝점에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초)|
|d2c.endpoints.egress.serviceBusTopics|Service Bus 토픽 끝점에 배달된 메시지|개수|합계|메시지가 Service Bus 토픽 끝점에 성공적으로 작성된 횟수|
|d2c.endpoints.latency.serviceBusTopics|Service Bus 항목 끝점에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초)|
|d2c.endpoints.egress.builtIn.events|기본 제공 끝점에 배달된 메시지(메시지/이벤트)|개수|합계|메시지가 기본 제공 끝점에 성공적으로 작성된 횟수(메시지/이벤트)|
|d2c.endpoints.latency.builtIn.events|기본 제공 끝점에 대한 메시지 대기 시간(메시지/이벤트)|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 끝점(메시지.이벤트)에 대한 메시지 수신 간의 평균 대기 시간(밀리초) |
|d2c.twin.read.success|장치에서의 성공한 쌍 읽기|개수|합계|성공한 모든 장치 시작 쌍 읽기 수입니다.|
|d2c.twin.read.failure|장치에서의 실패한 쌍 읽기|개수|합계|실패한 모든 장치 시작 쌍 읽기 수입니다.|
|d2c.twin.read.size|장치에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 장치 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|
|d2c.twin.update.success|장치에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 장치 시작 쌍 업데이트 수입니다.|
|d2c.twin.update.failure|장치에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 장치 시작 쌍 업데이트 수입니다.|
|d2c.twin.update.size|장치에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 장치 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|
|c2d.methods.success|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|
|c2d.methods.failure|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|
|c2d.methods.requestSize|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|
|c2d.methods.responseSize|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|
|c2d.twin.read.success|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|
|c2d.twin.read.failure|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|
|c2d.twin.read.size|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|
|c2d.twin.update.success|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|
|c2d.twin.update.failure|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|
|c2d.twin.update.size|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|
|twinQueries.success|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|
|twinQueries.failure|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|
|twinQueries.resultSize|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|
|jobs.createTwinUpdateJob.success|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|
|jobs.createTwinUpdateJob.failure|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|
|jobs.createDirectMethodJob.success|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|
|jobs.createDirectMethodJob.failure|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|
|jobs.listJobs.success|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|
|jobs.listJobs.failure|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|
|jobs.cancelJob.success|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|
|jobs.cancelJob.failure|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|
|jobs.queryJobs.success|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|
|jobs.queryJobs.failure|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|
|jobs.completed|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|
|jobs.failed|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|

## <a name="next-steps"></a>다음 단계
지금까지 IoT Hub 메트릭의 개요를 살펴보았습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [작업 모니터링][lnk-monitor]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
