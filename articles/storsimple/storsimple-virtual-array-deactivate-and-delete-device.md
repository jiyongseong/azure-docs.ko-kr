---
title: "Microsoft Azure StorSimple 가상 배열 비활성화 및 삭제 | Microsoft Docs"
description: "먼저 StorSimple 장치를 비활성화한 후 삭제하여 서비스에서 제거하는 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>StorSimple 가상 배열 비활성화 및 삭제

## <a name="overview"></a>개요

StorSimple 가상 배열을 비활성화하면 장치 및 해당 StorSimple 장치 관리자 서비스 간의 연결이 끊깁니다. 이 자습서에서는 다음을 수행하는 방법을 설명합니다.

* 장치 비활성화 
* 비활성화된 장치 삭제

이 문서의 정보는 StorSimple 가상 배열에만 적용됩니다. 8000 시리즈에 대한 내용은 [장치를 비활성화 또는 삭제](storsimple-deactivate-and-delete-device.md)하는 방법을 참조하세요.

## <a name="when-to-deactivate"></a>비활성화하는 경우

비활성화는 영구 작업이며 실행 취소할 수 없습니다. 비활성화된 장치를 StorSimple 장치 관리자 서비스에 다시 등록할 수 없습니다. 다음과 같은 시나리오에서 StorSimple 가상 배열을 비활성화하고 삭제해야 합니다.

* **계획된 장애조치**: 장치가 온라인 상태이고 이 장치를 장애 조치할 계획입니다. 더 큰 장치로 업그레이드하려는 경우 장치를 장애 조치해야 합니다. 데이터 소유권을 이전하고 장애 조치가 완료된 후에 원본 장치는 자동으로 삭제됩니다.
* **계획되지 않은 장애조치**: 장치가 오프라인 상태이고 이 장치를 장애 조치해야 합니다. 이 시나리오는 데이터 센터에서 중단이 발생하고 기본 장치가 중단되는 재해 발생 시 수행할 수 있습니다. 장치를 보조 장치로 장애 조치하려고 합니다. 데이터 소유권을 이전하고 장애 조치가 완료된 후에 원본 장치는 자동으로 삭제됩니다.
* **서비스 해제**: 장치 서비스를 해제하려고 합니다. 이를 위해서는 먼저 장치를 비활성화한 다음 삭제해야 합니다. 장치를 비활성화하면 로컬로 저장된 데이터에 더 이상 액세스할 수 없게 됩니다. 클라우드에 저장된 데이터에 액세스하고 복구할 수 있습니다. 비활성화 후 장치 데이터를 유지하려는 경우 장치를 비활성화하기 전에 모든 데이터의 클라우드 스냅숏을 만들어야 합니다. 이 클라우드 스냅숏을 사용하면 이후 단계에서 모든 데이터를 복구할 수 있습니다.

## <a name="deactivate-a-device"></a>장치 비활성화

장치를 비활성화하려면 다음 단계를 수행합니다.

#### <a name="to-deactivate-the-device"></a>장치를 비활성화하려면

1. 서비스에서 **관리 > 장치**로 이동합니다. **장치** 블레이드에서 비활성화하려는 장치를 클릭하고 선택합니다.
   
    ![비활성화할 장치 선택](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. **장치 대시보드** 블레이드의 목록에서 **... 추가**를 클릭하고 **비활성화**를 선택합니다.
   
    ![비활성화 클릭](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. **비활성화** 블레이드에서 장치 이름을 입력한 다음 **비활성화**를 클릭합니다. 
   
    ![비활성화 확인](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    비활성화 프로세스가 시작되고 완료하는 데 몇 분이 소요됩니다.
   
    ![진행 중인 비활성화](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. 비활성화한 후에 장치 목록이 새로 고쳐집니다.
   
    ![비활성화 완료](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    이제 이 장치를 삭제할 수 있습니다.

## <a name="delete-the-device"></a>장치 삭제

삭제하려면 먼저 장치를 비활성화해야 합니다. 장치를 삭제하면 서비스에 연결된 장치 목록에서 제거됩니다. 그러면 서비스에서 삭제된 장치를 더 이상 관리할 수 없습니다. 그러나 장치와 연결된 데이터는 클라우드에 유지됩니다. 그러면 이 데이터를 사용하는 데 요금이 발생합니다.

장치를 삭제하려면 다음 단계를 수행합니다.

#### <a name="to-delete-the-device"></a>장치를 삭제하려면

1. StorSimple 장치 관리자에서 **관리 > 장치**로 이동합니다. **장치** 블레이드에서 삭제하려는 비활성화된 장치를 선택합니다.
2. **장치 대시보드** 블레이드에서 **...추가**를 클릭한 다음 **삭제**를 클릭합니다.
   
   ![삭제할 장치 선택](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. **삭제** 블레이드에서 삭제를 확인하는 장치의 이름을 입력한 다음 **삭제**를 클릭합니다. 장치를 삭제하더라도 장치와 연결된 클라우드 데이터를 삭제하지 않습니다. 
   
   ![삭제 확인](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. 삭제 작업이 시작되고 완료하는 데 몇 분이 소요됩니다.
   
   ![진행 중인 삭제](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    장치를 삭제한 후에 업데이트된 장치 목록을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 장애 조치 방법에 대한 정보는 [StorSimple 가상 배열 장애 조치 및 재해 복구](storsimple-virtual-array-failover-dr.md)로 이동합니다.

* StorSimple 장치 관리자 서비스를 사용하는 방법을 자세히 알아보려면 [StorSimple 장치 관리자 서비스를 사용하여 StorSimple 가상 배열 관리](storsimple-virtual-array-manager-service-administration.md)로 이동하세요. 

