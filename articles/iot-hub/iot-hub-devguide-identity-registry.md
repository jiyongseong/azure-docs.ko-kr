---
title: Azure IoT Hub ID 레지스트리 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub ID 레지스트리 및 장치 관리에 레지스트리를 사용하는 방법 설명 대량으로 장치 ID 가져오기 및 내보내기에 대한 정보를 포함합니다.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dce0114c19f6153d2cba789cdc00920d42834b2d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>IoT Hub의 ID 레지스트리 이해

모든 IoT Hub에는 IoT Hub에 연결이 허용된 장치 및 모듈에 대한 정보를 저장하는 ID 레지스트리가 있습니다. 장치 또는 모듈을 IoT Hub에 연결할 수 있으려면 IoT Hub의 ID 레지스트리에 해당 장치 또는 모듈에 대한 항목이 있어야 합니다. 또한 장치 또는 모듈은 ID 레지스트리에 저장된 자격 증명에 따라 IoT Hub로 인증되어야 합니다.

ID 레지스트리에 저장된 장치 또는 모듈 ID는 대/소문자를 구분합니다.

높은 수준에서 ID 레지스트리는 장치 또는 모듈 ID 리소스의 REST를 지원하는 컬렉션입니다. ID 레지스트리에 항목을 추가하면 IoT Hub가 진행 중인 클라우드-장치 메시지를 포함하는 큐 등의 장치별 리소스 집합을 만듭니다.

다음과 같은 작업이 필요할 때 ID 레지스트리를 사용합니다.

* IoT Hub에 연결되는 장치 또는 모듈을 프로비전합니다.
* 허브의 장치 또는 모듈 지향 끝점에 대한 장치별/모듈별 액세스를 제어합니다.

> [!NOTE]
> ID 레지스트리에는 응용 프로그램별 메타데이터가 없습니다.

## <a name="identity-registry-operations"></a>ID 레지스트리 작업

IoT Hub ID 레지스트리는 다음과 같은 작업을 노출합니다.

* 장치 또는 모듈 ID 만들기
* 장치 또는 모듈 ID 업데이트
* 장치 또는 모듈 ID를 ID별로 검색
* 장치 또는 모듈 ID 삭제
* 최대 1000개의 ID 목록
> 모듈 ID 및 모듈 쌍은 공개 미리 보기로 제공됩니다. 아래 기능은 일반 공급 시 모듈 ID에 대해 지원됩니다.
* 장치 ID를 Azure Blob Storage로 내보내기
* Azure Blob 저장소에서 장치 ID 가져오기

이러한 모든 작업은 [RFC7232][lnk-rfc7232]에 지정된 낙관적 동시성을 사용할 수 있습니다.

> [!IMPORTANT]
> IoT Hub의 ID 레지스트리에서 모든 ID를 검색하는 유일한 방법은 [내보내기][lnk-export] 기능을 사용하는 것입니다.

IoT Hub ID 레지스트리:

* 응용 프로그램 메타 데이터가 없습니다.
* **deviceId** 또는 **moduleId**를 키로 사용하여 사전처럼 액세스할 수 있습니다.
* 표현 쿼리를 지원하지 않습니다.

IoT 솔루션에는 일반적으로 응용 프로그램 관련 메타데이터가 포함된 별도의 솔루션 관련 저장소가 있습니다. 예를 들어 스마트 건물 솔루션의 솔루션 관련 저장소는 온도 센서가 배포된 방을 기록합니다.

> [!IMPORTANT]
> 장치 관리 및 프로비전 작업에 대해서는 ID 레지스트리만 사용합니다. 런타임 시 처리량이 높은 작업은 ID 레지스트리에서 수행하는 작업에 따라 달라지지 합니다. 예를 들어 명령을 보내기 전에 장치의 연결 상태를 검사하는 것은 지원되는 패턴이 아닙니다. ID 레지스트리에 대한 [제한 속도][lnk-quotas]와 [장치 하트비트][lnk-guidance-heartbeat] 패턴을 확인해야 합니다.

