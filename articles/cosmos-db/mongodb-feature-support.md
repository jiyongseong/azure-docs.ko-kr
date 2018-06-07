---
title: MongoDB에 대한 Azure Cosmos DB 기능 지원 | Microsoft Docs
description: Azure Cosmos DB MongoDB API가 MongoDB 3.4에 대해 제공하는 기능 지원에 대해 알아봅니다.
services: cosmos-db
author: alekseys
manager: kfile
documentationcenter: ''
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: cadf637dd3a71e040fef8188f7290907659e5cdb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>MongoDB 기능 및 구문에 대한 MongoDB API 지원

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. 임의의 오픈 소스 MongoDB 클라이언트 [드라이버](https://docs.mongodb.org/ecosystem/drivers)를 통해 데이터베이스의 MongoDB API와 통신할 수 있습니다. MongoDB API에서는 MongoDB [유선 프로토콜](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)을 준수함으로써 기존 클라이언트 드라이버를 사용할 수 있습니다.

Azure Cosmos DB MongoDB API를 사용하면 Azure Cosmos DB가 제공하는 모든 엔터프라이즈 기능과 함께 익숙한 MongoDB API의 이점을 누릴 수 있습니다. 이러한 기능에는 [전역 배포](distribute-data-globally.md), [자동 분할](partition-data.md), 가용성 및 대기 시간 보장, 모든 필드의 자동 인덱싱, 휴식 시 암호화, 백업 등이 포함됩니다.

## <a name="mongodb-query-language-support"></a>MongoDB 쿼리 언어 지원

Azure Cosmos DB MongoDB API는 MongoDB 쿼리 언어 구문을 포괄적으로 지원합니다. 아래에서 현재 지원되는 연산, 연산자, 단계, 명령 및 옵션에 대한 자세한 목록을 찾을 수 있습니다.


## <a name="database-commands"></a>데이터베이스 명령

Azure Cosmos DB는 모든 MongoDB API 계정에서 다음 데이터베이스 명령을 지원합니다. 

### <a name="query-and-write-operation-commands"></a>쿼리 및 쓰기 작업 명령
- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>인증 명령
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>관리 명령
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>진단 명령
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>집계 파이프라인</a>

Azure Cosmos DB는 공개 미리 보기에서 집계 파이프라인을 지원합니다. 공개 미리 보기를 이용하는 방법에 대한 지침은 [Azure 블로그](https://aka.ms/mongodb-aggregation)를 참조하세요.

### <a name="aggregation-commands"></a>집계 명령
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>집계 단계
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>집계 식

#### <a name="boolean-expressions"></a>부울 식
- $and
- $or
- $not

#### <a name="set-expressions"></a>식 설정
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>비교 식
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>산술 식
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>문자열 식
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>배열 식
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>날짜 식
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>조건부 식
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>집계 누적기
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>연산자

다음 연산자는 해당 사용 예제에서 지원됩니다. 아래의 쿼리에서 사용되는 이 샘플 문서를 고려하세요.

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

연산자 | 예 |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>메모

$regex 쿼리에서 왼쪽에 고정된 식은 인덱스 검색을 허용합니다. 그러나 ‘i’ 한정자(대/소문자 구분 없음) 및 ‘m’ 한정자(여러 줄)를 사용하면 모든 식에서 컬렉션 검사가 수행됩니다.
‘$’ 또는 ‘|’을 포함해야 하는 경우 두 개 이상의 정규식 쿼리를 만드는 것이 가장 좋습니다. 예를 들어, 원래 쿼리가 ```find({x:{$regex: /^abc$/})```인 경우 이를 ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```로 수정해야 합니다.
첫 번째 부분은 인덱스를 사용하여 ^abc로 시작하는 문서로 검색을 제한하며, 두 번째 부분은 정확한 항목의 일치를 확인합니다. 막대 연산자 ‘|’는 “or” 함수 역할을 하고, ```find({x:{$regex: /^abc|^def/})``` 쿼리는 ‘x’ 필드에 “abc” 또는 “def”로 시작하는 값이 있는 문서를 일치하는 항목으로 검색합니다. 인덱스를 사용하려면 쿼리를 $or 연산자 ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```에 의해 조인된 두 개의 서로 다른 쿼리로 나누는 것이 좋습니다.

### <a name="update-operators"></a>업데이트 연산자

#### <a name="field-update-operators"></a>필드 업데이트 연산자
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>배열 업데이트 연산자
- $addToSet
- $pop
- $pullAll
- $pull(참고: $pull에는 조건이 지원되지 않음)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>비트 업데이트 연산자
- $bit

### <a name="geospatial-operators"></a>지리 공간적 연산자

연산자 | 예 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 예
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 예
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 예
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 예
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 예
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | 예
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 예
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | 예
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 예
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | 예
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 예

## <a name="additional-operators"></a>추가 연산자

연산자 | 예 | 메모 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | 지원되지 않습니다. 대신 $regex 사용 

### <a name="methods"></a>메서드

다음 메서드가 지원됩니다.

#### <a name="cursor-methods"></a>커서 메서드

방법 | 예 | 메모 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | 정렬 키가 없는 문서가 반환되지 않음

## <a name="unique-indexes"></a>고유 인덱스

Azure Cosmos DB는 기본적으로 데이터베이스에 기록되는 문서에서 모든 필드를 인덱싱합니다. 고유 인덱스를 사용하면 컬렉션의 모든 문서에서 특정 필드에 중복된 값이 나타나지 않으며, 이는 기본 “_id” 키에서 고유성이 유지되는 방식과 유사합니다. 이제 ‘고유’ 제약 조건을 포함하여 createIndex 명령을 사용하여 Azure Cosmos DB에서 사용자 지정 인덱스를 만들 수 있습니다.

모든 MongoDB API 계정에 대해 고유 인덱스를 사용할 수 있습니다.

## <a name="time-to-live-ttl"></a>TTL(Time to Live)

Azure Cosmos DB는 문서의 타임스탬프를 기반으로 상대적 TTL(Time to Live)을 지원합니다. [Azure Portal](https://portal.azure.com)을 통해 MongoDB API 컬렉션에 TTL을 사용할 수 있습니다.

## <a name="user-and-role-management"></a>사용자 및 역할 관리

Azure Cosmos DB는 아직 사용자 및 역할을 지원하지 않습니다. Azure Cosmos DB는 [Azure Portal](https://portal.azure.com)(연결 문자열 페이지)을 통해 가져올 수 있는 RBAC(역할 기반 액세스 제어), 그리고 읽기-쓰기 및 읽기 전용 암호/키를 지원합니다.

## <a name="replication"></a>복제

Azure Cosmos DB는 가장 낮은 계층에서 자동의 네이티브 복제를 지원합니다. 이 논리는 또한 짧은 대기 시간, 글로벌 복제를 달성하기 위해 확장됩니다. Azure Cosmos DB는 수동 복제 명령을 지원하지 않습니다.

## <a name="sharding"></a>분할

Azure Cosmos DB는 서버 측 자동 분할을 지원합니다. Azure Cosmos DB는 수동 분할 명령을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

- MongoDB 데이터베이스용 API와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법에 대해 알아봅니다.
- MongoDB 데이터베이스용 API와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법에 대해 알아봅니다.
- MongoDB [샘플](mongodb-samples.md)에 대한 프로토콜 지원을 사용하여 Azure Cosmos DB를 탐색합니다.
