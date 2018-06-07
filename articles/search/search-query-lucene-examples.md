---
title: Azure Search의 Lucene 쿼리 예제 | Microsoft Docs
description: Lucene은 유사 항목 검색, 근접 검색, 용어 상승, 정규식 검색 및 와일드카드 검색에 대해 구문을 쿼리합니다.
author: LiamCa
manager: jlembicz
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: liamca
ms.openlocfilehash: 46e03834cb307ea103a8794616f6f38227881272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Lucene은 Azure Search에서 퀴리를 만들기 위해 구문 예제를 쿼리합니다.
Azure Search를 위한 쿼리를 구성할 때는 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)을 사용하거나, [Azure 검색의 Lucene 쿼리 파서](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)를 대신 사용할 수 있습니다. Lucene 쿼리 파서는 필드 범위 쿼리, 유사 항목 검색, 근접 검색, 용어 상승, 정규식 검색 등의 더 복잡한 쿼리 구조를 지원합니다.

이 문서에서는 전체 구문을 사용할 때 사용할 수 있는 쿼리 작업을 보여 주는 예제를 단계별로 진행할 수 있습니다.

## <a name="viewing-the-examples-in-jsfiddle"></a>JSFiddle에서 예제 보기

이 문서의 모든 예제는 스크립트 및 HTML 테스트용 온라인 코드 편집기인 [JSFiddle](https://jsfiddle.net)에서 미리 로드된 검색 인덱스에 대해 실행되는 실행 가능한 쿼리입니다. 

이러한 쿼리를 실행하려면 쿼리 예제 URL을 마우스 오른쪽 단추로 클릭하여 별도의 브라우저 창에서 JSFiddle을 엽니다.

> [!NOTE]
> 다음 예제는 [City of New York OpenData](https://nycopendata.socrata.com/) 이니셔티브에서 제공하는 데이터 집합에 기반하여 사용 가능한 작업으로 구성된 검색 인덱스를 활용합니다. 이 데이터는 최신 또는 완료로 간주되어서는 안 됩니다. 인덱스는 Microsoft에서 제공하는 샌드박스 서비스에 있습니다. 이러한 쿼리를 시도하기 위해 Azure 구독 또는 Azure Search가 필요하지 않습니다.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>전체 Lucene 구문 분석을 호출하는 방법

이 문서의 모든 예제는 전체 구문을 나타내는 **queryType=full** 검색 매개 변수가 Lucene 쿼리 파서에서 처리되도록 지정합니다. 

**예제 1** -- 다음 쿼리 조각을 마우스 오른쪽 단추로 클릭하여 새 브라우저 페이지에서 열고 JSFiddle을 로드한 후 쿼리를 실행합니다.

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

새 브라우저 창에 JavaScript 소스 및 HTML 출력이 나란히 표시됩니다. 스크립트는 링크에 표시된 것 같은 코드 조각만이 아니라 전체 쿼리를 참조합니다. 전체 쿼리는 각 예제에 대한 URL에 표시됩니다. 

이 쿼리는 뉴욕시 작업 인덱스(샌드박스 서비스에 로드된 nycjobs)에서 문서를 반환합니다. 간략하게 표현하기 위해 쿼리는 직함만 반환되도록 지정합니다. 전체 기본 쿼리는 다음과 같습니다.

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**searchFields** 매개 변수는 검색을 직위 필드로만 제한합니다. **queryType**이 **full**로 설정되어,이 쿼리에 Lucene 쿼리 파서를 사용하도록 Azure Search에 지시합니다.

> [!NOTE]
> 쿼리 처리에 대한 배경 정보를 보려면 [Azure Search에서 전체 텍스트 검색의 작동 방식](search-lucene-query-architecture.md)을 참조하세요. 검색 매개 변수에 대한 자세한 내용은 [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)(문서 검색(Azure Search 서비스 REST API))를 참조하세요.
>

### <a name="fielded-query-operation"></a>필드 지정 쿼리 작업
**fieldname:searchterm** 구조를 정의하여 필드 지정 쿼리 작업을 정의하도록 이 문서의 예제를 수정할 수 있습니다. 여기서 필드는 단일 단어이고, 검색어도 선택적으로 부울 연산이 포함된 단일 단어 또는 구입니다. 몇 가지 예제는 다음과 같습니다. 

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

이 경우에 위치 필드에서 두 개의 다른 도시를 검색하고 있으므로 두 문자열이 단일 엔터티로 평가되길 원하는 경우 여러 문자열을 인용 부호로 묶어야 합니다. 또한, NOT과 AND와 같이 연산자는 대문자로 표시해야 합니다.

**fieldname:searchterm** 에서 지정된 필드는 검색 가능 필드이어야 합니다. 필드 정의에서 인덱스 특성이 사용되는 방법에 대한 자세한 내용은 [인덱스 만들기(Azure Search 서비스 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.

**예제 2** -- 다음 쿼리 조각을 마우스 오른쪽 단추로 클릭합니다. 이 쿼리는 junior가 아닌 senior라는 용어가 포함된 직함을 검색합니다.

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>유사 항목 검색 예제
유사 항목 검색은 용어에서 구조가 유사한 일치 항목을 찾습니다. [Lucene 문서](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)에 따라 유사 항목 검색은 [다메라우-레펜슈타인(Damerau-Levenshtein) 거리](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance)에 기반합니다.

유사 항목 검색을 수행하려면 편집 거리를 지정하는 0과 2 사이의 값을 선택적 매개 변수로 포함하여 단일 단어의 끝에 물결표(“~”) 기호를 추가합니다. 예를 들어, "blue~" 또는 "blue~1"은 blue, blues 및 glue를 반환합니다.

**예제 3** -- 다음 쿼리 조각을 마우스 오른쪽 단추로 클릭합니다. 이 쿼리는 associate라는 용어가 포함된 직업을 검색합니다(맞춤법이 틀린 경우).

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> 형태소 분석 또는 단어의 기본형 찾기를 기대하는 경우에는 놀랄 수 있겠지만 유사 항목 쿼리는 [분석](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)되지 않습니다. 어휘 분석은 완전한 용어(용어 쿼리 또는 구 쿼리)에만 수행됩니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="proximity-search-example"></a>근접 검색 예제
근접 검색은 문서에서 서로 근접한 용어를 찾는 데 사용됩니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다. 예를 들어, "hotel airport"~5는 문서에서 서로 5개의 단어 내에서 hotel과 airport라는 용어를 찾게 됩니다.

**예제 4** -- 쿼리를 마우스 오른쪽 단추로 클릭합니다. 단 한 단어로 구분된 "senior analyst"라는 용어가 포함된 직업을 검색합니다.

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**예제 5** -- "senior analyst"라는 용어 사이에 단어를 제거하여 다시 시도합니다.

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>용어 상승 예제
용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 이것은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다. 다음 예제는 차이점을 설명하는 데 도움이 됩니다.

musicstoreindex 예제에서 **genre** 와 같이, 특정 필드에서 일치 항목을 상승시키는 평가 프로필을 고려해 보세요. 용어 상승은 일부 검색어를 다른 것보다 높게 더 상승시키는 데 사용될 수 있습니다. 예를 들어, "rock^2 electronic"은 **genre** 필드에 검색어가 있는 문서를 인덱스의 다른 검색 가능 필드보다 높게 상승시킵니다. 또한, 용어 상승 값(2)의 결과로 "rock"이라는 검색어가 포함된 문서는 "electronic"이라는 다른 검색어보다 높은 순위로 매겨집니다.

용어를 상승시키려면 검색하려는 용어 끝 부분에 상승 계수(숫자)와 함께 캐럿("^") 기호를 사용하십시오. 상승 계수가 높을수록 해당 용어는 다른 검색어에 비해 더 관련성이 높아집니다. 기본적으로, 상승 계수는 1입니다. 상승 계수는 양수이어야 하지만, 1보다 작을 수 있습니다(예: 0.2).

**예제 6** -- 쿼리를 마우스 오른쪽 단추로 클릭합니다. "computer analyst"라는 용어가 포함된 직업을 검색합니다. 여기서 computer와 analyst 두 단어가 포함된 결과는 없지만, analyst라는 직업은 결과의 맨 위에 나옵니다.

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**예제 7** -- 다시 시도합니다. 이번에는 두 단어가 존재하지 않을 경우 analyst라는 용어보다 computer라는 용어가 포함된 결과를 상승시킵니다.

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>정규식 예제
정규식 검색은 [RegExp 클래스](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)에 나와 있는 것처럼 슬래시("/") 사이의 내용에 기반하여 일치 항목을 찾습니다.

**예제 8** -- 쿼리를 마우스 오른쪽 단추로 클릭합니다. Senior 또는 Junior라는 용어가 포함된 직업을 검색합니다.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

이 예제에 대한 URL은 페이지에서 제대로 렌더링되지 않습니다. 해결 방법으로 아래의 URL을 복사한 후 브라우저 URL 주소에 붙여 넣습니다.`http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>와일드카드 검색 예제
일반적으로 다중(\*) 또는 단일(?) 문자 와일드카드 검색에 인식된 구문을 사용할 수 있습니다. Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.

**예제 9** -- 쿼리를 마우스 오른쪽 단추로 클릭합니다. programming 및 programmer라는 용어가 있는 직함이 포함된 접두사 'prog'가 포함되어 있는 직업을 검색합니다.

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

검색의 첫 문자로 * 또는 ? 기호를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
코드에서 Lucene 쿼리 파서를 지정해 보십시오. 다음 링크에서는 .NET와 REST API 모두에 대한 검색 쿼리를 설정하는 방법에 대해 설명합니다. 링크는 기본 단순 구문을 사용하므로 **queryType**을 지정하려면 이 문서에서 배운 내용을 적용해야 합니다.

* [.NET SDK를 사용하여 Azure Search 인덱스 쿼리](search-query-dotnet.md)
* [REST API를 사용하여 Azure Search 인덱스 쿼리](search-query-rest-api.md)

## <a name="see-also"></a>참고 항목

 [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)