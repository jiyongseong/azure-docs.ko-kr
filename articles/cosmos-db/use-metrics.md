---
title: Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버깅 | Microsoft Docs
description: Azure Cosmos DB에서 메트릭을 사용하여 일반적인 문제를 디버그하고 데이터베이스를 모니터링합니다.
keywords: 메트릭
services: cosmos-db
author: gnot
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: a2934e636166867ce158d79897d8f1fccb1699fc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버깅

Azure Cosmos DB는 처리량, 저장소, 일관성, 가용성 및 대기 시간에 대한 메트릭을 제공합니다. [Azure Portal](https://portal.azure.com)에서는 이러한 메트릭의 집계 보기를 제공합니다. 보다 세부적인 메트릭을 위해 클라이언트 SDK와 [진단 로그](./logging.md) 둘 다를 사용할 수 있습니다.

새 메트릭에 대한 개요를 가져오고 데이터베이스에서 핫 파티션을 찾는 방법을 알아보려면 다음 Azure Friday 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

이 문서에서는 일반적인 사용 사례와 Azure Cosmos DB 메트릭을 사용하여 이러한 문제를 분석 및 디버그할 수 있는 방법을 안내합니다. 메트릭은 5분마다 수집되고 7일 동안 유지됩니다.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>성공한 요청 수 또는 오류가 발생하는 요청 수 이해

시작하려면 [Azure Portal](https://portal.azure.com)에서 **메트릭** 블레이드로 이동합니다. 블레이드에서 **1분당 용량을 초과한 요청 수** 차트를 찾습니다. 이 차트에서는 상태 코드로 분할된 총 요청 수를 분 단위로 보여 줍니다. HTTP 상태 코드에 대한 자세한 내용은 [Azure Cosmos DB에 대한 HTTP 상태 코드](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)를 참조하세요.

가장 일반적인 오류 상태 코드는 429(제한)로, Azure Cosmos DB에 대한 요청이 프로비전된 처리량을 초과함을 의미합니다. 가장 일반적인 솔루션은 지정된 컬렉션에 대한 [RU를 확장](./set-throughput.md)합니다.

![분당 요청 수](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>파티션의 처리량 배포 확인

파티션 키의 좋은 카디널리티 사용은 확장 가능한 모든 응용 프로그램에 필요합니다. 모든 분할된 컬렉션의 처리량 배포를 파티션으로 분석하여 확인하려면 [Azure Portal](https://portal.azure.com)의 **메트릭 블레이드**로 이동합니다. **처리량** 탭에서 저장소 분석은 **각 실제 파티션별 사용된 최대 RU/초** 차트에 표시됩니다. 다음 그림은 맨 왼쪽에 있는 불일치 파티션에서 알 수 있듯이 잘못된 데이터 배포의 예를 보여 줍니다. 

![오후 3시 05분에 높은 사용량을 표시하는 단일 파티션](media/use-metrics/metrics-17.png)

불균등 처리량 배포 시 *핫* 파티션이 발생할 수 있고, 이로 인해 제한된 요청이 초래되고 다시 분할해야 할 수 있습니다. Azure Cosmos DB에서 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](./partition-data.md)을 참조하세요.

## <a name="determining-the-storage-distribution-across-partitions"></a>파티션의 저장소 배포 확인

파티션의 좋은 카디널리티 사용은 확장 가능한 모든 응용 프로그램에 필요합니다. 모든 분할된 컬렉션의 처리량 배포를 파티션으로 분석하여 확인하려면 [Azure Portal](https://portal.azure.com)의 메트릭 블레이드로 이동합니다. 처리량 탭에서 저장소 분석은 각 실제 파티션별 사용된 최대 RU/초 차트에 표시됩니다. 다음 그림은 맨 왼쪽에 있는 불일치 파티션에서 알 수 있듯이 잘못된 데이터 배포를 보여 줍니다. 

![불량 데이터 배포의 예](media/use-metrics/metrics-07.png)

차트에서 파티션을 클릭하여 배포 불균등의 근본 원인인 파티션 키를 확인할 수 있습니다. 

![파티션 키로 인해 배포 불균등 발생](media/use-metrics/metrics-05.png)

배포 불균등을 초래하는 파티션 키를 식별한 후 더 많이 배포된 파티션 키로 컬렉션을 다시 분할해야 할 수도 있습니다. Azure Cosmos DB에서 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](./partition-data.md)을 참조하세요.

## <a name="comparing-data-size-against-index-size"></a>데이터 크기 및 인덱스 크기 비교

Azure Cosmos DB에서 사용된 총 저장소는 데이터 크기와 인덱스 크기의 조합입니다. 일반적으로 인덱스 크기는 데이터 크기의 비율입니다. [Azure Portal](https://portal.azure.com)의 메트릭 블레이드에서, 저장소 탭에는 저장소 사용량이 데이터 및 인덱스에 따라 분석되어 표시됩니다. 이미지(예상) 또는 SDK에서 컬렉션 읽기를 통해 현재 저장소 사용량을 찾을 수 있습니다.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
인덱스 공간을 절약하려는 경우 [인덱싱 정책](./indexing-policies.md)을 조정할 수 있습니다.

## <a name="debugging-why-queries-are-running-slow"></a>쿼리가 느리게 실행되는 이유 디버깅

SQL API SDK에서 Azure Cosmos DB는 쿼리 실행 통계를 제공합니다. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics*는 쿼리의 각 구성 요소를 실행하는 데 걸린 기간을 자세히 보여 줍니다. 장기 실행 쿼리의 가장 일반적인 근본 원인은 검색(쿼리에서 인덱스를 활용할 수 없음)으로, 더 나은 필터 조건을 사용해서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Portal에 제공된 메트릭을 사용하여 문제를 모니터링 및 디버그하는 방법을 알게 되었으므로 다음 문서를 참조하여 데이터베이스 성능을 개선하는 방법을 자세히 알아보는 것이 좋습니다.

* [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)
* [Azure Cosmos DB에 대한 성능 팁](performance-tips.md)
