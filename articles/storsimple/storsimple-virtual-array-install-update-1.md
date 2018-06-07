---
title: "StorSimple 가상 배열에 업데이트 1.0 설치 | Microsoft Docs"
description: "StorSimple 가상 배열 웹 UI를 사용하여 Azure Portal 및 핫픽스 방법을 사용하는 업데이트를 적용하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: a85290f3f56eb1e1bf57524c43c6d4fea36129f7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>StorSimple 가상 배열에 업데이트 1.0 설치

## <a name="overview"></a>개요

이 문서에서는 로컬 웹 UI 및 Azure Portal을 사용하여 StorSimple 가상 배열에 업데이트 1.0을 설치하는 데 필요한 단계를 설명합니다.

StorSimple 가상 배열을 최신 상태로 유지하려면 소프트웨어 업데이트 또는 핫픽스를 적용합니다. 업데이트를 적용하기 전에 호스트에서 볼륨 또는 공유를 오프라인으로 전환한 후 장치를 오프라인으로 전환하는 것이 좋습니다. 이렇게 하면 데이터 손상 가능성이 최소화됩니다. 볼륨 또는 공유가 오프라인 상태가 되면 장치의 수동 백업도 수행해야 합니다.

> [!IMPORTANT]
> - 업데이트 1.0은 장치에 있는 소프트웨어 버전 **10.0.10296.0**에 해당합니다. 이 업데이트의 새로운 기능에 대한 자세한 내용은 [업데이트 1.0에 대한 릴리스 정보](storsimple-virtual-array-update-1-release-notes.md)로 이동합니다.
>
> - 업데이트 또는 핫픽스를 설치하면 장치가 다시 시작됩니다. StorSimple 가상 배열이 단일 노드 장치인 경우 진행 중인 모든 IO가 중단되고 장치에 가동 중지 시간이 발생합니다.
>
> - 업데이트 1은 가상 배열에서 업데이트 0.6을 실행하는 경우에만 Azure Portal에서 사용할 수 있습니다. 사전 업데이트 0.6 버전을 실행 중인 가상 배열의 경우 업데이트 0.6을 먼저 설치하고 업데이트 1을 설치해야 합니다.

## <a name="use-the-azure-portal"></a>Azure 포털 사용

업데이트 0.2 이상을 실행하는 경우 Azure Portal을 통해 업데이트를 설치하는 것이 좋습니다. 포털 절차를 사용하려면 사용자가 업데이트를 스캔, 다운로드한 다음 설치해야 합니다. 가상 배열이 실행 중인 소프트웨어 버전에 따라, Azure Portal을 통한 업데이트 적용이 다릅니다.

 - 가상 배열에서 업데이트 0.6을 실행 중인 경우 Azure Portal은 장치에 업데이트 1(10.0.10296.0)을 직접 설치합니다. 이 절차를 완료하는 데 약 7분이 걸립니다.
 - 가상 배열에서 업데이트 0.6 이전 버전을 실행하는 경우 업데이트는 두 단계로 완료됩니다. 먼저 Azure Portal은 장치에 업데이트 0.6(10.0.10293.0)을 설치합니다. 가상 배열이 다시 부팅된 후 포털에서 장치에 업데이트 1(10.0.10296.0)을 설치합니다. 이 절차를 완료하는 데 약 15분이 걸립니다.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

설치가 완료된 후 StorSimple 장치 관리자 서비스로 이동합니다. **장치**를 선택한 후 방금 업데이트한 장치를 선택하고 클릭합니다. **설정 > 관리 > 장치 업데이트**로 이동합니다. 표시된 소프트웨어 버전은 **10.0.10296.0**이어야 합니다.

![업데이트 후 소프트웨어 버전](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>로컬 웹 UI 사용

로컬 웹 UI를 사용하는 경우 다음 두 단계로 구성됩니다.

* 업데이트 또는 핫픽스 다운로드
* 업데이트 또는 핫픽스 설치

> [!IMPORTANT] 
> **업데이트 0.6(10.0.10293.0)을 실행하는 경우에만 이 업데이트를 진행합니다. 이전 버전을 실행하는 경우 장치에 먼저 [업데이트 0.6을 설치](storsimple-virtual-array-install-update-06.md)한 후 업데이트 1을 적용합니다.**

### <a name="download-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 다운로드

가상 배열에서 업데이트 0.6을 실행 중인 경우 다음 단계를 수행하여 Microsoft 업데이트 카탈로그에서 업데이트 1을 다운로드합니다.

#### <a name="to-download-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스를 다운로드하려면

1. Internet Explorer를 시작하고 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)으로 이동합니다.

2. 이 컴퓨터에서 Microsoft 업데이트 카탈로그를 처음 사용하는 경우 Microsoft 업데이트 카탈로그 추가 기능을 설치하라는 메시지가 나타나면 **설치**를 클릭합니다.

