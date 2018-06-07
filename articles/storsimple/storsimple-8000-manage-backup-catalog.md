---
title: "StorSimple 백업 카탈로그 관리 | Microsoft Docs"
description: "StorSimple 장치 관리자 서비스 백업 카탈로그 페이지를 사용하여 백업 세트를 나열, 선택 및 삭제하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>StorSimple 장치 관리자 서비스를 사용하여 백업 카탈로그 관리
## <a name="overview"></a>개요
StorSimple 장치 관리자 서비스 **백업 카탈로그** 블레이드는 수동 또는 예약된 백업을 수행할 때 생성되는 모든 백업 세트를 표시합니다. 이 페이지를 사용하여 백업 정책 또는 볼륨에 대한 모든 백업을 나열하거나, 백업을 선택 또는 삭제하거나 백업을 사용하여 볼륨을 복원 또는 복제할 수 있습니다.

이 자습서에서는 백업 세트를 표시하고 선택하고 삭제하는 방법을 설명합니다. 백업에서 장치를 복원하는 방법을 알아보려면 [백업 세트에서 장치 복원](storsimple-8000-restore-from-backup-set-u2.md)으로 이동합니다. 볼륨 복제 방법을 알아보려면 [StorSimple 볼륨 복제](storsimple-8000-clone-volume-u2.md)로 이동합니다.

![백업 카탈로그](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

**백업 카탈로그** 블레이드는 백업 세트 선택 범위를 좁힐 수 있는 쿼리를 제공합니다. 다음 매개 변수를 기반으로 검색되는 백업 세트를 필터링 할 수 있습니다.

* **장치** – 백업 세트를 만든 장치입니다.
* **백업 정책 또는 볼륨** – 백업 정책 또는 이 백업 세트와 연결된 볼륨입니다.
* **시작 및 종료** – 백업 세트를 만든 날짜 및 시간 범위입니다.

그런 다음 필터링된 백업 세트는 다음 특성을 기반으로 표로 정리됩니다.

* **이름** – 백업 정책 또는 이 백업 세트와 연결된 볼륨입니다.
* **크기** – 백업 세트의 실제 크기입니다.
* **만든 날짜** – 백업이 만들어진 날짜 및 시간입니다. 
* **유형** – 백업 세트는 로컬 스냅숏 또는 클라우드 스냅숏일 수입니다. 로컬 스냅숏은 장치에 로컬로 저장된 모든 볼륨 데이터의 백업인 반면 클라우드 스냅숏은 클라우드에 상주하는 볼륨 데이터의 백업을 참조합니다. 로컬 스냅숏은 데이터 복구 기능으로 클라우드 스냅숏을 선택하는 반면 빠른 액세스를 제공합니다.
* **시작 기준** – 일정에 따라 자동으로 또는 사용자가 수동으로 백업을 시작할 수 있습니다. 백업을 예약하기 위해 백업 정책을 사용할 수 있습니다. 또는 **백업 수행** 옵션을 사용하여 수동 백업을 수행할 수 있습니다.

## <a name="list-backup-sets-for-a-backup-policy"></a>백업 정책에 대한 백업 세트 나열
백업 정책에 대한 모든 백업을 나열하려면 다음 단계를 완료합니다.

#### <a name="to-list-backup-sets"></a>백업 세트를 나열하려면
1. StorSimple 장치 관리자 서비스로 이동한 다음 **백업 카탈로그**를 클릭합니다.

2. 다음과 같이 선택 항목을 필터링합니다.
   
   1. 시간 범위를 지정합니다.
   2. 해당 장치를 선택합니다.
   3. **백업 정책**을 기준으로 필터링하여 해당 백업을 표시합니다.
   3. 백업 정책 드롭다운 목록에서 **모두**를 선택하여 선택한 장치의 모든 백업을 볼 수 있습니다.
   4. **적용**을 클릭하여 이 쿼리를 실행합니다.
      
      선택한 백업 정책과 연결된 백업이 백업 세트의 목록에 나타나야 합니다.

      ![백업 카탈로그로 이동](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>백업 세트를 선택합니다.
볼륨 또는 백업 정책에 백업 세트를 선택 하려면 다음 단계를 완료 합니다.

#### <a name="to-select-a-backup-set"></a>백업 세트를 선택하려면
1. StorSimple 장치 관리자 서비스로 이동한 다음 **백업 카탈로그**를 클릭합니다.
2. 다음과 같이 선택 항목을 필터링합니다.
   
   1. 시간 범위를 지정합니다. 
   2. 해당 장치를 선택합니다. 
   3. 선택하려는 백업에 대한 볼륨 또는 백업 정책을 기준으로 필터링합니다.
   4. **적용**을 클릭하여 이 쿼리를 실행합니다.
      
      선택한 볼륨와 연결된 백업을 또는 백업 정책이 백업 세트의 목록에 나타나야 합니다.

      ![백업 카탈로그로 이동](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. 백업 세트를 선택하고 확장합니다. 이제 포함된 볼륨별로 구분된 백업 세트를 볼 수 있습니다. **복원** 및 **삭제** 옵션은 백업 세트에 대한 상황에 맞는 메뉴(오른쪽 클릭)를 통해 사용할 수 있습니다. 선택한 백업 세트에 이러한 동작 중 하나를 수행할 수 있습니다.

    ![백업 카탈로그로 이동](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>백업 세트 삭제
백업에 연결된 데이터를 더이상 보존하고 싶지 않은 경우 백업을 삭제 합니다. 백업 세트를 삭제하려면 다음 단계를 수행합니다.

#### <a name="to-delete-a-backup-set"></a>백업 세트를 삭제 하려면
 StorSimple 장치 관리자 서비스로 이동한 다음 **백업 카탈로그**를 클릭합니다.
2. 다음과 같이 선택 항목을 필터링합니다.
   
   1. 시간 범위를 지정합니다. 
   2. 해당 장치를 선택합니다. 
   3. 선택하려는 백업에 대한 볼륨 또는 백업 정책을 기준으로 필터링합니다.
   4. **적용**을 클릭하여 이 쿼리를 실행합니다.
      
      선택한 볼륨와 연결된 백업을 또는 백업 정책이 백업 세트의 목록에 나타나야 합니다.

      ![백업 카탈로그로 이동](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. 백업 세트를 선택하고 확장합니다. 이제 포함된 볼륨별로 구분된 백업 세트를 볼 수 있습니다. **복원** 및 **삭제** 옵션은 백업 세트에 대한 상황에 맞는 메뉴(오른쪽 클릭)를 통해 사용할 수 있습니다. 선택한 백업 세트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **삭제**를 선택합니다.

    ![백업 카탈로그로 이동](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. 확인을 위한 메시지가 나타나면 표시된 정보를 검토하고 **삭제**를 클릭합니다. 선택한 백업이 영구적으로 삭제됩니다.

    ![백업 카탈로그로 이동](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. 삭제가 진행 중일 때와 삭제가 완료되었을 때 알림이 표시됩니다. 삭제를 완료 한 후 이 페이지의 쿼리를 새로 고칩니다. 삭제 된 백업 세트는 백업 세트의 목록에 더이상 나타나지 않습니다.

    ![백업 카탈로그로 이동](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>다음 단계
* 백업 카탈로그를 사용하여 [백업 세트에서 장치를 복원](storsimple-8000-restore-from-backup-set-u2.md)하는 방법을 알아봅니다.
* [StorSimple 장치 관리자 서비스를 사용하여 StorSimple 장치를 관리](storsimple-8000-manager-service-administration.md)하는 방법에 대해 자세히 알아보기

