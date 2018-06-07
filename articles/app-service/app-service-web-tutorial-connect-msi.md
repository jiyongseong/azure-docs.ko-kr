---
title: 관리되는 서비스 ID를 사용하여 App Service에서 Azure SQL Database 연결 보호 | Microsoft Docs
description: 관리되는 서비스 ID를 사용하여 데이터베이스 연결을 보다 안전하게 만드는 방법과 이를 다른 Azure 서비스에 적용하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1b51638754287d3359eaea7bd5da3f71bf15cc89
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-secure-sql-database-connection-with-managed-service-identity"></a>자습서: 관리되는 서비스 ID를 사용하여 SQL Database 연결 보호

[App Service](app-service-web-overview.md)는 Azure에서 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 또한 [Azure SQL Database](/azure/sql-database/) 및 기타 Azure 서비스에 대한 액세스를 보호하기 위한 턴키 솔루션인 [관리되는 서비스 ID](app-service-managed-service-identity.md)를 앱에 제공합니다. App Service의 관리되는 서비스 ID는 연결 문자열의 자격 증명과 같은 비밀을 앱에서 제거하여 앱의 보안을 보다 강화합니다. 이 자습서에서는 [자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)에서 만든 샘플 ASP.NET 웹앱에 관리되는 서비스 ID를 추가합니다. 완료되면 샘플 앱은 사용자 이름과 암호 없이도 안전하게 SQL Database에 연결됩니다.

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 관리되는 서비스 ID 사용
> * 서비스 ID에 SQL Database 액세스 권한 부여
> * Azure Active Directory 인증을 사용하여 SQL Database로 인증하도록 응용 프로그램 코드 구성
> * SQL Database에서 서비스 ID에 최소한의 권한 부여

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 아티클은 [자습서: SQL Database를 사용하여 Azure에 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)의 중단된 위치부터 계속됩니다. 아직 하지 않은 경우 먼저 해당 자습서를 따릅니다. 또는 SQL Database를 사용하여 해당 ASP.NET 앱에 맞게 단계를 조정할 수 있습니다.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-service-identity"></a>관리되는 서비스 ID 사용

Azure 앱에 대한 서비스 ID를 사용하려면 Cloud Shell에서 [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az_webapp_identity_assign) 명령을 사용합니다. 다음 명령에서 *\<app_name>* 을 바꿉니다.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Azure Active Directory에서 ID가 생성된 후의 출력 예는 다음과 같습니다.

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

다음 단계에서는 `principalId` 값을 사용합니다. Azure Active Directory에서 새 ID의 세부 정보를 확인하려면 다음과 같은 선택적 명령을 `principalId` 값과 함께 실행합니다.

```azurecli-interactive
az ad sp show --id <principalid>`
```

## <a name="grant-database-access-to-identity"></a>ID에 데이터베이스 액세스 권한 부여

다음으로 Cloud Shell에서 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az_sql_server_ad-admin_create) 명령을 사용하여 앱의 서비스 ID에 데이터베이스 액세스 권한을 부여합니다. 다음 명령에서 *\<server_name>* 및 <principalid_from_last_step>을 바꿉니다. *\<admin_user>* 에 관리자 이름을 입력합니다.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

이제 관리되는 서비스 ID가 Azure SQL Database 서버에 액세스할 수 있습니다.

## <a name="modify-connection-string"></a>연결 문자열 수정

Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용하여 앱에 대해 이전에 설정한 연결을 수정합니다. 다음 명령에서 *\<app name>* 을 해당 앱 이름으로 바꾸고 *\<server_name>* 및 *\<db_name>* 을 해당 SQL Database 이름으로 바꿉니다.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>ASP.NET 코드 수정

Visual Studio의 **DotNetAppSqlDb** 프로젝트에서 _packages.config_를 열고 패키지 목록에 다음 줄을 추가합니다.

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
```

_Models\MyDatabaseContext.cs_를 열고 파일의 맨 위에 다음 `using` 문을 추가합니다.

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

`MyDatabaseContext` 클래스에 다음 생성자를 추가합니다.

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

이 생성자는 App Service의 Azure SQL Database에 대한 액세스 토큰을 사용하도록 사용자 지정 SqlConnection 개체를 구성합니다. 이 액세스 토큰을 사용하면 App Service 앱이 관리되는 서비스 ID를 사용하여 Azure SQL Database로 인증됩니다. 자세한 내용은 [Azure 리소스 토큰 가져오기](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources)를 참조하세요. `if` 문을 사용하면 LocalDB에서 로컬로 앱을 계속 테스트할 수 있습니다.

