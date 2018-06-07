---
title: Azure Redis Cache란? | Microsoft Docs
description: Azure Redis Cache란 무엇이며 일반적으로 어떻게 사용되는지에 대해 알아봅니다.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 8f477282e49104e9b034e11656ff50c2a67545f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-redis-cache"></a>Azure Redis Cache란?

Azure Redis Cache는 인기 있는 오픈 소스 [Redis Cache](https://redis.io/)를 기반으로 하며, 일반적으로 백 엔드 데이터 저장소에 크게 의존하는 시스템의 성능 및 확장성을 개선하기 위해 캐시로 사용됩니다. 성능 향상을 위해 자주 액세스하는 데이터를 응용 프로그램 가까이에 있는 빠른 저장소에 일시적으로 복사합니다. [Redis Cache](https://redis.io/)를 사용하면 이 빠른 저장소는 데이터베이스에 의해 디스크에서 로드되는 대신 메모리 내 Redis Cache에 위치합니다.

Azure Redis Cache는 메모리 내 데이터 구조 저장소, 분산된 비관계형 데이터베이스 및 메시지 브로커로 사용할 수 있습니다. Redis 엔진의 낮은 대기 시간의 처리량이 높은 성능을 이용하여 응용 프로그램 성능이 향상됩니다.

Azure Redis Cache는 Microsoft에서 관리하고, Azure 내에서 호스팅되며, Azure 내 모든 응용 프로그램에 액세스할 수 있는 안전한 전용 Redis Cache에 대한 액세스를 사용자에게 제공합니다.

## <a name="why-use-azure-redis-cache"></a>Azure Redis Cache를 사용하는 이유

응용 프로그램 아키텍처를 지원하거나 응용 프로그램의 성능을 개선하기 위해 Redis Cache를 사용하는 많은 일반적인 패턴이 있습니다. 가장 일반적인 패턴의 일부는 다음과 같습니다.

| 패턴      | 설명                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | 데이터베이스가 커질 수 있으므로 전체 데이터베이스를 캐시에 로드하는 방법은 권장되지 않습니다. [캐시 배제](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) 패턴을 사용하여 필요할 때만 캐시에 데이터 항목을 로드하는 것이 일반적입니다. 시스템에서 백 엔드 데이터를 변경하는 경우, 바로 그때 다른 클라이언트로 배포되는 캐시를 업데이트할 수 있습니다. 또한 시스템에서 데이터 항목에 대한 만료 날짜를 설정하거나 제거 정책을 사용하여 데이터 업데이트가 캐시로 다시 로드되도록 할 수 있습니다.|
| [콘텐츠 캐시](cache-aspnet-output-cache-provider.md) | 대부분의 웹 페이지는 헤더, 바닥글, 도구 모음, 메뉴 등으로 이루어진 템플릿에서 생성됩니다. 실제로 자주 변경하지 않으며 동적으로 생성되지 않아야 합니다. Azure Redis Cache와 같은 메모리 내 캐시를 사용하면 백 엔드 데이터 저장소에 비해 사용자의 웹 서버는 이러한 유형의 정적 콘텐츠에 빠르게 액세스할 수 있습니다. 이 패턴은 콘텐츠를 동적으로 생성하는 데 필요한 처리 시간과 서버 로드를 줄일 수 있습니다. 그러면 웹 서버는 더 잘 대응할 수 있게 되고, 사용자는 부하를 처리하는 데 필요한 서버 수를 줄일 수 있습니다. Azure Redis Cache는 ASP.NET을 사용하여 이 패턴을 지원할 수 있도록 Redis 출력 캐시 공급자를 제공합니다.|
| [사용자 세션 캐싱](cache-aspnet-session-state-provider.md) | 이 패턴은 일반적으로 웹 응용 프로그램에서 사용자 쿠키와 연결할 수 있는 쇼핑 카트 및 다른 사용자 기록 입력 정보와 함께 사용됩니다. 쿠키에 너무 많이 저장하면 쿠키 크기가 증가하고 모든 요청에서 전달 및 유효성 검사가 수행되므로 성능이 저하될 수 있습니다. 일반적인 해결책은 쿠키를 키로 사용하여 백 엔드 데이터베이스에 데이터를 쿼리하는 것입니다. Azure Redis Cache와 같은 메모리 내 캐시를 사용하면 전체 관계형 데이터베이스와의 상호 작용보다 훨씬 더 빠르게 사용자와 정보를 연결할 수 있습니다. |
| 작업 및 메시지 큐 | 응용 프로그램이 요청을 수신하면 요청과 연결된 작업을 실행하는 데 추가 시간이 걸기는 경우가 많습니다. 나중에 처리되고 다른 서버에서 처리될 가능성이 있는 작업을 큐에 추가하여 더 길게 실행되는 작업을 지연하는 일반적인 패턴입니다. 이러한 작업 지연 메서드를 태스크 큐라고 합니다. 태스크 큐를 지원하도록 설계된 다양한 소프트웨어 구성 요소가 있습니다. Redis Cache도 이 목적을 위해 배포된 큐로 제공됩니다.|
| 분산 트랜잭션 | 응용 프로그램이 단일 작업(원자성)으로 백 엔드 데이터 저장소에 대한 일련의 명령을 실행하기 위해 필요한 일반적인 요구 사항입니다. 모든 명령은 성공하거나 모두 초기 상태로 롤백되어야 합니다. Redis Cache는 명령의 일괄 처리 실행을 [트랜잭션](https://redis.io/topics/transactions) 형식의 단일 작업으로 지원합니다. |

## <a name="azure-redis-cache-offerings"></a>Azure Redis Cache 기능

Azure Redis Cache는 다음과 같은 계층에서 사용할 수 있습니다.

| 계층 | 설명 |
|---|---|
Basic | 단일 노드 캐시. 이 계층은 여러 메모리 크기(250MB - 53GB)를 지원합니다. 이는 개발/테스트 및 중요하지 않은 작업에 이상적입니다. 기본 계층에는 SLA(서비스 수준 계약)가 없습니다. |
| Standard | 고가용성 SLA(99.9%)를 사용하며 Microsoft에서 관리되는 2노드 기본/보조 구성의 복제된 캐시입니다. |
| Premium | Premium 계층은 Enterprise 준비 계층입니다. Premium 계층 캐시는 더 많은 기능을 지원하며 더 낮은 대기 시간으로 더 많은 처리가 가능합니다. Premium 계층의 캐시는 Basic 또는 Standard 계층에 비해 더 나은 성능을 제공하는 더 강력한 하드웨어에 배포됩니다. 이러한 이점은 Standard 계층에 비해 Premium에서 동일한 크기의 캐시에 대한 처리량이 커짐을 의미합니다. |

> [!TIP]
> 프리미엄 캐시에서의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.
>

이미 만든 후에 더 높은 계층으로 사용자 캐시를 확장할 수 있습니다. 더 낮은 계층으로의 축소는 지원되지 않습니다. 단계별 크기 조정 지침에 대해서는 [Azure Redis Cache 크기를 조정하는 방법](cache-how-to-scale.md) 및 [크기 조정 작업을 자동화하는 방법](cache-how-to-scale.md#how-to-automate-a-scaling-operation)을 참조하세요.

### <a name="feature-comparision"></a>기능 비교

[Redis Cache 가격 책정](https://azure.microsoft.com/pricing/details/cache/) 페이지에서는 각 계층의 자세한 비교를 제공합니다. 다음 표는 계층별로 지원하는 기능 몇 가지를 설명하는 데 도움이 됩니다.

| 기능 설명 | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis 데이터 지속성](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis 클러스터](cache-how-to-premium-clustering.md) |✔|-|-|
| [방화벽 규칙을 통한 보안](cache-configure.md#firewall) |✔|✔|✔|
| [VNet으로 강화된 보안 및 격리](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [업데이트 예약](cache-administration.md#schedule-updates) |✔|-|-|
| [지역에서 복제](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>다음 단계

* [ASP.NET 웹앱 빠른 시작](cache-web-app-howto.md) Azure Redis Cache를 사용하는 간단한 ASP.NET 웹앱을 만듭니다.
* [.NET 빠른 시작](cache-dotnet-how-to-use-azure-redis-cache.md) Azure Redis Cache를 사용하는 .NET 앱을 만듭니다.
* [Node.js 빠른 시작](cache-nodejs-get-started.md) Azure Redis Cache를 사용하는 간단한 Node.js 앱을 만듭니다.
* [Java 빠른 시작](cache-java-get-started.md) Azure Redis Cache를 사용하는 간단한 Java 앱을 만듭니다.
* [Python 빠른 시작](cache-python-get-started.md) Azure Redis Cache를 사용하는 Python 앱을 만듭니다.