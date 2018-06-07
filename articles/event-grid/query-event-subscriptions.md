---
title: Azure Event Grid 구독 쿼리
description: Azure Event Grid 구독을 나열하는 방법을 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: 2b46cde4a352e647ee97669f116a6c1926879fa0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="query-event-grid-subscriptions"></a>Event Grid 구독 쿼리 

이 문서에서는 Azure 구독에 Event Grid 구독을 나열하는 방법을 설명합니다. 기존 Event Grid 구독을 쿼리하는 경우 다양한 구독 유형을 이해하는 것이 중요합니다. 원하는 구독 유형에 따라 다른 매개 변수를 제공합니다.

## <a name="resource-groups-and-azure-subscriptions"></a>리소스 그룹 및 Azure 구독

Azure 구독 및 리소스 그룹은 Azure 리소스가 아닙니다. 따라서 리소스 그룹 또는 Azure 구독에 대한 이벤트 그리드 구독에는 Azure 리소스에 대한 이벤트 그리드 구독과 동일한 속성이 없습니다. 리소스 그룹 또는 Azure 구독에 대한 이벤트 그리드 구독은 전역으로 간주됩니다.

Azure 구독 및 해당 리소스 그룹에 대한 이벤트 그리드 구독을 얻으려면 매개 변수를 제공할 필요가 없습니다. 쿼리하려는 Azure 구독을 선택했는지 확인합니다. 다음 예제는 사용자 지정 항목이나 Azure 리소스에 대한 이벤트 그리드 구독을 가져오지 않습니다.

Azure CLI의 경우 

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Set-AzureRmContext -Subscription "My Azure Subscription"
Get-AzureRmEventGridSubscription
```

Azure 구독에 대한 이벤트 그리드 구독을 얻으려면 **Microsoft.Resources.Subscriptions**의 항목 유형을 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Azure 구독 내 모든 리소스 그룹에 대한 이벤트 그리드 구독을 얻으려면 **Microsoft.Resources.ResourceGroups**의 항목 유형을 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

지정된 리소스 그룹에 대한 이벤트 그리드 구독을 얻으려면, 리소스 그룹의 이름을 매개 변수로 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>사용자 지정 항목 및 Azure 리소스

이벤트 그리드 사용자 지정 항목은 Azure 리소스입니다. 따라서 Blob 저장소 계정과 같은 사용자 지정 항목 및 기타 리소스에 대한 이벤트 그리드 구독을 동일한 방식으로 쿼리합니다. 사용자 지정 항목에 대한 이벤트 그리드 구독을 얻으려면 리소스를 식별하거나 리소스의 위치를 식별하는 매개 변수를 제공해야 합니다. Azure 구독 전반에서 리소스에 대한 이벤트 그리드 구독을 광범위하게 쿼리할 수는 없습니다.

특정 위치의 사용자 지정 항목 및 기타 리소스에 대한 이벤트 그리드 구독을 얻으려면 위치 이름을 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -Location westus2
```

특정 위치의 사용자 지정 항목에 대한 구독을 얻으려면 **Microsoft.EventGrid.Topics**의 위치와 항목 종류를 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

특정 위치의 저장소 계정에 대한 구독을 얻으려면 **Microsoft.Storage.StorageAccounts**의 위치와 항목 종류를 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

사용자 지정 항목에 대한 이벤트 그리드 구독을 얻으려면 사용자 지정 항목의 이름과 해당 리소스 그룹의 이름을 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

특정 리소스에 대한 이벤트 그리드 구독을 가져오려면 리소스 ID를 제공합니다.

Azure CLI의 경우 

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
$resourceid = (Get-AzureRmResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzureRmEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
