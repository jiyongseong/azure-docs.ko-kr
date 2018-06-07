---
title: Azure PowerShell을 사용하여 Virtual Machine Scale Sets 관리 | Microsoft Docs
description: 인스턴스를 시작하고 중지하는 방법 및 확장 집합 용량을 변경하는 방법과 같은 Virtual Machine Scale Sets를 관리하는 공통 Azure PowerShell cmdlet입니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: c463dd26c106b3178becc977a8afd742220d7973
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Azure PowerShell을 사용하여 가상 머신 확장 집합 관리
가상 머신 확장 집합의 수명 주기 동안 하나 이상의 관리 작업을 실행해야 합니다. 또한 다양한 수명 주기 작업을 자동화하는 스크립트를 만들어야 하는 경우가 있습니다. 이 문서에서는 이러한 작업을 수행할 수 있는 공통 Azure PowerShell cmdlet 중 일부를 설명합니다.

이러한 관리 작업을 완료하려면 최신 Azure PowerShell 모듈이 필요합니다. 자세한 내용은 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 참조하세요. 가상 머신 확장 집합을 만들어야 하는 경우 [Azure PowerShell을 사용하여 확장 집합을 만들](quick-create-powershell.md) 수 있습니다.


## <a name="view-information-about-a-scale-set"></a>확장 집합에 대한 정보 보기
확장 집합에 대한 전체 정보를 보려면 [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)를 사용합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myScaleSet*이라는 확장 집합에 대한 정보를 가져옵니다. 다음과 같이 고유한 이름을 입력합니다.

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>확장 집합의 VM 보기
확장 집합에서 VM 인스턴스 목록을 보려면 [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm)을 사용합니다. 다음 예제에서는 *myScaleSet*이라는 확장 집합 및 *myResourceGroup* 리소스 그룹에서 모든 VM 인스턴스를 나열합니다. 다음과 같은 이름에 고유한 값을 제공합니다.

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

특정 VM 인스턴스에 대한 추가 정보를 보려면 `-InstanceId` 매개 변수를 [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm)에 추가하고 보려는 인스턴스를 지정합니다. 다음 예제에서는 *myScaleSet*이라는 확장 집합 및 *myResourceGroup*이라는 리소스 그룹에서 VM 인스턴스 *0*에 대한 정보를 봅니다. 다음과 같이 고유한 이름을 입력합니다.

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>확장 집합의 용량 변경
이전 명령은 확장 집합 및 VM 인스턴스에 대한 정보를 표시했습니다. 확장 집합에서 인스턴스 수를 늘리거나 줄이려면 용량을 변경할 수 있습니다. 확장 집합은 자동으로 필요한 수의 VM을 만들거나 제거한 후 응용 프로그램 트래픽을 받도록 VM을 구성합니다.

먼저 [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)를 포함하는 확장 집합 개체를 만들고 `sku.capacity`에 새 값을 지정합니다. 용량 변경 내용을 적용하려면 [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)를 사용합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myScaleSet*을 *5* 인스턴스의 용량으로 업데이트합니다. 다음과 같이 사용자 고유의 값을 제공합니다.

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

확장 집합의 용량을 업데이트하는 데 몇 분 정도가 걸립니다. 확장 집합의 용량을 줄이는 경우 가장 높은 인스턴스 ID를 포함하는 VM을 먼저 제거합니다.


## <a name="stop-and-start-vms-in-a-scale-set"></a>확장 집합에서 VM 중지 및 시작
확장 집합에서 하나 이상의 VM을 중지하려면 [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss)를 사용합니다. `-InstanceId` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 중지하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 중지됩니다. 여러 VM을 중지하려면 각 인스턴스 ID를 쉼표로 구분합니다.

다음 예제에서는 *myScaleSet*이라는 확장 집합 및 *myResourceGroup*이라는 리소스 그룹에서 인스턴스 *0*을 중지합니다. 다음과 같이 값을 제공합니다.

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

기본적으로 중지된 VM은 할당을 취소하고 계산 요금을 부과하지 않습니다. VM이 중지될 때 프로비전된 상태로 유지하려는 경우 `-StayProvisioned` 매개 변수를 위의 명령에 추가합니다. 프로비전된 상태로 유지하는 중지된 VM은 기본 계산 요금을 부과합니다.


### <a name="start-vms-in-a-scale-set"></a>확장 집합의 VM 시작
확장 집합에서 하나 이상의 VM을 시작하려면 [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss)를 사용합니다. `-InstanceId` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 시작하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 시작됩니다. 여러 VM을 시작하려면 각 인스턴스 ID를 쉼표로 구분합니다.

다음 예제에서는 *myScaleSet*이라는 확장 집합 및 *myResourceGroup*이라는 리소스 그룹에서 인스턴스 *0*을 시작합니다. 다음과 같이 값을 제공합니다.

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>확장 집합의 VM 다시 시작
확장 집합에서 하나 이상의 VM을 다시 시작하려면 [Retart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss)를 사용합니다. `-InstanceId` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 다시 시작하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 다시 시작됩니다. 여러 VM을 다시 시작하려면 각 인스턴스 ID를 쉼표로 구분합니다.

다음 예제에서는 *myScaleSet*이라는 확장 집합 및 *myResourceGroup*이라는 리소스 그룹에서 인스턴스 *0*을 다시 시작합니다. 다음과 같이 값을 제공합니다.

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>확장 집합에서 VM 제거
확장 집합에서 하나 이상의 VM을 제거하려면 [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss)를 사용합니다. `-InstanceId` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 제거하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 제거됩니다. 여러 VM을 제거하려면 각 인스턴스 ID를 쉼표로 구분합니다.

다음 예제에서는 *myScaleSet*이라는 확장 집합 및 *myResourceGroup*이라는 리소스 그룹에서 인스턴스 *0*을 제거합니다. 다음과 같이 값을 제공합니다.

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>다음 단계
확장 집합에 대한 다른 공통 작업에는 [응용 프로그램을 배포](virtual-machine-scale-sets-deploy-app.md)하고 [VM 인스턴스를 업그레이드](virtual-machine-scale-sets-upgrade-scale-set.md)하는 방법이 포함됩니다. Azure PowerShell을 사용하여 [자동 크기 조정 규칙을 구성](virtual-machine-scale-sets-autoscale-overview.md)할 수도 있습니다.
