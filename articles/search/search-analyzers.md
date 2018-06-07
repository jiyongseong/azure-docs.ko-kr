---
title: Azure Search의 분석기 | Microsoft Docs
description: 인덱스의 검색 가능한 텍스트 필드에 분석기를 할당하여 사용자 지정, 미리 정의 또는 언어 특정 대안으로 기본 표준 Lucene을 바꿉니다.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.openlocfilehash: e858966fb5a15b84af1952399a5eff3ca50d0d59
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="analyzers-in-azure-search"></a>Azure Search의 분석기

*분석기*는 쿼리 문자열과 인덱싱된 문서의 텍스트를 처리하는 [전체 텍스트 검색](search-lucene-query-architecture.md) 구성 요소입니다. 다음 변환은 분석 중에 일반적으로 발생합니다.

+ 필수적이지 않은 단어(중지 단어) 및 문장 부호가 제거됩니다.
+ 구 및 하이픈을 넣은 단어는 구성 요소 부분으로 분류됩니다.
+ 대문자 단어는 소문자가 됩니다.
+ 단어는 시제에 관계 없이 일치를 찾을 수 있도록 루트 양식으로 세분화됩니다.

언어 분석기는 입력된 텍스트를 정보 저장 및 검색 시 효율적인 원시 형태 또는 루트 형태로 변환합니다. 변환은 인덱싱 중 인덱스가 빌드될 때, 그리고 검색 중 인덱스 읽기가 수행될 때 발생합니다. 두 작업에서 동일한 텍스트 분석기를 사용하면 예상되는 검색 결과를 얻을 확률이 큽니다.

Azure Search에서는 [표준 Lucene 분석기](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)를 기본 분석기로 사용합니다. 필드별로 기본 분석기를 재정의할 수 있습니다. 이 문서에서는 선택 옵션에 대해 설명하고 사용자 지정 분석에 대한 모범 사례를 제공합니다. 또한 주요 시나리오에 대한 예제 구성을 보여 줍니다.

## <a name="supported-analyzers"></a>지원되는 분석기

다음 목록에서는 Azure Search에서 지원되는 분석기를 설명합니다.

