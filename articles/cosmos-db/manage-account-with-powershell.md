---
title: Azure Cosmos DB Automation - Powershell로 관리 | Microsoft Docs
description: Azure Powershell을 사용하여 Azure Cosmos DB 계정을 관리합니다.
services: cosmos-db
author: dmakwana
manager: kfile
editor: ''
tags: azure-resource-manager
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: e049c94ae6c0ea2cf350d8c2ae9ca359f18a8751
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos DB 계정 만들기

이 문서에서는 Azure Powershell을 사용하여 Azure Cosmos DB 데이터베이스 계정 관리를 자동화하는 명령에 대해 설명합니다. [다중 하위 지역 데이터베이스 계정][scaling-globally]에서 계정 키 및 장애 조치 우선 순위를 관리하는 명령도 포함되어 있습니다. 데이터베이스 계정을 업데이트하면 일관성 정책을 수정하고 하위 지역을 추가/제거할 수 있습니다. Azure Cosmos DB 계정의 플랫폼 간 관리를 위해 [Azure CLI](cli-samples.md), [리소스 공급자 REST API][rp-rest-api] 또는 [Azure Portal](create-sql-api-dotnet.md#create-account)을 사용할 수 있습니다.

## <a name="getting-started"></a>시작하기

[Azure PowerShell을 설치 및 구성하는 방법][powershell-install-configure]의 지침에 따라 Powershell에서 Azure Resource Manager 계정을 설치하고 로그인합니다.

### <a name="notes"></a>메모

* 사용자에게 확인을 요구하지 않고 다음 명령을 실행하려면 명령에 `-Force` 플래그를 추가합니다.
* 다음 명령은 모두 동기식입니다.

## <a id="create-documentdb-account-powershell"></a> Azure Cosmos DB 계정 만들기

이 명령을 사용하면 Azure Cosmos DB 데이터베이스 계정을 만들 수 있습니다. 새 데이터베이스 계정을 특정 [일관성 정책](consistency-levels.md)을 사용하여 단일 하위 지역 또는 [다중 하위 지역][scaling-globally]으로 구성합니다.

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` - 데이터베이스 계정의 쓰기 하위 지역의 위치 이름입니다. 이 위치에는 0인 장애 조치 우선 순위 값이 있어야 합니다. 데이터베이스 계정마다 정확히 쓰기 하위 지역 하나만 있어야 합니다.
* `<read-region-location>` - 데이터베이스 계정의 읽기 하위 지역의 위치 이름입니다. 이 위치에는 0보다 큰 장애 조치 우선 순위 값이 있어야 합니다. 데이터베이스 계정마다 읽기 하위 지역이 둘 이상 있을 수 있습니다.
* `<ip-range-filter>` 지정된 데이터베이스 계정에 대해 허용된 클라이언트 IP 목록으로 포함할 IP 주소 집합 또는 IP 주소 범위를 CIDR 형식으로 지정합니다. IP 주소/범위는 쉼표로 구분하며 공백을 포함해서는 안 됩니다. 자세한 내용은 [Azure Cosmos DB 방화벽 지원](firewall-support.md)을 참조하세요.
* `<default-consistency-level>`Azure Cosmos DB 계정의 기본 일관성 수준입니다. 자세한 내용은 [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하세요.
* `<max-interval>` - 제한된 부실(Bounded Staleness) 일관성과 함께 사용되는 경우 이 값은 부실 허용 시간(초)을 나타냅니다. 값의 허용 범위는 1-100입니다.
* `<max-staleness-prefix>` - 제한된 부실 일관성과 함께 사용되는 경우 이 값은 허용된 부실 요청 수를 나타냅니다. 이 값의 허용 범위는 1-2,147,483,647입니다.
* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<resource-group-location>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 Azure 리소스 그룹의 위치입니다.
* `<database-account-name>` - 만들어질 Azure Cosmos DB 데이터베이스 계정의 이름입니다. 소문자, 숫자, '-'(대시) 문자만 사용할 수 있으며, 3-50자여야 합니다.

예: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>메모
* 앞의 예제에서는 두 개의 하위 지역이 있는 데이터베이스 계정을 만듭니다. 하나의 하위 지역(쓰기 하위 지역으로 지정되고 장애 조치 우선 순위 값이 0임) 또는 세 개 이상의 하위 지역이 있는 데이터베이스 계정도 만들 수 있습니다. 자세한 내용은 [다중 하위 지역 데이터베이스 계정][scaling-globally]을 참조하세요.
* 위치는 Azure Cosmos DB를 일반적으로 사용할 수 있는 하위 지역이어야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.

## <a id="update-documentdb-account-powershell"></a> Azure Cosmos DB 데이터베이스 계정 업데이트

이 명령을 사용하면 Azure Cosmos DB 데이터베이스 계정 속성을 업데이트할 수 있습니다. 여기에는 일관성 정책과 데이터베이스 계정이 있는 위치가 포함됩니다.

> [!NOTE]
> 이 명령을 사용하면 하위 지역을 추가/제거할 수 있지만 장애 조치 우선 순위는 수정할 수 없습니다. 장애 조치 우선 순위를 수정하려면 [아래 예제](#modify-failover-priority-powershell)를 참조하세요.

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` - 데이터베이스 계정의 쓰기 하위 지역의 위치 이름입니다. 이 위치에는 0인 장애 조치 우선 순위 값이 있어야 합니다. 데이터베이스 계정마다 정확히 쓰기 하위 지역 하나만 있어야 합니다.
* `<read-region-location>` - 데이터베이스 계정의 읽기 하위 지역의 위치 이름입니다. 이 위치에는 0보다 큰 장애 조치 우선 순위 값이 있어야 합니다. 데이터베이스 계정마다 읽기 하위 지역이 둘 이상 있을 수 있습니다.
* `<default-consistency-level>`Azure Cosmos DB 계정의 기본 일관성 수준입니다. 자세한 내용은 [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하세요.
* `<ip-range-filter>` 지정된 데이터베이스 계정에 대해 허용된 클라이언트 IP 목록으로 포함할 IP 주소 집합 또는 IP 주소 범위를 CIDR 형식으로 지정합니다. IP 주소/범위는 쉼표로 구분하며 공백을 포함해서는 안 됩니다. 자세한 내용은 [Azure Cosmos DB 방화벽 지원](firewall-support.md)을 참조하세요.
* `<max-interval>` - 제한된 부실(Bounded Staleness) 일관성과 함께 사용되는 경우 이 값은 부실 허용 시간(초)을 나타냅니다. 값의 허용 범위는 1-100입니다.
* `<max-staleness-prefix>` - 제한된 부실 일관성과 함께 사용되는 경우 이 값은 허용된 부실 요청 수를 나타냅니다. 이 값의 허용 범위는 1-2,147,483,647입니다.
* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<resource-group-location>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 Azure 리소스 그룹의 위치입니다.
* `<database-account-name>` - 업데이트할 Azure Cosmos DB 데이터베이스 계정의 이름입니다.

예: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Azure Cosmos DB 데이터베이스 계정 삭제

이 명령을 사용하면 기존 Azure Cosmos DB 데이터베이스 계정을 삭제할 수 있습니다.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<database-account-name>` - 삭제할 Azure Cosmos DB 데이터베이스 계정의 이름입니다.

예:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Azure Cosmos DB 데이터베이스 계정의 속성 가져오기

이 명령을 사용하면 기존 Azure Cosmos DB 데이터베이스 계정의 속성을 가져올 수 있습니다.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<database-account-name>` - Azure Cosmos DB 데이터베이스 계정의 이름입니다.

예:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> - Azure Cosmos DB 데이터베이스 계정의 업데이트 태그입니다.

다음 예제에서는 Azure Cosmos DB 데이터베이스 계정에 대해 [Azure 리소스 태그][azure-resource-tags]를 설정하는 방법을 설명합니다.

> [!NOTE]
> 이 명령은 `-Tags` 플래그에 해당 매개 변수를 추가하여 만들기 또는 업데이트 명령과 결합할 수 있습니다.

예:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> 계정 키 나열

Azure Cosmos DB 계정을 만들면 해당 서비스에서 Azure Cosmos DB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. Azure Cosmos DB에서는 2개의 액세스 키를 제공해서 사용자가 Azure Cosmos DB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다. 읽기 전용 작업을 인증하기 위한 읽기 전용 키도 사용할 수 있습니다. 두 개의 읽기-쓰기 키(기본 및 보조) 및 두 개의 읽기 전용 키(기본 및 보조)가 있습니다.

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<database-account-name>` - Azure Cosmos DB 데이터베이스 계정의 이름입니다.

예:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> 연결 문자열 나열

MongoDB 계정의 경우 MongoDB 앱을 데이터베이스 계정에 연결하기 위한 연결 문자열은 다음 명령을 사용하여 검색할 수 있습니다.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<database-account-name>` - Azure Cosmos DB 데이터베이스 계정의 이름입니다.

예:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> 계정 키 다시 생성

저장소 연결을 더욱 안전하게 유지할 수 있도록 정기적으로 Azure Cosmos DB 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 액세스 키 하나를 다시 생성하는 동안 다른 액세스 키를 사용하여 Azure Cosmos DB 계정에 대한 연결을 유지할 수 있습니다.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<database-account-name>` - Azure Cosmos DB 데이터베이스 계정의 이름입니다.
* `<key-kind>` - 다시 생성할 네 가지 유형의 키, 즉 ["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"] 중 하나입니다.

예:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Azure Cosmos DB 데이터베이스 계정의 장애 조치(Failover) 우선 순위 수정

다중 하위 지역 데이터베이스 계정의 경우 Azure Cosmos DB 데이터베이스 계정이 있는 다양한 하위 지역의 장애 조치(Failover) 우선 순위를 변경할 수 있습니다. Azure Cosmos DB 데이터베이스 계정의 장애 조치(Failover)에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포][distribute-data-globally]를 참조하세요.

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` - 데이터베이스 계정의 쓰기 하위 지역의 위치 이름입니다. 이 위치에는 0인 장애 조치 우선 순위 값이 있어야 합니다. 데이터베이스 계정마다 정확히 쓰기 하위 지역 하나만 있어야 합니다.
* `<read-region-location>` - 데이터베이스 계정의 읽기 하위 지역의 위치 이름입니다. 이 위치에는 0보다 큰 장애 조치 우선 순위 값이 있어야 합니다. 데이터베이스 계정마다 읽기 하위 지역이 둘 이상 있을 수 있습니다.
* `<resource-group-name>` - 새 Azure Cosmos DB 데이터베이스 계정이 속하는 [Azure 리소스 그룹][azure-resource-groups]의 이름입니다.
* `<database-account-name>` - Azure Cosmos DB 데이터베이스 계정의 이름입니다.

예:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>다음 단계

* .NET을 사용하여 연결하려면 [.NET을 사용하여 연결 및 쿼리](create-sql-api-dotnet.md)를 참조하세요.
* Node.js를 사용하여 연결하려면 [Node.js 및 MongoDB 앱을 사용하여 연결 및 쿼리](create-mongodb-nodejs.md)를 참조하세요.

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
