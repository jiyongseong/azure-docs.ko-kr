---
title: Azure Maps 맵 컨트롤을 사용하는 방법 | Microsoft Docs
description: Azure Maps 맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하는 방법을 알아봅니다.
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: bbd06aad9052d2a775c35dd08f80462f8ea505a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Azure Maps 맵 컨트롤을 사용하는 방법
맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하면 맵 및 포함된 Azure Maps 기능을 웹 또는 모바일 응용 프로그램에 렌더링할 수 있습니다. 

## <a name="create-a-new-map-in-a-web-page"></a>웹 페이지에 새 맵 만들기

맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하여 웹 페이지에 지도를 포함할 수 있습니다.

1. 새 파일을 만들고 이름을 MapSearch.html로 지정합니다.

2. 파일의 `<head>` 요소에 Azure Maps 스타일시트 및 스크립트 소스 참조를 추가합니다.

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. 브라우저에서 새 맵을 렌더링하려면 `<style>` 요소에 **#map** 참조를 추가합니다.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. 맵 컨트롤을 초기화하기 위해 html 본문에 새 섹션을 정의하고 스크립트를 만듭니다. 스크립트에서 자체 Azure Maps 계정 키를 사용합니다. 계정을 만들거나 키를 찾아야 하는 경우 [Azure Maps 계정 및 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요.

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. 웹 브라우저에서 파일을 열고 렌더링된 맵을 봅니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Maps 키로 기본 맵을 만드는 방법을 보았습니다. 맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요. 

* [맵 만들기](map-create.md)
* [핀 추가](map-add-pin.md)