> [!NOTE]
> `SqlConnection.AccessToken`은 현재 .NET Framework 4.6 이상에서만 지원되며 [.NET Core](https://www.microsoft.com/net/learn/get-started/windows)에서는 지원되지 않습니다.
>

이 새 생성자를 사용하려면 `Controllers\TodosController.cs`를 열고 `private MyDatabaseContext db = new MyDatabaseContext();` 줄을 확인합니다. 기존 코드는 기본 `MyDatabaseContext` 컨트롤러를 사용하여 표준 연결 문자열을 통해 데이터베이스를 만듭니다. [변경](#modify-connection-string) 전 사용자 이름과 암호는 일반 텍스트였습니다.

전체 줄을 다음 코드로 바꿉니다.

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>변경 내용 게시

이제 Azure에 변경 내용을 게시하면 됩니다.

**솔루션 탐색기**에서 **DotNetAppSqlDb** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![솔루션 탐색기에서 게시](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

게시 페이지에서 **게시**를 클릭합니다. 새 웹 페이지에 할 일 목록이 표시되면 앱이 관리되는 서비스 ID를 사용하여 데이터베이스에 연결합니다.

![Code First 마이그레이션 후 Azure 웹앱](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

이제 이전처럼 할 일 목록을 편집할 수 있어야 합니다.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>ID에 최소한의 권한 부여

이전 단계에서는 관리되는 서비스 ID가 SQL Server에 Azure AD 관리자로 연결되어 있습니다. 관리되는 서비스 ID에 최소한의 권한을 부여하려면 Azure AD 관리자로 Azure SQL Database 서버에 로그인한 다음, 서비스 ID가 포함된 Azure Active Directory 그룹을 추가합니다. 

### <a name="add-managed-service-identity-to-an-azure-active-directory-group"></a>Azure Active Directory 그룹에 관리되는 서비스 ID 추가

Cloud Shell에서 다음 스크립트에 표시된 _myAzureSQLDBAccessGroup_이라는 새로운 Azure Active Directory 그룹에 앱용 관리되는 서비스 ID를 추가합니다.

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiid
az ad group member list -g $groupid
```

각 명령에 대한 전체 JSON 출력을 표시하려는 경우 매개 변수 `--query objectId --output tsv`를 삭제합니다.

### <a name="reconfigure-azure-ad-administrator"></a>Azure AD 관리자 다시 구성

이전에는 관리되는 서비스 ID를 SQL Database의 Azure AD 관리자로 할당했습니다. 이 ID는 대화형 로그인(데이터베이스 사용자 추가용)에 사용할 수 없으므로, 실제 Azure AD 사용자를 사용해야 합니다. Azure AD 사용자를 추가하려면 [Azure SQL Database 서버에 대한 Azure Active Directory 관리자 프로비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)의 단계를 따릅니다. 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Azure Active Directory 그룹에 사용 권한 부여

Cloud Shell에서 SQLCMD 명령을 사용하여 SQL Database에 로그인합니다. _\<servername>_ 을 SQL Database 서버 이름으로 바꾸고, _\<AADusername>_ 및 _\<AADpassword>_ 를 Azure AD 사용자의 자격 증명으로 바꿉니다.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

SQL 프롬프트에서 이전에 사용자 권한으로 만든 Azure Active Directory 그룹을 추가하는 다음 명령을 실행합니다.

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

`EXIT`를 입력하여 Cloud Shell 프롬프트로 돌아갑니다. 다음으로, SQLCMD를 다시 실행하되 _\<dbname>_ 에 데이터베이스 이름을 지정합니다.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

원하는 데이터베이스에 대한 SQL 프롬프트에서 Azure Active Directory 그룹에 읽기 및 쓰기 권한을 부여하려면 다음 명령을 실행합니다.

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 관리되는 서비스 ID 사용
> * 서비스 ID에 SQL Database 액세스 권한 부여
> * Azure Active Directory 인증을 사용하여 SQL Database로 인증하도록 응용 프로그램 코드 구성
> * SQL Database에서 서비스 ID에 최소한의 권한 부여

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)