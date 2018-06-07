---
title: StorSimple 가상 배열 작업 보기 및 관리 | Microsoft Docs
description: StorSimple 장치 관리자 서비스 작업 페이지에 대해 설명하고 이 페이지를 사용하여 StorSimple 가상 배열에 대한 최근 및 현재 작업을 추적하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>StorSimple 장치 관리자 서비스를 사용하여 StorSimple 가상 배열에 대한 작업 보기
## <a name="overview"></a>개요
**작업** 블레이드에서는 StorSimple 장치 관리자 서비스에 연결된 가상 배열에서 시작한 작업을 보고 관리하기 위한 단일 중앙 포털을 제공합니다. 여러 가상 장치에 대한 실행, 완료 및 실패한 작업을 볼 수 있습니다. 결과는 표 형식으로 나타납니다.

![작업 블레이드](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

다음과 같이 필드에서 필터링하여 관심 있는 작업을 빠르게 찾을 수 있습니다.

* **시간 범위** – 작업은 날짜 및 시간 범위에 따라 필터링할 수 있습니다.
* **장치** – 작업은 서비스에 연결된 특정 장치에서 시작됩니다. 그런 다음 필터링된 작업은 다음 특성을 기반으로 표로 정리됩니다.
  
  * **이름** – 작업 이름은 **모두**, **Backup**, **복제**, **장애 조치**, **업데이트 다운로드** 또는 **업데이트 설치**일 수 있습니다.
  * **상태** – 작업은 **모두**, **진행 중**, **성공**이나 **실패** 또는 **취소** 상태일 수 있습니다.
  * **엔터티** – 작업은 볼륨, 공유 또는 장치에 연관될 수 있습니다.
  * **장치** – 작업이 시작된 장치의 이름입니다.
  * **시작 시간** – 작업이 시작된 시간입니다.
  * **기간** – 작업이 실행되는 기간입니다.
* **상태** – 모두, 실행 중, 완료 또는 실패한 작업을 검색할 수 있습니다.
* **작업 유형** – 작업 유형은 모두, 백업, 복원, 장애 조치, 업데이트 다운로드 또는 업데이트 설치일 수 있습니다.

작업 목록은 30초마다 새로 고쳐집니다.

## <a name="view-job-details"></a>작업 세부 정보 보기
다음 단계에 따라 작업 세부 정보를 봅니다.

#### <a name="to-view-job-details"></a>작업 세부 정보 보는 방법
1. **작업** 블레이드에서 적절한 필터와 함께 쿼리를 실행하여 관심 있는 작업을 표시합니다. 완료되거나, 실행 중인 작업을 검색할 수 있습니다.
2. 작업 테이블 형식 목록에서 작업을 선택합니다.
   
    ![작업 블레이드](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. 페이지 맨 아래에서 **세부 정보**를 클릭합니다.
4. **세부 정보** 대화 상자에서 상태, 세부 정보 및 시간 통계를 볼 수 있습니다. 다음 그림은 **백업 작업 세부 정보** 대화 상자의 예를 보여 줍니다.
   
    ![작업 세부 정보](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>가상 머신이 하이퍼바이저에서 일시 중지되는 경우 작업 실패
StorSimple 가상 배열에서 작업이 진행 중인 경우 장치(하이퍼바이저에 프로비전된 가상 컴퓨터)가 15분 넘게 일시 중지되면 작업이 실패합니다. 이는 StorSimple 가상 배열 시간과 Microsoft Azure 시간이 동기화 해제되기 때문입니다. 

다음과 같은 오류가 표시됩니다. "장치 시간이 15분 넘게 Microsoft Azure와 동기화가 끊어졌습니다. 하이퍼바이저와 장치 시간이 NTP 서비스와 동기화되어 있는지 확인합니다. 연결 문제가 없는지 확인합니다. 연결 문제를 해결하려면 가상 장치의 로컬 웹 UI에서 진단 테스트를 실행하세요."

이러한 실패는 백업, 복원, 업데이트 및 장애 조치(failover) 작업에 적용됩니다. 가상 머신이 Hyper-V에 프로비전된 경우 이 컴퓨터는 최종적으로 하이퍼바이저와 시간을 동기화합니다. 이러한 동기화 후 작업을 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[로컬 웹 UI를 사용하여 StorSimple 가상 배열을 관리하는 방법을 알아봅니다.](storsimple-ova-web-ui-admin.md)

