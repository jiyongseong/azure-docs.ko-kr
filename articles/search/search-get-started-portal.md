---
title: 포털을 사용한 인덱싱, 쿼리 및 필터링에 대한 Azure Search 자습서 | Microsoft Docs
description: Azure Portal에서 미리 정의된 샘플 데이터를 사용하여 Azure Search에서 인덱스를 생성합니다. 전체 텍스트 검색, 필터, 패싯, 유사 항목 검색, 지리적 검색 등을 살펴봅니다.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 9ee88b254131b40fdf1e01b771afa92127734e18
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-query-and-filter-an-azure-search-index-in-the-portal"></a>포털에서 Azure Search 인덱스 만들기, 쿼리 및 필터링

Azure Portal에서 미리 정의된 샘플 데이터 집합을 시작하고 **데이터를 가져오기** 마법사를 사용하여 신속하게 Azure Search 인덱스를 생성합니다. **검색 탐색기**를 사용하여 전체 텍스트 검색, 필터, 패싯, 유사 항목 검색, 지리적 검색을 살펴봅니다.  

이 코드 없는 소개문에서는 흥미로운 쿼리를 바로 작성할 수 있도록 미리 정의된 데이터를 시작합니다. 포털 도구는 코드를 대체하는 것은 아니지만 다음 작업에 유용합니다.

+ 최소의 노력으로 실습 학습
+ **데이터 가져오기**로 코드를 작성하기 전에 인덱스 프로토타입 제작
+ **검색 탐색기**에서 쿼리 및 구분 분석 구문 테스트
+ 고객의 서비스에 게시된 기존 인덱스를 보고 특성 조회

**예상 시간:** 약 15분. 계정 또는 서비스 등록이 필요할 시 더 길어질 수 있습니다. 

또는 [.NET에서 Azure Search 프로그래밍에 대한 코드 기반 소개](search-howto-dotnet-sdk.md)를 사용하여 증가시킵니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 [Azure 구독](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) 및 [Azure Search 서비스](search-create-service-portal.md)를 사용하는 것으로 가정합니다. 

