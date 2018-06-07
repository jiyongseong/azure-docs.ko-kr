---
title: Azure Search에서 Azure SQL Databases를 인덱싱하는 방법에 대한 자습서 | Microsoft Docs
description: 검색 가능한 데이터를 추출하고 Azure Search 인덱스에 입력하기 위해 Azure SQL Databases를 탐색합니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: abf121ec369d84dd307416d2c08971d9096de4a8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Azure Search 인덱서를 사용하여 Azure SQL Databases를 탐색하는 방법

이 자습서에서는 샘플 Azure SQL Database에서 검색할 수 있는 데이터를 추출하기 위해 인덱서를 구성하는 방법을 보여줍니다. [인덱서](search-indexer-overview.md)는 외부 데이터 원본을 탐색하는 Azure Search의 구성 요소이며 콘텐츠로 [검색 인덱스](search-what-is-an-index.md)를 채웁니다. 모든 인덱서 중에 Azure SQL Database의 인덱서가 가장 널리 사용됩니다. 

인덱서 구성의 숙련도에 따라 작성하고 유지 관리해야 하는 코드를 단순화할 수 있습니다. 스키마 준수 JSON 데이터 집합을 준비하고 푸시하는 대신 인덱서를 데이터 원본에 연결하고, 인덱서로 데이터를 추출하고, 인덱스에 삽입하고, 필요에 따라 되풀이 일정으로 인덱서를 실행하여 기본 원본에서 변경 내용을 적용할 수 있습니다.

