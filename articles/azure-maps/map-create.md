---
title: Azure Maps로 맵 만들기 | Microsoft Docs
description: Javascript 맵을 만드는 방법
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
ms.openlocfilehash: 9a7c611860a6d32f82d6714d945c62e6c562f91f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-map"></a>맵 만들기

이 문서에서는 맵을 만드는 방법을 보여 줍니다.  

## <a name="understand-the-code"></a>코드 이해

두 가지 방법으로 맵을 만들 수 있습니다. 중심점 및 확대/축소 수준을 지정하여 맵의 카메라를 설정하거나 남서쪽 경계 지점과 북동쪽 경계 지점을 지정하여 맵의 카메라 범위를 설정할 수 있습니다.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>카메라 설정

<iframe height='310' scrolling='no' title='CameraOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure LBS(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>CameraOptions를 통해 맵 만들기</a>를 참조하세요.
</iframe>

위의 코드에서 [맵 개체](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)는 `new atlas.Map()`을 통해 생성됩니다. 중심 및 확대/축소 수준과 같은 맵 속성은 [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest)의 일부입니다. CameraOptions는 맵 생성자에서 정의하거나 맵 클래스의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) 함수를 통해 정의할 수 있습니다.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>카메라 범위 설정

<iframe height='310' scrolling='no' title='CameraBoundsOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>CameraBoundsOptions를 통해 맵 만들기</a>를 참조하세요.
</iframe>

위의 코드에서 [맵 개체](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)는 `new atlas.Map()`을 통해 생성됩니다. 경계 상자와 같은 맵 속성은 [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest)의 일부입니다. CameraBoundsOptions는 맵 클래스의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) 함수를 통해 정의할 수 있습니다.

## <a name="try-out-the-code"></a>코드 사용해 보기 

위의 샘플 코드를 살펴보세요. 왼쪽에 있는 JS 탭에서 JavaScript 코드를 편집하고 오른쪽에 있는 결과 탭에서 맵 보기 변경 내용을 확인할 수 있습니다. 또한 “CodePen에서 편집” 단추를 클릭하고 CodePen에서 코드를 편집할 수 있습니다. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 
* [맵](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)