## <a name="disable-devices"></a>장치 비활성화

ID 레지스트리에서 ID의 **상태** 속성을 업데이트하여 장치를 사용하지 않을 수 있습니다. 일반적으로 이 속성은 두 가지 시나리오에 사용합니다.

* 오케스트레이션 과정을 프로비전하는 동안입니다. 자세한 내용은 [장치 프로비전][lnk-guidance-provisioning]을 참조하세요.
* 어떤 이유로든 손상되거나 권한이 없는 장치를 사용하도록 고려합니다.

이 기능을 모듈에는 사용할 수 없습니다.

## <a name="import-and-export-device-identities"></a>장치 ID 가져오기 및 내보내기

[IoT Hub 리소스 공급자 끝점][lnk-endpoints]에서 비동기 작업을 사용하여 IoT Hub의 ID 레지스트리에서 장치 ID를 대량으로 내보낼 수 있습니다. 내보내기는 고객이 제공한 Blob 컨테이너를 사용하여 ID 레지스터에서 읽은 장치 ID 데이터를 저장하는 장기 실행 작업입니다.

[IoT Hub 리소스 공급자 끝점][lnk-endpoints]에서 비동기 작업을 사용하여 IoT Hub의 ID 레지스트리로 장치 ID를 대량으로 가져올 수 있습니다. 가져오기는 고객이 제공한 Blob 컨테이너의 데이터를 사용하여 장치 ID 데이터를 ID 레지스트리에 쓰는 장기 실행 작업입니다.

API를 가져오고 내보내는 작업에 대한 자세한 정보는 [IoT Hub 리소스 공급자 REST API][lnk-resource-provider-apis]를 참조하세요. 가져오기 및 내보내기 작업 실행에 대한 자세한 내용은 [IoT Hub 장치 ID의 대량 관리][lnk-bulk-identity]를 참조하세요.

## <a name="device-provisioning"></a>장치 프로비전

지정된 IoT 솔루션이 저장하는 장치 데이터는 해당 솔루션의 요구 사항에 따라 다릅니다. 하지만 어떤 솔루션이든 최소한 장치 ID와 인증 키를 저장해야 합니다. Azure IoT Hub는 ID, 인증 키 및 상태 코드와 같은 각 장치에 대한 값을 저장할 수 있는 ID 레지스트리를 포함합니다. 솔루션은 Table Storage, Blob Storage 또는 Cosmos DB와 같은 기타 Azure 서비스를 사용하여 추가 장치 데이터를 저장할 수 있습니다.

*장치 프로비전* 은 솔루션 저장소에 초기 장치 데이터를 추가하는 프로세스입니다. 새 장치를 허브에 연결하도록 하려면 장치 ID 및 키를 IoT Hub ID 레지스트리에 추가해야 합니다. 프로비전 프로세스의 일부로, 다른 솔루션 저장소에서 장치 특정 데이터를 초기화해야 할 수 있습니다. 또한 Azure IoT Hub Device Provisioning 서비스를 사용하여 사용자 개입 없이, 하나 이상의 IoT Hub에 대해 무인 Just-In-Time 프로비저닝을 수행할 수도 있습니다. 자세한 내용은 [프로비저닝 서비스 설명서][lnk-dps]를 참조하세요.

## <a name="device-heartbeat"></a>장치 하트비트

IoT Hub ID 레지스트리는 **connectionState**라는 필드를 포함합니다. 개발 및 디버깅하는 동안 **connectionState** 필드만 사용합니다. IoT 솔루션은 런타임에 필드를 쿼리하면 안 됩니다. 예를 들어 클라우드-장치 메시지 또는 SMS를 보내기 전에 장치가 연결되었는지 확인하기 위해 **connectionState** 필드를 쿼리하지 마세요.

IoT 솔루션에서 장치가 연결되었는지 여부를 알아야 하는 경우 *하트비트 패턴*을 구현할 수 있습니다.

