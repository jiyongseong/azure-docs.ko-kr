---
title: Azure Site Recovery를 사용하여 주 Azure 지역으로 장애 조치(failover)된 Azure VM 다시 보호 | Microsoft Docs
description: Azure Site Recovery를 사용하여 주 지역에서 장애 조치(failover) 후 보조 지역에서 Azure VM을 다시 보호하는 방법에 대해 설명합니다.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: rajanaki
ms.openlocfilehash: ccec4262297314bad261a852bb5db25c428ce0a0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>주 지역으로 장애 조치(failover)된 Azure VM 다시 보호


>[!NOTE]
>
> Azure VM에 대한 Site Recovery 복제는 현재 미리 보기로 제공됩니다.



[Azure Site Recovery](site-recovery-overview.md)를 사용하여 한 지역에서 다른 지역으로 Azure VM을 [장애 조치](site-recovery-failover.md)(failover)할 경우 VM은 보조 지역에서 보호되지 않는 상태로 부팅됩니다. VM을 주 지역으로 장애 복구(failback)하려면 다음을 수행해야 합니다.

- VM이 주 지역으로 복제를 시작하도록 보조 지역의 VM을 다시 보호합니다. 
- 다시 보호를 완료하고 VM이 복제되면 보조 지역에서 주 지역으로 VM을 장애 조치(failover)할 수 있습니다.

> [!WARNING]
> 주 지역에서 보조 지역으로 컴퓨터를 [마이그레이션](migrate-overview.md#what-do-we-mean-by-migration)하고 다른 리소스 그룹으로 VM을 이동하거나 Azure VM을 삭제한 경우에는 VM을 다시 보호하거나 장애 복구(failback)할 수 없습니다.


## <a name="prerequisites"></a>필수 조건
1. 주 지역에서 보조 지역으로 VM 장애 조치(failover)를 커밋해야 합니다.
2. 주 대상 사이트가 사용 가능하고 해당 지역에서 리소스를 만들거나 액세스할 수 있어야 합니다.

## <a name="reprotect-a-vm"></a>VM 다시 보호

1. **자격 증명 모음** > **복제된 항목**에서 장애 조치(failover)된 VM을 마우스 오른쪽 단추로 클릭하고 **다시 보호**를 선택합니다. 다시 보호 방향이 보조에서 주로 표시됩니다. 

  ![다시 보호](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. 리소스 그룹, 네트워크, 저장소 및 가용성 집합을 검토합니다. 그런 후 **OK**를 클릭합니다. 신규로 표시된 리소스가 있는 경우 이러한 리소스는 다시 보호 프로세스의 일부로 만들어집니다.
3. 다시 보호 작업은 최신 데이터로 대상 사이트를 시드합니다. 완료되면 델타 복제가 수행됩니다. 그런 다음 주 사이트로 다시 장애 조치(failover)할 수 있습니다. 사용자 지정 옵션을 사용하여 다시 보호 중에 사용할 네트워크나 저장소 계정을 선택할 수 있습니다.

  ![옵션 사용자 지정](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>다시 보호 설정 사용자 지정

다시 보호하는 동안 대상 VM의 다음 속성을 사용자 지정할 수 있습니다.

![사용자 지정](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|자산 |메모  |
|---------|---------|
|대상 리소스 그룹     | VM이 만들어진 대상 리소스 그룹을 수정합니다. 다시 보호의 일부로 대상 VM이 삭제됩니다. 장애 조치(failover) 후 VM을 만들 새 리소스 그룹을 선택할 수 있습니다.        |
|대상 가상 네트워크     | 다시 보호 작업 동안 대상 네트워크를 변경할 수 없습니다. 네트워크를 변경하려면 네트워크 매핑을 다시 실행합니다.         |
|대상 저장소(보조 VM이 관리 디스크를 사용하지 않음)     | 장애 조치(failover) 후 VM이 사용하는 저장소 계정을 변경할 수 있습니다.         |
|복제본 관리 디스크(보조 VM이 관리 디스크를 사용함)    | Site Recovery는 보조 VM의 관리 디스크를 미러링하는 주 지역에서 관리 디스크의 복제본을 만듭니다.         | 
|캐시 저장소     | 복제하는 동안 사용할 캐시 저장소 계정을 지정할 수 있습니다. 기본적으로 캐시 저장소 계정이 없는 경우 새 캐시 저장소 계정이 만들어집니다.         |
|가용성 집합     |보조 지역의 VM이 가용성 집합의 일부인 경우 주 지역의 대상 VM에 대한 가용성 집합을 선택할 수 있습니다. 기본적으로 Site Recovery는 주 지역에서 기존 가용성 집합을 찾고 사용합니다. 사용자 지정하는 동안 새 가용성 집합을 지정할 수 있습니다.         |


### <a name="what-happens-during-reprotection"></a>다시 보호하는 동안 어떻게 되나요?

기본적으로 다음이 발생합니다.

1. 주 지역에 캐시 저장소 계정이 만들어집니다.
2. 대상 저장소 계정(주 지역의 원래 저장소 계정)이 없는 경우 새로 만들어집니다. 할당된 저장소 계정 이름은 보조 VM에서 사용하는 저장소 계정의 이름이며 접미사 "asr"이 사용됩니다.
3. VM에서 관리 디스크를 사용하는 경우 복제본 관리 디스크는 보조 VM의 디스크에서 복제된 데이터를 저장하기 위해 주 지역에 생성됩니다. 
4. 대상 가용성 집합이 없으면 다시 보호 작업의 일부로 필요한 경우 새로 만들어집니다. 다시 보호 설정을 사용자 지정한 경우 선택한 집합이 사용됩니다.

다시 보호 작업을 트리거하고 대상 VM이 있는 경우 다음이 발생합니다.

1. 필요한 구성 요소는 다시 보호의 일부로 생성됩니다. 이미 있는 경우 다시 사용됩니다.
2. 대상 쪽 VM은 실행 중인 경우 꺼집니다.
3. 대상 쪽 VM 디스크가 Site Recovery를 통해 컨테이너에 시드 Blob으로 복사됩니다.
4. 그런 다음 대상 쪽 VM이 삭제됩니다.
5. 시드 Blob은 현재 원본 쪽(보조) VM에서 복제하는 데 사용됩니다. 따라서 델타만 복제됩니다.
6. 원본 디스크와 시드 Blob 간의 주요 변경 내용이 동기화됩니다. 이 작업을 완료하는 데 약간의 시간이 걸릴 수 있습니다.
7. 다시 보호 작업이 완료되면 델타 복제가 시작되고 복제 정책에 따라 복구 지점을 만듭니다.
8. 다시 보호 작업이 성공하면 VM은 보호된 상태가 됩니다.

## <a name="next-steps"></a>다음 단계

VM을 보호한 후 장애 조치(failover)를 시작할 수 있습니다. 장애 조치(failover)를 수행하면 보조 지역의 VM을 종료하고 잠시 가동 중지되었다가 주 지역에 VM을 만들어 부팅합니다. 시간을 적절하게 선택하고, 테스트 장애 조치(failover)를 실행하지만 주 사이트에 전체 장애 조치(failover)를 시작하는 것이 좋습니다. 장애 조치(failover)에 대해 [자세히 알아봅니다](site-recovery-failover.md) .

