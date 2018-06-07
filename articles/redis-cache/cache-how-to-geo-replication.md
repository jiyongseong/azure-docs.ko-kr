---
title: "Azure Redis Cache에 대해 지역에서 복제를 구성하는 방법 | Microsoft Docs"
description: "지역 간에 Azure Redis Cache 인스턴스를 복제하는 방법을 알아봅니다."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: wesmc
ms.openlocfilehash: 883683f6af7943fa4da49095c9a15aefd5cfa719
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Azure Redis Cache에 대해 지역에서 복제를 구성하는 방법

지역에서 복제는 두 개의 프리미엄 계층 Azure Redis Cache 인스턴스를 연결하는 메커니즘을 제공합니다. 한 캐시는 주 연결된 캐시로 지정하고 다른 캐시는 보조 연결된 캐시로 지정합니다. 보조 연결된 캐시는 읽기 전용이 되고 주 캐시에 쓴 데이터는 보조 연결된 캐시에 복제됩니다. 이 기능은 Azure 지역 간에 캐시를 복제하는 데 사용할 수 있습니다. 이 문서에서는 프리미엄 계층 Azure Redis Cache 인스턴스에 대해 지역에서 복제를 구성하는 지침을 제공합니다.

## <a name="geo-replication-prerequisites"></a>지역에서 복제 필수 조건

두 캐시 간에 지역에서 복제를 구성하려면 다음 필수 조건을 충족해야 합니다.

- 두 캐시 모두 [프리미엄 계층](cache-premium-tier-intro.md) 캐시여야 합니다.
- 두 캐시 모두 동일한 Azure 구독에 있어야 합니다.
- 보조 연결된 캐시는 주 연결된 캐시와 동일한 가격 책정 계층이거나 더 큰 가격 책정 계층이어야 합니다.
- 주 연결된 캐시에서 클러스터링을 사용하는 경우 보조 연결된 캐시는 분할된 데이터베이스 수가 주 연결된 캐시와 동일한 클러스터링을 사용해야 합니다.
- 두 캐시 모두 만들어야 하며 실행 중 상태여야 합니다.
- 어느 캐시에서도 지속성을 사용하도록 설정하면 안 됩니다.
- 동일한 VNET에 있는 캐시 간의 지역에서 복제가 지원됩니다. VNET의 리소스가 TCP 연결을 통해 서로 연결할 수 있는 방식으로 두 VNET이 구성된 경우에는 다른 VNET에 있는 캐시 간의 지역에서 복제도 지원됩니다.

지역에서 복제를 구성한 후에는 연결된 캐시 쌍에 다음과 같은 제한이 적용됩니다.

