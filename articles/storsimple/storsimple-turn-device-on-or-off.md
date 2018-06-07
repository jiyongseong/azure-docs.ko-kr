---
title: "StorSimple 8000 시리즈 장치 켜기 또는 끄기 | Microsoft Docs"
description: "새 StorSimple 장치를 켜고, 종료되었거나 전원이 손실된 장치를 켜고, 실행 중인 장치를 끄는 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95fd00608be9cfafb4c703c32ec3ed4713855ca5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 장치 켜기 또는 끄기

## <a name="overview"></a>개요
Microsoft Azure StorSimple 장치 종료는 정상적인 시스템 작업의 일환으로 필요하지 않습니다. 그러나 새 장치 또는 종료할 장치의 전원을 켜야 할 수도 있습니다. 일반적으로 오류가 발생한 하드웨어를 교체, 물리적으로 장치를 이동하거나 장치의 서비스를 중단해야 하는 경우 종료가 필요합니다. 이 자습서는 다양한 시나리오에서 StorSimple 장치를 켜고 종료하는데 필요한 절차에 대해 설명합니다.

## <a name="turn-on-a-new-device"></a>새 장치 켜기
처음으로 StorSimple 장치를 켜는 단계는 장치가 8100인지 8600 모델인지에 따라 다릅니다. 8100에는 단일 기본 엔클로저가 있는 반면 8600은 기본 엔클로저와 EBOD 엔클로저가 있는 이중 엔클로저 장치입니다. 두 모델에 대한 자세한 단계는 다음 섹션에서 다룹니다.

