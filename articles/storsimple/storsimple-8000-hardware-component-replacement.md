---
title: "StorSimple 8000 시리즈 하드웨어 구성 요소 교체 | Microsoft Docs"
description: "StorSimple 장치의 PCM, 배터리, 컨트롤러 모듈, EBOD 컨트롤러, 디스크 드라이브 및 섀시를 안전하게 교체하는 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: 
ms.openlocfilehash: 6de50c5031db59176bdf17ecc69b934559220f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 장치에서 하드웨어 구성 요소 교체

## <a name="overview"></a>개요
하드웨어 구성 요소 교체 자습서에서는 Microsoft Azure StorSimple 8000 시리즈 장치의 하드웨어 구성 요소 및 구성 요소를 꺼내고 교체하는 데 필요한 단계를 설명합니다. 이 문서에서는 안전 아이콘을 설명하고, 자세한 자습서에 대한 포인터를 제공하고, 교체 가능한 구성 요소를 나열합니다.

> [!IMPORTANT]
> StorSimple 구성 요소를 제거하거나 교체하기 전에 [안전성 아이콘 표시 규칙](#safety-icon-conventions) 및 기타 [안전 주의 사항](storsimple-safety.md)을 검토해야 합니다.


### <a name="safety-icon-conventions"></a>안전성 아이콘 표시 규칙
다음 표에서는 이러한 자습서에서 사용된 안전성 아이콘에 대해 설명합니다. 장치 구성 요소를 꺼내고 교체하는 단계를 진행할 때 이러한 안전성 아이콘에 각별히 주의하세요.

| 아이콘 | 텍스트 | 추가 정보 |
|:--- |:--- |:--- |
| ![경고 아이콘](./media/storsimple-hardware-component-replacement/Warning.png) |**위험!** |피하지 않을 경우 사망 또는 심각한 부상을 당하는 위험한 상황을 나타냅니다. 이 위험도 표시는 가장 극단적인 상황으로 제한됩니다. |
| ![경고 아이콘](./media/storsimple-hardware-component-replacement/Warning.png) |**경고!** |피하지 않을 경우 사망 또는 심각한 부상을 당할 수 있는 위험한 상황을 나타냅니다. |
| ![주의 아이콘](./media/storsimple-hardware-component-replacement/Caution.png) |**주의!** |피하지 않을 경우 최소 또는 보통 수준의 부상을 당할 수 있는 위험한 상황을 나타냅니다. |
| ![고지 아이콘](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**고지:** |중요하지만 위험과 관련되지 않은 것으로 간주되는 정보를 나타냅니다. |
| ![감전 아이콘](./media/storsimple-hardware-component-replacement/Electric.png) |**감전 위험** |높은 전압을 나타냅니다. |
| ![무거운 무게 아이콘](./media/storsimple-hardware-component-replacement/Weight.png) |**무거운 무게** | |
| ![사용자 서비스 가능 부품 없음 아이콘](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**사용자 서비스 가능 부품 없음** |제대로 교육을 받지 않은 경우 액세스하지 마세요. |
| ![지침 읽기 아이콘](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**먼저 모든 지침 읽기** | |
| ![기울어짐 위험 아이콘](./media/storsimple-hardware-component-replacement/TipHazard.png) |**기울어짐 위험** | |

### <a name="before-you-begin"></a>시작하기 전에
이 자습서에서 사용된 안전성 아이콘 및 장치에 대한 안전성 정보를 숙지합니다. 자세한 내용을 보려면 [StorSimple 장치의 안전한 설치 및 작동](storsimple-safety.md) 으로 이동합니다. StorSimple 장치를 처리하기 전에 [안전 주의 사항](storsimple-safety.md#handling-precautions) 을 검토해야 합니다.

구성 요소를 교체하기 전에 다음 정보를 고려하세요.

![Warning Icon](./media/storsimple-hardware-component-replacement/Warning.png) ![Electrical Shock Icon](./media/storsimple-hardware-component-replacement/Electric.png) **경고!**

* StorSimple 장치의 모듈 및 구성 요소를 처리할 때 정전기 방전 또는 정전기 방지 매트를 사용하여 올바르게 접지합니다.
* 회로를 만지지 마세요. 노출된 회로가 있을 수 있는 구성 요소를 처리할 때는 제공된 핸들 및 가이드를 사용합니다.

![Warning Icon](./media/storsimple-hardware-component-replacement/Warning.png) ![Notice Icon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **고지:**

모듈을 교체하는 경우 **엔클로저 뒷면에 빈 베이를 남기지 마세요**. 문제 부품을 꺼내기 전에 교체 또는 빈 모듈을 구합니다.

## <a name="hardware-component-replacement-procedures"></a>하드웨어 구성 요소 교체 절차
StorSimple 8000 시리즈 장치는 기본 및/또는 EBOD 엔클로저의 여러 플러그 인 모듈로 구성되어 있습니다. 8100에는 단일 기본 엔클로저가 있는 반면 8600은 기본 엔클로저와 EBOD 엔클로저가 있는 이중 엔클로저 장치입니다.

장치의 기본 하드웨어 구성 요소는 다음 표에 요약되어 있습니다. **교체 절차** 열에 있는 링크를 클릭하면 연결된 자습서로 이동합니다.

| 구성 요소 | 현재 개수 | 플러그 인 모듈 여부 | 교체 절차 |
|:--- |:--- |:--- |:--- |
| 섀시 |1 |아니요 |[StorSimple 장치의 섀시 교체](storsimple-8000-chassis-replacement.md) |
| 기본 컨트롤러 |2 |예 |[StorSimple 장치의 컨트롤러 모듈 교체](storsimple-8000-controller-replacement.md) |
| 764W PCM(전원 및 냉각 모듈) |2 |예 |[StorSimple 장치의 전원 및 냉각 모듈 교체](storsimple-8000-power-cooling-module-replacement.md) |
| 백업 배터리 |2 |예 |[StorSimple 장치의 백업 배터리 모듈 교체](storsimple-8000-battery-replacement.md) |
| 디스크 드라이브 |12 |예 |[StorSimple 장치의 디스크 드라이브 교체](storsimple-8000-disk-drive-replacement.md) |

**표 1** 기본 엔클로저의 하드웨어 구성 요소

기본 엔클로저와 EBOD 엔클로저는 해당 I/O 모듈이 서로 다릅니다. 또한 PCM의 전력량이 서로 다릅니다. 기본 엔클로저의 PCM은 764W인 반면, EBOD 엔클로저의 PCM은 580W입니다. 기본 엔클로저의 PCM에는 백업 배터리 모듈도 포함되어 있습니다.

| 구성 요소 | 현재 개수 | 플러그 인 모듈 여부 | 교체 절차 |
|:--- |:--- |:--- |:--- |
| 섀시 |1 |아니요 |[StorSimple 장치의 섀시 교체](storsimple-8000-chassis-replacement.md) |
| EBOD 컨트롤러 |2 |예 |[StorSimple 장치의 EBOD 컨트롤러 교체](storsimple-8000-ebod-controller-replacement.md) |
| 580W PCM(전원 및 냉각 모듈) |2 |예 |[StorSimple 장치의 전원 및 냉각 모듈 교체](storsimple-8000-power-cooling-module-replacement.md) |
| 디스크 드라이브 |12 |예 |[StorSimple 장치의 디스크 드라이브 교체](storsimple-8000-disk-drive-replacement.md) |

**표 2** EBOD 엔클로저의 하드웨어 구성 요소

장치의 플러그 인 모듈은 다음 앞면 및 뒷면 다이어그램에서 강조 표시되어 있습니다. 교체가 필요한 경우 이러한 다이어그램을 사용하여 다양한 플러그 인 모듈의 위치를 확인할 수 있습니다. 앞면 다이어그램에는 디스크 드라이브가 표시되고, EBOD 엔클로저 및 기본 엔클로저의 뒷면 다이어그램에는 플러그 인 모듈이 표시됩니다.

![디스크 드라이브가 있는 장치의 프런트플레인](./media/storsimple-hardware-component-replacement/IC741028.png)

**그림 1** 장치 앞면

| 레이블 | 설명 |
|:--- |:--- |
| 0 - 11 |디스크 드라이브(총 12개) |

기본 엔클로저와 EBOD 엔클로저에 모두 드라이브 캐리어 모듈이 있습니다. 섀시에는 3.5" 디스크 드라이브 12개가 3X4 형식으로 정렬되어 있습니다.

![장치 기본 엔클로저 모듈의 백플레인](./media/storsimple-hardware-component-replacement/IC740994.png)

**그림 2** 기본 엔클로저 뒷면

| 레이블 | 설명 |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |컨트롤러 0 |
| 4 |컨트롤러 1 |

![장치 EBOD 엔클로저 플러그 인 모듈의 백플레인](./media/storsimple-hardware-component-replacement/IC769599.png)

**그림 3** EBOD 엔클로저 뒷면

| 레이블 | 설명 |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD 컨트롤러 0 |
| 4 |EBOD 컨트롤러 1 |

## <a name="field-replaceable-units"></a>FRU(필드 교체 장치)
StorSimple 장치에 사용할 수 있는 FRU(필드 교체 장치)는 다음과 같습니다.

* 섀시(통합 작업 패널 포함)
* 764 W AC PCM
* 580 W AC PCM
* 드라이브 캐리어 모듈이 있는 하드 디스크 드라이브
* 컨트롤러 모듈
* EBOD 컨트롤러 모듈
* 백업 배터리 모듈
* 랙 탑재 레일 키트

교체 장치를 주문하려면 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md) 하세요.

## <a name="next-steps"></a>다음 단계
StorSimple 하드웨어 구성 요소를 교체하기 전에 모든 [안전 정보](storsimple-safety.md) 를 검토하세요.

