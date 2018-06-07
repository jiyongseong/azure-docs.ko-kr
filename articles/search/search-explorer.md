---
title: 인덱스 쿼리(포털 - Azure Search) | Microsoft Docs
description: Azure Portal의 검색 탐색기에서 검색 쿼리를 실행합니다.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: heidist
ms.openlocfilehash: a3592bd0c304dfb78374eeba432c0d28203980c9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Azure Portal에서 Search Explorer를 사용하여 Azure Search 인덱스 쿼리
> [!div class="op_single_selector"]
> * [개요](search-query-overview.md)
> * [포털](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST (영문)](search-query-rest-api.md)
> 
> 

이 문서에서는 Azure Portal에서 **Search Explorer**를 사용하여 Azure Search 인덱스를 쿼리하는 방법을 보여줍니다. Search Explorer를 사용하여 서비스에서 기존 인덱스에 단일 또는 전체 Lucene 쿼리 문자열을 제출할 수 있습니다.

## <a name="open-the-service-dashboard"></a>서비스 대시보드 열기
1. [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)의 왼쪽에 있는 점프 표시줄에서 **모든 리소스**를 클릭합니다.
2. Azure Search 서비스를 선택합니다.

## <a name="select-an-index"></a>인덱스 선택

**인덱스** 타일에서 검색하려는 인덱스를 선택합니다.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Search Explorer 열기

Search Explorer 타일을 클릭하여 검색 표시줄 및 결과 창을 슬라이드로 엽니다.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>검색 시작

Search Explorer를 사용하는 경우 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)를 지정하여 쿼리를 작성할 수 있습니다.

1. **쿼리 문자열**에서 쿼리를 입력한 다음 **검색** 키를 누릅니다. 

   쿼리 문자열은 적절한 요청 URL로 자동으로 구문 분석되어 Azure Search REST API에 대해 HTTP 요청을 제출합니다.   
   
   유효한 단일 또는 전체 Lucene 쿼리 구문을 사용하여 요청을 만들 수 있습니다. `*` 문자는 임의의 순서로 모든 문서를 반환하는 비어 있거나 지정되지 않은 검색과 같습니다.

2. **결과**에서 쿼리 결과는 원시 JSON으로 표시되며 이는 프로그래밍 방식으로 요청을 실행하는 경우 HTTP 응답 본문에서 반환되는 페이로드와 동일합니다.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>다음 단계

다음 리소스는 추가 쿼리 구문 정보 및 예제를 제공합니다.

 + [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene 쿼리 구문 예제](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData 필터 식 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 