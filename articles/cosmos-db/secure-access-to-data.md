---
title: Azure Cosmos DB에서 데이터에 대한 액세스를 보호하는 방법 | Microsoft Docs
description: 마스터 키, 읽기 전용 키, 사용자 및 권한을 포함해서 Azure Cosmos DB의 액세스 제어 개념에 대해 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: sngun
ms.openlocfilehash: 7a53dda7d6b49187d77ca44bcb55db5f9c305f64
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Azure Cosmos DB 데이터에 대한 액세스 보호
이 문서에서는 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 저장된 데이터에 대한 액세스를 보호하는 방법을 개괄적으로 설명합니다.

Azure Cosmos DB는 두 가지 유형의 키를 사용하여 사용자를 인증하고 해당 데이터 및 리소스에 대한 액세스를 제공합니다. 

|키 유형|리소스|
|---|---|
|[마스터 키](#master-keys) |데이터베이스 계정, 데이터베이스, 사용자, 사용 권한을 비롯한 관리 리소스에 사용됩니다.|
|[리소스 토큰](#resource-tokens)|컬렉션, 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF를 비롯한 응용 프로그램 리소스에 사용됩니다.|

<a id="master-keys"></a>

## <a name="master-keys"></a>마스터 키 

마스터 키는 데이터베이스 계정에 대한 모든 관리 리소스에 대한 액세스를 제공합니다. 마스터 키:  
- 계정, 데이터베이스, 사용자 및 사용 권한에 대한 액세스를 제공합니다. 
- 컬렉션 및 문서에 대한 세분화된 액세스를 제공하는 데 사용할 수 없습니다.
- 계정 생성 중에 만들어집니다.
- 언제든지 다시 생성할 수 있습니다.

각 계정은 두 개의 마스터 키(주 키와 보조 키)로 구성됩니다. 이중 키를 사용하는 목적은 키를 다시 생성하거나 롤링하여 계정 및 데이터에 지속적인 액세스를 제공하는 것입니다. 

Cosmos DB 계정에 대한 두 개의 마스터 키 외에도 두 개의 읽기 전용 키가 있습니다. 이러한 읽기 전용 키는 계정에 대한 읽기 작업만 허용합니다. 읽기 전용 키는 읽기 권한 리소스에 대한 액세스를 제공하지 않습니다.

Azure Portal을 사용하여 주, 보조, 읽기 전용 및 읽기-쓰기 마스터 키를 검색 및 다시 생성할 수 있습니다. 자세한 내용은 [액세스 키 보기, 복사 및 다시 생성](manage-account.md#keys)을 참조하세요.

![Azure Portal에서 액세스 제어(IAM) - NoSQL 데이터베이스 보안 설명](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

마스터 키를 회전하는 프로세스는 간단합니다. Azure Portal로 이동하여 보조 키를 검색한 후 응용 프로그램에서 주 키를 보조 키로 바꾼 다음 Azure Portal에서 주 키를 회전합니다.

![Azure Portal에서 마스터 키 회전 - NoSQL 데이터베이스 보안 설명](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>마스터 키를 사용할 코드 샘플

다음 코드 샘플에서는 Cosmos DB 계정 끝점 및 마스터 키를 사용해서 DocumentClient를 인스턴스화하고 데이터베이스를 만듭니다. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>리소스 토큰

리소스 토큰은 데이터베이스 내에서 응용 프로그램 리소스에 대한 액세스를 제공합니다. 리소스 토큰:
- 특정 컬렉션, 파티션 키, 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF에 대한 액세스를 제공합니다.
- [사용자](#users)에게 특정 리소스에 대한 [사용 권한](#permissions)이 부여된 경우 생성됩니다.
- POST, GET 또는 PUT 호출로 권한 리소스가 작동할 때 다시 생성됩니다.
- 사용자, 리소스 및 사용 권한을 위해 특별히 구성된 해시 리소스 토큰을 사용합니다.
- 시간은 사용자 지정 가능한 유효 기간으로 제한됩니다. 기본 유효 기간은 1시간입니다. 하지만 토큰 수명은 최대 5시간까지 명시적으로 지정할 수 있습니다.
- 마스터 키를 전달하기 위한 안전한 대안을 제공합니다. 
- Cosmos DB 계정에서 클라이언트가 부여된 권한에 따라 리소스를 읽고 쓰고 삭제할 수 있도록 합니다.

Cosmos DB 계정에 있는 리소스에 대한 액세스 권한을 마스터 키로 신뢰할 수 없는 클라이언트에 제공하려는 경우 Cosmos DB 사용자 및 권한을 만들어서 리소스 토큰을 사용할 수 있습니다.  

Cosmos DB 리소스 토큰은 사용자가 부여한 권한에 따라 마스터 또는 읽기 전용 키에 대한 요구 없이 Cosmos DB 계정에서 클라이언트가 리소스를 읽고, 쓰고, 삭제할 수 있게 해주는 안전한 대안을 제공합니다.

다음은 리소스 토큰을 요청, 생성 및 클라이언트에 제공할 수 있는 일반적인 디자인 패턴이 있습니다.

1. 중간 계층 서비스는 모바일 응용 프로그램이 사용자 사진을 공유하도록 설정됩니다. 
2. 중간 계층 서비스는 Cosmos DB 계정의 마스터 키를 소유합니다.
3. 사진 앱은 최종 사용자 모바일 장치에 설치됩니다. 
4. 로그인하면 사진 앱이 중간 계층 서비스를 사용해서 사용자의 ID를 설정합니다. ID 설정 방식은 순전히 응용 프로그램에 달려 있습니다.
5. ID가 설정된 다음에는 중간 계층 서비스 요청 권한이 ID를 기반으로 합니다.
6. 중간 계층 서비스는 리소스 토큰을 다시 전화 앱으로 전송합니다.
7. 전화 앱은 계속 리소스 토큰을 사용해서 리소스의 토큰으로 정의된 권한을 사용하여 리소스 토큰에서 허용하는 간격으로 Cosmos DB 리소스에 직접 액세스합니다. 
8. 리소스 토큰이 만료되면 후속 요청은 401 허가되지 않은 예외를 수신합니다.  이 지점에서는 전화 앱이 ID 를 다시 설정하고 새로운 리소스 토큰을 요청합니다.

    ![Azure Cosmos DB 리소스 토큰 워크플로](./media/secure-access-to-data/resourcekeyworkflow.png)

리소스 토큰 생성 및 관리는 네이티브 Cosmos DB 클라이언트 라이브러리에서 처리하지만 REST를 사용하는 경우 요청/인증 헤더를 구성해야 합니다. REST에 대한 인증 헤더를 만드는 방법에 대한 자세한 내용은 [Cosmos DB 리소스에 대한 Access Control](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) 또는 [SDK에 대한 소스 코드](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js)를 참조하세요.

리소스 토큰을 생성하거나 broker하는 데 사용되는 중간 계층 서비스의 예는 [ResourceTokenBroker 앱](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)을 참조하세요.

<a id="users"></a>

## <a name="users"></a>사용자
Cosmos DB 사용자는 Cosmos DB 데이터베이스와 연결됩니다.  각 데이터베이스는 0개 이상의 Cosmos DB 사용자를 포함할 수 있습니다.  다음 코드 샘플에서는 Cosmos DB 사용자 리소스를 만드는 방법을 보여 줍니다.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> 각 Cosmos DB 사용자에게는 해당 사용자와 관련된 [권한](#permissions) 목록을 검색하기 위해 사용할 수 있는 PermissionsLink 속성이 있습니다.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>권한
Cosmos DB 권한 리소스는 Cosmos DB 사용자와 연결됩니다.  각 사용자는 0개 이상의 Cosmos DB 권한을 포함할 수 있습니다.  권한 리소스는 사용자가 특정 응용 프로그램 리소스에 액세스하려고 시도할 때 필요한 보안 토큰에 대한 액세스 권한을 제공합니다.
권한 리소스에서 제공될 수 있는 사용 가능한 액세스 수준은 다음 두 가지입니다.

* 전체: 사용자가 리소스에 대한 모든 권한을 갖습니다.
* 읽기: 사용자가 리소스 내용을 읽을 수만 있고 리소스에 대해 쓰기, 업데이트 또는 삭제 작업을 수행할 수 없습니다.

> [!NOTE]
> Cosmos DB 저장 프로시저를 실행하려면 사용자에게 저장 프로시저가 실행되는 컬렉션에 대한 모든 권한이 있어야 합니다.
> 
> 

### <a name="code-sample-to-create-permission"></a>사용 권한을 만드는 코드 샘플

다음 코드 샘플은 권한 리소스를 만들고, 권한 리소스의 리소스 토큰을 읽고, 위에서 생성된 [사용자](#users)와 사용 권한을 연결시키는 방법을 보여 줍니다.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

컬렉션에 파티션 키를 지정한 다음 컬렉션에 대한 권한을 지정한 경우 문서 및 첨부 파일 리소스는 ResourceLink 외에도 ResourcePartitionKey를 포함해야 합니다.

### <a name="code-sample-to-read-permissions-for-user"></a>사용자에 대한 권한을 읽는 코드 샘플

특정 사용자와 관련된 모든 권한 리소스를 쉽게 가져오기 위해, Cosmos DB는 각 사용자 개체에 대해 권한 피드를 사용할 수 있도록 설정합니다.  다음 코드 조각은 위에서 생성된 사용자와 연관된 권한을 검색하고, 권한 목록을 생성하고, 사용자를 대신해서 새 DocumentClient를 인스턴스화하는 방법을 보여 줍니다.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>다음 단계
* Cosmos DB 데이터베이스 보안에 대한 자세한 내용은 [Cosmos DB: 데이터베이스 보안](database-security.md)을 참조하세요.
* 마스터 키와 읽기 전용 키를 관리하는 방법에 대한 자세한 내용은 [Azure Cosmos DB 계정을 관리하는 방법](manage-account.md#keys)을 참조하세요.
* Azure Cosmos DB 권한 부여 토큰을 생성하는 방법에 대한 자세한 내용은 [Azure Cosmos DB 리소스에 대한 Access Control](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)을 참조하세요.
