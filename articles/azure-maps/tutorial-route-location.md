---
title: Azure Maps로 경로 찾기 | Microsoft Docs
description: Azure Maps를 사용하여 관심 지점으로 라우팅
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 73ca61140f05a65ca75cd703ed226773b9a43dfa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Azure Maps를 사용하여 관심 지점으로 라우팅

이 자습서에서는 Azure Maps 계정과 Route Service SDK를 사용하여 관심 지점까지의 경로를 찾는 방법을 보여줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주소 좌표 설정
> * 관심 지점으로의 방향에 대한 Route Service 쿼리

## <a name="prerequisites"></a>필수 조건

계속 진행하기 전에 이전 자습서의 단계에 따라 [Azure Maps 계정 만들기](./tutorial-search-location.md#createaccount), [계정에 대한 구독 키 가져오기](./tutorial-search-location.md#getkey)를 수행합니다. 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>새 지도 만들기 
다음 단계에서는 지도 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여줍니다. 

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapRoute.html**로 지정합니다. 
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    HTML 헤더에는 Azure Maps 라이브러리용 CSS 및 JavaScript 파일에 대한 리소스 위치가 포함됩니다. HTML 파일의 body에 있는 *스크립트* 세그먼트에는 Azure Maps API에 액세스하기 위한 인라인 JavaScript 코드가 포함됩니다.

3. 다음 JavaScript 코드를 HTML 파일의 *스크립트* 블록에 추가합니다. **\<계정 키\>** 문자열을 Maps 계정에서 복사한 기본 키로 바꿉니다.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas Map**은 시각적 및 대화형 웹 맵에 대한 컨트롤을 제공하고 Azure 맵 컨트롤 API의 구성 요소입니다.

4. 파일을 저장하고 브라우저에서 엽니다. 이 시점에서 더 자세히 개발할 수 있는 기본 지도가 있습니다. 

   ![기본 지도 보기](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>출발 지점 및 도착 지점 설정

이 자습서에서는 Microsoft를 시작점으로 설정하고, 시애틀의 주유소를 대상 지점으로 설정합니다. 

1. **MapRoute.html** 파일의 동일한 *script* 블록에서 다음 JavaScript 코드를 추가하여 경로의 출발점과 종료점을 만듭니다.

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    이 코드는 경로의 출발점과 도착점을 나타낼 두 개의 [GeoJSON 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다. 

2. 다음 JavaScript 코드를 추가하여 맵에 출발점과 종료점에 대한 핀을 추가합니다.

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    **map.setCameraBounds**는 출발점과 도착점의 좌표에 따라 지도 창을 조정합니다. API **map.addPins**는 점을 시각적 구성 요소로 맵 컨트롤에 추가합니다.

7. **MapRoute.html** 파일을 저장하고, 브라우저를 새로 고칩니다. 이제 지도의 중심에 시애틀이 표시되며, 출발점을 표시하는 둥근 파란색 핀과 도착점을 표시하는 파란색 핀을 볼 수 있습니다.

   ![시작점과 끝점이 표시된 지도 보기](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>방향 가져오기

이 섹션에서는 Maps의 경로 서비스 API를 사용하여 주어진 출발점에서 목적지까지 경로를 찾는 방법을 보여줍니다. 경로 서비스는 두 위치 간의 *최소 시간*, *최단 거리*, *최적* 또는 *모험* 경로를 계획할 수 있는 API를 제공합니다. 또한 사용자는 Azure의 광범위한 교통 기록 데이터베이스를 사용해 어떤 날짜 및 시간에 대한 경로 기간을 예측하여 미래의 경로를 계획할 수 있습니다. 자세한 내용은 [경로 방향 가져오기](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 참조하세요.


1. 먼저 지도에 새 레이어를 추가하여 경로 또는 *linestring*을 표시합니다. 다음 JavaScript 코드를 *스크립트* 블록에 추가합니다.

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

2. [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 만들고, 이벤트 처리기를 추가하여 Maps 경로 서비스에서 보낸 JSON 응답을 구문 분석합니다. 이 코드는 경로의 선 세그먼트에 대한 좌표 배열을 만든 다음, 지도의 Linestring 계층에 좌표 집합을 추가합니다. 

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```

3. 다음 코드를 추가하여 쿼리를 작성하고 XMLHttpRequest를 Maps 경로 서비스로 보냅니다.

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. **MapRoute.html** 파일을 저장하고, 웹 브라우저를 새로 고칩니다. Maps API와 성공적으로 연결되면 다음과 비슷한 지도가 표시됩니다. 

    ![Azure 맵 컨트롤 및 Route Service](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주소 좌표 설정
> * 관심 지점으로의 방향에 대한 경로 서비스 쿼리

다음 자습서에는 이동 모드 또는 화물 유형과 같은 제한을 적용하여 경로 쿼리를 만든 다음, 같은 지도에 여러 경로를 표시하는 방법을 보여줍니다. 

> [!div class="nextstepaction"]
> [여러 이동 모드에 대한 경로 찾기](./tutorial-prioritized-routes.md)
