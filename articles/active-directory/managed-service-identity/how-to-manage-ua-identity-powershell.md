---
title: Azure PowerShell을 사용하여 사용자 할당 MSI 만들고 나열하고 삭제하는 방법
description: Azure PowerShell을 사용하여 사용자 할당 관리 서비스 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: cc9b1b002c882a847d0ba2359caf4a193ea8d648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930793"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Azure PowerShell을 사용하여 사용자 할당 ID 생성, 나열 또는 삭제

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure PowerShell을 사용하여 사용자 할당 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](https://www.powershellgallery.com/packages/AzureRM)을 설치합니다.
- PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

사용자 할당 ID를 만들려면 [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) 명령을 사용합니다. `ResourceGroupName` 매개 변수는 사용자 할당 ID가 만들어질 리소스 그룹을 지정하고 `-Name` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

> [!IMPORTANT]
> 사용자 할당 ID 만들기는 영숫자와 하이픈(0-9 또는 a-z 또는 A-Z 또는 -) 문자만 지원합니다. 또한 VM/VMSS에 대한 할당이 제대로 작동하려면 이름의 길이가 24자로 제한되어야 합니다. 업데이트를 다시 확인하십시오. 자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>사용자 할당 ID 나열

사용자 할당 ID를 나열하려면 [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) 명령을 사용합니다.  `-ResourceGroupName` 매개 변수는 사용자 할당 ID가 만들어지는 리소스 그룹을 지정합니다.  `<RESOURCE GROUP>`을 원하는 값으로 바꿉니다.

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
응답에서 사용자 ID에는 키 `Type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>사용자 할당 ID 삭제

사용자 ID를 삭제하려면 [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) 명령을 사용합니다.  `-ResourceGroupName` 매개 변수는 사용자 할당 ID가 만들어진 리소스 그룹을 지정하고 `-Name` 매개 변수는 그 이름을 지정합니다.  `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> 사용자 할당 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. ID 할당은 별도로 제거해야 합니다.

## <a name="related-content"></a>관련 콘텐츠

Azure PowerShell MSI 명령의 전체 목록 및 자세한 내용은 [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity)를 참조하세요.


 
