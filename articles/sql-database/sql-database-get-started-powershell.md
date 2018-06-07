---
title: 'Azure PowerShell: SQL Database 만들기 | Microsoft Docs'
description: Azure Portal에서 SQL Database 논리 서버, 서버 수준 방화벽 규칙 및 데이터베이스를 만드는 방법을 알아봅니다.
keywords: SQL 데이터베이스 자습서, SQL 데이터베이스 만들기
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: PowerShell
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 91c92de4d7c94cceec69b19647b1fe0bf31915c4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-single-azure-sql-database-using-powershell"></a>PowerShell을 사용하여 단일 Azure SQL Database 만들기

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 PowerShell이 사용됩니다. 이 가이드에서는 PowerShell을 사용하여 [Azure SQL Database 논리 서버](sql-database-features.md)의 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)에서 Azure SQL Database를 배포하는 방법에 대해 자세히 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

이 자습서에는 Azure PowerShell 모듈 버전 4.0 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
```

## <a name="create-variables"></a>변수 만들기

이 빠른 시작의 스크립트에서 사용할 변수를 정의합니다.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>논리 서버 만들기

[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) 명령을 사용하여 [Azure SQL Database 논리 서버](sql-database-features.md)를 만듭니다. 논리 서버는 그룹으로 관리되는 데이터베이스 그룹을 포함합니다. 다음 예제에서는 관리자 로그인 이름이 `ServerAdmin`이고 암호가 `ChangeYourAdminPassword1`인 리소스 그룹에 임의로 이름이 지정된 서버를 생성합니다. 이러한 미리 정의된 값은 필요에 따라 바꿉니다.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>서버 방화벽 규칙 구성

[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) 명령을 사용하여 [Azure SQL Database 서버 수준 방화벽 규칙](sql-database-firewall-configure.md)을 만듭니다. 서버 수준 방화벽 규칙을 통해 외부 응용 프로그램(예: SQL Server Management Studio 또는 SQLCMD 유틸리티)이 SQL Database 서비스 방화벽을 통해 SQL Database에 연결되도록 할 수 있습니다. 다음 예제에서 방화벽은 다른 Azure 리소스에 대해서만 열립니다. 외부 연결을 사용하려면 IP 주소를 사용자 환경에 적절한 주소로 변경합니다. 모든 IP 주소를 열려면 시작 IP 주소로 0.0.0.0을, 끝나는 IP 주소로 255.255.255.255를 사용합니다.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 1433을 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>샘플 데이터를 사용하여 서버에서 데이터베이스 만들기

[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) 명령을 사용하여 서버에 [S0 성능 수준](sql-database-service-tiers-dtu.md)인 데이터베이스를 만듭니다. 다음 예제에서는 `mySampleDatabase`라는 데이터베이스를 만들고 AdventureWorksLT 샘플 데이터를 이 데이터베이스에 로드합니다. 이러한 미리 정의된 값을 원하는 대로 바꿉니다(이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 빌드).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksLT" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다.

> [!TIP]
> 다음 빠른 시작을 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>다음 단계

- 이제 데이터베이스가 생겼으니 자주 사용하는 도구 및 언어 중 하나를 사용하여 [연결하고 쿼리](sql-database-connect-query.md)할 수 있습니다.
- 첫 번째 데이터베이스를 디자인하고, 테이블을 만들고, 데이터를 삽입하는 방법을 알아보려면 다음 자습서 중 하나를 참조하세요.
 - [SSMS를 사용하여 첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
  - [Azure SQL Database 설계 및 C#과 ADO.NET에 연결T](sql-database-design-first-database-csharp.md)

