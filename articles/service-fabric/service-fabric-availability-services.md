---
title: Service Fabric 서비스의 가용성 | Microsoft Docs
description: 서비스에 대한 오류 검색, 장애 조치(Failover) 및 복구를 설명합니다.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0794c0e190ecbc4cce808e94f98bb0ac63d1075a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="availability-of-service-fabric-services"></a>서비스 패브릭 서비스의 가용성
이 문서에서는 Azure Service Fabric이 서비스의 가용성을 유지하는 방법에 대한 개요를 설명합니다.

## <a name="availability-of-service-fabric-stateless-services"></a>서비스 패브릭 상태 비저장 서비스의 가용성
Service Fabric 서비스는 상태 저장 또는 상태 비저장이 모두 될 수 있습니다. 상태 비저장 서비스는 높은 고가용성 또는 신뢰성이 필요한 [로컬 상태](service-fabric-concepts-state.md)를 포함하지 않는 응용 프로그램 서비스입니다.

상태 비저장 서비스를 만들려면 `InstanceCount`를 정의해야 합니다. 인스턴스 수는 클러스터에서 실행되어야 하는 상태 비저장 서비스의 응용 프로그램 논리 인스턴스 수를 정의합니다. 인스턴스 수를 늘리는 것은 상태 비저장 서비스를 확장하는 데 바람직한 방법입니다.

상태 비저장 서비스의 인스턴스가 실패하면 클러스터의 일부 적합한 노드에 새 인스턴스가 만들어집니다. 예를 들어 상태 비저장 서비스 인스턴스는 Node1에서 실패하고 Node5에서 다시 만들어질 수 있습니다.

## <a name="availability-of-service-fabric-stateful-services"></a>서비스 패브릭 상태 저장 서비스의 가용성
상태 저장 서비스에는 이와 관련된 일부 상태가 있습니다. 서비스 패브릭에서 상태 저장 서비스는 복제본 세트로 모델링됩니다. 각 복제본은 서비스 코드의 실행 중인 인스턴스입니다. 복제본에는 해당 서비스의 상태 복사본도 포함됩니다. 읽기 및 쓰기 작업은 하나의 복제본(*주*라고 함)에서 수행됩니다. 쓰기 작업으로 인한 상태 변경은 복제본 세트의 다른 복제본(*활성 보조 복제본*이라고 함)에 *복제되어* 적용됩니다. 

주 복제본은 하나만 있을 수 있지만 활성 보조 복제본은 여러 개가 있을 수 있습니다. 활성 보조 복제본의 수는 구성 가능하며 복제본의 수가 많을수록 더 많은 수의 소프트웨어 및 하드웨어 오류를 동시에 허용할 수 있습니다.

주 복제본이 중지되는 경우 Service Fabric은 활성 보조 복제본 중 하나를 새 주 복제본으로 만듭니다. 이 활성 보조 복제본은 이미 상태의 업데이트된 버전을 가지므로(*복제*를 통해) 읽기/쓰기 작업의 처리를 계속할 수 있습니다. 이 프로세스를 *재구성*이라고 하며 [재구성](service-fabric-concepts-reconfiguration.md) 문서에서 자세히 설명합니다.

복제본이 주 또는 활성 보조가 되는 이 개념은 *복제본 역할*로 알려져 있습니다. 이러한 복제본은 [복제본 및 인스턴스](service-fabric-concepts-replica-lifecycle.md) 문서에 자세히 설명되어 있습니다. 

## <a name="next-steps"></a>다음 단계
Service Fabric 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Service Fabric 서비스 크기 조정](service-fabric-concepts-scalability.md)
- [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)
- [상태 정의 및 관리](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