하트비트 패턴에서 장치는 정해진 시간에 최소 한 번(예: 1시간마다 최소 한 번) 장치-클라우드 메시지를 보냅니다. 따라서 장치가 보낼 데이터가 없는 경우 빈 장치-클라우드 메시지(이를 하트비트로 식별하는 속성을 가짐)라도 보낸다는 의미입니다. 서비스 쪽에서 솔루션은 각 장치에 대해 받은 마지막 하트비트와 함께 맵을 유지 관리합니다. 솔루션은 예상된 시간 내에 장치에서 보낸 하트비트 메시지를 받지 않을 경우 장치에 문제가 있다고 가정합니다.

좀 더 복잡한 구현에서는 연결 또는 통신을 시도했지만 실패한 장치를 식별하기 위해 [작업 모니터링][lnk-devguide-opmon] 정보를 포함할 수 있습니다. 하트비트 패턴을 구현하는 경우 [IoT Hub 할당량 및 제한][lnk-quotas]을 확인해야 합니다.

> [!NOTE]
> IoT 솔루션에서 연결 상태만 사용하여 클라우드-장치 메시지를 보낼지 여부를 결정해야 하고 메시지가 큰 장치 집합으로 브로드캐스트되지 않는 경우 단순한 *짧은 만료 시간* 패턴 사용을 고려하세요. 이렇게 하면 이 패턴은 더 효율적으로 유지되면서 하트비트 패턴을 사용하여 장치 연결 상태 레지스트리를 유지 관리하는 것과 동일한 결과를 얻을 수 있습니다. 메시지 승인을 요청하면 IoT Hub는 메시지를 수신할 수 있는 장치와 수신할 수 없는 장치에 대해 알릴 수 있습니다.

## <a name="device-and-module-lifecycle-notifications"></a>장치 및 모듈 수명 주기 알림

ID가 생성 또는 삭제되면 IoT Hub에서 수명 주기 알림을 전송하여 IoT 솔루션에 알릴 수 있습니다. 이를 수행하려면 IoT 솔루션이 경로를 만들고 데이터 원본을 *DeviceLifecycleEvents* 또는 *ModuleLifecycleEvents*와 동일하게 설정해야 합니다. 기본적으로 수명 주기 알림이 전송되지 않습니다. 즉, 이러한 경로는 미리 존재하지 않습니다. 알림 메시지는 속성과 본문을 포함합니다.

속성: 메시지 시스템 속성 앞에 `'$'` 기호를 붙입니다.

장치에 대한 알림 메시지:

| Name | 값 |
| --- | --- |
|$content-type | application/json |
|$iothub-enqueuedtime |  알림이 전송된 시간 |
|$iothub-message-source | deviceLifecycleEvents |
|$content-encoding | utf-8 |
|opType | **createDeviceIdentity** 또는 **deleteDeviceIdentity** |
|hubName | IoT Hub의 이름 |
|deviceId | 장치의 ID |
|operationTimestamp | 작업의 ISO8601 타임스탬프 |
|iothub-message-schema | deviceLifecycleNotification |

본문: 이 섹션은 JSON 형식이며, 생성된 장치 ID 쌍을 나타냅니다. 예를 들면 다음과 같습니다.

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
모듈에 대한 알림 메시지:

| Name | 값 |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  알림이 전송된 시간 |
$iothub-message-source | moduleLifecycleEvents |
$content-encoding | utf-8 |
opType | **createModuleIdentity** 또는 **deleteModuleIdentity** |
hubName | IoT Hub의 이름 |
moduleId | 모듈의 ID |
operationTimestamp | 작업의 ISO8601 타임스탬프 |
iothub-message-schema | moduleLifecycleNotification |

본문: 이 섹션은 JSON 형식이며, 생성된 모듈 ID 쌍을 나타냅니다. 예를 들면 다음과 같습니다.

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>장치 ID 속성

