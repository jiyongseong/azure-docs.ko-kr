---
title: Azure에서 Windows VM 리소스 이동 | Microsoft Docs
description: Resource Manager 배포 모델에서 다른 Azure 구독 또는 리소스 그룹으로 Windows VM을 이동합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: b98b8c947fb34b60c7bd27b006672e0e9d923d3b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>다른 Azure 구독 또는 리소스 그룹으로 Windows VM 이동
이 문서에서는 리소스 그룹 또는 구독 간에 Windows VM을 이동하는 방법을 안내합니다. 개인 구독에서 원래 VM을 만들고 회사 구독으로 이동한 후 작업을 계속하려는 경우에 구독 간의 이동이 편리할 수 있습니다.

> [!IMPORTANT]
>지금은 Managed Disks를 이동할 수 없습니다. 
>
>새 리소스 ID는 이동의 일부로 생성됩니다. VM을 이동하면 새 리소스 ID를 사용하도록 도구 및 스크립트를 업데이트해야 합니다. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>PowerShell을 사용하여 VM 이동

가상 머신을 다른 리소스 그룹에 이동하려면 모든 종속 리소스를 이동하는지 확인해야 합니다. AzureRMResource cmdlet을 사용하려면 각 리소스의 ResourceId가 필요합니다. [Find-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource) cmdlet을 사용하여 ResourceId의 목록을 가져올 수 있습니다.

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

VM을 이동하려면 여러 리소스를 이동해야 합니다. Find-AzureRMResource 출력을 사용하여 ResourceIds의 쉼표로 구분된 목록을 만들고 이를 [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource)에 전달하여 대상으로 이동시킬 수 있습니다. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
리소스를 다른 구독으로 이동하기 위해 **-DestinationSubscriptionId** 매개 변수를 포함합니다. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


지정한 리소스를 이동할 것인지 묻는 메시지가 나타납니다. 

## <a name="next-steps"></a>다음 단계
리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../resource-group-move-resources.md)을 참조하세요.    

