---
title: Azure IoT Hub 작업 이해 | Microsoft 문서
description: 개발자 가이드 - IoT 허브에 연결된 여러 장치에서 실행할 작업 예약 작업은 태그와 원하는 속성을 업데이트하고 여러 장치에서 직접 메서드를 호출할 수 있습니다.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 367eb703465b2fbc6f1e06a383bc9df709cabe78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-jobs-on-multiple-devices"></a>여러 장치에서 작업 예약

Azure IoT Hub를 통해 [장치 쌍 속성 및 태그][lnk-twin-devguide] 및 [직접 메서드][lnk-dev-methods]와 같은 다양한 구성 요소를 사용할 수 있습니다.  일반적으로, 백 엔드 앱을 사용하면 장치 관리자와 운영자는 IoT 장치를 대량으로 예약된 시간에 업데이트하고 상호 작용할 수 있습니다.  작업은 예약된 시간에 장치 집합에 대해 장치 쌍 업데이트 및 직접 메서드를 실행합니다.  예를 들어 운영자는 빌딩 운영에 지장을 주지 않는 시간에 빌딩 43 및 3층에서 장치 집합을 재부팅하기 위해 작업을 시작 및 추적하는 백 엔드 앱을 사용합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

장치 집합에서 다음 작업을 예약하고 진행 상태를 추적해야 하는 경우 작업을 사용하는 것이 좋습니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

## <a name="job-lifecycle"></a>작업 수명 주기
작업은 솔루션 백 엔드에 의해 시작되고 IoT Hub에 의해 유지 관리됩니다.  서비스 지향 URI(`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`)를 통해 작업을 시작하고 서비스 지향 URI(`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`)를 통해 작업 실행 진행 상태를 쿼리할 수 있습니다. 작업이 시작된 후 실행 중인 작업의 상태를 새로 고치기 위해 작업 쿼리를 실행합니다.

> [!NOTE]
> 작업을 시작할 때 속성 이름과 값은 US-ASCII로 출력 가능한 영숫자만 포함할 수 있으며 다음 집합은 제외됩니다. `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>직접 메서드를 실행할 작업
다음 코드 조각은 작업을 사용하여 장치 집합에서 [직접 메서드][lnk-dev-methods]를 실행하기 위한 HTTPS 1.1 요청 세부 정보를 나타냅니다.

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

쿼리 조건은 다음 예제와 같이 단일 장치 ID 또는 장치 ID 목록에 있을 수도 있습니다.

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[IoT Hub 쿼리 언어][lnk-query]는 추가 세부 정보에서 IoT Hub 쿼리 언어를 설명합니다.

## <a name="jobs-to-update-device-twin-properties"></a>장치 쌍 속성을 업데이트하는 작업
다음 코드 조각은 작업을 사용하여 장치 쌍 속성을 업데이트하는 HTTPS 1.1 요청 세부 정보를 나타냅니다.

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>작업 진행 상태 쿼리
다음 코드 조각은 [작업 쿼리][lnk-query]를 위한 HTTPS 1.1 요청 세부 정보를 나타냅니다.

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

응답에서 continuationToken이 제공됩니다.  

## <a name="jobs-properties"></a>작업 속성
다음 목록은 작업 또는 작업 결과를 쿼리할 때 사용할 수 있는 속성 목록 및 해당 설명을 나타냅니다.

| 자산 | 설명 |
| --- | --- |
| **jobId** |작업에 대해 응용 프로그램에서 제공한 ID입니다. |
| **startTime** |작업에 대해 응용 프로그램에서 제공한 시작 시간(ISO-8601)입니다. |
| **endTime** |작업이 완료될 때 IoT Hub에서 제공한 날짜(ISO-8601)입니다. 작업이 '완료됨' 상태에 도달한 후에만 유효합니다. |
| **type** |작업 형식: |
| | **scheduledUpdateTwin**: desired 속성 또는 태그 집합을 업데이트하는 데 사용되는 작업입니다. |
| | **scheduledDeviceMethod**: 장치 쌍 집합에서 장치 메서드를 호출하는 데 사용되는 작업입니다. |
| **상태** |작업의 현재 상태입니다. 가능한 상태 값: |
| | **보류 중** : 예약되어 작업 서비스에서 선택되기를 기다립니다. |
| | **예약됨** : 이후 시간에 예약됩니다. |
| | **실행 중** : 현재 활성 상태의 작업입니다. |
| | **취소**: 작업이 취소되었습니다. |
| | **실패함** : 작업이 실패했습니다. |
| | **완료됨** : 작업이 완료되었습니다. |
| **deviceJobStatistics** |작업 실행에 대한 통계입니다. |
| | **deviceJobStatistics** 속성: |
| | **deviceJobStatistics.deviceCount**: 작업의 장치 수입니다. |
| | **deviceJobStatistics.failedCount**: 작업이 실패한 장치 수입니다. |
| | **deviceJobStatistics.succeededCount**: 작업이 성공한 장치 수입니다. |
| | **deviceJobStatistics.runningCount**: 현재 작업을 실행 중인 장치 수입니다. |
| | **deviceJobStatistics.pendingCount**: 작업 실행이 보류 중인 장치 수입니다. |

### <a name="additional-reference-material"></a>추가 참조 자료
이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-endpoints] - 각 IoT Hub에서 런타임 및 관리 작업에 대해 공개하는 다양한 끝점에 대해 설명합니다.
* [제한 및 할당량][lnk-quotas] - IoT Hub 서비스에 적용되는 할당량과 서비스를 사용할 때 예상되는 제한 동작에 대해 설명합니다.
* [Azure IoT 장치 및 서비스 SDK][lnk-sdks] - IoT Hub와 상호 작용하는 장치 및 서비스 앱 모두를 개발할 때 사용할 수 있는 다양한 언어 SDK를 나열합니다.
* [장치 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어][lnk-query]에서는 IoT Hub 쿼리 언어를 설명합니다. 이 쿼리 언어를 사용하여 IoT Hub에서 장치 쌍 및 작업에 대한 정보를 검색합니다.
* [IoT Hub MQTT 지원][lnk-devguide-mqtt] - MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 참조하세요.

* [작업 예약 및 브로드캐스트][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
