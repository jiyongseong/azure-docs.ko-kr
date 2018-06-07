---
title: Azure Event Hubs Dedicated 용량 개요 | Microsoft Docs
description: Azure Event Hubs Dedicated 용량에 대한 개요입니다.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 7009710328c96660accdcf9c88313ad92d25d41c
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

*Event Hubs Dedicated* 용량은 요구 사항이 가장 까다로운 고객을 위해 단일 테넌트 배포를 제공합니다. 전체 규모 Azure Event Hubs는 초당 2백만 개 이상의 이벤트 또는 초당 최대 2GB의 원격 분석을 수신할 수 있으며 완벽한 내구성의 저장소와 1초 미만의 대기 시간을 제공합니다. 또한 동일한 시스템에서 실시간 및 배치를 처리하여 통합 솔루션을 사용할 수 있습니다. 제품에 [Event Hubs 캡처](event-hubs-capture-overview.md)가 포함되어 있기 때문에 단일 스트림으로 실시간 및 배치 기반 파이프라인을 모두 지원하여 솔루션의 복잡성을 줄일 수 있습니다.

다음은 Event Hubs에 제공되는 서비스 계층을 비교한 표입니다. Standard는 대부분의 기능이 사용량에 따라 요금이 부과되는 반면 Event Hubs Dedicated 제품은 혼합 방식의 월 요금제가 적용됩니다. Dedicated 계층은 표준 요금제의 모든 기능을 제공합니다. 하지만 워크로드가 까다로운 고객을 위해 엔터프라이즈급 용량을 제공합니다. 

| 기능 | Standard | 전용 |
| --- |:---:|:---:|:---:|
| 수신 이벤트 | 100만 이벤트당 요금 부과 | 포함 |
| 처리량 단위(1MB/초 수신, 2MB/초 송신) | 시간당 요금 부과 | 포함됨 |
| 메시지 크기 | 256 KB | 1MB |
| 게시자 정책 | 예 | 예 |   
| 소비자 그룹 | 20 | 20 |
| 메시지 재생 | 예 | 예 |
| 최대 처리량 단위 | 20(100까지 유연하게 구성 가능)   | 1 CU(용량 단위) ≈ 50 |
| 조정된 연결 | 1,000개 포함 | 100,000개 포함 |
| 추가 조정된 연결 | 예 | 예 |
| 메시지 보존 | 1일 포함 | 최대 7일 포함 |
| 캡처 | 시간당 요금 부과 | 포함 |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Event Hubs Dedicated 용량의 이점

Event Hubs Dedicated를 사용하는 경우 다음과 같은 이점이 제공됩니다.

* 다른 테넌트의 노이즈가 없는 단일 테넌트 호스팅
* 메시지 크기가 256KB인 Standard에 비해 1MB로 증가
* 매번 반복 가능한 성능
* 폭발적인 요구를 충족하는 보장된 용량
* 마이크로 일괄 처리 및 장기 보존 통합을 제공하기 위해 Event Hubs의 [캡처](event-hubs-capture-overview.md) 기능을 포함합니다.
* 유지 관리 시간 없음: 서비스가 부하 분산, OS 업데이트, 보안 패치 및 분할을 관리합니다.
* 시간당 가격 고정
* 추가 비용 없이 메시지 보존 최대 7일

또한 Event Hubs Dedicated는 Standard 제품의 처리량 제한 중 일부를 제거합니다. 표준 계층의 처리량 단위는 초당 1000개 이벤트 또는 TU당 1MBps를 수신하고 그 두 배를 송신할 수 있습니다. Dedicated 제품은 수신 및 송신 이벤트 수에 제한이 없습니다. 이러한 제한은 구입한 이벤트 허브의 처리 용량에 따라서만 적용됩니다.

이 예약된 전담 환경은 이 계층에만 다음과 같이 고유한 다른 기능을 제공합니다.

* 클러스터에 있는 네임스페이스 수를 제어합니다.
* 각 네임스페이스의 처리량 제한을 지정합니다.
* 각 네임스페이스에서 Event Hubs 수를 구성합니다.
* 파티션 수에 대한 제한을 결정합니다.

이 서비스는 가장 규모가 큰 원격 분석 사용자를 대상으로 하며 기업 계약을 맺은 고객에게 제공됩니다.

## <a name="how-to-onboard"></a>등록 방법

언제든지 요구 사항에 맞게 CU를 추가하거나 제거하여 용량을 늘리거나 줄일 수 있습니다. Dedicated 요금제는 Event Hubs 제품 팀의 실습 온보딩을 통해 고객에게 적합하도록 유연한 배포가 가능하다는 고유의 장점이 있습니다. 이 SKU를 사용하려면 [청구 지원](https://ms.portal.azure.com/#create/Microsoft.Support) 또는 Microsoft 담당자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

Event Hubs Dedicated 용량에 대한 추가 정보를 얻으려면 Microsoft 영업 담당자 또는 Microsoft 지원에 문의하세요. 다음 링크에서도 Event Hubs 가격 책정 계층에 대한 자세한 내용을 확인할 수 있습니다.

- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Microsoft 영업 담당자 또는 Microsoft 지원 서비스에서 Event Hubs Dedicated 용량에 대한 추가 정보를 얻을 수도 있습니다.
- [Event Hubs FAQ](event-hubs-faq.md)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다. 
