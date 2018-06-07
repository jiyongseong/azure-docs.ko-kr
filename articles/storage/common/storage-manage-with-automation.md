---
title: "Azure 자동화를 사용하여 Azure 저장소 관리"
description: "Azure 자동화 서비스를 사용하여 대규모 Azure 저장소를 관리하는 방법을 알아봅니다."
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-storage-using-azure-automation"></a>Azure 자동화를 사용하여 Azure 저장소 관리
이 가이드에서는 Azure 자동화 서비스 및 이 서비스를 사용하여 Azure 저장소 blob, 테이블 및 큐 관리를 간소화하는 방법을 소개합니다.

## <a name="what-is-azure-automation"></a>Azure 자동화 정의
[Azure 자동화](https://azure.microsoft.com/services/automation/) 는 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure 자동화를 사용하여 장기 실행 작업, 수동 작업, 오류가 발생하기 쉬운 작업 및 빈번하게 반복되는 작업을 자동화하여 조직의 안정성 및 효율성을 높이고 가치 창출 시간을 단축할 수 있습니다.

Azure 자동화는 조직이 성장함에 따라 요구를 충족하기 위해 크기가 조정되는 안정성과 가용성 높은 워크플로 실행 엔진을 제공합니다. Azure 자동화에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure 자동화에 의해 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT/DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Azure 자동화를 통해 Azure 저장소 관리 향상
[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)에서 사용할 수 있는 PowerShell cmdlet을 통해 Azure 자동화에서 Azure 저장소를 관리할 수 있습니다. Azure 자동화에서는 이러한 저장소 PowerShell cmdlet을 기본적으로 사용할 수 있으므로 서비스 내에서 blob, 테이블 및 큐 관리 작업을 모두 수행할 수 있습니다. Azure 자동화에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

[Azure 자동화 runbook 갤러리](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) 는 다양한 Azure 저장소, 다른 Azure 서비스 및 타사 시스템의 관리 자동화를 시작하도록 제품 군 및 커뮤니티 runbook을 포함합니다. Runbook 갤러리에는 다음이 포함됩니다.

* [자동화 서비스를 사용하여 특정 일 수가 지난 Azure 저장소 Blob 제거](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Azure 저장소에서 Blob 다운로드](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [단일 Azure VM 또는 클라우드 서비스의 모든 VM에 대한 모든 디스크 백업](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>다음 단계
Azure 자동화의 기본 사항과 Azure 자동화를 사용하여 Azure 저장소 blob, 테이블 및 큐를 관리하는 방법을 알아보았으므로 이제 다음 링크에 따라 Azure 자동화에 대해 자세히 알아보세요.

Azure 자동화 자습서 [Azure 자동화에서 Runbook 만들기 또는 가져오기](../../automation/automation-creating-importing-runbook.md)를 참조하세요.

