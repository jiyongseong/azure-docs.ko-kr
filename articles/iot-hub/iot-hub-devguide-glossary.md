---
title: Azure IoT Hub 용어집 | Microsoft Docs
description: 개발자 가이드 - Azure IoT Hub와 관련된 일반적인 용어집
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: aae72618da42db53304075506b4969945ff0165f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="glossary-of-iot-hub-terms"></a>IoT Hub 용어집
이 문서에서는 IoT Hub 문서에 사용된 몇 가지 일반적인 용어를 나열합니다.

## <a name="advanced-message-queueing-protocol"></a>고급 메시지 큐 프로토콜
[AMQP(고급 메시지 큐 프로토콜)](https://www.amqp.org/)는 [IoT Hub](#iot-hub)에서 장치와 통신을 위해 지원하는 메시징 프로토콜 중 하나입니다. IoT Hub에서 지원하는 메시징 프로토콜에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](iot-hub-devguide-messaging.md)를 참조하세요.

## <a name="automatic-device-management"></a>자동 장치 관리
Azure IoT Hub에서 자동 장치 관리는 전체 수명 주기를 통해 대규모 장치를 관리하는 반복적이고 복잡한 작업을 자동화합니다. 자동 장치 관리를 사용하여 해당 속성을 기반으로 장치 집합을 대상으로 지정하고, 원하는 구성을 정의하고, 범위에 나올 때마다 IoT Hub에서 장치를 업데이트하도록 할 수 있습니다.  [자동 장치 구성](iot-hub-auto-device-config.md) 및 [IoT Edge 자동 배포](../iot-edge/how-to-deploy-monitor.md)로 구성됩니다.

## <a name="automatic-device-configuration"></a>자동 장치 구성
솔루션 백 엔드는 [자동 장치 구성](iot-hub-auto-device-config.md)을 사용하여 [장치 쌍](#device-twin)의 집합에 원하는 속성을 할당하고 시스템 메트릭 및 사용자 지정 메트릭을 사용하여 상태를 보고할 수 있습니다. 

## <a name="azure-cli"></a>Azure CLI
[Azure CLI](../cli-install-nodejs.md)는 Microsoft Azure에서 리소스를 만들고 관리하기 위한 플랫폼 간 오픈 소스 셸 기반 명령 도구입니다. 이 버전의 CLI는 Node.js를 사용하여 구현됩니다.

## <a name="azure-cli-20"></a>Azure CLI 2.0
[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)은 Microsoft Azure에서 리소스를 만들고 관리하기 위한 플랫폼 간 오픈 소스 셸 기반 명령 도구입니다. 이 CLI 미리 보기 버전은 Python을 사용하여 구현됩니다.


## <a name="azure-iot-device-sdks"></a>Azure IoT 장치 SDK
여러 언어로 된 _장치 SDK_가 제공되어 이를 통해 IoT Hub와 상호 작용하는 [장치 앱](#device-app)을 만들 수 있습니다. IoT Hub 자습서는 이러한 장치 SDK를 사용하는 방법을 보여 줍니다. 이 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdks)에서 장치 SDK에 대한 소스 코드와 추가 정보를 확인할 수 있습니다.

## <a name="azure-iot-service-sdks"></a>Azure IoT 서비스 SDK
여러 언어로 된 _장치 SDK_가 제공되어 이를 통해 IoT Hub와 상호 작용하는 [백 엔드 앱](#back-end-app)을 만들 수 있습니다. IoT Hub 자습서는 이러한 서비스 SDK를 사용하는 방법을 보여 줍니다. 이 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdks)에서 서비스 SDK에 대한 소스 코드와 추가 정보를 확인할 수 있습니다.

## <a name="azure-portal"></a>Azure portal
[Microsoft Azure Portal](https://portal.azure.com)은 Azure 리소스를 프로비전 및 관리할 수 있는 중앙 위치입니다. _블레이드_를 사용하여 해당 콘텐츠를 구성합니다.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview)은 Windows PowerShell로 Azure를 관리하는 데 사용할 수 있는 cmdlet 컬렉션입니다. cmdlet을 사용하여 Azure 플랫폼을 통해 제공되는 솔루션과 서비스를 만들고 테스트, 배포 및 관리할 수 있습니다.

## <a name="azure-resource-manager"></a>Azure 리소스 관리자
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)를 사용하면 솔루션에서 리소스를 그룹으로 사용할 수 있습니다. 조정된 단일 작업에서 솔루션에 대한 리소스를 배포, 업데이트 또는 삭제할 수 있습니다.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md)에서는 온-프레미스 솔루션을 클라우드에 연결할 수 있는 릴레이된 통신과 엔터프라이즈 메시징을 사용하여 클라우드 사용 통신을 제공합니다. 일부 IoT Hub 자습서는 Service Bus [큐](../service-bus-messaging/service-bus-messaging-overview.md)를 활용합니다.

## <a name="azure-storage"></a>Azure Storage
[Azure Storage](../storage/common/storage-introduction.md)는 클라우드 저장소 솔루션입니다. 여기에는 구조화되지 않은 개체 데이터를 저장하는 데 사용할 수 있는 Blob Storage 서비스가 포함됩니다. 일부 IoT Hub 자습서는 Blob Storage를 사용합니다.

## <a name="back-end-app"></a>백 엔드 앱
[IoT Hub](#iot-hub)의 컨텍스트에서 백 엔드 앱은 IoT Hub에서 서비스 지향 끝점 중 하나에 연결되는 앱입니다. 예를 들어 백 엔드 앱은 [장치-클라우드](#device-to-cloud) 메시지를 검색하거나 [ID 레지스트리](#identity-registry)를 관리할 수 있습니다. 일반적으로 백 엔드 앱은 클라우드에서 실행되지만 대부분의 자습서에 나오는 백 엔드 앱은 로컬 개발 환경에서 실행 중인 콘솔 앱입니다.

## <a name="built-in-endpoints"></a>기본 제공 끝점
모든 IoT Hub에는 Event Hub와 호환되는 기본 제공 [끝점](iot-hub-devguide-endpoints.md)이 있습니다. Event Hubs와 함께 작동하는 모든 메커니즘을 사용하여 이 끝점에서 장치-클라우드 메시지를 읽을 수 있습니다.

## <a name="cloud-gateway"></a>클라우드 게이트웨이
클라우드 게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 장치의 연결이 가능합니다. 클라우드 게이트웨이는 장치에 대해 로컬로 시행되는 [필드 게이트웨이](#field-gateway)와 달리, 클라우드에서 호스트됩니다. 클라우드 게이트웨이를 위한 일반 사용 사례는 장치에 대한 프로토콜 변환을 구현하는 것입니다.

## <a name="cloud-to-device"></a>클라우드-장치
IoT Hub에서 연결된 장치로 전송되는 메시지를 참조하세요. 대개 이러한 메시지는 장치에 작업을 수행하도록 하는 명령입니다. 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](iot-hub-devguide-messaging.md)를 참조하세요.

## <a name="configuration"></a>구성
[자동 장치 구성](iot-hub-auto-device-config.md)의 컨텍스트에서 IoT Hub 내의 구성은 장치 쌍의 집합에 대해 원하는 구성을 정의하고 상태 및 진행률을 보고하도록 메트릭 집합을 제공합니다.

## <a name="connection-string"></a>연결 문자열
앱 코드에서 연결 문자열을 사용하여 끝점에 연결하는 데 필요한 정보를 캡슐화할 수 있습니다. 일반적으로 연결 문자열에는 끝점의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. IoT Hub 서비스와 연관된 연결 문자열에는 다음 두 종류가 있습니다.
- *장치 연결 문자열*은 IoT hub의 장치 연결 끝점에 연결하는 장치를 사용하도록 설정합니다.
- *IoT Hub 연결 문자열*은 IoT hub의 서비스 연결 끝점에 연결하는 백 엔드 앱을 사용하도록 설정합니다.

## <a name="custom-endpoints"></a>사용자 지정 끝점
IoT Hub에 사용자 지정 [끝점](iot-hub-devguide-endpoints.md)을 만들어 [라우팅 규칙](#routing-rules)으로 발송된 메시지를 배달할 수 있습니다. 사용자 지정 끝점은 Event Hub, Service Bus 큐 또는 Service Bus 토픽에 직접 연결됩니다.

## <a name="custom-gateway"></a>사용자 지정 게이트웨이
게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 장치의 연결이 가능합니다. [Azure IoT Edge](#azure-iot-edge)를 사용하여 메시지, 사용자 지정 프로토콜 변환 및 에지의 다른 프로세스를 처리하도록 사용자 지정 논리를 구현하는 사용자 지정 게이트웨이를 빌드할 수 있습니다.

## <a name="data-point-message"></a>데이터 요소 메시지
데이터 요소 메시지는 풍속 또는 온도와 같은 [원격 분석](#telemetry) 데이터를 포함하는 [클라우드-장치](#device-to-cloud) 메시지입니다.

## <a name="desired-configuration"></a>desired 구성
[장치 쌍](iot-hub-devguide-device-twins.md) 컨텍스트에서 desired 구성은 장치와 동기화해야 하는 장치의 속성 및 메타데이터 전체 집합을 나타냅니다.

## <a name="desired-properties"></a>Desired 속성
[장치 쌍](iot-hub-devguide-device-twins.md) 컨텍스트에서 desired 속성은 [reported 속성](#reported-properties)과 함께 장치 구성 또는 상황을 동기화하는 데 사용되는 장치 쌍의 하위 섹션입니다. desired 속성은 [백 엔드 앱](#back-end-app)에서만 설정할 수 있고 [장치 앱](#device-app)에서 관찰할 수 있습니다.

## <a name="device-to-cloud"></a>장치-클라우드
연결된 장치에서 [IoT Hub](#iot-hub)로 전송되는 메시지를 참조하세요. 이러한 메시지는 [데이터 요소](#data-point-message) 또는 [대화형](#interactive-message) 메시지일 수 있습니다. 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](iot-hub-devguide-messaging.md)를 참조하세요.

## <a name="device"></a>장치
IoT의 컨텍스트에서 일반적으로 장치는 데이터를 수집하거나 다른 장치를 제어할 수 있는 소규모의 독립 실행형 컴퓨팅 장치입니다. 예를 들어 장치는 환경 모니터링 장치이거나 온실에서 급수 및 통풍 시스템을 위한 컨트롤러일 수 있습니다. [장치 카탈로그](https://catalog.azureiotsuite.com/)는 [IoT Hub](#iot-hub)에서 작동이 인증된 하드웨어 장치 목록을 제공합니다.

## <a name="device-app"></a>장치 앱
장치 앱은 [장치](#device)에서 실행되며 [IoT Hub](#iot-hub)와의 통신을 처리합니다. 일반적으로 장치 앱을 구현할 때 [Azure IoT 장치 SDK](#azure-iot-device-sdks) 중 하나를 사용합니다. 대부분의 IoT 자습서에서는 편의를 위해 [시뮬레이트된 장치](#simulated-device)를 사용합니다.

## <a name="device-condition"></a>장치 조건
[장치 앱](#device-app)에서 보고된 장치 상태 정보(예: 현재 사용 중인 연결 방법)를 나타냅니다. [장치 앱](#device-app)은 해당 기능도 보고할 수 있습니다. 장치 쌍을 사용하여 조건 및 기능 정보를 쿼리할 수 있습니다.

## <a name="device-data"></a>장치 데이터
장치 데이터는 IoT Hub [ID 레지스트리](#identity-registry)에 저장된 장치 단위 데이터를 나타냅니다. 이 데이터를 가져오고 내보낼 수 있습니다.

## <a name="device-explorer"></a>장치 탐색기
[장치 탐색기](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)는 Windows에서 실행되는 도구이며 이 도구를 통해 [ID 레지스트리](#identity-registry)에서 장치를 관리할 수 있습니다. 또한 이 도구는 장치로 메시지를 보내고 받을 수 있습니다.

## <a name="device-identities-rest-api"></a>장치 ID REST API
[장치 ID REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)를 통해 REST API를 사용하여 [ID 레지스트리](#identity-registry)에 등록된 장치를 관리할 수 있습니다. 일반적으로 IoT Hub 자습서에 나와 있는 것보다 상위 수준의 [서비스 SDK](#azure-iot-service-sdks)를 사용해야 합니다.

## <a name="device-identity"></a>장치 ID
장치 ID는 [ID 레지스트리](#identity-registry)에 등록된 모든 장치에 할당된 고유한 식별자입니다.

## <a name="module-identity"></a>모듈 ID
모듈 ID는 장치에 속해 있는 모든 모듈에 할당된 고유 식별자입니다. 또한 모듈 ID는 [ID 레지스트리](#identity-registry)에도 등록됩니다.

## <a name="device-management"></a>장치 관리
장치 관리에는 계획, 프로비전, 구성, 모니터링 및 사용 중지를 비롯하여 IoT 솔루션에서 장치 관리와 관련된 전체 수명 주기가 포함됩니다.

## <a name="device-management-patterns"></a>장치 관리 패턴
[IoT Hub](#iot-hub)를 통해 장치에서 재부팅, 공장 재설정 수행 및 펌웨어 업데이트 수정을 비롯한 일반적인 장치 관리 패턴을 수행할 수 있습니다.

## <a name="device-messaging-rest-api"></a>장치 메시징 REST API
장치에서 [장치 메시징 REST API](https://docs.microsoft.com/rest/api/iothub/httpruntime)를 사용하여 장치-클라우드 메시지를 IoT Hub로 보내고 IoT Hub에서 [클라우드-장치](#cloud-to-device)를 수신할 수 있습니다. 일반적으로 IoT Hub 자습서에 나와 있는 것보다 상위 수준의 [장치 SDK](#azure-iot-device-sdks)를 사용해야 합니다.

## <a name="device-provisioning"></a>장치 프로비전
[장치 프로비전](#device-data)은 솔루션 저장소에 초기 장치 데이터를 추가하는 프로세스입니다. 새 장치를 허브에 연결하도록 하려면 장치 ID 및 키를 IoT Hub [ID 레지스트리](#identity-registry)에 추가해야 합니다. 프로비전 프로세스의 일부로, 다른 솔루션 저장소에서 장치 특정 데이터를 초기화해야 할 수 있습니다.

## <a name="device-twin"></a>장치 쌍
[장치 쌍](iot-hub-devguide-device-twins.md)은 장치의 상태 정보(메타데이터, 구성 및 조건)를 저장하는 JSON 문서입니다. [IoT Hub](#iot-hub)는 IoT Hub에 프로비전하는 각 장치에 대해 하나의 장치 쌍을 유지합니다. 장치 쌍을 통해 장치 및 솔루션 백 엔드 간의 [장치 조건](#device-condition) 및 구성을 동기화할 수 있습니다. 장치 쌍을 쿼리하여 특정 장치를 찾고 장기 실행 중인 작업의 상태를 쿼리할 수 있습니다.

## <a name="module-twin"></a>모듈 쌍
장치 쌍과 유사하게 모듈 쌍은 모듈의 상태 정보(메타데이터, 구성 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 IoT 허브의 장치 ID에서 프로비전하는 각 모듈 ID에 대해 모듈 쌍을 유지합니다. 모듈 쌍을 통해 모듈 및 솔루션 백 엔드 간의 모듈 조건 및 구성을 동기화할 수 있습니다. 모듈 쌍을 쿼리하여 특정 모듈을 찾고 장기 실행 중인 작업의 상태를 쿼리할 수 있습니다.

## <a name="twin-queries"></a>쌍 쿼리
[장치 및 모듈 쌍 쿼리](iot-hub-devguide-query-language.md)에서는 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 장치 쌍 또는 모듈 쌍에서 정보를 검색합니다. 동일한 IoT Hub 쿼리 언어를 사용하여 IoT Hub에서 실행 중인 [작업](#job)에 대한 정보를 검색할 수 있습니다.

## <a name="device-twin-rest-api"></a>장치 쌍 REST API
솔루션 백 엔드에서 [장치 쌍 REST API](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)를 사용하여 장치 쌍을 관리할 수 있습니다. API를 사용하면 [장치 쌍](#device-twin) 속성을 검색 및 업데이트하고 [직접 메서드](#direct-method)를 호출할 수 있습니다. 일반적으로 IoT Hub 자습서에 나와 있는 것보다 상위 수준의 [서비스 SDK](#azure-iot-service-sdks)를 사용해야 합니다.

## <a name="twin-synchronization"></a>쌍 동기화
쌍 동기화는 장치 쌍 또는 모듈 쌍에서 [원하는 속성](#desired-properties)을 사용하여 장치 또는 모듈을 구성하고 쌍에 저장할 장치 또는 모듈의 [보고된 속성](#reported-properties)을 검색합니다.

## <a name="direct-method"></a>직접 메서드
[직접 메서드](iot-hub-devguide-direct-methods.md)는 IoT Hub에서 API를 호출하여 장치에서 실행할 메서드를 트리거하는 방법입니다.

## <a name="endpoint"></a>끝점
IoT Hub는 앱을 IoT Hub에 연결할 수 있도록 해주는 여러 [끝점](iot-hub-devguide-endpoints.md)을 노출합니다. 장치가 [장치-클라우드](#device-to-cloud) 메시지 보내기 및 [클라우드-장치](#cloud-to-device) 메시지 받기와 같은 작업을 수행할 수 있도록 해주는 장치 지향 끝점이 있습니다. [백 엔드 앱](#back-end-app)이 [장치 ID](#device-identity) 관리 및 장치 쌍 관리와 같은 작업을 수행할 수 있도록 하는 서비스 지향 관리 끝점이 있습니다. 장치-클라우드 메시지 를 읽기 위한 서비스 지향 [기본 제공 끝점](#built-in-endpoints)이 있습니다. [사용자 지정 끝점](#custom-endpoints)을 만들어 [라우팅 규칙](#routing-rules)으로 발송된 장치-클라우드 메시지를 받을 수 있습니다.

## <a name="event-hubs-service"></a>Event Hubs 서비스
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)는 초당 수백만의 이벤트를 유입할 수 있는 확장성이 뛰어난 수집 서비스입니다. 이 서비스를 사용하면 연결된 장치와 응용 프로그램에서 생성한 대량의 데이터를 처리하고 분석할 수 있습니다. IoT Hub 서비스와 비교는 [Azure IoT Hub 및 Azure Event Hubs의 비교](iot-hub-compare-event-hubs.md)를 참조하세요.

## <a name="event-hub-compatible-endpoint"></a>Event Hub 호환 끝점
IoT Hub에 전송된 [장치-클라우드](#device-to-cloud) 메시지를 읽으려면 허브에 있는 끝점에 연결하고 Event Hub 호환 메서드를 사용하여 해당 메시지를 읽을 수 있습니다. 모든 Event Hub 호환 메서드에는 [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) 및 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 사용이 포함됩니다.

## <a name="field-gateway"></a>필드 게이트웨이
필드 게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 장치의 연결이 가능하며 일반적으로 장치와 함께 로컬로 배포됩니다. 자세한 내용은 [Azure IoT Hub란?](iot-hub-what-is-iot-hub.md)을 참조하세요.

## <a name="free-account"></a>무료 계정
[무료 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들어 IoT Hub 자습서를 완료하고 IoT Hub 서비스(및 기타 Azure 서비스)를 연습해볼 수 있습니다

## <a name="gateway"></a>게이트웨이
게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 장치의 연결이 가능합니다. 참고 항목: [필드 게이트웨이](#field-gateway), [클라우드 게이트웨이](#cloud-gateway) 및 [사용자 지정 게이트웨이](#custom-gateway)

## <a name="identity-registry"></a>ID 레지스트리
[ID 레지스트리](iot-hub-devguide-identity-registry.md)는 IoT Hub에 연결이 허용된 개별 장치에 대한 정보를 저장하는 IoT Hub의 기본 제공 구성 요소입니다.

## <a name="interactive-message"></a>대화형 메시지
대화형 메시지는 솔루션 백 엔드에서 즉각적인 작업을 트리거하는 [클라우드-장치](#cloud-to-device) 메시지입니다. 예를 들어 장치는 CRM 시스템에 자동으로 로깅되는 실패에 대한 경보를 보낼 수 있습니다.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT Hub는 수백만의 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 Azure 서비스입니다. 자세한 내용은 [Azure IoT Hub란?](iot-hub-what-is-iot-hub.md)을 참조하세요. [Azure 구독](#subscription)을 사용하여 IoT 메시징 워크로드를 처리하는 IoT Hub를 만들 수 있습니다.

## <a name="iot-hub-metrics"></a>IoT Hub 메트릭
[IoT Hub 메트릭](iot-hub-metrics.md)은 [Azure 구독](#subscription)의 IoT Hub 상태에 대한 데이터를 제공합니다. IoT Hub 메트릭을 통해 서비스와 연결된 장치의 전반적인 상태를 평가할 수 있습니다. IoT Hub 메트릭을 통해 Azure 지원 센터에 문의할 필요 없이 IoT Hub의 상황을 파악하고 근본 원인을 조사할 수 있습니다.

## <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어
[IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)는 SQL과 유사한 언어로, [작업](#job) 및 장치 쌍을 쿼리할 수 있습니다.

## <a name="iot-hub-resource-provider-rest-api"></a>IoT Hub 리소스 공급자 REST API
[IoT Hub 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest)를 사용하여 허브 만들기, 업데이트 및 삭제와 같은 작업을 수행하는 [Azure 구독](#subscription)에서 IoT Hub를 관리할 수 있습니다.

## <a name="iot-solution-accelerators"></a>IoT 솔루션 가속기
Azure IoT 솔루션 가속기는 여러 Azure 서비스를 솔루션으로 함께 패키징합니다. 이러한 솔루션을 통해 일반적인 IoT 시나리오의 종단 간 구현을 빠르게 시작합니다. 자세한 내용은 [Azure IoT 솔루션 가속기란?](../iot-suite/iot-suite-overview.md)을 참조하세요.

## <a name="the-iot-extension-for-azure-cli-20"></a>Azure CLI 2.0에 대한 IoT 확장
[Azure CLI 2.0에 대한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension)은 플랫폼 간 명령줄 도구입니다. 이 도구를 통해 [ID 레지스트리](#identity-registry)에서 장치를 관리하고 장치에서 메시지 및 파일을 보내고 받으며 IoT Hub 작업을 모니터링할 수 있습니다.

## <a name="job"></a>작업
솔루션 백 엔드에서는 IoT Hub에 등록된 장치 집합에서의 활동을 예약 및 추적하는 [작업](iot-hub-devguide-jobs.md)을 사용할 수 있습니다. 활동에는 장치 쌍 [desired 속성](#desired-properties) 업데이트, 장치 쌍 [태그](#tags) 업데이트 및 [직접 메서드](#direct-method) 호출이 포함됩니다. [IoT Hub](#iot-hub)는 [ID 레지스트리](#identity-registry)에서 [가져오고 내보내는](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) 작업도 사용합니다.

## <a name="jobs-rest-api"></a>작업 REST API
[작업 REST API](https://docs.microsoft.com/rest/api/iothub/jobapi)는 IoT Hub에서 실행되는 [작업](#job)을 관리할 수 있게 해줍니다.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/)는 [IoT Hub](#iot-hub)에서 장치와 통신을 위해 지원하는 메시징 프로토콜 중 하나입니다. IoT Hub에서 지원하는 메시징 프로토콜에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](iot-hub-devguide-messaging.md)를 참조하세요.

## <a name="operations-monitoring"></a>작업 모니터링
IoT Hub [작업 모니터링](iot-hub-operations-monitoring.md)을 사용하면 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링할 수 있습니다. [IoT Hub](#iot-hub)는 몇 가지 작업 범주에 걸쳐 이벤트를 추적합니다. 하나 이상의 범주에서 IoT Hub의 끝점으로 처리할 이벤트를 보내도록 선택할 수 있습니다. 데이터에 오류가 있는지 모니터링하거나 데이터 패턴을 기반으로 좀 더 복잡한 처리를 설정할 수 있습니다.

## <a name="physical-device"></a>물리적 장치
물리적 장치는 Raspberry Pi처럼 IoT Hub에 연결되는 실제 장치입니다. 편의를 위해 많은 IoT Hub 자습서에서 [시뮬레이션된 장치](#simulated-device)를 사용하여 로컬 컴퓨터에서 샘플을 실행할 수 있도록 합니다.

## <a name="primary-and-secondary-keys"></a>기본 및 보조 키
IoT Hub에서 장치 지향 또는 서비스 지향 끝점에 연결하는 경우 [연결 문자열](#connection-string)에는 액세스 권한을 부여하는 키가 포함됩니다. 장치를 [ID 레지스트리](#identity-registry)에 추가하거나 허브에 [공유 액세스 정책](#shared-access-policy)을 추가하는 경우 서비스에서 기본 및 보조 키를 생성합니다. 두 가지 키를 통해 키를 업데이트할 때 IoT Hub에 대한 액세스 권한을 잃지 않고도 한 키에서 다른 키로 롤오버가 가능합니다.

## <a name="protocol-gateway"></a>프로토콜 게이트웨이
일반적으로 프로토콜 게이트웨이는 클라우드에 배포되며 [IoT Hub](#iot-hub)에 연결되는 장치에 대해 프로토콜 번역 서비스를 제공합니다. 자세한 내용은 [Azure IoT Hub란?](iot-hub-what-is-iot-hub.md)을 참조하세요.

## <a name="quotas-and-throttling"></a>할당량 및 제한
[IoT Hub](#iot-hub) 사용에 적용되는 다양한 [할당량](iot-hub-devguide-quotas-throttling.md)이 있으며 대부분은 IoT Hub의 계층에 따라 달라집니다. [IoT Hub](#iot-hub)는 런타임 시 서비스 사용에 [제한](iot-hub-devguide-quotas-throttling.md)도 적용합니다.

## <a name="reported-configuration"></a>Reported 구성
[장치 쌍](iot-hub-devguide-device-twins.md) 컨텍스트에서 reported 구성은 솔루션 백 엔드에 보고해야 하는 장치 쌍의 속성 및 메타데이터 집합 전체를 나타냅니다.

## <a name="reported-properties"></a>reported 속성
[장치 쌍](iot-hub-devguide-device-twins.md) 컨텍스트에서 reported 속성은 [desired 속성](#desired-properties)과 함께 장치 구성 또는 상황을 동기화하는 데 사용되는 장치 쌍의 하위 섹션입니다. reported 속성은 [장치 앱](#device-app)에서만 설정할 수 있고 [백 엔드 앱](#back-end-app)에서 읽고 쿼리할 수 있습니다.

## <a name="resource-group"></a>리소스 그룹
[Azure Resource Manager](#azure-resource-manager)는 리소스 그룹을 사용하여 관련 리소스를 함께 그룹화합니다. 그룹에 있는 모든 리소스에 대해 작업을 동시에 수행하는 데 리소스 그룹을 사용할 수 있습니다.

## <a name="retry-policy"></a>다시 시도 정책
클라우드 서비스에 연결할 때는 재시도 정책을 사용하여 [일시적인 오류](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx)를 처리합니다.

## <a name="routing-rules"></a>라우팅 규칙
IoT Hub에서 [기본 제공 끝점](#built-in-endpoints) 또는 [사용자 지정 끝점](#custom-endpoints)으로 장치-클라우드 메시지를 라우팅하여 솔루션 백 엔드에서 처리하도록 [라우팅 규칙](iot-hub-devguide-messages-read-custom.md)을 구성합니다.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN은 [AMQP](#advanced-message-queue-protocol) 프로토콜에서 보안 토큰을 전송하는 데 사용하는 프로토콜입니다.

## <a name="shared-access-signature"></a>공유 액세스 서명
공유 액세스 서명(SAS)은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. SAS 인증에는 _공유 액세스 정책_과 _공유 액세스 서명_(토큰이라고 부름)의 두 구성 요소가 있습니다. 장치는 SAS를 사용하여 IoT Hub를 인증합니다. [백 엔드 앱](#back-end-app)도 SAS를 사용하여 IoT Hub에서 서비스 지향 끝점을 인증합니다. 일반적으로 앱이 IoT Hub와 연결을 설정하기 위해 사용하는 [연결 문자열](#connection-string)에 SAS 토큰을 포함합니다.

## <a name="shared-access-policy"></a>공유 액세스 정책
공유 액세스 정책은 해당 정책과 관련된 유효한 [기본 또는 보조 키](#primary-and-secondary-keys)를 보유한 모든 사람에게 부여할 사용 권한을 정의합니다. [포털](#azure-portal)에서 허브에 대한 공유 액세스 정책 및 키를 관리할 수 있습니다.

## <a name="simulated-device"></a>시뮬레이션된 장치
편의를 위해 많은 IoT Hub 자습서에서 시뮬레이션된 장치를 사용하여 로컬 컴퓨터에서 샘플을 실행할 수 있도록 합니다. 반면에, [물리적 장치](#physical-device)는 Raspberry Pi처럼 IoT Hub에 연결되는 실제 장치입니다.

## <a name="solution"></a>해결 방법
_솔루션_은 하나 이상의 프로젝트를 포함하는 Visual Studio 솔루션을 나타낼 수 있습니다. _솔루션_은 또한 장치, [장치 앱](#device-app), IoT Hub, 기타 Azure 서비스 및 [백 엔드 앱](#back-end-app)과 같은 요소를 포함하는 IoT 솔루션을 나타낼 수 있습니다.

## <a name="subscription"></a>구독
Azure 구독에서는 청구가 이루어집니다. 생성한 각 Azure 서비스 또는 사용하는 Azure 서비스는 단일 구독에 연결됩니다. 또한 많은 할당량이 구독 수준에서 적용됩니다.

## <a name="system-properties"></a>시스템 속성
[장치 쌍](iot-hub-devguide-device-twins.md) 컨텍스트에서 시스템 속성은 읽기 전용이며 마지막 작업 시간 및 연결 상태와 같은 장치 사용에 대한 정보를 포함합니다.

## <a name="tags"></a>태그들
[장치 쌍](iot-hub-devguide-device-twins.md) 컨텍스트에서 태그는 JSON 문서 형식으로 솔루션 백 엔드에 의해 저장 및 검색되는 장치 메타데이터입니다. 태그는 장치 앱에 표시되지 않습니다.

## <a name="telemetry"></a>원격 분석
장치는 풍속 또는 온도와 같은 원격 분석 데이터를 수집하고 [데이터 요소 메시지](#data-point-messages)를 사용하여 IoT Hub에 원격 분석을 보냅니다.

## <a name="token-service"></a>토큰 서비스
토큰 서비스를 사용하여 장치에 대한 인증 메커니즘을 구현합니다. **DeviceConnect** 권한으로 IoT Hub [공유 액세스 정책](#shared-access-policy)을 사용하여 *device-scoped* 토큰을 만듭니다. 이러한 토큰은 장치에서 IoT Hub에 연결할 수 있게 해줍니다. 장치는 사용자 지정 인증 메커니즘을 사용하여 토큰 서비스를 인증합니다. 장치가 성공적으로 인증되면 토큰 서비스는 사용할 장치에 대한 SAS 토큰을 발행하여 IoT Hub에 액세스합니다.

## <a name="x509-client-certificate"></a>X.509 클라이언트 인증서
장치는 X.509 인증서를 사용하여 [IoT Hub](#iot-hub)와 인증을 수행할 수 있습니다. [SAS 토큰](#shared-access-signature)을 사용하는 대신 X.509 인증서를 사용합니다.
