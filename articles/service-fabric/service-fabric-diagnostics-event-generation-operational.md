---
title: Azure Service Fabric 이벤트 목록 | Microsoft Docs
description: 클러스터를 모니터링할 수 있도록 Azure Service Fabric에서 제공하는 이벤트의 포괄적인 목록입니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 이벤트 목록 

Service Fabric은 [Service Fabric 이벤트](service-fabric-diagnostics-events.md)로 클러스터의 상태를 알리도록 클러스터 이벤트의 기본 집합을 노출합니다. 노드 및 클러스터의 Service Fabric에서 수행하는 작업 또는 클러스터 소유자/연산자에서 만드는 관리 결정을 기반으로 합니다. 이러한 이벤트는 클러스터에서 [EventStore](service-fabric-diagnostics-eventstore.md)를 쿼리하거나 작업 채널을 통해 액세스할 수 있습니다. Windows 컴퓨터에서 작업 채널은 EventLog에도 연결되므로 이벤트 뷰어에서 Service Fabric을 볼 수 있습니다. 

>[!NOTE]
>버전 < 6.2에서 클러스터에 대한 Service Fabric 이벤트 목록의 경우 다음 섹션을 참조하세요. 

다음은 매핑되는 엔터티별로 정렬된 플랫폼에서 사용할 수 있는 모든 이벤트의 목록입니다.

## <a name="cluster-events"></a>클러스터 이벤트

**클러스터 업그레이드 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | 정보 제공 | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | 정보 제공 | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | 정보 제공 | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | 정보 제공 | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | 정보 제공 | 1 |

**클러스터 상태 보고서 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | 정보 제공 | 1 |
| 54437 | ExpiredClusterEventOperational | HM | 정보 제공 | 1 |

**카오스 서비스 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | 테스트 가능성 | 정보 제공 | 1 |
| 50023 | ChaosStoppedEvent | 테스트 가능성 | 정보 제공 | 1 |

## <a name="node-events"></a>노드 이벤트

**노드 수명 주기 이벤트** 

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | 정보 제공 | 1 |
| 18603 | NodeUpOperational | FM | 정보 제공 | 1 |
| 18604 | NodeDownOperational | FM | 정보 제공 | 1 |
| 18605 | NodeAddedOperational | FM | 정보 제공 | 1 |
| 18606 | NodeRemovedOperational | FM | 정보 제공 | 1 |
| 18607 | DeactivateNodeStartOperational | FM | 정보 제공 | 1 |
| 25620 | NodeOpening | FabricNode | 정보 제공 | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | 정보 제공 | 1 |
| 25622 | NodeOpenedFailed | FabricNode | 정보 제공 | 1 |
| 25623 | NodeClosing | FabricNode | 정보 제공 | 1 |
| 25624 | NodeClosed | FabricNode | 정보 제공 | 1 |
| 25625 | NodeAborting | FabricNode | 정보 제공 | 1 |
| 25626 | NodeAborted | FabricNode | 정보 제공 | 1 |

**노드 상태 보고서 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | 정보 제공 | 1 |
| 54432 | ExpiredNodeEventOperational | HM | 정보 제공 | 1 |

**카오스 노드 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | 테스트 가능성 | 정보 제공 | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | 테스트 가능성 | 정보 제공 | 1 |

## <a name="application-events"></a>응용 프로그램 이벤트

**응용 프로그램 수명 주기 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | 정보 제공 | 1 |
| 29625 | ApplicationDeletedOperational | CM | 정보 제공 | 1 |
| 23083 | ProcessExitedOperational | Hosting | 정보 제공 | 1 |

**응용 프로그램 업그레이드 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | 정보 제공 | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | 정보 제공 | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | 정보 제공 | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | 정보 제공 | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | 정보 제공 | 1 |

**응용 프로그램 상태 보고서 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | 정보 제공 | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | 정보 제공 | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | 정보 제공 | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | 정보 제공 | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | 정보 제공 | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | 정보 제공 | 1 |