- 보조 연결된 캐시는 읽기 전용입니다. 이 캐시에서 읽을 수는 있지만 데이터를 쓸 수 없습니다. 
- 링크를 추가하기 전에 보조 연결된 캐시에 있던 모든 데이터가 제거됩니다. 그러나 이후에 지역에서 복제를 제거하면 복제된 데이터는 보조 연결된 캐시에 유지됩니다.
- 어느 캐시에서도 [크기 조정 작업](cache-how-to-scale.md)을 시작할 수 없거나 캐시에 클러스터링이 설정된 경우 [분할된 데이터베이스 수를 변경](cache-how-to-premium-clustering.md)할 수 없습니다.
- 어느 캐시에서도 지속성을 사용하도록 설정할 수 없습니다.
- [내보내기](cache-how-to-import-export-data.md#export)는 두 캐시에서 사용할 수 있지만 [가져오기](cache-how-to-import-export-data.md#import)는 기본 연결된 캐시에서만 사용할 수 있습니다.
- 지역에서 복제 링크를 제거할 때까지 연결된 캐시 또는 캐시가 포함된 리소스 그룹을 삭제할 수 없습니다. 자세한 내용은 [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)를 참조하세요.
- 두 캐시가 다른 지역에 있는 경우 네트워크 송신 비용이 지역 간에 보조 연결된 캐시로 복제된 데이터에 적용됩니다. 자세한 내용은 [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)를 참조하세요.
- 주 캐시 및 해당 복제본이 중단되는 경우 보조 연결된 캐시로 자동 장애 조치(failover)되지 않습니다. 클라이언트 응용 프로그램을 장애 조치(failover)하려면 지역에서 복제 링크를 수동으로 제거하고 클라이언트 응용 프로그램이 이전의 보조 연결된 캐시였던 캐시를 가리키도록 해야 합니다. 자세한 내용은 [보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?](#how-does-failing-over-to-the-secondary-linked-cache-work)를 참조하세요.

## <a name="add-a-geo-replication-link"></a>지역에서 복제 링크 추가

1. 지역에서 복제를 위해 두 프리미엄 캐시를 함께 연결하려면 주 연결된 캐시로 사용된 캐시의 리소스 메뉴에서 **지역에서 복제**를 클릭한 다음 **지역에서 복제** 블레이드에서 **Add cache replication link**(캐시 복제 링크 추가)를 클릭합니다.

    ![링크 추가](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. **Compatible caches**(호환되는 캐시) 목록에서 원하는 보조 캐시의 이름을 클릭합니다. 원하는 캐시가 목록에 표시되지 않으면 원하는 보조 캐시에 대한 [지역에서 복제 필수 조건](#geo-replication-prerequisites)이 충족되는지 확인합니다. 지역으로 캐시를 필터링하려면 지도에서 원하는 지역을 클릭하여 해당 캐시만 **Compatible caches**(호환되는 캐시) 목록에 표시합니다.

    ![지역에서 복제 호환되는 캐시](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    상황에 맞는 메뉴를 사용하여 연결 프로세스를 시작하거나 보조 캐시에 대한 세부 정보를 볼 수도 있습니다.

    ![지역에서 복제 상황에 맞는 메뉴](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. **링크**를 클릭하여 두 캐시를 함께 연결하고 복제 프로세스를 시작합니다.

    ![캐시 연결](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. **지역에서 복제** 블레이드에서 복제 프로세스의 진행률을 볼 수 있습니다.

    ![연결 상태](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    주 캐시와 보조 캐시에 대한 **개요** 블레이드에서 연결 상태를 볼 수도 있습니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    복제 프로세스가 완료되면 **링크 상태**가 **성공**으로 바뀝니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    연결 프로세스 중에 주 연결된 캐시는 계속 사용할 수 있지만 보조 연결된 캐시는 연결 프로세스가 완료된 다음에야 사용할 수 있습니다.

## <a name="remove-a-geo-replication-link"></a>지역에서 복제 링크 제거

1. 두 캐시 간 링크를 제거하고 지역에서 복제를 중지하려면 **지역에서 복제** 블레이드에서 **Unlink caches**(캐시 연결 해제)를 클릭합니다.
    
    ![캐시 연결 해제](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    연결 해제 프로세스가 완료되면 보조 캐시를 읽기 및 쓰기에 사용할 수 있습니다.

>[!NOTE]
>지역에서 복제 링크를 제거해도 주 연결된 캐시에서 복제된 데이터는 보조 캐시에 유지됩니다.
>
>

## <a name="geo-replication-faq"></a>지역에서 복제 FAQ

- [표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [둘 이상의 캐시를 함께 연결할 수 있나요?](#can-i-link-more-than-two-caches-together)
- [서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [크기가 다른 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-with-different-sizes)
- [클러스터링이 설정된 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-clustering-enabled)
- [VNET의 캐시에 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Redis 지역에서 복제의 복제 일정이란?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [지역에서 복제에 걸리는 기간은 얼마나 되나요?](#how-long-does-geo-replication-replication-take)
- [복제 복구 지점이 보장되나요?](#is-the-replication-recovery-point-guaranteed)
- [PowerShell 또는 Azure CLI를 사용하여 지역에서 복제를 관리할 수 있나요?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?

아니요, 지역에서 복제는 프리미엄 계층 캐시에만 사용할 수 있습니다.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?

- 지역에서 복제를 위해 두 캐시를 함께 연결하는 경우 주 연결된 캐시는 계속 사용할 수 있지만 보조 연결된 캐시는 연결 프로세스가 완료된 다음에야 사용할 수 있습니다.
- 두 캐시 간에 지역에서 복제 링크를 제거하면 두 캐시 모두 계속 사용할 수 있습니다.

### <a name="can-i-link-more-than-two-caches-together"></a>둘 이상의 캐시를 함께 연결할 수 있나요?

아니요, 지역에서 복제를 사용하는 경우 두 개의 캐시만 함께 연결할 수 있습니다.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?

아니요, 두 캐시 모두 동일한 Azure 구독에 있어야 합니다.

### <a name="can-i-link-two-caches-with-different-sizes"></a>크기가 다른 두 캐시를 연결할 수 있나요?

예, 보조 연결된 캐시가 주 연결된 캐시보다 큰 경우 연결할 수 있습니다.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>클러스터링이 설정된 지역에서 복제를 사용할 수 있나요?

예, 두 캐시의 분할된 데이터베이스 수가 동일한 경우 사용할 수 있습니다.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNET의 캐시에 지역에서 복제를 사용할 수 있나요?

예, VNET의 캐시에 대한 지역에서 복제가 지원됩니다. 

- 동일한 VNET에 있는 캐시 간의 지역에서 복제가 지원됩니다.
- VNET의 리소스가 TCP 연결을 통해 서로 연결할 수 있는 방식으로 두 VNET이 구성된 경우에는 다른 VNET에 있는 캐시 간의 지역에서 복제도 지원됩니다.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Redis 지역에서 복제의 복제 일정이란?

복제는 특정 일정에 따라 진행되지 않으며 연속 및 비동기로 진행됩니다. 즉, 주 복제본에 대해 수행된 모든 쓰기는 보조 복제본에서 즉시 비동기적으로 복제됩니다.

### <a name="how-long-does-geo-replication-replication-take"></a>지역에서 복제에 걸리는 기간은 얼마나 되나요?

복제는 점진적이고, 비동기적이고, 연속되며, 소요되는 시간은 일반적으로 지역 간 대기 시간과는 크게 다릅니다. 특정 상황에서 특정 시간에 보조 복제본은 주 복제본의 데이터에 대해 전체 동기화를 수행해야 할 수 있습니다. 이 경우 복제 시간은 주 캐시의 부하, 캐시 컴퓨터에서 사용 가능한 대역폭, 지역 간 대기 시간 등의 다양한 요소에 따라 좌우됩니다. 예를 들어 테스트를 통해, 미국 동부 지역 및 미국 서부 지역의 전체 53GB 지리적 복제 쌍에 대한 복제 시간이 5~10분 사이일 수 있다는 것을 알게 되었습니다.

### <a name="is-the-replication-recovery-point-guaranteed"></a>복제 복구 지점이 보장되나요?

현재 지리적 복제 모드의 캐시의 경우 지속성 및 가져오기/내보내기 기능이 사용되지 않도록 설정됩니다. 따라서 고객이 시작한 장애 조치(Failover)의 경우나 지리적 복제 쌍 간에 복제 링크가 끊어진 경우, 보조 복제본은 주 복제본에서 동기화한 메모리 내 데이터를 해당 시점까지 유지합니다. 이러한 경우 복구 지점 보장은 제공되지 않습니다.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>PowerShell 또는 Azure CLI를 사용하여 지역에서 복제를 관리할 수 있나요?

현재 지역에서 복제는 Azure Portal을 통해서만 관리할 수 있습니다.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?

지역에서 복제를 사용하면 주 연결된 캐시의 데이터가 보조 연결된 캐시에 복제됩니다. 두 연결된 캐시가 같은 Azure 지역에 있는 경우 데이터 전송에 대한 요금이 부과되지 않습니다. 두 연결된 캐시가 다른 Azure 지역에 있는 경우 지역에서 복제 데이터 전송 요금은 해당 데이터를 다른 Azure 지역에 복제하는 대역폭 비용입니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?

두 개의 캐시가 함께 연결된 경우 지역에서 복제 링크를 제거할 때까지 연결된 캐시 또는 캐시가 포함된 리소스 그룹을 삭제할 수 없습니다. 연결된 캐시 중 하나 또는 둘 다를 포함하는 리소스 그룹을 삭제하려고 하면, 리소스 그룹의 다른 리소스는 삭제되지만 해당 리소스 그룹은 `deleting` 상태로 유지되고 이 리소스 그룹의 연결된 캐시는 모두 `running` 상태로 남아 있습니다. 리소스 그룹 및 연결된 캐시의 삭제를 완료하려면 [지역에서 복제 링크 제거](#remove-a-geo-replication-link)에서 설명한 대로 지역에서 복제 링크를 중단합니다.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?

일반적으로 캐시는 해당 캐시에 액세스하는 응용 프로그램과 동일한 Azure 지역에 있는 것이 좋습니다. 응용 프로그램에 주 지역과 대체 지역이 있는 경우 주 캐시와 보조 캐시가 동일한 지역에 있어야 합니다. 쌍을 이루는 지역에 대한 자세한 내용은 [모범 사례 - Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조하세요.

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?

지역에서 복제의 초기 릴리스에서는 Azure Redis Cache가 Azure 지역 간의 자동 장애 조치(failover)를 지원하지 않습니다. 지역에서 복제는 주로 재해 복구 시나리오에서 사용됩니다. 재해 복구 시나리오에서 고객은 개별 응용 프로그램 구성 요소가 백업을 자체적으로 전환할 시기를 결정할 수 있도록 하는 것이 아니라 조정된 방식으로 전체 응용 프로그램 스택을 백업 지역에 표시해야 합니다. 특히 Redis와 관련이 있습니다. Redis의 주요 이점 중 하나는 대기 시간이 매우 짧은 저장소라는 점입니다. 응용 프로그램에서 사용하는 Redis는 다른 Azure 지역으로 장애 조치(failover)되지만 계산 계층은 그렇지 않을 경우 왕복 시간이 추가되어 성능에 크게 영향을 미칠 수 있습니다. 따라서 일시적인 가용성 문제로 인해 Redis에서 자동으로 장애 조치(failover)하는 것을 방지하려고 합니다.

현재 장애 조치(failover)를 시작하려면 Azure Portal에서 지역에서 복제 링크를 제거하고 Redis 클라이언트에서 연결 끝점을 주 연결된 캐시에서 (이전의 연결된) 보조 캐시로 변경해야 합니다. 두 캐시의 연결이 해제되면 복제본은 다시 일반적인 읽기/쓰기 캐시가 되고 Redis 클라이언트에서 직접 요청을 수락합니다.


## <a name="next-steps"></a>다음 단계

[Azure Redis Cache 프리미엄 계층](cache-premium-tier-intro.md)에 대해 자세히 알아봅니다.

