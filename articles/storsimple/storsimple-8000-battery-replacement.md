---
title: "Microsoft Azure StorSimple 8000 시리즈 장치의 배터리 교체 | Microsoft Docs"
description: "StorSimple 장치의 백업 배터리 모듈을 꺼내고 교체 및 유지 관리하는 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f8071cde67017ff031418f0d97da15a618c4969b
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2018
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>StorSimple 장치의 백업 배터리 모듈 교체

## <a name="overview"></a>개요
Microsoft Azure StorSimple 장치의 기본 엔클로저 PCM(전원 및 냉각 모듈)에는 추가 배터리 팩이 있습니다. 이 팩은 기본 엔클로저에 대한 AC 전원이 끊어질 경우 StorSimple 장치가 데이터를 저장할 수 있도록 전원을 공급합니다. 이 배터리 팩을 *백업 배터리 모듈*이라고 합니다. 백업 배터리 모듈은 StorSimple 장치의 기본 엔클로저에만 있습니다(EBOD 엔클로저에는 백업 배터리 모듈이 없음).

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

* 백업 배터리 모듈 꺼내기
* 새 백업 배터리 모듈 설치
* 백업 배터리 모듈 유지 관리

> [!IMPORTANT]
> 백업 배터리 모듈을 꺼내고 교체하기 전에 [StorSimple 하드웨어 구성 요소 교체 소개](storsimple-8000-hardware-component-replacement.md)에서 안전 정보를 검토하세요.


## <a name="remove-the-backup-battery-module"></a>백업 배터리 모듈 꺼내기
StorSimple 장치에 대한 백업 배터리 모듈은 FRU(현장 교체 장치)입니다. PCM에 설치하기 전에는 원래 포장 안에 배터리 모듈을 보관해야 합니다. 백업 배터리를 꺼내려면 다음 단계를 수행합니다.

#### <a name="to-remove-the-backup-battery-module"></a>백업 배터리 모듈을 꺼내려면
1. Azure Portal에서 StorSimple 장치 관리자 서비스 블레이드로 이동합니다. **장치**로 이동한 후 장치 목록에서 장치를 선택합니다. **모니터** > **하드웨어 상태**로 이동합니다. **공유 구성 요소**아래에서 배터리 상태를 확인합니다.
2. 배터리에서 오류가 발생한 PCM을 식별합니다. 그림 1은 StorSimple 장치의 뒷면의 보여 줍니다.
   
    ![장치 기본 엔클로저 모듈의 백플레인](./media/storsimple-battery-replacement/IC740994.png)
   
    **그림 1** PCM 및 컨트롤러 모듈을 표시하는 기본 장치 뒷면
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |컨트롤러 0 |
   | 4 |컨트롤러 1 |
   
    그림 2의 번호 3과 같이 **배터리 결함** 에 해당하는 PCM 0의 모니터링 표시기 LED가 켜져야 합니다.
   
    ![장치 PCM 모니터링 표시기 LED의 백플레인](./media/storsimple-battery-replacement/IC740992.png)
   
    **그림 2** 모니터링 표시기 LED를 표시하는 PCM 뒷면
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |AC 전원 오류 |
   | 2 |팬 오류 |
   | 3 |배터리 결함 |
   | 4 |PCM 정상 |
   | 5 |DC 전원 오류 |
   | 6 |배터리 정상 |
3. 오류가 발생한 배터리가 있는 PCM을 꺼내려면 [PCM 꺼내기](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm)의 단계를 따르세요.
4. PCM을 꺼낸 후 다음 그림과 같이 배터리 모듈 핸들을 들고 위로 회전해서 당겨 배터리를 꺼냅니다.
   
    ![PCM에서 배터리 꺼내기](./media/storsimple-battery-replacement/IC741019.png)
   
    **그림 3** PCM에서 배터리 꺼내기
5. FRU(현장 교체 장치) 포장 안에 모듈을 넣습니다.
6. 적절한 서비스 및 처리를 위해 결함이 있는 장치를 Microsoft에 반환합니다.

## <a name="install-a-new-backup-battery-module"></a>새 백업 배터리 모듈 설치
StorSimple 장치의 기본 엔클로저의 PCM에 교체 배터리 모듈을 설치하려면 다음 단계를 따르세요.

#### <a name="to-install-the-battery-module"></a>배터리 모듈을 설치하려면
1. 백업 배터리 모듈을 PCM에 올바른 방향으로 놓습니다.
2. 배터리 모듈 핸들을 아래로 완전히 눌러 커넥터를 장착합니다.
3. [StorSimple 장치의 전원 및 냉각 모듈 교체](storsimple-8000-power-cooling-module-replacement.md)의 지침에 따라 기본 엔클로저의 PCM을 교체합니다.
4. 바꾸기가 완료되면 Azure Portal에서 장치로 이동한 후 **모니터** > **하드웨어 상태**로 이동합니다. 설치가 성공적으로 수행되었는지 확인하려면 배터리 상태를 확인합니다. 녹색 상태는 배터리가 정상임을 나타냅니다.

## <a name="maintain-the-backup-battery-module"></a>백업 배터리 모듈 유지 관리
StorSimple 장치에서 백업 배터리 모듈은 전원 손실 이벤트가 발생하는 동안 컨트롤러에 전원을 공급합니다. StorSimple 장치가 종료되기 전에 제어된 방식으로 중요한 데이터를 저장할 수 있게 합니다. PCM에 완전히 충전된 두 개의 배터리가 있으면 시스템이 두 번의 연속된 손실 이벤트를 처리할 수 있습니다.

Azure Portal에서 **모니터** 블레이드의 **하드웨어 상태**는 배터리가 오작동하는지 또는 사용 기간 종료가 임박하는지 여부를 나타냅니다. 배터리 상태는 **공유 구성 요소**에서 **PCM 0의 배터리** 또는 **PCM 1의 배터리**로 표시됩니다. 이 블레이드에서 사용 기간 종료가 임박한 경우 **DEGRADED** 상태가 표시되고 사용 기간이 종료된 경우 **FAILED** 상태가 표시됩니다.

> [!NOTE]
> 단순히 충전이 필요한 경우 배터리에서 **FAILED** 를 보고할 수 있습니다.


**DEGRADED** 상태가 표시되는 경우 다음 작업을 수행하는 것이 좋습니다.

* 시스템에서 최근에 전원 손실이 발생했거나 배터리 정기 유지 관리가 진행 중일 수 있습니다. 계속 진행하기 전에 12시간 동안 시스템을 관찰합니다.
  
  * 컨트롤러와 PCM을 실행하여 AC 전원에 12시간 동안 연속해서 연결된 후에도 상태가 여전히 **DEGRADED** 이면 배터리를 교체해야 합니다. 교체 백업 배터리 모듈에 대해서는 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md) 하세요.
  * 12시간 후 정상 상태가 되면 배터리가 작동하는 것이며 유지 관리 충전만 하면 됩니다.
* 연결된 AC 전원 손실이 없으며 PCM이 켜져 있고 AC 전원에 연결된 경우 배터리를 교체해야 합니다. [Contact Microsoft Support](storsimple-8000-contact-microsoft-support.md) 하세요.

> [!IMPORTANT]
> 국가 및 지역 규정에 따라 오류가 발생한 배터리를 폐기합니다.

## <a name="next-steps"></a>다음 단계
[StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에 대해 자세히 알아봅니다.

