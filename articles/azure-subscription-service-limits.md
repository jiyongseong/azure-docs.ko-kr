---
title: Azure 구독 제한 및 할당량 | Microsoft Docs
description: 일반적인 Azure 구독 및 서비스 제한, 할당량 및 제약 조건 목록을 제공합니다. 여기에는 최대값과 함께 제한을 늘리는 방법에 대한 정보가 포함됩니다.
services: ''
documentationcenter: ''
author: rothja
manager: jeffreyg
editor: ''
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: byvinyal
ms.openlocfilehash: 8a8482fa93ca902746e4fc8ef6d67b274a926bdc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939963"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 구독 및 서비스 제한, 할당량 및 제약 조건
이 문서는 때때로 할당량이라고도 하는 가장 일반적인 Microsoft Azure 제한의 일부를 나열합니다. 현재 이 문서에서는 일부 Azure 서비스에 대해 다룹니다. 시간 경과에 따라 이 목록은 더 많은 플랫폼에 적용되도록 확장 및 업데이트됩니다.

Azure 가격에 대한 자세한 정보는 [Azure 가격 책정 개요](https://azure.microsoft.com/pricing/)를 참조하세요. 여기에서 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하거나 서비스에 대한 가격 정보 페이지를 방문하여 비용을 예측할 수 있습니다(예: [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). 비용 관리에 대한 팁은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing/billing-getting-started.md)를 참조하세요.

> [!NOTE]
> **기본 제한**이상으로 제한 또는 할당량을 높이려는 경우 [무료로 온라인 고객 지원 요청을 개설](azure-resource-manager/resource-manager-quota-errors.md)합니다. 다음 표에 나오는 **최대 제한** 값 이상으로 제한을 높일 수 없습니다. **최대 제한** 열이 없는 경우는 리소스에 조정 가능한 제한이 없습니다.
>
> [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)은 제한하거나 할당량을 증가할 수 없습니다. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 평가판을 종량제로 업그레이드](billing/billing-upgrade-azure-subscription.md) 및 [평가판 구독 FAQ](https://azure.microsoft.com/free/free-account-faq)를 참조하세요.
>

## <a name="limits-and-the-azure-resource-manager"></a>제한 및 Azure 리소스 관리자
이제 단일 Azure 리소스 그룹에 여러 Azure 리소스를 결합할 수 있습니다. 리소스 그룹을 사용하는 경우 전역이었던 제한이 Azure 리소스 관리자에서 지역 수준으로 관리됩니다. Azure 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](azure-resource-manager/resource-group-overview.md)를 참조하세요.

아래 제한에서는 Azure 리소스 관리자를 사용할 때 제한에 차이를 반영할 수 있도록 새로운 테이블이 추가되었습니다. 예를 들어, **구독 제한** 테이블 및 **구독 제한 - Azure Resource Manager** 테이블이 있습니다. 두 시나리오에 모두 제한이 적용되면 첫 번째 테이블에서만 표시됩니다. 별도로 지정하지 않으면 제한은 모든 지역에 걸쳐 전역으로 적용됩니다.

> [!NOTE]
> Azure 리소스 그룹의 리소스에 대한 할당량은 구독을 통해 지역별로 액세스할 수 있으며, 구독별로는 액세스할 수 없는데 서비스 관리 할당량이 구독별로 액세스되기 때문입니다. vCPU 할당량을 한 예로 살펴보겠습니다. vCPU를 지원하는 할당량 증가를 요청해야 하는 경우 어떤 지역에서 얼마나 많은 vCPU를 사용할 것인지 결정한 다음, 원하는 금액 및 지역에 대한 Azure 리소스 그룹 vCPU 할당량에 대해 특정 요청을 만들어야 합니다. 따라서 유럽 서부 지역에서 30개의 vCPU를 사용하여 응용 프로그램을 실행해야 하는 경우, 유럽 서부에서 30개의 vCPU를 구체적으로 요청해야 합니다. 하지만 다른 지역에는 vCPU 할당량이 증가하지 않고 유럽 서부만 30개의 vCPU 할당량이 갖게 됩니다.
> <!-- -->
> 따라서 어떤 한 지역에서 워크로드에 필요한 Azure 리소스 그룹 할당량을 결정하고 배포를 고려 중인 각 지역에서 해당 금액을 요청하는 것이 유용할 수 있습니다. 특정 지역의 현재 할당량 검색에 대한 자세한 내용은 [배포 문제 해결](resource-manager-common-deployment-errors.md) 을 참조하세요.
>
>

