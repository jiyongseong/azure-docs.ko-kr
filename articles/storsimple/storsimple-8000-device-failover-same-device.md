---
title: "8000 시리즈 장치에 대한 StorSimple 장애 조치(failover), 재해 복구 | Microsoft Docs"
description: "StorSimple 장치를 동일한 장치로 장애 조치(failover)하는 방법을 알아봅니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>StorSimple 물리적 장치를 동일한 장치로 장애 조치(failover)

## <a name="overview"></a>개요

이 자습서에서는 재해가 발생하는 경우 StorSimple 8000 시리즈 물리적 장치를 자체 장치로 장애 조치(failover)하는 데 필요한 단계를 설명합니다. StorSimple에서는 장치 장애 조치(failover) 기능을 사용하여 데이터 센터의 원본 물리적 장치에서 다른 물리적 장치로 데이터를 마이그레이션합니다. 이 자습서의 지침은 소프트웨어 버전 업데이트 3 이상을 실행하는 StorSimple 8000 시리즈 물리적 장치에 적용됩니다.

장치 장애 조치(failover) 및 재해 복구에 사용되는 방법에 대해 자세히 알아보려면 [StorSimple 8000 시리즈 장치에 대한 장애 조치(failover) 및 재해 복구](storsimple-8000-device-failover-disaster-recovery.md)로 이동하세요.

물리적 장치를 다른 물리적 장치로 장애 조치(failover)하려면 [동일한 StorSimple 물리적 장치로 장애 조치(failover)](storsimple-8000-device-failover-physical-device.md)로 이동하세요. StorSimple 물리적 장치를 StorSimple Cloud Appliance로 장애 조치(failover)하려면 [StorSimple Cloud Appliance로 장애 조치(failover)](storsimple-8000-device-failover-cloud-appliance.md)로 이동하세요.


## <a name="prerequisites"></a>필수 조건

- 장치 장애 조치(failover)에 대한 고려 사항을 검토했는지 확인하세요. 자세한 내용을 보려면 [장치 장애 조치(failover)에 대한 일반적인 고려 사항](storsimple-8000-device-failover-disaster-recovery.md)으로 이동하세요.


## <a name="steps-to-fail-over-to-the-same-device"></a>동일한 장치로 장애 조치(failover)하는 단계

동일한 장치로 장애 조치(failover)해야 하는 경우 다음 단계를 수행합니다.

1. 장치에서 모든 볼륨의 클라우드 스냅숏을 마련합니다. 자세한 내용을 보려면 [StorSimple 장치 관리자 서비스를 사용하여 백업 만들기](storsimple-8000-manage-backup-policies-u2.md)로 이동합니다.
2. 장치를 공장 기본값으로 다시 설정합니다. [StorSimple 장치를 공장 기본 설정으로 다시 설정하는 방법](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)의 자세한 지침에 따릅니다.
3. StorSimple 장치 관리자 서비스로 이동한 다음 **장치**를 선택합니다. **장치** 블레이드에서 이전 장치는 **오프라인**으로 표시되어야 합니다.

    ![오프라인 상태의 원본 장치](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. 장치를 구성하고 StorSimple 장치 관리자 서비스에 다시 등록합니다. 새로 등록된 장치는 **Ready to set up**(설정 준비 완료)으로 표시되어야 합니다. 새 장치의 장치 이름이 이전 장치와 동일하지만 장치가 공장 기본값으로 다시 설정되어 다시 등록되었음을 나타내는 숫자가 추가되어 있습니다.

    ![설정 준비 완료 상태의 새로 등록된 장치](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. 새 장치의 경우 장치 설정을 완료합니다. 자세한 내용을 보려면 [4단계: 최소 장치 설정 완료](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup)로 이동하세요. **장치** 블레이드에서 장치의 상태가 **온라인**으로 변경됩니다.

   > [!IMPORTANT]
   > **최소 구성을 먼저 완료하지 않으면 DR이 실패할 수 있습니다.**

    ![온라인 상태의 새로 등록된 장치](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. 이전 장치(오프라인 상태)를 선택하고 명령 모음에서 **장애 조치(failover)**를 클릭합니다. **장애 조치(failover)** 블레이드에서 이전 장치를 원본으로 선택하고 대상 장치를 새로 등록된 장치로 지정합니다.

    ![장애 조치(failover) 요약](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    자세한 지침은 [다른 실제 장치에 장애 조치](#fail-over-to-another-physical-device)를 참조하세요.

7. **작업** 블레이드에서 모니터링할 수 있는 장치 복원 작업이 만들어집니다.

8. 작업이 성공적으로 완료되면 새 장치에 액세스하고 **볼륨 컨테이너** 블레이드로 이동합니다. 이전 장치의 모든 볼륨 컨테이너가 새 장치로 마이그레이션되었는지 확인합니다.

   ![마이그레이션된 볼륨 컨테이너](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. 장애 조치(failover)가 완료되면 포털에서 이전 장치를 비활성화하고 삭제할 수 있습니다. 이전 장치(오프라인)를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **비활성화**를 선택합니다. 장치가 비활성화되면 장치 상태가 업데이트됩니다.

     ![비활성화된 원본 장치](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. 비활성화된 장치를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **삭제**를 선택합니다. 이렇게 하면 장치 목록에서 장치가 삭제됩니다.

    ![삭제된 원본 장치](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>다음 단계

* 장애 조치(failover)를 수행한 후에 [StorSimple 장치 비활성화 또는 삭제](storsimple-8000-deactivate-and-delete-device.md)를 해야 할 수도 있습니다.
* StorSimple 장치 관리자 서비스를 사용하는 방법에 대한 자세한 내용을 보려면 [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md)(StorSimple 장치 관리자 서비스를 사용하여 StorSimple 장치 관리)로 이동하세요.

