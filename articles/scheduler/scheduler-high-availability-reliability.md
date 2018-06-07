---
title: "스케줄러 고가용성 및 안정성"
description: "스케줄러 고가용성 및 안정성"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-high-availability-and-reliability"></a>스케줄러 고가용성 및 안정성
## <a name="azure-scheduler-high-availability"></a>Azure 스케줄러 고가용성 
핵심 Azure 플랫폼 서비스인 Azure 스케줄러는 가용성이 높으며 지역 중복 서비스 배포와 지리적 지역 작업 복제를 제공합니다.

### <a name="geo-redundant-service-deployment"></a>지리적 중복 서비스 배포
Azure 스케줄러는 현재 Azure에 있는 거의 모든 지리적 지역에서 UI를 통해 사용할 수 있습니다. Azure 스케줄러에서 사용할 수 있는 지역 목록은 [여기에 있습니다](https://azure.microsoft.com/regions/#services). 호스팅되는 지역의 데이터 센터가 사용할 수 없게 렌더링될 경우 Azure 스케줄러의 장애 조치 기능을 통해 다른 데이터 센터에서 해당 서비스를 제공할 수 있습니다.

### <a name="geo-regional-job-replication"></a>지리적 지역 작업 복제
관리 요청에 대해 Azure 스케줄러 프론트엔드 외에 자체 작업도 지리적으로 복제됩니다. 한 지역에서 정전이 발생할 경우 Azure 스케줄러가 장애 조치를 통해 해당 작업이 쌍을 이루는 지리적 지역에 있는 다른 데이터 센터에서 실행될 수 있게 합니다.

예: 미국 중남부에서 만든 작업을 Azure 스케줄러가 미국 중북부에서 자동으로 복제합니다. 미국 중남부에 장애가 발생하면 Azure 스케줄러가 이 작업을 미국 중북부에서 실행되게 합니다. 

![][1]

결과적으로, Azure 스케줄러는 Azure에서 오류가 발생할 경우 데이터가 동일한 광범위 지역 안에 유지되게 합니다. 즉 Azure 스케줄러가 작업에 대해 자동으로 고가용성 기능을 제공하므로 단지 고가용성을 목적으로 작업을 복제할 필요가 없습니다.

## <a name="azure-scheduler-reliability"></a>Azure 스케줄러 안정성
Azure 스케줄러는 자체 고가용성을 보장하며 사용자가 생성한 작업에 대해서 다른 방식을 적용합니다. 예를 들어, 사용자 작업에서 사용 불가능한 HTTP 끝점을 호출할 수 있습니다. 어쨌든 Azure 스케줄러는 장애 처리를 위한 대안을 제시하여 작업을 성공적으로 실행하려고 합니다. Azure 스케줄러는 두 가지 방법으로 이를 수행합니다.

### <a name="configurable-retry-policy-via-retrypolicy"></a>“retryPolicy”를 통해 구성 가능한 재시도 정책
Azure 스케줄러에서는 재시도 정책을 구성할 수 있습니다. 기본적으로, 작업이 실패하면 스케줄러는 30초 간격으로 4회 더 작업을 시도합니다. 더 적극적이거나(예: 30초 간격 10회) 더  소극적인(예: 매일 2회) 재시도 정책을 재구성할 수 있습니다.

예를 들어, 매주 한 번 실행되고 HTTP 끝점을 호출하는 작업을 만든 경우 이것이 도움이 될 수 있습니다. 작업을 실행하는데 HTTP 끝점이 몇 시간 동안 다운된 경우, 기본 재시도 정책이 실패할 것이므로 작업을 다시 실행하기 위해 1주일을 더 기다리고 싶지는 않을 것입니다. 이러한 경우 30초 간격 대신 3시간(예) 간격으로 표준 재시도 정책을 재구성할 수 있습니다.

재시도 정책을 구성하는 방법은 [retryPolicy](scheduler-concepts-terms.md#retrypolicy)를 참조하십시오.

### <a name="alternate-endpoint-configurability-via-erroraction"></a>“errorAction”을 통한 대체 끝점 구성
Azure 스케줄러 작업에 대한 대상 끝점이 연결 불가 상태로 있을 경우 Azure 스케줄러는 재시도 정책을 따른 후 대체 오류 처리 끝점으로 장애 전환을 수행합니다. 대체 오류 처리 끝점을 구성한 경우 Azure 스케줄러가 이를 호출합니다. 대체 끝점을 지정하면 장애를 고려했을 때 자체 작업의 가용성이 높아집니다.

한 예로 아래 표에서는 Azure 스케줄러가 재시도 정책에 따라 뉴욕 웹 서비스를 히트합니다. 재시도 실패 후에는 대체 항목이 있는지 확인합니다. 그런 다음 진행하여 동일한 재시도 정책으로 대체 항목에 대한 요청을 시작합니다.

![][2]

원래 동작과 대체 오류 동작 모두에 동일한 재시도 정책이 적용됩니다. 또한 대체 오류 동작의 동작 유형이 주 동작의 동작 유형과 다를 수도 있습니다. 예를 들어 주 동작은 HTTP 끝점을 호출하는 것이면서 오류 동작은 오류 로깅을 수행하는 저장소 큐, 서비스 버스 큐 또는 서비스 버스 토픽 동작이 될 수 있습니다.

대체 끝점을 구성하는 방법은 [errorAction](scheduler-concepts-terms.md#action-and-erroraction)을 참조하세요.

## <a name="see-also"></a>참고 항목
 [스케줄러란?](scheduler-intro.md)

 [Azure 스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)

 [Azure 스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure 스케줄러 PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure 스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [Azure 스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