장치 ID는 다음 속성을 사용하여 JSON 문서로 표시됩니다.

| 자산 | 옵션 | 설명 |
| --- | --- | --- |
| deviceId |필요한 경우 업데이트에서 읽기 전용입니다. |ASCII 7 비트 영숫자 문자 + 특정 특수 문자 `- . + % _ # * ? ! ( ) , = @ $ '`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| generationId |필요한 경우 읽기 전용 |IoT Hub에서 생성된 최대 128자의 대/소문자 구분 문자열입니다. 이 값은 삭제되고 다시 만들 때와 동일한 **deviceId**로 장치를 구분하는 데 사용됩니다. |
| etag |필요한 경우 읽기 전용 |[RFC7232][lnk-rfc7232]에 따라 장치 ID에 대해 약한 ETag를 나타내는 문자열입니다. |
| auth |선택 사항 |인증 정보 및 보안 자료를 포함하는 복합 개체입니다. |
| auth.symkey |선택 사항 |base64 형식으로 저장된 기본 및 보조 키를 포함하는 복합 개체입니다. |
| status |필수 |액세스 표시기입니다. **사용** 또는 **사용 안 함**으로 설정할 수 있습니다. **사용**이면 장치를 연결할 수 있습니다. **사용 안 함**이면 이 장치는 장치 연결 끝점에 액세스할 수 없습니다. |
| statusReason |선택 사항 |장치 ID 상태의 원인을 저장하는 128자 길이의 문자열입니다. UTF-8 문자를 모두 허용합니다. |
| statusUpdateTime |읽기 전용 |마지막 상태 업데이트의 시간과 날짜를 보여 주는 임시 표시기입니다. |
| connectionState |읽기 전용 |연결 상태를 나타내는 필드: **연결됨** 또는 **연결 끊김**. 이 필드는 장치 연결 상태의 IoT Hub 뷰를 나타냅니다. **중요**: 이 필드는 개발/디버깅 용도로만 사용해야 합니다. 연결 상태는 MQTT 또는 AMQP를 사용하여 장치에 대해서만 업데이트됩니다. 또한 이는 프로토콜 수준의 ping(MQTT ping 또는 AMQP ping)을 기반으로 하고 있으며 최대 5분 동안만 지연이 될 수 있습니다. 이러한 이유로, 연결되었지만 연결이 끊긴 것으로 보고된 장치와 같이 거짓 긍정이 있을 수 있습니다. |
| connectionStateUpdatedTime |읽기 전용 |연결 상태가 마지막으로 업데이트된 날짜 및 시간을 표시하는 임시 표시기입니다. |
| lastActivityTime |읽기 전용 |장치 연결이 마지막으로 연결되거나 메시지를 받거나 보낸 날짜 및 시간을 표시하는 임시 표시기입니다. |

> [!NOTE]
> 연결 상태는 연결 상태의 IoT Hub 뷰만을 나타낼 수 있습니다. 이 상태에 대한 업데이트는 네트워크 상태 및 구성에 따라 지연될 수도 있습니다.

## <a name="module-identity-properties"></a>모듈 ID 속성

모듈 ID는 다음 속성을 사용하여 JSON 문서로 표현됩니다.

