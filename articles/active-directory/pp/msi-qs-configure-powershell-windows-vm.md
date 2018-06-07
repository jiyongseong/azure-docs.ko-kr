---
title: PowerShell을 사용하여 Azure VM에서 MSI를 구성하는 방법
description: PowerShell을 사용하여 Azure VM에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9a466a5c695277a7b5833f997e2ad7281c962f3f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>PowerShell을 사용하여 VM MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 Azure VM에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

또한 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](https://www.powershellgallery.com/packages/AzureRM)(버전 4.3.1 이상)을 설치합니다.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 MSI를 사용하도록 설정

MSI 기반 VM을 만들려면:

1. 다음 Azure VM 퀵 스타트 중 하나를 참조하여 필요한 섹션만 완료하세요("Azure에 로그인", "리소스 그룹 만들기", "네트워킹 그룹 만들기", "VM 만들기"). 

   > [!IMPORTANT] 
   > "VM 만들기" 섹션으로 이동하려면 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 구문을 조금 수정합니다. 예를 들어 MSI를 사용하여 VM을 프로비전하려면 `-IdentityType "SystemAssigned"` 매개 변수를 추가해야 합니다.
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [PowerShell을 사용하여 Windows 가상 머신 만들기](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell을 사용하여 Linux 가상 머신 만들기](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type` 매개 변수를 사용하여 MSI VM 확장을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>기존 Azure VM에서 MSI를 사용하도록 설정

기존 Virtual Machine에서 MSI를 사용하도록 설정해야 하는 경우 다음을 수행합니다.

1. `Connect-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. 먼저 `Get-AzureRmVM` cmdlet을 사용하여 VM 속성을 검색합니다. 그런 후에 MSI를 사용하도록 설정하려면 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet에서 `-IdentityType` 스위치를 사용합니다.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type` 매개 변수를 사용하여 MSI VM 확장을 추가합니다. VM 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다. 기존 VM의 위치와 일치하는 올바른 `-Location` 매개 변수를 지정해야 합니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM에서 MSI 제거

MSI가 더 이상 필요하지 않은 Virtual Machine이 있으면 `RemoveAzureRmVMExtension` cmdlet을 사용하여 해당 VM에서 MSI를 제거할 수 있습니다.

1. `Connect-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. 확장을 추가할 때 사용한 것과 같은 이름을 지정하고 `-Name` 스위치를 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet과 함께 사용:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>관련 콘텐츠

- [관리 서비스 ID 개요](msi-overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조:
  
  - [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell을 사용하여 Linux 가상 머신 만들기](~/articles/virtual-machines/linux/quick-create-powershell.md) 

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
















