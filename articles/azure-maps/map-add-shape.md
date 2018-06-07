---
title: Azure Maps로 도형 추가 | Microsoft Docs
description: Javascript 맵에 도형을 추가하는 방법
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
ms.custom: codepen
ms.openlocfilehash: fb9ec0713d3db465cf835346465e70c4455b38ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-shape-to-a-map"></a>맵에 도형 추가

이 문서에서는 선, 원 및 다각형을 맵에 추가하는 방법을 보여 줍니다. 

<a id="addALine"></a>

## <a name="add-a-line"></a>선 추가

<iframe height='500' scrolling='no' title='맵에 선 추가' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>맵에 선 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성합니다. 지침은 [맵 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서 선이 만들어집니다. 선은 기능 속성으로 LineStringProperties를 사용하는 LineString의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)입니다. `new atlas.data.Feature(new atlas.data.LineString())`을 사용하여 선을 만들고 속성을 정의합니다. 

선 계층은 선의 배열입니다. 마지막 코드 블록은 map 클래스의 [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) 함수를 사용하여 맵에 선 계층을 추가하고 선 계층의 속성을 정의합니다. [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요.

<a id="addACircle"></a>

## <a name="add-a-circle"></a>원 추가

<iframe height='500' scrolling='no' title='맵에 원 추가' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>맵에 원 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성합니다. 지침은 [맵 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 원이 만들어집니다. 원은 기능 속성으로 [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest)를 사용하는 [Point](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest)의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)입니다. `new atlas.data.Feature(new atlas.data.Point())`을 사용하여 원을 만들고 속성을 정의합니다.

원 계층은 원의 배열입니다. 마지막 코드 블록은 map 클래스의 [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) 함수를 사용하여 원 계층을 맵에 추가하고 원 계층의 속성을 정의합니다. [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest)에서 원 계층의 속성을 참조하세요.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>다각형 추가
<iframe height='500' scrolling='no' title='맵에 다각형 추가 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>맵에 다각형 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성합니다. 지침은 [맵 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 다각형이 만들어집니다. 다각형은 기능 속성으로 [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest)를 사용하는 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest)의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)입니다. `new atlas.data.Feature(new atlas.data.Polygon())`을 사용하여 다각형을 만들고 속성을 정의합니다. polygon 생성자에서 다각형 경로에 대한 정렬된 좌표를 제공합니다.

다각형 계층은 다각형의 배열입니다. 마지막 코드 블록은 map 클래스의 [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) 함수를 사용하여 다각형 계층을 맵에 추가하고 해당 속성을 정의합니다. [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 
