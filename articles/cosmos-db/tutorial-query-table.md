---
title: Azure Cosmos DB에서 테이블 데이터를 쿼리하는 방법 | Microsoft Docs
description: Azure Cosmos DB에서 테이블 데이터를 쿼리하는 방법을 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: kanshiG
manager: kfile
editor: ''
tags: ''
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: d3b46169f7cc175d1959ecaa7184faa0fd81590c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>자습서: Table API를 사용하여 Azure Cosmos DB 쿼리

Azure Cosmos DB의 [Table API](table-introduction.md)는 키/값(테이블) 데이터에 대한 OData 및 [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) 쿼리를 지원합니다.  

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Table API를 사용한 데이터 쿼리

이 문서의 쿼리에서는 다음 샘플 `People` 테이블을 사용합니다.

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

테이블 API를 사용하여 쿼리하는 방법에 대한 자세한 내용은 [테이블 및 엔터티 쿼리](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities)를 참조하세요. 

Azure Cosmos DB에서 제공하는 프리미엄 기능에 대한 자세한 내용은 [Azure Cosmos DB: Table API](table-introduction.md) 및 [.NET에서 Table API를 통해 개발](tutorial-develop-table-dotnet.md)을 참조하세요. 

## <a name="prerequisites"></a>필수 조건

이러한 쿼리가 작동하려면 Azure Cosmos DB 계정이 있어야 하며 컨테이너에 엔터티 데이터가 있어야 합니다. 이들 중 하나라도 없는가요? 그러면 [5분 빠른 시작](create-table-dotnet.md) 또는 [개발자 자습서](tutorial-develop-table-dotnet.md)를 수행하여 계정을 만들고 데이터베이스를 채워 놓으세요.

## <a name="query-on-partitionkey-and-rowkey"></a>PartitionKey 및 RowKey에 대한 쿼리
PartitionKey 및 RowKey 속성은 엔터티의 기본 키를 구성하므로 다음과 같은 특수 구문을 사용하여 엔터티를 식별할 수 있습니다. 

**쿼리**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**결과**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

또는 다음 섹션과 같이 `$filter` 옵션의 일부로 이러한 속성을 지정할 수 있습니다. 키 속성 이름과 상수 값은 대/소문자를 구분합니다. PartitionKey 및 RowKey 속성은 모두 문자열 형식입니다. 

## <a name="query-by-using-an-odata-filter"></a>OData 필터를 사용하여 쿼리
필터 문자열을 생성할 때는 다음 규칙에 유의하세요. 

* OData 프로토콜 사양에 정의된 논리 연산자를 사용하여 속성과 값을 비교합니다. 동적 값과 속성을 비교할 수 없습니다. 식의 한 쪽은 상수여야 합니다. 
* 속성 이름, 연산자 및 상수 값은 URL로 인코딩된 공백으로 구분해야 합니다. URL로 인코딩된 공백은 `%20`입니다. 
* 필터 문자열의 모든 부분은 대/소문자를 구분합니다. 
* 상수 값은 유효한 결과를 반환하는 필터에 대한 속성과 동일한 데이터 형식이어야 합니다. 속성 형식에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)를 참조하세요. 

다음은 OData `$filter`를 사용하여 PartitionKey 및 Email 속성으로 필터링하는 방법을 보여 주는 예제 쿼리입니다.

**쿼리**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

다양한 데이터 형식에 대한 필터 식을 생성하는 방법에 대한 자세한 내용은 [테이블 및 엔터티 쿼리](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities)를 참조하세요.

**결과**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>LINQ를 사용하여 쿼리 
해당 OData 쿼리 식으로 변환되는 LINQ를 사용하여 쿼리할 수도 있습니다. 다음은 .NET SDK를 사용하여 쿼리를 작성하는 방법을 보여 주는 예제입니다.

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Table API를 사용하여 쿼리하는 방법

이제 전 세계로 데이터를 배포하는 방법을 알아보려면 다음 자습서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [전 세계로 데이터 배포](tutorial-global-distribution-table.md)
