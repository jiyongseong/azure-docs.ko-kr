---
title: "Azure Files 확장성 및 성능 목표 | Microsoft Docs"
description: "용량, 요청 속도 및 인바운드/아웃바운드 대역폭 제한을 포함하여 Azure Files의 확장성 및 성능 목표를 알아봅니다."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 381e96a0a777415b916e4093fe55aa0d355782a1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure Files 확장성 및 성능 목표
[Azure Files](storage-files-introduction.md)는 산업 표준 SMB 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. 이 문서에서는 Azure Files 및 Azure File Sync(미리 보기)의 확장성 및 성능 목표에 대해 설명합니다.

여기서 나열하는 확장성 및 성능 목표는 고성능 목표이지만 다른 배포 변수의 영향을 받을 수 있습니다. 예를 들어 Azure Files 서비스를 호스팅하는 서버뿐만 아니라 사용 가능한 네트워크 대역폭에 따라 파일 처리량이 제한될 수 있습니다. Azure Files의 확장성 및 성능이 요구 사항을 충족하는지 확인하려면 사용 패턴을 테스트하는 것이 좋습니다. 또한 시간이 지남에 따라 이러한 제한을 높이기 위해 노력하고 있습니다. 아래의 설명 또는 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에서 주저하지 마시고 늘리려는 제한에 대한 의견을 보내주세요.

## <a name="azure-storage-account-scale-targets"></a>Azure 저장소 계정의 크기 조정 목표
Azure File 공유에 대한 부모 리소스는 Azure 저장소 계정입니다. 저장소 계정은 Azure에 있는 저장소 풀을 나타내며, Azure Files를 포함한 여러 저장소 서비스에서 데이터를 저장하는 데 사용할 수 있습니다. 저장소 계정에 데이터를 저장하는 다른 서비스로 Azure Blob 저장소, Azure 큐 저장소 및 Azure 테이블 저장소가 있습니다. 저장소 계정에 데이터를 저장하는 모든 저장소 서비스를 적용하는 목표는 다음과 같습니다.

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> 다른 저장소 서비스에서 저장소 계정을 활용하면 저장소 계정의 Azure File 공유에 영향을 줍니다. 예를 들어 Azure Blob 저장소를 사용하여 최대 저장소 계정 용량에 도달하면 Azure File 공유가 최대 공유 크기보다 작은 경우에도 Azure File 공유에 새 파일을 만들 수 없습니다.

## <a name="azure-files-scale-targets"></a>Azure Files의 크기 조정 목표
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync의 크기 조정 목표
Azure File Sync를 사용하면 최대한 무제한으로 사용할 수 있도록 디자인하려고 했지만 가능한 것은 아닙니다. 아래 표에서는 테스트의 한계 및 실제적인 목표인 하드 한도를 나타냅니다.

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>참고 항목
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [다른 저장소 서비스에 대한 확장성 및 성능 목표](../common/storage-scalability-targets.md)