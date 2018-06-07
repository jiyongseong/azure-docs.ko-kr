---
title: PowerShell을 사용하여 Azure Cosmos DB Table API 작업 수행 | Microsoft Docs
description: PowerShell을 사용하여 Azure Cosmos DB Table API 작업을 수행하는 방법
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d40a56ca3c07bfadcf6e24d407b059a39522ca2b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Cosmos DB Table API 작업 수행 

>[!NOTE]
>Azure Cosmos DB Table API는 턴키 전역 배포, 짧은 읽기 및 쓰기 대기 시간, 자동 보조 인덱싱 및 전용 처리량 등 테이블 저장소에 대한 프리미엄 기능을 제공합니다. 대부분의 경우 이 문서의 PowerShell 명령은 Azure Cosmos DB Table API와 Azure Table Storage에 모두 작동하지만 이 문서는 Azure Cosmos DB Table API에만 국한됩니다. Azure Table Storage를 사용하는 경우 [Azure PowerShell을 사용하여 Azure Table Storage 작업 수행](table-storage-how-to-use-powershell.md)을 참조하세요.
>

Azure Cosmos DB Table API를 사용하면 방대한 구조화된 비관계형 데이터 집합을 저장 및 쿼리할 수 있습니다. 서비스의 주요 구성 요소로는 테이블, 엔터티 및 속성이 있습니다. 테이블은 엔터티 컬렉션입니다. 엔터티는 속성의 집합입니다. 각 엔터티는 모두 이름 값 쌍으로 구성된 속성을 최대 252개 가질 수 있습니다. 이 문서에서는 Azure Cosmos DB Table API 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [Azure Cosmos DB Table API 소개](table-introduction.md) 및 [Table API를 사용하여 .NET 응용 프로그램 빌드](create-table-dotnet.md)를 참조하세요.

이 방법 문서에서는 일반 Table API 작업을 설명합니다. 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * 테이블 만들기
> * 테이블 검색
> * 테이블 엔터티 추가
> * 테이블 쿼리
> * 테이블 엔터티 삭제

## <a name="prerequisites"></a>필수 조건

이 예제에는 Azure PowerShell 모듈 버전 4.4.0 이상이 필요합니다. PowerShell 창에서 `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 확인합니다. 표시되는 항목이 없거나 업그레이드가 필요한 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 

Azure PowerShell을 설치하거나 업데이트한 후에 엔터티 관리 명령이 있는 **AzureRmStorageTable** 모듈을 설치해야 합니다. 이 모듈을 설치하려면 PowerShell을 관리자 권한으로 실행하고 **Install-Module** 명령을 사용합니다.

```powershell
Install-Module AzureRmStorageTable
```

그런 후 이러한 PowerShell cmdlet을 사용하기 위해 Azure Cosmos DB 어셈블리를 로컬로 설치합니다. 이를 수행하는 방법에 대한 지침은 [Azure RM Storage Tables PowerShell module for Cosmos DB Tables](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/)(Cosmos DB Table용 Azure RM 저장소 테이블 PowerShell 모듈)를 참조하세요.

다음 연습을 사용하려면 Azure Cosmos DB 데이터베이스 계정이 필요합니다. 계정이 없는 경우 [Azure Portal](https://portal.azure.com)을 사용하여 새 Azure Cosmos DB 계정을 만듭니다. 새 데이터베이스 계정을 만드는 방법에 대한 자세한 내용은 [Azure Cosmos DB: 데이터베이스 계정 만들기](create-table-dotnet.md#create-a-database-account)를 참조하세요.

포털에서 데이터베이스 계정 이름 및 리소스 그룹을 가져옵니다. 테이블에 액세스하려면 스크립트에 이러한 값을 추가해야 합니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>테이블 만들기 또는 테이블 참조

테이블을 만들거나 테이블에 대한 참조를 가져오려면 **Get-AzureStorageTableTable**을 사용합니다. 존재하지 않는 테이블 이름으로 이 cmdlet을 호출하는 경우 해당 이름을 가진 새 테이블을 만들고 새 테이블에 대한 참조를 반환합니다. 테이블이 존재하는 경우 기존 테이블에 대한 참조를 반환합니다.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

PowerShell을 사용하여 Azure Cosmos DB 계정의 테이블을 나열할 수 없지만 포털에 로그인하여 테이블을 확인할 수 있습니다. 이제 테이블의 엔터티를 관리하는 방법을 살펴보겠습니다.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>테이블 삭제 

PowerShell은 Azure Cosmos DB에서 테이블 삭제를 지원하지 않습니다. 테이블을 삭제하려면 [Azure Portal](https://azure.portal.com)로 이동하여 사용 중인 Azure Cosmos DB 계정을 찾은 다음 테이블을 찾아서 삭제합니다. 

## <a name="clean-up-resources"></a>리소스 정리

새 리소스 그룹과 해당 그룹에 새 Azure Cosmos DB 계정을 만든 후에는 리소스 그룹을 제거하여 이 연습에서 만든 모든 자산을 제거할 수 있습니다. 이 명령은 리소스 그룹 자체뿐만 아니라 해당 그룹에 포함된 모든 리소스를 삭제합니다.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 다음 방법을 포함하여 PowerShell을 사용한 일반적인 Table API 작업에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 테이블 만들기
> * 테이블 검색
> * 테이블 엔터티 추가
> * 테이블 쿼리
> * 테이블 엔터티 삭제

자세한 내용은 다음 문서를 참조하세요.

* [PowerShell에서 Azure Storage 테이블 작업](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, MacOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
