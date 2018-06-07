---
title: Azure Search BLOB 인덱서를 사용하여 JSON BLOB 인덱싱
description: Azure Search BLOB 인덱서를 사용하여 JSON BLOB 인덱싱
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 752df29200a5e020ccf10f511ae2f02c0d72bd48
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Azure Search BLOB 인덱서를 사용하여 JSON BLOB 인덱싱
이 문서에서는 Azure Blob Storage의 JSON Blob에서 구조화된 콘텐츠를 추출하도록 Azure Search Blob 인덱서를 구성하는 방법을 보여줍니다.

Azure Blob Storage에서 JSON Blob은 일반적으로 단일 JSON 문서 또는 JSON 배열입니다. Azure Search에서 Blob 인덱서는 요청에 대한 **parsingMode** 매개 변수를 설정하는 방법에 따라 생성을 구문 분석할 수 있습니다.

| JSON 문서 | parsingMode | 설명 | 가용성 |
|--------------|-------------|--------------|--------------|
| Blob 당 하나 | `json` | JSON Blob을 텍스트의 단일 청크로 구문 분석합니다. 각 JSON Blob은 단일 Azure Search 문서가 됩니다. | 일반적으로 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 및 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 모두에서 사용할 수 있습니다. |
| Blob 당 여러 개 | `jsonArray` | Blob에서 JSON 배열을 구문 분석합니다. 여기서 배열의 각 요소는 별도의 Azure Search 문서가 됩니다.  | 미리 보기에서 [REST api-version=`2017-11-11-Preview`](search-api-2017-11-11-preview.md) 및 [.NET SDK 미지 보기](https://aka.ms/search-sdk-preview)입니다. |

> [!Note]
> 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않습니다.
>

## <a name="setting-up-json-indexing"></a>JSON 인덱싱 설정
JSON Blob을 인덱싱하는 작업은 Azure Search에 있는 모든 인덱서에 공통되는 세 부분으로 구성된 워크플로의 일반 문서 추출과 비슷합니다.

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

첫 번째 단계는 인덱서에서 사용하는 데이터 원본 연결 정보를 제공하는 것입니다. `azureblob`으로 지정된 데이터 원본 형식은 인덱서에 의해 호출되는 데이터 추출 동작을 결정합니다. JSON Blob 인덱싱의 경우 데이터 원본 정의는 JSON 문서 및 배열 모두에서 동일합니다. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>2단계: 대상 검색 인덱스 만들기 

인덱서는 인덱스 스키마과 쌍을 이룹니다. 포털이 아닌 API를 사용하는 경우 인덱서 작업에서 지정할 수 있도록 사전에 인덱스를 준비합니다. 

> [!Note]
> 일반적으로 사용할 수 있는 제한된 수의 인덱서의 경우 인덱서는 **가져오기** 작업을 통해 포털에서 노출됩니다. 가져오기 워크플로를 사용하여 원본의 메타데이터에 따라 준비 인덱스를 만들 수 있습니다. 자세한 내용은 [포털에서 Azure Search로 데이터 가져오기](search-import-data-portal.md)를 참조하세요.

### <a name="step-3-configure-and-run-the-indexer"></a>3단계: 인덱서 구성 및 실행

지금까지 데이터 원본 및 인덱스에 대한 정의는 parsingMode와 독립적이었습니다. 그러나 인덱서 구성 3단계의 경로는 Azure Search 인덱스에서 JSON Blob 콘텐츠를 구문 분석하고 구성하려는 방법에 따라 달라집니다.

인덱서를 호출할 때 다음을 수행합니다.

+ **parsingMode** 매개 변수를 `json`(각 blob을 단일 문서로 인덱싱) 또는 `jsonArray`(blob에 JSON 배열이 포함되어 있고 각 배열 요소를 별도 문서로 처리해야 하는 경우)로 설정합니다.

+ 필요한 경우 **필드 매핑**을 사용하여 대상 검색 인덱스를 채우는 데 사용되는 원본 JSON 문서의 속성을 선택합니다. JSON 배열의 경우 하위 수준 속성인 배열이 있으면 Blob 내에서 배열의 위치를 나타내는 문서 루트를 설정할 수 있습니다.

> [!IMPORTANT]
> `json` 또는 `jsonArray` 구문 분석 모드를 사용하는 경우 Azure Search는 데이터 원본의 모든 blob이 JSON을 포함한다고 가정합니다. 동일한 데이터 원본에서 JSON 및 비 JSON BLOB을 지원해야 하는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search)를 통해 알려주세요.


## <a name="how-to-parse-single-json-blobs"></a>단일 JSON Blob을 구문 분석하는 방법

기본적으로 [Azure Search BLOB 인덱서](search-howto-indexing-azure-blob-storage.md) 는 단일 텍스트 청크로 JSON BLOB을 구문 분석합니다. JSON 문서의 구조를 유지하려는 경우가 많습니다. 예를 들어 Azure Blob Storage에 다음 JSON 문서가 있다고 가정합니다.

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>단일 JSON Blob의 인덱서 정의

Azure Search Blob 인덱서를 사용하여 이전 예제와 비슷한 JSON 문서를 단일 Azure Search 문서로 구문 분석합니다. 인덱서는 동일하게 명명되고 형식이 지정된 대상 필드에 대해 원본의 "text", "datePublished" 및 "tags"를 일치시켜 인덱스를 로드합니다.

