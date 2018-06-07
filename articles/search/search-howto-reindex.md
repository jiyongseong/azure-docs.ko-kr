---
title: Azure Search 인덱스 다시 빌드 또는 검색 가능한 콘텐츠 새로 고침 | Microsoft Docs
description: 전체 다시 빌드 또는 부분 증분 인덱싱에서 새 요소를 추가하거나 기존 요소 또는 문서를 업데이트하거나 오래된 문서를 삭제하여 Azure Search 인덱스를 새로 고칩니다.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: f38054eaf2829149a496f840366b6f2f9e03e12b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942106"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Azure Search 인덱스를 다시 빌드하는 방법

인덱스를 다시 빌드하면 인덱스 구조가 변경되어 Azure Search 서비스의 인덱스에 대한 실제 표현이 변경됩니다. 이에 반해 인덱스 새로 고침은 콘텐트 전용 업데이트이며 영향을 주는 외부 데이터 원본에서 최신 변경 내용을 선택합니다. 이 문서에서는 구조적으로나 실질적으로 인덱스를 업데이트하는 방법에 대한 지침을 제공합니다.

인덱스 업데이트에는 서비스 수준의 읽기-쓰기 권한이 필요합니다. 프로그래밍 방식으로 REST 또는 .NET API를 호출하여 업데이트 옵션을 지정하는 매개 변수를 통해 콘텐츠 전체를 다시 작성하거나 증분 인덱싱을 수행할 수 있습니다. 

일반적으로 인덱스에 대한 업데이트는 주문형입니다. 하지만 특정 소스에 대한 [인덱서](search-indexer-overview.md)를 사용하여 채워진 인덱스의 경우 기본 제공 스케줄러를 사용할 수 있습니다. 스케줄러는 필요한 간격 및 패턴에 따라 최대 15분 간격으로 문서 새로 고침을 지원합니다. 새로 고침 빈도를 높이려면 외부 데이터 원본과 Azure Search 인덱스를 동시에 업데이트하여 트랜잭션에 대한 이중 쓰기를 통해 수동으로 인덱스 업데이트 내용을 밀어 넣어야 합니다.

## <a name="full-rebuilds"></a>전체 다시 빌드

