---
title: Azure Event Hubs FAQ | Microsoft Docs
description: Azure Event Hubs FAQ(질문과 대답)
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 8a30b8cb92b98606ca3d859ada329b8301806b10
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs 질문과 대답

## <a name="general"></a>일반

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Event Hubs 기본 및 표준 계층 간의 차이는 무엇입니까?

Azure Event Hubs의 표준 계층은 기본 계층에서 제공할 수 있는 것보다 많은 기능을 제공합니다. 다음 기능이 표준에 포함됩니다.
* 더 길어진 이벤트 보존 기간
* 추가 조정된 연결, 포함된 수보다 많은 경우 초과분 요금 있음
* 단일 소비자 그룹 이상
* [캡처](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

전용 Event Hubs를 비롯한 가격 책정 계층에 대한 자세한 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="what-are-event-hubs-throughput-units"></a>Event Hubs 처리량 단위는 무엇입니까?

이벤트 허브 처리량 단위는 Azure Portal 또는 Event Hubs Resource Manager 템플릿을 통해 명시적으로 선택합니다. 처리량 단위는 Event Hubs 네임스페이스의 모든 이벤트 허브에 적용되며, 각 처리량 단위를 통해 네임스페이스에서 다음 기능을 수행할 수 있습니다.

* 초당 최대 1MB의 수신 이벤트(이벤트 허브로 전송된 이벤트), 단 초당 1,000개 이하의 수신 이벤트, 관리 작업 또는 제어 API 호출
* 초당 최대 2MB의 송신 이벤트(이벤트 허브에서 사용된 이벤트)
* 최대 84GB의 이벤트 저장소(기본 24시간 보존 기간에 대해 충분).

Event Hubs 처리량 단위는 주어진 시간 동안 선택된 단위의 최대 수에 따라 시간당 청구됩니다. 사용량이 증가하면 자동으로 [처리량 단위 수를 증가](event-hubs-auto-inflate.md)시킬 수 있습니다.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Event Hubs 처리량 단위 제한을 적용하는 방법
전체 수신 처리량 또는 네임스페이스 내 모든 이벤트 허브에서의 전체 수신 이벤트 비율이 집계 처리량 단위 허용 한도를 초과하면, 발신자가 제한되고 수신 할당량을 초과했음을 나타내는 오류가 표시됩니다.

전체 송신 처리량 또는 네임스페이스 내 모든 이벤트 허브에서의 전체 송신 이벤트 비율이 집계 처리량 단위 허용 한도를 초과하면, 수신자가 제한되고 송신 할당량을 초과했음을 나타내는 오류가 표시됩니다. 수신 및 송신 할당량은 별도로 적용되므로 발신자가 이벤트 사용 속도를 저하시키지 않으며 수신자가 이벤트 허브로의 이벤트 전송을 차단하지도 않습니다.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>선택할 수 있는 처리량 단위 수에 제한이 있습니까?
네임스페이스 당 20개의 처리량 단위로 기본 할당량이 적용됩니다. 지원 티켓을 제출하여 더 많은 처리량 단위 할당량을 요청할 수 있습니다. 20개의 처리량 단위 제한을 초과하면 번들은 20-100 처리량 단위에서 사용할 수 있습니다. 20개 이상의 처리량 단위를 사용하면 지원 티켓을 제출하지 않고 처리량 단위 수를 변경하는 기능을 제거합니다.

[자동 확장](event-hubs-auto-inflate.md) 기능을 사용하여 사용량이 증가하면 자동으로 처리량 단위 수를 증가시킬 수 있습니다.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>단일 AMQP 연결을 사용하여 여러 이벤트 허브에서 보내고 받을 수 있나요?
예, 모든 이벤트 허브가 동일한 네임스페이스에 있는 경우 가능합니다.

### <a name="what-is-the-maximum-retention-period-for-events"></a>이벤트의 최대 보존 기간은 어떻게 되나요?
Event Hubs 표준 계층은 현재 최대 7일의 보존 기간을 지원합니다. 이벤트 허브는 영구 데이터 저장소로 사용되지 않습니다. 24시간 이상의 보존 기간은 이벤트 스트림을 동일한 시스템으로 재생 하기에 편리한 시나리오를 위해 사용됩니다. 예를 들어, 기존 데이터에서 새 기계 학습 모델을 훈련하거나 확인하기 위해서입니다. 7일 이상 메시지 보존이 필요할 경우 Event Hubs에서 [Event Hubs 캡처](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)를 사용하도록 설정하면 Event Hubs의 데이터가 선택한 Storage 계정 또는 Azure Data Lake 서비스 계정으로 옮겨집니다. 캡처를 사용하도록 설정하면 구매한 처리량 단위에 따라 요금이 부과됩니다.

### <a name="where-is-azure-event-hubs-available"></a>어디에서 Azure Event Hubs를 사용할 수 있나요?
Azure Event Hubs는 지원되는 모든 Azure 지역에서 사용할 수 있습니다. 목록은 [Azure 지역](https://azure.microsoft.com/regions/)를 참조하세요.  

## <a name="best-practices"></a>모범 사례

### <a name="how-many-partitions-do-i-need"></a>얼마나 많은 파티션이 필요한가요?

Event Hub의 파티션 수는 설정한 후 수정할 수 없습니다. 이 점을 염두하고 시작하기 전에 필요한 파티션 수를 생각하는 것이 중요합니다. 

Event Hubs는 소비자 그룹당 단일 파티션 판독기를 허용하도록 설계되었습니다. 대부분의 경우 기본 설정인 4개의 파티션만으로도 충분합니다. 이벤트 처리의 크기를 조정하려는 경우 추가 파티션을 추가할지 고려할 수 있습니다. 파티션에 대한 특정 처리량 제한은 없지만 네임스페이스에서 집계 처리량은 처리량 단위 수로 제한됩니다. 네임스페이스에서 처리량 단위 수를 늘리면 추가 파티션을 통해 동시 판독기가 고유한 최대 처리량을 달성하도록 할 수 있습니다.

그러나 응용 프로그램에 특정 파티션에 대한 선호도가 있는 모델인 경우 파티션 수를 늘리게 되면 사용자에게 혜택이 돌아오지 않습니다. 자세한 내용은 [가용성 및 일관성](event-hubs-availability-and-consistency.md)을 참조하세요.

## <a name="pricing"></a>가격

### <a name="where-can-i-find-more-pricing-information"></a>추가 가격 책정 정보는 어디에서 찾을 수 있나요?
Event Hubs 가격 책정에 대한 전체 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>24시간 이상 Event Hubs 이벤트를 유지 하려면 비용이 청구됩니까?
Event Hubs 표준 계층은 최대 7일 동안 24시간 이상 메시지 보존을 허용합니다. 저장된 이벤트 수의 총 크기가 선택한 처리량 단위(처리량 단위당 84GB)에 대한 저장소 허용 한도를 초과하면, 해당 허용 한도를 초과하는 크기는 게시된 Azure Blob 저장소 요금으로 청구됩니다. 처리량 단위가 최대 수신 허용 한도까지 사용되었더라도 각 처리량 단위의 저장소 허용 한도는 24 시간(기본값) 동안의 보존 기간에 대한 모든 저장소 비용이 포함됩니다.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Event Hubs 저장소 크기가 계산 및 청구되는 방법
모든 이벤트 허브의 디스크 저장소 구조 또는 이벤트 헤더의 내부 오버헤드를 포함하여, 저장된 모든 이벤트의 전체 크기가 하루 종일 측정됩니다. 하루가 끝날 때 최대 저장소 크기가 계산됩니다. 일일 저장소 허용 한도는 하루 동안 선택된 최소 처리량 단위 수를 기준으로 계산됩니다(각 처리량 단위는 84GB의 허용 한도를 제공함) . 총 크기가 계산된 일일 저장소 허용 한도를 초과하면, 초과 저장소는 Azure Blob Storage 가격을 사용하여 청구됩니다( **로컬 중복 저장소** 속도로).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Event Hubs 수신 이벤트 계산하는 방법
이벤트 허브에 전송된 각 이벤트는 청구 가능한 메시지로 계산됩니다. *수신 이벤트* 는 64KB보다 작은 데이터 단위로 정의됩니다. 크기가 64KB 이하인 모든 이벤트는 하나의 청구 가능한 이벤트로 간주됩니다. 이벤트가 64KB보다 큰 경우 64KB의 배수로 이벤트 크기에 따라 청구 가능한 이벤트 수가 계산됩니다. 예를 들어 이벤트 허브에 전송된 8KB 이벤트는 하나의 이벤트로 청구되지만 이벤트 허브에 전송된 96KB 메시지는 두 개의 이벤트로 청구됩니다.

이벤트 허브에서 사용된 이벤트와 검사점 등의 제어 호출 및 관리 작업은 청구 가능한 수신 이벤트로 계산되지 않고 처리량 단위 허용 한도까지 누적됩니다.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>조정된 연결 요금이 Event Hubs에 적용됩니까?
AMQP 프로토콜을 사용하는 경우에 연결 요금이 적용됩니다. 시스템 또는 장치 수에 관계 없이 HTTP를 사용하여 이벤트를 보내는 데에는 연결 요금이 부과되지 않습니다. AMQP를 사용하려는 경우(예: 보다 효율적인 이벤트 스트리밍을 달성하거나 IoT 명령 및 제어 시나리오에서 양방향 통신 설정), 각 서비스 계층에 포함된 연결 수에 대한 자세한 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/) 페이지를 참조하세요.

### <a name="how-is-event-hubs-capture-billed"></a>Event Hubs 캡처는 어떻게 청구되나요?
캡처는 네임스페이스에 캡처 옵션이 설정된 이벤트 허브가 하나라도 있을 경우 사용됩니다. Event Hubs 캡처는 구매한 처리량 단위를 기준으로 시간당 청구됩니다. 처리량 단위 수가 증가하거나 감소하면 Event Hubs 캡처 청구 시 전체 증가 시간에 이러한 변경 내용이 반영됩니다. Event Hubs 캡처 청구에 대한 자세한 내용은 [Event Hubs 가격 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Event Hubs 캡처에 사용하도록 선택한 저장소 계정에 청구되나요?
이벤트 허브에서 설정된 경우 캡처는 사용자가 제공한 저장소 계정을 사용합니다. 이 계정은 사용자의 저장소 계정이므로 이 구성의 변경 내용은 Azure 구독에 청구됩니다.

## <a name="quotas"></a>할당량

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Event Hubs와 관련된 할당량이 있나요?
Event Hubs 할당량의 목록은 [할당량](event-hubs-quotas.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Event Hubs에 의해 생성된 일부 예외 및 해당 제안된 작업은 무엇인가요?
가능한 Event Hubs 예외의 목록은 [예외 개요](event-hubs-messaging-exceptions.md)를 참조하세요.

### <a name="diagnostic-logs"></a>진단 로그
Event Hubs는 두 가지 유형의 [진단 로그](event-hubs-diagnostic-logs.md)인 캡처 오류 로그와 작업 로그를 지원합니다. 두 로그 모두 json으로 표시되며 Azure Portal을 통해 설정할 수 있습니다.

### <a name="support-and-sla"></a>지원 및 SLA
Event Hubs에 대한 기술 지원은 [커뮤니티 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)을 통해 사용할 수 있습니다. 청구 및 구독 관리 지원은 무료로 제공됩니다.

SLA에 대한 자세한 내용에 대해 알아보려면 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs 자동 확장](event-hubs-auto-inflate.md)