**카오스 응용 프로그램 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | 테스트 가능성 | 정보 제공 | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | 테스트 가능성 | 정보 제공 | 1 |

## <a name="service-events"></a>서비스 이벤트

**서비스 수명 주기 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | 정보 제공 | 1 |
| 18658 | ServiceDeletedOperational | FM | 정보 제공 | 1 |

**서비스 상태 보고서 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | 정보 제공 | 1 |
| 54433 | ExpiredServiceEventOperational | HM | 정보 제공 | 1 |

## <a name="partition-events"></a>파티션 이벤트

**파티션 이동 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | 정보 제공 | 1 |

**파티션 상태 보고서 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | 정보 제공 | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | 정보 제공 | 1 |

**카오스 파티션 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | 테스트 가능성 | 정보 제공 | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | 테스트 가능성 | 정보 제공 | 1 |

**파티션 분석 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | 테스트 가능성 | 정보 제공 | 1 |

## <a name="replica-events"></a>복제본 이벤트

**복제본 상태 보고서 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | 정보 제공 | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | 정보 제공 | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | 정보 제공 | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | 정보 제공 | 1 |

**카오스 복제본 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | 테스트 가능성 | 정보 제공 | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | 테스트 가능성 | 정보 제공 | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | 테스트 가능성 | 정보 제공 | 1 |

## <a name="container-events"></a>컨테이너 이벤트

**컨테이너 수명 주기 이벤트** 

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hosting | 정보 제공 | 1 |
| 23075 | ContainerDeactivatedOperational | Hosting | 정보 제공 | 1 |
| 23082 | ContainerExitedOperational | Hosting | 정보 제공 | 1 |

## <a name="other-events"></a>기타 이벤트

**상관 관계 이벤트**

| EventId | Name | 원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | 테스트 가능성 | 정보 제공 | 1 |

## <a name="events-prior-to-version-62"></a>버전 6.2 이전 이벤트

다음은 버전 6.2 이전의 Service Fabric이 제공하는 이벤트의 포괄적인 목록입니다.

| EventId | Name | 원본(태스크) | 수준 |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 정보 제공 |
| 25621 | NodeOpenedSuccess | FabricNode | 정보 제공 |
| 25622 | NodeOpenedFailed | FabricNode | 정보 제공 |
| 25623 | NodeClosing | FabricNode | 정보 제공 |
| 25624 | NodeClosed | FabricNode | 정보 제공 |
| 25625 | NodeAborting | FabricNode | 정보 제공 |
| 25626 | NodeAborted | FabricNode | 정보 제공 |
| 29627 | ClusterUpgradeStart | CM | 정보 제공 |
| 29628 | ClusterUpgradeComplete | CM | 정보 제공 |
| 29629 | ClusterUpgradeRollback | CM | 정보 제공 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 정보 제공 |
| 29631 | ClusterUpgradeDomainComplete | CM | 정보 제공 |
| 23074 | ContainerActivated | Hosting | 정보 제공 |
| 23075 | ContainerDeactivated | Hosting | 정보 제공 |
| 29620 | ApplicationCreated | CM | 정보 제공 |
| 29621 | ApplicationUpgradeStart | CM | 정보 제공 |
| 29622 | ApplicationUpgradeComplete | CM | 정보 제공 |
| 29623 | ApplicationUpgradeRollback | CM | 정보 제공 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 정보 제공 |
| 29625 | ApplicationDeleted | CM | 정보 제공 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 정보 제공 |
| 18566 | ServiceCreated | FM | 정보 제공 |
| 18567 | ServiceDeleted | FM | 정보 제공 |

## <a name="next-steps"></a>다음 단계

* Service Fabric에서 전반적인 [플랫폼 수준의 이벤트 생성](service-fabric-diagnostics-event-generation-infra.md)에 대해 자세히 알아보기
* 더 많은 로그를 수집하도록 [Azure 진단](service-fabric-diagnostics-event-aggregation-wad.md) 구성 수정
* 작동 채널 로그를 표시하도록 [Application Insights 설정](service-fabric-diagnostics-event-analysis-appinsights.md)
