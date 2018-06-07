---
title: "PowerShell을 사용한 Azure Event Hubs 리소스 관리 | Microsoft Docs"
description: "PowerShell 모듈을 사용하여 Event Hubs 만들기 및 관리"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: de86b8241166d4e0bd03beb22550464457e3db5e
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/20/2017
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>PowerShell을 사용하여 Event Hubs 리소스 관리

Microsoft Azure PowerShell은 Azure 서비스의 배포와 관리를 제어하고 자동화하는 데 사용할 수 있는 스크립팅 환경입니다. 이 문서에서는 [Event Hubs Resource Manager PowerShell 모듈](/powershell/module/azurerm.eventhub)을 사용하여 로컬 Azure PowerShell 콘솔 또는 스크립트를 통해 Event Hubs 엔터티(네임스페이스, 개별 이벤트 허브 및 소비자 그룹)를 프로비전하고 관리하는 방법을 설명합니다.

Azure Resource Manager 템플릿을 사용하여 Event Hubs 리소스를 관리할 수도 있습니다. 자세한 내용은 문서 [Azure Resource Manager 템플릿을 사용하여 이벤트 허브 및 소비자 그룹이 있는 Event Hubs 네임스페이스 만들기](event-hubs-resource-manager-namespace-event-hub.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 작업을 수행하려면 다음이 필요합니다.

* Azure 구독. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][purchase options], [구성원 제공 항목][member offers] 또는 [무료 계정][free account]을 참조하세요.
* Azure PowerShell이 설치된 컴퓨터 관련 지침은 [Azure PowerShell Cmdlet 시작](/powershell/azure/get-started-azureps)을 참조하세요.
* PowerShell 스크립트, NuGet 패키지 및 .NET Framework 전반에 대한 지식

## <a name="get-started"></a>시작하기

첫 번째 단계는 PowerShell을 사용하여 Azure 계정 및 Azure 구독에 로그인하는 것입니다. [Azure PowerShell cmdlet 시작](/powershell/azure/get-started-azureps)의 지침에 따라 Azure 계정에 로그인한 다음, Azure 구독에서 리소스를 검색하고 액세스합니다.

## <a name="provision-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 프로비전

Event Hubs 네임스페이스를 사용할 때 [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace) 및 [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace) cmdlet을 사용할 수 있습니다.

이 예제에서는 스크립트에 `$Namespace`과(와) `$Location`(이)라는 몇 가지 로컬 변수를 만듭니다.

* `$Namespace`는 사용하려는 Event Hubs 네임스페이스의 이름입니다.
* `$Location`은(는) 네임스페이스를 프로비전할 데이터 센터를 식별합니다.
* `$CurrentNamespace`에는 검색하거나 만드는 참조 네임스페이스가 저장됩니다.

실제 스크립트에서 `$Namespace` 및 `$Location`은(는) 매개 변수로 전달할 수 있습니다.

스크립트의 이 부분은 다음을 수행합니다.

1. 지정된 이름의 Event Hubs 네임스페이스를 검색하려고 합니다.
2. 네임스페이스가 있으면 발견된 항목을 보고합니다.
3. 네임스페이스가 없으면 만든 다음 새로 만든 네임스페이스를 검색합니다.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이벤트 허브를 만들려면 이전 섹션의 스크립트를 사용하여 네임스페이스 확인을 수행합니다. 그런 다음, [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) cmdlet을 사용하여 이벤트 허브를 만듭니다.

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>소비자 그룹 만들기

이벤트 허브 내에 소비자 그룹을 만들려면 이전 섹션의 스크립트를 사용하여 네임스페이스 및 이벤트 허브 확인을 수행합니다. 그런 다음, [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) cmdlet을 사용하여 이벤트 허브 내에 소비자 그룹을 만듭니다. 예: 

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>사용자 메타데이터 설정

이전 섹션의 스크립트를 실행한 후 [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) cmdlet을 사용하여 다음 예제와 같이 소비자 그룹의 속성을 업데이트할 수 있습니다.

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>이벤트 허브 제거

만든 이벤트 허브를 제거하려면 다음 예제와 같이 `Remove-*` cmdlet을 사용할 수 있습니다.

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>다음 단계

- [여기](/powershell/module/azurerm.eventhub)에서 전체 Event Hubs Resource Manager PowerShell 모듈 설명서를 참조하세요. 이 페이지에는 사용 가능한 모든 cmdlet이 표시됩니다.
- Azure Resource Manager 템플릿 사용에 대한 자세한 내용은 문서 [Azure Resource Manager 템플릿을 사용하여 이벤트 허브 및 소비자 그룹이 있는 Event Hubs 네임스페이스 만들기](event-hubs-resource-manager-namespace-event-hub.md)를 참조하세요.
- [Event Hubs .NET 관리 라이브러리](event-hubs-management-libraries.md)에 대한 정보

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
