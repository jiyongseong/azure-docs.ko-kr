---
title: Azure SQL의 DNS 별칭에 대한 PowerShell | Microsoft Docs
description: New-AzureRMSqlServerDNSAlias와 같은 PowerShell cmdlet을 사용하여 모든 클라이언트 구성을 변경하지 않고도, 새 클라이언트 연결을 다른 Azure SQL Database 서버로 리디렉션할 수 있습니다.
keywords: dns sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;amagarwa;maboja
ms.author: dmalik
ms.openlocfilehash: 06c27a2fda79afe5a7f4a0e9efdbc51e171bcbda
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Azure SQL Database의 DNS 별칭에 대한 PowerShell

이 문서에서는 Azure SQL Database에 대한 DNS 별칭을 관리하는 방법을 보여 주는 PowerShell 스크립트를 제공합니다. 이 스크립트는 다음 작업을 수행하는 다음 cmdlet을 실행합니다.


코드 예제에 사용된 cmdlet은 다음과 같습니다.
- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Azure SQL Database 서비스 시스템에 새 DNS 별칭을 만듭니다. 이 별칭은 Azure SQL Database 서버 1을 가리킵니다.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): SQL DB 서버 1에 할당된 모든 DNS 별칭을 가져와서 나열합니다.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): 별칭이 나타내도록 구성된 서버 이름을 서버 1에서 SQL DB 서버 2로 수정합니다.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): 별칭의 이름을 사용하여 SQL DB 서버 2에서 DNS 별칭을 제거합니다.


위의 PowerShell cmdlet은 버전 5.1.1부터 시작하는 **AzureRm.Sql** 모듈에 추가되었습니다.




#### <a name="dns-alias-in-connection-string"></a>연결 문자열의 DNS 별칭

특정 Azure SQL Database 서버에 연결하려면 SSMS(SQL Server Management Studio)와 같은 클라이언트가 실제 서버 이름 대신 DNS 별칭 이름을 제공할 수 있습니다. 다음 예제의 서버 문자열에서 별칭 *any-unique-alias-name*은 4개 노드 서버 문자열에서 점으로 구분된 첫 번째 노드를 대신합니다.
- 예제 서버 문자열: `any-unique-alias-name.database.windows.net`



## <a name="prerequisites"></a>필수 조건

이 문서에 제공된 데모 PowerShell 스크립트를 실행하려는 경우 다음과 같은 필수 조건이 적용됩니다.

- Azure 구독 및 계정 평가판의 경우 [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/]를 클릭합니다.

- cmdlet **New-AzureRMSqlServerDNSAlias**가 있는 Azure PowerShell 모듈
    - 설치 또는 업그레이드하려면 [Azure PowerShell 모듈 설치][install-azurerm-ps-84p]를 참조하세요.
    - powershell\_ise.exe에서 `Get-Module -ListAvailable AzureRM;`을 실행합니다.

- 2개의 Azure SQL Database 서버

## <a name="code-example"></a>코드 예제

다음 PowerShell 코드 예제에서는 먼저 여러 변수에 리터럴 값을 할당합니다. 이 코드를 실행하려면 먼저 시스템의 실제 값과 일치하도록 모든 자리 표시자 값을 편집해야 합니다. 또는 코드를 학습하기만 해도 됩니다. 코드의 콘솔 출력도 제공됩니다.


```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```


#### <a name="actual-console-output-from-the-powershell-example"></a>PowerShell 예제의 실제 콘솔 출력

다음 콘솔 출력은 실제 실행에서 복사되고 붙여넣은 것입니다.


```
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.
 
[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount : 

 
[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName    
----------------- ----------         ------------------    
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>> 
```

## <a name="next-steps"></a>다음 단계

SQL Database에 대한 DNS 별칭 기능의 전체 설명을 보려면 [Azure SQL Database에 대한 DNS 별칭][dns-alias-overview-37v]을 참조하세요.



<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

