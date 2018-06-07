---
title: Azure Service Fabric 클러스터 크기 조정 | Microsoft Docs
description: Service Fabric 클러스터의 스케일 인 또는 스케일 아웃, 규모 확장 또는 규모 축소에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: f9e3f190decdc907cf57a0235b9d7142081bd2f1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="scaling-service-fabric-clusters"></a>Service Fabric 클러스터 크기 조정
Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다. 클러스터의 일부인 컴퓨터 또는 VM을 노드라고 합니다. 클러스터는 잠재적으로 수천 개의 노드를 포함할 수 있습니다. Service Fabric 클러스터를 만든 후에 수평으로(노드 수 변경) 또는 수직으로(노드의 리소스 변경) 클러스터 크기를 조정할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 응용 프로그램 크기도 자동으로 조정됩니다.

클러스터 크기를 조정하는 이유 응용 프로그램 수요는 시간이 지남에 따라 달라집니다.  늘어난 응용 프로그램 워크로드나 네트워크 트래픽을 충족하기 위해 클러스터 리소스를 늘리고, 수요가 줄어들면 클러스터 리소스를 줄여야 할 수 있습니다.

### <a name="scaling-in-and-out-or-horizontal-scaling"></a>스케일 인/스케일 아웃 또는 수평적 크기 조정
클러스터의 노드 수를 변경합니다.  새 노드가 클러스터에 조인하면 [클러스터 리소스 관리자](service-fabric-cluster-resource-manager-introduction.md)에서 서비스를 이 노드로 이동하므로 기존 노드의 부하가 감소합니다.  클러스터의 리소스가 효율적으로 사용되지 않을 경우에도 노드 수를 줄일 수 있습니다.  노드가 클러스터를 떠나면 서비스는 해당 노드를 외부로 이동하므로 나머지 노드에서 부하가 늘어납니다.  해당 VM의 워크로드가 아닌 사용하는 VM 수에 따라 비용을 지불하게 되므로 Azure에서 실행되는 클러스터의 노드 수를 줄이면 비용을 절약할 수 있습니다.  

- 장점: 이론적으로는 제한없이 확장할 수 있습니다.  응용 프로그램이 확장 가능하게 디자인된 경우, 더 많은 노드를 추가하여 제한없이 증가할 수 있습니다.  클라우드 환경의 도구를 사용하면 노드를 손쉽게 추가 또는 제거할 수 있으므로 용량을 쉽게 조정할 수 있고, 사용한 리소스에 대해서만 비용을 지불하게 됩니다.  
- 단점: 응용 프로그램을 [확장 가능하게 디자인](service-fabric-concepts-scalability.md)해야 합니다.  응용 프로그램 데이터베이스 및 지속성도 확장하려면 추가적인 아키텍처 작업이 필요할 수 있습니다.  그렇지만 Service Fabric 상태 저장 서비스의 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)을 사용하면 응용 프로그램 데이터를 훨씬 더 쉽게 확장할 수 있습니다.

### <a name="scaling-up-and-down-or-vertical-scaling"></a>규모 확장 및 축소 또는 수직적 크기 조정 
클러스터에 있는 노드의 리소스(CPU, 메모리 또는 저장소)를 변경합니다.
- 이점: 소프트웨어 및 응용 프로그램 아키텍처가 동일하게 유지됩니다.
- 단점: 개별 노드에서 늘릴 수 있는 리소스 양이 제한되어 있으므로 확장 한계가 있습니다. 리소스를 추가하거나 제거하기 위해 물리적 컴퓨터 또는 가상 머신을 오프라인으로 전환해야 하므로 가동 중지 시간이 발생합니다.

