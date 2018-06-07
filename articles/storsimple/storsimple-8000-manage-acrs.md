---
title: "StorSimple에서 액세스 제어 레코드 관리 | Microsoft Docs"
description: "ACR(액세스 제어 레코드)을 사용하여 어떤 호스트가 StorSimple 장치의 볼륨에 연결할 수 있는지 지정하는 방법에 대해 설명합니다."
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
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>StorSimple 관리자 서비스를 사용하여 액세스 제어 레코드 관리

## <a name="overview"></a>개요
ACR(액세스 제어 레코드)을 사용하면 어떤 호스트가 StorSimple 장치의 볼륨에 연결할 수 있는지 지정할 수 있습니다. ACR은 특정 볼륨으로 설정되며 호스트의 IQN(iSCSI 정규화된 이름)을 포함합니다. 호스트가 볼륨에 연결하려고 할 때 해당 장치는 IQN 이름에 대한 볼륨과 연결된 ACR을 확인하고 일치하는 경우 이 연결이 확정됩니다. StorSimple 장치 관리자 서비스 블레이드의 **구성** 섹션 내에 있는 액세스 제어 레코드는 호스트의 해당 IQN으로 모든 액세스 제어 레코드를 표시합니다.

이 자습서에서는 다음과 같은 일반적인 ACR 관련 작업을 설명합니다.

* 액세스 제어 레코드 추가
* 액세스 제어 레코드 편집
* 액세스 제어 레코드 삭제

> [!IMPORTANT]
> * 볼륨에 ACR을 할당할 때 이 볼륨을 손상시킬 수 있으므로 하나 이상의 클러스터되지 않은 호스트에서 동시에 액세스하지 않은 볼륨에 주의를 기울여야 합니다.
> * 볼륨에서 ACR을 삭제할 때 삭제로 인해 읽기-쓰기 중단이 발생할 수 있기 때문에 해당 호스트가 볼륨에 액세스하지 않는지 확인해야 합니다.

## <a name="get-the-iqn"></a>IQN 가져오기

Windows Server 2012를 실행하는 Windows 호스트의 IQN을 가져오려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>액세스 제어 레코드 추가
StorSimple 장치 관리자 서비스 블레이드의 **구성** 섹션을 사용하여 ACR을 추가합니다. 일반적으로 하나의 볼륨으로 하나의 ACR을 연결합니다.

ACR을 추가하려면 다음 단계를 수행합니다.

#### <a name="to-add-an-acr"></a>ACR을 추가하려면

1. StorSimple 장치 관리자 서비스로 이동한 후 서비스 이름을 두 번 클릭하고 **구성** 섹션 내에서 **액세스 제어 레코드**를 클릭합니다.
2. **액세스 제어 레코드** 블레이드에서 **+ ACR 추가**를 클릭합니다.

    ![ACR 추가 클릭](./media/storsimple-8000-manage-acrs/createacr1.png)

3. **ACR 추가** 블레이드에서 다음 단계를 수행합니다.

    1. ACR의 이름을 지정합니다.
    
    2. **IQN(iSCSI 초기자 이름)**에서 Windows Server 호스트의 IQN 이름을 제공합니다.

    3. **추가**를 클릭하여 ACR을 만듭니다.

        ![ACR 추가 클릭](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  새로 추가된 테이블 형식의 ACR 목록에 표시됩니다.

    ![ACR 추가 클릭](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>액세스 제어 레코드 편집
StorSimple 장치 관리자 서비스 블레이드의 **구성** 섹션을 사용하여 ACR을 편집합니다.

> [!NOTE]
> 현재 사용하지 않고 있는 ACR만 수정하는 것이 좋습니다. 현재 사용 중인 볼륨과 연관된 ACR을 편집하려면 먼저 볼륨을 오프라인으로 전환해야 합니다.

ACR을 편집하려면 다음 단계를 수행합니다.

#### <a name="to-edit-an-access-control-record"></a>액세스 제어 레코드를 편집하려면
1.  StorSimple 장치 관리자 서비스로 이동한 후 서비스 이름을 두 번 클릭하고 **구성** 섹션 내에서 **액세스 제어 레코드**를 클릭합니다.

    ![액세스 제어 레코드로 이동](./media/storsimple-8000-manage-acrs/createacr1.png)

2. 액세스 제어 레코드의 테이블 형식 목록에서 수정하려는 ACR을 클릭하여 선택합니다.

    ![액세스 제어 레코드 편집](./media/storsimple-8000-manage-acrs/editacr1.png)

3. **액세스 제어 레코드 편집** 블레이드에서 다른 호스트에 해당하는 다른 IQN을 제공합니다.

    ![액세스 제어 레코드 편집](./media/storsimple-8000-manage-acrs/editacr2.png)

4. **Save**를 클릭합니다. 확인하라는 메시지가 표시되면 **예**를 클릭합니다. 

    ![액세스 제어 레코드 편집](./media/storsimple-8000-manage-acrs/editacr3.png)

5. ACR이 업데이트되면 알림이 제공됩니다. 이러한 변경을 반영하도록 테이블 형식 목록도 업데이트됩니다.

   
## <a name="delete-an-access-control-record"></a>액세스 제어 레코드 삭제
StorSimple 장치 관리자 서비스 블레이드의 **구성** 섹션을 사용하여 ACR을 삭제합니다.

> [!NOTE]
> 현재 사용하지 않고 있는 ACR만 삭제할 수 있습니다. 현재 사용 중인 볼륨과 연관된 ACR을 삭제하려면 먼저 볼륨을 오프라인으로 전환해야 합니다.

액세스 제어 레코드를 삭제하려면 다음 단계를 수행합니다.

#### <a name="to-delete-an-access-control-record"></a>액세스 제어 레코드를 삭제하려면
1.  StorSimple 장치 관리자 서비스로 이동한 후 서비스 이름을 두 번 클릭하고 **구성** 섹션 내에서 **액세스 제어 레코드**를 클릭합니다.

    ![액세스 제어 레코드로 이동](./media/storsimple-8000-manage-acrs/createacr1.png)

2. 액세스 제어 레코드의 테이블 형식 목록에서 삭제하려는 ACR을 클릭하여 선택합니다.

    ![액세스 제어 레코드로 이동](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. 마우스 오른쪽 단추를 클릭하여 상황에 맞는 메뉴를 호출하고 **삭제**를 선택합니다.

    ![액세스 제어 레코드로 이동](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. 확인을 위한 메시지가 나타나면 정보를 검토하고 **삭제**를 클릭합니다.

    ![액세스 제어 레코드로 이동](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. 삭제가 완료되면 알림이 표시됩니다. 테이블 형식 목록이 삭제를 반영하도록 업데이트됩니다.

    ![액세스 제어 레코드로 이동](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>다음 단계
* [StorSimple 볼륨 관리](storsimple-8000-manage-volumes-u2.md)에 대해 자세히 알아봅니다.
* [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 자세히 알아봅니다.

