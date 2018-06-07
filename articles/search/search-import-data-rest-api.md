---
title: 데이터 업로드(REST API - Azure Search) | Microsoft Docs
description: REST API를 사용하여 Azure Search에서 인덱스에 데이터를 업로드하는 방법에 대해 알아봅니다.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.openlocfilehash: 53b20c9db7efe1f8876eec7c0167dc151aa38786
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>REST API를 사용하여 Azure Search에 데이터 업로드
> [!div class="op_single_selector"]
>
> * [개요](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST (영문)](search-import-data-rest-api.md)
>
>

이 문서에서는 [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) 를 사용하여 Azure Search 인덱스에 데이터를 가져오는 방법을 보여줍니다.

이 연습을 시작하기 전에 [Azure Search 인덱스를 만들어야](search-what-is-an-index.md)합니다.

REST API를 사용하여 인덱스에 문서를 푸시하기 위해 인덱스의 URL 끝점에 HTTP 게시 요청을 발급합니다. HTTP 요청 본문의 본문은 문서의 추가, 수정 또는 삭제를 포함하는 JSON 개체입니다.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Azure Search 서비스의 관리 API 키 식별
REST API를 사용하여 서비스에 대한 HTTP 요청을 발급하는 경우 *각* API 요청은 프로비전한 Search 서비스에 생성된 API 키를 포함해야 합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 응용 프로그램과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하면 서비스의 API 키를 찾을 수 있습니다.
2. Azure Search 서비스의 블레이드로 이동합니다.
3. "키" 아이콘을 클릭합니다.

서비스에는 *관리 키* 및 *쿼리 키*가 있습니다.

* 기본 및 보조 *관리 키* 는 서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다. 두 개의 키가 있으므로 기본 키를 다시 생성하려는 경우 보조 키를 사용하여 계속할 수 있고 반대도 가능합니다.
* *쿼리 키* 는 인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 실행하는 클라이언트 응용 프로그램에 배포됩니다.

인덱스에 데이터를 가져오기 위해 기본 또는 보조 관리 키를 사용할 수 있습니다.

## <a name="decide-which-indexing-action-to-use"></a>사용할 인덱싱 동작 결정
REST API를 사용하는 경우 Azure Search 인덱스의 끝점 URL에 대한 JSON 요청 본문을 사용하여 HTTP POST 요청을 발급합니다. HTTP 요청 본문의 JSON 개체는 인덱스, 업데이트 또는 삭제에 추가하려는 문서를 나타내는 JSON 개체를 포함하는 "값"이라는 단일 JSON 배열을 포함합니다.

"값" 배열의 각 JSON 개체는 인덱싱할 문서를 나타냅니다. 이러한 각 개체는 문서의 키를 포함하고 원하는 인덱싱 작업(업로드, 병합, 삭제 등)을 지정합니다. 아래에서 어떤 작업을 선택하는지에 따라 특정 필드는 각 문서에 포함되어야 합니다.

| @search.action | 설명 | 각 문서에 대해 필요한 필드 | 메모 |
| --- | --- | --- | --- |
| `upload` |`upload` 작업은 새 문서는 삽입하고 기존 문서는 업데이트/교체하는 "upsert"와 비슷합니다. |키, 더하기 정의하려는 기타 필드 |기존 문서를 업데이트/교체하는 경우 요청에 지정되지 않은 필드는 해당 필드를 `null`로 설정합니다. 필드가 이전에 null이 아닌 값으로 설정된 경우에 발생합니다. |
| `merge` |기존 문서를 지정한 필드로 업데이트합니다. 인덱스에 문서가 없으면 병합이 실패합니다. |키, 더하기 정의하려는 기타 필드 |문서의 기존 필드는 병합에서 지정하는 필드로 바뀝니다. 여기에는 `Collection(Edm.String)`형식 필드가 포함됩니다. 예를 들어 값이 `["budget"]`인 `tags` 필드가 포함되어 있는 문서에서 `tags`에 대해 `["economy", "pool"]` 값과의 병합을 실행하면 `tags` 필드의 최종 값은 `["economy", "pool"]`이 됩니다. `["budget", "economy", "pool"]`이 아닙니다. |
| `mergeOrUpload` |이 작업은 지정된 키를 포함하는 문서가 인덱스에 이미 있는 경우 `merge`와 비슷하게 작동합니다. 문서가 없는 경우 새 문서가 있는 `upload` 와 비슷하게 작동합니다. |키, 더하기 정의하려는 기타 필드 |- |
| `delete` |삭제 시에는 지정된 문서를 인덱스에서 제거합니다. |키만 |키 필드 외의 지정한 모든 필드는 무시됩니다. 문서에서 개별 필드를 제거하려는 경우 대신 `merge` 를 사용하고 필드를 명시적으로 Null로 설정합니다. |

