---
title: "Microsoft Azure StorSimple 가상 배열 백업 자습서 | Microsoft Docs"
description: "StorSimple 가상 배열 공유 및 볼륨을 백업하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c926f0c80ce56cac3106ad97ec3ec2e18a8e2cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>StorSimple 가상 배열에서 공유 또는 볼륨 백업

## <a name="overview"></a>개요

StorSimple 가상 배열은 파일 서버 또는 iSCSI 서버로 구성할 수 있는 하이브리드 클라우드 저장소 온-프레미스 가상 장치입니다. 가상 배열을 사용하면 사용자가 장치에서 모든 공유 또는 볼륨의 예약된 백업과 수동 백업을 만들 수 있습니다. 파일 서버로 구성하면 항목 수준 복구도 가능합니다. 이 자습서에서는 예약된 백업과 수동 백업을 만들고 가상 배열에서 삭제된 파일을 복원하는 항목 수준 복구를 수행하는 방법을 설명합니다.

이 자습서는 StorSimple 가상 배열만에 적용됩니다. 8000 시리즈에 대한 내용은 [8000 시리즈 장치에 대한 백업 만들기](storsimple-manage-backup-policies-u2.md)로 이동합니다.

## <a name="back-up-shares-and-volumes"></a>공유 및 볼륨 백업

백업은 지정 시간 보호 기능을 제공하고, 복구 기능을 개선하며, 공유 및 볼륨의 복원 시간을 최소화합니다. StorSimple 장치에서 공유 또는 볼륨을 두 가지 방법(**예약** 또는 **수동**)으로 백업할 수 있습니다. 각 메서드는 다음 섹션에서 설명합니다.

## <a name="change-the-backup-start-time"></a>백업 시작 시간 변경

> [!NOTE]
> 이 릴리스에서 예약된 백업은 매일 지정된 시간에 실행되고 장치의 모든 공유 또는 볼륨을 백업하는 기본 정책에 따라 생성됩니다. 지금은 예약된 백업에 대한 사용자 지정 정책을 만드는 것이 불가능합니다.


StorSimple 가상 배열에는 하루 중 지정된 시간(22:30)에 시작하여 장치의 모든 공유 또는 볼륨을 하루에 한 번 백업하는 기본 백업 정책이 있습니다. 백업이 시작되는 시간은 변경할 수 있지만, 빈도 및 보존(보존할 백업의 수를 지정하는)은 변경할 수 없습니다. 이러한 백업 중에 전체 가상 장치가 백업됩니다. 그러면 잠재적으로 장치의 성능에 영향을 주고 장치에 배포된 워크로드에 영향을 줄 수 있습니다. 따라서 사용량이 적은 시간에 이러한 백업을 예약하는 것이 좋습니다.

 기본 백업 시작 시간을 변경하려면 [Azure Portal](https://portal.azure.com/)에서 다음 단계를 수행합니다.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>기본 백업 정책의 시작 시간을 변경하려면

1. **장치**로 이동합니다. StorSimple 장치 관리자 서비스에 등록된 장치 목록이 표시됩니다. 
   
    ![장치로 이동](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. 장치를 선택하고 클릭합니다. **설정** 블레이드가 표시됩니다. **관리 > 백업 정책**으로 이동합니다.
   
    ![장치 선택](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. **백업 정책** 블레이드에서 기본 시작 시간은 22시 30분입니다. 장치 표준 시간대의 일별 일정에 새로운 시작 시간을 지정할 수 있습니다.
   
    ![백업 정책으로 이동](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. **Save**를 클릭합니다.

### <a name="take-a-manual-backup"></a>수동 백업 수행

예약된 백업뿐만 아니라 장치 데이터의 수동(주문형) 백업도 언제든 가능합니다.

#### <a name="to-create-a-manual-backup"></a>수동 백업을 만들려면

1. **장치**로 이동합니다. 장치를 선택하고 선택한 행의 오른쪽 끝에 있는 **...**를 마우스 오른쪽 단추로 클릭합니다. 상황에 맞는 메뉴에서 **백업 수행**을 선택합니다.
   
    ![백업 수행으로 이동](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. **백업 수행** 블레이드에서 **백업 수행**을 클릭합니다. 그러면 파일 서버의 모든 공유 또는 iSCSI 서버의 모든 볼륨을 백업합니다. 
   
    ![백업 시작](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    요청 시 백업이 시작되면 백업 작업이 시작되었다고 표시됩니다.
   
    ![백업 시작](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    작업이 성공적으로 완료되면 다시 알림을 받습니다. 그러면 백업 프로세스를 시작합니다.
   
    ![백업 작업 생성됨](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. 백업 진행 상황을 추적하고 작업 세부 정보를 확인하려면 알림을 클릭합니다. 그러면 **작업 세부 정보**로 이동합니다.
   
     ![백업 작업 세부 정보](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. 백업이 완료되면 **관리 > 백업 카탈로그**로 이동합니다. 장치에서 모든 공유 또는 볼륨의 클라우드 스냅숏을 확인합니다.
   
    ![백업 완료됨](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>기존 백업 확인
기존 백업을 보려면 Azure Portal에서 다음 단계를 수행합니다.

#### <a name="to-view-existing-backups"></a>기존 백업을 보려면

1. **장치** 블레이드로 이동합니다. 장치를 선택하고 클릭합니다. **설정** 블레이드에서 **관리 > 백업 카탈로그**로 이동합니다.
   
    ![백업 카탈로그로 이동](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. 필터링에 사용할 다음 기준을 지정합니다.
   
    - **시간 범위** – **지난 1시간**, **지난 24시간**, **지난 7일**, **지난 30일**, **지난 해** 및 **사용자 지정 날짜**일 수 있습니다.
    
    - **장치** – StorSimple 장치 관리자 서비스에 등록된 파일 서버 또는 iSCSI 서버 목록에서 선택합니다.
   
    - **시작** – 백업 정책에 의해 자동으로 **예약**되거나 사용자에 의해 **수동으로** 시작될 수 있습니다.
   
    ![필터 백업](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. **Apply**를 클릭합니다. 필터링된 백업 목록은 **백업 카탈로그** 블레이드에 표시됩니다. 100개의 백업 요소만이 지정된 시간에 표시될 수 있습니다.
   
    ![업데이트된 백업 카탈로그](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>다음 단계

[StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)에 대해 자세히 알아봅니다.

