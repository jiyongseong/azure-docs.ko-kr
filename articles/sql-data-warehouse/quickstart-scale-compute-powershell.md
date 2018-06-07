---
title: '빠른 시작: Azure SQL Data Warehouse에서 계산 능력 확장 - PowerShell | Microsoft Docs'
description: PowerShell에서 Azure SQL Data Warehouse의 계산 능력을 조정합니다. 더 나은 성능을 위해 계산 능력을 확장하거나 비용 절감을 위해 다시 축소할 수 있습니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0718365153390f525b22ef07559a822c777c2ff4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>빠른 시작: PowerShell에서 Azure SQL Data Warehouse의 계산 능력 조정

PowerShell에서 Azure SQL Data Warehouse의 계산 능력을 조정합니다. 더 나은 성능을 위해 [계산 능력을 확장](sql-data-warehouse-manage-compute-overview.md)하거나 비용 절약을 위해 다시 축소할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

이 자습서에는 Azure PowerShell 모듈 버전 5.1.1 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 현재 사용 중인 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 빠른 시작에서는 규모를 조정할 수 있는 SQL Data Warehouse가 이미 있다고 가정합니다. 하나 만들어야 할 경우 [만들기 및 연결 - 포털](create-data-warehouse-portal.md)을 사용하여 **mySampleDataWarehouse**라는 데이터 웨어하우스를 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
```

사용 중인 구독을 보려면 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription)을 실행합니다.

```powershell
Get-AzureRmSubscription
```

기본 구독과는 다른 구독을 사용해야 할 경우 [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription)을 실행합니다.

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>데이터 웨어하우스 정보 조회

일시 중지 및 다시 시작하려는 데이터 웨어하우스의 데이터베이스 이름, 서버 이름 및 리소스 그룹을 찾습니다.

다음 단계에 따라 데이터 웨어하우스에 대한 위치 정보를 찾습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 페이지에서 **SQL 데이터 웨어하우스**를 클릭합니다.
3. **SQL 데이터 웨어하우스** 페이지에서 **mySampleDataWarehouse**를 선택합니다. 데이터 웨어하우스가 열립니다.

    ![서버 이름 및 리소스 그룹](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. 데이터베이스 이름으로 사용되는 데이터 웨어하우스 이름을 적어 둡니다. 데이터 웨어하우스는 데이터베이스의 한 종류입니다. 또한 서버 이름 및 리소스 그룹도 적어 둡니다. 일시 중지 및 다시 시작 명령에서 이러한 항목을 사용합니다.
5. 서버가 foo.database.windows.net인 경우 PowerShell cmdlet에 서버 이름의 첫 번째 부분만 사용합니다. 위 그림에서 전체 서버 이름은 newserver-20171113.database.windows.net입니다. PowerShell cmdlet에서는 **newserver-20180430**을 서버 이름으로 사용합니다.

## <a name="scale-compute"></a>계산 조정

SQL Data Warehouse에서 데이터 웨어하우스 단위를 조정하여 계산 리소스를 늘리거나 줄일 수 있습니다. [만들기 및 연결 - 포털](create-data-warehouse-portal.md)에서 **mySampleDataWarehouse**를 만들고 400 DWU로 초기화했습니다. 다음 단계에서는 **mySampleDataWarehouse**에 대해 DWU를 조정합니다.

데이터 웨어하우스 단위를 변경하려면 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell cmdlet을 사용합니다. 다음 예제에서는 **mynewserver-20180430** 서버의 리소스 그룹 **myResourceGroup**에서 호스트되는 데이터베이스 **mySampleDataWarehouse**에 대해 데이터 웨어하우스 단위를 DW300으로 설정합니다.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-data-warehouse-state"></a>데이터 웨어하우스 상태 확인

데이터 웨어하우스의 현재 상태를 보려면 [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) PowerShell cmdlet을 사용합니다. 이렇게 하면 리소스 그룹 **myResourceGroup** 및 서버 **mynewserver-20180430.database.windows.net**에 있는 **mySampleDataWarehouse** 데이터베이스의 상태가 표시됩니다.

```powershell
$database = Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

결과는 다음과 유사합니다.

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

출력에서 데이터베이스의 **상태**를 확인할 수 있습니다. 이 경우 이 데이터베이스가 온라인 상태인지 확인할 수 있습니다.  이 명령을 실행하면 온라인, 일시 중지 중, 다시 시작 중, 크기 조정 또는 일시 중지됨의 상태가 표시됩니다.

상태를 자체적으로 확인하려면 다음 명령을 실행합니다.

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>다음 단계
지금까지 데이터 웨어하우스에 대한 계산 성능을 조정하는 방법을 배웠습니다. Azure SQL Data Warehouse에 대해 자세히 알아보려면 데이터 로드에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
>[SQL 데이터 웨어하우스로 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)
