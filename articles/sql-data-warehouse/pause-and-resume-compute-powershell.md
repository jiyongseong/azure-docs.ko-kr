---
title: '빠른 시작: Azure SQL Data Warehouse에서 계산 일시 중지 및 다시 시작 - PowerShell | Microsoft Docs'
description: 비용 절감을 위해 PowerShell을 사용하여 Azure SQL Data Warehouse에서 계산을 일시 중지합니다. 데이터 웨어하우스를 사용할 준비가 되면 계산을 다시 시작합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ef341a1528bf759461abfb7cfc6d878fd8a44cb4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure SQL Data Warehouse에서 계산 일시 중지 및 다시 시작
비용 절감을 위해 PowerShell을 사용하여 Azure SQL Data Warehouse에서 계산을 일시 중지합니다. 데이터 웨어하우스를 사용할 준비가 되면 [계산을 다시 시작](sql-data-warehouse-manage-compute-overview.md)합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

이 자습서에는 Azure PowerShell 모듈 버전 5.1.1 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 현재 사용 중인 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 빠른 시작에서는 일시 중지한 후 다시 시작할 수 있는 SQL Data Warehouse가 이미 있다고 가정합니다. 하나 만들어야 할 경우 [만들기 및 연결 - 포털](create-data-warehouse-portal.md)을 사용하여 **mySampleDataWarehouse**라는 데이터 웨어하우스를 만들 수 있습니다.

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
2. Azure Portal의 왼쪽 페이지에서 **SQL Database**를 클릭합니다.
3. **SQL Database** 페이지에서 **mySampleDataWarehouse**를 선택합니다. 데이터 웨어하우스가 열립니다.

    ![서버 이름 및 리소스 그룹](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. 데이터베이스 이름에 해당하는 데이터 웨어하우스 이름을 적어 둡니다. 또한 서버 이름 및 리소스 그룹도 적어 둡니다. 이러한 항목을
5.  일시 중지 및 다시 시작 명령에서 사용합니다.
6. 서버가 foo.database.windows.net인 경우 PowerShell cmdlet에 서버 이름의 첫 번째 부분만 사용합니다. 위 그림에서 전체 서버 이름은 newserver-20171113.database.windows.net입니다. PowerShell cmdlet에서 접미사를 삭제하고 **newserver-20171113**을 서버 이름으로 사용합니다.

## <a name="pause-compute"></a>계산 일시 중지
비용을 절약하기 위해 필요에 따라 계산 리소스를 일지 중지 및 다시 시작할 수 있습니다. 예를 들어, 밤 시간과 주말에 데이터베이스를 사용하지 않으려면 해당 시간에 일시 중지했다가 주간에 다시 시작할 수 있습니다. 데이터베이스를 일시 중지하는 동안 계산 리소스에 대한 요금이 부과되지 않습니다. 그러나 저장소에 대한 비용은 계속 청구됩니다.

데이터베이스를 일시 중지하려면 [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase.md) cmdlet을 사용합니다. 다음 예제에서는 **newserver-20171113**이라는 서버에 호스트된 **mySampleDataWarehouse**라는 데이터 웨어하우스를 일시 중지합니다. 서버는 이름이 **myResourceGroup**인 Azure 리소스 그룹 내에 있습니다.


```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

다음 예에서는 이를 변형하여 데이터베이스를 $database 개체로 검색합니다. 그런 다음 개체를 [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase)에 파이프합니다. 결과는 resultDatabase 개체에 저장됩니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>계산 다시 시작
데이터베이스를 시작하려면 [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase) cmdlet을 사용합니다. 다음 예제에서는 newserver-20171113이라는 서버에 호스트된 mySampleDataWarehouse라는 데이터베이스를 시작합니다. 서버는 이름이 myResourceGroup인 Azure 리소스 그룹 내에 있습니다.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

다음 예에서는 이를 변형하여 데이터베이스를 $database 개체로 검색합니다. 그런 다음 개체를 [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase.md)에 파이프하고 결과를 $resultDatabase에 저장합니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

## <a name="clean-up-resources"></a>리소스 정리

데이터 웨어하우스 단위 및 데이터 웨어하우스에 저장된 데이터에 대해 요금이 청구됩니다. 이러한 계산 및 저장소 리소스에 대한 요금이 별도로 청구됩니다. 

- 저장소에 데이터를 유지하려는 경우 계산을 일시 중지합니다.
- 앞으로 요금이 부과되지 않게 하려면 데이터 웨어하우스를 삭제하면 됩니다.

필요에 따라 다음 단계에 따라 리소스를 정리합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 데이터 웨어하우스를 클릭합니다.

    ![리소스 정리](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. 계산을 일시 중지하려면 **일시 중지** 단추를 클릭합니다. 데이터 웨어하우스가 일시 중지되면 **시작** 단추가 표시됩니다.  계산을 다시 시작하려면 **시작**을 클릭합니다.

2. 계산 또는 저장소에 대한 요금이 청구되지 않도록 데이터 웨어하우스를 제거하려면 **삭제**를 클릭합니다.

3. 만든 SQL 서버를 제거하려면 **mynewserver-20171113.database.windows.net**을 클릭하고 **삭제**를 클릭합니다.  서버를 삭제하면 해당 서버에 할당된 모든 데이터베이스가 삭제되므로 주의해서 실행해야 합니다.

4. 리소스 그룹을 제거하려면 **myResourceGroup**을 클릭하고 **리소스 그룹 삭제**를 클릭합니다.


## <a name="next-steps"></a>다음 단계
이제 데이터 웨어하우스에 대한 계산을 일시 중지하고 다시 시작했습니다. Azure SQL Data Warehouse에 대해 자세히 알아보려면 데이터 로드에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
>[SQL 데이터 웨어하우스로 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)
