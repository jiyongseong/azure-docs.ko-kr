---
title: Azure Maps에서 확대/축소 수준 및 타일 그리드 | Microsoft Docs
description: Azure Maps에서 확대/축소 수준 및 타일 그리드에 대해 알아봅니다.
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 330333569f094fe3cec7f73ee3b20107ec70f5b5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="zoom-levels-and-tile-grid"></a>확대/축소 수준 및 타일 그리드
Azure Maps에서는 구면 메르카토르 도법 구면좌표계를 사용합니다(EPSG: 3857).

전 세계는 정사각형 타일로 구분됩니다. 렌더링(래스터)에는 0부터 18까지 19개의 확대/축소 수준이 있습니다. 렌더링(벡터)에는 0부터 20까지 21개의 확대/축소 수준이 있습니다. 확대/축소 수준 0에서 전 세계는 단일 타일로 맞춰집니다.

![세계 타일](./media/zoom-levels-and-tile-grid/world0.png)

확대/축소 수준 1은 4개의 타일을 사용하여 전 세계를 2x2 사각형으로 렌더링합니다.

![왼쪽 위 세계 타일](./media/zoom-levels-and-tile-grid/world1a.png)     ![오른쪽 위 세계 타일](./media/zoom-levels-and-tile-grid/world1c.png) 

![왼쪽 아래 세계 타일](./media/zoom-levels-and-tile-grid/world1b.png)     ![오른쪽 아래 세계 타일](./media/zoom-levels-and-tile-grid/world1d.png) 


후속 확대/축소 수준 4개 타일은 각각 이전 수준의 타일을 나누어서 2<sup>확대/축소</sup>x2<sup>확대/축소</sup> 그리드를 만듭니다. 확대/축소 수준 20은 2<sup>20</sup> x 2<sup>20</sup> 그리드 또는 1,048,576 x 1,048,576 타일(총 109,951,162,778개)입니다.

다음 표는 확대/축소 수준의 전체 목록 값을 제공합니다.

|확대/축소 수준|미터/픽셀|미터/타일 측|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|

타일은 해당 확대/축소 수준의 그리드에서 타일의 위치에 해당하는 확대/축소 수준과 xy 좌표로 호출됩니다.

사용할 확대/축소 수준을 결정할 때 각 위치는 해당 타일의 고정된 위치에 있습니다. 즉, 지정된 범위의 지역을 표시하는 데 필요한 타일의 수는 전 세계에 대한 확대/축소 그리드의 배치에 따라 달라집니다. 예를 들어 900미터 떨어진 두 개의 지점이 있는 경우 확대/축소 수준 17에서 서로 간에 경로를 표시하기 위해 세 개의 타일만을 사용할 *수* 있습니다. 그러나 서쪽 지점이 해당 타일의 오른쪽에 있고 동쪽 지점이 해당 타일의 왼쪽에 있는 경우 4개의 타일이 필요할 수 있습니다.

![확대/축소 데모 크기 조정](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

확대/축소 수준이 결정되면 x 및 y 값을 계산할 수 있습니다. 각 확대/축소 그리드의 왼쪽 위 타일은 x=0, y=0이고, 오른쪽 아래 타일은 x=2<sup>zoom -1</sup>, y=2<sup>zoom-1</sup>입니다.

확대/축소 수준 1에서 확대/축소 그리드는 다음과 같습니다.

![확대/축소 수준 1에서 확대/축소 그리드](./media/zoom-levels-and-tile-grid/api_x_y.png)
