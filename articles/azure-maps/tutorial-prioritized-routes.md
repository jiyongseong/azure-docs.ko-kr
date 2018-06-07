---
title: Azure Maps를 사용하여 여러 경로 찾기 | Microsoft Docs
description: Azure Maps를 사용하여 여러 여행 모드에 대한 경로 찾기
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
ms.openlocfilehash: b45394cedcb7bbcb46b9730b7e445b04c8cf4f18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Azure Maps를 사용하여 여러 여행 모드에 대한 경로 찾기

이 자습서에서는 Azure Maps 계정과 경로 서비스를 사용하여 여행 모드의 우선 순위에 따라 관심 지점까지의 경로를 찾는 방법을 보여 줍니다. 지도에는 서로 다른 두 개의 경로가 표시됩니다. 하나는 승용차용이고, 다른 하나는 높이, 무게 또는 위험 화물로 인해 경로가 제한되는 화물차용입니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 지도에서 교통 흐름 시각화
> * 여행 모드를 선언하는 경로 쿼리 만들기
> * 지도에 여러 경로 표시

## <a name="prerequisites"></a>필수 조건

계속 진행하기 전에 첫 번째 자습서의 단계에 따라 [Azure Maps 계정 만들기](./tutorial-search-location.md#createaccount), [계정에 대한 구독 키 가져오기](./tutorial-search-location.md#getkey)를 수행합니다. 


## <a name="create-a-new-map"></a>새 지도 만들기 
다음 단계에서는 지도 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여 줍니다. 

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapTruckRoute.html**로 지정합니다. 
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    HTML 헤더에는 Azure Maps 라이브러리용 CSS 및 JavaScript 파일에 대한 리소스 위치가 포함됩니다. HTML 본문의 *script* 세그먼트에는 지도에 대한 인라인 JavaScript 코드가 포함됩니다.
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

   ![기본 지도 보기](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>교통 흐름 시각화

1. 초점을 맞출 위치를 지도에 지정하지 않으면 전 세계 보기가 표시됩니다. 교통 데이터를 표시하려면 지도에서 중심점과 확대/축소 수준을 설정합니다. `new atlas.Map`을 선언하는 코드를 다음 JavaScript 코드로 바꿉니다. 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    이 코드에서는 지도의 중심점을 설정하고, 기본적으로 특정 영역에 집중할 수 있도록 확대/축소 수준을 선언합니다. 

1. 지도에 교통 흐름 표시를 추가합니다.

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    이 코드는 트래픽 흐름을 자유 흐름에 대한 상대 도로 속도인 `relative`로 설정합니다. 또한 도로의 `absolute` 속도 또는 자유 흐름과 다른 상대 속도를 표시하는 `relative-delay`로 설정할 수도 있습니다. 

2. **MapTruckRoute.html** 파일을 저장하고, 브라우저에서 페이지를 새로 고칩니다. 현재 교통 데이터가 있는 로스앤젤레스의 거리가 표시됩니다.

   ![교통 지도 보기](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>출발 지점 및 도착 지점 설정

이 자습서에서는 출발 지점을 시애틀 소재의 Fabrikam이라는 가상 회사로, 도착 지점을 Microsoft 본사로 설정합니다. 

1. 다음 JavaScript 코드를 추가하여 경로의 출발점과 도착점에 대한 핀을 만듭니다.

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    이 코드는 경로의 출발점과 도착점을 나타낼 두 개의 [GeoJSON 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다. 

2. 다음 JavaScript 코드를 추가하여 맵에 출발점과 도착점을 추가합니다.

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    **map.setCameraBounds** 호출은 출발 지점과 도착 지점의 좌표에 따라 지도 창을 조정합니다. API **map.addPins**는 점을 시각적 구성 요소로 맵 컨트롤에 추가합니다.

3. 파일을 저장하고, 브라우저를 새로 고쳐 지도에 표시된 핀을 확인합니다. 로스앤젤레스에 중심을 맞춘 지도를 선언했지만 **map.setCameraBounds**는 보기를 이동하여 출발 지점과 도착 지점을 표시했습니다. 

   ![출발 지점과 도착 지점이 포함된 지도 보기](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>여행 모드에 따라 우선 순위가 지정된 경로 렌더링

이 섹션에서는 Maps 경로 서비스 API를 사용하여 운송 모드에 따라 지정된 출발 지점과 도착 지점 간의 여러 경로를 찾는 방법을 보여 줍니다. 경로 서비스는 현재 교통 상황을 고려하여 두 위치 간의 *최소 시간*, *최단 거리*, *최적* 또는 *모험* 경로를 계획할 수 있는 API를 제공합니다. 또한 사용자는 Azure의 광범위한 교통 기록 데이터베이스를 사용해 어떤 날짜 및 시간에 대한 경로 기간을 예측하여 미래의 경로를 계획할 수 있습니다. 자세한 내용은 [경로 방향 가져오기](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 참조하세요.


1. 먼저 지도에 새 레이어를 추가하여 경로 또는 *linestring*을 표시합니다. 이 자습서에는 **car-route**(승용차 경로)와 **truck-route**(화물차 경로)의 두 가지 경로가 있으며, 각 경로마다 고유한 스타일이 적용됩니다. 다음 JavaScript 코드를 *script* 블록에 추가합니다.

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. *script* 블록에 다음 JavaScript 코드를 추가하여 화물차 경로를 요청하고 결과를 지도에 표시합니다.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    이 코드 조각은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 생성하고, 들어오는 응답을 구문 분석하는 이벤트 처리기를 추가합니다. 응답이 성공하면 반환된 경로에 대한 좌표 배열을 만들고 맵의 `truckRouteLayerName` 레이어에 추가합니다. 
    
    또한 이 코드 조각에서는 계정 키를 사용하여 Maps 경로 서비스에 대한 쿼리를 만듭니다. 이 쿼리에는 출발 지점 좌표, 도착 지점 좌표 및 대형 화물차에 대한 경로임을 나타내는 선택적 매개 변수가 포함됩니다.

2. 다음 JavaScript 코드를 추가하여 승용차 경로를 요청하고 결과를 표시합니다.

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    이 코드 조각에서는 또 다른 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 만들고, 들어오는 응답을 구문 분석하는 이벤트 처리기를 추가합니다. 응답이 성공하면 반환된 경로에 대한 좌표 배열을 만들고 지도의 `carRouteLayerName` 레이어에 추가합니다. 
    
    또한 이 코드 조각에서는 계정 키를 사용하여 Maps 경로 서비스에 대한 쿼리를 만듭니다. 쿼리에는 출발 지점 좌표와 도착 지점 좌표가 포함됩니다. 추가 매개 변수가 제공되지 않으므로 경로 서비스에서 기본적으로 여행 모드를 *car*(승용차)로 설정합니다. 

3. **MapTruckRoute.html** 파일을 저장하고, 브라우저를 새로 고쳐 결과를 확인합니다. Maps API와 성공적으로 연결되면 다음과 비슷한 지도가 표시됩니다. 

    ![Azure Route Service를 사용하여 우선 순위가 지정된 경로](./media/tutorial-prioritized-routes/prioritized-routes.png)

    화물차 경로는 두꺼운 파란색이지만, 승용차 경로는 얇은 자주색입니다. 승용차 경로는 I-90 도로를 통해 워싱턴 호수를 가로질러 주거 지역 아래의 터널을 통과하므로 위험한 폐기 화물을 제한합니다. USHazmatClass2 화물 유형을 지정한 화물차 경로는 다른 고속 도로를 사용하도록 올바르게 지시됩니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 지도에서 교통 흐름 시각화
> * 여행 모드를 선언하는 경로 쿼리 만들기
> * 지도에 여러 경로 표시

Azure Maps의 적용 범위 및 기능에 대해 자세히 알아보려면 [확대/축소 수준 및 타일 격자](zoom-levels-and-tile-grid.md) 및 다른 개념 문서를 참조하세요. 

더 많은 코드 예제 및 대화형 코딩 환경은 [지도 컨트롤을 사용하는 방법](how-to-use-map-control.md) 및 다른 방법 가이드를 참조하세요. 