이 자습서에서는 [Azure Search.NET 클라이언트 라이브러리](https://aka.ms/search-sdk) 및.NET Core 콘솔 응용 프로그램을 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 솔루션 다운로드 및 구성
> * 응용 프로그램 설정에 검색 서비스 정보 추가
> * Azure SQL Database에서 외부 데이터 집합 준비 
> * 샘플 코드에서 인덱스 및 인덱서 정의 검토
> * 데이터를 가져오는 인덱서 코드 실행
> * 인덱스 검색
> * 포털에서 인덱서 구성 보기

## <a name="prerequisites"></a>필수 조건

* 활성 Azure 계정. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 

* Azure Search 서비스. 설정하는 도움말은 [검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

* 인덱서 사용되는 외부 데이터 원본을 제공하는 Azure SQL Database입니다. 샘플 솔루션은 SQL 데이터 파일을 제공하여 테이블을 만듭니다.

* Visual Studio 2017. 체험 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 사용할 수 있습니다. 

> [!Note]
> 체험 Azure Search 서비스를 사용하는 경우 세 가지 인덱스, 세 가지 인덱서 및 세 가지 데이터 원본으로 제한됩니다. 이 자습서에서는 각각을 하나씩 만듭니다. 서비스에 새 리소스를 허용하는 공간이 있는지 확인합니다.

## <a name="download-the-solution"></a>솔루션 다운로드

이 자습서에 사용되는 인덱서 솔루션은 하나의 마스터 다운로드에 포함된 Azure Search 샘플의 컬렉션입니다. 이 자습서에 사용되는 솔루션은 *DotNetHowToIndexers*입니다.

1. Azure 샘플 GitHub 리포지토리에서 [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started)로 이동합니다.

2. **복제 또는 다운로드** > **ZIP 다운로드**를 클릭합니다. 기본적으로 파일은 Downloads 폴더에 저장됩니다.

3. **파일 탐색기** > **다운로드**에서 파일을 마우스 오른쪽 단추로 클릭하고 **전체 압축 풀기**를 선택합니다.

4. 읽기 전용 권한을 해제합니다. 폴더 이름 > **속성** > **일반**을 마우스 오른쪽 단추로 클릭하고, 현재 폴더, 하위 폴더 및 파일에서 **읽기 전용** 특성의 선택을 취소합니다.

5. **Visual Studio 2017**에서 *DotNetHowToIndexers.sln* 솔루션을 엽니다.

6. **솔루션 탐색기**에서 최상위 노드 부모 솔루션 > **Nuget 패키지 복원**을 마우스 오른쪽 단추로 클릭합니다.

## <a name="set-up-connections"></a>연결 설정
필요한 서비스에 대한 연결 정보는 솔루션의 **appsettings.json** 파일에 지정됩니다. 

이 자습서의 지침을 사용하여 각 설정을 채울 수 있도록 솔루션 탐색기에서 **appsettings.json**을 엽니다.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>검색 서비스 이름 및 관리 api-key 가져오기

포털에서 검색 서비스 끝점 및 키를 찾을 수 있습니다. 키는 서비스 작업에 대한 액세스 권한을 제공합니다. 관리자 키를 사용하면 서비스에서 인덱서 및 인덱스와 같은 개체를 만들고 삭제하는 데 필요한 쓰기 액세스를 허용합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 [구독에 대한 검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 찾습니다.

2. 서비스 페이지를 엽니다.

3. 기본 페이지의 맨 위에서 서비스 이름을 찾습니다. 다음 스크린샷에서 *azs-tutorial*입니다.

   ![서비스 이름](./media/search-indexer-tutorial/service-name.png)

4. 해당 항목을 복사하고 Visual Studio에서 **appsettings.json**에 첫 번째 항목으로 붙여넣습니다.

  > [!Note]
  > 서비스 이름은 search.windows.net을 포함하는 끝점의 일부입니다. 자세한 내용을 보려면 개요 페이지의 **Essentials**에서 전체 URL을 확인할 수 있습니다. URL은 https://your-service-name.search.windows.net과 비슷합니다.

5. 왼쪽의 **설정** > **키**에서 관리자 키 중 하나를 복사하고 i**appsettings.json**에 두 번째 항목으로 붙여넣습니다. 키는 프로비전하는 동안 서비스에 대해 생성되는 영숫자 문자열이며 서비스 작업에 대한 권한 있는 액세스에 필요합니다. 

  설정을 모두 추가한 후에 파일은 다음 예제와 비슷합니다.

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>외부 데이터 원본 준비

이 단계에서 인덱서가 탐색할 수 있는 외부 데이터 원본을 만듭니다. 이 자습서의 데이터 파일은 *hotels.sql*이며 \DotNetHowToIndexers 솔루션 폴더에 제공됩니다. 

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Portal 및 샘플의 *hotels.sql* 파일을 사용하여 Azure SQL Database에서 데이터 집합을 만들 수 있습니다. Azure Search는 뷰 또는 쿼리에서 생성된 일반 행 집합을 사용합니다. 샘플 솔루션에 있는 SQL 파일은 단일 테이블을 만들고 채웁니다.

다음 연습은 기존 서버 또는 데이터베이스를 사용하지 않고 2단계에서 모두 만들도록 지시합니다. 필요에 따라 기존 리소스가 있는 경우 4단계에서부터 여기에 호텔 테이블을 추가할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. 

2. **리소스 만들기** > **SQL Database**를 클릭하여 데이터베이스, 서버 및 리소스 그룹을 만듭니다. 기본값 및 가장 낮은 수준의 가격 책정 계층을 사용할 수 있습니다. 서버를 만드는 이점은 이후 단계에서 테이블을 만들고 로드하는 데 필요한 관리자 사용자 이름 및 암호를 지정할 수 있다는 것입니다.

   ![새 데이터베이스 페이지](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. **만들기**를 클릭하여 새 서버 및 데이터베이스를 배포합니다. 서버 및 데이터베이스를 배포하도록 대기합니다.

4. 아직 새 데이터베이스에 대한 SQL Database 페이지를 열지 않은 경우 엽니다. 리소스 이름은 *SQL Server*가 아닌 *SQL Database*여야 합니다.

  ![SQL Database 페이지](./media/search-indexer-tutorial/hotels-db.png)

4. 명령 모음에서 **도구** > **쿼리 편집기**를 클릭합니다.

5. **로그인**을 클릭하고 사용자 이름 및 서버 관리자의 암호를 입력합니다.

6. **쿼리 열기**를 클릭하고 *hotels.sql*의 위치로 이동합니다. 

7. 파일을 선택하고 **열기**를 클릭합니다. 이 스크립트는 다음 스크린샷과 비슷하게 표시됩니다.

  ![SQL 스크립트](./media/search-indexer-tutorial/sql-script.png)

8. **실행**을 클릭하여 쿼리를 실행합니다. 결과 창에서 3행에 대한 쿼리 성공 메시지가 표시됩니다.

9. 이 테이블에서 행 집합을 반환하려면 확인 단계로 다음 쿼리를 실행할 수 있습니다.

   ```sql
   SELECT HotelId, HotelName, Tags FROM Hotels
   ```
   정형화된 쿼리인 `SELECT * FROM Hotels`는 쿼리 편집기에서 작동하지 않습니다. 샘플 데이터는 위치 필드에 지리 좌표를 포함합니다. 이 시점에는 편집기에서 처리되지 않습니다. 쿼리할 다른 열의 목록은 다음 문을 실행할 수 있습니다.`SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. 이제 외부 데이터 집합이 있으므로 데이터베이스에 대한 ADO.NET 연결 문자열을 복사합니다. 데이터베이스의 SQL Database 페이지에서 **설정** > **연결 문자열**로 이동하고, ADO.NET 연결 문자열을 복사합니다.
 
  ADO.NET 연결 문자열은 다음 예제와 비슷하며 유효한 데이터베이스 이름, 사용자 이름 및 암호를 사용하도록 수정됩니다.

  ```sql
  Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  ```
11. Visual Studio에 있는 **appsettings.json** 파일에서 세 번째 항목으로 "AzureSqlConnectionString"에 연결 문자열을 붙여넣습니다.

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>인덱스 및 인덱서 코드 이해

이제 코드를 빌드하고 실행할 준비가 되었습니다. 그렇게 하기 전에 이 샘플에 대한 인덱스 및 인덱서 정의를 알아보는 데 1분이 걸립니다. 관련된 코드가 다음과 같은 두 개의 파일에 있습니다.

  + **hotel.cs**는 인덱스를 정의하는 스키마가 포함됩니다.
  + **Program.cs**는 서비스에서 구조를 만들고 관리하는 함수가 포함됩니다.

### <a name="in-hotelcs"></a>hotel.cs에서

인덱스 스키마는 다음과 같은 HotelName의 필드 정의에 표시된 대로 필드가 전체 텍스트 검색 가능, 필터링 가능, 정렬 가능한지 등 허용되는 작업을 지정하는 속성을 비롯한 필드 컬렉션을 정의합니다. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

스키마는 점수 매기기, 사용자 지정 분석기 및 다른 구문을 향상시키기 위해 점수 매기기 프로필을 비롯한 다른 요소를 포함할 수도 있습니다. 그러나 목적상 스키마는 밀도가 낮게 정의되어 샘플 데이터 집합에서 발견되는 필드를 구성합니다.

이 자습서에서 인덱서는 하나의 데이터 원본에서 데이터를 끌어옵니다. 실제로 여러 인덱서를 동일한 인덱스에 연결하여 여러 데이터 원본 및 인덱서에서 통합된 검색 가능한 인덱스를 만들 수 있습니다. 동일한 인덱스 인덱서 쌍을 사용하여 유연성이 필요한 경우에 다양한 인덱서 및 데이터 원본 조합으로 데이터 원본 또는 하나의 인덱스가 달라질 수 있습니다.

### <a name="in-programcs"></a>Program.cs에서

주 프로그램에는 세 개의 모든 대표 데이터 원본에 대한 함수가 포함됩니다. Azure SQL Database에 중점을 두면 다음 개체가 강조됩니다.

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

Azure Search에서 독립적으로 보거나, 구성하거나, 삭제할 수 있는 개체에는 인덱스, 인덱서 및 데이터 원본이 포함됩니다(*hotels*, *azure-sql-indexer*, *azure-sql* 각각). 

*AzureSqlHighWaterMarkColumnName* 열은 변경 감지 정보를 제공하기 때문에 특별히 설명해야 합니다. 인덱서에서 마지막 인덱싱 작업 이후에 행이 변경되었는지를 결정하는 데 해당 열을 사용합니다. [변경 감지 정책](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)은 인덱서에서만 지원되고 데이터 원본에 따라 달라집니다. Azure SQL Database의 경우 데이터베이스 요구 사항에 따라 두 가지 정책 중에 선택할 수 있습니다.

다음 코드는 데이터 원본 및 인덱서를 만드는 데 사용된 Program.cs의 메서드를 보여줍니다. 코드는 이 프로그램을 여러 번 실행한다는 가정 하에서 동일한 이름의 기존 리소스를 확인하고 삭제합니다.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

인덱서 API 호출은 [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet)을 제외하고 플랫폼과 독립적입니다. 여기서 호출할 크롤러의 형식을 지정합니다.

## <a name="run-the-indexer"></a>인덱서 실행

이 단계에서는 프로그램을 컴파일하고 실행합니다. 

1. 솔루션 탐색기에서 **DotNetHowToIndexers**를 마우스 오른쪽 단추로 클릭하고 **빌드**를 선택합니다.
2. 다시 **DotNetHowToIndexers** 및 **디버그** > **새 인스턴스 시작**을 마우스 오른쪽 단추로 클릭합니다.

디버그 모드로 프로그램을 실행합니다. 콘솔 창에서는 각 작업의 상태를 보고합니다.

  ![SQL 스크립트](./media/search-indexer-tutorial/console-output.png)

코드는 Visual Studio에서 로컬로 실행되어 Azure의 검색 서비스에 연결합니다. 그러면 연결 문자열을 사용하여 Azure SQL Database에 연결하고 데이터 집합을 검색합니다. 이 많은 작업에서 잠재적인 실패 지점이 있지만 오류가 발생하는 경우 다음 상태를 먼저 확인하세요.

+ 제공한 검색 서비스 연결 정보가 이 자습서에서 해당 서비스 이름으로 제한됩니다. 전체 URL을 입력한 경우 인덱스 생성 시 연결 오류와 함께 작업이 중지됩니다.

+ **appsettings.json**에 있는 데이터베이스 연결 정보입니다. 해당 항목은 포털에서 가져온 ADO.NET 연결 문자열이며 데이터베이스에 유효한 사용자 이름 및 암호를 포함하도록 수정되어야 합니다. 사용자 계정에는 데이터를 검색할 수 있는 권한이 있어야 합니다.

+ 리소스 제한 공유 (체험) 서비스에 3개의 인덱스, 인덱서 및 데이터 원본이라는 제한이 있습니다. 최대 제한 시 서비스는 새 개체를 만들 수 없습니다.

## <a name="search-the-index"></a>인덱스 검색 

Azure Portal의 검색 서비스 개요 페이지에서 맨 위에 있는 **검색 탐색기**를 클릭하고 새 인덱스에 몇 가지 쿼리를 제출합니다.

1. 맨 위에서 **변경 인덱스**를 클릭하고 *hotels* 인덱스를 선택합니다.

2. **검색** 단추를 클릭하여 빈 검색을 발급합니다. 

  인덱스에 있는 세 개의 항목이 JSON 문서로 반환됩니다. 전체 구조를 볼 수 있도록 검색 탐색기는 JSON으로 문서를 반환합니다.

3. 다음으로 검색 문자열을 입력합니다.`search=river&$count=true` 

  이 쿼리는 `river` 용어의 전체 텍스트 검색을 호출하고 결과에는 일치하는 문서 수가 포함됩니다. 일치하는 문서 수를 반환하는 작업은 수많은 문서를 포함하는 대규모 인덱스가 있을 때 시나리오를 테스트하는 경우 유용합니다. 이 경우에 하나의 문서만이 쿼리와 일치합니다.

4. 마지막으로 JSON 출력을 관심 분야로 제한하는 검색 문자열을 입력합니다.`search=river&$count=true&$select=hotelId, baseRate, description` 

  쿼리 응답이 선택한 필드로 줄어들어 더 간결한 출력이 표시됩니다.

## <a name="view-indexer-configuration"></a>인덱서 구성 보기

프로그래밍 방식으로 방금 만든 인덱서를 비롯한 모든 인덱서가 포털에 나열됩니다. 인덱서 정의를 열거나, 해당 데이터 원본을 보거나, 새로운 기능과 변경된 행을 선택하도록 새로 고침 일정을 구성할 수 있습니다.

1. Azure Search 서비스의 서비스 개요 페이지를 엽니다.
2. 아래로 스크롤하여 **인덱서** 및 **데이터 원본**에 대한 타일을 찾습니다.
3. 타일을 클릭하여 각 리소스의 목록을 엽니다. 개별 인덱서 또는 데이터 원본을 선택하여 구성 설정을 보거나 수정할 수 있습니다.

  ![인덱서 및 데이터 원본 타일](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>리소스 정리

이러한 서비스를 계속 사용하지 않으려면 Azure Portal에서 이러한 단계에 따라 이 자습서에서 만든 리소스를 모두 삭제합니다. 

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **리소스 그룹 삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용 및 기타 지원되는 데이터 원본에 관련된 작업은 다음 문서를 참조하세요.

* [Azure SQL Database 또는 Azure 가상 머신의 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
* [Azure Search Blob 인덱서를 사용하여 JSON Blob 인덱싱](search-howto-index-json-blobs.md)

