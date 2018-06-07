---
title: Azure Maps로 핀 추가 | Microsoft Docs
description: Javascript 맵에 핀을 추가하는 방법
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
ms.openlocfilehash: 189b30ff0b5c47d0a4bd3181c8eb2143213ad6cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="add-pins-to-the-map"></a>맵에 핀 추가

이 문서에서는 맵에 핀을 추가하는 방법을 보여 줍니다.  

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='맵에 핀 추가' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>맵에 핀 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 만듭니다. [맵 만들기](./map-create.md)에서 지침을 참조할 수 있습니다.

두 번째 코드 블록에서 핀이 생성되고 맵에 추가됩니다. 핀은 원은 기능 속성으로 [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest)를 사용하는 [Point](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest)의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)입니다. `new atlas.data.Feature(new atlas.data.Point())`를 사용하여 핀을 만들고 속성을 정의합니다. 핀 계층은 핀의 배열입니다. 맵 클래스의 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) 기능을 사용하여 맵에 핀 계층을 추가하고 핀 계층의 속성을 정의합니다. [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest)에서 핀 계층의 속성을 참조하세요. 

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 
* [맵](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
