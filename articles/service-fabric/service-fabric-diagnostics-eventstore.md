---
title: Azure Service Fabric EventStore  | Microsoft Docs
description: Azure Service Fabric의 EventStore 알아보기
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 1d235d5a75975c8d58cad1bbde0f16df2b1b3e7a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="eventstore-service-overview"></a>EventStore 서비스 개요

>[!NOTE]
>Service Fabric 버전 6.2부터 EventStore API는 현재 Azure에서만 실행되는 Windows 클러스터에 대한 미리 보기로 제공됩니다. 이 기능을 Linux 및 독립 실행형 클러스터에도 이식하려고 노력하고 있습니다.

## <a name="overview"></a>개요

6.2 버전에 도입된 EventStore 서비스는 Service Fabric의 모니터링 옵션으로, 지정된 시간에 클러스터 또는 워크로드의 상태를 파악할 수 있는 방법을 제공합니다. EventStore 서비스는 호출을 만들 수 있는 API를 통해 Service Fabric 이벤트를 노출합니다. 이러한 EventStore API를 사용하면 클러스터를 직접 쿼리하여 클러스터의 모든 엔터티에 대한 진단 데이터를 획득하고 다음 용도로 사용할 수 있습니다.
* 개발 또는 테스트의 문제 또는 모니터링 파이프라인을 사용할 위치의 문제 진단
* 클러스터에 대해 수행하는 관리 작업이 클러스터에서 제대로 처리되고 있는지 확인

EventStore에서 사용할 수 있는 전체 이벤트 목록은 [Service Fabric 이벤트](service-fabric-diagnostics-event-generation-operational.md)를 참조하세요.

클러스터의 각 엔터티 및 엔터티 형식에 사용할 수 있는 이벤트에 대해 EventStore 서비스를 쿼리할 수 있습니다. 즉, 다음 수준에서 이벤트를 쿼리할 수 있습니다.
* 클러스터: 모든 클러스터 수준 이벤트
* 노드: 모든 노드 수준 이벤트
* 노드: 한 노드에 한정된 이벤트, `nodeName` 기반
* 응용 프로그램: 모든 응용 프로그램 수준 이벤트
* 응용 프로그램: 한 응용 프로그램에 한정된 이벤트
* 서비스: 클러스터에 있는 모든 서비스의 이벤트
* 서비스: 특정 서비스의 이벤트
* 파티션: 모든 파티션의 이벤트
* 파티션: 특정 파티션의 이벤트
* 복제본: 모든 복제본/인스턴스의 이벤트
* 복제본: 특정 복제본/인스턴스의 이벤트


EventStore 서비스에는 클러스터의 이벤트 간에 상관 관계를 지정하는 기능도 있습니다. EventStore 서비스는 서로 영향을 미칠 수 있는 서로 다른 엔터티에서 동시에 기록된 이벤트를 살펴보고 이러한 이벤트를 연결하여 클러스터의 작업에 대한 원인을 식별하는 데 도움을 줍니다. 예를 들어 아무런 변경 작업 없이 응용 프로그램 중 하나가 비정상 상태가 되면 EventStore는 플랫폼에서 노출하는 다른 이벤트를 살펴보고 `NodeDown` 이벤트로 상관 관계를 지정합니다. 이렇게 하면 더 빠르게 오류를 감지하고 근본 원인을 분석할 수 있습니다.

EventStore 서비스 사용을 시작하려면 [EventStore API를 통한 클러스터 이벤트 쿼리](service-fabric-diagnostics-eventstore-query.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Service Fabric의 모니터링 및 진단 개요 - [Service Fabric에 대한 모니터링 및 진단](service-fabric-diagnostics-overview.md)
* 클러스터 모니터링에 대해 자세히 알아보기 - [클러스터 및 플랫폼 모니터링](service-fabric-diagnostics-event-generation-infra.md).