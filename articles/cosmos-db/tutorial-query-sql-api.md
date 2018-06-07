---
title: Azure Cosmos DB에서 SQL을 사용하여 쿼리하는 방법 | Microsoft Docs
description: Azure Cosmos DB에서 SQL을 사용하여 쿼리하는 방법을 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.openlocfilehash: 131296d5219dffb2c4d7602b61b3c69b390e211e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>자습서: SQL API를 사용하여 Azure Cosmos DB 쿼리

Azure Cosmos DB [SQL API](documentdb-introduction.md)는 SQL을 사용하여 문서를 쿼리할 수 있도록 지원합니다. 이 문서에서는 샘플 문서 및 두 가지 샘플 SQL 쿼리와 결과를 제공합니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * SQL을 사용한 데이터 쿼리

## <a name="sample-document"></a>샘플 문서

이 문서의 SQL 쿼리에서는 다음과 같은 샘플 문서를 사용합니다.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="where-can-i-run-sql-queries"></a>SQL 쿼리를 실행할 수 있는 위치

Azure Portal의 [데이터 탐색기]를 사용하여 [REST API 및 SDK](sql-api-sdk-dotnet.md)를 통해 쿼리를 실행할 수 있으며, 기존 샘플 데이터 집합에 대해 쿼리를 실행하는 [쿼리 실습](https://www.documentdb.com/sql/demo)도 사용할 수 있습니다.

SQL 쿼리에 대한 자세한 내용은 다음을 참조하세요.
* [SQL 쿼리 및 SQL 구문](sql-api-sql-query.md)

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 Azure Cosmos DB 계정과 컬렉션이 있다고 가정합니다. 이들 중 하나라도 없는가요? [5분 빠른 시작](create-mongodb-nodejs.md)을 완료하세요.

## <a name="example-query-1"></a>예제 쿼리 1

위의 샘플 가족 문서를 고려해 볼 때 다음 SQL 쿼리에서는 ID 필드가 `WakefieldFamily`와 일치하는 문서를 반환합니다. `SELECT *` 문이므로 쿼리 결과는 완전한 JSON 문서입니다.

**쿼리**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**결과**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>예제 쿼리 2

다음 쿼리에서는 ID가 `WakefieldFamily`와 일치하는 가족의 자식 이름을 학년(grade) 순서로 정렬하여 모두 반환합니다.

**쿼리**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**결과**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * SQL을 사용하여 쿼리하는 방법  

이제 전 세계로 데이터를 배포하는 방법을 알아보려면 다음 자습서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [전 세계로 데이터 배포](tutorial-global-distribution-sql-api.md)

