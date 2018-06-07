---
title: 관리 그룹을 변경, 삭제 또는 관리하는 방법 - Azure | Microsoft Docs
description: 관리 그룹 계층 구조를 유지 관리하고 업데이트하는 방법을 알아봅니다.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/15/2018
ms.author: rithorn
ms.openlocfilehash: 822a2df113b848f07e616f155881f345028cee1d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-your-resources-with-management-groups"></a>관리 그룹으로 리소스 관리 
관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 변경하고 삭제하고 관리하여 [Azure Policy](../azure-policy/azure-policy-introduction.md) 및 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)에서 사용할 수 있는 계층 구조를 유지할 수 있습니다. 관리 그룹에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](management-groups-overview.md)을 참조하세요.

관리 그룹 기능은 공개 미리 보기에서 사용할 수 있습니다. 관리 그룹 사용을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인하거나 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) 또는 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)를 사용하여 관리 그룹을 관리할 수 있습니다.

관리 그룹을 변경하려면 관리 그룹에 대해 소유자 또는 참가자 역할이 있어야 합니다. 보유하고 있는 사용 권한을 보려면 관리 그룹을 선택하고 **IAM**을 선택합니다. RBAC 역할에 대해 자세히 알아보려면 [RBAC로 액세스 및 사용 권한 관리](../role-based-access-control/overview.md)를 참조하세요.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>관리 그룹의 이름 변경 
포털, PowerShell 또는 Azure CLI를 사용하여 관리 그룹의 이름을 변경할 수 있습니다.

### <a name="change-the-name-in-the-portal"></a>포털에서 이름 변경

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **관리 그룹**을 선택합니다.  
3. 이름을 바꾸려는 관리 그룹을 선택합니다. 
4. 페이지 맨 위에서 **그룹 이름 바꾸기** 옵션을 선택합니다. 

    ![그룹 이름 바꾸기](media/management-groups/detail_action_small.png)
5. 메뉴가 열릴 때 표시하려는 새 이름을 입력합니다.

    ![그룹 이름 바꾸기](media/management-groups/rename_context.png) 
4. **저장**을 선택합니다. 

### <a name="change-the-name-in-powershell"></a>PowerShell에서 이름 변경

표시 이름을 업데이트하려면 **Update-AzureRmManagementGroup**을 사용합니다. 예를 들어, "Contoso IT"에서 "Contoso Group"으로 관리 그룹 이름을 변경하려면 다음 명령을 실행합니다. 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Azure CLI에서 이름 변경

Azure CLI의 경우 update 명령을 사용합니다. 

```azure-cli
C:\> az account management-group update --group-name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>관리 그룹 삭제
관리 그룹을 삭제하려면 다음 요구 사항이 충족되어야 합니다.
1. 관리 그룹 아래에 자식 관리 그룹 또는 구독이 없습니다. 
    - 구독을 관리 그룹 외부로 이동하려면 [다른 관리 그룹으로 구독 이동](#Move-subscriptions-in-the-hierarchy)을 참조하세요. 
    - 관리 그룹을 다른 관리 그룹으로 이동하려면 [계층에서 관리 그룹 이동](#Move-management-groups-in-the-hierarchy)을 참조하세요. 
2. 관리 그룹에 대해 쓰기 권한이 있거나 소유자 또는 참가자 역할이 있습니다. 보유하고 있는 사용 권한을 보려면 관리 그룹을 선택하고 **IAM**을 선택합니다. RBAC 역할에 대해 자세히 알아보려면 [RBAC로 액세스 및 사용 권한 관리](../role-based-access-control/overview.md)를 참조하세요.  

### <a name="delete-in-the-portal"></a>포털에서 삭제

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **관리 그룹**을 선택합니다.  
3. 삭제하려는 관리 그룹을 선택합니다. 
    
    ![그룹 삭제](media/management-groups/delete.png)
4. **삭제**를 선택합니다. 
    - 아이콘이 사용되지 않도록 설정되면 아이콘 위로 마우스 선택기를 가져가면 이유가 표시됩니다. 
5. 관리 그룹을 삭제할 것인지 확인하는 창이 열립니다. 

    ![그룹 삭제](media/management-groups/delete_confirm.png) 
6. **예**를 선택합니다. 


### <a name="delete-in-powershell"></a>PowerShell에서 삭제

관리 그룹을 삭제하려면 PowerShell 내에서 **Remove-AzureRmManagementGroup** 명령을 사용합니다. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Azure CLI에서 삭제
Azure CLI에서 az account management-group delete 명령을 사용합니다. 

```azure-cli
C:\> az account management-group delete --group-name Contoso
```
---

## <a name="view-management-groups"></a>관리 그룹 보기
직접 또는 상속된 RBAC 역할이 있는 모든 관리 그룹을 볼 수 있습니다.  

### <a name="view-in-the-portal"></a>포털에서 보기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **관리 그룹**을 선택합니다. 
3. 관리 그룹 계층 페이지는 액세스 권한이 있는 모든 관리 그룹 및 구독을 탐색할 경우 로드됩니다. 그룹 이름을 선택하면 계층에서 한 수준 아래로 이동합니다. 탐색은 파일 탐색기처럼 작동합니다. 
    ![기본](media/management-groups/main.png)
4. 관리 그룹의 세부 정보를 보려면 관리 그룹의 제목 옆에 있는 **(세부 정보)** 링크를 선택합니다. 이 링크를 사용할 수 없으면 해당 관리 그룹을 볼 수 있는 권한이 없습니다.  

### <a name="view-in-powershell"></a>PowerShell에서 보기
모든 그룹을 검색하려면 Get-AzureRmManagementGroup 명령을 사용합니다.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
단일 관리 그룹의 정보를 보려면 -GroupName 매개 변수를 사용합니다.

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Azure CLI에서 보기
모든 그룹을 검색하려면 list 명령을 사용합니다.  

```azure-cli
az account management-group list
```
단일 관리 그룹의 정보를 보려면 show 명령을 사용합니다.

```azurepowershell-interactive
az account management-group show --group-name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>계층에서 구독 이동
관리 그룹을 만드는 한 가지 이유는 구독을 번들로 묶는 것입니다. 관리 그룹 및 구독만 다른 관리 그룹의 자식으로 만들 수 있습니다. 관리 그룹으로 이동되는 구독은 부모 관리 그룹에서 모든 사용자 액세스 및 정책을 상속합니다. 

