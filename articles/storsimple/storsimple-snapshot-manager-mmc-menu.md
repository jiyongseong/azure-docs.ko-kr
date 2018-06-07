---
title: "StorSimple Snapshot Manager MMC 메뉴 작업 | Microsoft Docs"
description: "StorSimple 스냅숏 관리자에서 표준 Microsoft Management Console(MMC) 메뉴 작업을 사용하는 방법에 대해 설명합니다."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 48f439a566a8067e153aab4fb789937d2f91268d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>StorSimple 스냅숏 관리자에서 MMC 메뉴 작업 사용

## <a name="overview"></a>개요
StorSimple 스냅숏 관리자에서 모든 작업 메뉴에 나열된 다음과 같은 작업 및 **작업** 창의 모든 변형을 확인할 수 있습니다.

* 보기
* 여기에서 창 새로 만들기 
* 새로 고침 
* 목록 내보내기 
* 도움말 

이러한 작업은 Microsoft Management Console(MMC)의 일부로, StorSimple 스냅숏 관리자 특정 메뉴가 아닙니다. 이 자습서에서는 이러한 작업 및 StorSimple 스냅숏 관리자에서 각 작업을 사용하는 방법에 대해 설명합니다.

## <a name="view"></a>보기
**보기** 옵션을 사용하면 **결과** 창 보기와 콘솔 창 보기를 변경할 수 있습니다. 

#### <a name="to-change-the-results-pane-view"></a>결과 창 보기를 변경하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창의 항목을 마우스 오른쪽 단추로 클릭한 다음 **보기** 옵션을 클릭합니다. 
3. **결과** 창에 표시된 열을 추가하거나 제거하려면 **열 추가/제거**를 클릭합니다. **열 추가/제거** 대화 상자가 나타납니다.
   
    ![결과 창에 열 추가 또는 제거](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. 다음과 같이 양식을 완료합니다.
   
   * **사용 가능** 열 목록에서 항목을 선택하고 **추가**를 클릭하여 **표시된 열** 목록에 추가합니다. 
   * **표시된 열** 목록에서 항목을 클릭하고 **제거**를 클릭하여 목록에서 제거합니다. 
   * **표시된** 열 목록에서 항목을 선택하고 **위로 이동** 또는 **아래로 이동**을 클릭하여 항목을 목록에서 위 또는 아래로 이동합니다. 
   * **기본값 복원**을 클릭하여 기본 **결과** 창 구성으로 돌아갑니다. 
5. 선택이 끝나면 **확인**을 클릭합니다. 

#### <a name="to-change-the-console-window-view"></a>콘솔 창 보기를 변경하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 노드를 마우스 오른쪽 단추로 클릭하고 **보기**를 클릭한 다음 **사용자 지정**을 클릭합니다. **사용자 지정** 대화 상자가 나타납니다.
   
    ![콘솔 창 사용자 지정](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. 확인란을 선택하거나 선택 취소하여 콘솔 창에 항목을 표시하거나 숨깁니다. 선택이 끝나면 **확인**을 클릭합니다.

## <a name="new-window-from-here"></a>여기에서 창 새로 만들기
**여기에서 창 새로 만들기** 옵션을 사용하여 새 콘솔 창을 열 수 있습니다.

#### <a name="to-open-a-new-console-window"></a>새 콘솔 창을 열려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 노드를 마우스 오른쪽 단추로 클릭하고 **여기에서 창 새로 만들기**를 클릭합니다. 
   
    선택한 범위만 표시된 새 창이 나타납니다. 예를 들어 **백업 정책** 노드를 마우스 오른쪽 단추로 클릭한 경우 새 창에는 **범위** 창의 **백업 정책** 노드 및 **결과** 창에 정의된 백업 정책 목록만 표시됩니다. 다음 예제를 참조하세요.
   
    ![여기에서 창 새로 만들기](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>새로 고침
**새로 고침** 작업을 사용하여 콘솔 창을 업데이트할 수 있습니다.

#### <a name="to-update-the-console-window"></a>콘솔 창을 업데이트하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창의 항목을 마우스 오른쪽 단추로 클릭한 다음 **새로 고침**을 클릭합니다. 

## <a name="export-list"></a>목록 내보내기
**목록 내보내기** 작업을 사용하면 쉼표로 구분한 값(CSV) 파일로 목록을 저장할 수 있습니다. 예를 들어 백업 정책 또는 백업 카탈로그 목록을 내보낼 수 있습니다. 그런 다음 분석을 위해 CSV 파일을 스프레드시트 응용 프로그램으로 가져올 수 있습니다.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>쉼표로 구분한 값(CSV) 파일로 목록을 저장하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다. 
2. **범위** 창에서 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창의 항목을 마우스 오른쪽 단추로 클릭한 다음 **목록 내보내기**를 클릭합니다. 
3. **목록 내보내기** 대화 상자가 나타납니다. 다음과 같이 양식을 완료합니다. 
   
   1. **파일 이름** 상자에 CSV 파일의 이름을 입력하거나 화살표를 클릭하여 드롭다운 목록에서 선택합니다.
   2. **파일 형식** 상자에서 화살표를 클릭하고 드롭다운 목록에서 파일 형식을 선택합니다.
   3. 선택한 항목만 저장하려면 원하는 행들을 선택한 다음 **선택한 행만 저장** 확인란을 클릭합니다. 내보낸 목록을 모두 저장하려면 **선택한 행만 저장** 확인란의 선택을 취소합니다.
   4. **저장**을 클릭합니다.
      
      ![쉼표로 구분된 값 파일로 목록 내보내기](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>도움말
**도움말** 메뉴를 사용하여 StorSimple 스냅숏 관리자 및 MMC에 사용 가능한 온라인 도움말을 볼 수 있습니다.

#### <a name="to-view-available-online-help"></a>사용 가능한 온라인 도움말을 보려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다.
2. **범위** 창에서 노드를 마우스 오른쪽 단추로 클릭하거나 노드를 확장하고 **결과** 창의 항목을 마우스 오른쪽 단추로 클릭한 다음 **도움말**을 클릭합니다. 

## <a name="next-steps"></a>다음 단계
* [StorSimple 스냅숏 관리자 사용자 인터페이스](storsimple-use-snapshot-manager.md)에 대해 자세히 알아봅니다.
* [StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 자세히 알아봅니다.