구성은 인덱서 작업의 본문에 제공됩니다. 이전에 정의된 데이터 원본 개체를 호출하고, 데이터 원본 형식과 연결 정보를 지정합니다. 서비스에서 빈 컨테이너인 대상 인덱스도 있어야 합니다. 일정 및 매개 변수는 선택 사항이지만 생략하는 경우 `json`을 구문 분석 모드로 사용하여 인덱서가 즉시 실행됩니다.

전체 지정된 요청은 다음과 같이 표시될 수 있습니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

명시된 대로 필드 매핑이 필요하지 않습니다. "text", "datePublished" 및 "tags" 필드를 포함하는 인덱스가 지정된 Blob 인덱서는 요청에서 필드 매핑 없이 올바른 매핑을 유추할 수 있습니다.

## <a name="how-to-parse-json-arrays-preview"></a>JSON 배열(미리 보기)을 구문 분석하는 방법

또는 JSON 배열 미리 보기 기능을 선택할 수 있습니다. 이 기능은 Blob에 *JSON 개체 배열*이 포함되고 각 배열 요소를 별도의 Azure Search 문서로 나타내려고 하는 경우에 유용합니다. 예를 들어 다음 JSON Blob의 경우 각각 "id" 및 "text" 필드가 있는 별도의 3개 문서로 Azure Search 인덱스를 채울 수 있습니다.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>JSON 배열의 인덱서 정의

인덱서 요청은 JSON 배열에 미리 보기 API 및 `jsonArray` 파서를 사용합니다. JSON Blob을 인덱싱하는 두 개의 배열 관련 요구 사항은 다음과 같습니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

다시 말하지만 필드 매핑이 필요하지 않습니다. "id" 및 "text" 필드를 포함하는 인덱스가 지정된 Blob 인덱서는 필드 매핑 목록 없이 올바른 매핑을 유추할 수 있습니다.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>중첩된 JSON 배열
JSON 개체의 배열을 인덱싱하려고 하지만 해당 배열이 문서 내에 중첩되어 있으면 어떻게 할까요? `documentRoot` 구성 속성을 사용하여 배열을 포함하는 속성을 선택할 수 있습니다. 예를 들어 blob은 다음과 같습니다.

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

다음 구성을 사용하여 `level2` 속성에 포함된 배열을 인덱싱합니다.

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>필드 매핑을 사용하여 검색 문서 빌드

원본 및 대상 필드가 완벽하게 정렬되지 않은 경우 명시적 필드 간 연결을 위해 요청 본문에서 필드 매핑 섹션을 정의할 수 있습니다.

현재 Azure Search는 기본 데이터 형식, 문자열 배열 및 GeoJSON 포인트만 지원하므로 임의의 JSON 문서를 직접 인덱싱할 수 없습니다. 그러나 **필드 매핑** 을 사용하여 JSON 문서의 부분을 선택하고 검색 문서의 최상위 필드로 이 부분을 "올릴" 수 있습니다. 필드 매핑 기본 사항에 대한 자세한 내용은 [Azure Search 인덱서의 필드 매핑](search-indexer-field-mappings.md)을 참조하세요.

예제 JSON 문서 다시 방문:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

`Edm.String` 형식의 `text`, `Edm.DateTimeOffset` 형식의 `date` 및 `Collection(Edm.String)` 형식의 `tags` 필드를 포함하는 검색 인덱스가 있다고 가정해 봅니다. 원본의 "datePublished"와 인덱스의 `date` 필드 간에 불일치를 확인합니다. JSON을 원하는 모양으로 매핑하려면 다음 필드 매핑을 사용합니다.

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

매핑의 원본 필드 이름은 [JSON 포인터](http://tools.ietf.org/html/rfc6901) 표기법을 사용하여 지정됩니다. JSON 문서의 루트를 참조하도록 슬래시로 시작한 다음 슬래시로 구분된 경로를 사용하여 (임의의 중첩 수준에서) 원하는 속성을 선택합니다.

또한 0부터 시작하는 인덱스를 사용하여 개별 배열 요소를 참조할 수 있습니다. 예를 들어 위의 예제에서 "tags" 배열의 첫 번째 요소를 선택하려면 다음과 같은 필드 매핑을 사용합니다.

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 필드 매핑 경로의 원본 필드 이름이 JSON에 없는 속성을 참조하는 경우 해당 매핑은 오류 없이 건너뜁니다. 다른 스키마를 사용하여 문서를 지원할 수 있도록 이 작업을 수행합니다(일반적인 사용 사례). 유효성을 검사하지 않기 때문에 필드 매핑 사양에 오타가 발생하지 않도록 주의해야 합니다.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>예: 필드 매핑을 포함한 인덱서 요청

필드 매핑을 비롯하여 완전히 지정된 인덱서 페이로드는 다음과 같습니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Azure Search 개선 지원
기능 요청 또는 개선에 대한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 연락해 주세요.

## <a name="see-also"></a>참고 항목

+ [Azure Search의 인덱서](search-indexer-overview.md)
+ [Azure Search로 Azure Blob Storage 인덱싱](search-howto-index-json-blobs.md)
+ [Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
+ [자습서: Azure Blob Storage에서 반구조화된 데이터 검색](search-semi-structured-data.md)