---
title: Azure Maps를 사용하여 좌표에 대한 정보 표시 | Microsoft Docs
description: 사용자가 좌표를 선택할 경우 맵에서 주소에 대한 정보를 표시하는 방법
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
ms.openlocfilehash: bb8644724cc872a0a8bc331e76251218492fd93d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="get-information-from-a-coordinate"></a>좌표에서 정보 가져오기

이 문서에서는 역 주소 검색을 만들고 마우스 클릭 시 팝업에 클릭한 위치의 주소를 표시하는 방법을 보여 줍니다. 

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='좌표에서 정보 가져오기' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>좌표에서 정보 가져오기</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 만듭니다. [맵 만들기](./map-create.md)에서 지침을 참조할 수 있습니다.

두 번째 코드 블록은 포인터에 마우스 커서의 스타일을 업데이트합니다.

세 번째 코드 블록은 팝업을 만듭니다. [맵에서 팝업 추가](./map-add-popup.md)에서 지침을 확인할 수 있습니다.

마지막 코드 블록은 마우스 클릭에 대한 이벤트 수신기를 추가합니다. 마우스 클릭 시 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 [Azure Maps 역방향 주소 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)로 전송합니다. 성공적인 응답의 경우 클릭한 위치에 대한 주소를 수집하고 팝업 클래스의 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) 기능을 통해 팝업 콘텐츠 및 위치를 정의합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 
* [역 주소 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [맵](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [팝업](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [열기](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [닫기](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
