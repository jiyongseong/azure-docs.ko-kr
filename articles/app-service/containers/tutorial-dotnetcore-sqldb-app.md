---
title: Linux의 Azure App Service에서 .NET Core 및 SQL Database 웹앱 빌드 | Microsoft Docs
description: SQL Database에 연결하여 Linux의 Azure App Service에서 .NET Core 앱이 작동하도록 하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/11/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 654c187bcd552e9682115bc5e53ba69a3dca7c1d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-app-service-on-linux"></a>Linux의 Azure App Service에서 .NET Core 및 SQL Database 웹앱 빌드

> [!NOTE]
> 이 문서에서는 Linux의 App Service에 앱을 배포합니다. _Windows_에서 App Service를 배포하려면 [Azure App Service에서 .NET Core 및 SQL Database 웹앱 빌드](../app-service-web-tutorial-dotnetcore-sqldb.md)를 참조하세요.
>

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 사용하여 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 .NET Core 웹앱을 만들고 SQL Database에 연결하는 방법을 보여줍니다. 완료되면 .NET Core MVC 앱이 Linux의 App Service에서 실행됩니다.

![Linux의 App Service에서 실행 중인 앱](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 SQL Database 만들기
> * SQL Database에 .NET Core 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Git 설치](https://git-scm.com/)
* [.NET Core 설치](https://www.microsoft.com/net/core/)

## <a name="create-local-net-core-app"></a>로컬 .NET Core 앱 만들기

이 단계에서는 로컬 .NET Core 프로젝트를 설정합니다.

### <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제하고 해당 루트를 변경합니다.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

샘플 프로젝트에는 [Entity Framework Core](https://docs.microsoft.com/ef/core/)를 사용하는 기본 CRUD(Create-Read-Update-Delete) 앱이 포함되어 있습니다.

### <a name="run-the-application"></a>응용 프로그램 실행

다음 명령을 실행하여 필요한 패키지를 설치하고 데이터베이스 마이그레이션을 실행하고 응용 프로그램을 시작합니다.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

브라우저에서 `http://localhost:5000`으로 이동합니다. **새로 만들기** 링크를 선택하고 두 개의 _할 일_ 항목을 만듭니다.

![SQL Database 연결에 성공](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

언제든지 .NET Core를 중지하려면 터미널에서 `Ctrl+C`를 입력합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>프로덕션 SQL Database 만들기

이 단계에서는 Azure에 SQL Database를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

SQL Database의 경우 이 자습서에서는 [Azure SQL Database](/azure/sql-database/)를 사용합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database 논리 서버 만들기

Cloud Shell에서 [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) 명령을 사용하여 SQL Database 논리 서버를 만듭니다.

*\<server_name>* 자리 표시자를 고유한 SQL Database 이름으로 바꿉니다. 이 이름은 SQL Database 끝점(`<server_name>.database.windows.net`)의 일부로 사용되므로 Azure의 모든 논리 서버에서 고유해야 합니다. 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-50자 사이여야 합니다. 또한 *\<db_username>* 및 *\<db_password>* 를 선택한 사용자 이름 및 암호로 바꿉니다. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

SQL Database 논리 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>서버 방화벽 규칙 구성

[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az_sql_server_firewall_rule_create)를 사용하여 [Azure SQL Database 서버 수준 방화벽 규칙](../../sql-database/sql-database-firewall-configure.md)을 만듭니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>데이터베이스 만들기

[`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create) 명령을 사용하여 서버에서 [S0 성능 수준](../../sql-database/sql-database-service-tiers-dtu.md)인 데이터베이스를 만듭니다.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>연결 문자열 만들기

다음 문자열을 이전에 사용한 *\<server_name>*, *\<db_username>* 및 *\<db_password>* 로 바꿉니다.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

.NET Core 앱에 대한 연결 문자열입니다. 나중에 사용하기 위해 복사합니다.

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포

이 단계에서는 SQL Database 연결 .NET Core 응용 프로그램을 Linux의 App Service에 배포합니다.

### <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>환경 변수 구성

Azure 앱에 연결 문자열을 설정하려면 Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용합니다. 다음 명령에서 *\<app name>* 및 *\<connection_string>* 매개 변수를 앞에서 만든 연결 문자열로 바꿉니다.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

다음으로 `ASPNETCORE_ENVIRONMENT` 앱 설정을 _프로덕션_으로 지정합니다. 로컬 개발 환경에 SQLite를 사용하고 Azure 환경에 SQL Database를 사용하기 때문에 이 설정을 통해 Azure에서 실행 중인지 여부를 알 수 있습니다.

다음 예제에서는 Azure 웹앱에 `ASPNETCORE_ENVIRONMENT` 앱 설정을 구성합니다. *\<app_name>* 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>프로덕션에서 SQL Database에 연결

로컬 리포지토리에서 Startup.cs를 열고 다음 코드를 찾습니다.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

이전에 구성된 환경 변수를 사용하는 다음 코드로 바꿉니다.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

이코드가 프로덕션(즉, Azure 환경)에서 실행되고 있다고 감지되는 경우 구성한 연결 문자열을 사용하여 SQL Database에 연결합니다.

Azure에서 실행되는 경우 `Database.Migrate()` 호출이 해당 마이그레이션 구성에 따라 .NET Core 앱이 필요한 데이터베이스를 자동으로 생성하기 때문에 도움을 받을 수 있습니다. 

변경 내용을 저장한 후 Git 리포지토리로 커밋합니다. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

웹 브라우저를 사용하여 배포된 웹앱으로 이동합니다.

```bash
http://<app_name>.azurewebsites.net
```

몇 가지 할 일 항목을 추가합니다.

![Linux의 App Service에서 실행 중인 앱](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**축하합니다.** Linux의 App Service에서 데이터 기반 .NET Core 앱이 실행되고 있습니다.

## <a name="update-locally-and-redeploy"></a>로컬로 업데이트 및 다시 배포

이 단계에서는 데이터베이스 스키마를 변경하고 Azure에 게시합니다.

### <a name="update-your-data-model"></a>데이터 모델 업데이트

코드 편집기에서 _Models\Todo.cs_를 엽니다. 다음 속성을 `ToDo` 클래스에 추가합니다.

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First 마이그레이션을 로컬에서 실행

몇 가지 명령을 실행하여 로컬 데이터베이스를 업데이트합니다.

```bash
dotnet ef migrations add AddProperty
```

로컬 데이터베이스를 업데이트합니다.

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>새 속성 사용

`Done` 속성을 사용하도록 코드를 약간 변경합니다. 이 자습서에서는 간단하게 `Index` 및 `Create` 보기만 변경하여 속성의 실제 작동을 확인합니다.

_Controllers\TodosController.cs_를 엽니다.

`Create()` 메서드를 찾고 `Done`을 `Bind` 특성의 속성 목록에 추가합니다. 완료되면 `Create()` 메서드 시그니처가 다음 코드와 같이 표시됩니다.

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

_Views\Todos\Create.cshtml_을 엽니다.

Razor 코드에서 `Description`의 경우 `<div class="form-group">` 요소가 표시되고 `CreatedDate`의 경우 또 다른 `<div class="form-group">` 요소가 표시됩니다. 이러한 두 요소 바로 뒤에 `Done`의 경우 또 다른 `<div class="form-group">` 요소를 추가합니다.

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

_Views\Todos\Index.cshtml_을 엽니다.

빈 `<th></th>` 요소를 검색합니다. 이 요소 바로 위에 다음 Razor 코드를 추가합니다.

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

`asp-action` 태그 도우미가 포함된 `<td>` 요소를 찾습니다. 이 요소 바로 위에 다음 Razor 코드를 추가합니다.

```csharp
<td>
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

`Index` 및 `Create` 보기에서 변경 내용을 확인하기만 하면 됩니다.

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

로컬로 앱 실행

```bash
dotnet run
```

브라우저에서 `http://localhost:5000/`로 이동합니다. 이제 할 일 항목을 추가하고 **완료**를 확인할 수 있습니다. 그러면 홈페이지에 완료된 항목으로 표시됩니다. `Edit` 보기를 변경하지 않았으므로 `Edit` 보기에서 `Done` 필드가 표시되지 않습니다.

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

```bash
git add .
git commit -m "added done field"
git push azure master
```

`git push`가 완료되면 Azure 웹앱으로 이동하여 새 기능을 테스트해 봅니다.

![Code First 마이그레이션 후 Azure 웹앱](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

기존의 모든 할 일 항목이 계속 표시됩니다. .NET Core 앱을 다시 게시해도 SQL Database의 기존 데이터가 손실되지 않습니다. 또한 Entity Framework Core 마이그레이션은 데이터 스키마만 변경하고 기존 데이터는 그대로 유지합니다.

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 웹앱을 확인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

기본적으로 포털에는 웹앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure에서 SQL Database 만들기
> * SQL Database에 .NET Core 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)