---
title: Azure 스택의 저장소 용량 관리 | Microsoft Docs
description: 모니터링 하 고 Azure 스택에 대 한 사용 가능한 저장 공간을 관리 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: da6bb00d7538c1a26e1ed4be29d3c882aa378e9e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Azure 스택에 대 한 저장소 용량 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이 문서의 정보 Azure 스택 클라우드 연산자 모니터 하 고 Azure 스택 배포의 저장소 용량을 관리 합니다. Azure 스택 저장소 인프라에 사용할 Azure 스택 배포의 총 저장소 용량의 하위 집합을 할당 **저장소 서비스**합니다. 저장소 서비스 배포의 노드에 해당 하는 볼륨에는 공유에 테 넌 트의 데이터를 저장 합니다.

클라우드 운영자는 제한 된 양의 저장소가 작업할 수 있습니다. 저장소의 크기는 구현 솔루션에 의해 정의 됩니다. 솔루션에는 다중 노드 솔루션을 사용 하는 경우 OEM 공급 업체에서 또는 Azure 스택 개발 키트를 설치 하는 하드웨어에 의해 제공 됩니다.

Azure 스택 저장소 용량을 확장을 지원 하지 않으므로 반드시 [모니터](#monitor-shares) 효율적인 운영을 위해 사용 가능한 저장소 유지 관리 됩니다.  

공유의 남은 사용 가능한 용량 제한 되 면 계획 [공간을 관리](#manage-available-space) 하는 공유 용량 실행 되지 않도록 합니다.

용량을 관리 하는 옵션은 다음과 같습니다.
- 용량을 회수 합니다.
- 컨테이너 마이그레이션

공유 경우 100%를 사용한 저장소 서비스 더 이상 해당 공유에 대 한 함수입니다. 공유에 대 한 작업을 복원를 지 원하는 얻으려면 Microsoft 지원에 문의 합니다.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>볼륨 및 공유, 컨테이너 및 디스크 이해
### <a name="volumes-and-shares"></a>볼륨 및 공유
*저장소 서비스* 구분 되어 있지만 같은 할당 된 볼륨을 테 넌 트 데이터를 저장할 사용 가능한 저장소 분할 합니다. 볼륨의 수가 Azure 스택 배포의 노드 수와 같습니다.

- 4 노드 배포에 4 개의 볼륨을 나와 있습니다. 각 볼륨에 단일 공유 합니다. 다중 노드 배포에 대해 공유의 수가 줄어들지 노드를 제거 하거나 작동 하지 않는 경우.
- Azure 스택 개발자 키트를 사용 하는 경우 단일 공유 단일 볼륨이 있습니다.

저장소 서비스 공유에 대 한 저장소 서비스를 단독으로 사용 되므로 있습니다 해야 직접적 수정, 추가 또는 공유에 파일을 제거 합니다. 저장소 서비스에만 이러한 볼륨에 저장 된 파일에서 작동 해야 합니다.

공유 볼륨에는 테 넌 트 데이터를 보관합니다. 테 넌 트 데이터 페이지 blob에 포함 되어, 블록 blob, 추가 blob, 테이블, 큐, 데이터베이스 및 관련 메타 데이터 저장소. 저장소 개체 (blob, 등)는 단일 공유 내에서 개별적으로 포함 되므로, 각 개체의 최대 크기는 공유 크기를 초과할 수 없습니다. 새 개체의 최대 크기는 새 개체가 만들어질 때 사용 되지 않은 공간으로 공유에 보관할 수 있는 용량에 따라 달라 집니다.

공유가 낮은에 있는 경우 사용 가능한 공간 및 동작을 [회수](#reclaim-capacity) 성공 하거나 사용 가능한 공간 하지 않은, Azure 스택 클라우드 운영자 수 [마이그레이션할](#migrate-a-container-between) 간에 공유 하나에서 blob 컨테이너입니다.

- 컨테이너 및 blob에 대 한 자세한 내용은 참조 [Blob 저장소](azure-stack-key-features.md#blob-storage) 주요 기능 및 Azure 스택에 대 한 개념입니다.
- 스택에서 Azure blob 저장소와 테 넌 트 사용자가 작업 하는 방법에 대 한 정보를 참조 하십시오. [Azure 스택 저장소 서비스](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services)합니다.


### <a name="containers"></a>컨테이너
테 넌 트 사용자가 다음 blob 데이터를 저장 하는 데 사용 되는 컨테이너를 만듭니다. Blob 배치 하는 컨테이너에 사용자가을 반면 저장소 서비스 컨테이너를 넣을 수 있는 볼륨에서 확인 하는 알고리즘을 사용 합니다. 알고리즘에는 일반적으로 가장 사용 가능한 공간으로 볼륨을 선택합니다.  

Blob을 컨테이너에 배치 된 후 더 많은 공간을 사용 하도록 해당 blob 증가할 수 있습니다. 기존 및 새 blob을 추가 하면 blob 증가 함에 따라 해당 컨테이너를 축소를 보관 하는 볼륨에서 사용 가능한 공간입니다.  

컨테이너는 단일 공유로 제한 됩니다. 컨테이너를 입력 하는 사용 가능한 공간을 사용 하 여 80% 이상을 조합 된 blob 데이터 컨테이너에 이르면 *오버플로* 모드입니다. 오버플로 모드에 있을 때 해당 컨테이너에서 만든 모든 새 blob은 충분 한 공간이 있는 다른 볼륨에 할당 됩니다. 시간이 지남에 따라 오버플로 모드에서 컨테이너는 여러 볼륨을 통해 배포 되는 blob를 가질 수 있습니다.

80%와 볼륨에서 사용 가능한 공간의 90%를 사용 하는 시스템 Azure 스택 관리자 포털에서 경고를 발생 시킵니다. 클라우드 운영자는 사용 가능한 저장소 용량을 검토 하 고를 콘텐츠를 다시 분산 계획 해야 합니다. 저장소 서비스는 디스크는 100%를 사용 하 고 없는 추가 경고가 발생 하는 경우 작업을 중지 합니다.

### <a name="disks"></a>디스크
VM 디스크 테 넌 트 별 컨테이너에 추가 되 고 운영 체제 디스크를 포함 합니다. Vm은 데이터 디스크가 하나 이상 있을 수도 있습니다. 두 가지 유형의 디스크 페이지 blob으로 저장 됩니다. 테 넌 트에 지침은 각 디스크는 VM의 성능 향상을 위해 별도 컨테이너에 배치 하는 것입니다.
- 각 컨테이너는 VM의 디스크 (페이지 blob)을 포함 하는 디스크를 소유 하는 VM에 연결 된 컨테이너는 것으로 간주 됩니다.
- VM에서 디스크를 보유 하지 않는 하는 컨테이너에는 무료 컨테이너도 간주 됩니다.

연결 된 컨테이너에 대 한 공간을 확보 하는 옵션 [제한](#move-vm-disks)합니다.
> [!TIP]  
> 클라우드 운영자는 테 넌 트를 컨테이너에 추가할 수 있는 가상 컴퓨터 (Vm)에 연결 된 디스크를 직접 관리 하지 않습니다. 그러나 저장소 공유에는 공간 관리를 계획할 때 컨테이너 및 공유 디스크 어떻게 관련 되는지를 이해 하는 데 사용의 수 있습니다.

## <a name="monitor-shares"></a>공유 모니터
PowerShell 또는 관리 포털을 사용 하 여 사용 가능한 공간이 제한 된 시기를 이해할 수 있도록 공유를 모니터링 합니다. 포털을 사용할 때은 적은 공간에 있는 공유에 대 한 알림을 수신 합니다.    

### <a name="use-powershell"></a>PowerShell 사용
클라우드 운영자는 PowerShell을 사용 하 여 공유의 저장소 용량을 모니터링할 수 있습니다 **Get AzsStorageShare** cmdlet. Get AzsStorageShare cmdlet는 공유의 각 바이트의 총, 할당 된 공간 및 여유 공간을 반환합니다.   
![예: 공유에 대 한 사용 가능한 공간 반환](media/azure-stack-manage-storage-shares/free-space.png)

- **전체 용량** 공유에 사용할 수 있는 바이트의 총 공간입니다. 이 공간 데이터 및 저장소 서비스에 의해 유지 관리 되는 메타 데이터에 사용 됩니다.
- **용량 사용** 테 넌 트 데이터와 연결 된 메타 데이터를 저장 하는 파일의 모든 범위에서 사용 되는 바이트의 데이터의 양입니다.

### <a name="use-the-administrator-portal"></a>관리자 포털을 사용 하 여
클라우드 연산자로 모든 공유의 저장소 용량을 볼 관리자 포털을 사용할 수 있습니다. **저장소로 이동** > **파일 공유** 사용 정보를 볼 수 있는 파일 공유 목록을 엽니다.
![예: 저장소 파일 공유](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **총** 공유에 사용할 수 있는 바이트의 총 공간입니다. 이 공간 데이터 및 저장소 서비스에 의해 유지 관리 되는 메타 데이터에 사용 됩니다.
- **사용 되는** 테 넌 트 데이터와 연결 된 메타 데이터를 저장 하는 파일의 모든 범위에서 사용 되는 바이트의 데이터의 양입니다.

### <a name="storage-space-alerts"></a>저장소 공간 경고
관리 포털을 사용할 때은 적은 공간에 있는 공유에 대 한 알림을 받습니다.

> [!IMPORTANT]
> 클라우드 운영자 공유 충분히 도달 하지 못하도록 보관 해야 합니다. 공유 경우 100%를 사용한 저장소 서비스 더 이상 해당 공유에 대 한 함수입니다. 사용 가능한 공간을 복구 하 고 복원 작업은 100%를 사용 하는 공유에서 하려면 Microsoft 지원에 문의 해야 합니다.

**경고**: 받은 파일 공유를 사용 하는 80% 넘는 경우는 *경고* 관리 포털의 경고: ![예: 경고](media/azure-stack-manage-storage-shares/alert-warning.png)


**중요 한**: 받은 파일 공유 사용 90% 이상 경우는 *중요* 관리 포털의 경고: ![예제: 중요 한 알림](media/azure-stack-manage-storage-shares/alert-critical.png)

**세부 정보를 볼**: 관리 포털에서 완화 옵션을 확인 하려는 경고에 대 한 세부 정보를 열 수 있습니다: ![예: 경고 세부 정보 보기](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>사용 가능한 공간을 관리 합니다.
여유 공간을 공유 해야 하는 경우 먼저 이상 침투 적 메서드를 사용 합니다. 예를 들어 컨테이너에 마이그레이션할 선택 하기 전에 공간을 확보 하려고 합니다.  

### <a name="reclaim-capacity"></a>용량을 회수 합니다.
*이 옵션 다중 노드 배포와 Azure 스택 개발 키트 둘 다에 적용 됩니다.*

삭제 된 테 넌 트 계정에 의해 사용 되는 용량을 회수할 수 있습니다. 이 용량은 자동으로 회수 하는 경우 데이터 [보존 기간](azure-stack-manage-storage-accounts.md#set-the-retention-period) 에 도달 하거나 즉시 회수 하려면 조치를 취할 수 있습니다.

자세한 내용은 참조 [용량을 회수](azure-stack-manage-storage-accounts.md#reclaim) 에서 저장소 리소스를 관리 합니다.

### <a name="migrate-a-container-between-volumes"></a>컨테이너 볼륨 간 마이그레이션
*이 옵션은 다중 노드 배포에만 적용 됩니다.*

테 넌 트 사용 패턴으로 인해 일부 테 넌 트 공유는 다른 항목 보다 더 많은 공간을 사용합니다. 상대적으로 사용 하지 않은 다른 공유 앞에 공백을에서 느리게 실행 되는 공유 될 수 있습니다.

다른 공유를 일부 blob 컨테이너를 수동으로 마이그레이션하여 과도 공유에 대 한 공간을 확보 하도록 시도할 수 있습니다. 여러 개의 더 작은 컨테이너의 용량이 모두 저장할 수 있는 단일 공유를 마이그레이션할 수 있습니다. 마이그레이션을 사용 하 여 이동 하려면 *무료* 컨테이너입니다. 무료 컨테이너는 VM에 대 한 디스크를 포함 하지 않는 컨테이너입니다.   

마이그레이션 새 공유에 대해 모든 컨테이너 blob을 통합합니다.

- 컨테이너 모드가 시작 되었으면 오버플로 하이 blob 추가 볼륨에 배치 하는 경우 새 공유 마이그레이션할 컨테이너에 대 한 blob의 모든를 충분 한 용량이 있어야 합니다. 추가 공유에 있는 blob가 포함 됩니다.

- PowerShell cmdlet *Get AzsStorageContainer* 만 컨테이너에 대 한 초기 볼륨에서 사용 중인 공간을 식별 합니다. Cmdlet에서 추가 볼륨에 저장 된 blob에서 사용 되는 공간을 식별 하지 않습니다. 따라서 컨테이너의 전체 크기에서 분명 하 게 확인할 수 있습니다. 새 공유에 있는 컨테이너의 통합을 보낼 수 새 공유 하는 오버플로 조건을에 추가 공유에 데이터를 놓이는 위치 같습니다. 따라서를 다시 공유를 다시 분산 할 수 있습니다.

- 리소스 그룹에 권한이 부족 하 고 PowerShell을 사용 하 여 오버플로 데이터에 대 한 추가 볼륨을 쿼리할 수 없습니다 해당 리소스 그룹 및 컨테이너의 데이터의 전체 크기를 이해 하려면 해당 데이터를 마이그레이션하기 전에 마이그레이션 소유자와 협력 합니다.  

> [!IMPORTANT]
> Blob 컨테이너에 대 한 마이그레이션은 PowerShell 사용 해야 하는 오프 라인 작업. 마이그레이션 완료 될 때까지 마이그레이션하는 컨테이너에 대 한 모든 blob는 오프 라인으로 유지 하 고 사용할 수 없습니다. 모든 진행 중인 마이그레이션 완료 될 때까지 Azure 스택 업그레이드 피해 야 합니다.

#### <a name="to-migrate-containers-using-powershell"></a>PowerShell을 사용 하 여 컨테이너를 마이그레이션하려면
1. 있는지 확인 [Azure PowerShell 설치 및 구성](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)합니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](http://go.microsoft.com/fwlink/?LinkId=394767)을 참조하세요.
2.  마이그레이션하려는 공유에 데이터를 이해 하는 컨테이너를 검사 합니다. 볼륨에서 마이그레이션에 대 한 최상의 후보 컨테이너를 식별 하려면 사용 하 여는 **Get AzsStorageContainer** cmdlet:

    ````PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    $shares = Get-AzsStorageShare -FarmName $farm_name
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
    ````
    그런 다음 $containers 없는지 확인 합니다.

    ````PowerShell
    $containers
    ````

    ![예: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  마이그레이션하는 컨테이너를 보유할 최상의 대상 공유를 식별 합니다.

    ````PowerShell
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ````

    그런 다음 $destinationshares 없는지 확인 합니다.

    ' ' $Destinationshares PowerShell
    ````

    ![Example: $destination shares](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Start migration for a container. Migration is asynchronous. If you start migration of additional containers before the first migration completes, use the job id to track the status of each.

  ````PowerShell
  $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
  ````

  $JobId 검사. 다음 예제에서는 대체 *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* 작업 id를 확인 하려면:

  ````PowerShell
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ````

5. 작업 id를 사용 하 여 마이그레이션 작업의 상태를 확인 합니다. 컨테이너 마이그레이션이 완료 되었을 때 **MigrationStatus** 로 설정 된 **완료**합니다.

  ````PowerShell 
  Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
  ````

  ![예: 마이그레이션 상태](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  진행 중인 마이그레이션 작업을 취소할 수 있습니다. 마이그레이션 작업이 비동기적으로 처리를 취소 합니다. $Jobid를 사용 하 여 취소를 추적할 수 있습니다.

  ````PowerShell
  Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
  ````

  ![예: 롤백 상태](media/azure-stack-manage-storage-shares/rollback.png)

7. 명령을 실행할 수 있습니다는 6 단계에서 다시 마이그레이션 작업은 상태 확인 될 때까지 **Canceled**:  

    ![예: 취소 됨된 상태](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>VM 디스크를 이동 합니다.
*이 옵션은 다중 노드 배포에만 적용 됩니다.*

공간을 관리 하는 가장 극단적인 방법은 가상 컴퓨터 디스크를 이동 하 여 사용 하는 것입니다. (VM 디스크를 포함 하나)는 연결 된 컨테이너를 이동 하는 것은 복잡 한 이므로이 동작을 수행 하려면 Microsoft 지원에 문의 합니다.

## <a name="next-steps"></a>다음 단계
에 대 한 자세한 내용은 [가상 컴퓨터 사용자에 게 제공](azure-stack-tutorial-tenant-vm.md)합니다.
