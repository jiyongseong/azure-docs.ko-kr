---
title: StorSimple 8000 시리즈 업데이트 4 릴리스 정보 | Microsoft Docs
description: StorSimple 8000 시리즈 업데이트 4의 새로운 기능, 문제 및 해결 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: f4db5a49b71b0c384da7af2b90bd5426a4f35c72
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 시리즈 업데이트 4 릴리스 정보

## <a name="overview"></a>개요

다음 릴리스 정보는 새로운 기능에 대해 설명하고 StorSimple 8000 시리즈 업데이트 4에 대한 중요한 미해결 문제를 식별합니다. 또한 이 릴리스에 포함된 StorSimple 소프트웨어 업데이트 목록을 포함합니다. 

업데이트 4는 릴리스(GA) 또는 업데이트 0.1~업데이트 3.1을 실행하는 모든 StorSimple 장치에 적용할 수 있습니다. 업데이트 4에 연결된 장치 버전은 6.3.9600.17820입니다.

StorSimple 솔루션에 업데이트를 배포하기 전에 릴리스 정보에 포함된 정보를 검토하십시오.

> [!IMPORTANT]
> * 업데이트 4는 장치 소프트웨어, USM 펌웨어, LSI 드라이버 및 펌웨어, 디스크 펌웨어, Storport 및 Spaceport, 보안 및 기타 OS 업데이트를 포함합니다. 이 업데이트를 설치하는 데는 4시간 정도 걸립니다. 디스크 펌웨어 업데이트는 중단 업데이트이며 사용자 장치에 대한 가동 중지 시간이 발생합니다. 장치를 최신 상태로 유지하려면 업데이트 4를 적용하는 것이 좋습니다. 
> * 새 릴리스의 경우, 업데이트의 단계적 롤아웃을 수행하기 때문에 즉시 업데이트를 볼 수는 없습니다. 업데이트가 곧 제공될 예정이니 몇 일 후에 업데이트를 다시 검색하세요.

## <a name="whats-new-in-update-4"></a>업데이트 4의 새로운 기능

업데이트 4에는 다음과 같은 주요 향상 기능 및 버그 수정이 포함되어 있습니다.

* **우수한 자동화된 공간 재사용 알고리즘** - 업데이트 4에서 자동화된 공간 재사용 알고리즘은 클라우드에서 사용 가능한 예상 회수 공간에 따라 공간 재사용 주기를 조정하도록 향상되었습니다. 

* **로컬로 고정된 볼륨에 대한 성능 향상** - 업데이트 4에서는 높은 데이터 수집(볼륨 크기와 비교할 수 있는 데이터)이 있는 시나리오에서 로컬로 고정된 볼륨의 성능이 향상되었습니다.