구독을 이동하려면 다음과 같은 몇 가지 사용 권한이 있어야 합니다. 
- 자식 구독에 대한 "소유자" 역할
- 새 부모 관리 그룹에 대한 "소유자" 또는 "참가자" 역할 
- 이전 부모 관리 그룹에 대한 "소유자" 또는 "참가자" 역할
보유하고 있는 사용 권한을 보려면 관리 그룹을 선택하고 **IAM**을 선택합니다. RBAC 역할에 대해 자세히 알아보려면 [RBAC로 액세스 및 사용 권한 관리](../role-based-access-control/overview.md)를 참조하세요. 

### <a name="move-subscriptions-in-the-portal"></a>포털에서 구독 이동

**관리 그룹에 기존 구독 추가**
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **관리 그룹**을 선택합니다. 
3. 부모로 지정할 관리 그룹을 선택합니다.      
5. 페이지의 위쪽에서 **기존 항목 추가**를 선택합니다. 
6. 열리는 메뉴에서 **구독**에 해당하는 이동하려는 항목의 **리소스 종류**를 선택합니다.
7. 올바른 ID를 사용해서 목록의 구독을 선택합니다. 

    ![자식](media/management-groups/add_context_2.png)
8. “저장”을 선택합니다.

**관리 그룹에서 구독 제거**
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **관리 그룹**을 선택합니다. 
3. 현재 부모 항목인 관리 그룹을 선택합니다.  
4. 이동하려는 목록의 구독에 대해 행 끝에 있는 줄임표를 선택합니다.

    ![이동](media/management-groups/move_small.png)
5. **이동**을 선택합니다.
6. 열리는 메뉴에서 **부모 관리 그룹**을 선택합니다.

    ![이동](media/management-groups/move_small_context.png)
7. **저장**을 선택합니다.

### <a name="move-subscriptions-in-powershell"></a>PowerShell에서 구독 이동
PowerShell에서 구독을 이동하려면 Add-AzureRmManagementGroupSubscription 명령을 사용합니다.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

구독 및 관리 그룹 간의 링크를 제거하려면 Remove-AzureRmManagementGroupSubscription 명령을 사용합니다.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Azure CLI에서 구독 이동
CLI에서 구독을 이동하려면 add 명령을 사용합니다. 

```azure-cli
C:\> az account management-group add --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

관리 그룹에서 구독을 제거하려면 구독 제거 명령을 사용합니다.  

```azure-cli
C:\> az account management-group remove --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>계층 구조에서 관리 그룹 이동  
부모 관리 그룹을 이동할 경우 관리 그룹, 구독, 리소스 그룹 및 리소스를 포함하는 모든 자식 리소스가 부모와 함께 이동됩니다.   

### <a name="move-management-groups-in-the-portal"></a>포털에서 관리 그룹 이동

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **관리 그룹**을 선택합니다. 
3. 부모로 지정할 관리 그룹을 선택합니다.      
5. 페이지의 위쪽에서 **기존 항목 추가**를 선택합니다.
6. 열리는 메뉴에서 **관리 그룹**에 해당하는 이동하려는 항목의 **리소스 종류**를 선택합니다.
7. 올바른 ID 및 이름을 가진 관리 그룹을 선택합니다.

    ![이동](media/management-groups/add_context.png)
8. **저장**을 선택합니다.

### <a name="move-management-groups-in-powershell"></a>PowerShell에서 관리 그룹 이동
PowerShell에서 Update-AzureRmManagementGroup 명령을 사용하여 관리 그룹을 다른 그룹 아래로 이동합니다.  

```powershell
C:\> Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Azure CLI에서 관리 그룹 이동
Azure CLI에서 update 명령을 사용하여 관리 그룹을 이동합니다. 

```azure-cli
C:/> az account management-group udpate --group-name Contoso --parent-id "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>다음 단계 
관리 솔루션에 대해 자세히 알아보려면 다음을 참조하세요. 
- [Azure 관리 그룹으로 리소스 구성](management-groups-overview.md)
- [관리 그룹을 만들어 Azure 리소스 구성](management-groups-create.md)
- [Azure PowerShell 모듈 설치](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [REST API 사양 검토](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Azure CLI 확장 설치](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
