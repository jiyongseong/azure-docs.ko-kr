---
title: Azure Maps를 사용하여 검색 결과 표시 | Microsoft Docs
description: Azure Maps로 검색 요청을 수행한 후 Javascrip 지도에 결과를 표시하는 방법
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
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="show-search-results-on-the-map"></a>지도에 검색 결과 표시

이 문서에서는 검색 요청을 만들고 지도에 검색 결과를 표시하는 방법을 보여줍니다. 

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='지도에 검색 결과 표시' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>지도에 검색 결과 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 검색 핀 계층을 만들고 지도에 추가합니다. 지침은 [지도에 핀 추가](./map-add-pin.md)를 참조하세요.

세 번째 코드 블록은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)로 보냅니다.

코드의 마지막 블록은 수신 응답을 구문 분석합니다. 성공적인 응답을 위해 반환되는 각 위치에 대한 위도 및 경도 정보를 수집합니다. 지도에 모든 위치 지점을 핀으로 추가하고, 모든 핀이 렌더링되도록 지도 경계를 조정합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 

* [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
