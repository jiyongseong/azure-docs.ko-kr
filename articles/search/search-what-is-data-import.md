---
title: Azure Search에서 데이터 가져오기 | Microsoft Docs
description: Azure Search에서 인덱스에 데이터를 업로드하는 방법에 대해 알아봅니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: heidist
ms.openlocfilehash: e54b89d31042d43a14379dea6daffd3e7763a64a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="indexing-in-azure-search"></a>Azure Search의 인덱싱
> [!div class="op_single_selector"]
> * [개요](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST (영문)](search-import-data-rest-api.md)
> 
> 

Azure Search에서는 [검색 인덱스](search-what-is-an-index.md)에 로드된 콘텐츠에 대해 쿼리가 실행됩니다. 이 문서에서는 인덱스에 콘텐츠를 로드하는 두 가지 기본 방법인 데이터를 인덱스에 프로그래밍 방식으로 *푸시* 또는 지원되는 데이터 원본에서 데이터로 *끌어올* [Azure Search 인덱서](search-indexer-overview.md) 지정에 대해 알아보겠습니다.

## <a name="pushing-data-to-an-index"></a>인덱스에 데이터 푸시
데이터를 Azure Search에 프로그래밍 방식으로 보내는 데 사용되는 푸시 모델은 가장 유연한 방법입니다. 첫째, 데이터 원본 형식의 제한이 없습니다. JSON 문서로 구성된 모든 데이터 집합을 Azure Search 인덱스로 푸시할 수 있으며, 데이터 집합의 각 문서에는 인덱스 스키마에 정의된 필드로 매핑되는 필드가 있습니다. 둘째, 실행 빈도의 제한이 없습니다. 원하는 만큼 자주 변경 내용을 인덱스에 푸시할 수 있습니다. 대기 시간 요구 사항이 매우 낮은 응용 프로그램의 경우(예: 검색 작업이 동적 재고 데이터베이스와 동기화되어야 하는 경우) 푸시 모델이 유일한 옵션입니다.

개별적으로 또는 배치에서 문서를 업로드할 수 있기 때문에 이 접근 방식은 끌어오기 모델 보다 더 유연합니다(최대 배치 당 1000 또는 16MB, 모두 제한됨). 또한 밀어넣기 모델을 사용하면 데이터의 위치에 관계 없이 Azure Search에 문서를 업로드할 수 있습니다.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Azure Search 인덱스에 데이터를 푸시하는 방법

다음 API를 사용하여 하나 또는 여러 문서를 인덱스에 로드할 수 있습니다.

+ [문서 추가, 업데이트 또는 삭제(REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) 또는 [indexBatch 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

현재 포털을 통해 데이터를 푸시할 수 있는 도구가 없습니다.

각 방법에 대한 소개는 [REST를 사용하여 데이터 가져오기](search-import-data-rest-api.md) 또는 [.NET을 사용하여 데이터 가져오기](search-import-data-dotnet.md)를 참조하세요.


## <a name="pulling-data-into-an-index"></a>인덱스로 데이터 끌어오기
끌어오기 모델은 지원되는 데이터 원본을 탐색하고 인덱스에 데이터를 자동으로 업로드합니다. Azure Search에서는 *인덱서*를 통해 이 기능이 구현되며 현재 다음 플랫폼에서 사용할 수 있습니다.

+ [Blob storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Azure SQL 데이터베이스 및 Azure VM의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

인덱서는 인덱스를 데이터 원본(일반적으로 테이블, 뷰 또는 이와 동등한 구조)에 연결하고 원본 필드를 인덱스에 해당하는 필드에 매핑합니다. 실행 중에 행 집합은 자동으로 JSON으로 변환되고 지정한 인덱스에 로드됩니다. 모든 인덱서는 데이터를 새로 고치는 빈도를 지정할 수 있도록 일정 예약을 지원합니다. 대부분의 인덱서는 데이터 원본에서 지원하는 경우 변경 내용 추적을 제공합니다. 새 문서를 인식하는 것 외에도 기존 문서에서 변경 사항 및 삭제를 추적하므로 인덱서는 인덱스에서 데이터를 적극적으로 관리해야 할 필요를 없애줍니다. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Azure Search 인덱스로 데이터를 끌어오는 방법

인덱서 기능은 [Azure Portal](search-import-data-portal.md), [REST API](/rest/api/searchservice/Indexer-operations) 및 [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations)에서 공개되어 있습니다. 

포털을 사용하는 한 가지 장점으로 Azure Search에서 일반적으로 원본 데이터 집합의 메타데이터를 읽음으로써 기본 인덱스 스키마를 생성할 수 있다는 것입니다. 인덱스를 처리할 때까지 생성된 인덱스를 수정할 수 있으며, 그 후에는 다시 인덱싱할 필요가 없는 스키마만 편집할 수 있습니다. 스키마에 직접 적용하려는 변경 내용이 있으면 인덱스를 다시 작성해야 합니다. 

## <a name="verify-data-import-with-search-explorer"></a>Search Explorer를 사용하여 데이터 가져오기 확인

문서 업로드에 대한 사전 검사를 수행하는 빠른 방법은 포털에서 **검색 탐색기**를 사용하는 것입니다. 탐색기를 사용하면 코드를 작성할 필요 없이 인덱스를 쿼리할 수 있습니다. 검색 환경은 [간단한 구문](/rest/api/searchservice/simple-query-syntax-in-azure-search) 및 기본 [searchMode 쿼리 매개 변수](/rest/api/searchservice/search-documents)를 기반으로 합니다. 결과는 JSON으로 반환되므로 전체 문서를 검사할 수 있습니다.

> [!TIP]
> 수많은 [Azure Search 코드 샘플](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)에는 포함되거나 쉽게 사용할 수 있는 데이터 집합이 있어 손쉽게 시작할 수 있는 방법을 제공합니다. 또한 포털에서는 작은 부동산 데이터 집합("realestate-us-sample")으로 구성된 샘플 인덱서 및 데이터 원본도 제공합니다. 샘플 데이터 원본에 미리 구성된 인덱서를 실행하면 인덱스가 [검색 탐색기] 또는 작성한 코드로 쿼리할 수 있는 문서로 만들어지고 로드됩니다.

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [포털 연습: 인덱스 만들기, 로드, 쿼리](search-get-started-portal.md)