## <a name="scaling-an-azure-cluster-in-or-out"></a>Azure 클러스터 스케일 인 또는 스케일 아웃
가상 머신 확장 집합은 가상 머신의 컬렉션을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설치](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. 

Azure 클러스터 크기를 조정할 때는 다음 지침에 유의하세요.
- 프로덕션 워크로드를 실행하는 주 노드 형식에는 항상 5개 이상의 노드가 있어야 합니다.
- 상태 저장 프로덕션 워크로드를 실행하는 주가 아닌 노드 형식에는 항상 5개 이상의 노드가 있어야 합니다.
- 상태 비저장 프로덕션 워크로드를 실행하는 주가 아닌 노드 형식에는 항상 2개 이상의 노드가 있어야 합니다.
- [내구성 수준](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)이 골드 또는 실버인 모든 노드 형식에는 항상 5개 이상의 노드가 있어야 합니다.
- 노드 형식에서 임의의 VM 인스턴스/노드를 삭제하지 말고, 항상 가상 머신 확장 집합 규모 축소 기능을 사용하세요. 임의 VM 인스턴스를 삭제하면 부하를 적절히 분산하는 시스템 기능에 부정적인 영향을 줄 수 있습니다.
- 자동 크기 조정 규칙을 사용하는 경우 스케일 인(VM 인스턴스 제거)을 한 번에 한 노드에서 수행하도록 규칙을 설정합니다. 한 번에 여러 인스턴스의 규모를 축소하는 방식은 안전하지 않습니다.

클러스터의 Service Fabric 노드 형식은 백 엔드에서 가상 머신 확장 집합으로 구성되므로 각 노드 형식/가상 머신 확장 집합에 대한 [자동 크기 조정 규칙을 설정하거나 수동으로 크기를 조정](service-fabric-cluster-scale-up-down.md)해야 합니다.

### <a name="programmatic-scaling"></a>프로그래밍 방식으로 크기 조정
많은 시나리오에서 [수동으로 또는 자동 크기 조정 규칙을 통해 클러스터 크기를 조정](service-fabric-cluster-scale-up-down.md)하는 것은 적절한 해결 방법입니다. 그렇지만 좀 더 고급 시나리오에는 적절하지 않을 수 있습니다. 이러한 접근 방식의 잠재적 단점은 다음과 같습니다.

- 수동으로 크기를 조정하려면 로그인하여 크기 조정 작업을 명시적으로 요청해야 합니다. 크기 조정 작업을 자주 또는 예기치 않은 시점에 수행해야 하는 경우에는 이 방법이 좋지 않을 수 있습니다.
- 자동 크기 조정 규칙은 가상 머신 확장 집합에서 인스턴스를 제거할 때 노드 형식의 내구성 수준이 Silver 또는 Gold가 아닌 한 연결된 Service Fabric 클러스터에서 해당 노드의 지식을 제거하지 않습니다. 자동 크기 조정 규칙은 Service Fabric 수준이 아닌 확장 집합 수준에서 작동하기 때문에 Service Fabric 노드를 정상적으로 종료하지 않아도 자동 크기 조정 규칙이 Service Fabric 노드를 제거할 수 있습니다. 이 강제 노드 제거는 규모 감축 작업 후 'ghost' Service Fabric 노드 상태를 남깁니다. 개인(또는 서비스)은 Service Fabric 클러스터에서 제거된 노드 상태를 주기적으로 정리해야 합니다.
- 내구성 수준이 Gold 또는 Silver인 노드 유형은 제거된 노드를 자동으로 정리하므로 추가 정리가 필요하지 않습니다.
- 자동 크기 조정 규칙이 지원되는 [여러 메트릭](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)이 있지만 아직은 제한된 집합입니다. 이 집합에 포함되지 않는 일부 메트릭을 기반으로 하는 크기 조정이 필요한 시나리오의 경우 자동 크기 조정 규칙은 좋은 옵션이 아닐 수 있습니다.

Service Fabric 크기 조정에 접근하는 방식은 시나리오에 달렸습니다. 크기 조정을 자주 하지 않는다면 노드를 수동으로 추가 또는 제거할 수만 있으면 충분할 것입니다. 좀 더 복잡한 시나리오의 경우 프로그래밍 방식으로 확장하는 기능을 노출하는 자동 크기 조정 규칙과 SDK가 강력한 대안이 될 수 있습니다.

응용 프로그램이 가상 머신 확장 집합 및 Service Fabric 클러스터를 프로그래밍 방식으로 작업할 수 있는 Azure API가 있습니다. 기존 자동 크기 조정 옵션이 시나리오에 적합하지 않은 경우 이러한 API를 사용하여 사용자 지정 크기 조정 논리를 구현할 수 있습니다. 

이 '홈 수행 '수제작' 자동 크기 조정 기능을 구현하는 한 가지 방법은 크기 조정 작업을 관리하는 새로운 상태 비저장 서비스를 Service Fabric 응용 프로그램에 추가하는 것입니다. 사용자 고유의 크기 조정 서비스를 만들면 응용 프로그램 동작을 최대한 원하는 대로 제어하고 사용자 지정할 수 있습니다. 응용 프로그램을 규모 감축 또는 규모 확장하는 시기와 방법을 정교하게 제어해야 하는 시나리오에는 이 방법이 유용할 수 있습니다. 그러나 이 제어 방법은 코드가 복잡해지는 단점이 있습니다. 이 방법을 사용하려면 특수한 크기 조정 코드가 필요합니다. 서비스의 `RunAsync` 메서드 내에서 트리거 집합은 크기 조정이 필요한지 여부(최대 클러스터 크기 및 크기 조정 휴지 시간 같은 매개 변수 확인 포함)를 확인할 수 있습니다.   

가상 머신 확장 집합 상호 작용(현재 가상 머신 인스턴스의 수를 확인하고 수정하는 작업 포함)에 사용되는 API는 [fluent Azure Management Compute 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/)입니다. fluent compute 라이브러리는 가상 머신 확장 집합과 상호 작용하는 간편한 API를 제공합니다.  Service Fabric 클러스터 자체와 상호 작용하려면 [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient)를 사용하세요.

그렇지만 크기를 조정하기 위해 클러스터에서 크기 조정 코드를 서비스로 실행할 필요는 없습니다. `IAzure` 및 `FabricClient`는 각각 연결된 Azure 리소스에 원격으로 연결하기 때문에 Service Fabric 응용 프로그램 외부에서 실행되는 콘솔 응용 프로그램 또는 Windows 서비스는 손쉽게 크기 조정 서비스가 될 수 있습니다.

이러한 제한에 따라 [보다 사용자 지정된 자동 크기 조정 모델을 구현](service-fabric-cluster-programmatic-scaling.md)하고자 할 수 있습니다.


## <a name="scaling-a-standalone-cluster-in-or-out"></a>독립 실행형 클러스터 스케일 인 또는 스케일 아웃
독립 실행형 클러스터를 사용하면 온-프레미스 또는 선택한 클라우드 공급자에 Service Fabric 클러스터를 배포할 수 있습니다.  노드 형식은 배포에 따라, 물리적 컴퓨터 또는 가상 머신으로 구성됩니다. Azure에서 실행되는 클러스터에 비해, 독립 실행형 클러스터의 크기 조정 프로세스는 좀 더 복잡합니다.  클러스터의 노드 수를 수동으로 변경한 후 클러스터 구성 업그레이드를 실행해야 합니다.

노드를 제거하여 여러 업그레이드를 시작할 수 있습니다. 일부 노드는 `IsSeedNode=”true”` 태그로 표시되고 [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest)를 사용하여 클러스터 매니페스트를 쿼리하여 확인할 수 있습니다. 시드 노드는 이러한 시나리오에서 이동되어야 하므로 이러한 노드의 제거는 다른 항목보다 더 오래 걸릴 수 있습니다. 클러스터는 최소 3가지 주 노드 유형 노드를 유지해야 합니다.

독립 실행형 클러스터 크기를 조정할 때는 다음 지침에 유의하세요.
- 주 노드 교체는 일괄 처리로 제거한 다음 추가하는 대신 차례로 수행되어야 합니다.
- 노드 유형을 제거하기 전에 노드 유형을 참조하는 노드가 있는지 확인합니다. 해당 노드 유형을 제거하기 전에 이러한 노드를 제거합니다. 모든 해당 노드가 제거된 후 클러스터 구성에서 NodeType을 제거하고 [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)를 사용하여 구성 업그레이드를 시작할 수 있습니다.

자세한 내용은 [독립 실행형 클러스터 크기 조정](service-fabric-cluster-windows-server-add-remove-nodes.md)을 참조하세요.

## <a name="scaling-an-azure-cluster-up-or-down"></a>Azure 클러스터 규모 확장 또는 축소
가상 머신 확장 집합은 가상 머신의 컬렉션을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설치](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다.  노드 형식의 규모를 확장 또는 축소할 경우 확장 집합에 있는 가상 머신 인스턴스의 SKU가 변경됩니다. 

> [!WARNING]
> [실버 내구성 이상](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)에서 실행되는 경우가 아니면 확장 집합/노드 형식의 VM SKU를 변경하지 않는 것이 좋습니다. VM SKU 크기 변경은 데이터 파괴적 내부 인프라 작업입니다. 이러한 변경을 지연하거나 모니터링하는 기능이 없으면 이러한 작업으로 인해 상태 저장 서비스에 대해 데이터 손실이 발생하거나 상태 비저장 워크로드의 경우에도 예기치 못한 다른 작동 문제가 발생할 수 있습니다. 
>

Azure 클러스터 크기를 조정할 때는 다음 지침에 유의하세요.
- 주 노드 형식의 규모를 축소하는 경우에는 [안정성 계층](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)에서 허용하는 범위 이상으로 규모를 축소하면 안 됩니다.

노드 형식 규모 확장 또는 축소 프로세스는 주 노드 형식인지 아닌지에 따라 달라집니다.

### <a name="scaling-non-primary-node-types"></a>주가 아닌 노드 형식 크기 조정
필요한 리소스를 사용하여 새 노드 형식을 만듭니다.  새 노드 형식을 포함하도록 실행 중인 서비스의 배치 제약 조건을 업데이트합니다.  클러스터의 안정성에 영향을 주지 않도록 점진적으로(한 번에 하나씩) 이전 노드 형식 인스턴스의 수를 0까지 줄입니다.  이전 노드 형식이 서비스 해제될 때 서비스는 새 노드 형식으로 점진적으로 마이그레이션됩니다.

### <a name="scaling-the-primary-node-type"></a>주 노드 형식 크기 조정
주 노드 형식의 VM SKU는 변경하지 않는 것이 좋습니다. 클러스터 용량이 더 필요한 경우 인스턴스를 더 추가하는 것이 좋습니다. 

이것이 불가능한 경우 새 클러스터를 만들고 구 클러스터에서 [응용 프로그램 상태를 복원](service-fabric-reliable-services-backup-restore.md)할 수 있습니다(해당하는 경우). 시스템 서비스 상태를 복원할 필요는 없습니다. 응용 프로그램을 새 클러스터에 배포하면 다시 만들어집니다. 클러스터에서 상태 비저장 응용 프로그램을 실행한 경우에만 응용 프로그램을 새 클러스터에 배포하기만 하면 되며 복원은 필요하지 않습니다. 지원되지 않은 경로를 선택하고 VM SKU를 변경하려는 경우 가상 머신 확장 집합 모델 정의를 수정하여 새 SKU를 반영합니다. 클러스터에 한 노드 형식만 있다면 모든 상태 저장 응용 프로그램이 모든 [서비스 복제본 수명 주기 이벤트](service-fabric-reliable-services-lifecycle.md)(예: 빌드의 복제본에 문제가 있을 경우)에 제때 응답하는지, 서비스 복제본 다시 빌드 기간이 5분 미만(실버 내구성 수준일 경우)인지 확인하세요. 

## <a name="next-steps"></a>다음 단계
* [응용 프로그램 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
* [Azure 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-tutorial-scale-cluster.md)합니다.
* 유용한 Azure Compute SDK를 사용하여 [Azure 클러스터의 크기를 프로그래밍 방식으로 조정](service-fabric-cluster-programmatic-scaling.md)합니다.
* [독립 실행형 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-cluster-windows-server-add-remove-nodes.md)합니다.