## <a name="service-specific-limits"></a>서비스 특정 제한
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Event Grid](#azure-event-grid-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Redis 캐시(영문)](#azure-redis-cache-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [컨테이너 레지스트리](#container-registry-limits)
* [Kubernetes 서비스](#container-service-aks-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [IoT 허브](#iot-hub-limits)
* [IoT Hub Device Provisioning 서비스](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [모바일 고객 관리](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [모니터](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [네트워킹](#networking-limits)
* [Network Watcher](#network-watcher-limits)
* [알림 허브 서비스](#notification-hub-service-limits)
* [리소스 그룹](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [이를 통해 검색](#search-limits)
* [Service Bus](#service-bus-limits)
* [사이트 복구](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple 시스템](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [구독](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Virtual Machine Scale Sets](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>구독 제한
#### <a name="subscription-limits"></a>구독 제한
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>구독 제한 - Azure Resource Manager
Azure 리소스 관리자 및 Azure 리소스 그룹을 사용하는 경우 다음과 같은 제한이 적용됩니다. Azure 리소스 관리자에서 변경되지 않는 제한은 아래에 나열되지 않습니다. 이러한 제한에 대해서는 이전 테이블을 참조하세요.

리소스 관리자 요청의 제한 처리에 대한 정보는 [리소스 관리자 요청 제한](resource-manager-request-limits.md)을 참조하세요.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>리소스 그룹 제한
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines 제한
#### <a name="virtual-machine-limits"></a>Virtual Machine 제한
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines 제한 - Azure 리소스 관리자
Azure 리소스 관리자 및 Azure 리소스 그룹을 사용하는 경우 다음과 같은 제한이 적용됩니다. Azure 리소스 관리자에서 변경되지 않는 제한은 아래에 나열되지 않습니다. 이러한 제한에 대해서는 이전 테이블을 참조하세요.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Virtual Machine Scale Sets 제한
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances 제한
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry 제한
다음 표에서는 기본, 표준 및 프리미엄 [서비스 계층](./container-registry/container-registry-skus.md)의 기능 및 제한에 대해 자세히 설명합니다.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Kubernetes 서비스 제한
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>네트워킹 제한
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>네트워킹 제한
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway 제한
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher 제한
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager 제한
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS 제한
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>저장소 제한
Storage 계정 제한에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](storage/common/storage-scalability-targets.md)를 참조하세요.

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob 저장소 용량 한도
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files 한도
Azure Files 한도에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](storage/files/storage-files-scale-targets.md)를 참조하세요.

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure File Sync 한도
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue 저장소 용량 한도
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table 저장소 용량 한도
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>가상 머신 디스크 제한
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [가상 머신 크기](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

#### <a name="managed-virtual-machine-disks"></a>관리되는 가상 컴퓨터 디스크

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Cloud Services 제한
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service 제한
다음 App Service 제한에는 Web Apps, Mobile Apps, API Apps 및 Logic Apps에 대한 제한이 포함됩니다.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Scheduler 제한
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch 제한
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services 제한
다음 표에서는 Azure Biztalk 서비스에 대한 제한을 보여 줍니다.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 제한
Azure Cosmos DB는 어떠한 응용 프로그램의 요구도 처리하도록 처리량과 저장소 크기를 조정할 수 있는 뛰어난 확장성의 데이터베이스입니다. Azure Cosmos DB가 제공하는 규모에 대한 궁금한 사항은 askcosmosdb@microsoft.com에 메일을 보내 주세요.

### <a name="mobile-engagement-limits"></a>모바일 참여 제한
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>검색 제한
가격 책정 계층은 검색 서비스의 용량 및 제한을 결정합니다. 계층은 다음을 포함합니다.

* *무료* 다중 테넌트 서비스는 다른 Azure 구독자와 공유되며 평가 및 소규모 개발 프로젝트용으로 사용하기 위한 것입니다.
* *기본* 은 높은 가용성의 쿼리 작업에 대한 최대 3개의 복제본과 함께 프로덕션 워크로드 전용 컴퓨팅 리소스를 더 작은 규모로 제공합니다.
* *표준(S1, S2, S3, S3 고밀도)* 은 더 큰 프로덕션 작업용입니다. 표준 계층 내에는 여러 수준이 있으므로 워크로드 프로필에 가장 적합한 리소스 구성을 선택할 수 있습니다.

**구독당 제한**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**검색 서비스당 제한**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

문서 크기, 초당 쿼리, 키, 요청 및 응답을 비롯한 좀 더 자세한 수준의 제한 사항에 대한 자세한 내용은 [Azure Search의 서비스 제한 사항](search/search-limits-quotas-capacity.md)을 참조하세요.

### <a name="media-services-limits"></a>Media Services 제한
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>CDN 제한
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services 제한
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>모니터 제한
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>알림 허브 서비스 제한
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs 제한
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus 제한
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub 제한
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning 서비스 제한
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>데이터 팩터리 제한
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics 제한
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store 제한
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service 제한
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics 제한
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory 제한
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Azure Event Grid 제한
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps 제한
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple 시스템 제한
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics 한도
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Backup 제한
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>사이트 복구 제한
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights 제한
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API Management 제한
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis 캐시 제한
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>키 값 제한
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation 한도
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>SQL Database 제한
SQL Database 제한은 [SQL Database 리소스 제한](sql-database/sql-database-resource-limits.md)을 참조하세요.

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse 제한
SQL Data Warehouse 제한은 [SQL Data Warehouse 리소스 제한](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
[Azure 제한 및 증가 이해](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure를 위한 Virtual Machine 및 클라우드 서비스 크기](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Cloud Services 크기](cloud-services/cloud-services-sizes-specs.md)
