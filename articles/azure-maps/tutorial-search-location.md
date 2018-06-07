---
title: Azure Maps를 사용하여 검색 | Microsoft Docs
description: Azure Maps를 사용하여 주변 관심 지점 검색
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
ms.openlocfilehash: a4479ceebd4c8aad477b5f13a5bcc06d24c1202d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Azure Maps를 사용하여 주변 관심 지점 검색

이 자습서에서는 Azure Maps를 사용하여 계정을 설정한 다음, Maps API를 사용하여 관심 지점을 검색하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Maps 계정 만들기
> * Maps 계정에 대한 기본 키 검색
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * Maps 검색 서비스를 사용하여 주변 관심 지점 찾기

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure 포털](https://portal.azure.com) 에 로그인합니다.

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps를 사용하여 계정 만들기

다음 단계에 따라 새 Maps 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. *마켓플레이스 검색* 상자에서 **Maps**를 입력합니다.
3. *결과*에서 **Maps**를 선택합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다. 
4. **Maps 계정 만들기** 페이지에서 다음 값을 입력합니다.
    - 새 계정의 *이름*. 
    - 이 계정에 사용하려는 *구독*.
    - 이 계정에 대한 *리소스 그룹* 이름. *새로 만들기* 또는 *기존* 리소스 그룹 사용을 선택할 수도 있습니다.
    - *리소스 그룹 위치*를 선택합니다.
    - *라이선스* 및 *개인정보처리방침*을 읽고 조건에 동의하는 확인란을 선택합니다. 
    - **만들기** 단추를 클릭합니다.
   
    ![포털에서 Maps 계정 만들기](./media/tutorial-search-location/create-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>사용자 계정에 대한 기본 키 가져오기

Maps 계정이 성공적으로 만들어지면 Maps API를 쿼리할 수 있는 키를 검색합니다.

1. 포털에서 Maps 계정을 엽니다.
2. 설정 섹션에서 **키**를 선택합니다.
3. **기본 키**를 클립보드로 복사합니다. 이 자습서의 뒷부분에서 사용하기 위해 로컬로 저장합니다. 

    ![포털에서 기본 키 가져오기](./media/tutorial-search-location/get-key.png)


<a id="createmap"></a>

## <a name="create-a-new-map"></a>새 지도 만들기 
지도 컨트롤 API는 Maps를 웹 응용 프로그램에 쉽게 통합할 수 있도록 하는 편리한 클라이언트 라이브러리입니다. 기본 REST 서비스 호출의 복잡성을 숨기고 스타일 지정 및 사용자 지정 가능한 구성 요소를 사용하여 생산성을 향상합니다. 다음 단계에서는 지도 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여 줍니다. 

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapSearch.html**로 지정합니다. 
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    HTML 헤더는 Azure 맵 컨트롤 라이브러리에서 호스팅하는 CSS 및 JavaScript 리소스 파일을 포함합니다. *스크립트* 세그먼트가 HTML 파일의 *body*에 추가됨을 참고합니다. 이 세그먼트에는 Azure Maps API에 액세스할 수 있는 인라인 JavaScript 코드가 포함됩니다.
 
3. 다음 JavaScript 코드를 HTML 파일의 *스크립트* 블록에 추가합니다. **\<계정 키\>** 문자열을 Maps 계정에서 복사한 기본 키로 바꿉니다.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    이 세그먼트는 Azure Maps 계정 키에 대한 지도 컨트롤 API를 시작합니다. **Atlas**는 API 및 관련 시각적 구성 요소가 포함된 네임스페이스입니다. **Atlas.Map**은 시각적 및 대화형 웹 지도에 대한 컨트롤을 제공합니다. 
    
4. 변경 내용을 파일에 저장하고 브라우저에서 해당 HTML 페이지를 엽니다. 이 지도는 **atlas.map**을 호출하고 계정 키를 제공하여 만들 수 있는 가장 기본적인 지도입니다. 

   ![지도 보기](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>검색 기능 추가

이 섹션에서는 Maps 검색 API를 사용하여 지도에서 관심 지점을 찾는 방법을 보여 줍니다. 개발자가 주소, 관심 지점 및 기타 지역 정보를 검색할 수 있도록 설계된 RESTful API입니다. 검색 서비스에서 지정된 주소에 위도와 경도 정보를 할당합니다. 

1. 지도에 새 레이어를 추가하여 검색 결과를 표시합니다. 지도를 초기화하는 코드 뒤에 있는 *script* 블록에 다음 JavaScript 코드를 추가합니다. 

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 만들고, 이벤트 처리기를 추가하여 Maps 검색 서비스에서 보낸 JSON 응답을 구문 분석합니다. 다음 코드 조각에서는 이벤트 처리기를 빌드하여 `searchPins` 변수에 반환된 각 위치에 대한 주소, 이름, 위도 및 경도 정보를 수집합니다. 마지막으로 이 위치 지점의 컬렉션을 `map` 컨트롤에 핀으로 추가합니다. 

    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```

3. 다음 코드를 *script* 블록에 추가하여 쿼리를 작성하고 XMLHttpRequest를 Maps 검색 서비스로 보냅니다.

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    이 코드 조각은 **Fuzzy Search**이라는 Search Service의 기본 검색 API를 사용합니다. 주소 또는 POI(관심 지점) 토큰의 조합을 포함하여 가장 유사한 입력 항목을 처리합니다. 지정된 위도와 경도 좌표의 지정된 반경 내에서 인접한 **주유소**를 검색합니다. 예제 파일의 앞부분에서 제공된 계정의 기본 키를 사용하여 Maps를 호출합니다. 찾은 위치에 대해 위도/경도 쌍으로 결과를 반환합니다. 
    
4. **MapSearch.html** 파일을 저장하고, 브라우저를 새로 고칩니다. 이제 지도가 시애틀 중심부에 있고 파란색 핀이 해당 영역의 주유소 위치를 표시하고 있습니다. 

   ![검색 결과가 포함된 지도 보기](./media/tutorial-search-location/pins-map.png)

5. 파일에 작성한 XMLHTTPRequest를 사용하고 브라우저에 입력하여 지도에서 렌더링하는 원시 데이터를 볼 수 있습니다. \<계정 키\>를 기본 키로 바꿉니다. 

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

이 시점에서 MapSearch 페이지에는 유사 항목 검색 쿼리에서 반환된 관심 지점의 위치가 표시될 수 있습니다. 몇 가지 대화형 기능과 위치에 대한 자세한 정보를 추가해 보겠습니다. 

## <a name="add-interactive-data"></a>대화형 데이터 추가

지금까지 만든 지도에서는 검색 결과의 위도/경도 데이터만 조사했습니다. 그러나 Maps 검색 서비스에서 반환하는 원시 JSON을 살펴보면 이름과 주소를 포함하여 각 주유소에 대한 추가 정보가 있음을 알 수 있습니다. 대화형 팝업 상자를 사용하여 해당 데이터를 지도에 통합할 수 있습니다. 

1. 다음 코드 줄을 *스크립트* 블록에 추가하여 Search Service에서 반환한 관심 지점에 대한 팝업을 만듭니다.

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    API **atlas Popup**은 맵의 필수 지점에 고정된 정보 창을 제공합니다. 이 코드 조각은 팝업에 대한 콘텐츠 및 위치를 설정할 뿐만 아니라 _마우스_가 팝업을 롤오버하도록 기다려 이벤트 수신기를 `map` 컨트롤에 추가합니다. 

4. 파일을 저장하고 브라우저를 새로 고칩니다. 이제 브라우저의 지도에서 검색 핀 위를 마우스로 가리키면 정보 팝업이 표시됩니다. 

    ![Azure 맵 컨트롤 및 Search Service](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Maps를 사용하여 계정 만들기
> * 사용자 계정에 대한 기본 키 가져오기
> * 맵 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주변 관심 지점을 찾기 위해 Search Service 사용

다음 자습서에서는 두 위치 사이의 경로를 표시하는 방법을 보여 줍니다. 

> [!div class="nextstepaction"]
> [대상으로 라우팅](./tutorial-route-location.md)
