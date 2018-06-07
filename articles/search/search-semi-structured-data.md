---
title: Azure 클라우드 저장소에서 반구조화된 데이터 검색
description: Azure Search를 사용하여 반구조화된 BLOB 데이터 검색.
author: roygara
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.openlocfilehash: 7579862e132724d101e4267023afd9e3336bc3b1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="part-2-search-semi-structured-data-in-cloud-storage"></a>2부: 클라우드 저장소에 반구조화된 데이터 검색

두 부분으로 구성된 이 자습서에서는 Azure Search를 사용하여 반구조화된 데이터 및 구조화되지 않은 데이터를 검색하는 방법을 배웁니다. [1부](../storage/blobs/storage-unstructured-search.md)에서는 구조화되지 않은 데이터를 검색하는 단계를 설명하며, 저장소 계정 만들기와 같이 이 자습서에 중요한 필수 구성 요소도 포함되어 있습니다. 

2부에서는 Azure Blob에 저장되는 JSON과 같은 반구조화 데이터에 중점을 둡니다. 반구조화된 데이터에는 데이터 내의 콘텐츠를 구분하는 태그 또는 표시가 포함되어 있습니다. 전체적으로 인덱싱해야 하는 구조화되지 않은 데이터와 필드 단위를 기반으로 크롤링할 수 있는 데이터 모델(예: 관계형 데이터베이스 스키마)을 준수하는 형식으로 구조화된 데이터의 차이를 구분합니다.

2부에서는 다음과 같은 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Blob 컨테이너에 대한 Azure Search 데이터 원본 구성
> * 컨테이너를 크롤링하고 검색 가능한 콘텐츠를 추출하는 Azure Search 인덱스 및 인덱서를 만들고 채우기
> * 방금 만든 인덱스 검색

> [!NOTE]
> 이 자습서에서는 현재 Azure Search의 미리 보기 기능인 JSON 배열 지원을 사용합니다. 포털에서는 사용할 수 없습니다. 이러한 이유로 이 기능을 제공하는 미리 보기 REST API와 API를 호출하는 REST 클라이언트 도구가 사용됩니다.

## <a name="prerequisites"></a>필수 조건

* 저장소 계정 및 검색 서비스 만들기를 제공하는 [이전 자습서](../storage/blobs/storage-unstructured-search.md) 완료.

