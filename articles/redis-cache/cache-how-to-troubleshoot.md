---
title: "Azure Redis Cache 문제를 해결하는 방법 | Microsoft Docs"
description: "Azure Redis Cache와 관련된 일반적인 문제를 해결하는 방법을 알아봅니다."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: e5f6f423697d90e889ebde2cd203891e34278b3c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Azure Redis Cache 문제를 해결하는 방법
이 문서에서는 다음 범주의 Azure Redis Cache 문제를 해결하는 것에 대한 지침을 제공합니다.

* [클라이언트 쪽 문제 해결](#client-side-troubleshooting) - 이 섹션에서는 Azure Redis Cache에 연결하는 응용 프로그램으로 인해 발생하는 문제를 파악하고 해결하는 것에 대한 지침을 제공합니다.
* [서버 쪽 문제 해결](#server-side-troubleshooting) - 이 섹션에서는 Azure Redis Cache 서버 쪽에서 일어난 문제를 파악하고 해결하는 것에 대한 지침을 제공합니다.
* [StackExchange.Redis 시간 제한 예외 조사](#stackexchangeredis-timeout-exceptions) -이 섹션에서는 StackExchange.Redis 클라이언트를 사용할 때의 문제 해결에 관한 정보를 제공합니다.

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
> 
> 

## <a name="client-side-troubleshooting"></a>클라이언트 쪽 문제 해결
이 섹션에서는 클라이언트 응용 프로그램에 부가된 조건 때문에 발생하는 문제 해결에 대해 알아봅니다.

* [클라이언트 쪽의 메모리 부족](#memory-pressure-on-the-client)
* [트래픽 폭주](#burst-of-traffic)
* [클라이언트의 높은 CPU 사용량](#high-client-cpu-usage)
* [클라이언트 쪽 대역폭 초과](#client-side-bandwidth-exceeded)
* [큰 요청/응답 크기](#large-requestresponse-size)
* [내 Redis 데이터에서 무엇이 변경되었나요?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>클라이언트 쪽의 메모리 부족
#### <a name="problem"></a>문제
클라이언트 컴퓨터의 메모리 부족은 Redis 인스턴스에서 지연없이 보낸 데이터 처리를 지연시킬 수 있는 온갖 종류의 성능 문제를 일으킵니다. 메모리가 부족해지면 시스템은 일반적으로 데이터를 실제 메모리로부터 디스크에 있는 가상 메모리로 페이지합니다. 이 *페이지 폴트* 가 시스템이 크게 느려진 원인입니다.

#### <a name="measurement"></a>측정
1. 사용 가능한 메모리를 초과하지 않는지 확인하기 위해 컴퓨터에서의 메모리 사용량을 모니터링합니다. 
2. `Page Faults/Sec` 성능 카운터를 모니터링합니다. 대부분의 시스템은 정상 작동을 하는 중에도 약간의 페이지 폴트가 있으므로 시간 제한에 해당하는 이 페이지 폴트 성능 카운터에서의 급증을 확인합니다.

#### <a name="resolution"></a>해결 방법
클라이언트를 더 많은 메모를 가진 더 큰 클라이언트 VM 크기로 그레이드하거나 메모리 소비를 줄이기 위해 메모리 사용 패턴을 자세히 알아봅니다.

### <a name="burst-of-traffic"></a>트래픽 폭주
#### <a name="problem"></a>문제
나쁜 `ThreadPool` 설정과 결합된 트래픽 폭주는 Redis 서버에서 이미 보냈으나 클라이언트 쪽에서 아직 소비되지 않은 데이타의 처리 지연이 발생할 수 있습니다.

#### <a name="measurement"></a>측정
[다음과 같은](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) 코드를 사용할 때 `ThreadPool` 통계가 시간에 따라 어떻게 바뀌는지 모니터링합니다. 또한 StackExchange.Redis에서 `TimeoutException` 메시지를 살펴볼 수 있습니다. 다음은 예제입니다.

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

위의 메시지에는 몇 가지 흥미로운 문제가 있습니다.

1. `IOCP` 섹션과 `WORKER` 섹션에 `Min` 값보다 큰 `Busy` 값이 있습니다. 이러한 차이는 `ThreadPool` 설정이 조정을 필요로 한다는 것을 의미입니다.
2. 또한 `in: 64221`도 볼 수 있습니다: 이 값은 64211 바이트를 커널 소켓 계층에서 받았지만 응용 프로그램 (예: StackExchange.Redis)에서 아직 읽지 않았음을 나타냅니다. 이 차이는 일반적으로 응용 프로그램이 데이터를 서버에서 보내는 만큼 빠르게 서버로부터 읽지 못하고 있음을 의미합니다.

#### <a name="resolution"></a>해결 방법
버스트 시나리오 하에서 스레드 풀이 신속하게 규모 확장을 하도록 [스레드 풀 설정](https://gist.github.com/JonCole/e65411214030f0d823cb)을 구성합니다.

### <a name="high-client-cpu-usage"></a>클라이언트의 높은 CPU 사용량
#### <a name="problem"></a>문제
클라이언트 쪽에서의 높은 CPU 사용량은 수행하도록 요청된 작업을 시스템이 처리할 수 없음을 나타냅니다. 이 상황은 Redis가 응답을 신속하게 전송하더라도 클라이언트가 Redis의 응답을 적시에 처리하지 못할 수도 있음을 의미합니다.

#### <a name="measurement"></a>측정
Azure Portal을 통해 또는 연결된 성능 카운터를 통해 시스템 전반 CPU 사용량을 모니터링합니다. 단일 프로세스는 CUP 사용량이 낮은 동시에 전체 시스템 CPU 사룡량을 높을 수 있기 때문에 *프로세스* CPU를 모니터링 하지 않도록 주의합니다. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다. 높은 CPU 사용량으로 인해 [트래픽 폭주](#burst-of-traffic) 섹션에서 설명한 대로 `TimeoutException` 오류 메시지에서 높은 `in: XXX` 값을 보게 될 것입니다.

> [!NOTE]
> StackExchange.Redis 1.1.603 이상은 `TimeoutException` 오류 메시지에 `local-cpu` 매트릭을 포함합니다. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
> 
> 

#### <a name="resolution"></a>해결 방법
더 많은 CPU 용량을 가진 더 큰 VM 크기로 업그레이드하거나 CPU 스파이크의 원인이 무엇인지 확인합니다. 

### <a name="client-side-bandwidth-exceeded"></a>클라이언트 쪽 대역폭 초과
#### <a name="problem"></a>문제
클라이언트 컴퓨터의 아키텍처에 따라, 클라이언트 컴퓨터는 어느 정도의 네트워크 대역폭을 사용할 수 있는지에 제한이 있습니다. 클라이언트가 네트워크 용량을 오버로드하여 사용 가능한 대역폭을 초과하면 서버에서 보내는 만큼 신속하게 클라이언트 쪽에서 데이터가 처리되지 않습니다. 이 경우 시간이 초과될 수 있습니다.

#### <a name="measurement"></a>측정
[다음과 같은](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)코드를 사용할 때 대역폭 사용량이 시간에 따라 어떻게 바뀌는지 모니터링합니다. 이 코드는 (Azure 웹 사이트와 같이) 권한이 제한된 일부 환경에서 성공적으로 실행되지 않을 수도 있습니다.

#### <a name="resolution"></a>해결 방법
클라이언트 VM 크기를 늘리거나 네트워크 대역폭 사용량을 줄입니다.

### <a name="large-requestresponse-size"></a>큰 요청/응답 크기
#### <a name="problem"></a>문제
큰 요청/응답으로 시간 초과가 발생할 수 있습니다. 예를 들어 클라이언트에 구성된 시간 제한 값이 1초라고 가정합시다. 응용 프로그램이 동시에 2개의 키(예: 'A' 및 'B')를 요청합니다(동일한 실제 네트워크 연결 사용). 대부분 클라이언트는 두 요청 ‘A’와 ‘B’를 응답을 기다리지 않고 연달아 서버에 전송되는 것과 같은 요청 “파이프라인”을 지원합니다. 서버는 응답을 동일한 순서로 전송합니다. 응답 'A'가 큰 경우 후속 요청을 위한 제한 시간 대부분을 다 써버릴 수 있습니다. 

다음 예제에서는 이 시나리오를 보여 줍니다. 이 시나리오에서는 요청 'A'와 'B'가 신속하게 전송되고, 서버가 응답 'A'와 'B'를 신속하게 보내기 시작하지만, 데이터 전송 시간 때문에 서버가 신속하게 응답했어도 'B'가 다른 요청 뒤에 갇혀 꼼짝 못하고 시간이 초과되어 버립니다.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>측정
이 요청/응답은 측정하기 어렵습니다. 기본적으로 큰 요청 및 응답을 추적하도록 클라이언트 코드를 계측해야 합니다. 

#### <a name="resolution"></a>해결 방법
1. Redis는 몇 개의 큰 값보다는 많은 수의 작은 값에 대해 최적화됩니다. 선호하는 해결 방법은 데이터를 더 작은 값으로 분할하는 것입니다. 왜 더 작은 값이 추천되는지에 대한 상세한 내용은 [redis를 위한 이상적인 값 크기 범위는 무엇입니까? 100KB는 너무 큰가요?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) 게시물을 참조하세요.
2. 더 높은 대역폭 기능을 얻어 더 큰 응답에 대한 데이터 전송 시간을 줄이기 위해 (클라이언트와 Redis 캐시 서버에 대해) VM의 크기를 늘립니다. 서버에만 또는 클라이언트에만 대역폭을 늘리는 것은 충분하지 않을 수도 있음을 유의하세요. 대역폭 사용량을 측정하고 현재 VM 크기의 용량과 비교합니다.
3. 사용하는 `ConnectionMultiplexer` 개체의 수와 다른 연결을 통해 라운드 로빈 요청을 늘립니다.

### <a name="what-happened-to-my-data-in-redis"></a>내 Redis 데이터에서 무엇이 변경되었나요?
#### <a name="problem"></a>문제
특정 데이터가 Azure Redis Cache 인스턴스에 있을 것이라 예상했지만 그곳에 없는 듯이 보입니다.

#### <a name="resolution"></a>해결 방법
가능한 원인 및 해결 방법은 [내 Redis 데이터에서 무엇이 변경되었나요?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 를 참조하세요.

## <a name="server-side-troubleshooting"></a>서버 쪽 문제 해결
이 섹션에서는 캐시 서버에 부가된 조건 때문에 발생하는 문제 해결에 대해 알아봅니다.

* [서버 쪽의 메모리 부족](#memory-pressure-on-the-server)
* [높은 CPU 사용량 / 서버 부하](#high-cpu-usage-server-load)
* [서버 쪽 대역폭 초과](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>서버 쪽의 메모리 부족
#### <a name="problem"></a>문제
서버 쪽의 메모리 부족은 요청 처리를 지연시킬 수 있는 온갖 종류의 성능 문제를 일으킵니다. 메모리가 부족해지면 시스템은 일반적으로 데이터를 실제 메모리로부터 디스크에 있는 가상 메모리로 페이지합니다. 이 *페이지 폴트* 가 시스템이 크게 느려진 원인입니다. 이 메모리 부족의 몇 가지 가능한 원인은 다음과 같습니다. 

1. 캐시를 데이터로 최대 용량까지 채웠습니다. 
2. Redis가 높은 메모리 조각화를 보입니다-큰 개체 저장이 원인이 되어 일어나는 경우가 가장 많습니다.(Redis 작은 개체에 대해 최적화됩니다-상세한 내용은 [redis를 위한 이상적인 값 크기 범위는 무엇입니까? 100KB는 너무 큰가요?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) 게시물을 참조하세요.) 

#### <a name="measurement"></a>측정
Redis는 이 문제를 식별하는 데 도움이 되는 두 개의 메트릭을 노출합니다. 첫째는 `used_memory`이고 다른 하나는 `used_memory_rss`입니다. [이러한 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)은 Azure Portal에서 또는 [Redis INFO](http://redis.io/commands/info) 명령을 통해 사용할 수 있습니다.

#### <a name="resolution"></a>해결 방법
메모리 사용량의 정상 유지를 위해 몇 가지 가능한 변경은 다음과 같습니다.

1. [메모리 정책을 구성](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 하고 키에 만료 시간을 설정합니다. 조각화가 있을 경우 이 구성으로는 부족할 수 있습니다.
2. [maxmemory-reserved 값을 구성합니다](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) .
3. 크게 캐시된 개체를 더 작은 관련 개체로 분할합니다.
4. [확장합니다](cache-how-to-scale.md) .
5. [Redis 클러스터를 사용할 수 있는 프리미엄 캐시](cache-how-to-premium-clustering.md)를 사용하고 있다면, [분할된 데이터베이스 수를 늘릴](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache) 수 있습니다.

### <a name="high-cpu-usage--server-load"></a>높은 CPU 사용량 / 서버 부하
#### <a name="problem"></a>문제
높은 CPU 사용량은 Redis가 응답을 신속하게 전송하더라도 클라이언트 쪽이 Redis의 응답을 적시에 처리하지 못할 수도 있음을 의미할 수 있습니다.

#### <a name="measurement"></a>측정
Azure Portal을 통해 또는 연결된 성능 카운터를 통해 시스템 전반 CPU 사용량을 모니터링합니다. 단일 프로세스는 CUP 사용량이 낮은 동시에 전체 시스템 CPU 사룡량을 높을 수 있기 때문에 *프로세스* CPU를 모니터링 하지 않도록 주의합니다. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다.

#### <a name="resolution"></a>해결 방법
* [Redis Cache Advisor](cache-configure.md#redis-cache-advisor)에 설명된 권장 사항 및 경고를 검토하세요.
* 또한 이 항목의 다른 권장 사항과 [Azure Redis에 대한 모범 사례](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f)를 검토하여 캐시 및 클라이언트를 좀 더 최적화하기 위한 모든 옵션을 사용했는지 확인하세요. 
* [Azure Redis Cache 성능](cache-faq.md#azure-redis-cache-performance) 차트를 검토하고 현재 계층의 상한 입계값에 거의 도달하고 있는지 알아보세요. 필요한 경우 더 많은 CPU 용량을 갖는 더 큰 캐시 계층으로 [크기를 조정](cache-how-to-scale.md)하세요. 프리미엄 계층을 이미 사용 중인 경우 [클러스터링으로 스케일 아웃](cache-how-to-premium-clustering.md)할 수 있습니다.


### <a name="server-side-bandwidth-exceeded"></a>서버 쪽 대역폭 초과
#### <a name="problem"></a>문제
캐시 인스턴스의 크기에 따라, 클라이언트 컴퓨터는 어느 정도의 네트워크 대역폭을 사용할 수 있는지에 제한이 있습니다. 서버가 사용 가능한 대역폭을 초과하면 데이터가 클라이언트에게 신속하게 전송되지 않습니다. 이 경우 시간이 초과될 수 있습니다.

#### <a name="measurement"></a>측정
지정한 보고 간격 동안 캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양인 `Cache Read` 메트릭을 모니터링할 수 있습니다. 이 값은 캐시에서 사용되는 네트워크 대역폭에 해당합니다. 서버 쪽 네트워크 대역폭 제한에 대한 경고를 설정하려면, `Cache Read` 카운터를 사용하여 경고를 생성합니다. 여러분의 판독값을 다양한 캐시 가격 책정 계층 및 크기에 대해 관찰된 대역폭 제한은 [이 테이블](cache-faq.md#cache-performance) 의 값과 비교합니다.

#### <a name="resolution"></a>해결 방법
가격 책정 계층과 캐시 크기에 대해 관찰된 최대 대역폭에 지속적으로 접근한다면 [이 테이블](cache-faq.md#cache-performance)의 값을 가이드로 사용하여 더 큰 네트워크 대역폭을 가진 가격 책정 계층 또는 크기로 [확장](cache-how-to-scale.md)하는 것을 고려합니다.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 시간 제한 예외 조사
StackExchange.Redis는 기본값이 1000ms인 동기 작업에 대해 `synctimeout`이라는 이름의 구성 설정을 사용합니다. 동기 호출이 규정된 시간 내에 완료되지 않으면, StackExchange.Redis 클라이언트는 다음 예제와 유사하게 시간 초과 오류 메시지를 냅니다.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


이 오류 메시지에는 문제의 원인과 가능한 해결로 이끌 메트릭이 들어 있습니다. 다음 테이블에는 오류 메시지 메트릭에 대한 세부 정보가 있습니다.

| 오류 메시지 메트릭 | 세부 정보 |
| --- | --- |
| inst |마지막 시간 조각에서: 0 명령이 발급되었습니다 |
| mgr |소켓 관리자가 OS에게 해야 할 일이 있는 소켓을 나타내게 하고자 `socket.select`을 수행합니다. 기본적으로: 판독기는 해야 할 일이 없다고 생각하기 때문에 네트워크에서 적극적으로 읽지 않습니다. |
| 큐 |진행 중인 작업이 총 73건 있습니다. |
| qu |진행 중인 작업의 6건이 보내지 않은 큐에 있고 아직 아웃바운드 네트워크에 작성되지 않았습니다. |
| qs |진행 중인 작업의 67건은 서버로 보내졌지만 아직 응답이 없습니다. 응답은 `Not yet sent by the server` 또는 `sent by the server but not yet processed by the client.`일 수 있습니다. |
| qc |진행 중인 작업의 0건이 응답을 보았으나 완료 루프에서 대기하는 까닭에 아직 완료로 표시되지 않았습니다. |
| wr |활성 기록기가 있습니다(보내지 않은 요청 6건이 무시되지 않았음을 의미합니다)바이트/activewriters |
| in |활성 판독기가 없으며 NIC 바이트/activereaders에서 판독하는 데 사용 가능한 바이트는 0입니다. |

### <a name="steps-to-investigate"></a>조사 단계
1. 모범 사례로 StackExchange.Redis 클리이언트를 사용할 때 연결하기 위해 다음 패턴을 사용하는지 확인합니다.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ````

    자세한 내용은 [StackExchange.Redis를 사용하여 캐시에 연결](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)을 참조하세요.

1. Azure Redis Cache와 클라이언트 응용 프로그램이 Azure에서 동일한 지역에 있는지 확인합니다. 예를 들어 캐시는 미국 동부에 있는 반면에 클라이언트는 미국 서부에 있고 요청이 `synctimeout` 간격 내에 완료되지 않을 때 시간 초과가 일어날 수 있거나 또는 로컬 개발 컴퓨터에서 디버깅을 할 때 시관 초과가 일어날 수 있습니다. 
   
    동일한 Azure 지역에 캐시와 클라이언트를 두도록 하는 것이 좋습니다. 지역 간 호출을 포함하는 시나리오가 있다면, `synctimeout` 속성을 연결 문자열에 포함함으로써 `synctimeout` 간격을 기본값인 1000ms 간격보다 높은 값에 설정해야 합니다. 다음 예제는 StackExchange.Redis 캐시 연결 문자열을 2000ms의 `synctimeout` 과 연결하는 코드 조각을 보여줍니다.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
3. 서버나 클라이언트에 걸린 대역폭 제한에 묶인 요청이 있다면, 완료하는 데 더 많은 시간이 걸려 시간 초과의 원인이 됩니다. 시간 초과가 서버의 네트워크 대역폭 때문인지 보려면 [서버 쪽 대역폭 초과](#server-side-bandwidth-exceeded)를 참조하세요. 시간 초과가 클라이언트 네트워크 대역폭 때문인지 보려면 [클라이언트 쪽 대역폭 초과](#client-side-bandwidth-exceeded)를 참조하세요.
4. CPU가 서버 또는 클라이언트에 바인딩됩니까?
   
   * CPU가 클라이언트에 바인딩되어 요청이 `synctimeout` 간격 내에 처리될 수가 없게 되어 시간 초과가 일어나는지 확인합니다. 더 큰 클라이언트 크기로 이동하거나 부하를 분산한다면 이 문제를 제어하는데 도움이 됩니다. 
   * 서버에서 CPU가 서버에 바인딩되는지 `CPU` [캐시 성능 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 모니터링하여 확인합니다. Redis가 CPU에 바인딩된 동안 들어오는 요청은 시간 초과를 초래할 수 있습니다. 이 상태를 해결하기 위해 부하를 프리미엄 캐시에서 여러 분할된 데이터베이스로 분산하거나 더 큰 크기나 가격 책정 계층으로 업그레이드할 수 있습니다. 자세한 내용은 [서버 쪽 대역폭 초과](#server-side-bandwidth-exceeded)를 참조합니다.
5. 서버에서 처리하는데 시간이 오래 걸리는 명령이 있습니까? Redis 서버에서 처리하는 데 시간이 오래 걸리는 장기 실행 명령은 시간 초과의 원인이 될 수 있습니다. 장기 실행 명령의 몇 가지 예로 다수의 키 작동을 요하는 `mget`, `keys *` 또는 형편없게 짜여진 lua 스크립트 등이 있습니다. 예상보다 오래 걸리는 요청이 있는지 확인하기 위해 Redis cli 클라이언트를 사용하여 Azure Redis Cache 인스턴스에 연결하거나 [Redis 콘솔](cache-configure.md#redis-console)을 사용하여 [SlowLog](http://redis.io/commands/slowlog) 명령을 실행할 수 있습니다. Redis 서버와 StackExchange.Redis는 작은 수의 큰 요청보다 많은 수의 작은 요청을 위해 최적화되어 있습니다. 데이터를 더 작은 청크로 분할한다면 다음을 향상시킵니다. 
   
    Redis cli 및 stunnel를 사용하여 Azure Redis 캐시 SSL 끝점에 연결 하는 방법에 대한 자세한 내용은 [Redis용 ASP.NET 세션 상태 공급자 미리 보기 릴리스 발표](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 블로그 게시물을 참조하세요. 자세한 내용은 [SlowLog](http://redis.io/commands/slowlog)를 참조하세요.
6. 높은 Redis 서버 부하로 시간 초과가 발생할 수 있습니다. `Redis Server Load` [캐시 성능 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 모니터링함으로써 서버 부하를 모니터링할 수 있습니다. 100(최대 값)이란 서버 부하는 redis 서버가 요청을 처리하느라 유휴 시간이 전혀 없이 사용 중이었음을 나타냅니다. 특정 요청이 서버 기능의 전부를 차지하는지 확인하려면 이전 단락에서 설명한 대로 SlowLog 명령을 실행합니다. 자세한 내용은 [높은 CPU 사용량/서버 로드](#high-cpu-usage-server-load)를 참조하세요.
7. 클라이언트 쪽에서 네트워크 문제를 일으킬 만한 이벤트가 있었습니까? 클라이언트 인스턴스 수를 위아래로 조정하거나, 클라이언트 새 버전을 배포하거나, 자동 크기 조정을 사용하도록 설정되었는지 클라이언트(웹, 작업자 역할 또는 Iaas VM)를 확인합니다.우리 테스트에서 자동 크기 조정이나 위/아래 크기 조정을 하면 아웃바운드 네트워크 연결이 몇초간 끊어진다는 점이 밝혀졌습니다. StackExchange.Redis 코드는 이러한 이벤트에 복원력이 있어 다시 연결합니다. 다시 연결되는 이 시간 동안 큐에 있는 모든 요청은 시간 초과될 수 있습니다.
8. 시간 초과한 Redis 캐시에 여러 작은 요청에 앞서 큰 요청이 있었습니까? 오류 메시지에 있는 매개 변수 `qs`는 클라이언트에서 서버로 요청은 보내졌으나 아직 응답 처리가 되지 않은 경우가 얼마나 많은지 알려줍니다. StackExchange.Redis는 하나의 TCP 연결을 사용하고 한 번에 하나의 응답만 읽을 수 있기 때문에 이 값은 계속 증가할 수 있습니다. 첫 번째 작업이 시간 초과하더라도, 서버와 데이터를 주고 받는 일이 중단되지 않으며, 다른 요청은 큰 요청이 끝날 때까지 차단되기 때문에 시간 초과가 일어납니다. 하나의 솔루션은 워크로드를 감당할 수 있게 캐시를 충분히 크게 하고 큰 값을 작은 청크로 분할하여 시간 초과의 가능성을 최소화하는 것입니다. 또 다른 가능한 솔루션은 클라이언트에서 `ConnectionMultiplexer` 개체 풀을 사용하고, 새 요청을 보낼 때 부하가 최소인 `ConnectionMultiplexer`을 선택합니다, 이렇게 하면 단일 시간 초과가 다른 요청들도 또한 시간 초과되게 하는 것을 막습니다.
9. `RedisSessionStateprovider`보다 높아야 합니다. `retrytimeoutInMilliseconds`는 `operationTimeoutinMilliseonds`보다 높아야 합니다. 그렇지 않으면 재시도가 발생하지 않습니다. 다음 예제에서 `retrytimeoutInMilliseconds` 가 3000으로 설정됩니다. 자세한 내용은 [Azure Redis Cache용 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md) 및 [세션 상태 제공자와 출력 캐시 공급자의 구성 매개 변수를 사용하는 방법](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)을 참조하세요.

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. `Used Memory RSS`와 `Used Memory`를 [모니터링](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)하여Azure Redis Cache 서버의 메모리 사용량을 확인합니다. 제거 정책이 구현되었다면, `Used_Memory` 가 캐시 크기에 도달할 때 Redis는 제거 키를 작동합니다. 이상적으로 `Used Memory RSS`는 `Used memory`보다 약간만 높아야 합니다. 큰 차이는 메모리 조각화 (내부 또는 외부)가 심함을 의미합니다. `Used Memory RSS`가 `Used Memory`보다 작다면 운영 체제가 캐시 메모리의 일부를 스왑했음을 의미합니다. 이 스왑이 발생하는 경우 일부 상당한 대기 시간을 예상할 수 있습니다. Redis는 할당이 메모리 페이지에 매핑되는 방법을 제어하지 않기 때문에 높은 `Used Memory RSS` 은 흔히 메모리 사용량에서 스파이크의 결과로 나타납니다. Redis가 메모리를 놓아주면, 메모리는 할당자에게 돌아가고, 할당자는 메모리를 시스템에 돌려주거나 그러지 않을 수 있습니다. 운영 체제가 보고하는 `Used Memory` 값과 메모리 소비량 사이에 차이가 있을 수 있습니다. Redis가 메모리를 사용되고 사용하고 놓아줬지만 시스템으로 돌려지지 않았습니다. 메모리 문제를 완화하려면 다음 방법들을 수행할 수 있습니다.
   
   * 캐시를 더 큰 크기로 업그레이드하여 시스템에서 메모리 제한을 겪지 않도록 합니다.
   * 키에 만료 시간을 설정하여 이전 값이 사전에 제거되게 합니다.
   * `used_memory_rss` 캐시 메트릭을 모니터링합니다. 이 값이 해당 캐시의 크기에 가까워질 때 성능 문제가 나타나는 것을 보기 시작할 가능성이 있습니다. 프리미엄 캐시를 사용하거나 더 큰 캐시 크기로 업그레이드 하는 경우, 데이터를 여러 분할된 데이터베이스에 배포합니다.
   
   자세한 내용은 [서버 쪽의 메모리 부족](#memory-pressure-on-the-server)을 참조하세요.

## <a name="additional-information"></a>추가 정보
* [어떤 Redis Cache 제품 및 크기를 사용해야 하나요?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)
* [Azure Redis Cache를 모니터링하는 방법](cache-how-to-monitor.md)

