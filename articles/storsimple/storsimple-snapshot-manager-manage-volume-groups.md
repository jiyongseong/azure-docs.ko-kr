---
title: StorSimple Snapshot Manager 볼륨 그룹 | Microsoft Docs
description: StorSimple 스냅숏 관리자 MMC 스냅인을 사용하여 볼륨 그룹을 만들고 관리하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>StorSimple 스냅숏 관리자를 사용하여 볼륨 그룹 만들기 및 관리
## <a name="overview"></a>개요
**범위** 창에서 **볼륨 그룹** 노드를 사용하면 볼륨 그룹에 볼륨을 할당하고 볼륨 그룹에 대한 정보를 확인하고 백업을 예약하고 볼륨 그룹을 편집할 수 있습니다.

볼륨 그룹은 백업이 응용 프로그램에 일관됨을 보장하는 데 사용되는 관련 볼륨들의 풀입니다. 자세한 내용은 [볼륨 및 볼륨 그룹](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups)과 [Windows 볼륨 섀도 복사본 서비스와의 통합](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)을 참조하세요.

> [!IMPORTANT]
> * 볼륨 그룹의 모든 볼륨은 단일 클라우드 서비스 공급자에서 가져와야 합니다.
> * 볼륨 그룹을 구성할 때는 클러스터 공유 볼륨(CSV)과 비 CSV를 동일한 볼륨 그룹에 혼합하지 않도록 합니다. StorSimple 스냅숏 관리자는 동일한 스냅숏에 혼합되어 있는 CSV와 비 CSV를 지원하지 않습니다.

