---
title: 'Azure Cosmos DB: SQL API 시작 자습서 | Microsoft Docs'
description: SQL API를 사용하여 온라인 데이터베이스 및 C# 콘솔 응용 프로그램을 만드는 자습서입니다.
keywords: NoSQL 자습서, 온라인 데이터베이스, C# 콘솔 응용 프로그램
services: cosmos-db
documentationcenter: .net
author: AndrewHoh
manager: kfile
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2017
ms.author: anhoh
ms.openlocfilehash: 9de183ccd60dca0b8c67bc3e17136212b7132439
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-sql-api-getting-started-tutorial"></a>Azure Cosmos DB: SQL API 시작 자습서
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [MongoDB용 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Azure Cosmos DB SQL API 시작 자습서를 시작합니다. 이 자습서를 따라 하면 Azure Cosmos DB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 생깁니다.

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

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침을 보려면 [전체 NoSQL 자습서 솔루션 가져오기 섹션](#GetSolution)으로 이동하세요.

이제 시작하겠습니다.

## <a name="prerequisites"></a>필수 조건

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1단계: Azure Cosmos DB 계정 만들기
Azure Cosmos DB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [Visual Studio 솔루션 설치](#SetupVS)로 건너뛸 수 있습니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설치하고 [Visual Studio 솔루션 설치](#SetupVS)로 건너뜁니다.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>2단계: Visual Studio 솔루션 설치
1. 컴퓨터에서 **Visual Studio 2017**을 엽니다.
2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.
3. **새 프로젝트** 대화 상자에서 **템플릿** / **Visual C#** / **콘솔 응용 프로그램**을 선택하고 프로젝트 이름을 지정한 후 **확인**을 클릭합니다.
   ![새 프로젝트 창의 스크린샷](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
4. **솔루션 탐색기**에서 Visual Studio 솔루션 아래에 있는 새 콘솔 응용 프로그램을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...**를 클릭합니다.
    
    ![프로젝트의 마우스 오른쪽 단추 클릭 메뉴의 스크린샷](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. **NuGet** 탭에서 **찾아보기**를 클릭하고 검색 상자에 **azure documentdb**를 입력합니다.
6. 결과 내에서 **Microsoft.Azure.DocumentDB**를 찾아 **설치l**를 클릭합니다.
   Azure Cosmos DB SQL API 클라이언트 라이브러리의 패키지 ID는 [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)입니다.
   ![Azure Cosmos DB 클라이언트 SDK를 찾기 위한 NuGet 메뉴의 스크린샷](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    솔루션 변경 내용을 검토하는 메시지가 표시되면 **확인**을 클릭합니다. 라이선스 승인에 관한 메시지가 표시되면 **동의합니다.**를 클릭합니다.

잘하셨습니다. 설치를 완료했으므로 코드를 작성해 보겠습니다. [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs)에서 이 자습서의 완성된 코드 프로젝트를 찾을 수 있습니다.

## <a id="Connect"></a>3단계: Azure Cosmos DB 계정에 연결
먼저 Program.cs에서 C# 응용 프로그램의 시작 부분에 다음 참조를 추가합니다.

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> 이 자습서를 완료하려면 위의 종속성을 추가했는지 확인합니다.
> 
> 

이제, 두 가지 상수와 *클라이언트* 변수를 공용 클래스 *프로그램* 아래에 추가합니다.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

다음으로 [Azure Portal](https://portal.azure.com)로 다시 이동하여 끝점 URL과 기본 키를 검색합니다. 끝점 URL과 기본 키는 응용 프로그램에서 연결할 위치를 식별하고 Azure Cosmos DB에서 응용 프로그램의 연결을 신뢰하는 데 필요합니다.

Azure Portal에서 Azure Cosmos DB 계정으로 이동한 다음 **키**를 클릭합니다.

포털에서 URI를 복사하고 program.cs 파일의 `<your endpoint URL>` 에 붙여 넣습니다. 그런 다음 포털에서 기본 키를 복사하고 `<your primary key>`에 붙여 넣습니다.

![C# 콘솔 응용 프로그램을 만들기 위해 NoSQL 자습서에서 사용하는 Azure Portal의 스크린샷 액티브 허브, Azure Cosmos DB 계정 페이지의 키 단추 및 키 페이지의 URI, 기본 키 및 보조 키 값이 강조 표시된 Azure Cosmos DB 계정을 보여 줌][keys]

다음으로 **DocumentClient**의 새 인스턴스를 만들어 응용 프로그램을 시작합니다.

**Main** 메서드 아래에 **GetStartedDemo**라는 이름의 새 비동기 작업을 추가하면, 새 **DocumentClient**가 인스턴스화됩니다.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
    }

다음 코드를 추가하여 **Main** 메서드에서 비동기 작업을 실행합니다. **Main** 메서드가 예외를 catch하여 콘솔에 기록합니다.

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

**F5** 키를 눌러 응용 프로그램을 실행합니다. 콘솔 창 출력에서는 연결되었음을 확인하는 `End of demo, press any key to exit.` 메시지를 표시합니다.  그런 다음 콘솔 창을 닫을 수 있습니다. 

축하합니다! Azure Cosmos DB 계정에 성공적으로 연결되었으므로 Azure Cosmos DB 리소스 작업에 대해 살펴보겠습니다.  

## <a name="step-4-create-a-database"></a>4단계: 데이터베이스 만들기
데이터베이스 생성을 위한 코드를 추가하기 전에, 콘솔에 쓰기 위한 도우미 메서드를 추가합니다.

**WriteToConsoleAndPromptToContinue** 메서드를 복사하여 **GetStartedDemo** 메서드 다음에 붙여넣습니다.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

**DocumentClient** 클래스의 [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) 메서드를 사용하여 Azure Cosmos DB [데이터베이스](sql-api-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다.

클라이언트를 만든 후 다음 코드를 복사하여 **GetStartedDemo** 메서드에 붙여넣습니다. *FamilyDB*라는 데이터베이스가 생성됩니다.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

**F5** 키를 눌러 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스를 성공적으로 만들었습니다.  

## <a id="CreateColl"></a>5단계: 컬렉션 만들기
> [!WARNING]
> **CreateDocumentCollectionIfNotExistsAsync**는 가격 책정 의미가 포함된 예약된 처리량이 있는 새 컬렉션을 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
> 
> 

**DocumentClient** 클래스의 [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) 메서드를 사용하여 [컬렉션](sql-api-resources.md#collections)을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다.

데이터베이스를 만든 후 다음 코드를 복사하여 **GetStartedDemo** 메서드에 붙여넣습니다. *FamilyCollection*이라는 문서 컬렉션이 생성됩니다.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

**F5** 키를 눌러 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스 컬렉션을 성공적으로 만들었습니다.  

## <a id="CreateDoc"></a>6단계: JSON 문서 만들기
**DocumentClient** 클래스의 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 메서드를 사용하여 [문서](sql-api-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 하나 이상의 문서를 삽입할 수 있습니다. 데이터베이스에 저장하려는 데이터가 이미 있는 경우 Azure Cosmos DB의 [데이터 마이그레이션 도구](import-data.md)를 사용하여 데이터를 데이터베이스로 가져올 수 있습니다.

먼저 이 샘플에서는 Azure Cosmos DB 내에 저장된 개체를 나타내는 **가족** 클래스를 만들어야 합니다. 또한 **가족** 내에서 사용되는 **부모**, **자식**, **애완 동물**, **주소** 하위 클래스를 만듭니다. 문서에는 JSON에서 **ID**로 직렬화된 **ID** 속성이 있어야 합니다. **GetStartedDemo** 메서드 다음에 다음 내부 하위 클래스를 추가하여 이러한 클래스를 만듭니다.

**가족**, **부모**, **자식**, **애완 동물** 및 **주소** 클래스를 복사하여 **WriteToConsoleAndPromptToContinue** 메서드 다음에 붙여넣습니다.

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

**Address** 클래스 아래에 **CreateFamilyDocumentIfNotExists** 메서드를 복사하여 붙여넣습니다.

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

그리고 Andersen Family와 Wakefield Family의 문서 하나씩 두 개의 문서를 삽입합니다.

문서 컬렉션을 만든 후 다음 코드를 복사하여 **GetStartedDemo** 메서드에 붙여넣습니다.

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
    
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });


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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

**F5** 키를 눌러 응용 프로그램을 실행합니다.

축하합니다! 두 개의 Azure Cosmos DB 문서를 성공적으로 만들었습니다.  

![NoSQL에서 C# 콘솔 응용 프로그램을 만들기 위해 사용한 계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7단계: Azure Cosmos DB 리소스 쿼리
Azure Cosmos DB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](sql-api-sql-query.md)를 지원합니다.  다음 샘플 코드는 Azure Cosmos DB SQL 구문뿐 아니라 LINQ를 사용하는 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 삽입한 문서에 대해 실행할 수 있습니다.

**ExecuteSimpleQuery** 메서드를 복사하여 **CreateFamilyDocumentIfNotExists** 메서드 다음에 붙여넣습니다.

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

두 번째 문서를 만든 후 다음 코드를 복사하여 **GetStartedDemo** 메서드에 붙여넣습니다.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

**F5** 키를 눌러 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 컬렉션에 대한 쿼리가 성공적으로 수행되었습니다.

다음 다이어그램에서는 만든 컬렉션에 대해 Azure Cosmos DB SQL 쿼리 구문을 호출하는 방법을 보여 주며, 마찬가지로 동일한 논리가 LINQ 쿼리에 적용됩니다.

![NoSQL에서 C# 콘솔 응용 프로그램을 만들기 위해 사용한 쿼리의 의미와 범위를 보여 주는 다이어그램](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Azure Cosmos DB 쿼리는 이미 단일 컬렉션으로 범위가 지정되었기 때문에 [FROM](sql-api-sql-query.md#FromClause) 키워드는 쿼리에서 선택 사항입니다. 따라서 "FROM Families f"를 "FROM root r" 또는 선택한 다른 변수 이름으로 교체할 수 있습니다. Azure Cosmos DB는 패밀리, 루트 또는 선택한 변수 이름이 기본적으로 현재 컬렉션을 참조하는 것으로 유추합니다.

## <a id="ReplaceDocument"></a>8단계: JSON 문서 바꾸기
Azure Cosmos DB는 JSON 문서 바꾸기를 지원합니다.  

**ReplaceFamilyDocument** 메서드를 복사하여 **ExecuteSimpleQuery** 메서드 다음에 붙여넣습니다.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
         await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
         this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }

메서드 끝에서 쿼리를 실행한 후 다음 코드를 복사하여 **GetStartedDemo** 메서드에 붙여넣습니다. 문서를 바꾼 후에, 변경된 문서를 표시하도록 같은 쿼리가 다시 실행됩니다.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

**F5** 키를 눌러 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 문서를 성공적으로 대체했습니다.

## <a id="DeleteDocument"></a>9단계: JSON 문서 삭제
Azure Cosmos DB는 JSON 문서 삭제를 지원합니다.  

**DeleteFamilyDocument** 메서드를 복사하여 **ReplaceFamilyDocument** 메서드 다음에 붙여넣습니다.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
         await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
         Console.WriteLine("Deleted Family {0}", documentName);
    }

메서드 끝에서 두 번째 쿼리를 실행한 후 다음 코드를 복사하여 **GetStartedDemo** 메서드에 붙여넣습니다.

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);
    
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
    
    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

**F5** 키를 눌러 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 문서를 성공적으로 삭제했습니다.

## <a id="DeleteDatabase"></a>10단계: 데이터베이스 삭제
만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다.

문서를 삭제한 후 다음 코드를 복사하여 **GetStartedDemo** 메서드에 붙여넣어 전체 데이터베이스 및 모든 자식 리소스를 삭제합니다.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

**F5** 키를 눌러 응용 프로그램을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스를 성공적으로 삭제했습니다.

## <a id="Run"></a>11단계: C# 콘솔 응용 프로그램 모두 함께 실행
디버그 모드에서 응용 프로그램을 빌드하려면 Visual Studio에서 F5 키를 누릅니다.

시작한 앱의 출력이 콘솔 창에 표시됩니다. 출력은 추가한 쿼리 결과를 보여 주며, 아래 예제 텍스트와 일치해야 합니다.

    Created FamilyDB
    Press any key to continue ...
    Created FamilyCollection
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

축하합니다! 이 자습서를 완료했으며 실행되는 C# 콘솔 응용 프로그램이 생겼습니다.

## <a id="GetSolution"></a> 전체 자습서 솔루션 가져오기
이 자습서의 단계를 완료할 시간이 없거나 코드 샘플만 다운로드하려는 경우 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started)에서 가져올 수 있습니다. 

GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* [Azure Cosmos DB 계정][cosmos-db-create-account].
* GitHub에서 제공하는 [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) 솔루션

Visual Studio에서 Azure Cosmos DB .NET SDK에 대한 참조를 복원하려면 솔루션 탐색기에서 **GetStarted** 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 복원 사용**을 클릭합니다. 다음으로, App.config 파일에서 EndpointUrl 및 AuthorizationKey 값을 [Azure Cosmos DB 계정에 연결](#Connect)에 설명된 대로 업데이트합니다.

정말 간단하죠? 빌드하고 원하는 대로 진행하세요!


## <a name="next-steps"></a>다음 단계
* 보다 복잡한 ASP.NET MVC 자습서가 필요하신가요? [ASP.NET MVC 자습서: Azure Cosmos DB를 사용한 웹 응용 프로그램 개발](sql-api-dotnet-application.md)을 참조하세요.
* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행하고 싶으신가요? [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)를 참조하세요.
* [Azure Cosmos DB 요청, 사용 및 저장소 모니터링](monitor-accounts.md) 방법에 대해 알아보세요.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 시작](https://docs.microsoft.com/azure/cosmos-db/introduction)을 참조하세요.

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
[cosmos-db-create-account]: create-sql-api-dotnet.md#create-account
