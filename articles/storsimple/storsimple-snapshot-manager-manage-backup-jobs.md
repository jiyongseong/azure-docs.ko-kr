---
title: "StorSimple Snapshot Manager 백업 작업 | Microsoft Docs"
description: "StorSimple 스냅숏 관리자 MMC 스냅인을 사용하여 예약된 백업 작업, 현재 실행 중인 백업 작업 및 완료된 백업 작업을 보고 관리하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>StorSimple 스냅숏 관리자를 사용하여 백업 작업 보기 및 관리

## <a name="overview"></a>개요
**범위** 창의 **작업** 노드에는 대화형으로 또는 구성된 정책에 따라 시작한 **예약됨**, **최근 24시간** 및 **실행 중** 백업 태스크가 표시됩니다. 

이 자습서에서는 **작업** 노드를 사용하여 예약된 백업 작업, 최근 백업 작업 및 현재 실행 중인 백업 작업에 대한 정보를 표시하는 방법을 설명합니다. (작업 및 해당 정보 목록이 **결과** 창에 나타납니다.) 또한 나열된 작업을 마우스 오른쪽 단추로 클릭하면 사용 가능한 작업이 나열된 상황에 맞는 메뉴를 표시할 수 있습니다.

## <a name="view-scheduled-jobs"></a>예약된 작업 보기
다음 절차를 사용하여 예약된 백업 작업을 볼 수 있습니다.

#### <a name="to-view-scheduled-jobs"></a>예약된 작업을 보려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다. 
2. **범위** 창에서 **작업** 노드를 확장한 다음 **예약됨**을 클릭합니다. **결과** 창에 다음 정보가 표시됩니다.
   
   * **이름** – 예약된 스냅숏의 이름
   * **다음 실행** – 다음으로 예약된 스냅숏의 날짜 및 시간
   * **마지막 실행** – 가장 최근으로 예약된 스냅숏의 날짜 및 시간
     
     > [!NOTE]
     > 일회성 스냅숏은 **다음 실행** 및 **마지막 실행**이 동일합니다.
     
     ![예약된 백업 작업](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. 특정 작업에 대해 추가 작업을 수행하려면 **결과** 창에서 해당 작업 이름을 마우스 오른쪽 단추로 클릭하고 메뉴 옵션 중에서 선택합니다.

## <a name="view-recent-jobs"></a>최근 작업 보기
다음 절차를 사용하여 최근 24시간 동안 완료된 백업 및 복원 작업을 볼 수 있습니다.

#### <a name="to-view-recent-jobs"></a>최근 작업을 보려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 **작업** 노드를 확장한 다음 **최근 24시간**을 클릭합니다. **결과** 창에 최근 24시간 동안의 백업 작업이 표시됩니다(최대 64개 작업까지). 지정한 **보기** 옵션에 따라 **결과** 창에 다음 정보가 나타납니다.
   
   * **이름** – 예약된 스냅숏의 이름
   * **시작** – 스냅숏이 시작된 날짜 및 시간
   * **중지** – 스냅숏이 완료되거나 종료된 날짜 및 시간
   * **경과** – **시작** 및 **중지** 시간 사이의 시간
   * **상태** - 최근에 완료된 작업의 상태 **성공**은 백업이 성공적으로 만들어졌음을 나타냅니다. **실패**는 작업이 성공적으로 실행되지 않았음을 나타냅니다.
   * **정보** – 실패한 이유
   * **처리된 바이트(MB)** – 처리된 볼륨 그룹의 데이터 양(MB) 
     
     ![최근 24시간 동안 실행된 작업](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. 특정 작업에 대해 추가 작업을 수행하려면 **결과** 창에서 해당 작업 이름을 마우스 오른쪽 단추로 클릭하고 메뉴 옵션 중에서 선택합니다.
   
    ![작업 삭제](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>현재 실행 중인 작업 보기
다음 절차를 사용하여 현재 실행 중인 작업을 볼 수 있습니다.

#### <a name="to-view-currently-running-jobs"></a>현재 실행 중인 작업을 보려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 **작업** 노드를 확장한 다음 **실행 중**을 클릭합니다. 지정한 **보기** 옵션에 따라 **결과** 창에 다음 정보가 나타납니다.
   
   * **이름** – 예약된 스냅숏의 이름
   * **시작** – 스냅숏이 시작된 날짜 및 시간
   * **검사점** - 백업의 현재 작업
   * **상태** – 완료율
   * **경과** - 백업이 시작된 이후 경과된 시간 
   * **평균 처리량(MB)** – 처리하는 데 걸린 총 시간에 대해 처리된 데이터 총 바이트의 비율(MB)
   * **처리된 바이트(MB)** - 처리된 데이터의 총 바이트(MB)
   * **작성된 바이트(MB)** - 작성된 데이터의 총 바이트(MB) 메타데이터 뿐만 아니라 데이터를 포함하고 따라서 일반적으로 처리된 바이트보다 큽니다.
     
     ![현재 실행 중인 작업](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. 특정 작업에 대해 추가 작업을 수행하려면 **결과** 창에서 해당 작업 이름을 마우스 오른쪽 단추로 클릭하고 메뉴 옵션 중에서 선택합니다.

## <a name="next-steps"></a>다음 단계
* [StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 알아봅니다.
* [StorSimple 스냅숏 관리자를 사용하여 백업 카탈로그를 관리](storsimple-snapshot-manager-manage-backup-catalog.md)하는 방법을 알아봅니다.

