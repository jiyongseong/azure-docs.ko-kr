---
title: Azure SQL Database - 복제본에 대한 쿼리 읽기 | Microsoft Docs
description: Azure SQL Database는 읽기 확장이라는 읽기 전용 복제본의 용량을 사용하여 읽기 전용 작업의 부하를 분산하는 기능을 제공합니다.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/23/2018
ms.author: sashan
ms.openlocfilehash: d2472867c71aedf35e537a29d3912b9e423de2e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185429"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>읽기 전용 복제본을 사용하여 읽기 전용 쿼리 작업의 부하 분산(미리 보기)

**읽기 확장**을 사용하면 읽기 전용 복제본의 용량을 통해 Azure SQL Database 읽기 전용 작업의 부하를 분산할 수 있습니다. 

## <a name="overview-of-read-scale-out"></a>읽기 확장 개요

프리미엄 계층([DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)) 또는 중요 비즈니스 계층([vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md))의 각 데이터베이스는 가용성 SLA를 지원하기 위해 여러 개의 Always ON 복제본에 자동으로 프로비전됩니다. 이러한 복제본은 일반 데이터베이스 연결에서 사용되는 읽기/쓰기 복제본과 동일한 성능 수준으로 프로비전됩니다. **읽기 확장** 기능을 사용하면 읽기/쓰기 복제본을 공유하는 대신 읽기 전용 복제본의 용량을 사용하여 SQL Database 읽기 전용 작업의 부하를 분산할 수 있습니다. 이러한 방식으로 읽기 전용 워크로드는 주 읽기-쓰기 작업에서 격리되고 해당 성능에 영향을 주지 않습니다. 기능은 분석과 같은 논리적으로 구분된 읽기 전용 워크로드를 포함하는 응용 프로그램을 위한 것이므로 추가 비용 없이 이 추가 용량을 사용하여 성능 혜택을 활용할 수 있습니다.

특정 데이터베이스에서 읽기 확장 기능을 사용하려면, 데이터베이스를 만들 때 또는 나중에 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet을 호출하는 PowerShell을 사용하거나 [데이터베이스 - 만들기 또는 업데이트](/rest/api/sql/databases/createorupdate) 방법을 사용하는 Azure Resource Manager REST API를 통해 해당 구성을 변경하여 명시적으로 사용하도록 설정해야 합니다. 

데이터베이스에 대해 읽기 확장을 사용하도록 설정하면, 응용 프로그램의 연결 문자열에 구성된 `ApplicationIntent` 속성에 따라 해당 데이터베이스에 연결하는 응용 프로그램이 해당 데이터베이스의 읽기/쓰기 복제본 또는 읽기 전용 복제본으로 전달됩니다. `ApplicationIntent` 속성에 대한 자세한 내용은 [응용 프로그램 의도 지정](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)을 참조하세요.

읽기 스케일 아웃을 사용할 수 없거나 지원되지 않는 서비스 계층에서 ReadScale 속성을 설정한 경우 모든 연결은 `ApplicationIntent` 속성과 독립적으로 읽기/쓰기 복제본으로 이동됩니다.

> [!NOTE]
> 미리 보기 동안에는 쿼리 데이터 저장소 및 확장 이벤트가 읽기 전용 복제본에서 지원되지 않습니다.

## <a name="data-consistency"></a>데이터 일관성

Always ON의 이점 중 하나는 복제본이 트랜잭션 측면에서 항상 일관된 상태로 있지만 다른 시점에 서로 다른 복제본 간에 약간의 짧은 대기 시간이 있을 수 있다는 것입니다. 읽기 확장은 세션 수준 일관성을 지원합니다. 즉, 복제본 비가용성으로 인해 연결 오류가 발생한 후 읽기 전용 세션이 다시 연결되면, 읽기/쓰기 복제본이 포함된 100% 최신이 아닌 복제본으로 리디렉션될 수 있습니다. 마찬가지로 응용 프로그램에서 읽기/쓰기 세션을 사용하여 데이터를 쓰고 읽기 전용 세션을 사용하여 즉시 읽는 경우 최신 업데이트가 즉시 표시되지 않을 수도 있습니다. 이는 복제본에 대한 트랜잭션 로그 다시 실행이 비동기적이기 때문입니다.

> [!NOTE]
> 지역 내의 복제 대기 시간은 낮으며 이 상황은 드물게 발생합니다.


## <a name="connecting-to-a-read-only-replica"></a>읽기 전용 복제본에 연결

데이터베이스에 대한 읽기 확장을 사용하도록 설정하면 클라이언트에서 제공하는 연결 문자열의 `ApplicationIntent` 옵션은 연결이 쓰기 복제본 또는 읽기 전용 복제본으로 라우팅되는지 여부를 나타냅니다. 특히 `ApplicationIntent` 값이 `ReadWrite`(기본값)이면 연결이 데이터베이스의 읽기/쓰기 복제본으로 전달됩니다. 이는 기존 동작과 동일합니다. `ApplicationIntent` 값이 `ReadOnly`이면 연결이 읽기 가능한 복제본으로 라우팅됩니다.

예를 들어 다음 연결 문자열은 클라이언트를 읽기 전용 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

다음 연결 문자열 중 하나는 클라이언트를 읽기/쓰기 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

다음 쿼리를 실행하여 읽기 전용 복제본에 연결되어 있는지 여부를 확인할 수 있습니다. 읽기 전용 복제본에 연결된 경우 READ_ONLY가 반환됩니다.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Azure PowerShell을 사용하여 읽기 확장을 사용하거나 사용하지 않도록 설정

Azure PowerShell에서 읽기 확장을 관리하려면 2016년 12월 Azure PowerShell 릴리스 이상이 필요합니다. 최신 PowerShell 버전은 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 참조하세요.

Azure PowerShell에서 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) cmdlet을 호출하고 `-ReadScale` 매개 변수에 대해 원하는 값(`Enabled` 또는 `Disabled`)을 전달하여 읽기 확장을 사용하거나 사용하지 않도록 설정합니다. 또는 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet을 사용하여 읽기 확장을 사용하도록 설정된 새 데이터베이스를 만들 수 있습니다.

예를 들어 기존 데이터베이스에 대해 읽기 확장을 사용하도록 설정하려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

기존 데이터베이스에 대해 읽기 확장을 사용하지 않도록 설정하려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

읽기 확장을 사용하지 않도록 설정된 새 데이터베이스를 만들려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Azure SQL Database REST API를 사용하여 읽기 확장을 사용하거나 사용하지 않도록 설정

읽기 확장을 사용하도록 설정된 데이터베이스를 만들거나 기존 데이터베이스에 대해 읽기 확장을 사용하거나 사용하지 않도록 설정하려면, 아래 샘플 요청과 같이 `readScale` 속성이 `Enabled` 또는 `Disabled`로 설정된 해당 데이터베이스 엔터티를 만들거나 업데이트합니다.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
} 
```

자세한 내용은 [데이터베이스 - 만들기 또는 업데이트](/rest/api/sql/databases/createorupdate)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell을 사용하여 읽기 확장을 설정하는 방법에 대한 자세한 내용은 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet을 참조하세요.
- REST API를 사용하여 읽기 확장을 설정하는 방법에 대한 내용은 [데이터베이스 - 만들기 또는 업데이트](/rest/api/sql/databases/createorupdate)를 참조하세요.
