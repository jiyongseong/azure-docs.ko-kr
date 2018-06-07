---
title: Fiddler 또는 Postman에서 REST API 탐색(Azure Search REST) | Microsoft Docs
description: Fiddler 또는 Postman을 사용하여 Azure Search에 HTTP 요청 및 REST API 호출을 발행하는 방법.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: eba41086da645c2ff5cee65f9395267227cb1c11
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Fiddler 또는 Postman을 사용하여 Azure Search REST API 탐

[Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice)를 탐색하는 가장 쉬운 방법 중 하나는 Fiddler 또는 Postman을 사용하여 HTTP 요청을 공식으로 만들고 응답을 검사하는 것입니다. 적절한 도구와 이러한 지침을 사용하면 코드를 작성하기 전에 요청을 전송하고 응답을 볼 수 있습니다.

> [!div class="checklist"]
> * 웹 API 테스트 도구 다운로드
> * 검색 서비스의 API 키와 끝점을 가져옵니다.
> * 요청 헤더 구성
> * 인덱스 만들기
> * 인덱스 로드
> * 인덱스 검색

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만든 다음 [Azure Search에 등록](search-create-service-portal.md)하십시오.

## <a name="download-and-install-tools"></a>도구 다운로드 및 설치

다음 도구는 웹 개발에 널리 사용되지만 다른 도구에 익숙하더라도 이 문서의 지침이 계속 적용됩니다.

