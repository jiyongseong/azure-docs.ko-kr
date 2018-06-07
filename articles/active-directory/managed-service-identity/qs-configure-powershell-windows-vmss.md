---
title: PowerShell을 사용하여 Azure VMSS에서 MSI를 구성하는 방법
description: PowerShell을 사용하여 Azure VMSS에서 시스템 및 사용자 할당 ID를 구성하기 위한 단계별 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 97c5e2dde3faeaad13317597bef4f70455d22102
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930132"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>PowerShell을 사용하여 VMSS MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 Azure VMSS(Virtual Machine Scale Set)에서 다음과 같은 관리 서비스 ID 작업을 수행하는 방법을 알아봅니다.
- Azure VMSS에서 시스템 할당 ID를 사용 및 사용하지 않도록 설정
- Azure VMSS에서 사용자 할당 ID 추가 및 제거

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](https://www.powershellgallery.com/packages/AzureRM)을 설치합니다. 

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 시스템 할당 ID를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Azure VMSS 생성 중에 시스템 할당 ID를 사용하도록 설정

시스템 할당 ID가 설정된 VMSS를 만들려면

1. 시스템 할당 ID가 있는 VMSS를 만들려면 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) cmdlet 참조 문서의 *예제 1*을 참조하세요.  `-IdentityType SystemAssigned` 매개 변수를 `New-AzureRmVmssConfig` cmdlet에 추가:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (선택 사항) [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet에서 `-Name` 및 `-Type` 매개 변수를 사용하여 MSI VMSS 확장을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다.

    > [!NOTE]
    > 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>기존 Azure VMSS에서 시스템 할당 ID를 사용하도록 설정

기존 Azure VMSS에서 시스템 할당 ID를 사용하도록 설정해야 하는 경우:

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”:

   ```powershell
   Login-AzureRmAccount
   ```

2. 먼저 [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss) cmdlet을 사용하여 VMSS 속성을 검색합니다. 그런 후에 시스템 할당 ID를 사용하도록 설정하려면 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet에서 `-IdentityType` 스위치를 사용합니다.

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet에서 `-Name` 및 `-Type` 매개 변수를 사용하여 MSI VMSS 확장을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Azure VMSS에서 시스템 할당 ID를 사용하지 않도록 설정

> [!NOTE]
> 가상 머신 확장 집합에서 관리 서비스 ID를 사용하지 않도록 설정하는 기능은 현재 지원되지 않습니다. 그 동안 시스템 할당 및 사용자 할당 ID를 전환할 수 있습니다.

시스템 할당 ID는 더 이상 필요하지 않고 사용자 할당 ID는 여전히 필요한 가상 머신 확장 집합이 있는 경우 다음 cmdlet을 사용합니다.

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”:

2. 다음 cmdlet을 실행합니다.

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>사용자 할당 ID

이 섹션에서는 Azure PowerShell을 사용하여 VMSS에서 사용자 할당 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Azure VMSS 생성 중에 사용자 할당 ID 할당

PowerShell을 통해 사용자 할당 ID가 있는 새 VMSS 만들기는 현재 지원되지 않습니다. 기존 VMSS에 사용자 할당 ID를 추가하는 방법은 다음 섹션을 참조하세요. 업데이트를 다시 확인하세요.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>기존 Azure VMSS에 사용자 ID 할당

기존 Azure VMSS에 사용자 할당 ID를 할당하려면

1. `Connect-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. 먼저 `Get-AzureRmVM` cmdlet을 사용하여 VM 속성을 검색합니다. 그런 다음, Azure VMSS에 사용자 할당 ID를 할당하고 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet에서 `-IdentityType` 및 `-IdentityID` 스위치를 사용합니다. `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2`를 사용자 고유의 값으로 바꿉니다.

   > [!IMPORTANT]
   > 사용자 할당 ID 만들기는 영숫자와 하이픈(0-9 또는 a-z 또는 A-Z 또는 -) 문자만 지원합니다. 또한 VM/VMSS에 대한 할당이 제대로 작동하려면 이름의 길이가 24자로 제한되어야 합니다. 업데이트를 다시 확인하세요. 자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Azure VMSS에서 사용자 할당 ID 제거

> [!NOTE]
> 시스템 할당 ID가 없다면, 가상 머신 확장 집합에서 모든 사용자 할당 ID를 제거하는 것은 현재 지원되지 않습니다. 업데이트를 다시 확인하세요.

VMSS에 여러 사용자 할당 ID가 있는 경우 다음 명령을 사용하여 마지막 것을 제외하고 모두 제거할 수 있습니다. `<RESOURCE GROUP>` 및 `<VMSS NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<MSI NAME>`은 사용자 할당 ID의 이름 속성으로, VMSS에 남아 있어야 합니다. 이 정보는 `az vmss show`를 사용하여 VMSS의 ID 섹션에서 찾을 수 있습니다.

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

VMSS에 시스템 할당 및 사용자 할당 ID가 모두 있는 경우 시스템 할당만 사용하도록 전환하여 모든 사용자 할당 ID를 제거할 수 있습니다. 다음 명령을 사용합니다.

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>관련 콘텐츠

- [관리 서비스 ID 개요](overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조:
  
  - [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md) 

















