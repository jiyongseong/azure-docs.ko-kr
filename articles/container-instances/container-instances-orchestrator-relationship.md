---
title: Azure Container Instances 및 컨테이너 오케스트레이션
description: Azure Container Instances가 컨테이너 오케스트레이터와 상호 작용하는 방법을 이해합니다
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/23/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: e1455cba004facfa03dca21544eec754f5dc60be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances 및 컨테이너 오케스트레이터

크기가 작고 응용 프로그램 방향이기 때문에 컨테이너는 신속한 배달 환경 및 마이크로 서비스 기반 아키텍처에 적합합니다. 많은 수의 컨테이너를 자동화하고 관리하는 방법 및 상호 작용하는 방법을 *오케스트레이션*이라고 합니다. 많이 사용되는 오케스트레이터에는 Kubernetes, DC/OS 및 Docker Swarm이 포함됩니다.

Azure Container Instances는 오케스트레이션 플랫폼의 기본 일정 관리 기능 중 일부를 제공합니다. 또한 해당 플랫폼에서 제공하는 고급 서비스를 포함하지 않는 반면 Azure Container Instances는 이를 보완할 수 있습니다. 이 아티클에서는 Azure Container Instances에서 처리하는 범위 및 전체 컨테이너 오케스트레이터가 상호 작용할 수 있는 방법에 대해 설명합니다.

## <a name="traditional-orchestration"></a>기존 오케스트레이션

오케스트레이션의 표준 정의에는 다음 작업이 포함됩니다.

- **예약**: 컨테이너 이미지 및 리소스 요청을 지정하여 컨테이너를 실행할 적절한 컴퓨터를 찾습니다.
- **선호도/반선호도**: 서로 가깝거나(성능 목적) 충분히 멀리 떨어져 있는(가용성 목적) 컨테이너 집합이 실행되도록 지정합니다.
- **상태 모니터링**: 컨테이너 오류를 관찰하여 자동으로 일정을 다시 조정합니다.
- **장애 조치**: 각 컴퓨터에서 실행되는 작업을 추적하고 실패한 컴퓨터의 컨테이너를 정상 노드로 다시 예약합니다.
- **크기 조정**: 수동 또는 자동으로 요청에 맞게 Container Instance를 추가하거나 제거합니다.
- **네트워킹**: 여러 호스트 컴퓨터 간에 통신하도록 컨테이너를 조정하는 오버레이 네트워크를 제공합니다.
- **서비스 검색**: 컨테이너가 호스트 컴퓨터 간에 이동하면서 IP 주소를 변경하는 경우에도 자동으로 서로 찾을 수 있게 합니다.
- **조정된 응용 프로그램 업그레이드**: 응용 프로그램 작동 중단 시간을 방지하고 문제가 발생한 경우 롤백할 수 있도록 컨테이너 업그레이드를 관리합니다.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Azure Container Instances와 오케스트레이션: 계층화된 접근 방식

Azure Container Instances를 사용하여 계층화된 접근 방식을 오케스트레이션할 수 있고 단일 컨테이너를 실행하는 데 필요한 예약 및 관리 기능을 제공하면서 동시에 오케스트레이터 플랫폼이 다중 컨테이너 작업을 관리할 수 있도록 합니다.

Container Instances에 대한 기본 인프라가 Azure에서 관리되기 때문에 오케스트레이터 플랫폼은 단일 컨테이너를 실행하는 적절한 호스트 컴퓨터를 찾는 작업을 처리할 필요가 없습니다. 클라우드의 탄력성을 항상 사용할 수 있도록 합니다. 대신, 오케스트레이터는 크기 조정 및 조정된 업그레이드를 비롯한 다중 컨테이너 아키텍처의 개발을 간소화하는 작업에 집중할 수 있습니다.

## <a name="potential-scenarios"></a>잠재적 시나리오

Azure Container Instances를 포함한 오케스트레이터 통합이 여전히 초기 상태인 동안 몇 가지 다양한 환경이 제공됩니다.

### <a name="orchestration-of-container-instances-exclusively"></a>Container Instances의 오케스트레이션 독점

신속하게 시작하고 즉시 요금을 청구하기 때문에 Azure Container Instances에 기반한 독점 환경은 매우 가변적인 작업을 시작하고 처리하는 가장 빠른 방법을 제공합니다.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Virtual Machines에서 Container Instances 및 컨테이너 조합

안정된 장기 실행 작업의 경우 전용 가상 머신의 클러스터에 있는 컨테이너를 조정하는 작업은 일반적으로 Azure Container Instances와 동일한 컨테이너를 실행하는 것보다 비용이 더 적게 듭니다. 그러나 Container Instances는 예기치 않은 단기 사용량 급증을 다루는 전체 수용작업량을 신속하게 확장하고 축소하는 최고의 솔루션을 제공합니다.

클러스터에서 가상 머신의 수를 확장한 다음, 해당 컴퓨터에 추가 컨테이너를 배포하는 대신 오케스트레이터는 단순하게 Azure Container Instances에서 추가 컨테이너를 예약하고 더 이상 필요하지 않은 경우 삭제할 수 있습니다.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>샘플 구현: Kubernetes의 Azure Container Instances 커넥터

컨테이너 오케스트레이션 플랫폼이 Azure Container Instances와 통합하는 방법을 보여주기 위해 [Kubernetes의 샘플 커넥터][aci-connector-k8s]를 빌드하기 시작했습니다.

Kubernetes의 커넥터는 수용작업량이 무제한인 노드로 등록하고 [포드][pod-doc] 생성을 Azure Container Instances의 컨테이너 그룹으로 디스패치하여 [kubelet][kubelet-doc]을 모방합니다.

다른 오케스트레이터의 커넥터는 Azure Container Instances에서 컨테이너를 관리하는 신속성 및 간소성을 통해 오케스트레이터 API의 기능을 결합하기 위해 플랫폼 기본 형식과 마찬가지로 통합되어 빌드될 수 있습니다.

> [!WARNING]
> Kubernetes용 ACI 커넥터는 *실험적*이므로 프로덕션에서 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작 가이드](container-instances-quickstart.md)를 사용하여 Azure Container Instances에서 첫 번째 컨테이너를 만듭니다.

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/