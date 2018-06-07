---
title: Azure Cosmos DB의 고유 키 | Microsoft Docs
description: Azure Cosmos DB 데이터베이스에서 고유 키를 사용하는 방법을 알아봅니다.
services: cosmos-db
keywords: 고유 키 제약 조건, 고유 키 제약 조건 위반
author: rafats
manager: kfile
editor: monicar
documentationcenter: ''
ms.assetid: b15d5041-22dd-491e-a8d5-a3d18fa6517d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: rafats
ms.openlocfilehash: dd23f24fd817bfc443457dee30d2f3091c0d9f6b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Azure Cosmos DB의 고유 키

개발자는 고유 키를 사용하여 데이터베이스에 데이터 무결성 레이어를 추가할 수 있습니다. 컨테이너를 만들 때 고유 키 정책을 만들면 [파티션 키](partition-data.md)마다 하나 이상의 값이 고유하도록 보장됩니다. 고유 키 정책을 사용하여 컨테이너가 생성되면 고유 키 제약 조건으로 지정된 값을 가진 새 항목 또는 업데이트된 항목을 생성할 수 없습니다.   

> [!NOTE]
> 고유 키는 최신 버전의 [.NET](sql-api-sdk-dotnet.md) 및 [.NET Core](sql-api-sdk-dotnet-core.md) SQL SDK와 [MongoDB API](mongodb-feature-support.md#unique-indexes)에서 지원됩니다. Table API 및 Graph API는 아직 고유 키를 지원하지 않습니다. 
> 
>

## <a name="use-case"></a>사용 사례

이메일 주소에 고유 키 정책을 적용하면 [소셜 응용 프로그램](use-cases.md#web-and-mobile-applications)과 연결된 사용자 데이터베이스에 어떤 이점이 있는지 예를 통해 알아보겠습니다. 사용자의 이메일 주소를 고유 키로 만들면 각 레코드가 고유한 이메일 주소를 갖게 되고, 중복되는 이메일 주소로는 새 레코드를 만들 수 없습니다. 

사용자가 같은 이메일 주소를 사용하여 여러 레코드를 만들 수 있지만 이름, 성 및 이메일 주소가 같은 경우에는 만들 수 없도록 하려면 고유 키 정책에 다른 경로를 추가하면 됩니다. 따라서 단순히 이메일 주소를 기반으로 고유 키를 만드는 대신 이름, 성, 전자 메일의 조합으로 고유 키를 만들 수 있습니다. 이 경우 세 경로의 고유한 조합이 가능하므로 데이터베이스에 다음 경로 값을 가진 항목이 포함될 수 있습니다. 이러한 각 레코드는 고유 키 정책을 전달합니다.  

**고유 키 이름, 성, 전자 메일에 허용되는 값**

|이름|성|메일 주소|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

위의 표에 나열된 아무 조합을 사용하여 또 다른 레코드를 삽입하려고 시도하면 고유 키 제약 조건이 충족되지 않았다는 오류가 표시됩니다. Azure Cosmos DB에서 "지정된 ID 또는 이름을 사용하는 리소스가 이미 있습니다." 또는 "지정된 ID, 이름 또는 고유 인덱스를 사용하는 리소스가 이미 있습니다."라는 오류를 반환합니다. 

## <a name="using-unique-keys"></a>고유 키 사용

고유 키는 컨테이너를 만들 때 정의해야 하며 파티션 키까지 고유 키의 범위에 포함됩니다. 이전 예제에 대입해 보면, 우편 번호를 기반으로 파티션을 분할하면 각 파티션서 중복되는 테이블의 레코드가 있을 수 있습니다.

고유 키를 사용하도록 기존 컨테이너를 업데이트할 수 없습니다.

고유 키 정책을 사용하여 컨테이너를 만들면 컨테이너를 다시 만들기 전에는 정책을 변경할 수 없습니다. 고유 키를 구현하고 싶은 기존 데이터가 있는 경우 새 컨테이너를 만든 다음 적절한 데이터 마이그레이션 도구를 사용하여 데이터를 새 컨테이너로 이동합니다. SQL 컨테이너의 경우 [데이터 마이그레이션 도구](import-data.md)를 사용합니다. MongoDB 컨테이너의 경우 [mongoimport.exe 또는 mongorestore.exe](mongodb-migrate.md)를 사용합니다.

각 고유 키에 최대 16경로 값(예: /firstName, /lastName, /address/zipCode 등)을 포함할 수 있습니다. 

고유한 키 정책에는 각각 최대 10개의 고유한 키 제약 조건이 있을 수 있습니다. 또는 모든 고유한 인덱스 속성에 대해 조합과 결합된 경로는 60자를 초과하지 않아야 합니다. 이름, 성 및 이메일 주소를 사용하는 이전 예제는 제약 조건이 하나뿐이고, 최대 16경로 중 3개를 사용합니다. 

컨테이너에 대한 고유한 키 정책이 있으면 항목을 만들고 업데이트하고 삭제하기 위한 요청 단위 요금이 약간 더 비쌉니다. 

스파스 고유 키는 지원되지 않습니다. 일부 고유 경로의 값이 누락되면 특수 null 값으로 간주되어 고유성 제약 조건에 포함됩니다.

## <a name="sql-api-sample"></a>SQL API 샘플

다음 코드 샘플에서는 두 개의 고유 키 제약 조건이 있는 새 SQL 컨테이너를 만드는 방법을 보여 줍니다. 첫 번째 제약 조건은 이전 예제에서 설명한 이름, 성, 이메일 주소입니다. 두 번째 제약 조건은 사용자 주소/우편 번호입니다. 이 고유 키 정책의 경로를 사용하는 샘플 JSON 파일은 코드 예제를 따릅니다. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipCode" } },

                }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

샘플 JSON 문서.

```json
{
    "id": "1",
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": [
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipCode": 98012
        }
    ],
}
```
## <a name="mongodb-api-sample"></a>MongoDB API 샘플

다음 명령 샘플은 MongoDB API에 대한 사용자 컬렉션의 이름, 성 및 전자 메일 필드에 대한 고유 인덱스를 만드는 방법을 보여 줍니다. 이렇게 하면 컬렉션의 모든 문서에서 세 필드 조합의 고유성이 보장됩니다. MongoDB API 컬렉션의 경우 고유 인덱스는 컬렉션이 생성되었지만 아직 컬렉션을 채우지는 않은 시점에 생성됩니다.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 데이터베이스의 항목에 대한 고유 키를 만드는 방법을 배웠습니다. 이름에 대한 컨테이너를 만들려는 경우 고유 키와 파티션 키가 서로를 이용하므로 [Azure Cosmos DB에서 데이터 분할](partition-data.md)을 참조하세요. 