* REST 클라이언트 설치 및 HTTP 요청을 구성하는 방법에 대한 이해. 이 자습서에서는 [Postman](https://www.getpostman.com/)을 사용하고 있습니다. 특정 REST 클라이언트를 이미 익숙하게 사용하고 있다면 다른 REST 클라이언트를 자유롭게 사용할 수 있습니다.

## <a name="set-up-postman"></a>Postman 설정

Postman을 시작하고 HTTP 요청을 설정합니다. 이 도구가 생소한 경우 [Fiddler 또는 Postman을 사용하여 Azure Search REST API 살펴보기](search-fiddler.md)에서 자세한 내용을 참조하세요.

이 자습서의 모든 호출에 대한 요청 메서드는 "POST"입니다. 헤더 키는 "Content-type" 및 "api-key"입니다. 헤더 키의 값은 각각 "application/json"과 "관리자 키"(관리자 키는 검색 기본 키의 자리 표시자임)입니다. 본문은 호출의 실제 콘텐츠가 배치되는 위치입니다. 사용 중인 클라이언트에 따라 쿼리를 구성하는 방법에 약간의 차이가 있을 수 있지만 일반적으로 기본 사항입니다.

  ![반구조화된 검색](media/search-semi-structured-data/postmanoverview.png)

이 자습서에서 다루는 REST 호출의 경우 검색 api-key가 필요합니다. 검색 서비스 내부의 **키** 아래에서 api-key를 찾을 수 있습니다. 이 api-key는 이 자습서가 만들도록 지시하는 모든 API 호출의 헤더에 있어야 합니다(이전 스크린샷의 "관리자 키"를 이 키로 대체). 키는 각 호출에 대해 필요하기 때문에 보관합니다.

  ![반구조화된 검색](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>샘플 파일 다운로드

샘플 데이터 집합이 준비되어 있습니다. **[clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)을 다운로드하고** 해당 폴더에 압축을 풉니다.

샘플에는 예제 JSON 파일이 들어 있습니다. 이 파일은 원래 [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)에서 가져온 텍스트 파일이었습니다. 편의를 위해 JSON으로 변환했습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Azure 포털](http://portal.azure.com) 에 로그인합니다.

## <a name="upload-the-sample-data"></a>샘플 파일 업로드

Azure Portal에서, [이전 자습서](../storage/blobs/storage-unstructured-search.md)에서 만든 저장소 계정으로 다시 이동합니다. 그런 다음 **데이터** 컨테이너를 열고 **업로드**를 클릭합니다.

**고급**을 클릭하고 "clinical-trials-json"을 입력한 다음 다운로드한 JSON 파일을 모두 업로드합니다.

  ![반구조화된 검색](media/search-semi-structured-data/clinicalupload.png)

업로드가 완료되면 파일이 데이터 컨테이너의 하위 폴더에 나타납니다.

## <a name="connect-your-search-service-to-your-container"></a>검색 서비스를 컨테이너에 연결

Postman을 통해 데이터 원본, 인덱스 및 인덱서를 만들기 위해 검색 서비스에 대한 API 호출을 세 번 수행합니다. 데이터 원본은 저장소 계정 및 JSON 데이터에 대한 포인터를 포함합니다. 검색 서비스는 데이터를 로드할 때 연결합니다.

쿼리 문자열은 **api-version=2016-09-01-Preview**를 포함해야 하며 각 호출은 **201 Created**를 반환해야 합니다. 일반적으로 사용할 수 있는 api-version에는 아직 JSON을 jsonArray로 처리할 수 있는 기능이 없고 현재는 미리 보기 api-version만 있습니다.

REST 클라이언트에서 다음 세 가지 API 호출을 실행합니다.

### <a name="create-a-datasource"></a>데이터 원본 만들기

데이터 원본은 인덱스할 데이터를 지정합니다.

이 호출의 끝점은 `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`입니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

이 호출의 경우 스토리지 계정의 이름과 스토리지 계정 키가 필요합니다. 스토리지 계정 키는 Azure Portal에서 스토리지 계정의 **액세스 키** 내에서 찾을 수 있습니다. 위치가 다음 그림에 표시됩니다.

  ![반구조화된 검색](media/search-semi-structured-data/storagekeys.png)

호출 본문에서 `[storage account name]` 및 `[storage account key]`를 바꾼 다음 호출을 실행해야 합니다.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

응답은 다음과 같아야 합니다.

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### <a name="create-an-index"></a>인덱스 만들기
    
두 번째 API 호출에서 인덱스를 만듭니다. 인덱스는 모든 매개 변수 및 해당 특성을 지정합니다.

이 호출에 대한 URL은 `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`입니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

먼저 URL을 바꿉니다. 그런 후 다음 코드를 복사하여 본문에 붙여넣고 쿼리를 실행합니다.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

응답은 다음과 같아야 합니다.

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### <a name="create-an-indexer"></a>인덱서 만들기

인덱서는 데이터 원본을 대상 검색 인덱스에 연결하고 필요에 따라 데이터 새로 고침을 자동화하는 일정을 제공합니다.

이 호출에 대한 URL은 `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`입니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

먼저 URL을 바꿉니다. 그런 후 다음 코드를 복사하여 본문에 붙여넣고 쿼리를 실행합니다.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

응답은 다음과 같아야 합니다.

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>JSON 파일 검색

이제 검색 서비스가 데이터 컨테이너에 연결되었으므로 파일 검색을 시작할 수 있습니다.

Azure Portal을 열고 검색 서비스로 다시 이동합니다. 이전 자습서에서 했던 대로 수행합니다.

  ![구조화되지 않은 검색](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>사용자 정의 메타데이터 검색

이전과 마찬가지로 전체 텍스트 검색, 시스템 속성 또는 사용자 정의 메타데이터 등 여러 가지 방법으로 데이터를 쿼리할 수 ​​있습니다. 시스템 속성 및 사용자 정의 메타데이터는 대상 인덱스를 만드는 동안 **retrievable**로 표시되어 있는 경우에만 `$select` 매개 변수로 검색할 수 있습니다. 인덱스의 매개 변수를 만든 후에는 변경할 수 없습니다. 그러나 매개 변수를 더 추가할 수는 있습니다.

기본 쿼리의 예로 `$select=Gender,metadata_storage_size`를 들 수 있으며 이는 반환 값을 두 매개 변수로 제한합니다.

  ![반구조화된 검색](media/search-semi-structured-data/lastquery.png)

보다 복잡한 쿼리의 예로 `$filter=MinimumAge ge 30 and MaximumAge lt 75`를 들 수 있으며 이는 매개 변수 MinimumAge가 30 이상이고 MaximumAge가 75 미만인 결과만 반환합니다.

  ![반구조화된 검색](media/search-semi-structured-data/metadatashort.png)

직접 몇 가지 쿼리를 더 실험해 보고 싶다면 자유롭게 수행해 봅니다. 논리 연산자(and, or) 및 비교 연산자(eq, ne, gt, lt, ge, le)를 사용할 수 있습니다. 문자열 비교는 대/소문자를 구분합니다.

`$filter` 매개 변수는 인덱스를 만들 때 필터링 가능으로 표시된 메타데이터에서만 작동합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Search에서 다음 방법 등을 사용하여 반구조화된 데이터를 검색하는 방법을 배웠습니다.

> [!div class="checklist"]
> * REST API를 사용하여 Azure Search Service 만들기
> * Azure Search Service를 사용하여 컨테이너 검색

검색에 대해 더 자세한 내용을 알아보려면 이 링크를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Blob Storage에서 문서 인덱싱](search-howto-indexing-azure-blob-storage.md)