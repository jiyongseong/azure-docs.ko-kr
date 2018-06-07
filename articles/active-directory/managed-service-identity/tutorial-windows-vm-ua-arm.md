---
title: Windows VM 사용자 할당 MSI를 사용하여 Azure Resource Manager에 액세스
description: Windows VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 설명하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: a2225409e4cb50d91c09207ee70b76df12925192
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301214"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>자습서: Windows VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Resource Manager에 액세스

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 자습서에서는 사용자 할당 ID를 만들어서, Windows VM(가상 머신)에 할당한 다음, 이 ID를 사용하여 Azure Resource Manager API에 액세스하는 방법을 설명합니다. 관리 서비스 ID는 Azure에서 자동으로 관리됩니다. 그러면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Windows VM 만들기 
> * 사용자 할당 ID 만들기
> * 사용자 할당 ID를 Windows VM에 할당
> * 사용자 할당 ID에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 
> * 사용자 할당 ID를 사용하여 액세스 토큰을 가져와서 Azure Resource Manager를 호출하는 데 사용 
> * Resource Group의 속성 읽기

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요](overview.md) 섹션을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 자습서에서 필요한 리소스 만들기 및 역할 관리 단계를 수행하려면 적절한 범위(사용자 구독 또는 리소스 그룹)에서 계정에 "소유자" 권한이 필요합니다. 역할 할당에 관한 도움이 필요한 경우 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](/azure/role-based-access-control/role-assignments-portal)를 참조하세요.

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.7 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기

다음 예제에서는 *EastUS* 지역에 *myResourceGroupVM*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

리소스 그룹을 만든 후에 Windows VM을 만듭니다.

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 가상 머신의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```
[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 머신을 만듭니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

사용자 할당 ID는 독립 실행형 Azure 리소스로 생성됩니다. [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity)를 사용하면 하나 이상의 Azure 서비스 인스턴스를 할당할 수 있는 Azure AD 테넌트에 ID가 만들어집니다.

> [!IMPORTANT]
> 사용자 할당 ID 만들기는 영숫자와 하이픈(0-9 또는 a-z 또는 A-Z 또는 -) 문자만 지원합니다. 또한 VM/VMSS에 대한 할당이 제대로 작동하려면 이름의 길이가 24자로 제한되어야 합니다. 업데이트를 다시 확인하십시오. 자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

응답에는 다음 예제와 같이 생성된 사용자 할당 ID에 대한 세부 정보가 포함됩니다. 다음 단계에서 사용되므로 사용자 할당 ID의 `Id` 값을 적어둡니다.

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>사용자 할당 ID를 Windows VM에 할당

시스템 할당 ID는 여러 Azure 리소스에 있는 클라이언트에 사용될 수 있습니다. 다음 명령을 사용하여 사용자 할당 ID를 단일 VM에 할당합니다. 이전 단계에서 반환된 `Id` 속성을 `-IdentityID` 매개 변수에 사용합니다.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>사용자 할당 MSI에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 

MSI(관리 서비스 ID)는 Azure AD 인증을 지원하는 리소스 API를 인증하는 액세스 토큰을 요청하기 위해 코드에 사용할 수 있는 ID를 제공합니다. 이 자습서에서 코드는 Azure Resource Manager API에 액세스합니다. 

코드가 API에 액세스할 수 있으려면 그 전에 Azure Resource Manager의 리소스에 ID 액세스 권한을 부여해야 합니다. 이 경우에는 VM이 포함된 리소스 그룹입니다. `<SUBSCRIPTION ID>`의 값을 환경에 적합하게 업데이트합니다.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

응답에는 다음 예제와 같이 만든 역할 할당에 대한 세부 정보가 포함됩니다.

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>VM의 ID를 사용하여 액세스 토큰을 가져온 다음 Resource Manager를 호출하는 데 사용 

자습서의 나머지 부분에서는 앞서 만든 VM에서 작업합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

2. Portal에서 **Virtual Machines** -> Windows 가상 머신으로 이동한 다음, **개요**에서 **연결**을 클릭합니다.

3. Windows VM을 만들 때 사용한 **사용자 이름**과 **암호**를 입력합니다.

4. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 **PowerShell**을 엽니다.

5. PowerShell의 `Invoke-WebRequest`를 사용하여 로컬 MSI 엔드포인트에 대한 요청을 수행해 Azure Resource Manager용 액세스 토큰을 가져옵니다.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Resource Group의 속성 읽기

이전 단계에서 검색한 액세스 토큰을 사용하여 Azure Resource Manager에 액세스하고, 사용자 할당 ID 액세스 권한을 부여한 리소스 그룹의 속성을 읽습니다. <SUBSCRIPTION ID>를 환경의 구독 ID로 바꿉니다.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
응답은 다음 예제와 비슷하게 특정 리소스 그룹 정보를 포함합니다.

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.