* [기본 인클로저만 있는 새 장치](#new-device-with-primary-enclosure-only)
* [EBOD 인클로저가 있는 새 장치](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>기본 인클로저만 있는 새 장치
StorSimple 8100 모델은 단일 인클로저 장치입니다. 장치에는 예비 전원 및 냉각 모듈(PCM)이 포함됩니다. 두 PCM을 모두 설치하고 다른 전원 공급 장치에 연결하여 높은 가용성을 보장해야 합니다.

다음 단계를 수행하여 케이블로 장치를 전원에 연결하세요.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> 장치 설정 완료 및 케이블 연결 지침은 [StorSimple 8100 장치 설치](storsimple-8100-hardware-installation.md)로 이동합니다. 지침을 정확하게 따르고 있는지 확인합니다.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>EBOD 인클로저가 있는 새 장치
StorSimple 8600 모델에는 기본 인클로저 및 EBOD 인클로저가 있습니다. 장치를 Serial Attached SCSI(SAS) 연결 및 전원에 모두 케이블 연결해야 합니다.

처음으로 이 장치를 설정할 때 SAS 케이블 연결을 먼저 수행한 다음 전원 케이블 연결에 대한 단계를 완료합니다.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> 장치 설정 완료 및 케이블 연결 지침은 [StorSimple 8600 장치 설치](storsimple-8600-hardware-installation.md)로 이동합니다. 지침을 정확하게 따르고 있는지 확인합니다.

## <a name="turn-on-a-device-after-shutdown"></a>종료 후 장치 켜기
StorSimple 장치가 종료된 후에 켜는 단계는 장치가 8100인지 8600 모델인지에 따라 다릅니다. 8100에는 단일 기본 엔클로저가 있는 반면 8600은 기본 엔클로저와 EBOD 엔클로저가 있는 이중 엔클로저 장치입니다.

* [기본 인클로저만 있는 장치](#device-with-primary-enclosure-only)
* [EBOD 인클로저가 있는 장치](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>기본 인클로저만 있는 장치
종료 후 기본 인클로저가 있고 EBOD 인클로저가 없는 StorSimple 장치를 켜려면 다음 절차를 따릅니다.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>기본 인클로저만 있는 장치 켜기
1. 두 PCM(전원 및 냉각 모듈)의 전원 스위치가 OFF 위치에 있는지 확인합니다. 스위치가 OFF 위치에 없는 경우 OFF 위치로 밀고 표시등이 꺼질 때까지 대기합니다.
2. 양쪽 PCM 상의 전원 스위치를 ON 위치로 밀어 장치의 전원을 켭니다. 장치를 켜야 합니다.
3. 다음을 확인하여 장치가 완전히 켜졌는지 확인합니다.
   
   1. 두 PCM 모듈의 OK LED가 녹색입니다.
   2. 두 컨트롤러의 상태 LED가 녹색으로 고정됩니다.
   3. 컨트롤러 중 하나의 파란색 LED가 깜박이는 것은 컨트롤러 활성화되어 있음을 나타냅니다.
      
      이러한 조건 중 하나라도 충족되지 않은 경우 장치가 정상이 아닙니다. [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요.

### <a name="device-with-ebod-enclosure"></a>EBOD 인클로저가 있는 장치
종료 후 기본 인클로저와 EBOD 인클로저가 있는 StorSimple 장치를 켜려면 다음 절차를 따릅니다. 설명한대로 정확하게 각 단계를 순서대로 수행합니다. 이렇게 하지 않으면 데이터가 손실될 수 있습니다.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>기본 및 EBOD 인클로저가 있는 장치 켜기
1. EBOD 인클로저가 기본 인클로저에 연결되었는지 확인합니다. 자세한 내용은 [StorSimple 8600 장치 설치](storsimple-8600-hardware-installation.md)를 참조하세요.
2. EBOD 및 기본 인클로저의 PCM(전원 및 냉각 모듈)이 OFF 위치에 있는지 확인합니다. 스위치가 OFF 위치에 없는 경우 OFF 위치로 밀고 표시등이 꺼질 때까지 대기합니다.
3. 양쪽 PCM 상의 전원 스위치를 ON 위치로 밀어 EBOD 인클로저의 전원을 먼저 켭니다. PCM LED가 녹색이어야 합니다. 이 장치의 녹색 EBOD 컨트롤러 LED는 EBOD 인클로저가 켜져 있음을 나타냅니다.
4. 양쪽 PCM 상의 전원 스위치를 ON 위치로 밀어 기본 인클로저의 전원을 켭니다. 이제 전체 시스템이 켜져 있어야 합니다.
5. EBOD 인클로저 및 기본 인클로저 간의 연결이 좋은지를 나타내는 SAS LED가 녹색인지 확인합니다.

## <a name="turn-on-a-device-after-a-power-loss"></a>전원 손실 후 장치 켜기
정전 또는 전원 중단으로 StorSimple 장치가 종료될 수 있습니다. 전원 공급 장치 중 하나 또는 두 전원 공급 장치에서 정전이 발생할 수 있습니다. 복구 단계는 장치가 8100 또는 8600 모델인지 여부에 따라 다릅니다. 8100에는 단일 기본 엔클로저가 있는 반면 8600은 기본 엔클로저와 EBOD 엔클로저가 있는 이중 엔클로저 장치입니다. 이 섹션에서는 각 시나리오에 대한 복구 절차를 설명합니다.

* [기본 인클로저만 있는 장치](#8100)
* [EBOD 인클로저가 있는 장치](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>기본 인클로저만 있는 장치 <a name="8100">
전원 공급 장치 중 하나에 전원 손실이 발생하는 경우 시스템은 일반적인 작업을 계속할 수 있습니다. 하지만 장치의 고가용성을 보장하려면 가능한 빨리 전원 공급 장치에 전원을 복원하십시오.

전원 공급 장치 모두에서 정전 또는 전원 중단이 있는 경우 시스템이 순차적이고 제어된 방식으로 종료됩니다. 전원이 복원되면 시스템이 자동으로 켜집니다.

### <a name="device-with-ebod-enclosure-a-name8600"></a>EBOD 인클로저가 있는 장치 <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>하나의 전원 공급 장치에서 전원 손실
기본 인클로저 또는 EBOD 인클로저의 전원 공급 장치 중 하나에 전원 손실이 발생하는 경우 시스템은 일반적인 작업을 계속할 수 있습니다. 하지만 장치의 고가용성을 보장하려면 가능한 빨리 전원 공급 장치에 전원을 복원하십시오.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>기본 및 EBOD 인클로저의 두 전원 공급 장치에서 전원 손실
전원 공급 장치 모두에서 정전 또는 전원 중단이 있는 경우 EBOD 인클로저는 즉시 종료되고 기본 인클로저는 순차적이고 제어된 방식으로 종료됩니다. 전원이 복원되면 기기가 자동으로 시작됩니다.

전원이 수동으로 꺼진 경우 다음 단계를 따라 시스템의 전원을 복원합니다.

1. EBOD 인클로저를 켭니다.
2. EBOD 인클로저의 전원이 켜진 후 기본 인클로저를 켭니다.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>EBOD 인클로저의 두 전원 공급 장치에서 전원 손실
케이블을 설정할 때 EBOD가 별도 PDU에 단독 연결되지 않도록 확인해야 합니다. EBOD 및 기본 인클로저가 동시에 실패하는 경우 시스템이 복구됩니다.

EBOD 인클로저의 두 전원 공급 장치가 실패한 경우 시스템이 자동으로 복구되지 않습니다. 다음 단계를 수행하여 시스템의 전원을 켜고 정상 상태로 복원합니다.

1. 기본 인클로저가 켜져 있는 경우 두 PCM(전원 및 냉각 모듈)을 모두 끕니다.
2. 시스템이 종료될 때까지 몇 분 정도 기다립니다.
3. EBOD 인클로저를 켭니다.
4. EBOD 인클로저의 전원이 켜진 후 기본 인클로저를 켭니다.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>기본 및 EBOD 인클로저 연결 손실 후 장치 켜기
대기 컨트롤러와 해당 EBOD 컨트롤러 간의 연결이 끊어진 경우 장치가 계속 작동합니다. 시스템 활성 컨트롤러와 해당 EBOD 컨트롤러 간의 연결이 끊어진 경우 장애 조치가 발생해야 하며 장치는 계속 정상적으로 작동해야 합니다.

두 SAS(Serial Attached SCSI) 케이블이 제거되거나 EBOD 인클로저와 기본 인클로저 간의 연결이 단절되는 경우 장치의 작동이 중지됩니다. 이때 다음 단계를 수행합니다.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>연결이 끊어진 후 장치를 켜려면
1. 장치의 뒷면에 액세스합니다.
2. EBOD 인클로저와 기본 인클로저 간의 SAS 케이블 연결이 중단되는 경우 EBOD 인클로저의 모든 SAS 레인 LED가 꺼집니다.
3. EBOD 인클로저 및 기본 인클로저의 두 PCM(전원 및 냉각 모듈)을 종료합니다.
4. 두 인클로저의 뒷면에 있는 모든 표시등이 꺼질 때까지 기다립니다.
5. SAS 케이블을 다시 삽입하고 EBOD 인클로저와 기본 인클로저 간의 연결이 양호한지 확인합니다.
6. 두 PCM 스위치를 ON 위치로 밀어 EBOD 인클로저의 전원을 먼저 켭니다.
7. 녹색 LED가 ON인지 확인하여 EBOD 인클로저가 켜졌는지 확인합니다.
8. 기본 인클로저를 켭니다.
9. 컨트롤러 녹색 LED가 ON인지 확인하여 기본 인클로저가 켜졌는지 확인합니다.
10. SAS 레인 LED(EBOD 컨트롤러당 4개)가 모두 ON인지 확인하여 EBOD 인클로저와 기본 인클로저의 연결이 양호한지 확인합니다.

> [!IMPORTANT]
> SAS 케이블이 결함이 있거나 EBOD 인클로저와 기본 인클로저 간의 연결이 좋지 않은 경우 시스템을 켜면 복구 모드로 전환됩니다. 이 경우 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md) 하십시오.


## <a name="turn-off-a-running-device"></a>실행 중인 장치 끄기
실행 중인 StorSimple 장치를 이동 중이거나 서비스를 받지 못하거나 교체가 필요한 오작동하는 구성 요소가 있는 경우 종료해야 합니다. 단계는 StorSimple 장치가 8100 또는 8600 모델인지 여부에 따라 다릅니다. 8100에는 단일 기본 엔클로저가 있는 반면 8600은 기본 엔클로저와 EBOD 엔클로저가 있는 이중 엔클로저 장치입니다. 이 섹션에서는 실행 중인 장치를 종료하는 단계를 자세히 설명합니다.

* [기본 인클로저가 있는 장치](#8100a)
* [EBOD 인클로저가 있는 장치](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>기본 인클로저가 있는 장치 <a name="8100a">
제어된 방식으로 올바른 순서에 따라 장치를 종료하려는 경우 Azure Portal 또는 StorSimple용 Windows PowerShell을 통해 종료할 수 있습니다. 

> [!IMPORTANT]
> 장치 뒷면의 전원 단추를 사용하여 실행 중인 장치를 종료하지 마십시오.
> 
> 장치를 종료하기 전에 모든 장치 구성 요소가 올바르게 작동하는지 확인합니다. Azure Portal에서 **장치** > **모니터** > **하드웨어 상태**로 이동한 다음, 모든 구성 요소의 상태가 녹색인지 확인합니다. 양호한 시스템에 대해서만 적용됩니다. 시스템이 종료되어 작동하지 않는 구성 요소를 교체하는 경우 **하드웨어 상태**의 해당 구성 요소에 실패(빨간색) 또는 성능 저하(노란색) 상태가 표시됩니다.
> 
> 

StorSimple용 Windows PowerShell 또는 Azure Portal에 액세스한 후 [StorSimple 장치 종료](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device)의 단계를 수행합니다. 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>EBOD 인클로저가 있는 장치 <a name="8600a">
> [!IMPORTANT]
> 기본 인클로저 및 EBOD 인클로저를 종료 기 전에 모든 장치 구성 요소가 정상인지 확인합니다. Azure Portal에서 **장치** > **모니터** > **하드웨어 상태**로 이동한 다음, 모든 구성 요소의 상태가 정상인지 확인합니다.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>EBOD 인클로저가 있는 실행 중인 장치를 종료하려면
1. 기본 엔클로저에 대해 [StorSimple 장치 종료](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) 에 나와 있는 모든 단계를 수행합니다.
2. 기본 인클로저가 종료된 후 두 PCM(전원 및 냉각 모듈) 스위치를 꺼 EBOD를 종료합니다.
3. EBOD가 종료되었는지 확인하려면 EBOD 인클로저의 뒷면에 있는 모든 표시등이 꺼졌는지 확인합니다.

> [!NOTE]
> EBOD 인클로저를 기본 인클로저에 연결하는데 사용되는 SAS 케이블을 시스템이 종료된 후까지 제거하면 안됩니다.

## <a name="next-steps"></a>다음 단계
[Contact Microsoft Support](storsimple-8000-contact-microsoft-support.md) 하십시오.

