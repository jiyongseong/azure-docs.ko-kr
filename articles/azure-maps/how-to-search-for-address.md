---
title: Azure Maps 검색 서비스를 사용하여 주소를 검색하는 방법 | Microsoft Docs
description: Azure Maps 검색 서비스를 사용하여 주소를 검색하는 방법을 알아봅니다.
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
ms.openlocfilehash: 4a87c0d0a487706209acc1c6a4ded92e6aab8f9e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Azure Maps 검색 서비스를 사용하여 주소를 찾는 방법

맵 검색 서비스는 개발자가 주소, 위치, 관심 지점, 비즈니스 목록 및 기타 지리 정보를 검색할 수 있는 API의 RESTful 세트입니다. 검색 서비스는 특정 주소, 교차로, 지리적 특징 또는 POI(관심 지점)에 위도/경고를 할당합니다. 검색에서 반환된 위도 및 경도를 라우트 및 트래픽 흐름과 같은 다른 맵 서비스에서 매개 변수로 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

맵 서비스 API에 대한 호출을 생성하려면 맵 계정 및 키가 필요합니다. 계정 만들기 및 키 검색에 대한 자세한 내용은 [Azure Maps 계정 및 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다. 


## <a name="using-fuzzy-search"></a>유사 항목 검색 사용

검색 서비스용 기본 API는 주소 또는 POI 토큰의 임의 조합 입력을 처리하는 [유사 항목 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)입니다. 이 검색 API는 정식 '한 줄 검색'이며 사용자가 검색 쿼리로 입력하는 내용을 모르는 경우 유용합니다. 유사 항목 검색 API는 POI 검색과 지오코딩의 조합입니다. 또한 API는 컨텍스트 위치(위도/경도  쌍)로 가중치를 부여하거나 좌표와 반경으로 완전히 구속하거나 지오 바이어스 앵커 지점 없이 더 일반적으로 실행할 수도 있습니다.

대부분의 검색 쿼리는 기본적으로 성능을 얻고 이상한 결과를 줄이는 'maxFuzzyLevel = 1'입니다. 요청에 따라 필요한 경우 쿼리 매개 변수에 'maxFuzzyLevel=2' 또는 '3'을 전달하여 이 기본값을 재정의할 수 있습니다.

### <a name="search-for-an-address-using-fuzzy-search"></a>유사 항목 검색을 사용하여 주소 검색

1. Postman 앱을 열고 새로 만들기 새로 만들기를 클릭하고 **GET 요청**을 선택합니다. **유사 항목 검색**의 요청 이름을 입력하고 이를 저장할 컬렉션 또는 폴더를 선택하고 **저장**을 클릭합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 끝점에 대한 요청 URL을 입력합니다.

    ![유사 항목 검색 ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | https://atlas.microsoft.com/search/fuzzy/json? |
    | 권한 부여 | 인증 없음 |

    URL 경로의 **json** 특성은 응답 형식을 결정합니다. 이 문서 전체에서 편의성과 가독성을 위해 json을 사용합니다. [맵 기능 API 참조](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy))의 **유사 항목 검색 가져오기** 정의에서 사용 가능한 응답 형식을 찾을 수 있습니다.

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.

    ![유사 항목 검색 ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | 쿼리 | 피자 |

4. **보내기**를 클릭하고 응답 본문을 검토합니다. 

    "피자"라는 모호한 쿼리 문자열을 입력하면 "피자"와 "음식점"에 속하는 범주를 가진 관심 지점(POI) 결과 10개가 반환됩니다. 각 결과는 위치에 대한 도로 주소, 위도 / 경도 값, 뷰 포트 및 진입점을 반환합니다.
    
    결과는 특정 기준 위치에 연결되지 않고 이 쿼리에 대해 변화됩니다. 기본 동작이 전체 세계를 검색하여 불필요한 결과를 반환할 가능성이 높기 때문에 **countrySet** 매개 변수를 사용하여 응용 프로그램에 적용 범위가 필요한 국가만 지정할 수 있습니다.

5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |------------------|-------------------------|
    | countrySet | US |
    
    이제 결과는 국가 코드로 경계가 구분되며 쿼리는 미국의 피자 음식점을 반환합니다.
    
    특정 위치를 중심으로 하는 결과를 제공하려면 관심 지점을 쿼리하고 반환된 위도와 경도를 유사 항목 검색 서비스 호출에 사용할 수 있습니다. 이 경우 검색 서비스를 사용하여 시애틀의 스페이스 니들(Space Needle) 탑의 위치를 반환하고 위도  / 경도를 사용했습니다. 검색 방향을 지정하는 값입니다.
    
4. 매개 변수에서 다음 키 / 값 쌍을 입력하고 **보내기**를 클릭합니다.

    ![유사 항목 검색 ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | 키 | 값 |
    |-----|------------|
    | 윈도 | 47.62039 |
    | 경도 | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>주소 속성 및 좌표 검색 

전체 또는 부분 도로 주소를 검색 주소 API에 전달하고 지방 자치체 또는 구획 등과 같은 상세 주소 속성뿐만 아니라 위도와 경도 단위의 위치 값도 포함하는 응답을 받습니다.

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 이름을 **주소 검색**으로 지정합니다.
2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 끝점에 대한 요청 URL을 입력한 다음 권한 부여 프로토콜이 있는 경우 해당 프로토콜을 선택합니다.

    ![주소 검색 ](./media/how-to-search-for-address/address_search_url.png)
    
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | https://atlas.microsoft.com/search/address/json? |
    | 권한 부여 | 인증 없음 |

2. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
    
    ![주소 검색 ](./media/how-to-search-for-address/address_search_params.png)
    
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | 쿼리 | 400 한 광범위 한 St, 시애틀, WA 98109 |
    
3. **보내기**를 클릭하고 응답 본문을 검토합니다. 
    
    이 경우 전체 주소 쿼리를 지정했으며 응답 본문에 단일 결과를 받습니다. 
    
4. 매개 변수에 다음 값에 대한 쿼리 문자열을 편집합니다.
    ```
        400 Broad, Seattle
    ```

5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | typeahead | true |

    **typeahead** 플래그는 주소 검색 API에 쿼리를 부분 입력으로 처리하고 예측 값의 배열을 반환하도록 지시합니다.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>역방향 주소 검색을 사용하여 주소 검색
1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 **역 주소 검색**으로 이름 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 끝점에 대한 요청 URL을 입력합니다.
    
    ![역 주소 검색 URL ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | https://atlas.microsoft.com/search/address/reverse/json? |
    | 권한 부여 | 인증 없음 |
    
2. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
    
    ![역 주소 검색 매개 변수 ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | 쿼리 | 47.59093,-122.33263 |
    
3. **보내기**를 클릭하고 응답 본문을 검토합니다. 
    
    응답은 "운동장" POI 범주와 함께 세이프코 필드 야구장에 대한 POI 항목을 포함합니다. 
    
4. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | number | true |

    요청과 함께 [숫자](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 쿼리 매개 변수를 보내는 경우 응답은 도로의 측면(왼쪽/오른쪽) 및 해당 번호에 대한 오프셋 위치로 포함할 수 있습니다.
    
5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | spatialKeys | true |

    [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 쿼리 매개 변수를 설정한 경우, 응답은 지정된 위치에 대한 독자적인 지역-공간 키 정보를 포함합니다.

6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | returnSpeedLimit | true |
    
    [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 쿼리 매개 변수를 설정한 경우 응답은 게시된 속도 제한을 반환합니다.

7. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | returnRoadUse | true |

    [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 쿼리 매개 변수를 설정한 경우 응답은 도로 수준의 리버스 지오코드(reversegeocode)에 대한 도로 사용 배열을 반환합니다.

8. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | roadUse | true |

    [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 쿼리 매개 변수를 사용하면 역 지오코드 쿼리를 특정 유형의 도로 사용으로 제한할 수 있습니다.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>역 주소 교차로 검색을 사용하여 교차로 검색

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 **역 주소 교차로 검색**으로 이름 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 끝점에 대한 요청 URL을 입력합니다.
    
    ![역 주소 교차로 검색 ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | 권한 부여 | 인증 없음 |
    
3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
    
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | 쿼리 | 47.59093,-122.33263 |
    
4. **보내기**를 클릭하고 응답 본문을 검토합니다. 

## <a name="next-steps"></a>다음 단계
- [Azure Maps 검색 서비스](https://docs.microsoft.com/rest/api/maps/search) API 문서 탐색 