서비스를 즉시 프로비전하지 않으려는 경우 이 [Azure Search 개요 비디오](https://channel9.msdn.com/Events/Connect/2016/138)를 시작하고 약 3분 후에 나오는 이 자습서의 단계에 대한 6분짜리 데모 영상을 시청하셔도 됩니다.

## <a name="find-your-service"></a>서비스 찾기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Search 서비스의 서비스 대시보드를 엽니다. 서비스 타일을 대시보드에 고정하지 않은 경우 다음과 같은 방법으로 서비스를 찾을 수 있습니다. 
   
   * 표시줄의 왼쪽 탐색창에서 **모든 서비스**를 클릭합니다.
   * 검색 상자에 *검색*을 입력하여 구독에 대한 검색 서비스 목록을 가져옵니다. 목록에 서비스가 표시됩니다. 

## <a name="check-for-space"></a>공간 확인
많은 고객이 무료 서비스를 시작합니다. 이 버전은 3개의 인덱스, 3개의 데이터 소스 및 3개의 인덱서로 제한됩니다. 시작하기 전에 추가 항목에 대한 공간이 있는지 확인합니다. 이 자습서에서는 각 개체를 하나씩 만듭니다. 

> [!TIP] 
> 서비스 대시보드의 타일은 현재 갖고 있는 인덱스, 인덱서 및 데이터 원본 수를 보여줍니다. 인덱서 타일은 성공 및 실패 지표를 표시합니다. 인덱서 수를 보려면 타일을 클릭합니다. 
>
> ![인덱서 및 데이터 원본에 대한 타일][1]
>

## <a name="create-index"></a> 인덱스 및 부하 데이터 만들기
특정 검색 동작을 최적화하는 데 사용되는 검색 가능한 데이터, 메타데이터 및 구문을 포함하는 *인덱스* 에 검색 쿼리가 반복됩니다.

이 작업을 계속 포털 기반으로 유지하기 위해 **데이터 가져오기** 마법사를 통해 인덱서를 사용하여 크롤링할 수 있는 기본 제공 샘플 데이터 집합을 사용하겠습니다. 

#### <a name="step-1-start-the-import-data-wizard"></a>1단계: 데이터 가져오기 마법사 시작
1. Azure Search 서비스 대시보드의 명령 모음에서 **데이터 가져오기**를 클릭하여 인덱스를 만들고 채우는 마법사를 시작합니다.
   
    ![데이터 가져오기 명령][2]

2. 마법사에서 **데이터 원본** > **샘플** > **realestate-us-sample**을 클릭합니다. 이 데이터 원본은 이름, 형식 및 연결 정보를 통해 미리 구성되어 있습니다. 생성되는 데이터 원본은 다른 가져오기 작업에서 다시 사용할 수 있는 "기존 데이터 원본"이 됩니다.

    ![샘플 데이터 집합 선택][9]

3. 사용하려면 **확인**을 클릭합니다.

#### <a name="step-2-define-the-index"></a>2단계: 인덱스 정의
인덱스 만들기는 일반적으로 코드를 기반으로 수동으로 수행되지만 마법사는 크롤링할 수 있는 모든 데이터 원본에 대한 인덱스를 생성할 수 있습니다. 인덱스에는 적어도 각 문서를 고유하게 식별하는 문서 키로 표시된 하나의 필드가 있는 이름과 필드 컬렉션이 필요합니다.

필드에는 데이터 유형과 특성이 있습니다. 위쪽에 있는 확인란은 필드가 사용되는 방법을 제어하는 *인덱스 특성*입니다. 

* **조회 가능** 은 검색 결과 목록에 표시된다는 의미입니다. 예를 들어 필드가 필터 식에만 사용되는 경우 이 확인란을 지워 검색 결과에 대한 제한 해제로 개별 필드를 표시할 수 있습니다. 
* **필터링 가능**, **정렬 가능** 및 **패싯 가능**은 필드를 필터, 정렬 또는 패싯 탐색 구조에 사용할 수 있는지 여부를 결정합니다. 
* **검색 가능** 은 필드가 전체 텍스트 검색에 포함된다는 의미입니다. 문자열은 검색할 수 있습니다. 숫자 필드와 부울 필드는 종종 검색할 수 없다고 표시됩니다. 

기본적으로 마법사는 키 필드에 대한 기반으로 고유 식별자에 대한 데이터 원본을 검색합니다. 문자열은 검색 가능한 것으로 규정됩니다. 정수는 검색 가능하고, 필터링 가능하고, 정렬 가능하고, 패싯 가능한 것으로 규정됩니다.

  ![생성된 realestate 인덱스][3]

**확인**을 클릭하여 인덱스를 만듭니다.

#### <a name="step-3-define-the-indexer"></a>3단계: 인덱서 정의
**데이터 가져오기** 마법사에서 **인덱서** > **이름**을 클릭하고 인덱서 이름을 입력합니다. 

이 개체는 실행 가능한 프로세스를 정의합니다. 이 개체를 되풀이 일정에 게시할 수 있지만 지금은 **확인**을 클릭할 때 즉시 기본 옵션을 사용하여 인덱서를 한 번 실행합니다.  

  ![realestate 인덱서][8]

## <a name="check-progress"></a>진행 확인
데이터 가져오기를 모니터링하려면 서비스 대시보드로 돌아가서 아래로 스크롤하고 **인덱서** 타일을 두 번 클릭하여 인덱서 목록을 엽니다. 방금 만든 인덱서가 목록에 표시되고 인덱싱된 문서 수와 함께 상태가 "진행 중" 또는 성공으로 표시될 것입니다.

   ![인덱서 진행 메시지][4]

## <a name="query-index"></a> 인덱스 쿼리
이제 쿼리할 준비가 된 검색 인덱스가 있습니다. **검색 탐색기** 는 포털에 기본 제공되는 쿼리 도구입니다. 검색 결과가 예상과 일치하는지 확인할 수 있도록 검색 상자를 제공합니다. 

> [!TIP]
> [Azure Search 개요 비디오](https://channel9.msdn.com/Events/Connect/2016/138)의 6분 8초에 다음 단계가 설명되어 있습니다.
>

1. 명령 모음에서 **검색 탐색기** 를 클릭합니다.

   ![검색 탐색기 명령][5]

2. *realestate-us-sample*로 변경하려면 명령 모음에서 **인덱스 변경**을 클릭합니다.

   ![인덱스 및 API 명령][6]

3. 어떤 REST API가 제공되는지 확인하려면 명령 모음에서 **API 버전 설정**을 클릭합니다. 미리 보기 API는 아직 정식 출시되지 않은 새 기능을 제공합니다. 아래 쿼리의 경우 별도의 지시가 없으면 일반적으로 제공되는 버전(2017-11-11)을 사용합니다. 

    > [!NOTE]
    > [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)와 [.NET 라이브러리](search-howto-dotnet-sdk.md#core-scenarios)는 완전히 동일하지만 **검색 탐색기**는 REST 호출만 처리할 수 있습니다. [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 및 [완전한 Lucene 쿼리 파서](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)에 대한 구문과 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) 작업에 제공되는 모든 검색 매개 변수를 허용합니다.
    > 

4. 검색 창에 아래의 쿼리 문자열을 입력하고 **검색**을 클릭합니다.

  ![검색 쿼리 예제][7]

**`search=seattle`**

+ **search** 매개 변수는 완전한 텍스트 검색의 키워드를 입력하는 데 사용됩니다. 이 예에서는 워싱턴 주 킹 카운티의 목록을 반환하며, 문서에서 검색 가능한 모든 필드에 *시애틀*이 포함되어 있습니다. 

+ **검색 탐색기**는 문서 구조가 조밀하면 장황하고 읽기 어려운 JSON으로 결과를 반환합니다. 문서에 따라 검색 결과를 처리하여 중요한 요소를 추출하는 코드를 작성해야 할 수도 있습니다. 

+ 문서는 인덱스에서 검색 가능으로 표시되는 모든 필드로 구성됩니다. 포털에서 인덱스 특성을 보려면 *인덱스* 타일에서 **realestate-us-sample**을 클릭합니다.

**`search=seattle&$count=true&$top=100`**

+ **&** 기호는 순서에 관계 없이 지정할 수 있는 검색 매개 변수를 추가하는 데 사용됩니다. 

+  **$count=true** 매개 변수는 반환된 총 문서 수를 반환합니다. **$count=true**에서 보고하는 변경 내용을 모니터링하여 필터 쿼리를 확인할 수 있습니다. 

+ **$top=100**은 문서 전체에서 가장 순위가 높은 문서 100개를 반환합니다. 기본적으로 Azure Search는 일치 항목 중 처음 50개를 반환합니다. **$top**을 통해 이 수를 늘리거나 줄일 수 있습니다.


## <a name="filter-query"></a> 쿼리 필터링

**$filter** 매개 변수를 추가할 때 검색 요청에 필터가 포함됩니다. 

**`search=seattle&$filter=beds gt 3`**

+ **$filter** 매개 변수는 사용자가 입력한 조건과 일치하는 결과를 반환합니다. 이 예에서는 침실 수가 3보다 큽니다. 

+ 필터 구문은 OData 구조입니다. 자세한 내용은 [OData 필터 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)을 참조하세요.

## <a name="facet-query"></a> 쿼리 패싯

패싯 필터는 검색 요청에 포함됩니다. 패싯 매개 변수를 사용하면 사용자가 입력하는 패싯 값과 일치하는 집계된 문서 수를 반환할 수 있습니다. 

**`search=*&facet=city&$top=2`**

+ **search=** \* 는 빈 검색입니다. 빈 검색은 모든 것을 검색합니다. 빈 쿼리를 제출하는 한 가지 이유는 문서는 문서 전체를 필터링하거나 패싯하는 것입니다. 예를 들어 인덱스의 모든 도시로 구성되는 패싯 탐색 구조가 필요할 수 있습니다.

+  **facet**은 UI 컨트롤에 전달할 수 있는 탐색 구조를 반환합니다. 범주와 개수를 반환합니다. 이 예에서 범주는 도시 수를 기반으로 합니다. Azure Search에는 집계가 없습니다. 하지만 각 범주의 문서 수를 제공하는 `facet`을 통해 근사치를 집계할 수 있습니다.

+ **$top=2**는 두 문서를 가져오고, `top`을 사용하여 결과를 늘리거나 줄일 수 있다는 것을 보여줍니다.

**`search=seattle&facet=beds`**

+ 이 쿼리는 *시애틀*에 대한 텍스트 검색에서 침대에 대한 패싯입니다. *침대*라는 조건을 패싯으로 지정할 수 있습니다. 왜냐하면 필드는 인덱스에서 검색 가능하고, 필터링 가능하고, 패싯 가능한 것으로 표시되고(숫자 1-5), 필드에 포함된 값은 목록을 그룹으로 분류(침실 3개, 침실 4개 목록)하는 데 적합하기 때문입니다. 

+ 필터링 가능한 필드만 패싯이 가능합니다. 검색이 가능한 필드만 결과에 반환할 수 있습니다.

## <a name="highlight-query"></a> 강조 표시 추가

적중 항목 강조 표시는 키워드와 일치하는 텍스트의 형식을 참조하며, 제공되는 일치 항목은 특정 필드에 있습니다. 검색어가 설명에 깊게 묻혀 있으면 좀 더 쉽게 찾을 수 있도록 적중 항목 강조 표시를 추가할 수 있습니다. 

**`search=granite countertops&highlight=description`**

+ 이 예제에서는 형식이 지정된 *화강암 싱크대*라는 구를 설명 필드에서 쉽게 찾을 수 있습니다.

**`search=mice&highlight=description`**

+ 전체 텍스트 검색은 의미 체계가 유사한 단어 형태를 찾습니다. 이 예에서는 키워드 검색 "mice"에 대한 응답으로 쥐가 출몰한 집에 대해 검색 결과에 "mouse"에 대해 강조 표시된 텍스트가 포함되었습니다. 언어 분석 때문에 같은 단어가 다른 형태로 결과에 나타날 수 있습니다. 

+ Azure Search는 Lucene와 Microsoft의 56가지 분석기를 지원합니다. Azure Search에서 사용하는 기본 분석기는 표준 Lucene 분석기입니다. 

## <a name="fuzzy-search"></a> 유사 항목 검색 사용

시애틀 지역의 Samammish 고원을 *samamish*로 잘못 입력한 경우처럼 맞춤법이 잘못된 단어는 일반 검색에서 일치 항목을 반환하지 않습니다. 맞춤법 오류를 처리하려면 다음 예제에 설명된 것처럼 유사 항목 검색을 사용하면 됩니다.

**`search=samamish`**

+ 이 예제에서는 시애틀 인근 지역의 철자를 잘못 입력합니다.

**`search=samamish~&queryType=full`**

+ **~** 기호를 지정하고 전체 쿼리 파서를 사용하면 **~** 구문을 해석하여 올바르게 구문 분석하는 유사 항목 검색이 설정됩니다. 

+ **queryType=full**을 설정할 때 발생하는 전체 쿼리 파서를 옵트인하면 유사 항목 검색이 제공됩니다. 전체 쿼리 파서를 통해 지원되는 쿼리 시나리오에 대한 자세한 내용은 [Azure Search의 Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)을 참조하세요.

+ **queryType**을 지정하지 않으면 기본 단순 쿼리 파서가 사용됩니다. 단순 쿼리 파서는 속도가 좀 더 빠릅니다. 하지만 유사 항목 검색, 정규식, 근접 검색 또는 다른 고급 쿼리 종류가 필요한 경우 전체 구문을 사용해야 합니다. 

## <a name="geo-search"></a> 지리 공간 검색 시도

지리 공간 검색은 좌표가 포함된 필드의 [edm.GeographyPoint 데이터 유형](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)을 통해 지원됩니다. Geosearch는 [OData 필터 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)에 지정된 필터 유형입니다. 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ 예제 쿼리는 위치 데이터의 모든 결과를 필터링하며, 결과는 위도 및 경도 좌표로 지정된 특정 지점으로부터 5킬로미터 미만입니다. **$count**를 추가하면 거리 또는 좌표를 변경할 때 반환되는 결과가 몇 개인지 확인할 수 있습니다. 

+ 지리 공간 검색은 검색 응용 프로그램에 "주변 찾기" 기능이 있거나 지도 탐색을 사용하는 경우에 유용합니다. 하지만 전체 텍스트 검색은 아닙니다. 이름으로 도시 또는 국가를 검색할 수 있어야 한다는 사용자 요구 사항이 있는 경우 좌표 외에도 도시 또는 국가 이름이 포함된 필드를 추가하면 됩니다.

## <a name="next-steps"></a>다음 단계

+ 방금 만든 개체를 수정합니다. 마법사를 실행하면 다시 돌아가서 인덱스, 인덱서 또는 데이터 원본과 같은 개별 구성 요소를 보거나 수정할 수 있습니다. 필드 데이터 형식을 변경하는 등 일부 편집은 인덱스에서 허용되지 않지만 대부분의 속성 및 설정은 수정할 수 있습니다.

  개별 구성 요소를 보려면 대시보드에서 **인덱스**, **인덱서** 또는 **데이터 원본** 타일을 클릭하여 기존 개체의 목록을 표시합니다. 다시 빌드할 필요가 없는 인덱스 편집에 대한 자세한 내용은 [인덱스 업데이트(Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index)를 참조하세요.

+ 다른 데이터 원본으로 도구 및 단계를 시도해 보세요. `realestate-us-sample` 샘플 데이터 집합은 Azure Search에서 크롤링할 수 있는 Azure SQL Database에서 가져온 것입니다. Azure Search는 Azure SQL Database 외에도 Azure Table Storage, Blob Storage, Azure VM의 SQL Server, Azure Cosmos DB의 플랫 데이터 구조에서 인덱스를 크롤링하고 유추할 수 있습니다. 이 모든 데이터 원본이 마법사에서 지원됩니다. 코드에서 *인덱서*를 사용하여 간편하게 인덱스를 채울 수 있습니다.

+ 그 외의 비 인덱서 데이터 원본은 푸시 모델을 통해 지원되며, 푸시 모델에서는 코드가 새 행 집합과 변경된 행 집합을 JSON으로 인덱스에 푸시합니다. 자세한 내용은 [Azure Search에서 문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 참조하세요.

이 문서에 언급된 다른 기능에 대해 자세히 알아보려면 다음 링크를 방문하세요.

* [인덱서 개요](search-indexer-overview.md)
* [인덱스 만들기(인덱스 특성에 대한 자세한 정보 포함)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [검색 탐색기](search-explorer.md)
* [문서 검색(쿼리 구문의 예제 포함)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png