---
title: PowerShell을 사용한 Azure 솔루션 관리 | Microsoft Docs
description: Azure PowerShell 및 Resource Manager를 사용하여 리소스를 관리합니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 13e5836aea0e307cdce5bcdcd5cf3c50969dfbf8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Azure PowerShell을 사용하여 리소스 관리

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="understand-scope"></a>범위 이해

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

이 문서에서는 이러한 설정이 완료된 후 쉽게 제거할 수 있도록 모든 관리 설정을 리소스 그룹에 적용 합니다.

리소스 그룹을 만들어 보겠습니다.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

현재 리소스 그룹이 비어 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>역할 할당

이 문서에서는 가상 머신 및 관련 가상 네트워크를 배포합니다. 가상 머신 솔루션을 관리하기 위해서는 일반적으로 필요한 액세스 권한을 제공하는 세 가지 리소스 특정 역할이 있습니다.

* [Virtual Machine 기여자](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [네트워크 기여자](../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage 계정 기여자](../role-based-access-control/built-in-roles.md#storage-account-contributor)

개별 사용자에게 역할을 할당하는 대신 비슷한 동작을 수행해야 하는 사용자에게 [Azure Active Directory 그룹을 만들기](../active-directory/active-directory-groups-create-azure-portal.md)가 더 쉽습니다. 그런 다음, 해당 그룹에 적절한 역할을 할당합니다. 이 문서를 단순화하려면 구성원이 없는 Azure Active Directory 그룹을 만들 수 있습니다. 여전히 해당 그룹에 역할 범위를 할당할 수 있습니다. 

다음 예제에서는 그룹을 만들고 해당 그룹에 리소스 그룹에 대한 가상 머신 참가자 역할을 할당합니다. `New-AzureAdGroup`명령을 실행하려면 [Azure Cloud Shell](/azure/cloud-shell/overview)을 사용하거나 [Azure AD PowerShell 모듈을 다운로드](https://www.powershellgallery.com/packages/AzureAD/)해야 합니다.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

일반적으로 **네트워크 참가자**와 **Storage 계정 참가자**를 위한 프로세스를 반복해 배포된 리소스를 관리하도록 사용자가 할당됐는지 확인합니다. 이 문서에서는 이러한 단계를 건너뛸 수 있습니다.

## <a name="azure-policies"></a>Azure 정책

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>정책 적용

사용 중인 구독에 이미 여러 개의 정책 정의가 있습니다. 사용 가능한 정책 정의를 참조하려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

기존 정책 정의를 참조할 수 있습니다. 해당 정책 유형은 **BuiltIn** 또는 **사용자 지정** 중 하나입니다. 할당하고자 하는 조건을 설명하는 정책에 대한 정의를 검토해 보세요. 이 문서에서는 다음과 같은 정책을 할당할 수 있습니다.

* 모든 리소스의 위치 제한
* 가상 머신에 대한 SKU 제한
* 관리 디스크를 사용하지 않는 가상 머신 감사

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>가상 머신 배포

솔루션을 배포할 수 있도록 역할 및 정책을 할당했습니다. 기본 크기는 허용된 SKU 중 하나인 Standard_DS1_v2입니다. 이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 가상 머신에 대한 사용자 이름 및 암호로 구성됩니다.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

배포를 완료한 후 솔루션에 추가 관리 설정을 적용할 수 있습니다.

## <a name="lock-resources"></a>리소스 잠금

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>리소스 잠금

가상 머신과 네트워크 보안 그룹을 잠그려면 다음을 사용합니다.

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

특별히 잠금을 해제하는 경우 가상 머신을 삭제할 수 있습니다. 해당 단계는 [리소스 정리](#clean-up-resources)에 표시됩니다.

## <a name="tag-resources"></a>리소스 태그 지정

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>리소스 태그 지정

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

가상 머신에 태그를 적용하려면 다음을 사용합니다.

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>태그로 리소스 찾기

태그 이름 및 값을 사용하여 리소스를 찾으려면 다음을 사용합니다.

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

태그 값으로 모든 가상 머신 중지와 같은 관리 작업에 대한 반환 값을 사용할 수 있습니다.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>태그 값으로 비용 보기

태그를 리소스에 적용한 후 해당 태그를 사용하여 리소스에 대한 비용을 볼 수 있습니다. 최신 사용법을 표시하기 위한 비용 분석에 시간이 걸리므로 아직 비용을 참조할 수 없습니다. 해당 비용을 사용할 수 있는 경우 사용 중인 구독에서 여러 리소스 그룹에 걸친 리소스에 대한 비용을 볼 수 있습니다. 사용자는 비용을 확인하려면 [청구 정보에 대한 구독 수준의 액세스 권한](../billing/billing-manage-access.md)을 가져야 합니다.

해당 포털에서 태그로 비용을 보려면 구독을 선택하고 **비용 분석**을 선택합니다.

![비용 분석](./media/powershell-azure-resource-manager/select-cost-analysis.png)

태그 값으로 필터링하고 **적용**을 선택합니다.

![태그로 비용 보기](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

[Azure 청구 API](../billing/billing-usage-rate-card-overview.md)를 사용하여 프로그래밍 방식으로 비용을 볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

잠금이 해제될 때까지는 잠긴 네트워크 보안 그룹을 삭제할 수 없습니다. 잠금을 해제하려면 다음을 사용합니다.

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계
* 가상 머신 모니터링에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Windows 가상 머신 모니터링 및 업데이트](../virtual-machines/windows/tutorial-monitoring.md)를 참조하세요.
* 권장 보안 사례를 구현하기 위해 Azure Security Center 사용하는 것에 대한 자세한 내용은 [Azure Security Center를 사용하여 가상 머신 보안을 모니터링](../virtual-machines/windows/tutorial-azure-security.md)합니다.
* 기존 리소스를 새 리소스 그룹으로 이동할 수 있습니다. 예제를 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.
