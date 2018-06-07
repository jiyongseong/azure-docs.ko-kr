---
title: PowerShell을 사용하여 MSI에 Azure 리소스 액세스 권한을 할당하는 방법
description: PowerShell을 사용하여 특정 리소스에 MSI를 할당하고 다른 리소스에 대한 액세스 권한을 할당하기 위한 단계별 지침을 제공합니다.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6d503526ed97dd1f61269acd83810cd44598d72f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33929132"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>PowerShell을 사용하여 MSI(관리 서비스 ID)에 리소스 액세스 권한 할당

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

MSI를 사용하여 Azure 리소스를 구성한 후에는 모든 보안 주체와 마찬가지로 MSI에 다른 리소스 액세스 권한을 제공할 수 있습니다. 이 예제에서는 PowerShell을 사용하여 Azure 가상 컴퓨터의 MSI에 Azure Storage 계정 액세스 권한을 제공하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

또한 [Azure PowerShell 버전 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1)을 아직 설치하지 않은 경우 설치합니다.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC를 사용하여 MSI에 다른 리소스 액세스 권한 할당

[Azure VM](qs-configure-powershell-windows-vm.md)과 같은 Azure 리소스에서 MSI를 사용하도록 설정한 후에 다음을 수행합니다.

1. `Connect-AzureRmAccount` cmdlet을 사용하여 Azure에 로그인합니다. MSI를 구성한 Azure 구독과 연결된 계정을 사용하세요.

   ```powershell
   Connect-AzureRmAccount
   ```
2. 이 예제에서는 Azure VM에 저장소 계정 액세스 권한을 제공합니다. 먼저 [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)을 사용하여 MSI를 사용하도록 설정할 때 만든 VM "myVM"의 서비스 주체를 가져옵니다. 그런 다음 [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)를 사용하여 저장소 계정 "myStorageAcct"에 대한 "Reader" 액세스 권한을 VM에 제공합니다.

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>문제 해결

리소스의 MSI가 사용 가능한 ID 목록에 표시되지 않으면 MSI가 올바르게 사용하도록 설정되었는지 확인하세요. 이 예제에서는 [Azure Portal](https://portal.azure.com)에서 Azure VM으로 돌아가서 다음을 수행할 수 있습니다.

- "구성" 페이지에서 MSI enabled = "Yes"인지 확인합니다.
- "확장" 페이지에서 MSI 확장이 올바르게 배포되었는지 확인합니다.

이 두 항목 중 하나가 올바르지 않으면 리소스에서 MSI를 재배포하거나 배포 오류 관련 문제를 해결해야 할 수 있습니다.

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.
- Azure VM에서 MSI를 사용하도록 설정하려면 [PowerShell을 사용하여 Azure VM MSI(관리 서비스 ID) 구성](qs-configure-powershell-windows-vm.md)을 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