## <a name="construct-your-http-request-and-request-body"></a>HTTP 요청 및 요청 본문 생성
인덱스 작업에 필요한 필드 값을 수집했다면 실제 HTTP 요청 및 JSON 요청 본문을 생성하여 데이터를 가져올 준비가 되었습니다.

#### <a name="request-and-request-headers"></a>요청 및 요청 헤더
URL에서 서비스 이름, 인덱스 이름(이 경우 "호텔") 뿐만 아니라 적절한 API 버전을 제공해야 합니다(이 문서를 게시할 때 현재 API 버전은 `2017-11-11` 임). `Content-Type` 및 `api-key` 요청 헤더를 정의해야 합니다. 후자의 경우 서비스의 관리 키 중 하나를 사용합니다.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>요청 본문
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
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
            "description_fr": "Hôtel le moins cher en ville",
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

이 경우 `upload`, `mergeOrUpload` 및 `delete`를 검색 작업으로 사용합니다.

이 예제 "호텔" 인덱스는 문서 수로 채워진다고 가정합니다. `mergeOrUpload`를 사용하는 경우 가능한 문서 필드를 모두 지정하지 않는 방법 및 `delete`를 사용하는 경우 문서 키(`hotelId`)를 지정하는 방법을 참고합니다.

또한 단일 인덱싱 요청에서 1000개의 문서(또는 16MB)까지 포함할 수 있습니다.

## <a name="understand-your-http-response-code"></a>HTTP 응답 코드 이해
#### <a name="200"></a>200
성공적인 인덱싱 요청을 제출한 후에 `200 OK`라는 상태 코드로 HTTP 응답을 받게됩니다. HTTP 응답의 JSON 본문은 다음과 같습니다.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
하나 이상의 항목이 성공적으로 인덱싱되지 않은 경우 상태 코드인 `207` 이 반환됩니다. HTTP 응답의 JSON 본문은 실패한 문서에 대한 정보를 포함합니다.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> 대개는 검색 서비스에 대한 부하가 인덱싱 요청이 `503` 응답을 반환하기 시작하는 지점에 도달한 것을 의미합니다. 이 경우 다시 시도하기 전에 클라이언트 코드를 백오프하고 대기하는 것이 좋습니다. 이렇게 하면 시스템이 복구할 시간을 제공하여 이후 요청이 성공할 가능성이 높아집니다. 신속하게 요청을 다시 시도하면 이 상황만 연장됩니다.
>
>

#### <a name="429"></a>429
인덱스당 문서 수의 할당량이 초과된 경우 상태 코드 `429` 가 반환됩니다.

#### <a name="503"></a>503
요청에서 어떤 항목도 성공적으로 인덱싱되지 않는 경우 상태 코드 `503` 이 반환됩니다. 이 오류는 시스템의 사용량이 많아 지금 요청을 처리할 수 없다는 것을 나타냅니다.

> [!NOTE]
> 이 경우 다시 시도하기 전에 클라이언트 코드를 백오프하고 대기하는 것이 좋습니다. 이렇게 하면 시스템이 복구할 시간을 제공하여 이후 요청이 성공할 가능성이 높아집니다. 신속하게 요청을 다시 시도하면 이 상황만 연장됩니다.
>
>

문서 동작 및 성공/오류 응답에 대한 자세한 내용은 [문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)를 참조하세요. 오류가 발생한 경우 반환될 수 있는 기타 HTTP 상태 코드에 대한 자세한 내용은 [HTTP 상태 코드(Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Search 인덱스를 채운 후에 문서를 검색하기 위해 쿼리를 발급하기 시작할 준비가 되었습니다. 세부 정보는 [Azure Search 인덱스 쿼리](search-query-overview.md) 를 참조하세요.