* **열 지도 기반 복원** - 이전 릴리스에서는 DR(재해 복구)에 따라 성능 저하를 가져오는 액세스 패턴을 기반으로 하는 클라우드에서 데이터가 복원되었습니다. 

    장치가 DR보다 먼저 사용될 때 열 지도를 만드는 자주 액세스되는 데이터를 추적하는 새로운 기능이 업데이트 4에서 구현됩니다(가장 많이 사용되는 데이터 청크는 높은 열을 가지는 반면 적게 사용되는 청크는 낮은 열을 가짐). DR 후 StorSimple은 클라우드로부터 데이터를 자동으로 복원하고 리하이드레이션하는 데 열 지도를 사용합니다. 

    모든 복원은 이제 열 지도 기반 복원입니다. 열 지도 기반 복원 및 리하이드레이션 작업을 쿼리 및 취소하는 방법에 대한 자세한 내용은 [StorSimple용 Windows PowerShell cmdlet 참조](https://technet.microsoft.com/library/dn688168.aspx)로 이동합니다.

* **StorSimple 진단 도구** – 업데이트 4에서 StorSimple 진단 도구는 시스템, 네트워크, 성능 및 하드웨어 구성 요소 상태와 관련된 문제를 쉽게 진단 및 해결할 수 있도록 릴리스되었습니다. 이 도구는 StorSimple용 Windows PowerShell을 통해 실행됩니다. 자세한 정보는 [StorSimple 진단 도구를 사용한 문제 해결](storsimple-8000-diagnostics.md)을 참조하세요.

* **UI 기반 StorSimple 마이그레이션 도구** - 이 릴리스 전에 5000-7000 시리즈에서 데이터 마이그레이션에는 사용자가 Azure PowerShell 인터페이스를 사용하여 마이그레이션 워크플로의 일부를 실행해야 했습니다. 이 릴리스에서 사용하기 쉬운 UI 기반 StorSimple 마이그레이션 도구는 동일한 마이그레이션 워크플로를 용이하게 하는 지원에 사용 가능합니다. 이 도구는 복구 버킷의 통합도 허용합니다. 

* **FIPS 관련 변경 사항** - 이 릴리스부터 Microsoft Azure Government 및 Azure 공용 클라우드 계정에 대한 모든 StorSimple 8000 시리즈 장치에는 기본적으로 FIPS가 활성화됩니다.

* **업데이트 변경 내용** - 이 릴리스에서는 업데이트 실패와 관련된 버그가 수정되었습니다.

* **디스크 오류에 대한 경고** - 임박한 디스크 오류를 사용자에게 경고하는 새로운 경고가 이 릴리스에서 추가됩니다. 이 경고가 발생하는 경우 Microsoft 지원에 문의하여 대체 디스크를 보내주세요. 자세한 정보는 [StorSimple 장치에서 하드웨어 경고](storsimple-8000-manage-alerts.md#hardware-alerts)를 참조하세요.

* **컨트롤러 교체 변경 사항** - 사용자가 컨트롤러 교체 프로세스의 상태를 쿼리할 수 있도록 하는 cmdlet이 이 릴리스에서 추가됩니다. 자세한 내용은 [컨트롤러 교체 상태를 쿼리하는 cmdlet](https://technet.microsoft.com/library/dn688168.aspx)으로 이동합니다.


## <a name="issues-fixed-in-update-4"></a>업데이트 4에서 해결된 문제

다음 테이블에서 업데이트 4에서 해결된 문제를 간략하게 설명합니다.    

| 아니오 | 기능 | 문제 | 실제 장치에 적용 | 가상 장치에 적용 |
| --- | --- | --- | --- | --- |
| 1 |장애 조치(failover) |이전 릴리스에서 장애 조치(failover) 후 고객 쪽에서 관찰된 정리와 관련된 문제가 있었습니다. 이 문제는 이 릴리스에서 해결되었습니다. |예 |예 |
| 2 |로컬로 고정된 볼륨 |이전 릴리스에서 볼륨 만들기 실패를 초래할 수 있는 로컬로 고정된 볼륨에 대한 볼륨 만들기와 관련된 문제가 있었습니다. 이 문제는 근본 원인이 파악되었고 이 릴리스에서 수정되었습니다. |예 |아니오 |
| 3 |지원 패키지 |이전 릴리스에서 System.OutOfMemory 예외를 초래할 수 있는 지원 패키지 또는 지원 패키지 만들기 실패를 가져오는 다른 오류와 관련된 문제가 있었습니다. 이러한 버그는 이 릴리스에서 해결되었습니다. |예 |예 |
| 4 |모니터링 |이전 릴리스에서 EB에서 소비가 표시되었던 로컬로 고정된 볼륨에 대한 모니터링 차트와 관련된 문제가 있었습니다. 이 버그는 이 릴리스에서 해결되었습니다. |예 |예 |
| 5 |마이그레이션 |이전 릴리스에서 5000-7000 시리즈에서 8000 시리즈 장치로 마이그레이션의 안정성과 관련된 몇 가지 문제가 있었습니다. 이러한 문제는 이 릴리스에서 해결되었습니다. |예 |예 |
| 6 |주 지역에서 |이전 릴리스에서 업데이트 오류가 발생한 경우 컨트롤러가 복구 모드로 전환되므로 사용자는 업데이트를 계속할 수 없으며 Microsoft 지원에 문의해야 했습니다. <br> 이번 릴리스에서는 이 동작이 바뀌었습니다. 두 컨트롤러 모두 동일한 버전(업데이트 4)을 실행한 후에 업데이트가 실패하면 컨트롤러는 복구 모드로 전환되지 않습니다. 이 오류가 발생하는 경우 잠시 기다린 다음 업데이트를 다시 시도하는 것이 좋습니다. 다시 시도는 성공할 수 있습니다. 다시 시도가 실패하는 경우 Microsoft 지원에 문의해야 합니다. |예 |예 |


## <a name="known-issues-in-update-4-from-previous-releases"></a>이전 릴리스에서 업데이트 4의 알려진 문제

업데이트 4에 알려진 새로운 문제가 없습니다. 이전 릴리스에서 업데이트 4로 이어지는 문제 목록은 [업데이트 3 릴리스 정보](storsimple-update3-release-notes.md#known-issues-in-update-3)로 이동합니다.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>업데이트 4의 SAS(Serial attached SCSI) 컨트롤러 및 펌웨어 업데이트

이 릴리스에는 SAS 컨트롤러, LSI 드라이버 및 펌웨어 업데이트가 있습니다. 이러한 업데이트를 설치하는 방법에 대한 자세한 내용은 StorSimple 장치의 [업데이트 4 설치](storsimple-install-update-4.md)를 참조하세요.

## <a name="virtual-device-updates-in-update-4"></a>업데이트 4의 가상 장치 업데이트

이 업데이트는 StorSimple 클라우드 어플라이언스에 적용할 수 없습니다(가상 장치라고도 함). 새 가상 장치를 만들어야 합니다. 

## <a name="next-step"></a>다음 단계

StorSimple 장치에 [업데이트 4를 설치](storsimple-install-update-4.md)하는 방법을 알아봅니다.

