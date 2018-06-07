---
title: Azure Monitor 메트릭 - 리소스 유형별 지원 메트릭 | Microsoft Docs
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
author: anirudhcavale
manager: ashwink
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ancav
ms.openlocfilehash: a5c0d529357d8ab92cf63ca4a0293753d55801d6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779093"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor에서 지원되는 메트릭
Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 다음은 현재 Azure Monitor의 메트릭 파이프라인을 통해 사용할 수 있는 모든 메트릭의 전체 목록입니다.  레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다. 아래 목록에는 통합 Azure Monitor 메트릭 파이프라인을 통해 사용할 수 있는 메트릭만 포함됩니다. 이러한 메트릭을 쿼리하고 액세스하려면 [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)을 사용하세요.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|qpu_metric|QPU|개수|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|ServerResourceType|
|memory_metric|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|ServerResourceType|
|TotalConnectionRequests|총 연결 요청 수|개수|평균|도착한 총 연결 요청 수입니다.|ServerResourceType|
|SuccessfullConnectionsPerSec|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|ServerResourceType|
|TotalConnectionFailures|총 연결 실패 수|개수|평균|실패한 총 연결 시도 수입니다.|ServerResourceType|
|CurrentUserSessions|현재 사용자 세션 수|개수|평균|현재 설정된 사용자 세션 수입니다.|ServerResourceType|
|QueryPoolBusyThreads|쿼리 풀의 사용 중인 스레드|개수|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolJobQueueLength|명령 풀의 작업 큐 길이|개수|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolJobQueueLength|처리 풀의 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|ServerResourceType|
|CurrentConnections|연결: 현재 연결|개수|평균|현재 설정된 클라이언트 연결 수입니다.|ServerResourceType|
|CleanerCurrentPrice|메모리: 클리너 현재 가격|개수|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|ServerResourceType|
|CleanerMemoryShrinkable|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|ServerResourceType|
|CleanerMemoryNonshrinkable|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|ServerResourceType|
|MemoryUsage|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 xVelocity 엔진 메모리 제한을 초과하여 xVelocity 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|ServerResourceType|
|MemoryLimitHard|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitHigh|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitLow|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitVertiPaq|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|ServerResourceType|
|할당량|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|ServerResourceType|
|QuotaBlocked|메모리: 차단된 할당량|개수|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|ServerResourceType|
|VertiPaqNonpaged|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|ServerResourceType|
|VertiPaqPaged|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|ServerResourceType|
|RowsReadPerSec|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|ServerResourceType|
|RowsConvertedPerSec|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|ServerResourceType|
|RowsWrittenPerSec|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|ServerResourceType|
|CommandPoolBusyThreads|스레드: 명령 풀 사용 중인 스레드|개수|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolIdleThreads|스레드: 명령 풀 유휴 상태 스레드|개수|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingBusyThreads|스레드: 긴 구문 분석 사용 중인 스레드|개수|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|LongParsingIdleThreads|스레드: 긴 구문 분석 유휴 상태 스레드|개수|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingJobQueueLength|스레드: 긴 구문 분석 작업 큐 길이|개수|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|스레드: 처리 풀 사용 중인 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|스레드: 처리 풀 사용 중인 비-I/O 스레드|개수|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|스레드: 처리 풀 I/O 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|스레드: 처리 풀 유휴 상태 비-I/O 스레드|개수|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolIdleThreads|스레드: 쿼리 풀 유휴 상태 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ShortParsingBusyThreads|스레드: 짧은 구문 분석 사용 중인 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|ShortParsingIdleThreads|스레드: 짧은 구문 분석 유휴 상태 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ShortParsingJobQueueLength|스레드: 짧은 구문 분석 작업 큐 길이|개수|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|ServerResourceType|
|mashup_engine_qpu_metric|M 엔진 QPU|개수|평균|매시업 엔진 프로세스별 QPU 사용량|ServerResourceType|
|mashup_engine_memory_metric|M 엔진 메모리|바이트|평균|매시업 엔진 프로세스별 메모리 사용량|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalRequests|총 게이트웨이 요청|개수|합계|게이트웨이 요청 수|위치, 호스트 이름|
|SuccessfulRequests|성공적인 게이트웨이 요청|개수|합계|성공적인 게이트웨이 요청 수|위치, 호스트 이름|
|UnauthorizedRequests|허가되지 않은 게이트웨이 요청|개수|합계|허가되지 않은 게이트웨이 요청 수|위치, 호스트 이름|
|FailedRequests|실패한 게이트웨이 요청|개수|합계|게이트웨이 요청 실패 수|위치, 호스트 이름|
|OtherRequests|기타 게이트웨이 요청|개수|합계|기타 게이트웨이 요청 수|위치, 호스트 이름|
|기간|게이트웨이 요청의 전체 기간|밀리초|평균|게이트웨이 요청의 전체 기간(밀리초)|위치, 호스트 이름|
|용량|용량(미리 보기)|백분율|평균|ApiManagement 서비스에 대한 사용률 메트릭|위치|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalJob|총 작업 수|개수|합계|총 작업 수|Runbook, 상태|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CoreCount|전용된 코어 수|개수|합계|배치 계정의 총 전용 코어 수|차원 없음|
|TotalNodeCount|전용된 노드 수|개수|합계|배치 계정의 총 전용 노드 수|차원 없음|
|LowPriorityCoreCount|LowPriority 코어 수|개수|합계|배치 계정의 우선 순위가 낮은 총 코어 수|차원 없음|
|TotalLowPriorityNodeCount|우선 순위가 낮은 노드 수|개수|합계|배치 계정의 우선 순위가 낮은 총 노드 수|차원 없음|
|CreatingNodeCount|노드 수 만들기|개수|합계|만든 노드 수|차원 없음|
|StartingNodeCount|시작 노드 수|개수|합계|시작 노드 수|차원 없음|
|WaitingForStartTaskNodeCount|작업 시작 대기 노드 수|개수|합계|시작 작업 완료를 기다리는 노드 수|차원 없음|
|StartTaskFailedNodeCount|시작 작업 실패 노드 수|개수|합계|시작 작업이 실패한 노드 수|차원 없음|
|IdleNodeCount|유휴 상태인 노드 수|개수|합계|유휴 노드 수|차원 없음|
|OfflineNodeCount|오프라인 노드 수|개수|합계|오프라인 노드 수|차원 없음|
|RebootingNodeCount|재부팅 노드 수|개수|합계|다시 부팅하는 노드의 수|차원 없음|
|ReimagingNodeCount|이미지로 다시 설치하는 노드 수|개수|합계|이미지로 다시 설치하는 노드의 수|차원 없음|
|RunningNodeCount|실행 노드 수|개수|합계|실행 중인 노드의 수|차원 없음|
|LeavingPoolNodeCount|나가는 풀 노드 수|개수|합계|풀을 나가는 노드 수|차원 없음|
|UnusableNodeCount|사용 불가 노드 수|개수|합계|사용할 수 없는 노드 수|차원 없음|
|PreemptedNodeCount|선점된 노드 수|개수|합계|선점된 노드 수|차원 없음|
|TaskStartEvent|작업 시작 이벤트|개수|합계|시작된 총 작업 수|차원 없음|
|TaskCompleteEvent|작업 완료 이벤트|개수|합계|완료된 총 작업 수|차원 없음|
|TaskFailEvent|작업 실패 이벤트|개수|합계|실패한 상태로 완료된 총 작업 수|차원 없음|
|PoolCreateEvent|풀 만들기 이벤트|개수|합계|만든 총 풀 수|차원 없음|
|PoolResizeStartEvent|풀 크기 조정 시작 이벤트|개수|합계|시작된 총 풀 크기 조정 작업 수|차원 없음|
|PoolResizeCompleteEvent|풀 크기 조정 완료 이벤트|개수|합계|완료된 총 풀 크기 조정 수|차원 없음|
|PoolDeleteStartEvent|풀 삭제 시작 이벤트|개수|합계|시작된 총 풀 삭제 수|차원 없음|
|PoolDeleteCompleteEvent|풀 삭제 완료 이벤트|개수|합계|완료된 총 풀 삭제 수|차원 없음|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|connectedclients|연결된 클라이언트|개수|최대||차원 없음|
|totalcommandsprocessed|총 작업|개수|합계||차원 없음|
|cachehits|캐시 적중|개수|합계||차원 없음|
|cachemisses|캐시 누락|개수|합계||차원 없음|
|getcommands|가져오기|개수|합계||차원 없음|
|setcommands|설정|개수|합계||차원 없음|
|operationsPerSecond|초당 작업|개수|합계||차원 없음|
|evictedkeys|제거된 키|개수|합계||차원 없음|
|totalkeys|전체 키|개수|최대||차원 없음|
|expiredkeys|만료된 키|개수|합계||차원 없음|
|usedmemory|사용된 메모리|바이트|최대||차원 없음|
|usedmemoryRss|사용된 메모리 RSS|바이트|최대||차원 없음|
|serverLoad|서버 부하|백분율|최대||차원 없음|
|cacheWrite|캐시 쓰기|초당 바이트 수|최대||차원 없음|
|cacheRead|캐시 읽기|초당 바이트 수|최대||차원 없음|
|percentProcessorTime|CPU|백분율|최대||차원 없음|
|connectedclients0|연결된 클라이언트(분할 0)|개수|최대||차원 없음|
|totalcommandsprocessed0|총 작업(분할 0)|개수|합계||차원 없음|
|cachehits0|캐시 적중(분할 0)|개수|합계||차원 없음|
|cachemisses0|캐시 누락(분할 0)|개수|합계||차원 없음|
|getcommands0|가져오기(분할 0)|개수|합계||차원 없음|
|setcommands0|설정(분할 0)|개수|합계||차원 없음|
|operationsPerSecond0|초당 작업(분할 0)|개수|합계||차원 없음|
|evictedkeys0|제거된 키(분할 0)|개수|합계||차원 없음|
|totalkeys0|총 키(분할 0)|개수|최대||차원 없음|
|expiredkeys0|만료된 키(분할 0)|개수|합계||차원 없음|
|usedmemory0|사용된 메모리(분할 0)|바이트|최대||차원 없음|
|usedmemoryRss0|사용된 메모리 RSS(분할 0)|바이트|최대||차원 없음|
|serverLoad0|서버 부하(분할 0)|백분율|최대||차원 없음|
|cacheWrite0|캐시 쓰기(분할 0)|초당 바이트 수|최대||차원 없음|
|cacheRead0|캐시 읽기(분할 0)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime0|CPU(분할 0)|백분율|최대||차원 없음|
|connectedclients1|연결된 클라이언트(분할 1)|개수|최대||차원 없음|
|totalcommandsprocessed1|총 작업(분할 1)|개수|합계||차원 없음|
|cachehits1|캐시 적중(분할 1)|개수|합계||차원 없음|
|cachemisses1|캐시 누락(분할 1)|개수|합계||차원 없음|
|getcommands1|가져오기(분할 1)|개수|합계||차원 없음|
|setcommands1|집합(분할 1)|개수|합계||차원 없음|
|operationsPerSecond1|초당 작업(분할 1)|개수|합계||차원 없음|
|evictedkeys1|제거된 키(분할 1)|개수|합계||차원 없음|
|totalkeys1|총 키(분할 1)|개수|최대||차원 없음|
|expiredkeys1|만료된 키(분할 1)|개수|합계||차원 없음|
|usedmemory1|사용된 메모리(분할 1)|바이트|최대||차원 없음|
|usedmemoryRss1|사용된 메모리 RSS(분할 1)|바이트|최대||차원 없음|
|serverLoad1|서버 부하(분할 1)|백분율|최대||차원 없음|
|cacheWrite1|캐시 쓰기(분할 1)|초당 바이트 수|최대||차원 없음|
|cacheRead1|캐시 읽기(분할 1)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime1|CPU(분할 1)|백분율|최대||차원 없음|
|connectedclients2|연결된 클라이언트(분할 2)|개수|최대||차원 없음|
|totalcommandsprocessed2|총 작업(분할 2)|개수|합계||차원 없음|
|cachehits2|캐시 적중(분할 2)|개수|합계||차원 없음|
|cachemisses2|캐시 누락(분할 2)|개수|합계||차원 없음|
|getcommands2|가져오기(분할 2)|개수|합계||차원 없음|
|setcommands2|설정(분할 2)|개수|합계||차원 없음|
|operationsPerSecond2|초당 작업(분할 2)|개수|합계||차원 없음|
|evictedkeys2|제거된 키(분할 2)|개수|합계||차원 없음|
|totalkeys2|총 키(분할 2)|개수|최대||차원 없음|
|expiredkeys2|만료된 키(분할 2)|개수|합계||차원 없음|
|usedmemory2|사용된 메모리(분할 2)|바이트|최대||차원 없음|
|usedmemoryRss2|사용된 메모리 RSS(분할 2)|바이트|최대||차원 없음|
|serverLoad2|서버 부하(분할 2)|백분율|최대||차원 없음|
|cacheWrite2|캐시 쓰기(분할 2)|초당 바이트 수|최대||차원 없음|
|cacheRead2|캐시 읽기(분할 2)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime2|CPU(분할 2)|백분율|최대||차원 없음|
|connectedclients3|연결된 클라이언트(분할 3)|개수|최대||차원 없음|
|totalcommandsprocessed3|총 작업(분할 3)|개수|합계||차원 없음|
|cachehits3|캐시 적중(분할 3)|개수|합계||차원 없음|
|cachemisses3|캐시 누락(분할 3)|개수|합계||차원 없음|
|getcommands3|가져오기(분할 3)|개수|합계||차원 없음|
|setcommands3|설정(분할 3)|개수|합계||차원 없음|
|operationsPerSecond3|초당 작업(분할 3)|개수|합계||차원 없음|
|evictedkeys3|제거된 키(분할 3)|개수|합계||차원 없음|
|totalkeys3|총 키(분할 3)|개수|최대||차원 없음|
|expiredkeys3|만료된 키(분할 3)|개수|합계||차원 없음|
|usedmemory3|사용된 메모리(분할 3)|바이트|최대||차원 없음|
|usedmemoryRss3|사용된 메모리 RSS(분할 3)|바이트|최대||차원 없음|
|serverLoad3|서버 부하(분할 3)|백분율|최대||차원 없음|
|cacheWrite3|캐시 쓰기(분할 3)|초당 바이트 수|최대||차원 없음|
|cacheRead3|캐시 읽기(분할 3)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime3|CPU(분할 3)|백분율|최대||차원 없음|
|connectedclients4|연결된 클라이언트(분할 4)|개수|최대||차원 없음|
|totalcommandsprocessed4|총 작업(분할 4)|개수|합계||차원 없음|
|cachehits4|캐시 적중(분할 4)|개수|합계||차원 없음|
|cachemisses4|캐시 누락(분할 4)|개수|합계||차원 없음|
|getcommands4|가져오기(분할 4)|개수|합계||차원 없음|
|setcommands4|설정(분할 4)|개수|합계||차원 없음|
|operationsPerSecond4|초당 작업(분할 4)|개수|합계||차원 없음|
|evictedkeys4|제거된 키(분할 4)|개수|합계||차원 없음|
|totalkeys4|총 키(분할 4)|개수|최대||차원 없음|
|expiredkeys4|만료된 키(분할 4)|개수|합계||차원 없음|
|usedmemory4|사용된 메모리(분할 4)|바이트|최대||차원 없음|
|usedmemoryRss4|사용된 메모리 RSS(분할 4)|바이트|최대||차원 없음|
|serverLoad4|서버 부하(분할 4)|백분율|최대||차원 없음|
|cacheWrite4|캐시 쓰기(분할 4)|초당 바이트 수|최대||차원 없음|
|cacheRead4|캐시 읽기(분할 4)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime4|CPU(분할 4)|백분율|최대||차원 없음|
|connectedclients5|연결된 클라이언트(분할 5)|개수|최대||차원 없음|
|totalcommandsprocessed5|총 작업(분할 5)|개수|합계||차원 없음|
|cachehits5|캐시 적중(분할 5)|개수|합계||차원 없음|
|cachemisses5|캐시 누락(분할 5)|개수|합계||차원 없음|
|getcommands5|가져오기(분할 5)|개수|합계||차원 없음|
|setcommands5|설정(분할 5)|개수|합계||차원 없음|
|operationsPerSecond5|초당 작업(분할 5)|개수|합계||차원 없음|
|evictedkeys5|제거된 키(분할 5)|개수|합계||차원 없음|
|totalkeys5|총 키(분할 5)|개수|최대||차원 없음|
|expiredkeys5|만료된 키(분할 5)|개수|합계||차원 없음|
|usedmemory5|사용된 메모리(분할 5)|바이트|최대||차원 없음|
|usedmemoryRss5|사용된 메모리 RSS(분할 5)|바이트|최대||차원 없음|
|serverLoad5|서버 부하(분할 5)|백분율|최대||차원 없음|
|cacheWrite5|캐시 쓰기(분할 5)|초당 바이트 수|최대||차원 없음|
|cacheRead5|캐시 읽기(분할 5)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime5|CPU(분할 5)|백분율|최대||차원 없음|
|connectedclients6|연결된 클라이언트(분할 6)|개수|최대||차원 없음|
|totalcommandsprocessed6|총 작업(분할 6)|개수|합계||차원 없음|
|cachehits6|캐시 적중(분할 6)|개수|합계||차원 없음|
|cachemisses6|캐시 누락(분할 6)|개수|합계||차원 없음|
|getcommands6|가져오기(분할 6)|개수|합계||차원 없음|
|setcommands6|설정(분할 6)|개수|합계||차원 없음|
|operationsPerSecond6|초당 작업(분할 6)|개수|합계||차원 없음|
|evictedkeys6|제거된 키(분할 6)|개수|합계||차원 없음|
|totalkeys6|총 키(분할 6)|개수|최대||차원 없음|
|expiredkeys6|만료된 키(분할 6)|개수|합계||차원 없음|
|usedmemory6|사용된 메모리(분할 6)|바이트|최대||차원 없음|
|usedmemoryRss6|사용된 메모리 RSS(분할 6)|바이트|최대||차원 없음|
|serverLoad6|서버 부하(분할 6)|백분율|최대||차원 없음|
|cacheWrite6|캐시 쓰기(분할 6)|초당 바이트 수|최대||차원 없음|
|cacheRead6|캐시 읽기(분할 6)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime6|CPU(분할 6)|백분율|최대||차원 없음|
|connectedclients7|연결된 클라이언트(분할 7)|개수|최대||차원 없음|
|totalcommandsprocessed7|총 작업(분할 7)|개수|합계||차원 없음|
|cachehits7|캐시 적중(분할 7)|개수|합계||차원 없음|
|cachemisses7|캐시 누락(분할 7)|개수|합계||차원 없음|
|getcommands7|가져오기(분할 7)|개수|합계||차원 없음|
|setcommands7|설정(분할 7)|개수|합계||차원 없음|
|operationsPerSecond7|초당 작업(분할 7)|개수|합계||차원 없음|
|evictedkeys7|제거된 키(분할 7)|개수|합계||차원 없음|
|totalkeys7|총 키(분할 7)|개수|최대||차원 없음|
|expiredkeys7|만료된 키(분할 7)|개수|합계||차원 없음|
|usedmemory7|사용된 메모리(분할 7)|바이트|최대||차원 없음|
|usedmemoryRss7|사용된 메모리 RSS(분할 7)|바이트|최대||차원 없음|
|serverLoad7|서버 부하(분할 7)|백분율|최대||차원 없음|
|cacheWrite7|캐시 쓰기(분할 7)|초당 바이트 수|최대||차원 없음|
|cacheRead7|캐시 읽기(분할 7)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime7|CPU(분할 7)|백분율|최대||차원 없음|
|connectedclients8|연결된 클라이언트(분할 8)|개수|최대||차원 없음|
|totalcommandsprocessed8|총 작업(분할 8)|개수|합계||차원 없음|
|cachehits8|캐시 적중(분할 8)|개수|합계||차원 없음|
|cachemisses8|캐시 누락(분할 8)|개수|합계||차원 없음|
|getcommands8|가져오기(분할 8)|개수|합계||차원 없음|
|setcommands8|설정(분할 8)|개수|합계||차원 없음|
|operationsPerSecond8|초당 작업(분할 8)|개수|합계||차원 없음|
|evictedkeys8|제거된 키(분할 8)|개수|합계||차원 없음|
|totalkeys8|총 키(분할 8)|개수|최대||차원 없음|
|expiredkeys8|만료된 키(분할 8)|개수|합계||차원 없음|
|usedmemory8|사용된 메모리(분할 8)|바이트|최대||차원 없음|
|usedmemoryRss8|사용된 메모리 RSS(분할 8)|바이트|최대||차원 없음|
|serverLoad8|서버 부하(분할 8)|백분율|최대||차원 없음|
|cacheWrite8|캐시 쓰기(분할 8)|초당 바이트 수|최대||차원 없음|
|cacheRead8|캐시 읽기(분할 8)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime8|CPU(분할 8)|백분율|최대||차원 없음|
|connectedclients9|연결된 클라이언트(분할 9)|개수|최대||차원 없음|
|totalcommandsprocessed9|총 작업(분할 9)|개수|합계||차원 없음|
|cachehits9|캐시 적중(분할 9)|개수|합계||차원 없음|
|cachemisses9|캐시 누락(분할 9)|개수|합계||차원 없음|
|getcommands9|가져오기(분할 9)|개수|합계||차원 없음|
|setcommands9|설정(분할 9)|개수|합계||차원 없음|
|operationsPerSecond9|초당 작업(분할 9)|개수|합계||차원 없음|
|evictedkeys9|제거된 키(분할 9)|개수|합계||차원 없음|
|totalkeys9|총 키(분할 9)|개수|최대||차원 없음|
|expiredkeys9|만료된 키(분할 9)|개수|합계||차원 없음|
|usedmemory9|사용된 메모리(분할 9)|바이트|최대||차원 없음|
|usedmemoryRss9|사용된 메모리 RSS(분할 9)|바이트|최대||차원 없음|
|serverLoad9|서버 부하(분할 9)|백분율|최대||차원 없음|
|cacheWrite9|캐시 쓰기(분할 9)|초당 바이트 수|최대||차원 없음|
|cacheRead9|캐시 읽기(분할 9)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime9|CPU(분할 9)|백분율|최대||차원 없음|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 계산 단위의 백분율.|차원 없음|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|차원 없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|차원 없음|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|차원 없음|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|차원 없음|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 계산 단위의 백분율.|차원 없음|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|차원 없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|차원 없음|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|차원 없음|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|차원 없음|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalCalls|총 호출|개수|합계|총 호출 수.|차원 없음|
|SuccessfulCalls|성공한 호출|개수|합계|성공한 호출 수입니다.|차원 없음|
|TotalErrors|총 오류|개수|합계|오류 응답(HTTP 응답 코드 4xx 또는 5xx)이 있는 총 호출 수입니다.|차원 없음|
|BlockedCalls|차단된 호출|개수|합계|요금 또는 할당량 한도를 초과한 호출 수입니다.|차원 없음|
|ServerErrors|서버 오류|개수|합계|서비스 내부 오류(HTTP 응답 코드 5xx)가 있는 호출 수입니다.|차원 없음|
|ClientErrors|클라이언트 오류|개수|합계|클라이언트 쪽 오류(HTTP 응답 코드 4xx)가 있는 호출 수입니다.|차원 없음|
|DataIn|데이터 입력|바이트|합계|들어오는 데이터 크기(바이트)입니다.|차원 없음|
|DataOut|데이터 출력|바이트|합계|나가는 데이터 크기(바이트)입니다.|차원 없음|
|대기 시간|대기 시간|밀리초|평균|대기 시간(밀리초)입니다.|차원 없음|
|CharactersTranslated|변환된 문자|개수|합계|들어오는 텍스트 요청에 있는 문자의 총 수입니다.|차원 없음|
|SpeechSessionDuration|음성 세션 기간|초|합계|음성 세션의 총 기간(초)입니다.|차원 없음|
|TotalTransactions|총 트랜잭션|개수|합계|총 트랜잭션 수|차원 없음|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 계산 단위의 백분율|차원 없음|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 총 바이트|차원 없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 총 바이트|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|남은 CPU 크레딧|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 총 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에 쓴 총 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽는 동안 완료한 총 IOPS|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에 쓰는 동안 완료한 총 IOPS|SlotId|
|디스크당 QD|데이터 디스크 QD(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 총 바이트/초|차원 없음|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 총 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽는 동안 완료한 총 IOPS|차원 없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓰는 동안 완료한 총 IOPS|차원 없음|
|디스크당 OS QD|OS 디스크 QD(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 계산 단위의 백분율|차원 없음|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 총 바이트|차원 없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 총 바이트|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|남은 CPU 크레딧|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 총 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에 쓴 총 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽는 동안 완료한 총 IOPS|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에 쓰는 동안 완료한 총 IOPS|SlotId|
|디스크당 QD|데이터 디스크 QD(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 바이트/초|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 총 바이트/초|차원 없음|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 총 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽는 동안 완료한 총 IOPS|차원 없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓰는 동안 완료한 총 IOPS|차원 없음|
|디스크당 OS QD|OS 디스크 QD(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 계산 단위의 백분율|차원 없음|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 총 바이트|차원 없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 총 바이트|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|남은 CPU 크레딧|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 총 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에 쓴 총 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽는 동안 완료한 총 IOPS|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에 쓰는 동안 완료한 총 IOPS|SlotId|
|디스크당 QD|데이터 디스크 QD(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 총 바이트/초|차원 없음|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 총 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽는 동안 완료한 총 IOPS|차원 없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓰는 동안 완료한 총 IOPS|차원 없음|
|디스크당 OS QD|OS 디스크 QD(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuUsage|CPU 사용량|개수|평균|모든 코어의 CPU 사용량(밀리코어)|containerName|
|MemoryUsage|메모리 사용량|바이트|평균|총 메모리 사용량(바이트)|containerName|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|개수|합계|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|차원 없음|
|kube_node_status_allocatable_memory_bytes|관리 클러스터에서 사용 가능한 총 메모리 양|바이트|합계|관리 클러스터에서 사용 가능한 총 메모리 양|차원 없음|
|kube_pod_status_ready|준비 상태인 Pod 수|개수|합계|준비 상태인 Pod 수|네임스페이스, Pod|
|kube_node_status_condition|다양한 노드 조건에 대한 상태|개수|합계|다양한 노드 조건에 대한 상태|조건, 상태, 노드|
|kube_pod_status_phase|단계별 Pod 수|개수|합계|단계별 Pod 수|단계, 네임스페이스, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API 호출 수|개수|합계||차원 없음|
|DCIMappingImportOperationSuccessfulLines|매핑 가져오기 작업 성공 줄 수|개수|합계||차원 없음|
|DCIMappingImportOperationFailedLines|매핑 가져오기 작업 실패 줄 수|개수|합계||차원 없음|
|DCIMappingImportOperationTotalLines|매핑 가져오기 작업 총 줄 수|개수|합계||차원 없음|
|DCIMappingImportOperationRuntimeInSeconds|매핑 가져오기 작업 런타임(초)|초|합계||차원 없음|
|DCIOutboundProfileExportSucceeded|아웃바운드 프로필 내보내기 성공|개수|합계||차원 없음|
|DCIOutboundProfileExportFailed|아웃바운드 프로필 내보내기 실패|개수|합계||차원 없음|
|DCIOutboundProfileExportDuration|아웃바운드 프로필 내보내기 기간|초|합계||차원 없음|
|DCIOutboundKpiExportSucceeded|아웃바운드 Kpi 내보내기 성공|개수|합계||차원 없음|
|DCIOutboundKpiExportFailed|아웃바운드 Kpi 내보내기 실패|개수|합계||차원 없음|
|DCIOutboundKpiExportDuration|아웃바운드 Kpi 내보내기 기간|초|합계||차원 없음|
|DCIOutboundKpiExportStarted|아웃바운드 Kpi 내보내기 시작|초|합계||차원 없음|
|DCIOutboundKpiRecordCount|아웃바운드 Kpi 레코드 수|초|합계||차원 없음|
|DCIOutboundProfileExportCount|아웃바운드 프로필 내보내기 수|초|합계||차원 없음|
|DCIOutboundInitialProfileExportFailed|아웃바운드 초기 프로필 내보내기 실패|초|합계||차원 없음|
|DCIOutboundInitialProfileExportSucceeded|아웃바운드 초기 프로필 내보내기 성공|초|합계||차원 없음|
|DCIOutboundInitialKpiExportFailed|아웃바운드 초기 Kpi 내보내기 실패|초|합계||차원 없음|
|DCIOutboundInitialKpiExportSucceeded|아웃바운드 초기 Kpi 내보내기 성공|초|합계||차원 없음|
|DCIOutboundInitialProfileExportDurationInSeconds|아웃바운드 초기 프로필 내보내기 기간(초)|초|합계||차원 없음|
|AdlaJobForStandardKpiFailed|표준 Kpi에 대한 Adla 작업 실패(초)|초|합계||차원 없음|
|AdlaJobForStandardKpiTimeOut|표준 Kpi에 대한 Adla 작업 시간 제한(초)|초|합계||차원 없음|
|AdlaJobForStandardKpiCompleted|표준 Kpi에 대한 Adla 작업 완료(초)|초|합계||차원 없음|
|ImportASAValuesFailed|ASA 값 가져오기 실패 수|개수|합계||차원 없음|
|ImportASAValuesSucceeded|ASA 값 가져오기 성공 수|개수|합계||차원 없음|
|DCIProfilesCount|프로필 인스턴스 수|개수|마지막||차원 없음|
|DCIInteractionsPerMonthCount|개월당 상호 작용|개수|마지막||차원 없음|
|DCIKpisCount|KPI 수|개수|마지막||차원 없음|
|DCISegmentsCount|세그먼트 수|개수|마지막||차원 없음|
|DCIPredictiveMatchPoliciesCount|예측 일치 수|개수|마지막||차원 없음|
|DCIPredictionsCount|예측 수|개수|마지막||차원 없음|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|FailedRuns|실패한 실행|개수|합계||pipelineName, activityName, windowEnd, windowStart|
|SuccessfulRuns|성공한 실행|개수|합계||pipelineName, activityName, windowEnd, windowStart|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|PipelineFailedRuns|실패한 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|PipelineSucceededRuns|성공한 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|ActivityFailedRuns|실패한 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|성공한 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|실패한 트리거 실행 메트릭|개수|합계||Name, FailureType|
|TriggerSucceededRuns|성공한 트리거 실행 메트릭|개수|합계||Name, FailureType|
|IntegrationRuntimeCpuPercentage|통합 런타임 CPU 사용률|백분율|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|통합 런타임 사용 가능한 메모리|바이트|평균||IntegrationRuntimeName, NodeName|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|JobEndedSuccess|성공한 작업|개수|합계|성공한 작업의 수입니다.|차원 없음|
|JobEndedFailure|실패한 작업|개수|합계|실패한 작업 수입니다.|차원 없음|
|JobEndedCancelled|취소된 작업|개수|합계|취소된 작업 수입니다.|차원 없음|
|JobAUEndedSuccess|성공한 AU 시간|초|합계|성공한 작업에 대한 총 AU 시간입니다.|차원 없음|
|JobAUEndedFailure|실패한 AU 시간|초|합계|실패한 작업에 대한 총 AU 시간입니다.|차원 없음|
|JobAUEndedCancelled|취소된 AU 시간|초|합계|취소된 작업에 대한 총 AU 시간입니다.|차원 없음|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalStorage|총 저장소|바이트|최대|계정에 저장된 총 데이터 양.|차원 없음|
|DataWritten|기록된 데이터|바이트|합계|계정에 기록된 총 데이터 양.|차원 없음|
|DataRead|데이터 읽기|바이트|합계|계정에서 읽어 온 총 데이터 양.|차원 없음|
|WriteRequests|쓰기 요청|개수|합계|계정에 데이터 쓰기 요청 수.|차원 없음|
|ReadRequests|읽기 요청|개수|합계|계정에 데이터 읽기 요청 수.|차원 없음|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|차원 없음|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|차원 없음|
|storage_limit|저장소 제한|바이트|평균|저장소 제한|차원 없음|
|serverlog_storage_percent|서버 로그 저장소 비율|백분율|평균|서버 로그 저장소 비율|차원 없음|
|serverlog_storage_percent|사용된 서버 로그 저장소|바이트|평균|사용된 서버 로그 저장소|차원 없음|
|serverlog_storage_limit|서버 로그 저장소 제한|바이트|평균|서버 로그 저장소 제한|차원 없음|
|active_connections|전체 활성 연결|개수|평균|전체 활성 연결|차원 없음|
|connections_failed|실패한 전체 연결|개수|합계|실패한 전체 연결|차원 없음|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|차원 없음|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|차원 없음|
|storage_limit|저장소 제한|바이트|평균|저장소 제한|차원 없음|
|serverlog_storage_percent|서버 로그 저장소 비율|백분율|평균|서버 로그 저장소 비율|차원 없음|
|serverlog_storage_percent|사용된 서버 로그 저장소|바이트|평균|사용된 서버 로그 저장소|차원 없음|
|serverlog_storage_limit|서버 로그 저장소 제한|바이트|평균|서버 로그 저장소 제한|차원 없음|
|active_connections|전체 활성 연결|개수|합계|전체 활성 연결|차원 없음|
|connections_failed|실패한 전체 연결|개수|합계|실패한 전체 연결|차원 없음|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 장치-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.telemetry.ingress.success|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 장치-클라우드 원격 분석 메시지 수|차원 없음|
|c2d.commands.egress.complete.success|완료된 명령|개수|합계|장치에서 성공적으로 완료한 클라우드-장치 명령 수|차원 없음|
|c2d.commands.egress.abandon.success|중단된 명령|개수|합계|장치에서 중단한 클라우드-장치 명령 수|차원 없음|
|c2d.commands.egress.reject.success|거부된 명령|개수|합계|장치에서 거부한 클라우드-장치 명령 수|차원 없음|
|devices.totalDevices|총 장치|개수|합계|IoT 허브에 등록된 장치 수|차원 없음|
|devices.connectedDevices.allProtocol|연결된 장치|개수|합계|IoT 허브에 연결된 장치 수|차원 없음|
|d2c.telemetry.egress.success|배달된 원격 분석 메시지|개수|합계|메시지가 끝점에 성공적으로 작성된 횟수(전체)|차원 없음|
|d2c.telemetry.egress.dropped|삭제된 메시지|개수|합계|배달 끝점이 쓸모가 없어졌기 때문에 삭제된 메시지 수|차원 없음|
|d2c.telemetry.egress.orphaned|분리된 메시지|개수|합계|대체 경로를 포함하여 경로가 일치하지 않는 메시지 수|차원 없음|
|d2c.telemetry.egress.invalid|잘못된 메시지|개수|합계|끝점과 호환되지 않아서 배달되지 않은 메시지 수|차원 없음|
|d2c.telemetry.egress.fallback|대체(fallback) 조건과 일치하는 메시지|개수|합계|대체(fallback) 끝점에 작성된 메시지 수|차원 없음|
|d2c.endpoints.egress.eventHubs|이벤트 허브 끝점에 배달된 메시지|개수|합계|메시지가 이벤트 허브 끝점에 성공적으로 작성된 횟수|차원 없음|
|d2c.endpoints.latency.eventHubs|이벤트 허브 끝점에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초)|차원 없음|
|d2c.endpoints.egress.serviceBusQueues|Service Bus 큐 끝점에 배달된 메시지|개수|합계|메시지가 Service Bus 큐 끝점에 성공적으로 작성된 횟수|차원 없음|
|d2c.endpoints.latency.serviceBusQueues|Service Bus 큐 끝점에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초)|차원 없음|
|d2c.endpoints.egress.serviceBusTopics|Service Bus 토픽 끝점에 배달된 메시지|개수|합계|메시지가 Service Bus 토픽 끝점에 성공적으로 작성된 횟수|차원 없음|
|d2c.endpoints.latency.serviceBusTopics|Service Bus 항목 끝점에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초)|차원 없음|
|d2c.endpoints.egress.builtIn.events|기본 제공 끝점에 배달된 메시지(메시지/이벤트)|개수|합계|메시지가 기본 제공 끝점에 성공적으로 작성된 횟수(메시지/이벤트)|차원 없음|
|d2c.endpoints.latency.builtIn.events|기본 제공 끝점에 대한 메시지 대기 시간(메시지/이벤트)|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 끝점(메시지.이벤트)에 대한 메시지 수신 간의 평균 대기 시간(밀리초) |차원 없음|
|d2c.endpoints.egress.storage|저장소 끝점에 배달된 메시지|개수|합계|메시지가 저장소 끝점에 성공적으로 기록된 횟수|차원 없음|
|d2c.endpoints.latency.storage|저장소 끝점에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 저장소 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초)|차원 없음|
|d2c.endpoints.egress.storage.bytes|저장소에 기록된 데이터|바이트|합계|저장소 끝점에 기록된 데이터 양(바이트)|차원 없음|
|d2c.endpoints.egress.storage.blobs|저장소에 기록된 Blob|개수|합계|저장소 끝점에 기록된 Blob 수|차원 없음|
|d2c.twin.read.success|장치에서의 성공한 쌍 읽기|개수|합계|성공한 모든 장치 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.read.failure|장치에서의 실패한 쌍 읽기|개수|합계|실패한 모든 장치 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.read.size|장치에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 장치 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|d2c.twin.update.success|장치에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 장치 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c.twin.update.failure|장치에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 장치 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c.twin.update.size|장치에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 장치 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|c2d.methods.success|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.methods.failure|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.methods.requestSize|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.responseSize|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.twin.read.success|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.read.failure|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.read.size|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|c2d.twin.update.success|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d.twin.update.failure|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d.twin.update.size|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|twinQueries.success|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries.failure|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries.resultSize|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|차원 없음|
|jobs.createTwinUpdateJob.success|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.failure|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.success|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.failure|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.listJobs.success|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.listJobs.failure|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.cancelJob.success|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.cancelJob.failure|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.success|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.failure|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.completed|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|차원 없음|
|jobs.failed|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|차원 없음|
|d2c.telemetry.ingress.sendThrottle|제한 오류 수|개수|합계|장치 처리량 제한으로 인한 제한 오류 수|차원 없음|
|dailyMessageQuotaUsed|사용된 전체 메시지 수|개수|평균|오늘 사용된 전체 메시지 수입니다. 매일 00:00 UTC에 0으로 다시 설정되는 누적 값입니다.|차원 없음|
|deviceDataUsage|총 장치 데이터 사용|개수|합계|IotHub에 연결된 모든 장치에서 전송된 바이트|차원 없음|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|RegistrationAttempts|등록 시도|개수|합계|장치 등록 시도 수|ProvisioningServiceName, IotHubName, 상태|
|DeviceAssignments|할당된 장치|개수|합계|IoT Hub에 할당된 장치 수|ProvisioningServiceName, IotHubName|
|AttestationAttempts|증명 시도|개수|합계|장치 증명 시도 수|ProvisioningServiceName, 상태, 프로토콜|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|MetadataRequests|메타데이터 요청|개수|개수|메타데이터 요청 수. Cosmos DB는 컬렉션, 데이터베이스 등과 해당 구성을 무료로 열거할 수 있는 각 계정에 대한 시스템 메타데이터 컬렉션을 유지 관리합니다.|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|MongoRequestCharge|Mongo 요청 요금|개수|합계|사용된 Mongo 요청 단위|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo 요청|개수|개수|생성된 Mongo 요청 수|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|TotalRequestUnits|총 요청 단위|개수|합계|사용된 요청 단위|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|TotalRequests|총 요청 수|개수|개수|요청 수|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청(미리 보기)|개수|합계|Microsoft.EventHub에 대한 성공한 요청. (미리 보기)|EntityName|
|ServerErrors|서버 오류. (미리 보기)|개수|합계|Microsoft.EventHub에 대한 서버 오류. (미리 보기)|EntityName|
|UserErrors|사용자 오류. (미리 보기)|개수|합계|Microsoft.EventHub에 대한 사용자 오류. (미리 보기)|EntityName|
|QuotaExceededErrors|할당량 초과 오류. (미리 보기)|개수|합계|Microsoft.EventHub에 대한 할당량 초과 오류. (미리 보기)|EntityName|
|ThrottledRequests|제한된 요청. (미리 보기)|개수|합계|Microsoft.EventHub에 대한 제한된 요청. (미리 보기)|EntityName|
|IncomingRequests|들어오는 요청(미리 보기)|개수|합계|Microsoft.EventHub에 대한 들어오는 요청. (미리 보기)|EntityName|
|IncomingMessages|들어오는 메시지(미리 보기)|개수|합계|Microsoft.EventHub에 대한 들어오는 메시지. (미리 보기)|EntityName|
|OutgoingMessages|보내는 메시지(미리 보기)|개수|합계|Microsoft.EventHub에 대한 보내는 메시지. (미리 보기)|EntityName|
|IncomingBytes|들어오는 바이트 (미리 보기)|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트. (미리 보기)|EntityName|
|OutgoingBytes|보내는 바이트 (미리 보기)|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트. (미리 보기)|EntityName|
|ActiveConnections|활성 연결(미리 보기)|개수|평균|Microsoft.EventHub에 대한 총 활성 연결. (미리 보기)|차원 없음|
|ConnectionsOpened|열린 연결. (미리 보기)|개수|평균|Microsoft.EventHub에 대한 열린 연결. (미리 보기)|EntityName|
|ConnectionsClosed|끊어진 연결. (미리 보기)|개수|평균|Microsoft.EventHub에 대한 끊어진 연결. (미리 보기)|EntityName|
|CaptureBacklog|캡처 백로그. (미리 보기)|개수|합계|Microsoft.EventHub에 대한 캡처 백로그. (미리 보기)|EntityName|
|CapturedMessages|캡처된 메시지. (미리 보기)|개수|합계|Microsoft.EventHub에 대한 캡처된 메시지. (미리 보기)|EntityName|
|CapturedBytes|캡처된 바이트. (미리 보기)|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트. (미리 보기)|EntityName|
|크기|크기(미리 보기)|바이트|평균|EventHub 크기(바이트) (미리 보기)|EntityName|
|INREQS|들어오는 요청|개수|합계|네임스페이스에 대한 총 들어오는 전송 요청|차원 없음|
|SUCCREQ|성공한 요청|개수|합계|네임스페이스에 대한 총 성공한 요청|차원 없음|
|FAILREQ|실패한 요청|개수|합계|네임스페이스에 대한 총 실패한 요청|차원 없음|
|SVRBSY|서버 작업 중 오류|개수|합계|네임스페이스에 대한 총 서버 작업 중 오류|차원 없음|
|INTERR|내부 서버 오류|개수|합계|네임스페이스에 대한 총 내부 서버 오류|차원 없음|
|MISCERR|다른 오류|개수|합계|네임스페이스에 대한 총 실패한 요청|차원 없음|
|INMSGS|들어오는 메시지(사용되지 않음)|개수|합계|네임스페이스에 들어오는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 들어오는 메시지 메트릭을 사용하세요.|차원 없음|
|EHINMSGS|들어오는 메시지 |개수|합계|네임스페이스에 대한 총 들어오는 메시지|차원 없음|
|OUTMSGS|보내는 메시지(사용되지 않음)|개수|합계|네임스페이스에 보내는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 보내는 메시지 메트릭을 사용하세요.|차원 없음|
|EHOUTMSGS|보내는 메시지 |개수|합계|네임스페이스에 대한 총 나가는 메시지|차원 없음|
|EHINMBS|들어오는 바이트(사용되지 않음)|바이트|합계|네임스페이스에 들어오는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 들어오는 바이트 메트릭을 사용하세요.|차원 없음|
|EHINBYTES|들어오는 바이트|바이트|합계|Event Hub 네임스페이스에 대한 들어오는 메시지 처리량|차원 없음|
|EHOUTMBS|보내는 바이트(사용되지 않음)|바이트|합계|네임스페이스에 보내는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 보내는 바이트 메트릭을 사용하세요.|차원 없음|
|EHOUTBYTES|나가는 바이트|바이트|합계|네임스페이스에 보내는 Event Hub 메시지 처리량|차원 없음|
|EHABL|백로그 메시지 보관|개수|합계|Event Hub 네임스페이스에 대한 백로그에 메시지 보관|차원 없음|
|EHAMSGS|메시지 보관|개수|합계|Event Hub 네임스페이스에 보관된 메시지|차원 없음|
|EHAMBS|메시지 보관 처리량|바이트|합계|Event Hub 네임스페이스에서 보관된 메시지 처리량|차원 없음|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|GatewayRequests|게이트웨이 요청|개수|합계|게이트웨이 요청 수|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|분류된 게이트웨이 요청|개수|합계|범주별 게이트웨이 요청 수(1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ObservedMetricValue|관찰된 메트릭 값|개수|평균|실행될 때 자동 크기 조정에서 계산된 값|MetricTriggerSource|
|MetricThreshold|메트릭 임계값|개수|평균|자동 크기 조정이 실행되었을 때 구성된 자동 크기 조정 임계값입니다.|MetricTriggerRule|
|ObservedCapacity|관찰된 용량|개수|평균|실행될 때 자동 크기 조정을 위해 보고된 용량입니다.|차원 없음|
|ScaleActionsInitiated|시작된 크기 조정 작업|개수|합계|크기 조정 작업의 방향입니다.|ScaleDirection|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ServiceApiHit|Service API 총 방문 횟수|개수|개수|Service API의 총 방문 횟수|ActivityType, ActivityName|
|ServiceApiLatency|전체 Service API 대기 시간|밀리초|평균|Service API 요청의 전체 대기 시간|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Service API 총 결과|개수|개수|Service API의 총 결과 수|ActivityType, ActivityName, StatusCode|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|대기 시간|대기 시간|밀리초|평균|API 호출 기간|OperationName, OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|RunsStarted|실행 시작됨|개수|합계|실행 시작된 워크플로 수|차원 없음|
|RunsCompleted|실행 완료됨|개수|합계|실행 완료된 워크플로 수|차원 없음|
|RunsSucceeded|실행 성공함|개수|합계|실행 성공한 워크플로 수|차원 없음|
|RunsFailed|실행 실패함|개수|합계|실행 실패한 워크플로 수|차원 없음|
|RunsCancelled|실행 취소됨|개수|합계|실행 취소된 워크플로 수|차원 없음|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간 |차원 없음|
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간 |차원 없음|
|RunThrottledEvents|실행 제한 이벤트|개수|합계|워크플로 작업 또는 트리거 제한 이벤트 수|차원 없음|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|차원 없음|
|ActionsStarted|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|차원 없음|
|ActionsCompleted|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|차원 없음|
|ActionsSucceeded|작업 성공함 |개수|합계|성공한 워크플로 작업 수|차원 없음|
|ActionsFailed|작업 실패함|개수|합계|실패한 워크플로 작업 수|차원 없음|
|ActionsSkipped|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|차원 없음|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간 |차원 없음|
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간 |차원 없음|
|ActionThrottledEvents|작업 제한 이벤트|개수|합계|워크플로 작업 제한 이벤트 수|차원 없음|
|TriggersStarted|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|차원 없음|
|TriggersCompleted|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|차원 없음|
|TriggersSucceeded|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|차원 없음|
|TriggersFailed|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|차원 없음|
|TriggersSkipped|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|차원 없음|
|TriggersFired|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|차원 없음|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간 |차원 없음|
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|차원 없음|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|차원 없음|
|TriggerThrottledEvents|트리거 제한 이벤트|개수|합계|워크플로 트리거 제한 이벤트 수|차원 없음|
|BillableActionExecutions|청구 가능한 작업 실행|개수|합계|청구되는 워크플로 작업 실행 수.|차원 없음|
|BillableTriggerExecutions|청구 가능한 트리거 실행|개수|합계|청구되는 워크플로 트리거 실행 수.|차원 없음|
|TotalBillableExecutions|총 청구 가능 실행|개수|합계|청구되는 워크플로 실행 수.|차원 없음|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|VipAvailability|VIP 가용성|개수|평균|프로브 결과에 따른 VIP 끝점의 가용성|VipAddress, VipPort|
|DipAvailability|DIP 가용성|개수|평균|프로브 결과에 따른 DIP 끝점의 가용성|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|바이트 수|개수|합계|기간 내에 전송된 총 바이트 수|VipAddress, VipPort, 방향|
|PacketCount|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|VipAddress, VipPort, 방향|
|SYNCount|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|VipAddress, VipPort, 방향|
|SnatConnectionCount|SNAT 연결 수|개수|합계|기간 내에 만들어진 새 SNAT 연결의 총 수|VipAddress, DipAddress, ConnectionState|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QueryVolume|쿼리 볼륨|개수|합계|DNS 영역에 대해 제공된 쿼리 수|차원 없음|
|RecordSetCount|레코드 집합 수|개수|최대|DNS 영역의 레코드 집합 수|차원 없음|
|RecordSetCapacityUtilization|레코드 집합 용량 사용률|백분율|최대|DNS 영역에서 사용된 레코드 집합 용량 백분율|차원 없음|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|PacketsInDDoS|인바운드 패킷 DDoS|초당 개수|최대|인바운드 패킷 DDoS|차원 없음|
|PacketsDroppedDDoS|인바운드 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 패킷이 삭제된 DDoS|차원 없음|
|PacketsForwardedDDoS|인바운드 패킷이 전달된 DDoS|초당 개수|최대|인바운드 패킷이 전달된 DDoS|차원 없음|
|TCPPacketsInDDoS|인바운드 TCP 패킷 DDoS|초당 개수|최대|인바운드 TCP 패킷 DDoS|차원 없음|
|TCPPacketsDroppedDDoS|인바운드 TCP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 삭제된 DDoS|차원 없음|
|TCPPacketsForwardedDDoS|인바운드 TCP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 전달된 DDoS|차원 없음|
|UDPPacketsInDDoS|인바운드 UDP 패킷 DDoS|초당 개수|최대|인바운드 UDP 패킷 DDoS|차원 없음|
|UDPPacketsDroppedDDoS|인바운드 UDP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 삭제된 DDoS|차원 없음|
|UDPPacketsForwardedDDoS|인바운드 UDP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 전달된 DDoS|차원 없음|
|BytesInDDoS|인바운드 바이트 DDoS|초당 바이트 수|최대|인바운드 바이트 DDoS|차원 없음|
|BytesDroppedDDoS|인바운드 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 바이트가 삭제된 DDoS|차원 없음|
|BytesForwardedDDoS|인바운드 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 바이트가 전달된 DDoS|차원 없음|
|TCPBytesInDDoS|인바운드 TCP 바이트 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트 DDoS|차원 없음|
|TCPBytesDroppedDDoS|인바운드 TCP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 삭제된 DDoS|차원 없음|
|TCPBytesForwardedDDoS|인바운드 TCP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 전달된 DDoS|차원 없음|
|UDPBytesInDDoS|인바운드 UDP 바이트 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트 DDoS|차원 없음|
|UDPBytesDroppedDDoS|인바운드 UDP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 삭제된 DDoS|차원 없음|
|UDPBytesForwardedDDoS|인바운드 UDP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 전달된 DDoS|차원 없음|
|IfUnderDDoSAttack|DDoS 공격 진행 여부|개수|최대|DDoS 공격 진행 여부|차원 없음|
|DDoSTriggerTCPPackets|DDoS 완화를 트리거하는 인바운드 TCP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 TCP 패킷|차원 없음|
|DDoSTriggerUDPPackets|DDoS 완화를 트리거하는 인바운드 UDP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 UDP 패킷|차원 없음|
|DDoSTriggerSYNPackets|DDoS 완화를 트리거하는 인바운드 SYN 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 SYN 패킷|차원 없음|
|VipAvailability|가용성|개수|평균|기간 내에 평균 IP 주소 가용성|포트|
|ByteCount|바이트 수|개수|합계|기간 내에 전송된 총 바이트 수|포트, 방향|
|PacketCount|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|포트, 방향|
|SynCount|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|포트, 방향|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|처리량|처리량|초당 바이트 수|합계|Application Gateway에서 제공하는 초당 바이트 수|차원 없음|
|UnhealthyHostCount|비정상 호스트 수|개수|평균|비정상 백 엔드 호스트 수. 특정 백 엔드 풀의 정상/비정상 호스트를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.|BackendSettingsPool|
|HealthyHostCount|정상 호스트 수|개수|평균|정상 백 엔드 호스트 수. 특정 백 엔드 풀의 정상/비정상 호스트를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.|BackendSettingsPool. |
|TotalRequests|총 요청 수|개수|합계|Application Gateway가 제공하는 성공한 요청 수|BackendSettingsPool|
|FailedRequests|실패한 요청|개수|합계|Application Gateway가 제공하는 실패한 요청 수|BackendSettingsPool|
|ResponseStatus|응답 상태|개수|합계|Application Gateway에서 반환된 HTTP 응답 상태. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.|HttpStatusGroup|
|CurrentConnections|현재 연결|개수|합계|Application Gateway와 설정된 현재 연결 수|차원 없음|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|터널 대역폭|초당 바이트 수|평균|초당 터널의 평균 대역폭(바이트)|ConnectionName, RemoteIP|
|TunnelEgressBytes|터널 송신 바이트|바이트|합계|터널의 송신 바이트|ConnectionName, RemoteIP|
|TunnelIngressBytes|터널 수신 바이트|바이트|합계|터널의 수신 바이트|ConnectionName, RemoteIP|
|TunnelEgressPackets|터널 송신 패킷|개수|합계|터널의 송신 패킷 수|ConnectionName, RemoteIP|
|TunnelIngressPackets|터널 수신 패킷|개수|합계|터널의 수신 패킷 수|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|터널 송신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|터널 수신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|차원 없음|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|차원 없음|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QpsByEndpoint|반환된 끝점별 쿼리|개수|합계|Traffic Manager 끝점이 주어진 시간 내에 반환된 횟수|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|끝점별 끝점 상태|개수|최대|끝점의 프로브 상태가 "사용"이면 1이고 그렇지 않으면 0입니다.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ProbesFailedPercent|실패한 프로브 %|백분율|평균|실패한 연결 모니터링 프로브 %|차원 없음|
|AverageRoundtripMs|평균 왕복 시간(ms)|밀리초|평균|원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 왕복 시간(ms)|차원 없음|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|registration.all|등록 작업|개수|합계|성공한 모든 등록 작업(만들기 업데이트 쿼리 및 삭제)입니다. |차원 없음|
|registration.create|등록 만들기 작업|개수|합계|성공한 모든 등록 만들기의 수입니다.|차원 없음|
|registration.update|등록 업데이트 작업|개수|합계|성공한 모든 등록 업데이트의 수입니다.|차원 없음|
|registration.get|등록 읽기 작업|개수|합계|성공한 모든 등록 쿼리의 수입니다.|차원 없음|
|registration.delete|등록 삭제 작업|개수|합계|성공한 모든 등록 삭제의 수입니다.|차원 없음|
|incoming|들어오는 메시지 |개수|합계|성공한 모든 API 호출 전송의 수입니다. |차원 없음|
|incoming.scheduled|전송된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|차원 없음|
|incoming.scheduled.cancel|취소된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|차원 없음|
|scheduled.pending|보류 중인 예약된 알림|개수|합계|보류 중인 예약된 알림|차원 없음|
|installation.all|설치 관리 작업|개수|합계|설치 관리 작업|차원 없음|
|installation.get|설치 작업 가져오기|개수|합계|설치 작업 가져오기|차원 없음|
|installation.upsert|설치 작업 만들기 또는 업데이트|개수|합계|설치 작업 만들기 또는 업데이트|차원 없음|
|installation.patch|설치 작업 패치|개수|합계|설치 작업 패치|차원 없음|
|installation.delete|설치 작업 삭제|개수|합계|설치 작업 삭제|차원 없음|
|outgoing.allpns.success|성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.allpns.invalidpayload|페이로드 오류|개수|합계|PNS가 잘못된 페이로드 오류를 반환하기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.pnserror|외부 알림 시스템 오류|개수|합계|PNS와 통신하는 데 문제(인증 문제 제외)가 있기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.channelerror|채널 오류|개수|합계|채널이 잘못되었거나 제한 또는 만료된 올바른 앱과 연결되지 않았으므로 실패 한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.badorexpiredchannel|잘못되거나 만료된 채널 오류|개수|합계|등록의 channel/token/registrationId가 만료되었거나 올바르지 않기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.wns.success|WNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.wns.invalidcredentials|WNS 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다. (Windows Live는 자격 증명을 인식하지 못함)|차원 없음|
|outgoing.wns.badchannel|WNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(WNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.wns.expiredchannel|WNS 만료된 채널 오류|개수|합계|ChannelURI가 만료되어 실패한 푸시의 수입니다(WNS 상태: 410 없음).|차원 없음|
|outgoing.wns.throttled|WNS 제한된 알림|개수|합계|WNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS 상태: 406 승인 금지).|차원 없음|
|outgoing.wns.tokenproviderunreachable|WNS 권한 부여 오류(연결할 수 없음)|개수|합계|Windows Live에 연결할 수 없습니다.|차원 없음|
|outgoing.wns.invalidtoken|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공한 토큰이 잘못되었습니다(WNS 상태: 401 권한 없음).|차원 없음|
|outgoing.wns.wrongtoken|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공된 토큰은 유효하지만 다른 응용 프로그램에 대해서는 유효하지 않습니다(WNS 상태: 403 사용 권한 없음). 등록의 ChannelURI가 다른 앱에 연결된 경우 이 문제가 발생할 수 있습니다. 클라이언트 앱은 자격 증명이 알림 허브에 있는 동일한 앱과 연결되어 있는지 확인합니다.|차원 없음|
|outgoing.wns.invalidnotificationformat|WNS 잘못된 알림 형식|개수|합계|알림의 형식이 잘못되었습니다(WNS 상태: 400). WNS가 잘못된 모든 페이로드를 거부하지는 않습니다.|차원 없음|
|outgoing.wns.invalidnotificationsize|WNS 잘못된 알림 크기 오류|개수|합계|알림 페이로드가 너무 큽니다(WNS 상태: 413).|차원 없음|
|outgoing.wns.channelthrottled|WNS 채널 제한|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-NotificationStatus:channelThrottled).|차원 없음|
|outgoing.wns.channeldisconnected|WNS 채널 연결 끊김|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.dropped|WNS 삭제된 알림|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.pnserror|WNS 오류|개수|합계|WNS와의 통신 오류로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.wns.authenticationerror|WNS 인증 오류|개수|합계|Windows Live와의 통신 오류(잘못된 자격 증명 또는 잘못된 토큰)로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.apns.success|APNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.apns.invalidcredentials|APNS 권한 부여 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.apns.badchannel|APNS 잘못된 채널 오류|개수|합계|토큰이 잘못되어 실패한 푸시의 수입니다(APNS 상태 코드: 8).|차원 없음|
|outgoing.apns.expiredchannel|APNS 만료된 채널 오류|개수|합계|APNS 피드백 채널에서 무효화된 토큰의 수입니다.|차원 없음|
|outgoing.apns.invalidnotificationsize|APNS 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(APNS 상태 코드: 7).|차원 없음|
|outgoing.apns.pnserror|APNS 오류|개수|합계|APNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.success|GCM 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.gcm.invalidcredentials|GCM 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.badchannel|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 인식되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: 잘못된 등록).|차원 없음|
|outgoing.gcm.expiredchannel|GCM 만료된 채널 오류|개수|합계|등록의 registrationId가 만료되었기 때문에 실패한 푸시의 수입니다(GCM 결과: NotRegistered).|차원 없음|
|outgoing.gcm.throttled|GCM 제한된 알림|개수|합계|GCM이 이 앱을 제한하기 때문에 실패한 푸시의 수(GCM 상태 코드: 501-599 또는 결과: Unavailable).|차원 없음|
|outgoing.gcm.invalidnotificationformat|GCM 잘못된 알림 형식|개수|합계|페이로드 형식이 올바르지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidDataKey 또는 InvalidTtl).|차원 없음|
|outgoing.gcm.invalidnotificationsize|GCM 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(GCM 결과: MessageTooBig).|차원 없음|
|outgoing.gcm.wrongchannel|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 현재 앱과 연결되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidPackageName).|차원 없음|
|outgoing.gcm.pnserror|GCM 오류|개수|합계|GCM과의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.authenticationerror|GCM 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나, 자격 증명이 차단되었거나, 앱에서 SenderId가 올바르게 구성되지 않았기 때문에 실패한 푸시의 수입니다(GCM 결과: MismatchedSenderId).|차원 없음|
|outgoing.mpns.success|MPNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.mpns.invalidcredentials|MPNS 잘못된 자격 증명|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.badchannel|MPNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(MPNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.mpns.throttled|MPNS 제한된 알림|개수|합계|MPNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS MPNS: 406 승인 금지).|차원 없음|
|outgoing.mpns.invalidnotificationformat|MPNS 잘못된 알림 형식|개수|합계|알림의 페이로드가 너무 커서 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.channeldisconnected|MPNS 채널 연결 끊김|개수|합계|등록의 ChannelURI 연결이 끊어졌으므로 실패한 푸시의 수입니다(MPNS 상태: 412 찾을 수 없음).|차원 없음|
|outgoing.mpns.dropped|MPNS 삭제된 알림|개수|합계|MPNS에서 삭제된 푸시의 수입니다(MPNS 응답 헤더: X-NotificationStatus: QueueFull 또는 Suppressed).|차원 없음|
|outgoing.mpns.pnserror|MPNS 오류|개수|합계|MPNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.authenticationerror|MPNS 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|notificationhub.pushes|모든 나가는 알림 수|개수|합계|알림 허브의 모든 나가는 알림 수|차원 없음|
|incoming.all.requests|들어오는 모든 요청|개수|합계|알림 허브에 대해 들어오는 전체 요청|차원 없음|
|incoming.all.failedrequests|들어오는 모든 실패한 요청|개수|합계|알림 허브에 대해 들어오는 실패한 전체 요청|차원 없음|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces
(공개 미리 보기)

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|Average_% Free Inodes|Average_% Free Inodes|개수|평균|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Average_% Free Space|개수|평균|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Average_% Used Inodes|개수|평균|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Average_% Used Space|개수|평균|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Average_Disk Read Bytes/sec|개수|평균|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Average_Disk Reads/sec|개수|평균|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Average_Disk Transfers/sec|개수|평균|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Average_Disk Write Bytes/sec|개수|평균|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Average_Disk Writes/sec|개수|평균|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Average_Free Megabytes|개수|평균|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Average_Logical Disk Bytes/sec|개수|평균|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|Average_% Available Memory|개수|평균|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Average_% Available Swap Space|개수|평균|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Average_% Used Memory|개수|평균|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Average_% Used Swap Space|개수|평균|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Average_Available MBytes Memory|개수|평균|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Average_Available MBytes Swap|개수|평균|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Average_Page Reads/sec|개수|평균|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Average_Page Writes/sec|개수|평균|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Average_Pages/sec|개수|평균|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Average_Used MBytes Swap Space|개수|평균|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Average_Used Memory MBytes|개수|평균|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Average_Total Bytes Transmitted|개수|평균|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Average_Total Bytes Received|개수|평균|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Average_Total Bytes|개수|평균|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Average_Total Packets Transmitted|개수|평균|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Average_Total Packets Received|개수|평균|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Average_Total Rx Errors|개수|평균|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Average_Total Tx Errors|개수|평균|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Average_Total Collisions|개수|평균|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|Average_Avg. 디스크 초/읽기|개수|평균|Average_Avg. 디스크 초/읽기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/전송|Average_Avg. 디스크 초/전송|개수|평균|Average_Avg. 디스크 초/전송|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|Average_Avg. 디스크 초/쓰기|개수|평균|Average_Avg. 디스크 초/쓰기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Average_Physical Disk Bytes/sec|개수|평균|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Average_Pct Privileged Time|개수|평균|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Average_Pct User Time|개수|평균|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Average_Used Memory kBytes|개수|평균|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Average_Virtual Shared Memory|개수|평균|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Average_% DPC Time|개수|평균|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Average_% Idle Time|개수|평균|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Average_% Interrupt Time|개수|평균|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Average_% IO Wait Time|개수|평균|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Average_% Nice Time|개수|평균|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Average_% Privileged Time|개수|평균|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Average_% Processor Time|개수|평균|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Average_% User Time|개수|평균|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Average_Free Physical Memory|개수|평균|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Average_Free Space in Paging Files|개수|평균|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Average_Free Virtual Memory|개수|평균|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Average_Processes|개수|평균|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Average_Size Stored In Paging Files|개수|평균|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Average_Uptime|개수|평균|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Average_Users|개수|평균|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|Average_Avg. 디스크 초/읽기|개수|평균|Average_Avg. 디스크 초/읽기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|Average_Avg. 디스크 초/쓰기|개수|평균|Average_Avg. 디스크 초/쓰기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Average_Current Disk Queue Length|개수|평균|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Average_Disk Reads/sec|개수|평균|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Average_Disk Transfers/sec|개수|평균|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Average_Disk Writes/sec|개수|평균|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Average_Free Megabytes|개수|평균|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Average_% Free Space|개수|평균|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Average_Available MBytes|개수|평균|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Average_% Committed Bytes In Use|개수|평균|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Average_Bytes Received/sec|개수|평균|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Average_Bytes Sent/sec|개수|평균|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Average_Bytes Total/sec|개수|평균|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Average_% Processor Time|개수|평균|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Average_Processor Queue Length|개수|평균|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|개수|평균|Heartbeat|Computer, OSType, Version, SourceComputerId|
|주 지역에서|주 지역에서|개수|평균|주 지역에서|Computer, Product, Classification, UpdateState, Optional, Approved|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QueryDuration|쿼리 기간|밀리초|평균|마지막 간격의 DAX 쿼리 기간|차원 없음|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|
|qpu_high_utilization_metric|QPU 높은 사용률|개수|합계|지난 1분 간의 QPU 높은 사용률로, QPU 사용률이 높으면 1, 그렇지 않으면 0|차원 없음|
|memory_metric|메모리|바이트|평균|메모리. A1은 0-3GB, A2는 0-5GB, A3는 0-10GB, A4는 0-25GB, A5는 0-50GB, A6는 0-100GB 범위|차원 없음|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|차원 없음|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|개수|합계|Microsoft.Relay의 성공적인 ListenerConnections.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|개수|합계|Microsoft.Relay의 총 ListenerConnections에서의 ClientError.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|개수|합계|Microsoft.Relay의 ListenerConnections에서의 ServerError.|EntityName|
|SenderConnections-Success|SenderConnections-Success|개수|합계|Microsoft.Relay의 성공적인 SenderConnections.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|개수|합계|Microsoft.Relay의 SenderConnections에서 ClientError.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|개수|합계|Microsoft.Relay의 SenderConnections에서의 ServerError.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 ListenerConnections.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 SenderConnections 요청.|EntityName|
|ActiveConnections|ActiveConnections|개수|합계|Microsoft.Relay의 총 ActiveConnections.|EntityName|
|ActiveListeners|ActiveListeners|개수|합계|Microsoft.Relay의 총 ActiveListeners.|EntityName|
|BytesTransferred|BytesTransferred|개수|합계|Microsoft.Relay의 총 BytesTransferred.|EntityName|
|ListenerDisconnects|ListenerDisconnects|개수|합계|Microsoft.Relay의 총 ListenerDisconnects.|EntityName|
|SenderDisconnects|SenderDisconnects|개수|합계|Microsoft.Relay의 총 SenderDisconnects.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|SearchLatency|검색 대기 시간|초|평균|검색 서비스에 대한 평균 검색 대기 시간|차원 없음|
|SearchQueriesPerSecond|초당 검색 쿼리 수|초당 개수|평균|Search 서비스에 대한 초당 검색 쿼리|차원 없음|
|ThrottledSearchQueriesPercentage|제한된 검색 쿼리 백분율|백분율|평균|검색 서비스에 대해 제한된 검색 쿼리의 백분율|차원 없음|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청(미리 보기)|개수|합계|네임스페이스에 대한 총 성공한 요청(미리 보기)|EntityName, |
|ServerErrors|서버 오류. (미리 보기)|개수|합계|Microsoft.ServiceBus에 대한 서버 오류. (미리 보기)|EntityName, |
|UserErrors|사용자 오류. (미리 보기)|개수|합계|Microsoft.ServiceBus에 대한 사용자 오류. (미리 보기)|EntityName, |
|ThrottledRequests|제한된 요청. (미리 보기)|개수|합계|Microsoft.ServiceBus에 대한 제한된 요청. (미리 보기)|EntityName, |
|IncomingRequests|들어오는 요청(미리 보기)|개수|합계|Microsoft.ServiceBus에 대한 들어오는 요청. (미리 보기)|EntityName|
|IncomingMessages|들어오는 메시지(미리 보기)|개수|합계|Microsoft.ServiceBus에 대한 들어오는 메시지. (미리 보기)|EntityName|
|OutgoingMessages|보내는 메시지(미리 보기)|개수|합계|Microsoft.ServiceBus에 대한 보내는 메시지. (미리 보기)|EntityName|
|ActiveConnections|활성 연결(미리 보기)|개수|합계|Microsoft.ServiceBus에 대한 총 활성 연결. (미리 보기)|차원 없음|
|크기|크기(미리 보기)|바이트|평균|큐/토픽 크기(바이트) (미리 보기)|EntityName|
|메시지|큐/토픽에 있는 메시지 수 (미리 보기)|개수|평균|큐/토픽에 있는 메시지 수 (미리 보기)|EntityName|
|ActiveMessages|큐/토픽에 있는 활성 메시지 수 (미리 보기)|개수|평균|큐/토픽에 있는 활성 메시지 수 (미리 보기)|EntityName|
|CPUXNS|네임스페이스당 CPU 사용량|백분율|최대|서비스 버스 프리미엄 네임스페이스 CPU 사용량 메트릭|차원 없음|
|WSXNS|네임스페이스당 메모리 크기 사용|백분율|최대|서비스 버스 프리미엄 네임스페이스 메모리 사용량 메트릭|차원 없음|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ConnectionCount|연결 수|개수|최대|사용자 연결의 양.|차원 없음|
|ConnectionCountPerSecond|초당 연결 수|초당 개수|평균|초당 평균 연결 수.|차원 없음|
|MessageCount|메시지 수|개수|최대|해당 월의 총 메시지 양|차원 없음|
|MessageCountPerSecond|초당 메시지 수|초당 개수|평균|평균 메시지 수|차원 없음|
|MessageUsed|사용된 메시지|백분율|최대|해당 월에 사용된 메시지의 비율|차원 없음|
|ConnectionUsed|사용된 연결|백분율|최대|사용된 연결의 비율|차원 없음|
|UserErrors|사용자 오류|백분율|최대|사용자 오류의 비율|차원 없음|
|SystemErrors|시스템 오류|백분율|최대|시스템 오류의 비율|차원 없음|
|SystemLoad|시스템 부하|백분율|최대|시스템 부하의 비율|차원 없음|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|차원 없음|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|차원 없음|
|저장소|총 데이터베이스 크기|바이트|최대|총 데이터베이스 크기|차원 없음|
|connection_successful|성공적인 연결|개수|합계|성공적인 연결|차원 없음|
|connection_failed|실패한 연결|개수|합계|실패한 연결|차원 없음|
|blocked_by_firewall|방화벽에 의해 차단|개수|합계|방화벽에 의해 차단|차원 없음|
|교착 상태|교착 상태|개수|합계|교착 상태|차원 없음|
|storage_percent|데이터베이스 크기 비율|백분율|최대|데이터베이스 크기 비율|차원 없음|
|xtp_storage_percent|메모리 내 OLTP 저장소 백분율|백분율|평균|메모리 내 OLTP 저장소 백분율|차원 없음|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|차원 없음|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|차원 없음|
|dtu_limit|DTU 제한|개수|평균|DTU 제한|차원 없음|
|dtu_used|DTU 사용됨|개수|평균|DTU 사용됨|차원 없음|
|dwu_limit|DWU 제한|개수|최대|DWU 제한|차원 없음|
|dwu_consumption_percent|DWU 백분율|백분율|최대|DWU 백분율|차원 없음|
|dwu_used|DWU 사용됨|개수|최대|DWU 사용됨|차원 없음|
|dw_cpu_percent|DW 노드 수준 CPU 비율|백분율|평균|DW 노드 수준 CPU 비율|DwLogicalNodeId|
|dw_physical_data_read_percent|DW 노드 수준 데이터 IO 백분율|백분율|평균|DW 노드 수준 데이터 IO 백분율|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|차원 없음|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|차원 없음|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|차원 없음|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|차원 없음|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|차원 없음|
|eDTU_limit|eDTU 제한|개수|평균|eDTU 제한|차원 없음|
|storage_limit|저장소 제한|바이트|평균|저장소 제한|차원 없음|
|eDTU_used|eDTU 사용|개수|평균|eDTU 사용|차원 없음|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|차원 없음|
|xtp_storage_percent|메모리 내 OLTP 저장소 백분율|백분율|평균|메모리 내 OLTP 저장소 백분율|차원 없음|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|ElasticPoolResourceId|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|UsedCapacity|사용된 용량|바이트|평균|계정 사용 용량|차원 없음|
|트랜잭션|트랜잭션|개수|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BlobCapacity|Blob 용량|바이트|합계|저장소 계정의 Blob service가 사용하는 저장소의 양(바이트)입니다.|BlobType|
|BlobCount|Blob 수|개수|합계|저장소 계정의 Blob service에 있는 Blob 수입니다.|BlobType|
|ContainerCount|Blob 컨테이너 수|개수|평균|저장소 계정의 Blob service에 있는 컨테이너 수입니다.|차원 없음|
|트랜잭션|트랜잭션|개수|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TableCapacity|테이블 용량|바이트|평균|저장소 계정의 Table service가 사용하는 저장소의 양(바이트)입니다.|차원 없음|
|TableCount|테이블 수|개수|평균|저장소 계정의 Table service에 있는 테이블 수입니다.|차원 없음|
|TableEntityCount|테이블 엔터티 수|개수|평균|저장소 계정의 Table service에 있는 테이블 엔터티 수입니다.|차원 없음|
|트랜잭션|트랜잭션|개수|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QueueCapacity|큐 용량|바이트|평균|저장소 계정의 큐 서비스가 사용하는 저장소의 양(바이트)입니다.|차원 없음|
|QueueCount|큐 수|개수|평균|저장소 계정의 큐 서비스에 있는 큐 수입니다.|차원 없음|
|QueueMessageCount|큐 메시지 수|개수|평균|저장소 계정의 큐 서비스에 있는 대략적인 큐 메시지 수입니다.|차원 없음|
|트랜잭션|트랜잭션|개수|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|FileCapacity|파일 용량|바이트|평균|저장소 계정의 파일 서비스가 사용하는 저장소의 양(바이트)입니다.|차원 없음|
|FileCount|파일 수|개수|평균|저장소 계정의 파일 서비스에 있는 파일 수입니다.|차원 없음|
|FileShareCount|파일 공유 수|개수|평균|저장소 계정의 파일 서비스에 있는 파일 공유 수입니다.|차원 없음|
|트랜잭션|트랜잭션|개수|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 사용률|백분율|최대|SU % 사용률|차원 없음|
|InputEvents|입력 이벤트|개수|합계|입력 이벤트|차원 없음|
|InputEventBytes|입력 이벤트 바이트|바이트|합계|입력 이벤트 바이트|차원 없음|
|LateInputEvents|늦은 입력 이벤트|개수|합계|늦은 입력 이벤트|차원 없음|
|OutputEvents|출력 이벤트|개수|합계|출력 이벤트|차원 없음|
|ConversionErrors|데이터 변환 오류|개수|합계|데이터 변환 오류|차원 없음|
|오류|런타임 오류|개수|합계|런타임 오류|차원 없음|
|DroppedOrAdjustedEvents|잘못된 이벤트|개수|합계|잘못된 이벤트|차원 없음|
|AMLCalloutRequests|기능 요청|개수|합계|기능 요청|차원 없음|
|AMLCalloutFailedRequests|실패한 기능 요청|개수|합계|실패한 기능 요청|차원 없음|
|AMLCalloutInputEvents|함수 이벤트|개수|합계|함수 이벤트|차원 없음|
|DeserializationError|입력 역직렬화 오류|개수|합계|입력 역직렬화 오류|차원 없음|
|EarlyInputEvents|응용 프로그램 시간이 도착 시간보다 이전인 이벤트|개수|합계|응용 프로그램 시간이 도착 시간보다 이전인 이벤트|차원 없음|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 메시지 수입니다.|차원 없음|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 잘못된 메시지 수입니다.|차원 없음|
|IngressReceivedBytes|수신된 바이트|바이트|합계|모든 이벤트 원본에서 읽은 바이트 수입니다.|차원 없음|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|개수|평균|이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이입니다.|차원 없음|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|개수|합계|이벤트 원본에서 읽은 메시지 수|차원 없음|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|개수|합계|이벤트 원본에서 읽은 잘못된 메시지 수|차원 없음|
|IngressReceivedBytes|수신된 바이트|바이트|합계|이벤트 원본에서 읽은 바이트 수|차원 없음|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|개수|평균|이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이입니다.|차원 없음|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|개수|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|개수|평균|Http 큐 길이|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites(Functions 제외)

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|개수|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|Http 401|개수|합계|Http 401|인스턴스|
|Http403|Http 403|개수|합계|Http 403|인스턴스|
|Http404|Http 404|개수|합계|Http 404|인스턴스|
|Http406|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|AppConnections|연결|개수|평균|연결|인스턴스|
|핸들|핸들 개수|개수|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|개수|평균|스레드 개수|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|응용 프로그램 큐의 요청 수|개수|평균|응용 프로그램 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites(functions)

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|FunctionExecutionUnits|함수 실행 단위|개수|합계|함수 실행 단위|인스턴스|
|FunctionExecutionCount|함수 실행 횟수|개수|합계|함수 실행 횟수|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|응용 프로그램 큐의 요청 수|개수|평균|응용 프로그램 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|개수|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|Http 401|개수|합계|Http 401|인스턴스|
|Http403|Http 403|개수|합계|Http 403|인스턴스|
|Http404|Http 404|개수|합계|Http 404|인스턴스|
|Http406|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|FunctionExecutionUnits|함수 실행 단위|개수|합계|함수 실행 단위|인스턴스|
|FunctionExecutionCount|함수 실행 횟수|개수|합계|함수 실행 횟수|인스턴스|
|AppConnections|연결|개수|평균|연결|인스턴스|
|핸들|핸들 개수|개수|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|개수|평균|스레드 개수|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|응용 프로그램 큐의 요청 수|개수|평균|응용 프로그램 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|요청|요청|개수|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|Http 401|개수|합계|Http 401|인스턴스|
|Http403|Http 403|개수|합계|Http 403|인스턴스|
|Http404|Http 404|개수|합계|Http 404|인스턴스|
|Http406|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|개수|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|개수|평균|Http 큐 길이|인스턴스|
|ActiveRequests|활성 요청|개수|합계|활성 요청|인스턴스|
|TotalFrontEnds|총 프런트 엔드|개수|평균|총 프런트 엔드|차원 없음|
|SmallAppServicePlanInstances|소형 App Service 계획 작업자|개수|평균|소형 App Service 계획 작업자|차원 없음|
|MediumAppServicePlanInstances|중형 App Service 계획 작업자|개수|평균|중형 App Service 계획 작업자|차원 없음|
|LargeAppServicePlanInstances|대형 App Service 계획 작업자|개수|평균|대형 App Service 계획 작업자|차원 없음|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|WorkersTotal|총 작업자|개수|평균|총 작업자|차원 없음|
|WorkersAvailable|사용 가능한 작업자|개수|평균|사용 가능한 작업자|차원 없음|
|WorkersUsed|사용된 작업자|개수|평균|사용된 작업자|차원 없음|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|

## <a name="next-steps"></a>다음 단계
* [Azure Monitor의 메트릭에 대해 읽기](monitoring-overview-metrics.md)
* [메트릭에 대한 경고 만들기](insights-receive-alert-notifications.md)
* [저장소, 이벤트 허브 또는 Log Analytics에 메트릭 내보내기](monitoring-overview-of-diagnostic-logs.md)
