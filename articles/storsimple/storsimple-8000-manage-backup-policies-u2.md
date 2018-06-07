---
title: "StorSimple 8000 시리즈 백업 정책 관리 | Microsoft Docs"
description: "StorSimple 장치 관리자 서비스를 사용하여 StorSimple 8000 시리즈 장치에서 수동 백업, 백업 일정 및 백업 보존을 만들고 관리하는 방법을 설명합니다."
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Azure Portal에서 StorSimple 장치 관리자 서비스를 사용하여 백업 정책 관리


## <a name="overview"></a>개요

이 자습서에서는 StorSimple 장치 관리자 서비스 **백업 정책** 블레이드를 사용하여 StorSimple 볼륨에 대한 백업 프로세스 및 백업 보존을 제어하는 방법을 설명합니다. 수동 백업을 완료하는 방법도 설명합니다.

볼륨을 백업할 때 로컬 스냅숏 또는 클라우드 스냅숏을 만들도록 선택할 수 있습니다. 로컬로 고정된 볼륨을 백업하는 경우 클라우드 스냅숏을 지정하는 것이 좋습니다. 이탈 수가 많은 데이터 집합과 결합하여 로컬로 고정된 볼륨의 로컬 스냅숏을 많이 만들면 로컬 공간이 빨리 소진될 수 있습니다. 로컬 스냅숏을 만들기로 선택한 경우 가장 최근 상태를 백업하는 일일 스냅숏을 더 적게 만들어 하루 동안 유지한 다음 삭제하는 것이 좋습니다.

로컬로 고정된 볼륨의 클라우드 스냅숏을 만드는 경우 중복 제거 및 압축된 클라우드로 변경된 데이터만 복사합니다.

## <a name="the-backup-policy-blade"></a>백업 정책 블레이드

StorSimple 장치에 대한 **백업 정책** 블레이드에서는 백업 정책을 관리하고 로컬과 클라우드 스냅숏을 예약할 수 있습니다. 백업 정책이 볼륨의 컬렉션에 대한 백업 보존 및 백업 일정을 구성하는데 사용됩니다. 백업 정책을 통해 동시에 여러 볼륨의 스냅숏을 사용할 수 있습니다. 이 백업 정책에서 생성된 백업은 크래시 일관성이 있는 복사본임을 의미합니다.

백업 정책 테이블 형식 목록에서 다음 필드 중 하나 이상의 기존 백업 정책을 필터링할 수도 있습니다.

* **정책 이름** – 정책과 연결된 이름입니다. 다음과 같은 다양한 유형의 정책이 포함됩니다.

  * 사용자가 명시적으로 만든 예약된 정책입니다.
  * 원래 StorSimple 스냅숏 관리자에서 만든 가져온 정책입니다. 이 정책에서 가져온 StorSimple 스냅숏 관리자 호스트를 설명하는 태그가 포함됩니다.

  > [!NOTE]
  > 볼륨을 만들 때는 자동 또는 기본 백업 정책이 더 이상 사용되지 않습니다.

* **마지막으로 성공한 백업** –이 정책을 사용하여 수행된 마지막으로 성공한 백업 시간과 날짜입니다.

* **다음 백업** –이 정책에서 시작될 다음 예약 백업의 시간과 날짜입니다.

* **볼륨** – 정책과 연결된 볼륨입니다. 백업을 만들 때 백업 정책과 연관된 모든 볼륨이 함께 그룹화됩니다.

* **일정** – 백업 정책과 연관된 일정의 수입니다.

백업 정책에 대해 수행할 수 있는 자주 사용되는 작업은 다음과 같습니다.

* 백업 정책 추가
* 일정 추가 또는 수정
* 볼륨 추가 또는 제거
* 백업 정책 삭제
* 수동 백업 수행

## <a name="add-a-backup-policy"></a>백업 정책 추가

자동 백업을 예약하려면 백업 정책을 추가합니다. 볼륨을 처음 만들 때는 볼륨과 연결된 기본 백업 정책이 없습니다. 볼륨 데이터를 보호하기 위해 백업 정책을 추가하고 할당해야 합니다.

StorSimple 장치에 대한 백업 정책을 추가하려면 Azure Portal에서 아래 단계를 수행합니다. 정책을 추가한 후 일정을 정의할 수 있습니다( [일정 추가 또는 수정](#add-or-modify-a-schedule)참조).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>일정 추가 또는 수정

StorSimple 장치의 기존 백업 정책에 첨부된 일정을 추가하거나 수정할 수 있습니다. 일정을 추가하거나 수정하려면 Azure Portal에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>볼륨 추가 또는 제거

StorSimple 장치에서 백업 정책에 할당된 볼륨을 추가하거나 제거할 수 있습니다. 볼륨을 추가하거나 제거하려면 Azure Portal에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>백업 정책 삭제

StorSimple 장치에서 백업 정책을 삭제하려면 Azure Portal에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>수동 백업 수행

단일 볼륨에 대한 주문형(수동) 백업을 만들려면 Azure Portal에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>다음 단계

[StorSimple 장치 관리자 서비스를 사용하여 StorSimple 장치를 관리하는 방법](storsimple-8000-manager-service-administration.md)에 대해 자세히 알아봅니다.

