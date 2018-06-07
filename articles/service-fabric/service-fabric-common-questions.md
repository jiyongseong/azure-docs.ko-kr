---
title: Microsoft Azure Service Fabric에 대한 일반적인 질문 | Microsoft Docs
description: 다음은 Service Fabric에 대해 자주 묻는 몇 가지 질문과 그에 대한 답변입니다.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: a40432aa1d9a466706b4a3ebbcbd56cd8e5b768e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="commonly-asked-service-fabric-questions"></a>Service Fabric에 대해 자주 묻는 질문

Service Fabric으로 수행할 수 있는 작업 및 사용 방법에 대한 여러 가지 자주 묻는 질문이 있습니다. 이 문서에서는 자주 묻는 질문 및 그에 대한 답변을 설명합니다.

## <a name="cluster-setup-and-management"></a>클러스터 설정 및 관리

### <a name="how-do-i-rollback-my-service-fabric-cluster-certificate"></a>Service Fabric 클러스터 인증서를 롤백하려면 어떻게 해야 하나요?

응용 프로그램 업그레이드를 롤백하려면 Service Fabric 클러스터 쿼럼에서 변경 내용을 커밋하기 전에 상태 오류를 감지해야 합니다. 감지된 변경 내용만 롤포워드할 수 있습니다. 모니터링되지 않은 주요 인증서 변경 내용이 있는 경우 고객 지원 서비스의 에스컬레이션 엔지니어가 클러스터를 복구해야 할 수도 있습니다.  [Service Fabric의 응용 프로그램 업그레이드](https://review.docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-upgrade?branch=master)는 [응용 프로그램 업그레이드 매개 변수](https://review.docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)를 적용하고, 가동 중지 시간 0이라는 약속을 이행합니다.  Microsoft의 권장 응용 프로그램 업그레이드 모니터링 모드에 따라, 업데이트 도메인을 통한 자동 진행은 상태 검사 통과를 기반으로 하며, 기본 서비스 업데이트가 실패하는 경우 자동으로 롤백됩니다.
 
클러스터에서 여전히 Resource Manager 템플릿의 클래식 인증서 지문 속성을 사용하는 경우 최신 비밀 관리 기능을 활용할 수 있도록 [클러스터를 인증서 지문에서 일반 이름으로 변경](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)하는 것이 좋습니다.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>여러 Azure 지역 또는 나만의 데이터 센터에 걸쳐서 클러스터를 만들 수 있나요?

예. 

코어 Service Fabric 클러스터링 기술은 서로 네트워크로 연결되어 있기만 한다면 전 세계 어디서나 실행되는 컴퓨터를 결합하는 데 사용할 수 있습니다. 그러나 이러한 클러스터를 구축하고 실행하는 것은 복잡할 수 있습니다.

이 시나리오에 관심이 있는 경우 [Service Fabric Github 문제 목록](https://github.com/azure/service-fabric-issues)을 확인하거나 지원 담당자를 통해 추가 지침을 얻는 것이 좋습니다. Service Fabric 팀은 이 시나리오에 대해 추가적인 설명, 지침 및 권장 사항을 제공하기 위해 작업 중입니다. 

고려할 사항은 다음과 같습니다. 

1. 현재 Azure에서 Service Fabric 클러스터 리소스는 클러스터가 작성된 가상 머신 확장 집합이므로 지역에 국한됩니다. 즉, 하위 지역에서 오류가 발생할 경우 Azure Resource Manager 또는 Azure Portal을 통해 클러스터를 관리하지 못하게 될 수 있습니다. 클러스터는 계속 실행되는데도 이러한 문제가 발생할 수도 있으며, 이 경우 직접 개입하는 것이 나을 수 있습니다. 또한 현재 Azure에서는 하위 지역에 걸쳐 사용할 수 있는 단일 가상 네트워크를 유지하는 기능을 제공하지 않습니다. 즉, Azure의 다중 지역 클러스터에는 [VM Scale Sets의 각 VM에 대한 공용 IP 주소](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) 또는 [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)가 필요합니다. 이러한 네트워킹 선택 항목은 비용, 성능 및 응용 프로그램 디자인에 어느 정도 다르게 영향을 미치므로 이러한 환경을 구성하기 전에 신중하게 분석하고 계획해야 합니다.
2. 이러한 컴퓨터의 유지 관리, 관리, 모니터링은 복잡할 수 있으며 다른 클라우드 공급자 간 또는 온-프레미스 리소스와 Azure 간 등, 여러 환경 _유형_ 간에 걸쳐 있는 경우에는 특히 복잡할 수 있습니다. 이러한 환경에서 프로덕션 워크로드를 실행하기 전에 클러스터 및 응용 프로그램 둘 다에 대해 업그레이드, 모니터링, 관리 및 진단을 파악하도록 해야 합니다. Azure 또는 자체 데이터 센터에서 이러한 문제를 이미 해결한 경험이 있으면 Service Fabric 클러스터를 빌드하거나 실행할 때도 이러한 동일한 해결 방법을 적용할 수 있습니다. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric 노드에서 OS 업데이트를 자동으로 수신하나요?

당장은 아니지만, 이것 역시 Azure에서 제공하고자 하는 일반적인 요청입니다.

일단 Service Fabric 노드 아래의 운영 체제를 패치하고 최신 상태로 유지하는 [응용 프로그램을 제공](service-fabric-patch-orchestration-application.md)하고 있습니다.

OS 업데이트 문제는 일반적으로 컴퓨터를 재부팅해야 하며 이로 인해 일시적인 가용성 손실이 발생합니다. Service Fabric에서 해당 서비스에 대한 트래픽을 다른 노드로 리디렉션하므로 이것 자체는 문제가 아닙니다. 하지만 OS 업데이트가 클러스터 간에 조정되지 않는다면 많은 노드가 한 번에 다운될 가능성이 있습니다. 이러한 동시 재부팅은 서비스 또는 적어도 특정 파티션(상태 저장 서비스)에 대한 총체적인 가용성 손실을 유도할 수 있습니다.

향후에는 완전 자동화되고 업데이트 도메인 간에 조정되는 OS 업데이트 정책을 지원하여 재부팅하거나 다른 예기치 않은 오류가 발생해도 가용성이 유지되도록 할 것입니다.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>SF 클러스터에서 큰 가상 머신 확장 집합을 사용할 수 있나요? 

**간단한 대답** - 아니요 

**자세한 대답** - 큰 가상 머신 확장 집합은 최대 1000개 VM 인스턴스까지 가상 머신 확장 집합을 확장하도록 허용하지만 PG(배치 그룹)를 사용하여 이 작업을 수행합니다. FD(장애 도메인) 및 UD(업그레이드 도메인)는 Service Fabric이 서비스 복제본/서비스 인스턴스의 배치 결정을 내리는 데 사용하는 배치 그룹 내에서만 일관됩니다. FD 및 UD는 배치 그룹 내에서만 비교할 수 있으므로 SF에서 사용할 수 없습니다. 예를 들어 PG1의 VM1에 FD=0 토폴로지가 있고 PG2의 VM9에 FD=4 토폴로지가 있는 경우 VM1과 VM2가 두 개의 다른 하드웨어 랙에 있다는 의미는 아니므로 이 경우 SF에서 FD 값을 사용하여 배치 결정을 내릴 수 없습니다.

현재 큰 가상 머신 확장 집합에는 수준 4 부하 분산 지원 부족과 같은 기타 문제가 있습니다. [큰 크기 집합에 대한 세부 정보](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)를 참조하세요.



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Service Fabric 클러스터의 최소 크기는 어떻게 되나요? 작으면 안되는 이유는 무엇인가요?

프로덕션 워크로드를 시행하는 Service Fabric 클러스터에 지원되는 최소 크기는 5개 노드입니다. 개발/테스트 시나리오에 대해 3개의 노드 클러스터를 지원합니다.

Service Fabric 클러스터는 이름 지정 서비스 및 장애 조치(Failover) 관리자를 포함한 일련의 상태 저장 시스템 서비스를 실행하므로 이러한 최소 크기가 존재합니다. 이러한 클러스터에 어떤 서비스가 배포되었고 현재 호스트되는 위치를 추적하며 강력한 일관성을 따릅니다. 한편 강력한 일관성은 해당 서비스 상태로 특정 업데이트를 위해 *쿼럼*을 획득하는 기능에 의존하는데, 여기서 쿼럼은 지정된 서비스에 대한 복제본(N/2 +1)의 엄격한 다수성을 나타냅니다.

배경 정보로 몇 가지 가능한 클러스터 구성을 살펴보겠습니다.

**한 개 노드**: 이 옵션에서는 어떤 이유로든 단일 노드 손실이 전체 클러스터의 손실을 의미하므로 고가용성을 제공하지 않습니다.

**두 개 노드**: 두 노드(N = 2) 간에 배포된 서비스의 쿼럼은 2(2/2 + 1 = 2)입니다. 단일 복제본이 손실되면 쿼럼을 만들 수 없습니다. 서비스 업그레이드를 수행하려면 복제본을 일시적으로 종료해야 하므로 유용한 구성이 아닙니다.

**세 개 노드**: 세 개 노드(N=3)를 사용하며 쿼럼을 만들기 위한 요건은 계속해서 두 개 노드입니다(3/2 + 1 = 2). 따라서 개별 노드를 손실하고 쿼럼을 계속 유지할 수 있습니다.

업그레이드를 안전하게 수행하고 개별 노드 오류에도 대응 가능하므로(동시에 발생하지만 않는다면) 개발/테스트에 대해 세 개 노드 클러스터 구성이 지원됩니다. 프로덕션 워크로드를 위해 이러한 동시 오류에 대해 복원력이 있어야 하므로 5개의 노드가 필요합니다.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>비용 절감을 위해 야간/주말에 내 클러스터를 해제할 수 있나요?

일반적으로 그렇지 않습니다. Service Fabric은 임시 로컬 디스크에 상태를 저장하므로 가상 머신이 다른 호스트로 이동하는 경우 데이터가 이동하지 않습니다. 정상 작동 시에는 새 노드가 다른 노드에 의해 최신 상태가 되므로 문제가 되지 않습니다. 그러나 모든 노드를 중지하고 나중에 다시 시작하는 경우 대부분의 노드가 새 호스트에서 실행되고 시스템을 복구할 수 없을 가능성이 높습니다.

배포하기 전 응용 프로그램 테스트를 위해 클러스트를 만드는 경우 해당 클러스터를 [연속 통합/연속 배포 파이프라인](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)의 일부로 동적으로 만드는 것이 좋습니다.


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>운영 체제를 업그레이드(예: Windows Server 2012를 Windows Server 2016으로)하려면 어떻게 해야 하나요?

Microsoft는 환경 개선을 위해 노력하고 있지만 업그레이드에 대한 책임은 귀하에게 있습니다. 클러스터의 가상 머신에서 OS 이미지를 업그레이드하고 한 번에 하나의 VM에서 수행해야 합니다. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>클러스터 노드 형식(가상 머신 확장 집합)의 연결된 데이터 디스크를 암호화할 수 있나요?
예.  자세한 내용은 [연결된 데이터 디스크를 사용하여 클러스터 만들기](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [디스크 암호화(PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) 및 [디스크 암호화(CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md)를 참조하세요.

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>클러스터에서 바이러스 백신 프로그램을 실행하는 경우 제외해야 하는 디렉터리 및 프로세스는 무엇입니까?

| **바이러스 백신 제외된 디렉터리** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot(클러스터 구성에서) |
| FabricLogRoot(클러스터 구성에서) |

| **바이러스 백신 제외된 프로세스** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
## <a name="application-design"></a>응용 프로그램 설계

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Reliable Collection의 파티션에 대해 데이터를 쿼리하는 가장 좋은 방법은 무엇인가요?

일반적으로 Reliable Collection은 더 나은 성능 및 처리량을 달성하도록 규모 확장하기 위해 [분할](service-fabric-concepts-partitioning.md)됩니다. 즉, 지정된 서비스의 상태가 수십 또는 수백 대의 컴퓨터에 분배될 수 있습니다. 전체 데이터 집합에 대한 작업을 수행하기 위한 몇 가지 옵션이 있습니다.

- 다른 서비스의 모든 파티션을 쿼리하는 서비스를 만들어 필요한 데이터를 가져옵니다.
- 다른 서비스의 모든 파티션에서 데이터를 수신할 수 있는 서비스를 만듭니다.
- 각 서비스에서 외부 저장소로 주기적으로 데이터를 푸시합니다. 이 방법은 수행하는 쿼리가 핵심 비즈니스 논리에 포함되지 않는 경우에만 적절합니다.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>내 행위자에 대해 데이터를 쿼리하는 가장 좋은 방법은 무엇인가요?

행위자는 독립적인 상태 및 계산 단위로 설계되므로 런타임에는 행위자 상태에 대한 광범위한 쿼리를 수행하지 않는 것이 좋습니다. 전체 행위자 상태 집합에 대해 쿼리를 해야 하는 경우 다음을 고려합니다.

- 행위자 서비스를 상태 저장 신뢰할 수 있는 서비스로 바꿔서 해당 네트워크 요청 수로 해당 행위자 수의 모든 데이터를 서비스의 해당 파티션 수로 수집하도록 합니다.
- 상태를 외부 저장소로 주기적으로 푸시하도록 행위자를 설계하여 간편하게 쿼리할 수 있도록 합니다. 위에서처럼 이 방법은 수행 중인 쿼리가 런타임 동작에 대해 필요하지 않은 경우에만 필요합니다.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Reliable Collection에 저장할 수 있는 데이터는 얼마나 되나요?

Reliable Services는 일반적으로 분할되므로 저장할 수 있는 양은 클러스터에 있는 컴퓨터 수와 해당 컴퓨터에서 사용할 수 있는 메모리 양에 의해서만 제한됩니다.

예를 들어 평균 1kb 크기의 개체를 저장하는 100개 파티션과 3개 복제본을 포함하는 신뢰할 수 있는 컬렉션이 서비스에 있다고 가정합니다. 이제 컴퓨터당 16gb의 메모리가 있는 10개의 컴퓨터 클러스터가 있다고 가정합니다. 간단하고 무난한 작업을 위해 운영 체제 및 시스템 서비스, Service Fabric 런타임 및 사용자 서비스가 이 중에서 6gb를 사용하며 컴퓨터당 10gb(클러스터의 경우 100gb)가 사용 가능하다고 가정합니다.

각 개체는 세 번(주에서 1번, 복제본에서 2번) 저장되어야 하며 전체 용량으로 작동할 때 컬렉션에서 약 3천 500만 개체에 대해 충분한 메모리를 포함합니다. 하지만 오류 도메인 및 업그레이드 도메인의 동시 손실에 복원력을 갖추는 것이 좋습니다. 동시 손실의 경우 용량의 약 1/3을 나타내므로 약 2천 300만 개체로 감소합니다.

이 계산에서는 다음 사항도 가정합니다.

- 파티션 간 데이터 분포는 대략적으로 균일하거나 부하 메트릭을 Cluster Resource Manager에 보고합니다. 기본적으로 Service Fabric은 복제본 수에 따라 부하 분산을 수행합니다. 앞의 예제에서는 클러스터의 각 노드에 주 복제본 10개와 보조 복제본 20개를 배치합니다. 파티션 간에 부하가 고르게 분포된 경우에는 원활하게 작동합니다. 부하가 고르게 분포되지 않은 경우 로드를 보고하여 Resource Manager에서 작은 크기의 복제본을 함께 패킹하여 큰 복제본이 개별 노드에서 더 많은 메모리를 사용하도록 합니다.

- 문제가 되는 Reliable Services는 클러스터에서 유일한 저장 상태입니다. 여러 서비스를 클러스터에 배포할 수 있으므로 각각 해당 상태를 실행 및 관리해야 하는 리소스에 유념해야 합니다.

- 클러스터 자체는 증가 또는 감소하지 않습니다. 더 많은 컴퓨터를 추가하는 경우 개별 복제본은 컴퓨터 간에 걸쳐 있을 수 없으므로 컴퓨터 수가 서비스의 파티션 수를 초과할 때까지 Service Fabric은 복제본의 부하를 다시 조정하여 추가 용량을 활용합니다. 이와 반대로 컴퓨터를 제거하여 클러스터 크기를 줄이는 경우 복제본을 보다 조밀하게 압축하면 전체 용량이 감소합니다.

### <a name="how-much-data-can-i-store-in-an-actor"></a>행위자에 저장할 수 있는 데이터는 얼마나 되나요?

Reliable Services와 마찬가지로 행위자 서비스에 저장할 수 있는 데이터 양은 클러스터에 있는 노드 간에 사용 가능한 총 디스크 공간 및 메모리 양에 의해서만 제한됩니다. 하지만 개별 행위자는 적은 양의 상태 및 관련 비즈니스 논리를 캡슐화하는 데 사용할 경우 가장 효과적입니다. 일반적으로 개별 행위자는 킬로바이트 단위로 측정되는 상태를 포함하게 됩니다.

## <a name="other-questions"></a>기타 질문

### <a name="how-does-service-fabric-relate-to-containers"></a>Service Fabric은 컨테이너와 어떻게 연결되나요?

컨테이너는 서비스와 해당 종속성을 패키지하는 간단한 방법을 제공하여 모든 환경에서 일관성 있게 실행되고 단일 컴퓨터에서 격리된 방식으로 작동할 수 있도록 합니다. Service Fabric은 [컨테이너에 패키지된 서비스](service-fabric-containers-overview.md)를 비롯하여 서비스를 배포 및 관리하는 방법을 제공합니다.

### <a name="are-you-planning-to-open-source-service-fabric"></a>Service Fabric의 오픈 소스 계획이 있나요?

GitHub의 Service Fabric 일부에 대해 오픈 소스를 제공하였으며([신뢰할 수 있는 서비스 프레임워크](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [신뢰할 수 있는 작업자 프레임워크](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core 통합 라이브러리](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer) 및 [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)), 해당 프로젝트에 대한 커뮤니티 참여를 받고 있습니다. 

Service Fabric 런타임의 오픈 소스를 제공할 계획임을 [최근에 발표했습니다](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/). 현재 GitHub의 Linux 빌드 및 테스트 도구가 포함된 [Service Fabric 리포지토리](https://github.com/Microsoft/service-fabric/)가 있습니다. 즉, 리포지토리를 복제하고, Linux용 Service Fabric을 빌드하고, 기본 테스트를 실행하고, 현안을 공개하고, 끌어오기 요청을 제출할 수 있습니다. 마이그레이션된 Windows 빌드 환경과 함께 완벽한 CI 환경을 제공하기 위해 최선을 다하고 있습니다.

공지되면 [Service Fabric 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 자세한 내용을 확인하세요.

## <a name="next-steps"></a>다음 단계

- [핵심 Service Fabric 개념 및 모범 사례에 대해 알아보기](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