| Category | 설명 |
|----------|-------------|
| [표준 Lucene 분석기](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 기본값 사양 또는 구성이 필요하지 않습니다. 이 범용 분석기는 대부분의 언어와 시나리오에서 잘 작동합니다.|
| 미리 정의된 분석기 | 제한된 사용자 지정을 그대로 사용하는 완성된 제품으로 제공됩니다. <br/>특수 및 언어와 같은 두 가지 형식이 있습니다. "미리 정의"하는 방법은 사용자 지정 없이 이름별로 참조하는 것입니다. <br/><br/>[특수(언어 중립적) 분석기](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)는 텍스트 입력에 특수 처리 또는 최소한의 처리가 필요할 때 사용됩니다. 미리 정의된 비언어 분석기는 **Asciifolding**, **키워드**, **패턴**, **단순**, **중지**, **공백**을 포함합니다.<br/><br/>[언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)는 개별 언어에 대해 풍부한 언어 지원이 필요할 때 사용됩니다. Azure Search는 35개의 Lucene 언어 분석기 및 50개의 Microsoft 자연어 처리 분석기를 지원합니다. |
|[사용자 지정 분석기](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 하나의 토크 나이저(필수) 및 선택적 필터(char 또는 토큰)로 구성된 기존 요소를 결합한 사용자 정의 구성입니다.|

**패턴** 또는 **중지**와 같은 미리 정의된 분석기를 사용자 지정하여 [미리 정의된 분석기 참조](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)에서 설명한 다른 옵션을 사용할 수 있습니다. 미리 정의된 분석기 중 일부에만 옵션을 설정할 수 있습니다. 사용자 지정으로 *myPatternAnalyzer*와 같은 이름을 가진 새 구성을 제공하여 Lucene 패턴 분석기와 구분합니다.

## <a name="how-to-specify-analyzers"></a>분석기를 지정하는 방법

1. (사용자 지정 분석기의 경우) 인덱스 정의에 **분석기** 섹션을 만듭니다. 자세한 내용은 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 및 [사용자 지정 분석기 > 만들기](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer)를 참조하세요.

2. 인덱스의 [필드 정의](https://docs.microsoft.com/rest/api/searchservice/create-index)에서 **analyzer** 속성을 대상 분석기의 이름(예: `"analyzer" = "keyword"`)으로 설정합니다. 유효한 값에는 미리 정의된 분석기, 언어 분석기 또는 인덱스 스키마에 정의된 사용자 지정 분석기의 이름이 포함됩니다.

3. 경우에 따라 한 가지 **analyzer** 속성 대신 **indexAnalyzer** 및 **searchAnalyzer** 필드 매개 변수를 사용하여 인덱싱 및 쿼리에 대해 서로 다른 분석기를 설정할 수 있습니다. 

3. 필드 정의에 분석기를 추가하면 인덱스에 대해 쓰기 작업이 발생합니다. 기존 인덱스에 **analyzer**를 추가할 경우 다음 단계를 확인합니다.
 
 | 시나리오 | 영향 | 단계 |
 |----------|--------|-------|
 | 새 필드 추가 | 최소 | 스키마에 아직 필드가 존재하지 않으면 인덱스에 실제로 존재하는 필드가 없는 것이므로 변경할 필드가 없습니다. 이 작업에 [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) 및 [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 사용합니다.|
 | 기존 인덱싱된 필드에 분석기를 추가합니다. | 다시 빌드 | 해당 필드에 대해 반전된 인덱스를 처음부터 다시 만들고 이 필드에 대한 콘텐츠를 다시 인덱싱해야 합니다. <br/> <br/>개발 중인 인덱스의 경우 인덱스를 [삭제하고](https://docs.microsoft.com/rest/api/searchservice/delete-index) [만들어](https://docs.microsoft.com/rest/api/searchservice/create-index) 새 필드 정의를 선택합니다. <br/> <br/>프로덕션 중인 인덱스의 경우 수정된 정의를 제공하는 새 필드를 만들어 사용하기 시작해야 합니다. [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) 및 [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 사용하여 새 필드를 통합합니다. 나중에 계획된 인덱스 서비스의 일환으로 인덱스를 정리하여 오래된 필드를 제거할 수 있습니다. |

## <a name="tips-and-best-practices"></a>팁 및 모범 사례

이 섹션에서는 분석기 작동 방식에 대한 조언을 제공합니다.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>특정 요구 사항이 없는 경우 읽기 쓰기에 대해 하나의 분석기

Azure Search는 추가적인 `indexAnalyzer` 및 `searchAnalyzer` 필드 매개 변수를 통해 인덱싱 및 검색에 서로 다른 분석기를 지정할 수 있습니다. 분석기를 지정하지 않으면 `analyzer` 속성을 통해 설정된 분석기가 인덱싱과 검색에 모두 사용됩니다. `analyzer`를 지정하지 않으면 기본 표준 Lucene 분석기가 사용됩니다.

특정 요구 사항이 따로 지정되지 않으면 인덱싱 및 쿼리 모두에 대해 동일한 분석기를 사용하는 것이 일반적인 규칙입니다. 철저하게 테스트해야 합니다. 검색 및 인덱싱 시간에서 텍스트 처리가 다른 경우 검색 및 인덱싱 분석기 구성이 일치하지 않으면 쿼리 용어와 인덱싱된 용어 사이에 불일치가 발생할 위험을 떠안게 됩니다.

### <a name="test-during-active-development"></a>활성 개발 중 테스트

표준 분석기를 재정의하려면 인덱스를 다시 빌드해야 합니다. 가능하면 프로덕션 환경에 인덱스를 이동하기 전에 활성 개발 중 사용할 분석기를 결정합니다.

### <a name="inspect-tokenized-terms"></a>토큰화된 용어 검사

검색에서 예상되는 결과가 반환되지 않은 경우, 쿼리에 입력된 용어와 인덱스의 토큰화된 용어의 토큰이 같지 않기 때문일 가능성이 큽니다. 토큰이 같지 않으면 일치하는 결과가 반환되지 않습니다. 토크나이저 출력을 검사할 때는 조사 도구로 [분석 API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)를 사용하는 것이 좋습니다. 응답은 해당 분석기에 의해 생성되는 토큰으로 구성됩니다.

### <a name="compare-english-analyzers"></a>영어 분석기 비교

[검색 분석기 데모](http://alice.unearth.ai/)는 표준 Lucene 분석기, Lucene의 영어 분석기 및 Microsoft의 영어 자연어 프로세서를 나란히 비교하여 보여 주는 타사 데모 앱입니다. 인덱스가 수정되었습니다. 잘 알려진 이야기의 텍스트가 포함되어 있습니다. 사용자가 입력한 각 검색 입력에 대해 각 분석기의 결과가 인접한 창에 표시되어 분석기별로 동일한 문자열을 어떤 식으로 처리하는지 살펴볼 수 있습니다. 

## <a name="examples"></a>예

다음 예제에서는 몇 가지 주요 시나리오에 대한 분석기 정의를 보여줍니다.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>예제1: 사용자 지정 옵션

이 예제에서는 사용자 지정 옵션을 사용하는 분석기 정을 보여줍니다. char 필터, 토크나이저 및 토큰 필터에 대한 사용자 지정 옵션은 명명된 구문으로 개별 지정되고 분석기 정의에서 참조됩니다. 미리 정의된 요소도 그대로 사용되며 이름별로 참조하기만 하면 됩니다.

이 예제를 진행합니다.

* 분석기는 검색 가능한 필드에 대한 필드 클래스의 속성입니다.
* 사용자 지정 분석기는 인덱스 정의의 일부입니다. 가볍게 사용자 지정(예: 한 필터에서 단일 옵션 사용자 지정)되거나 여러 위치에서 사용자 지정될 수도 있습니다.
* 이 경우에 사용자 지정 분석기는 "my_analyzer"이며, 사용자 지정된 표준 토크나이저 "my_standard_tokenizer" 및 소문자 필터와 사용자 지정된 asciifolding 필터 "my_asciifolding"라는 두 개의 토큰 필터를 차례로 사용합니다.
* 또한 토크나이저 앞에 밑줄이 표시된 모든 대시를 바꾸는 사용자 지정 "map_dash" char 필터를 정의합니다(표준 토크나이저는 대시로 나뉘지만 밑줄이 없음).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>예제2: 기본 분석기 재정의

표준 분석기는 기본값입니다. 패턴 분석기와 같은 다른 미리 정의된 분석기로 기본값을 바꾸려 한다고 가정합니다. 사용자 지정 옵션을 설정하지 않은 경우 필드 정의에서 이름으로 지정하기만 하면 됩니다.

"analyzer" 요소에서 필드 기준으로 표준 분석기를 재정의합니다. 전역 재정의가 없습니다. 이 예제에서 `text1`는 패턴 분석기를 사용하고 분석기를 지정하지 않는 `text2`는 기본값을 사용합니다.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>예제3: 인덱싱 및 검색 작업에 대한 여러 분석기

API는 인덱싱 및 검색에 대해 다른 분석기를 지정하기 위한 추가 인덱스 특성을 포함합니다. `searchAnalyzer` 및 `indexAnalyzer` 특성은 한 쌍으로 지정되어야 하며 단일 `analyzer` 특성을 바꿉니다.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>예제4: 언어 분석기

다른 언어의 문자열을 포함하는 필드는 언어 분석기를 사용할 수 있지만 다른 필드는 기본값을 그대로 둡니다(또는 다른 미리 정의되거나 사용자 지정된 분석기 사용). 언어 분석기를 사용하는 경우 인덱싱 및 검색 작업에 사용해야 합니다. 언어 분석기를 사용하는 필드는 인덱싱 및 검색에 다른 분석기를 가질 수 없습니다.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>다음 단계

+ [전체 텍스트 검색이 Azure Search에서 작동하는 방식](search-lucene-query-architecture.md)의 포괄적인 설명을 검토하세요. 이 문서에서는 화면에서 직관적이지 않은 것처럼 보일 수 있는 동작을 설명하는 예제를 사용합니다.

+ 포털의 Search 탐색기에서 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) 예제 섹션 또는 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)의 추가 쿼리 구문을 사용해 보세요.

+ [언어별 어휘 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)를 적용하는 방법을 알아보세요.

+ 개별 필드에 대해 최소한의 처리 또는 특수한 처리를 수행하려면 [사용자 지정 분석기를 구성](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)하세요.

+ 이 데모 웹 사이트의 인접한 창에서 [표준 및 영어 분석기를 나란히 비교](http://alice.unearth.ai/)하세요. 

## <a name="see-also"></a>참고 항목

 [문서 검색 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [전체 Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [검색 결과 처리](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
