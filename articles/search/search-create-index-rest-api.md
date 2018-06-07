---
title: 인덱스 만들기(REST API - Azure Search) | Microsoft Docs
description: Azure Search HTTP REST API를 사용하여 코드에 인덱스를 만듭니다.
author: chaosrealm
manager: jlembicz
ms.author: eugenesh
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.openlocfilehash: 86260e1929e8bd5c31682ef7f59cacae19a66020
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>REST API를 사용하여 Azure Search 인덱스 만들기
> [!div class="op_single_selector"]
>
> * [개요](search-what-is-an-index.md)
> * [포털](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST (영문)](search-create-index-rest-api.md)
>
>

이 문서는 Azure Search REST API를 사용하여 Azure Search [인덱스](https://docs.microsoft.com/rest/api/searchservice/Create-Index) 를 만드는 프로세스를 안내합니다.

이 가이드를 수행하고 인덱스를 만들기 전에 이미 [Azure Search 서비스를 만들어야](search-create-service-portal.md)합니다.

REST API를 사용하여 Azure Search 인덱스를 만들려면 Azure Search 서비스의 URL 끝점에 단일 HTTP 게시 요청을 발행합니다. 인덱스 정의는 올바른 형식의 JSON 콘텐츠로 요청 본문에 포함됩니다.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Azure Search 서비스의 관리 API 키 식별
Azure Search 서비스를 프로비전했다면 REST API를 사용하여 서비스의 URL 끝점에 대한 HTTP 요청을 실행할 수 있습니다. *모든* API 요청은 프로비전된 Search 서비스에 대해 생성된 API 키를 포함해야 합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 응용 프로그램과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

1. 서비스의 API 키를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인해야 합니다.
2. Azure Search 서비스의 블레이드로 이동합니다.
3. "키" 아이콘을 클릭합니다.

서비스에는 *관리 키* 및 *쿼리 키*가 있습니다.

* 기본 및 보조 *관리 키* 는 서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다. 두 개의 키가 있으므로 기본 키를 다시 생성하려는 경우 보조 키를 사용하여 계속할 수 있고 반대도 가능합니다.
* *쿼리 키* 는 인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 실행하는 클라이언트 응용 프로그램에 배포됩니다.

인덱스를 만들기 위해 기본 또는 보조 관리 키를 사용할 수 있습니다.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>올바른 형식의 JSON 형식을 사용하여 Azure Search 인덱스 정의
서비스에 대한 단일 HTTP 게시 요청은 인덱스를 만듭니다. HTTP 게시 요청의 본문은 Azure Search 인덱스를 정의하는 단일 JSON 개체를 포함합니다.

1. 이 JSON 개체의 첫 번째 속성은 인덱스의 이름입니다.
2. 이 JSON 개체의 두 번째 속성은 인덱스의 각 필드에 대한 별도 JSON 개체를 포함하는 `fields` 라는 JSON 배열입니다. 이러한 JSON 개체 각각은 "이름", "형식" 등을 비롯한 각 필드 특성에 여러 이름/값 쌍을 포함합니다.

인덱스를 각 필드로 디자인하는 경우 검색 사용자 환경 및 비즈니스 요구를 [적절한 특성](https://docs.microsoft.com/rest/api/searchservice/Create-Index)으로 할당해야 한다는 점을 염두에 두는 것이 중요합니다. 이러한 특성은 어떤 검색 기능(전체 텍스트 검색의 필터링, 패싯, 정렬 등)이 어떤 필드에 적용될지를 제어합니다. 지정하지 않은 특성의 경우 구체적으로 비활성화하지 않는 한 기본값은 해당 검색 기능을 사용합니다.

예를 들어 인덱스 "호텔"의 이름을 지정하고 필드를 다음과 같이 정의했습니다.

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

응용 프로그램에서 사용되는 방법에 따라 각 필드에 대한 인덱스 특성을 신중하게 선택했습니다. 예를 들어 `hotelId`는 호텔을 검색하는 사람들이 알 수 없는 고유한 키이므로 `searchable`을 `false`로 설정하여 해당 필드에 대한 전체 텍스트 검색 비활성화하여 인덱스의 공간을 절약합니다.

형식 `Edm.String`의 인덱스에 정확히 하나의 필드가 '키' 필드로 지정되어야 합니다.

위의 인덱스 정의는 프랑스어 텍스트를 저장하기 위해서 `description_fr` 필드에 언어 분석기를 사용합니다. 언어 분석기에 대한 자세한 내용은 [언어 지원 항목](https://docs.microsoft.com/rest/api/searchservice/Language-support)뿐만 아니라 해당하는 [블로그 게시물](https://azure.microsoft.com/blog/language-support-in-azure-search/)을 참조하세요.

## <a name="issue-the-http-request"></a>HTTP 요청 발급
1. 인덱스 정의를 요청 본문으로 사용하여 Azure Search 서비스 끝점 URL에 HTTP 게시 요청을 발급합니다. URL에 서비스 이름을 호스트 이름으로 사용하고 적절한 `api-version`을 쿼리 문자열 매개 변수로 배치합니다(현재 API 버전은 이 문서를 게시할 때 `2017-11-11`임).
2. 요청 헤더에서 `Content-Type`을 `application/json`으로 지정합니다. `api-key` 헤더의 I 단계에서 식별하는 서비스의 관리 키를 제공해야 합니다.

아래와 같이 요청을 실행할 고유한 서비스 이름 및 api 키를 제공해야 합니다.

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [api-key]


성공적인 요청의 경우 상태 코드 201(생성됨)이 표시되어야 합니다. REST API를 통해 인덱스를 만드는 방법에 대한 자세한 내용은 [여기서 API 참조](https://docs.microsoft.com/rest/api/searchservice/Create-Index)를 방문합니다. 오류가 발생한 경우 반환될 수 있는 기타 HTTP 상태 코드에 대한 자세한 내용은 [HTTP 상태 코드(Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)를 참조하세요.

인덱스 관련 작업을 완료하고 삭제하려는 경우 HTTP 삭제 요청을 발급합니다. 예를 들어 "호텔" 인덱스를 삭제하는 방법입니다.

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11
    api-key: [api-key]


## <a name="next-steps"></a>다음 단계
Azure Search 인덱스를 만든 후에 데이터를 검색하기 시작할 수 있도록 [콘텐츠를 인덱스에 업로드](search-what-is-data-import.md) 할 준비가 되었습니다.
