---
title: Azure Storage 확장성 및 성능 목표 | Microsoft Docs
description: 표준 및 프리미엄 Storage 계정에 대한 용량, 요청 속도 및 인바운드 및 아웃 바운드 대역폭을 포함한 Azure Storage의 확장성 및 성능 목표를 알아보세요. Azure Storage 서비스 각각의 파티션에 대한 성능 목표를 이해해 보세요.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: e393bb9e7615b893699caf5a931ede5803046892
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Azure Storage 확장성 및 성능 목표
## <a name="overview"></a>개요
이 문서에서는 Azure Storage에 대한 확장성 및 성능을 설명합니다. 기타 Azure 제한 사항에 대한 요약은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-subscription-service-limits.md)을 참조하세요.

> [!NOTE]
> 모든 저장소 계정은 새로운 플랫 네트워크 토폴로지에서 실행되고 작성된 시기에 관계 없이 이 문서에 설명된 확장성 및 성능 목표를 지원합니다. Azure Storage 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure Storage: 일관성과 가용성이 뛰어난 클라우드 Storage 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)를 참조하세요.
> 

> [!IMPORTANT]
> 여기에 나열된 확장성 및 성능 목표는 최첨단 목표이지만 달성할 수 있습니다. 모든 경우, 계정 사용량에 따라 달성된 요청 속도 및 대역폭은 저장된 개채의 크기 및 응용 프로그램이 수행한 작업 형태에 따릅니다. 해당 성능이 요구 사항을 충족시키는지 여부를 확인 하려면 서비스를 반드시 테스트해야 합니다. 가능하면 트래픽 속도가 갑자기 증가하지 않고 파티션 간의 트래픽이 적절하게 분산되도록 보장합니다.
> 
> 응용 프로그램이 파티션의 작업 처리 가능한 제한에 도달하면 Azure Storage는 오류 코드 503(서버 작업 중) 또는 오류 코드 500(작업 시간 제한) 응답을 반송하기 시작합니다. 이러한 오류가 발생하는 경우 응용 프로그램은 재시도를 위한 지수 백오프 정책을 사용해야 합니다. 지수 백오프는 파티션에 대한 부하를 감소시키고 해당 파티션에 트래픽의 급증을 완화할 수 있습니다.
> 
> 

응용 프로그램의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우 여러 저장소 계정을 사용하도록 응용 프로그램을 빌드하면 됩니다. 그런 다음 해당 저장소 계정 간에 데이터 개체를 분할할 수 있습니다. 볼륨 가격에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 을 참조하세요.

## <a name="scalability-targets-for-a-storage-account"></a>저장소 계정의 확장성 목표
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 크기 조정 목표
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files 크기 조정 목표
Azure File 및 Azure File Sync의 크기 조정 및 성능 목표에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](../files/storage-files-scale-targets.md)를 참조하세요.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync 크기 조정 목표
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue 저장소 크기 조정 목표
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table 저장소 크기 조정 목표
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>참고 항목
* [저장소 가격 정보](https://azure.microsoft.com/pricing/details/storage/)
* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-subscription-service-limits.md)
* [Azure Storage 복제](../storage-redundancy.md)
* [Microsoft Azure Storage 성능 및 확장성 검사 목록](../storage-performance-checklist.md)
* [Microsoft Azure Storage: 일관성과 가용성이 뛰어난 클라우드 Storage 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

