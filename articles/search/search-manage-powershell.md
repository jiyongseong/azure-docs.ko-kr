---
title: Powershell 스크립트를 사용하여 Azure Search 관리 | Microsoft Docs
description: PowerShell 스크립트를 사용하여 Azure Search 서비스를 관리합니다. Azure Search 서비스 만들기 또는 업데이트 및 Azure Search 관리자 키 관리
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: 8f83f2652f7bd34f53dde1674c022f8792ae7658
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="manage-your-azure-search-service-with-powershell"></a>PowerShell을 사용한 Azure Search 서비스 관리
> [!div class="op_single_selector"]
> * [포털](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

이 항목에서는 Azure Search 서비스에 대한 다양한 관리 작업을 수행하는 PowerShell 명령을 설명합니다. 검색 서비스 만들기, 확장 및 해당 API 키 관리 과정을 설명합니다.
이러한 명령은 [Azure Search 관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement)에서 사용할 수 있는 관리 옵션과 유사합니다.

## <a name="prerequisites"></a>필수 조건
* Azure PowerShell 1.0 이상이 필요합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/overview)을 참조하세요.
* 아래 설명된 대로 PowerShell에서 Azure 구독에 로그인해야 합니다.

먼저 다음 명령을 사용하여 Azure에 로그인해야 합니다.

    Connect-AzureRmAccount

Microsoft Azure 로그인 대화 상자에서 Azure 계정의 메일 주소 및 해당 암호를 지정합니다.

또는 [비대화형으로 서비스 주체를 사용하여 로그인](../azure-resource-manager/resource-group-authenticate-service-principal.md)할 수 있습니다.

여러 Azure 구독이 있는 경우 Azure 구독을 설정해야 합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

구독을 지정하려면 다음 명령을 실행합니다. 다음 예제에서 구독 이름은 `ContosoSubscription`입니다.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>시작하는 데 도움이 되는 명령
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>다음 단계
이제 서비스를 만들었으므로 다음 단계를 수행할 수 있습니다. [인덱스](search-what-is-an-index.md)를 빌드하고 [인덱스를 쿼리](search-query-overview.md)하며 마지막으로 Azure Search를 사용하는 고유의 검색 응용 프로그램을 만들고 관리합니다.

* [Azure Portal에서 Azure Search 인덱스 만들기](search-create-index-portal.md)
* [Azure Portal에서 검색 탐색기를 사용하여 Azure Search 인덱스 쿼리](search-explorer.md)
* [기타 서비스에서 데이터를 로드하기 위한 인덱서 설정](search-indexer-overview.md)
* [.NET에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)
* [Azure Search 트래픽 분석](search-traffic-analytics.md)

