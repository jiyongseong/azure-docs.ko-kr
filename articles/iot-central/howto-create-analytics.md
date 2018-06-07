---
title: Azure IoT Central 응용 프로그램을 위한 사용자 지정 분석 만들기 | Microsoft Docs
description: 운영자로서 Azure IoT Central 응용 프로그램을 위한 사용자 지정 분석을 만드는 방법을 알아봅니다.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 2f19998429daab9000e35b520673c417b4bc828a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199938"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>분석을 사용하여 장치 데이터를 분석하는 방법

Microsoft Azure IoT Central은 장치에서 오는 대량의 데이터를 이해하는 풍부한 분석 기능을 제공합니다. 분석을 사용하여 응용 프로그램의 [장치 집합](howto-use-device-sets.md)에 대한 데이터를 살펴보고 분석할 수 있습니다. 장치 집합은 사용자 정의 장치 그룹입니다. 장치 범위를 소규모 장치 집합 또는 단일 장치로 좁힐 수 있습니다.

운영자로서 왼쪽 탐색 메뉴에서 **분석**을 선택하고, 장치 집합을 선택하고, 그래프에 표시할 측정값을 선택합니다. 다음은 데이터를 추가로 분할하는 데 사용할 수는 몇 가지 도구입니다.

* **측정값:** 온도, 습도 등 표시할 측정값을 선택합니다.
* **집계:** 측정값에 대한 집계 함수를 선택합니다. 예를 들어 **최소** 또는 **평균**을 선택합니다.
* **분할 기준:** 장치 속성 또는 장치 이름을 기준으로 데이터를 분할하여 드릴다운합니다. 예를 들어 장치 위치에 따라 분할합니다.

     ![분석 기본 페이지](media\howto-create-analytics\analytics-main.png)

* **시간 범위:** 미리 정의된 시간 범위 중 하나에서 시간 범위를 선택하거나 사용자 지정 시간 범위 ![분석 시간 범위](media\howto-create-analytics\analytics-time-range.png)를 만들 수 있습니다.

## <a name="change-the-visualizations"></a>시각화 변경

다음 세 모드 중 하나를 선택하여 시각화 요구 사항을 충족하도록 그래프를 변경할 수 있습니다.

* **누적형:** 모든 측정값의 그래프가 누적되고 그래프마다 고유의 Y축이 있습니다. 누적된 그래프는 측정값을 여러 개 선택했는데 각 측정값을 뚜렷하게 보고 싶은 경우에 유용합니다.
* **비 누적형:** 모든 측정값의 그래프가 한 Y축을 기준으로 표시되지만, Y축의 값은 강조 표시된 측정값에 따라 변합니다. 비 누적형 그래프는 여러 측정값을 오버레이하여 동일한 시간 범위의 측정값 패턴을 확인하려는 경우에 유용 합니다.
* **Y축 공유:** 모든 그래프가 동일한 Y축을 공유하고 축 값이 변하지 않습니다. Y축 공유 그래프는 단일 측정값을 살펴보고 데이터를 기준에 따라 조각화하려는 경우에 유용합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에 대한 사용자 지정 분석을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [Node.js 응용 프로그램 준비 및 연결](howto-connect-nodejs.md)