---
title: Azure Table Storage 및 Visual Studio 연결 서비스 시작(ASP.NET) | Microsoft Docs
description: Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio ASP.NET 프로젝트에서 Azure Table Storage 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 39456380769e1c3b790d2bbc6fdf9c04c983d054
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Azure Table Storage 및 Visual Studio 연결 서비스 시작
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>개요

Azure 테이블 저장소를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다.

이 자습서에서는 Azure Table Storage 항목을 사용하여 몇 가지 일반적인 시나리오에 대한 ASP.NET 코드를 작성하는 방법을 보여 줍니다. 이러한 시나리오는 테이블 만들기 및 테이블 엔터티 추가, 쿼리 및 삭제를 포함합니다. 

## <a name="prerequisites"></a>필수 조건

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure 저장소 계정](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC 컨트롤러 만들기 

1. **솔루션 탐색기**에서 **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->컨트롤러**를 차례로 선택합니다.

    ![ASP.NET MVC 앱에 컨트롤러 추가](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. **스캐폴드 추가** 대화 상자에서 **MVC 5 컨트롤러 - 비어 있음**을 선택하고 **추가**를 선택합니다.

    ![MVC 컨트롤러 유형 지정](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. **컨트롤러 추가** 대화 상자에서 컨트롤러 이름을*TablesController*로 설정하고 **추가**를 선택합니다.

    ![MVC 컨트롤러 이름 지정](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. 다음 *using* 지시문을 `TablesController.cs` 파일에 추가합니다.

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>모델 클래스 만들기

이 문서에서 대부분의 예제는 **CustomerEntity**라는 **TableEntity** 파생 클래스를 사용합니다. 다음 단계는 모델 클래스로 이 클래스를 선언하는 과정을 안내합니다.

1. **솔루션 탐색기**에서 **모델**을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->클래스**를 차례로 선택합니다.

1. **새 항목 추가** 대화 상자에서 클래스의 이름을 **CustomerEntity**로 지정합니다.

1. `CustomerEntity.cs` 파일을 열고 다음 **using** 지시문을 추가합니다.

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. 완료되면 클래스가 다음 코드와 같이 선언되도록 클래스를 해당 내용으로 수정합니다. 클래스는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 **CustomerEntity**라는 엔터티 클래스를 선언합니다.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>테이블 만들기

다음 단계에서는 테이블을 만드는 방법을 보여 줍니다.

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment)의 단계를 완료했다고 가정합니다. 

1. `TablesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **CreateTable**이라는 메서드를 추가합니다.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **CreateTable** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. 다음 코드를 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져옵니다(*&lt;storage-account-name>* 을 액세스하는 Azure 저장소 계정의 이름으로 변경).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 원하는 테이블 이름에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. **CloudTableClient.GetTableReference** 메서드는 테이블 저장소에 대한 요청을 만들지 않습니다. 테이블이 있는지 여부에 관계없이 참조가 반환됩니다. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 테이블이 아직 없으면 **CloudTable.CreateIfNotExists** 메서드를 호출하여 만듭니다. 테이블이 없는 경우 테이블이 성공적으로 생성되면 **CloudTable.CreateIfNotExists** 메서드는 **true**를 반환합니다. 그렇지 않으면 **false**가 반환됩니다.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. **ViewBag**을 테이블의 이름으로 업데이트합니다.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Tables**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **CreateTable**을 입력하고 **추가**를 선택합니다.

1. `CreateTable.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. 응용 프로그램을 실행하고 **테이블 만들기**를 선택하여 다음 스크린샷과 유사한 결과를 확인합니다.
  
    ![테이블 만들기](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    앞에서 언급했듯이 테이블이 없고 만들어진 경우에만 **CloudTable.CreateIfNotExists** 메서드에서 **true**를 반환합니다. 따라서 테이블이 있을 때 앱을 실행하면 메서드에서 **false**를 반환합니다. 앱을 여러 번 실행하려면 앱을 다시 실행하기 전에 테이블을 삭제해야 합니다. **CloudTable.Delete** 메서드를 통해 테이블 삭제를 수행할 수 있습니다. 또한 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) 또는 [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 테이블을 삭제할 수도 있습니다.  

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

*엔터티*는 **TableEntity**에서 파생된 사용자 지정 클래스를 사용하여 C\# 개체에 매핑됩니다. 테이블에 엔터티를 추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다. 이 섹션에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 엔터티 클래스를 정의하는 방법을 알아보겠습니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있지만 다양한 파티션 키를 사용하면 병렬 작업 확장성이 커집니다. 테이블 서비스에 저장되어야 하는 속성의 경우 속성은 설정과 검색 값을 모두 표시하는 지원되는 형식의 공용 속성이어야 합니다.
엔터티 클래스는 공용 매개 변수가 없는 생성자를 선언*해야* 합니다.

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment)의 단계를 완료했다고 가정합니다.

1. `TablesController.cs` 파일을 엽니다.

1. `TablesController.cs` 파일의 코드가 **CustomerEntity** 클래스에 액세스할 수 있도록 다음 지시문을 추가합니다.

    ```csharp
    using StorageAspnet.Models;
    ```

1. **ActionResult**를 반환하는 **AddEntity**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **AddEntity** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. 다음 코드를 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져옵니다(*&lt;storage-account-name>* 을 액세스하는 Azure 저장소 계정의 이름으로 변경).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 새 엔터티를 추가하려는 테이블에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **CustomerEntity** 클래스를 인스턴스화 및 초기화합니다.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. customer 엔터티를 삽입하는 **TableOperation** 개체를 만듭니다.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. **CloudTable.Execute** 메서드를 호출하여 삽입 작업을 실행합니다. **TableResult.HttpStatusCode** 속성을 검사하여 작업의 결과를 확인할 수 있습니다. 상태 코드 2xx는 클라이언트에서 요청한 작업이 성공적으로 처리되었음을 나타냅니다. 예를 들어 새 엔터티를 성공적으로 삽입하면 작업이 성공적으로 처리되었고 서버가 어떤 콘텐츠도 반환하지 않았음을 의미하는 HTTP 상태 코드 204가 표시됩니다.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. **ViewBag**을 테이블 이름으로 업데이트하고 삽입 작업의 결과를 업데이트합니다.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Tables**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **AddEntity**를 입력하고 **추가**를 선택합니다.

1. `AddEntity.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. 응용 프로그램을 실행하고 **엔터티 추가**를 선택하여 다음 스크린샷과 유사한 결과를 확인합니다.
  
    ![엔터티 추가](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    [단일 엔터티 가져오기](#get-a-single-entity) 섹션의 단계를 수행하여 엔터티가 추가되었는지 확인할 수 있습니다. [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 테이블에 대한 모든 엔터티를 볼 수도 있습니다.

## <a name="add-a-batch-of-entities-to-a-table"></a>테이블에 엔터티를 일괄로 추가

[한 번에 하나의 테이블에 엔터티를 추가](#add-an-entity-to-a-table)할 수 있을 뿐 아니라 엔터티를 일괄로 추가할 수도 있습니다. 엔터티를 일괄로 추가하면 코드와 Azure Table service 간의 왕복 수가 줄어듭니다. 다음 단계에서는 단일 삽입 작업으로 여러 엔터티를 테이블에 추가하는 방법을 보여 줍니다.

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment)의 단계를 완료했다고 가정합니다.

1. `TablesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **AddEntities**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **AddEntities** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. 다음 코드를 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져옵니다(*&lt;storage-account-name>* 을 액세스하는 Azure 저장소 계정의 이름으로 변경).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 새 엔터티를 추가하려는 테이블에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. [테이블에 엔터티 추가](#add-an-entity-to-a-table) 섹션에 표시되는 **CustomerEntity** 모델 클래스에 따라 일부 고객 개체를 인스턴스화합니다.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. **TableBatchOperation** 개체를 가져옵니다.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. 일괄 삽입 작업 개체에 엔터티를 추가합니다.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. **CloudTable.ExecuteBatch** 메서드를 호출하여 일괄 삽입 작업을 실행합니다.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** 메서드는 **TableResult** 개체 목록을 반환합니다. 여기에서 각 개별 작업의 성공 여부를 확인하도록 각 **TableResult** 개체를 검사할 수 있습니다. 이 예에서는 보기로 목록을 전달하고 보기에 각 작업의 결과를 표시하도록 합니다. 
 
    ```csharp
    return View(results);
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Tables**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **AddEntities**를 입력하고 **추가**를 선택합니다.

1. `AddEntities.cshtml`을 열고 다음과 같이 수정합니다.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. 응용 프로그램을 실행하고 **엔터티 추가**를 선택하여 다음 스크린샷과 유사한 결과를 확인합니다.
  
    ![엔터티 추가](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    [단일 엔터티 가져오기](#get-a-single-entity) 섹션의 단계를 수행하여 엔터티가 추가되었는지 확인할 수 있습니다. [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 테이블에 대한 모든 엔터티를 볼 수도 있습니다.

## <a name="get-a-single-entity"></a>단일 엔터티 가져오기

이 섹션에서는 엔터티의 행 키 및 파티션 키를 사용하여 테이블에서 단일 엔터티를 가져오는 방법을 보여 줍니다. 

> [!NOTE]
> 
> 이 섹션은 [개발 환경 설정](#set-up-the-development-environment)의 단계를 완료했다고 가정하고 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table)의 데이터를 사용합니다. 

1. `TablesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **GetSingle**이라는 메서드를 추가합니다.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **GetSingle** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. 다음 코드를 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져옵니다(*&lt;storage-account-name>* 을 액세스하는 Azure 저장소 계정의 이름으로 변경).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 엔터티를 검색하려는 테이블에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **TableEntity**에서 파생된 엔터티 개체를 사용하는 검색 작업 개체를 만듭니다. 첫 번째 매개 변수는 *partitionKey*이고, 두 번째 매개 변수는 *rowKey*입니다. 다음 코드 조각은 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 사용하여 *partitionKey* 값 "Smith" 및 *rowKey* 값 "Ben"을 사용하여 테이블에서 **CustomerEntity** 엔터티를 쿼리합니다.

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. 검색 작업을 실행합니다.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. 표시를 위해 결과를 보기에 전달합니다.

    ```csharp
    return View(result);
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Tables**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **GetSingle**을 입력하고 **추가**를 선택합니다.

1. `GetSingle.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. 응용 프로그램을 실행하고 **단일 가져오기**를 선택하여 다음 스크린샷과 유사한 결과를 확인합니다.
  
    ![단일 가져오기](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>파티션의 모든 엔터티 가져오기

[테이블에 엔터티 추가](#add-an-entity-to-a-table) 섹션에서 설명했듯이 파티션 및 행 키의 조합은 테이블의 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있습니다. 이 섹션은 지정된 파티션에서 모든 엔터티에 대한 테이블을 쿼리하는 방법을 보여 줍니다.  

> [!NOTE]
> 
> 이 섹션은 [개발 환경 설정](#set-up-the-development-environment)의 단계를 완료했다고 가정하고 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table)의 데이터를 사용합니다. 

1. `TablesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **GetPartition**이라는 메서드를 추가합니다.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **GetPartition** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. 다음 코드를 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져옵니다(*&lt;storage-account-name>* 을 액세스하는 Azure 저장소 계정의 이름으로 변경).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 엔터티를 검색하려는 테이블에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **TableQuery** 개체를 인스턴스화하고 **Where** 절에 쿼리를 지정합니다. 다음 코드 조각은 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 사용하여 **PartitionKey**(고객의 성) 값이 "Smith"인 모든 엔터티를 테이블에서 쿼리합니다.

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. 루프 내에서 **CloudTable.ExecuteQuerySegmented** 메서드를 호출하고 이전 단계에서 인스턴스화한 쿼리 개체를 전달합니다.  **CloudTable.ExecuteQuerySegmented** 메서드는 **TableContinuationToken** 개체를 반환합니다. 이 개체는 **null**인 경우 검색할 추가 엔터티가 없음을 나타냅니다. 루프 내에서 다른 루프를 사용하여 반환된 엔터티를 반복합니다. 다음 코드 예제에서는 반환된 각 엔터티가 목록에 추가됩니다. 루프가 종료되면 표시를 위해 목록이 보기에 전달됩니다. 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Tables**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **GetPartition**을 입력하고 **추가**를 선택합니다.

1. `GetPartition.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. 응용 프로그램을 실행하고 **파티션 가져오기**를 선택하여 다음 스크린샷과 유사한 결과를 확인합니다.
  
    ![파티션 가져오기](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>엔터티 삭제

이 섹션에서는 테이블에서 엔터티를 삭제하는 방법을 보여 줍니다.

> [!NOTE]
> 
> 이 섹션은 [개발 환경 설정](#set-up-the-development-environment)의 단계를 완료했다고 가정하고 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table)의 데이터를 사용합니다. 

1. `TablesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **DeleteEntity**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **DeleteEntity** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. 다음 코드를 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져옵니다(*&lt;storage-account-name>* 을 액세스하는 Azure 저장소 계정의 이름으로 변경).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 엔터티를 삭제하려는 테이블에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **TableEntity**에서 파생된 엔터티 개체를 사용하는 삭제 작업 개체를 만듭니다. 이 경우 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 사용합니다. 엔터티의 **ETag**는 유효한 값으로 설정되어야 합니다.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. 삭제 작업을 실행합니다.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. 표시를 위해 결과를 보기에 전달합니다.

    ```csharp
    return View(result);
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Tables**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **DeleteEntity**를 입력하고 **추가**를 선택합니다.

1. `DeleteEntity.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. 응용 프로그램을 실행하고 **엔터티 삭제**를 선택하여 다음 스크린샷과 유사한 결과를 확인합니다.
  
    ![단일 가져오기](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>다음 단계
Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.

  * [Azure Blob 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Azure 큐 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