3. Microsoft 업데이트 카탈로그의 검색 상자에 다운로드하려는 핫픽스의 KB(기술 자료) 번호를 입력합니다. 업데이트 1.0의 경우 **4047203**을 입력하고 **검색**을 클릭합니다.
   
    핫픽스 목록(예: **StorSimple 가상 배열 업데이트 1.0**)이 나타납니다.
   
    ![카탈로그 검색](./media/storsimple-virtual-array-install-update-1/download1.png)

4. **다운로드**를 클릭합니다.

5. 폴더에 두 개의 파일을 다운로드합니다. 장치에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.

6. 파일이 있는 폴더를 엽니다.

    ![패키지의 파일](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    두 개의 파일이 표시됩니다.
    -  Microsoft 업데이트 독립 실행형 패키지 파일 `WindowsTH-KB3011067-x64`. 이 파일은 장치 소프트웨어를 업데이트하는 데 사용됩니다.
    - 8월 `windows8.1-kb4034681-x64`에 대한 누적 업데이트를 포함하는 파일입니다. 이 롤업에 포함된 내용에 대한 자세한 내용은 [8월 월간 보안 롤업](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810)으로 이동하세요.

### <a name="install-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 설치

업데이트 또는 핫픽스를 설치하기 전에 다음을 확인합니다.

 - 업데이트 또는 핫픽스를 호스트에 로컬로 다운로드하거나 네트워크 공유를 통해 액세스할 수 있는지 확인합니다.
 - 가상 배열에서 업데이트 0.6(10.0.10293.0)을 실행하고 있습니다. 업데이트 0.6 이전 버전을 실행하고 있는 경우 먼저 [업데이트 0.6을 설치](storsimple-virtual-array-install-update-06.md)한 후 업데이트 1을 설치합니다.

이 절차를 완료하는 데 약 4분이 걸립니다. 다음 단계를 수행하여 업데이트 또는 핫픽스를 설치합니다.

#### <a name="to-install-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스를 설치하려면

1. 로컬 웹 UI에서 **유지 관리** > **소프트웨어 업데이트**로 이동합니다. 실행 중인 소프트웨어 버전을 기록해 둡니다. **업데이트 0.6(10.0.10293.0)을 실행하는 경우에만 이 업데이트를 진행합니다. 이전 버전을 실행하는 경우 장치에 먼저 [업데이트 0.6을 설치](storsimple-virtual-array-install-update-06.md)한 후 업데이트 1을 적용합니다.**
   
    ![장치 업데이트](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. **업데이트 파일 경로**에 업데이트 또는 핫픽스의 파일 이름을 입력합니다. 네트워크 공유에 있는 경우 업데이트 또는 핫픽스 설치 파일로 이동할 수 있습니다. **적용**을 클릭합니다.
   
    ![장치 업데이트](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. 경고가 표시됩니다. 가상 배열이 단일 노드 장치인 경우 업데이트가 적용된 후 장치를 다시 시작하고 가동 중지 시간이 발생합니다. 확인 아이콘을 클릭합니다.
   
   ![장치 업데이트](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. 업데이트가 시작됩니다. 장치가 성공적으로 업데이트된 후 다시 시작됩니다. 이 시간 동안 로컬 UI에 액세스할 수 없습니다.
   
    ![장치 업데이트](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. 다시 시작이 완료된 후 **로그인** 페이지가 열립니다. 로컬 웹 UI에서 장치 소프트웨어가 업데이트되었는지 확인하려면 **유지 관리** > **소프트웨어 업데이트**로 이동합니다. 표시된 소프트웨어 버전은 업데이트 1.0의 경우 **10.0.0.0.0.10296**이어야 합니다.
   
   > [!NOTE]
   > 로컬 웹 UI 및 Azure Portal에서 약간 다른 방법으로 소프트웨어 버전을 보고합니다. 예를 들어 같은 버전에 대해 로컬 웹 UI는 **10.0.0.0.0.10296**을, Azure Portal은 **10.0.10296.0**을 보고합니다.
   
    ![장치 업데이트](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. 2-4단계를 반복하여 `windows8.1-kb4012213-x64` 파일을 통해 Windows 보안 수정을 설치합니다. 설치 후 가상 배열이 다시 시작되고 로컬 웹 UI에 로그인해야 합니다.

> [!NOTE]
> 업데이트 0.6 이전 버전을 실행하는 장치에 업데이트 1을 직접 적용하는 경우 일부 업데이트가 없습니다. 다음 단계는 Microsoft 지원에 문의하세요.

## <a name="next-steps"></a>다음 단계

[StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)에 대해 자세히 알아봅니다.
