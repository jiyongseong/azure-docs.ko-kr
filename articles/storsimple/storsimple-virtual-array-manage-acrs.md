---
title: "StorSimple Virtual Array에 대한 액세스 제어 레코드 관리 | Microsoft Docs"
description: "ACR(액세스 제어 레코드)을 관리하여 어떤 호스트가 StorSimple 가상 배열의 볼륨에 연결할 수 있는지 지정하는 방법에 대해 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>StorSimple 장치 관리자를 사용하여 StorSimple 가상 배열에 대한 액세스 제어 레코드 관리

## <a name="overview"></a>개요

ACR(액세스 제어 레코드)을 사용하면 어떤 호스트가 StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치라고도 함)장치의 볼륨에 연결할 수 있는지 지정할 수 있습니다. ACR은 특정 볼륨으로 설정되며 호스트의 IQN(iSCSI 정규화된 이름)을 포함합니다. 호스트가 볼륨에 연결하려고 할 때 해당 장치는 IQN 이름에 대한 볼륨과 연결된 ACR을 확인하고 일치하는 경우 이 연결이 확정됩니다. 장치 관리자 서비스의 **구성** 섹션 내에 있는 **액세스 제어 레코드** 블레이드는 호스트의 해당 IQN으로 모든 액세스 제어 레코드를 표시합니다.

![액세스 제어 레코드 관리](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

이 자습서에서는 다음과 같은 일반적인 ACR 관련 작업을 설명합니다.

* IQN 가져오기
* 액세스 제어 레코드 추가
* 액세스 제어 레코드 편집
* 액세스 제어 레코드 삭제

> [!IMPORTANT]
> 
> * 볼륨에 ACR을 할당할 때 이 볼륨을 손상시킬 수 있으므로 하나 이상의 클러스터되지 않은 호스트에서 동시에 액세스하지 않은 볼륨에 주의를 기울여야 합니다.
> * 볼륨에서 ACR을 삭제할 때 삭제로 인해 읽기-쓰기 중단이 발생할 수 있기 때문에 해당 호스트가 볼륨에 액세스하지 않는지 확인해야 합니다.


## <a name="get-the-iqn"></a>IQN 가져오기

Windows Server 2012를 실행하는 Windows 호스트의 IQN을 가져오려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>ACR 추가

StorSimple Device Manager 서비스의 **구성** 섹션 내에 있는 **액세스 제어 레코드** 블레이드를 사용하여 ACR을 추가합니다. 일반적으로 볼륨 하나와 ACR 하나를 연결합니다.

볼륨과 ACR을 연결하는 방법에 대한 정보는 [볼륨 추가](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)로 이동합니다.

> [!IMPORTANT]
> 볼륨에 ACR을 할당할 때 이 볼륨을 손상시킬 수 있으므로 하나 이상의 클러스터되지 않은 호스트에서 동시에 액세스하지 않은 볼륨에 주의를 기울여야 합니다.


ACR을 추가하려면 다음 단계를 수행합니다.

#### <a name="to-add-an-acr"></a>ACR을 추가하려면

1. 서비스 방문 페이지에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음 **구성** 섹션 내에서 **액세스 제어 레코드**를 클릭합니다.
2. **액세스 제어 레코드** 블레이드에서 **추가**를 클릭합니다.
3. **ACR 추가** 블레이드에서 다음을 수행합니다.
   
    1. ACR의 **이름** 을 지정합니다.
    
    2. **iSCSI 초기자 이름**에서 Windows 호스트의 IQN 이름을 제공합니다. Windows Server 호스트의 IQN을 가져오려면 다음을 수행합니다.
   
    3. Windows 호스트에서 Microsoft iSCSI 초기자를 시작합니다. iSCSI 초기자 속성 창의 **구성** 탭에서 **초기자 이름** 필드의 문자열을 선택하고 복사합니다.
    **ACR 추가** 블레이드의 **IQN** 필드에 이 문자열을 붙여넣습니다.
   
    6. **추가**를 클릭하여 ACR을 추가합니다.  
   
        ![액세스 제어 레코드 추가](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. 테이블 형식 목록이 이 추가 사항을 반영하도록 업데이트됩니다.

## <a name="edit-an-acr"></a>ACR 편집

Azure Portal에서 장치 관리자 서비스의 **구성** 섹션 내에 있는 **액세스 제어 레코드** 블레이드를 사용하여 ACR을 편집합니다.

> [!NOTE]
> 현재 사용 중인 ACR을 수정하지 않아야 합니다. 현재 사용 중인 볼륨과 연관된 ACR을 편집하려면 먼저 볼륨을 오프라인으로 전환해야 합니다.


ACR을 편집하려면 다음 단계를 수행합니다.

#### <a name="to-edit-an-acr"></a>ACR을 편집하려면

1. 서비스 방문 페이지에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음 **구성** 섹션 내에서 **액세스 제어 레코드**를 클릭합니다.
2. **액세스 제어 레코드** 블레이드에서 액세스 제어 레코드의 테이블 형식 목록에서 수정하려는 ACR을 두 번 클릭합니다.
3. **액세스 제어 레코드 편집** 블레이드에서 다음을 수행합니다.
   
    1. ACR에 IQN을 제공합니다.
   
    2. 블레이드의 맨 위에서 **저장**을 클릭하여 수정된 ACR을 저장합니다. 다음과 같은 확인 메시지가 표시됩니다.
   
        ![액세스 제어 레코드 편집](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>액세스 제어 레코드 삭제

Azure Portal에서 **구성** 페이지를 사용하여 ACR을 삭제합니다.

> [!NOTE]
> 
> * 현재 사용 중인 ACR을 삭제하지 않아야 합니다. 현재 사용 중인 볼륨과 연관된 ACR을 삭제하려면 먼저 볼륨을 오프라인으로 전환해야 합니다.
> * 볼륨에서 ACR을 삭제할 때 삭제로 인해 읽기-쓰기 중단이 발생할 수 있기 때문에 해당 호스트가 볼륨에 액세스하지 않는지 확인해야 합니다.


액세스 제어 레코드를 삭제하려면 다음 단계를 수행합니다.

#### <a name="to-delete-an-access-control-record"></a>액세스 제어 레코드를 삭제하려면

1. 서비스 방문 페이지에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음 **구성** 섹션 내에서 **액세스 제어 레코드**를 클릭합니다.

2. **액세스 제어 레코드** 블레이드에서 액세스 제어 레코드의 테이블 형식 목록에서 삭제하려는 ACR을 두 번 클릭합니다.

3. 액세스 제어 레코드 편집 블레이드에서 **삭제**를 두 번 클릭합니다.
   
    ![ACR 삭제](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. 확인 메시지가 나타나면 **삭제**를 클릭하여 삭제를 계속합니다. 테이블 형식 목록이 삭제를 반영하도록 업데이트됩니다.
   
   ![경고 메시지](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>다음 단계

* [볼륨 추가 및 ACR 구성](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)에 대해 자세히 알아봅니다.

