---
title: Azure Service Fabric 성능 모니터링 | Microsoft Docs
description: Azure Service Fabric 클러스터를 모니터링하고 진단하기 위한 성능 카운터에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 9e740dd3acce842f888e5994fe8f46222477adc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="performance-metrics"></a>성능 메트릭

클러스터의 성능 및 클러스터에서 실행 중인 응용 프로그램을 이해하기 위해 메트릭을 수집해야 합니다. Service Fabric 클러스터의 경우 다음과 같은 성능 카운터를 수집하는 것이 좋습니다.

## <a name="nodes"></a>노드

클러스터의 컴퓨터의 경우 각 컴퓨터의 부하를 이해하고 적절한 클러스터 크기 조정을 결정하려면 다음과 같은 성능 카운터를 수집하는 것이 좋습니다.

| 카운터 범주 | 카운터 이름 |
| --- | --- |
| PhysicalDisk(디스크당) | 평균 디스크 읽기 큐 길이 |
| PhysicalDisk(디스크당) | 평균 디스크 쓰기 큐 길이 |
| PhysicalDisk(디스크당) | 평균 디스크 초/읽기 |
| PhysicalDisk(디스크당) | 평균 디스크 초/쓰기 |
| PhysicalDisk(디스크당) | 디스크 읽기/초  |
| PhysicalDisk(디스크당) | 디스크 읽기 바이트/초  |
| PhysicalDisk(디스크당) | 디스크 쓰기/초 |
| PhysicalDisk(디스크당) | 디스크 쓰기 바이트/초 |
| 메모리 | Available MBytes |
| PagingFile | % 사용량 |
| 프로세서(합계) | % Processor Time |
| 프로세스(서비스당) | % Processor Time |
| 프로세스(서비스당) | ID 프로세스 |
| 프로세스(서비스당) | 프로세스 바이트 |
| 프로세스(서비스당) | 스레드 개수 |
| 프로세스(서비스당) | 가상 바이트 |
| 프로세스(서비스당) | 작업 집합 |
| 프로세스(서비스당) | 작업 집합 - 개인 |
| 네트워크 인터페이스(모든 인스턴스) | Output Queue Length |
| 네트워크 인터페이스(모든 인스턴스) | Packets Outbound Discarded |
| 네트워크 인터페이스(모든 인스턴스) | Packets Received Discarded |
| 네트워크 인터페이스(모든 인스턴스) | Packets Outbound Errors |
| 네트워크 인터페이스(모든 인스턴스) | Packets Received Errors |

## <a name="net-applications-and-services"></a>.NET 응용 프로그램 및 서비스

.NET 서비스를 클러스터에 배포하는 경우 다음과 같은 카운터를 수집합니다. 

| 카운터 범주 | 카운터 이름 |
| --- | --- |
| .NET CLR 메모리(서비스당) | 프로세스 ID |
| .NET CLR 메모리(서비스당) | #커밋된 전체 바이트 |
| .NET CLR 메모리(서비스당) | #예약된 전체 바이트 |
| .NET CLR 메모리(서비스당) | #모든 힙의 바이트 |
| .NET CLR 메모리(서비스당) | #0 컬렉션 생성 |
| .NET CLR 메모리(서비스당) | #1 컬렉션 생성 |
| .NET CLR 메모리(서비스당) | #2 컬렉션 생성 |
| .NET CLR 메모리(서비스당) | % Time in GC |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric 사용자 지정 성능 카운터

Service Fabric은 상당한 양의 사용자 지정 성능 카운터를 생성합니다. SDK가 설치되어 있는 경우 성능 모니터 응용 프로그램에서 Windows 컴퓨터의 포괄적인 목록을 볼 수 있습니다(시작 > 성능 모니터). 

클러스터에 배포하는 응용 프로그램에서 Reliable Actors를 사용하는 경우 `Service Fabric Actor` 및 `Service Fabric Actor Method` 범주에서 카운터를 추가합니다([Service Fabric Reliable Actors 진단](service-fabric-reliable-actors-diagnostics.md) 참조).

마찬가지로 Reliable Services를 사용하는 경우 카운터를 수집해야 하는 `Service Fabric Service` 및 `Service Fabric Service Method` 카운터 범주가 있습니다. 

신뢰할 수 있는 컬렉션을 사용하는 경우 `Service Fabric Transactional Replicator`에서 `Avg. Transaction ms/Commit`을 추가하여 트랜잭션 메트릭당 평균 커밋 대기 시간을 수집하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

* Service Fabric에서 [플랫폼 수준의 이벤트 생성](service-fabric-diagnostics-event-generation-infra.md)에 대해 자세히 알아보기
* [OMS 에이전트](service-fabric-diagnostics-oms-agent.md)를 통해 성능 메트릭 수집
