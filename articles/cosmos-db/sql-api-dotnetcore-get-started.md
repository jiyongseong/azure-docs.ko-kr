---
title: 'Azure Cosmos DB: SQL API .NET Core 시작 자습서 | Microsoft Docs'
description: Azure Cosmos DB SQL API .NET Core SDK를 사용하여 온라인 데이터베이스 및 C# 콘솔 응용 프로그램을 만드는 자습서입니다.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/12/2018
ms.author: sngun
ms.custom: devcenter
ms.openlocfilehash: 81c7767852b25c6e075c48feb245227643799316
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-getting-started-with-the-sql-api-and-net-core"></a>Azure Cosmos DB: SQL API 및 .NET Core 시작
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [MongoDB용 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Azure Cosmos DB SQL API .NET Core 시작 자습서를 시작합니다. 이 자습서를 따라 하면 Azure Cosmos DB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 생깁니다.

이 자습서에서는 다음 내용을 다룹니다.

* Azure Cosmos DB 계정 만들기 및 연결
* Visual Studio 솔루션 구성
* 온라인 데이터베이스 만들기
* 컬렉션 만들기
* JSON 문서 만들기
* 컬렉션 쿼리
* 문서 바꾸기
* 문서 삭제
* 데이터베이스 삭제

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침을 보려면 [전체 솔루션 다운로드 섹션](#GetSolution) 으로 이동하세요.

SQL API 및 .NET Core SDK를 사용하여 Xamarin iOS, Android 또는 Forms 응용 프로그램을 빌드하시겠어요? [Xamarin 및 Azure Cosmos DB를 사용하여 모바일 응용 프로그램 빌드](mobile-apps-with-xamarin.md)를 참조하세요.

이제 시작하겠습니다.

## <a name="prerequisites"></a>필수 조건

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2017이 아직 설치되지 않은 경우 무료 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. UWP(유니버설 Windows 플랫폼) 앱을 개발하는 경우에는 **Visual Studio 2017 버전 15.4** 이상을 사용해야 합니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.
    * MacOS 또는 Linux에서 작업하는 경우 원하는 플랫폼에 대한 [.NET Core SDK](https://www.microsoft.com/net/core#macos)를 설치하여 명령줄에서 .NET Core 앱을 개발할 수 있습니다. 
    * Windows에서 작업하는 경우 [.NET Core SDK](https://www.microsoft.com/net/core#windows)를 설치하여 명령줄에서 .NET Core 앱을 개발할 수 있습니다. 
    * 자체 편집기를 사용하거나 Windows, Linux, MacOS에서 작동하는 무료 [Visual Studio Code](https://code.visualstudio.com/)를 다운로드할 수 있습니다. 

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1단계: Azure Cosmos DB 계정 만들기
Azure Cosmos DB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [Visual Studio 솔루션 설치](#SetupVS)로 건너뛸 수 있습니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설치하고 [Visual Studio 솔루션 설치](#SetupVS)로 건너뜁니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>2단계: Visual Studio 솔루션 설치
1. 컴퓨터에서 **Visual Studio 2017**을 엽니다.
2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.
3. **새 프로젝트** 대화 상자에서 **템플릿** / **Visual C#** / **.NET Core**/**콘솔 응용 프로그램(.NET Core)**을 선택하고 프로젝트 이름을 **DocumentDBGettingStarted**라고 지정한 후 **확인**을 클릭합니다.

   ![새 프로젝트 창의 스크린샷](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. **솔루션 탐색기**에서 **DocumentDBGettingStarted**를 마우스 오른쪽 단추로 클릭합니다.
5. 그런 다음 메뉴를 벗어나지 않은 상태에서 **NuGet 패키지 관리...**를 클릭합니다.

   ![프로젝트의 마우스 오른쪽 단추 클릭 메뉴의 스크린샷](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. **NuGet** 탭에서 창의 맨 위에 있는 **찾아보기**를 클릭하고 검색 상자에 **azure documentdb**를 입력합니다.
7. 결과 내에서 **Microsoft.Azure.DocumentDB.Core**를 찾아 **설치**를 클릭합니다.
   라이브러리의 패키지 ID는 [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)입니다. 이 .NET Core NuGet 패키지에서 지원되지 않는 .NET Framework 버전(예: net461)을 대상으로 하는 경우 .NET Framework 4.5 이후의 모든 .NET Framework 버전을 지원하는 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)를 사용합니다.
8. 프롬프트가 표시되면 NuGet 패키지 설치를 수락하고 사용권 계약에 동의합니다.

잘하셨습니다. 이제 설치가 완료되었으므로 코드를 작성해 보겠습니다. [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)에서 이 자습서의 완성된 코드 프로젝트를 찾을 수 있습니다.

## <a id="Connect"></a>3단계: Azure Cosmos DB 계정에 연결
먼저 Program.cs에서 C# 응용 프로그램의 시작 부분에 다음 참조를 추가합니다.

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

> [!IMPORTANT]
> 이 자습서를 완료하려면 위의 종속성을 추가했는지 확인합니다.

이제, 두 가지 상수와 *클라이언트* 변수를 공용 클래스 *프로그램* 아래에 추가합니다.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

다음으로 [Azure Portal](https://portal.azure.com)로 이동하여 URI 및 기본 키를 검색합니다. Azure Cosmos DB URI와 기본 키는 응용 프로그램에서 연결할 위치를 식별하고 Azure Cosmos DB에서 응용 프로그램의 연결을 신뢰하는 데 필요합니다.

Azure Portal에서 Azure Cosmos DB 계정으로 이동한 다음 **키**를 클릭합니다.

포털에서 URI를 복사하고 program.cs 파일의 `<your endpoint URI>` 에 붙여 넣습니다. 그런 다음 포털에서 기본 키를 복사하고 `<your key>`에 붙여 넣습니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 끝점으로 `https://localhost:8081`과, [Azure Cosmos DB 에뮬레이터를 사용한 개발 방법](local-emulator.md)에서 잘 정의된 권한 부여 키를 사용합니다. < 및 >를 반드시 제거하고 끝점 및 키를 묶은 큰따옴표는 유지합니다.

![C# 콘솔 응용 프로그램을 만들기 위해 NoSQL 자습서에서 사용하는 Azure Portal의 스크린샷 액티브 허브, Azure Cosmos DB 계정 페이지의 키 단추 및 키 페이지의 URI, 기본 키 및 보조 키 값이 강조 표시된 Azure Cosmos DB 계정을 보여 줌][keys]

**DocumentClient**의 새 인스턴스를 만드는 것으로 시작 응용 프로그램을 시작해 보겠습니다.

**Main** 메서드 아래에 **GetStartedDemo**라는 이름의 새 비동기 작업을 추가하면, 새 **DocumentClient**가 인스턴스화됩니다.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

다음 코드를 추가하여 **Main** 메서드에서 비동기 작업을 실행합니다. **Main** 메서드가 예외를 catch하여 콘솔에 씁니다.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 빌드하고 실행합니다.

축하합니다! Azure Cosmos DB 계정에 성공적으로 연결되었으므로 Azure Cosmos DB 리소스 작업에 대해 살펴보겠습니다.  

## <a name="step-4-create-a-database"></a>4단계: 데이터베이스 만들기
데이터베이스 생성을 위한 코드를 추가하기 전에, 콘솔에 쓰기 위한 도우미 메서드를 추가합니다.

**GetStartedDemo** 메서드에 **WriteToConsoleAndPromptToContinue** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

**DocumentClient** 클래스의 [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 메서드를 사용하여 Azure Cosmos DB [데이터베이스](sql-api-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다.

클라이언트 생성의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다. *FamilyDB*라는 데이터베이스가 만들어집니다.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스를 성공적으로 만들었습니다.  

## <a id="CreateColl"></a>5단계: 컬렉션 만들기
> [!WARNING]
> **CreateDocumentCollectionAsync**는 가격 책정 의미가 포함된 예약된 처리량이 있는 새 컬렉션을 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

**DocumentClient** 클래스의 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 메서드를 사용하여 [컬렉션](sql-api-resources.md#collections)을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다.

데이터베이스 생성의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다. *FamilyCollection_oa*라는 문서 컬렉션이 만들어집니다.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스 컬렉션을 성공적으로 만들었습니다.  

## <a id="CreateDoc"></a>6단계: JSON 문서 만들기
**DocumentClient** 클래스의 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 메서드를 사용하여 [문서](sql-api-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 하나 이상의 문서를 삽입할 수 있습니다. 데이터베이스에 저장하려는 데이터가 이미 있다면 Azure Cosmos DB의 [데이터 마이그레이션 도구](import-data.md)를 사용할 수 있습니다.

먼저 이 샘플에서는 Azure Cosmos DB 내에 저장된 개체를 나타내는 **가족** 클래스를 만들어야 합니다. 또한 **가족** 내에서 사용되는 **부모**, **자식**, **애완 동물**, **주소** 하위 클래스를 만듭니다. 문서에는 JSON에서 **ID**로 직렬화된 **ID** 속성이 있어야 합니다. **GetStartedDemo** 메서드 다음에 다음 내부 하위 클래스를 추가하여 이러한 클래스를 만듭니다.

**WriteToConsoleAndPromptToContinue** 메서드에 **가족**, **부모**, **자식**, **애완 동물** 및 **주소** 클래스를 복사하여 붙여 넣습니다.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
public class Family
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    public string LastName { get; set; }
    public Parent[] Parents { get; set; }
    public Child[] Children { get; set; }
    public Address Address { get; set; }
    public bool IsRegistered { get; set; }
    public override string ToString()
    {
            return JsonConvert.SerializeObject(this);
    }
}

public class Parent
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
}

public class Child
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
    public string Gender { get; set; }
    public int Grade { get; set; }
    public Pet[] Pets { get; set; }
}

public class Pet
{
    public string GivenName { get; set; }
}

public class Address
{
    public string State { get; set; }
    public string County { get; set; }
    public string City { get; set; }
}
```

**CreateDocumentCollectionIfNotExists** 메서드에 **CreateFamilyDocumentIfNotExists** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

그리고 Andersen Family와 Wakefield Family의 문서 하나씩 두 개의 문서를 삽입합니다.

문서 컬렉션 생성 아래에서 **GetStartedDemo** 메서드에 `// ADD THIS PART TO YOUR CODE` 다음에 코드를 복사하고 붙여 넣습니다.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FirstName = "Henriette Thaulow",
                        Gender = "female",
                        Grade = 5,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Fluffy" }
                        }
                }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
                new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FamilyName = "Merriam",
                        FirstName = "Jesse",
                        Gender = "female",
                        Grade = 8,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Goofy" },
                                new Pet { GivenName = "Shadow" }
                        }
                },
                new Child
                {
                        FamilyName = "Miller",
                        FirstName = "Lisa",
                        Gender = "female",
                        Grade = 1
                }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 실행합니다.

축하합니다! 두 개의 Azure Cosmos DB 문서를 성공적으로 만들었습니다.  

![NoSQL에서 C# 콘솔 응용 프로그램을 만들기 위해 사용한 계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7단계: Azure Cosmos DB 리소스 쿼리
Azure Cosmos DB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](sql-api-sql-query.md)를 지원합니다.  다음 샘플 코드는 Azure Cosmos DB SQL 구문뿐 아니라 LINQ를 사용하는 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 삽입한 문서에 대해 실행할 수 있습니다.

**CreateFamilyDocumentIfNotExists** 메서드에 **ExecuteSimpleQuery** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

두 번째 문서 생성의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 컬렉션에 대한 쿼리가 성공적으로 수행되었습니다.

다음 다이어그램에서는 만든 컬렉션에 대해 Azure Cosmos DB SQL 쿼리 구문을 호출하는 방법을 보여 주며, 마찬가지로 동일한 논리가 LINQ 쿼리에 적용됩니다.

![NoSQL에서 C# 콘솔 응용 프로그램을 만들기 위해 사용한 쿼리의 의미와 범위를 보여 주는 다이어그램](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Azure Cosmos DB 쿼리는 이미 단일 컬렉션으로 범위가 지정되었기 때문에 [FROM](sql-api-sql-query.md#FromClause) 키워드는 쿼리에서 선택 사항입니다. 따라서 "FROM Families f"를 "FROM root r" 또는 선택한 다른 변수 이름으로 교체할 수 있습니다. Azure Cosmos DB는 패밀리, 루트 또는 선택한 변수 이름이 기본적으로 현재 컬렉션을 참조하는 것으로 유추합니다.

## <a id="ReplaceDocument"></a>8단계: JSON 문서 바꾸기
Azure Cosmos DB는 JSON 문서 바꾸기를 지원합니다.  

**ExecuteSimpleQuery** 메서드에 **ReplaceFamilyDocument** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

쿼리 실행의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다. 문서를 바꾼 후에, 변경된 문서를 표시하도록 같은 쿼리가 다시 실행됩니다.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 문서를 성공적으로 대체했습니다.

## <a id="DeleteDocument"></a>9단계: JSON 문서 삭제
Azure Cosmos DB는 JSON 문서 삭제를 지원합니다.  

**ReplaceFamilyDocument** 메서드에 **DeleteFamilyDocument** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

두 번째 쿼리 실행의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 문서를 성공적으로 삭제했습니다.

## <a id="DeleteDatabase"></a>10단계: 데이터베이스 삭제
만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다.

전체 데이터베이스와 모든 자식 리소스를 삭제하기 위해 문서 삭제의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

**DocumentDBGettingStarted** 단추를 클릭하여 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스를 성공적으로 삭제했습니다.

## <a id="Run"></a>11단계: C# 콘솔 응용 프로그램 모두 함께 실행
Visual Studio에서 **DocumentDBGettingStarted** 단추를 클릭하여 디버그 모드에서 응용 프로그램을 빌드합니다.

시작한 앱의 출력이 콘솔 창에 표시됩니다. 출력은 추가한 쿼리 결과를 보여 주며, 아래 예제 텍스트와 일치해야 합니다.

```
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

축하합니다! 이 자습서를 완료했으며 실행되는 C# 콘솔 응용 프로그램이 생겼습니다.

## <a id="GetSolution"></a> 전체 자습서 솔루션 가져오기
이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* [Azure Cosmos DB 계정][create-sql-api-dotnet.md#create-account].
* GitHub에서 제공하는 [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) 솔루션

Visual Studio에서 Azure Cosmos DB .NET Core SDK용 SQL API 참조를 복원하려면 솔루션 탐색기에서 **GetStarted** 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 복원 사용**을 클릭합니다. 다음으로, Program.cs 파일에서 EndpointUrl 및 AuthorizationKey 값을 [Azure Cosmos DB 계정에 연결](#Connect)에 설명된 대로 업데이트합니다.

## <a name="next-steps"></a>다음 단계
* 보다 복잡한 ASP.NET MVC 자습서가 필요하신가요? [ASP.NET MVC 자습서: Azure Cosmos DB를 사용한 웹 응용 프로그램 개발](sql-api-dotnet-application.md)을 참조하세요.
* Azure Cosmos DB .NET Core SDK용 SQL API를 사용하여 Xamarin iOS, Android 또는 Forms 응용 프로그램을 개발하시겠어요? [Xamarin 및 Azure Cosmos DB를 사용하여 모바일 응용 프로그램 빌드](mobile-apps-with-xamarin.md)를 참조하세요.
* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행하고 싶으신가요? [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)를 참조하세요.
* [Azure Cosmos DB 요청, 사용 및 저장소 모니터링](monitor-accounts.md) 방법에 대해 알아보세요.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* 프로그래밍 모델에 대한 자세히 알아보려면 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)를 참조하세요.

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