많은 유형의 업데이트에 대해 전체 다시 빌드가 필요합니다. 전체 다시 빌드란 인덱스(데이터와 메타데이터 모두)를 삭제한 다음, 외부 데이터 원본으로 인덱스를 다시 채우는 작업을 말합니다. 프로그래밍 방식으로 인덱스를 [삭제](https://docs.microsoft.com/rest/api/searchservice/delete-index), [생성](https://docs.microsoft.com/rest/api/searchservice/create-index) 및 [다시 로드](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)하여 다시 빌드합니다. 

다시 빌드한 다음, 쿼리 패턴 및 채점 프로파일을 테스트해봤다면, 기본 콘텐츠가 변경된 경우 쿼리 결과의 변형을 예상할 수 있습니다.

## <a name="when-to-rebuild"></a>다시 빌드하는 경우

인덱스 스키마가 유동적인 상태인 경우 개발하는 동안 빈번한 전체 다시 빌드를 계획합니다.

| 수정 | 다시 빌드 상태|
|--------------|---------------|
| 필드 이름, 데이터 형식 또는 [인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index) 변경 | 필드 정의를 변경하면 일반적으로 [인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index): Retrievable, SearchAnalyzer, SynonymMaps를 제외하고 다시 빌드 패널티가 발생합니다. 색인을 다시 빌드할 필요 없이 Retrievable, SearchAnalyzer 및 SynonymMaps 특성을 기존 필드에 추가할 수 있습니다.|
| 필드 추가 | 다시 빌드에 대한 엄격한 요구 사항은 없습니다. 기존의 인덱싱된 문서에는 새 필드에 null 값이 제공됩니다. 나중에 인덱스를 다시 수행하면 원본 데이터의 값이 Azure Search에 의해 추가된 null을 대체합니다. |
| 필드 삭제 | Azure Search 인덱스에서 필드를 직접 삭제할 수는 없습니다. 대신 응용 프로그램이 "삭제된" 필드를 무시하고 사용하지 않도록 해야 합니다. 물리적으로 필드 정의와 콘텐츠는 다음 번에 해당 필드를 생략하는 스키마를 사용하여 인덱스를 다시 빌드할 때까지 인덱스에 남아 있습니다.|

> [!Note]
> 계층을 전환하는 경우에도 다시 빌드가 필요합니다. 특정 시점에서 용량 확장을 결정하는 경우, 현재 위치 업그레이드가 없습니다. 새 용량 지점에서 새 서비스를 만들어야 하며 새 서비스에서 인덱스는 처음부터 빌드해야 합니다. 

## <a name="partial-or-incremental-indexing"></a>부분 또는 증분 인덱싱

인덱스가 프로덕션 환경에 있으면 증분 인덱싱에 중점을 두게 되며 인식할만한 서비스 중단은 대개 없습니다. 부분 인덱싱 또는 증분 인덱싱은 검색 인덱스의 콘텐츠를 동기화하여 영향을 주는 데이터 원본의 콘텐트 상태를 반영하는 콘텐트 전용 워크로드입니다. 원본에 추가되거나 삭제된 문서가 인덱스에 추가되거나 삭제됩니다. 코드에서 [문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 작업이나 .NET의 해당 작업을 호출합니다.

> [!Note]
> 외부 데이터 원본을 크롤링하는 인덱서를 사용하는 경우 원본 시스템의 변경 내용 추적 메커니즘을 사용하여 증분 인덱싱이 수행됩니다. [Azure Blob 저장소](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)의 경우 `lastModified` 필드가 사용됩니다. [Azure Table 저장소](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)에서 `timestamp`는 동일한 용도로 사용됩니다. 마찬가지로 [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)와 [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md#indexing-changed-documents) 모두에는 행 업데이트 플래그를 지정하는 필드가 있습니다. 인덱서에 대한 자세한 내용은 [인덱서 개요](search-indexer-overview.md)를 참조하세요.

## <a name="scale-out-indexing"></a>인덱싱 확장

데이터 볼륨이 커지거나 처리 요구 사항이 변경되면 간단한 다시 빌드 및 다시 인덱싱 작업만으로는 충분하지 않을 수 있습니다. 늘어나는 수요를 충족시키기 위한 첫 번째 단계로 기존 서비스의 한도 내에서 [규모와 용량](search-capacity-planning.md)을 증가시키는 것이 좋습니다. 

[인덱서](search-indexer-overview.md)를 사용하는 경우 추가 확장 메커니즘을 사용할 수 있습니다. 인덱서에는 일정한 간격으로 인덱싱을 처리하거나 24시간 범위를 초과하여 처리를 확장할 수 있는 기본 제공 스케줄러가 함께 제공됩니다. 또한 데이터 원본 정의와 쌍을 이루어 인덱서를 사용하여 데이터를 분할하고 병렬로 실행하는 일정을 사용하여 병렬 처리를 구현할 수 있습니다.

### <a name="scheduled-indexing-for-large-data-sets"></a>큰 데이터 집합에 대한 인덱싱 예약

일정 예약은 큰 데이터 집합을 처리하고 Cognitive Search 파이프라인에서 이미지 분석과 같이 느리게 실행되는 분석을 처리하는 중요한 메커니즘입니다. 인덱서 처리는 24시간 범위 내에 실행됩니다. 24시간 내에 처리가 완료되지 않으면 인덱서 일정 예약의 동작이 사용자에게 유리하게 작용할 수 있습니다. 

기본적으로 예약된 인덱싱은 특정 간격으로 시작되며, 대개 다음으로 예약된 간격에서 다시 시작되기 전에 작업이 완료됩니다. 하지만 간격 내에 처리가 완료되지 않으면 인덱서가 중지됩니다(시간이 초과되기 때문임). 다음 번 간격이 시작되면, 마지막으로 중단된 위치에서 처리가 다시 시작되며, 시스템은 해당 위치를 추적합니다. 

실제 여러 날에 걸쳐 수행되는 인덱스 로드의 경우 24시간 일정으로 인덱서를 배치할 수 있습니다. 다음 번 24시간 범위에서 인덱싱이 재개되면 마지막으로 성공한 문서에서 다시 시작됩니다. 이러한 방식으로 인덱서는 처리되지 않은 모든 문서가 처리될 때까지 여러 날에 걸쳐 문서 백로그를 통해 작업을 수행할 수 있습니다. 이러한 방식에 대한 자세한 내용은 [큰 데이터 집합 인덱싱](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)을 참조하세요.

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>병렬 인덱싱

두 번째 방법은 병렬 인덱싱 전략을 설정하는 것입니다. 일상적이지 않고 계산 집약적인 인덱싱 요구 사항(예: Cognitive Search 파이프라인에서 검색된 문서의 OCR)의 경우, 병렬 인덱싱 전략이 이러한 특정 목적에 적합한 방법일 수 있습니다. Cognitive Search 보강 파이프라인에서 이미지 분석과 자연어 처리가 장기간 실행됩니다. 쿼리 요청을 동시에 처리하지 않는 서비스에 대한 병렬 인덱싱은 처리 속도가 느린 대량의 콘텐츠에 적절한 옵션이 될 수 있습니다. 

병렬 처리 전략에는 다음과 같은 요소가 있습니다.

+ 원본 데이터를 동일한 컨테이너 내의 여러 컨테이너 또는 여러 가상 폴더로 나눕니다. 
+ 각각의 미니 데이터 집합을 [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)에 매핑하고, 자체 [인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer)와 쌍을 이루도록 합니다.
+ Cognitive Search의 경우 각 인덱서 정의에 동일한 [기술 집합](ref-create-skillset.md)을 참조합니다.
+ 동일한 대상 검색 인덱스에 기록합니다. 
+ 모든 인덱서가 동시에 실행되도록 예약합니다.

> [!Note]
> Azure Search는 복제본이나 파티션을 특정 워크로드 전용으로 지원하지 않습니다. 동시 인덱싱이 과도할 경우 발생할 수 있는 문제는 시스템에 과부하가 발생하여 쿼리 성능이 저하되는 것입니다. 테스트 환경이 있는 경우, 먼저 장단점을 파악할 수 있도록 병렬 인덱싱을 구현하십시오.

### <a name="configure-parallel-indexing"></a>병렬 인덱싱 구성

인덱서의 경우 처리 용량은 검색 서비스에 사용되는 각 SU(서비스 단위)에 대해 하나의 인덱서 하위 시스템을 기반으로 합니다. 다수의 동시 인덱서는 복제본이 2개 이상 있는 기본 또는 표준 계층에 프로비전된 Azure Search 서비스에서 가능합니다. 

1. [Azure Portal](https://portal.azure.com)의 검색 서비스 대시보드 **개요** 페이지에서 **가격 책정 계층**을 확인하여 병렬 인덱싱을 수용할 수 있는지 확인합니다. 기본 및 표준 계층 모두 여러 복제본이 제공됩니다.

2. **설정** > **규모**에서 병렬 처리를 위해 인덱서 워크로드마다 복제본을 하나씩 추가하여 [복제본을 증가](search-capacity-planning.md)시킵니다. 기존 쿼리 볼륨에 대해 충분한 수를 유지합니다. 인덱싱을 위해 쿼리 워크로드를 희생하는 것은 바람직한 절충안이 아닙니다.

3. Azure Search 인덱서가 도달할 수 있는 수준의 여러 컨테이너에 데이터를 배포합니다. 여기에는 Azure SQL Database의 여러 테이블, Azure Blob 저장소의 여러 컨테이너 또는 여러 컬렉션이 해당될 수 있습니다. 각 테이블 또는 컨테이너에 대해 하나의 데이터 원본 개체를 정의합니다.

4. 병렬로 실행할 여러 인덱서 생성 및 예약:

   + 서비스에 복제본이 여섯 개 있다고 가정합니다. 전체 데이터 집합의 6방향 분할을 위해 데이터 집합의 1/6을 포함하는 데이터 원본에 각각 매핑된 인덱서 여섯 개를 구성합니다. 

   + 각 인덱서에서 동일한 인덱스를 가리킵니다. Cognitive Search 워크로드의 경우 각 인덱서가 동일한 기술 집합을 가리키도록 합니다.

   + 각 인덱서 정의 내에 동일한 런타임 실행 패턴을 예약합니다. 예를 들어, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }`는 2018-05-15에 모든 인덱서에 대해 8시간 간격으로 실행되는 일정을 만듭니다.

예약된 시간에 모든 인덱서는 실행, 데이터 로드, 보강(Cognitive Search 파이프라인을 구성한 경우) 적용, 인덱스에 쓰기 작업을 시작합니다. Azure Search는 업데이트 시 인덱스를 잠그지 않습니다. 동시 쓰기가 관리되며 첫 번째 시도에서 특정 쓰기가 성공하지 못하면 다시 시도됩니다.

> [!Note]
> 복제본을 늘릴 때 인덱스 크기가 상당히 증가할 것으로 예상되면 파티션 수를 늘리는 것이 좋습니다. 파티션은 인덱싱된 콘텐츠 조각을 저장합니다. 파티션이 많을수록 각 파티션이 저장해야 하는 조각이 줄어듭니다.

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Search의 보안](search-security-overview.md)