![볼륨 그룹 노드](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**그림 1: StorSimple 스냅숏 관리자 볼륨 그룹 노드** 

이 자습서에서는 StorSimple 스냅숏 관리자를 사용하여 다음 작업을 수행하는 방법을 설명합니다.

* 볼륨 그룹에 대한 정보 보기
* 볼륨 그룹 만들기
* 볼륨 그룹 백업
* 볼륨 그룹 편집
* 볼륨 그룹 삭제

이러한 모든 작업을 **작업** 창에서도 사용할 수 있습니다.

## <a name="view-volume-groups"></a>볼륨 그룹 보기
**볼륨 그룹** 노드를 클릭하면 선택한 열에 따라 **결과** 창에 각 볼륨 그룹에 대해 다음 정보가 표시됩니다. **결과** 창의 열은 구성할 수 있습니다. **볼륨** 노드를 마우스 오른쪽 단추로 클릭하고 **보기**를 선택한 다음 **열 추가/제거**를 선택합니다.

| 결과 열 | 설명 |
|:--- |:--- |
| Name |**이름** 열에는 볼륨 그룹의 이름이 포함됩니다. |
| 응용 프로그램 |**응용 프로그램** 열은 Windows 호스트에 현재 설치되어 실행 중인 VSS 기록기의 수를 표시합니다. |
| 선택 |**선택** 열은 볼륨 그룹에 포함된 볼륨의 수를 표시합니다. 0이면 볼륨 그룹의 볼륨에 연결된 응용 프로그램이 없음을 나타냅니다. |
| 가져옴 |**가져옴** 열은 가져온 볼륨의 수를 표시합니다. 이 열이 **True**로 설정되면 볼륨 그룹을 Azure Portal에서 가져왔으며 StorSimple 스냅숏 관리자에서 만들지 않았음을 나타냅니다. |

> [!NOTE]
> StorSimple 스냅숏 관리자 볼륨 그룹은 Azure Portal의 **Backup 정책** 탭에도 표시됩니다.
> 
> 

## <a name="create-a-volume-group"></a>볼륨 그룹 만들기
다음 절차에 따라 볼륨 그룹을 만들 수 있습니다.

#### <a name="to-create-a-volume-group"></a>볼륨 그룹을 만들려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다.
2. **범위** 창에서 **볼륨 그룹**을 마우스 오른쪽 단추로 클릭한 다음 **볼륨 그룹 만들기**를 클릭합니다.
   
    ![볼륨 그룹 만들기](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    **볼륨 그룹 만들기** 대화 상자가 나타납니다.
   
    ![볼륨 그룹 만들기 대화 상자](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. 다음 정보를 입력합니다.
   
   1. **이름** 상자에 새 볼륨 그룹에 대해 고유한 이름을 입력합니다.
   2. **응용 프로그램** 상자에서 볼륨 그룹에 추가할 볼륨과 연결된 응용 프로그램을 선택합니다.
      
       **응용 프로그램** 상자에는 StorSimple 볼륨을 사용하고 VSS 기록기를 사용하도록 설정된 응용 프로그램만 나열됩니다. VSS 기록기는 기록기가 인식하는 모든 볼륨이 StorSimple 볼륨인 경우에만 활성화됩니다. 응용 프로그램 상자가 비어 있으면 Azure StorSimple 볼륨을 사용하고 VSS 기록기를 지원하는 응용 프로그램이 설치되어 있지 않습니다. (현재 Azure StorSimple은 Microsoft Exchange 및 SQL Server를 지원합니다.) VSS 기록기에 대한 자세한 내용은 [Windows 볼륨 섀도 복사본 서비스와의 통합](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)을 참조하세요.
      
       응용 프로그램을 선택하면 연결된 모든 볼륨이 자동으로 선택됩니다. 반대로, 특정 응용 프로그램과 연결된 볼륨을 선택하면 **응용 프로그램** 상자에서 해당 응용 프로그램이 자동으로 선택됩니다. 
   3. **볼륨** 상자에서 볼륨 그룹에 추가할 StorSimple 볼륨을 선택합니다. 
      
      * 단일 또는 다중 파티션이 있는 볼륨을 포함할 수 있습니다. (다중 파티션 볼륨은 다중 파티션이 있는 기본 디스크 또는 동적 디스크일 수 있습니다.) 다중 파티션이 포함된 볼륨은 단일 단위로 취급됩니다. 따라서 파티션 중 하나만 볼륨 그룹에 추가해도 다른 모든 파티션이 해당 볼륨 그룹에 동시에 자동으로 추가됩니다. 볼륨 그룹에 다중 파티션 볼륨을 추가한 후에도 해당 다중 파티션 볼륨은 계속 단일 단위로 취급됩니다.
      * 볼륨을 할당하지 않으면 빈 볼륨 그룹을 만들 수 있습니다. 
      * 클러스터 공유 볼륨(CSV)과 비 CSV를 동일한 볼륨 그룹에 혼합하지 않도록 합니다. StorSimple 스냅숏 관리자는 동일한 스냅숏에 혼합되어 있는 CSV 볼륨과 비 CSV 볼륨을 지원하지 않습니다.
4. **확인** 을 클릭하여 볼륨 그룹을 저장합니다.

## <a name="back-up-a-volume-group"></a>볼륨 그룹 백업
다음 절차에 따라 볼륨 그룹을 백업할 수 있습니다.

#### <a name="to-back-up-a-volume-group"></a>볼륨 그룹을 백업하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 **볼륨 그룹** 노드를 확장하고 볼륨 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **Backup 수행**을 클릭합니다.
   
    ![볼륨 그룹 즉시 백업](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. **Backup 수행** 대화 상자에서 **로컬 스냅숏** 또는 **클라우드 스냅숏**를 선택한 다음 **만들기**를 클릭합니다.
   
    ![백업 수행 대화 상자](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. 백업이 실행되고 있는지 확인하려면 **작업** 노드를 확장한 다음 **실행 중**을 클릭합니다. 백업이 나열됩니다.
5. 완료된 스냅숏을 보려면 **Backup 카탈로그** 노드를 확장하고 볼륨 그룹 이름을 확장한 다음 **로컬 스냅숏** 또는 **클라우드 스냅숏**을 클릭합니다. 성공적으로 완료되면 백업이 나열됩니다.

## <a name="edit-a-volume-group"></a>볼륨 그룹 편집
다음 절차에 따라 볼륨 그룹을 편집할 수 있습니다.

#### <a name="to-edit-a-volume-group"></a>볼륨 그룹을 편집하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다.
2. **범위** 창에서 **볼륨 그룹** 노드를 확장하고 볼륨 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **편집**을 클릭합니다.
3. **볼륨 그룹 만들기** 대화 상자가 나타납니다. **이름**, **응용 프로그램** 및 **볼륨** 항목을 변경할 수 있습니다.
4. **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="delete-a-volume-group"></a>볼륨 그룹 삭제
다음 절차에 따라 볼륨 그룹을 삭제할 수 있습니다. 

> [!WARNING]
> 이 작업을 수행하면 볼륨 그룹에 연결된 모든 백업이 함께 삭제됩니다.
> 
> 

#### <a name="to-delete-a-volume-group"></a>볼륨 그룹을 삭제하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다.
2. **범위** 창에서 **볼륨 그룹** 노드를 확장하고 볼륨 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **삭제**를 클릭합니다.
3. **볼륨 그룹 삭제** 대화 상자가 나타납니다. 텍스트 상자에 **Confirm**을 입력한 다음 **확인**을 클릭합니다.
   
    삭제한 볼륨 그룹이 **결과** 창의 목록에서 사라지고 해당 볼륨 그룹에 연결된 모든 백업이 백업 카탈로그에서 삭제됩니다.

## <a name="next-steps"></a>다음 단계
* [StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 알아봅니다.
* [StorSimple 스냅숏 관리자를 사용하여 백업 정책을 만들고 관리](storsimple-snapshot-manager-manage-backup-policies.md)하는 방법을 알아봅니다.

