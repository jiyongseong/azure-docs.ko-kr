---
title: Azure Cosmos DB MongoDB API에서 인덱싱 | Microsoft Docs
description: Azure Cosmos DB MongoDB API에서 인덱싱 기능에 대한 개요를 제공합니다.
services: cosmos-db
documentationcenter: ''
author: orestis-ms
manager: kfile
editor: ''
ms.assetid: daacbabf-1bb5-497f-92db-079910703047
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: 6cb267096e04d4957b166e539a4fc58aa25e04cb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB: MongoDB API에서 인덱싱

Azure Cosmos DB MongoDB API는 Azure Cosmos DB의 자동 인덱스 관리 기능을 활용합니다. 결과적으로, 사용자는 Azure Cosmos DB의 기본 인덱싱 정책에 액세스할 수 있습니다. 따라서 사용자에 의해 정의된 인덱스가 없거나 삭제된 인덱스가 없는 경우 모든 필드는 컬렉션에 삽입될 때 기본적으로 자동으로 인덱싱됩니다. 대부분의 시나리오의 경우 계정에서 설정된 기본 인덱싱 정책을 사용하는 것이 좋습니다.

## <a name="dropping-the-default-indexes"></a>기본 인덱스 삭제

컬렉션 ```coll```에 대한 기본 인덱스를 삭제하는 데 다음 명령을 사용할 수 있습니다.

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>복합 인덱스 만들기

복합 인덱스는 문서의 여러 필드에 대한 참조를 유지합니다. 논리적으로 필드당 여러 개의 개별 인덱스 만들기와 동일합니다. Cosmos DB 인덱싱 기술에서 제공하는 최적화를 활용하려면 단일(비고유) 복합 인덱스 대신 여러 개의 개별 인덱스를 만드는 것이 좋습니다.

## <a name="creating-unique-indexes"></a>고유 인덱스 만들기

[고유 인덱스](unique-keys.md)는 두 개 이상의 문서에 인덱싱된 필드에 대해 동일한 값을 포함하지 않도록 하는 데 유용합니다. 
>[!important] 
> 현재 컬렉션이 비어 있는 경우에만 고유 인덱스를 만들 수 있습니다(문서를 포함하지 않음). 

다음 명령은 "student_id" 필드에 고유 인덱스를 만듭니다.

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

분할된 컬렉션의 경우 MongoDB 동작에 따라 고유 인덱스를 만드는 데 분할된(파티션) 키를 제공해야 합니다. 즉, 분할된 컬렉션의 모든 고유 인덱스는 필드 중 하나가 파티션 키인 복합 인덱스입니다.

다음 명령은 student_id 및 university 필드에서 고유 인덱스로 분할된 컬렉션 ```coll```(분할 키는 ```university```)을 만듭니다.

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

위의 예에서 ```"university":1``` 절이 생략되면 다음 메시지와 함께 오류가 반환됩니다.

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL 인덱스

특정 컬렉션에서 문서 만료를 활성화하려면 ["TTL index"(Time to Live 인덱스)](../cosmos-db/time-to-live.md)를 만들어야 합니다. TTL 인덱스는 "expireAfterSeconds" 값을 가진 _ts 필드의 인덱스입니다.
 
예:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

위의 명령은 마지막 10초에서 수정되지 않은 ```db.coll``` 컬렉션의 문서를 삭제합니다. 
 
> [!NOTE]
> **_ts**는 Cosmos DB 전용 필드이며 MongoDB 클라이언트에서 액세스할 수 없습니다. 문서의 마지막 수정의 타임스탬프를 포함하는 예약된(시스템) 속성입니다.
>

## <a name="migrating-collections-with-indexes"></a>인덱스를 사용하여 컬렉션 마이그레이션

현재 컬렉션에 문서가 포함되지 않은 경우에만 고유 인덱스를 만들 수 있습니다. 인기 있는 MongoDB 마이그레이션 도구는 데이터를 가져온 후 고유 인덱스를 만들도록 시도합니다. 이 문제를 방지하기 위해 사용자는 마이그레이션 도구를 허용하는 대신 해당 컬렉션 및 고유 인덱스를 수동으로 만드는 것이 좋습니다(```mongorestore```의 경우 이 동작은 명령줄에서 --noIndexRestore 플래그를 사용하여 수행됨).

## <a name="next-steps"></a>다음 단계
* [Azure Cosmos DB는 데이터를 어떻게 인덱싱하나요?](../cosmos-db/indexing-policies.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB 컬렉션의 데이터 만료](../cosmos-db/time-to-live.md)