| 자산 | 옵션 | 설명 |
| --- | --- | --- |
| deviceId |필요한 경우 업데이트에서 읽기 전용입니다. |ASCII 7 비트 영숫자 문자 + 특정 특수 문자 `- . + % _ # * ? ! ( ) , = @ $ '`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| moduleId |필요한 경우 업데이트에서 읽기 전용입니다. |ASCII 7 비트 영숫자 문자 + 특정 특수 문자 `- . + % _ # * ? ! ( ) , = @ $ '`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| generationId |필요한 경우 읽기 전용 |IoT Hub에서 생성된 최대 128자의 대/소문자 구분 문자열입니다. 이 값은 삭제되고 다시 만들 때와 동일한 **deviceId**로 장치를 구분하는 데 사용됩니다. |
| etag |필요한 경우 읽기 전용 |[RFC7232][lnk-rfc7232]에 따라 장치 ID에 대해 약한 ETag를 나타내는 문자열입니다. |
| auth |선택 사항 |인증 정보 및 보안 자료를 포함하는 복합 개체입니다. |
| auth.symkey |선택 사항 |base64 형식으로 저장된 기본 및 보조 키를 포함하는 복합 개체입니다. |
| status |필수 |액세스 표시기입니다. **사용** 또는 **사용 안 함**으로 설정할 수 있습니다. **사용**이면 장치를 연결할 수 있습니다. **사용 안 함**이면 이 장치는 장치 연결 끝점에 액세스할 수 없습니다. |
| statusReason |선택 사항 |장치 ID 상태의 원인을 저장하는 128자 길이의 문자열입니다. UTF-8 문자를 모두 허용합니다. |
| statusUpdateTime |읽기 전용 |마지막 상태 업데이트의 시간과 날짜를 보여 주는 임시 표시기입니다. |
| connectionState |읽기 전용 |연결 상태를 나타내는 필드: **연결됨** 또는 **연결 끊김**. 이 필드는 장치 연결 상태의 IoT Hub 뷰를 나타냅니다. **중요**: 이 필드는 개발/디버깅 용도로만 사용해야 합니다. 연결 상태는 MQTT 또는 AMQP를 사용하여 장치에 대해서만 업데이트됩니다. 또한 이는 프로토콜 수준의 ping(MQTT ping 또는 AMQP ping)을 기반으로 하고 있으며 최대 5분 동안만 지연이 될 수 있습니다. 이러한 이유로, 연결되었지만 연결이 끊긴 것으로 보고된 장치와 같이 거짓 긍정이 있을 수 있습니다. |
| connectionStateUpdatedTime |읽기 전용 |연결 상태가 마지막으로 업데이트된 날짜 및 시간을 표시하는 임시 표시기입니다. |
| lastActivityTime |읽기 전용 |장치 연결이 마지막으로 연결되거나 메시지를 받거나 보낸 날짜 및 시간을 표시하는 임시 표시기입니다. |

## <a name="additional-reference-material"></a>추가 참조 자료

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-endpoints] - 각 IoT Hub에서 런타임 및 관리 작업에 대해 공개하는 다양한 끝점에 대해 설명합니다.
* [제한 및 할당량][lnk-quotas]은 IoT Hub 서비스에 적용되는 할당량과 제한 동작에 대해 설명합니다.
* [Azure IoT 장치 및 서비스 SDK][lnk-sdks] - IoT Hub와 상호 작용하는 장치 및 서비스 앱 모두를 개발할 때 사용할 수 있는 다양한 언어 SDK를 나열합니다.
* [IoT Hub 쿼리 언어][lnk-query]는 IoT Hub에서 장치 쌍 및 작업에 대한 정보를 검색하는 데 사용할 수 있는 쿼리 언어에 대해 설명합니다.
* [IoT Hub MQTT 지원][lnk-devguide-mqtt] - MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

IoT Hub ID 레지스트리를 사용하는 방법에 대해 알아봤으니 다음 IoT Hub 개발자 가이드 항목을 살펴보세요.

* [IoT Hub에 대한 액세스 제어][lnk-devguide-security]
* [장치 쌍을 사용하여 상태 및 구성 동기화][lnk-devguide-device-twins]
* [장치에서 직접 메서드 호출][lnk-devguide-directmethods]
* [여러 장치에서 jobs 예약][lnk-devguide-jobs]

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 참조하세요.

* [Azure IoT Hub 시작][lnk-getstarted-tutorial]

IoT Hub Device Provisioning 서비스를 사용하여 무인 Just-In-Time 프로비저닝을 수행하는 방법을 알아보려면 다음을 참조하세요. 

* [Azure IoT Hub Device Provisioning 서비스][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