+ [Postman 데스크톱 앱](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>API 키와 끝점 확보하기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. Azure Portal의 대시보드에서 검색 서비스 페이지를 열거나 서비스 목록에서 [서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. **개요** > **기본 정보** > **Url**에서 끝점을 가져옵니다. 끝점의 예는 다음과 같습니다. `https://my-service-name.search.windows.net`
3. **설정** > **키**에서 API 키를 가져옵니다. 키를 롤오버하려는 경우 중복성을 위한 두 개의 관리자 키가 있습니다. 관리자 키는 인덱스 생성 및 로드에 필요한 서비스에 대한 쓰기 권한을 부여합니다. 쓰기 작업에는 기본 또는 보조 키를 사용할 수 있습니다.

## <a name="configure-request-headers"></a>요청 헤더 구성

각 도구는 세션에 대한 요청 헤더 정보를 유지하므로 URL 끝점, api-version, api-key 및 content-type을 한 번만 입력하면 됩니다.

전체 URL은 다음 예제와 유사하게 표시되어야 하며 **`my-app`** 자리 표시자 이름을 유효하게 대체해야 합니다. `https://my-app.search.windows.net/indexes/hotels?api-version=2017-11-11`

서비스 URL 구성에는 다음 요소가 포함됩니다.

+ HTTPS 접두사.
+ 서비스 URL, 포털에서 확보.
+ 리소스, 서비스에 개체를 만드는 작업. 이 단계에서는 hotels라는 인덱스입니다.
+ api-version, 현재 버전에 대해 “?api-version=2017-11-11”로 지정된 필수 소문자 문자열. [API 버전](search-api-versions.md)은 정기적으로 업데이트됩니다. 각 요청에 api-version을 포함시키면 어느 것이 사용되는지를 완전히 제어할 수 있습니다.  

요청 헤더 구성에는 이전 섹션에서 설명한 content-type과 api-key라는 두 가지 요소가 포함됩니다.

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

다음 스크린샷과 같은 요청을 공식으로 작성합니다. **PUT**을 동사로 선택합니다. Fiddler가 `User-Agent=Fiddler`를 추가합니다. 두 개의 추가 요청 헤더를 그 아래 새 줄에 붙여넣을 수 있습니다. 서비스에 대한 관리자 액세스 키를 사용하여 서비스의 content-type 및 api-key를 포함시킵니다.

![Fiddler 요청 헤더][1]

> [!Tip]
> 수행 중인 작업과 관련이 없는 불필요한 HTTP 작업을 숨기려면 웹 트래픽을 끕니다. Fiddler에서 **파일** 메뉴로 이동하여 **Capture Traffic**(트래픽 캡처)를 끕니다. 

### <a name="postman"></a>postman

다음 스크린샷과 같은 요청을 공식으로 작성합니다. **PUT**을 동사로 선택합니다. 

![Postman 요청 헤더][6]

## <a name="create-the-index"></a>인덱스 만들기

요청 본문에 인덱스 정의가 포함됩니다. 요청 본문을 추가하면 인덱스를 생성하는 요청이 완료됩니다.

인덱스 이름 외에 요청에서 가장 중요한 구성 요소는 필드 컬렉션입니다. 필드 컬렉션은 인덱스 스키마를 정의합니다. 각 필드에 형식을 지정합니다. 문자열 필드는 전체 텍스트 검색에 사용되므로 콘텐츠를 검색할 수 있도록 하려면 숫자 데이터를 문자열로 캐스팅해야 합니다.

필드의 특성에 따라 허용되는 작업이 결정됩니다. REST API는 기본적으로 많은 작업을 허용합니다. 예를 들어, 모든 문자열은 기본적으로 검색, 조회, 필터링이 가능하고 패싯이 가능합니다. 동작을 꺼야 할 때만 특성을 설정해야 하는 경우가 많습니다. 특성에 대한 자세한 내용은 [인덱스 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }


이 요청을 제출할 때 인덱스가 성공적으로 생성되었음을 나타내는 HTTP 201 응답을 받아야 합니다. 이 작업은 포털에서 확인할 수 있지만 포털 페이지에 새로 고침 간격이 있으므로 그 때까지 1~2분이 걸릴 수 있습니다.

HTTP 504가 표시될 경우 URL이 HTTPS를 지정하는지 확인합니다. HTTP 400 또는 404가 표시되는 경우 요청 본문에서 복사/붙여 넣기 오류가 없는지 확인합니다. HTTP 403은 대개 api-key에 문제가 있음을 나타냅니다(잘못된 키 또는 api-key 지정 방법과 관련된 구문 문제).

### <a name="fiddler"></a>Fiddler

다음 스크린샷과 유사하게 인덱스 정의를 요청 본문에 복사한 다음 오른쪽 상단의 **실행**을 클릭하여 완료된 요청을 보냅니다.

![Fiddler 요청 본문][7]

### <a name="postman"></a>postman

다음 스크린샷과 유사하게 인덱스 정의를 요청 본문에 복사한 다음 오른쪽 상단의 **보내기**를 클릭하여 완료된 요청을 보냅니다.

![Postman 요청 본문][8]

## <a name="load-documents"></a>문서 로드

인덱스 생성과 인덱스 채우기는 별도의 단계입니다. Azure Search 검색에서 인덱스에는 JSON 문서로 제공할 수 있는 검색 가능한 모든 데이터가 포함됩니다. 이 작업에 대한 API를 검토하려면 [문서 추가, 업데이트 또는 삭제(REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 참조하세요.

+ 이 단계에서 동사를 **POST**로 변경합니다.
+ `/docs/index`를 포함하도록 끝점을 변경합니다. 전체 URL은 다음과 같습니다. `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`
+ 요청 헤더를 그대로 유지합니다. 

요청 본문에 호텔 인덱스에 추가될 문서 4개가 포함됩니다.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

몇 초 후에 HTTP 200 응답이 검색 목록에 표시됩니다. 이는 문서가 성공적으로 만들어졌음을 나타냅니다. 

207이 표시될 경우 하나 이상의 문서를 업로드하지 못했습니다. 404가 표시될 경우 요청의 헤더 또는 본문에 구문 오류가 있습니다. `/docs/index`를 포함하도록 끝점을 변경했는지 확인하십시오.

> [!Tip]
> 선택한 데이터 원본의 경우 인덱싱에 필요한 코드의 양을 줄이고 단순화할 수 있는 대체 *인덱서* 방식을 선택할 수 있습니다. 자세한 내용은 [인덱서 작업](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)을 참조하세요.

### <a name="fiddler"></a>Fiddler

동사를 **POST**로 변경합니다. `/docs/index`를 포함하도록 URL을 변경합니다. 다음 스크린샷과 유사하게 문서를 요청 본문에 복사한 다음 요청을 실행합니다.

![Fiddler 요청 페이로드][9]

### <a name="postman"></a>postman

동사를 **POST**로 변경합니다. `/docs/index`를 포함하도록 URL을 변경합니다. 다음 스크린샷과 유사하게 문서를 요청 본문에 복사한 다음 요청을 실행합니다.

![Postman 요청 페이로드][10]

## <a name="query-the-index"></a>인덱스 쿼리
이제 인덱스와 문서가 로드되었으므로 쿼리를 실행할 수 있습니다. 이 API에 대한 자세한 내용은 [문서 검색(REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents)을 참조하세요.  

+ 이 단계에서 동사를 **GET**로 변경합니다.
+ 검색 문자열을 포함하여 쿼리 매개 변수를 포함하도록 끝점을 변경합니다. 쿼리 URL은 다음과 같습니다. `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ 요청 헤더를 그대로 유지합니다.

이 쿼리는 "motel"이라는 용어를 검색하고 검색 결과에 문서의 수를 반환합니다. 요청 및 응답은 **보내기**를 클릭한 후 Postman의 다음 스크린샷과 유사합니다. 상태 코드는 200이어야 합니다.

 ![Postman 쿼리 응답][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Fiddler에서 샘플 쿼리를 실행하는 팁

다음 예제 쿼리는 [검색 인덱스 작업(Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 문서에서 가져온 것입니다. 이 문의 많은 예제 쿼리에는 공백이 포함되어 있으며, 공백은 Fiddler에서 허용되지 않습니다. Fiddler에서 쿼리를 시도하기에 앞서, 쿼리 문자열을 붙여 넣기 전에 각 공백을 + 문자로 바꾸세요.

**공백이 교체되기 전(lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**공백이 +로 교체된 후(lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

## <a name="query-index-properties"></a>쿼리 인덱스 속성
시스템을 쿼리하여 문서 수와 저장소 사용량을 가져올 수도 있습니다. `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

Postman에서 요청은 다음과 유사하며 응답에는 문서 수와 사용된 공간(바이트 단위)이 포함됩니다.

 ![Postman 시스템 쿼리][12]

api-version 구문이 다른 점에 유의하세요. 이 요청의 경우 `?`를 사용하여 api-version을 추가합니다. ?는 쿼리 문자열에서 URL 경로를 구분하고 &는 쿼리 문자열에서 각 ‘name=value’ 쌍을 구분합니다. 이 쿼리에서 api-version은 쿼리 문자열의 처음이자 유일한 항목입니다.

이 API에 대한 자세한 내용은 [인덱스 통계 가져오기(REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics)를 참조하세요.


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Fiddler에서 인덱스 통계를 보는 팁

Fiddler에서 **Inspectors**(검사기) 탭과 **헤더** 탭을 차례로 클릭하고 JSON 형식을 선택합니다. 문서 수와 저장소 크기(KB)가 표시됩니다.

## <a name="next-steps"></a>다음 단계

REST 클라이언트는 즉석 탐색에 매우 유용하지만 이제 REST API가 어떻게 작동하는지 알게 되었니 코드로 진행할 수 있습니다. 다음 단계는 다음 링크를 참조하세요.

+ [인덱스 만들기(REST)](search-create-index-rest-api.md)
+ [데이터 가져오기(REST)](search-import-data-rest-api.md)
+ [인덱스 검색(REST)](search-query-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png