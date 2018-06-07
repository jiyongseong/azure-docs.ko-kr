---
title: DNS 영역 및 레코드 보호 | Microsoft 문서
description: Microsoft Azure DNS에서 DNS 영역 및 레코드 집합을 보호하는 방법
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: kumud
ms.openlocfilehash: d13556eb274990eadf58070c119a3e9960b90699
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS 영역 및 레코드를 보호하는 방법

DNS 영역 및 레코드는 중요한 리소스입니다. DNS 영역 또는 단일 DNS 레코드만 삭제해도 전체 서비스 중단이 발생할 수 있습니다.  따라서 중요 DNS 영역 및 레코드가 무단 또는 실수로 변경되지 않도록 보호하는 것이 중요합니다.

이 문서는 Azure DNS를 통해 DNS 영역 및 레코드를 이러한 변경으로부터 보호하는 방식에 대해 설명합니다.  Azure Resource Manager에서 제공하는 두 가지 강력한 보안 기능, 즉, [역할 기반 액세스 제어](../role-based-access-control/overview.md)와 [리소스 잠금](../azure-resource-manager/resource-group-lock-resources.md)을 적용합니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure RBAC(Role-Based Access Control)는 Azure 사용자, 그룹, 리소스에 대해 정확한 액세스 관리를 지원합니다. RBAC를 사용하여 사용자가 해당 작업을 수행하는 데 필요한 만큼의 액세스 권한을 정확하게 부여할 수 있습니다. RBAC를 사용하여 액세스를 관리하는 방법에 대한 자세한 내용은 [역할 기반 Access Control이란](../role-based-access-control/overview.md)을 참조하세요.

### <a name="the-dns-zone-contributor-role"></a>'DNS 영역 참가자' 역할

'DNS 영역 참가자' 역할은 DNS 리소스 관리를 위해 Azure에서 제공하는 기본 역할입니다.  DNS 영역 참가자 권한을 사용자 또는 그룹에 할당할 경우 해당 그룹에서 DNS 리소스를 관리할 수 있지만 다른 유형의 리소스는 관리할 수 없습니다.

예를 들어 'myzones' 리소스 그룹에 Contoso Corporation의 영역 5개가 있다고 가정하겠습니다. 해당 리소스 그룹에'DNS 영역 참가자' DNS 관리자 권한을 부여할 경우 해당 DNS 영역을 완전히 관리할 수 있습니다. 또한 불필요한 권한을 부여하지 않습니다. 예를 들어 DNS 관리자는 Virtual Machines를 만들거나 중지할 수 없습니다.

RBAC 권한을 할당하는 가장 간단한 방법은 [Azure Portal을 사용](../role-based-access-control/role-assignments-portal.md)하는 것입니다.  리소스 그룹의 [Access Control(IAM)] 블레이드를 열고 [추가]를 클릭한 다음 [DNS 영역 참가자] 역할을 선택하고 권한을 부여할 사용자 또는 그룹을 선택합니다.

![Azure Portal을 통한 리소스 그룹 수준 RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>영역 수준 RBAC

구독, 리소스 그룹 또는 개별 리소스에 Azure RBAC 규칙을 적용할 수 있습니다. Azure DNS의 경우 해당 리소스는 개별 DNS 영역이 될 수 있으며 개별 레코드 집합도 가능합니다.

예를 들어 'myzones' 리소스 그룹에 'contoso.com' 영역과 각 고객 계정에 대해 CNAME 레코드가 만들어져 있는 'customers.contoso.com' 하위 영역이 있다고 가정합니다.  이러한 CNAME 레코드를 관리하는 데 사용하는 계정은 'customers.contoso.com' 영역의 레코드만 만들 수 있는 권한을 부여받아야 하며 다른 영역에는 액세스할 수 없습니다.

영역 수준 RBAC 권한은 Azure Portal을 통해 부여할 수 있습니다.  영역의 [Access Control(IAM)] 블레이드를 열고 [추가]를 클릭한 다음 [DNS 영역 참가자] 역할을 선택하고 권한을 부여할 필요가 있는 사용자 또는 그룹을 선택합니다.

![Azure Portal을 통한 DNS 영역 수준 RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>레코드 집합 수준 RBAC

한 단계 더 나아갈 수 있습니다. 'contoso.com' 영역의 루트 수준에서 MX 및 TXT 레코드에 액세스해야 하는 Contoso Corporation의 메일 관리자를 고려해 보세요.  이 메일 관리자는 다른 MX 레코드, TXT 레코드 또는 다른 유형의 레코드에 액세스 할 필요가 없습니다.  Azure DNS에서는 레코드 집합 수준에서 메일 관리자가 액세스해야 하는 레코드에 정확하게 권한을 할당할 수 있습니다.  메일 관리자는 자신에게 필요한 관리 권한만 정확하게 부여받으며 다른 사항을 변경할 수 없습니다.

Azure Portal에서 레코드 집합 블레이드의 [사용자] 단추를 사용하여 레코드 집합 수준 RBAC 권한을 구성할 수 있습니다.

![Azure Portal을 통한 레코드 집합 수준 RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

레코드 집합 수준 RBAC 권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>사용자 지정 역할

기본 제공 'DNS 영역 참가자' 역할을 사용하면 DNS 리소스를 완벽히 관리할 수 있습니다. 또한 사용자 지정 Azure 역할을 구축하여 더욱 정밀한 관리를 제공할 수도 있습니다.

'customers.contoso.com' 영역에서 Contoso Corporation 고객 계정마다 CNAME 레코드를 만드는 예를 다시 고려해 보세요.  이러한 CNAME을 관리하는 데 사용한 계정은 CNAME 레코드만 관리할 권한을 부여받아야 합니다.  그런 다음 다른 유형의 레코드(예: MX 레코드 변경)를 수정하거나 영역 삭제와 같은 영역 수준 작업을 수행할 수 없습니다.

다음 예는 CNAME 레코드 관리에 대한 사용자 지정 역할 정의를 보여줍니다.

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

작업 속성은 다음과 같은 DNS별 권한을 정의합니다.

* `Microsoft.Network/dnsZones/CNAME/*`은 CNAME 레코드에 대한 모든 권한을 부여합니다.
* `Microsoft.Network/dnsZones/read`는 DNS 영역을 읽을 권한을 부여하지만 수정 권한은 부여하지 않아 CNAME이 생성되는 영역을 확인할 수 있습니다.

나머지 작업은 [DNS 영역 참가자의 기본 역할](../role-based-access-control/built-in-roles.md#dns-zone-contributor)에서 복사됩니다.

> [!NOTE]
> 레코드 집합 삭제를 방지하기 위해 사용자 지정 RBAC 역할을 사용하면서 업데이트를 허용하는 것은 효과적인 관리가 아닙니다. 이 방식에서는 레코드 집합을 삭제할 수 없지만 수정은 방지할 수 없습니다.  허용되는 수정 작업에는 레코드 집합을 '비우기' 위해 레코드를 모두 제거하는 작업을 포함하여 레코드 집합에서 레코드를 추가 및 제거하는 작업이 포함됩니다. 이 경우 DNS 확인 관점에서 레코드 집합을 삭제하는 것과 같은 효과를 가집니다.

사용자 지정 역할 정의는 현재 Azure Portal을 통해 정의할 수 없습니다. 이 역할 정의를 기준으로 하는 사용자 지정 역할은 Azure PowerShell을 사용하여 만들 수 있습니다.

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Azure CLI를 통해 만들 수도 있습니다.

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

그런 다음 이 문서의 앞부분에서 설명한 대로 역할을 기본 역할과 동일한 방식으로 할당할 수 있습니다.

사용자 지정 역할을 만들고 관리하고 할당하는 방법은 [Azure RBAC에서 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

## <a name="resource-locks"></a>리소스 잠금

RBAC 외에도 Azure Resource Manager는 리소스를 '잠그는' 기능과 같은 다른 유형의 보안 관리 기능을 지원합니다. 특정 사용자와 그룹의 작업을 관리할 수 있는 RBAC 규칙의 경우 리소스 잠금이 리소스에 적용되며 모든 사용자와 역할에 대해 유효합니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](../azure-resource-manager/resource-group-lock-resources.md)를 참조하세요.

리소스 잠금은 **DoNotDelete** 및 **ReadOnly**의 두 가지 유형이 있습니다. 이러한 잠금은 DNS 영역 또는 개별 레코드 집합에 적용할 수 있습니다.  다음 섹션에서는 몇 가지 일반적인 시나리오와 리소스 잠금을 사용하여 지원하는 방법에 대해 설명합니다.

### <a name="protecting-against-all-changes"></a>모든 변경으로부터 보호

모든 변경을 방지하려면 영역에 ReadOnly 잠금을 적용합니다.  이 경우 새 레코드 집합을 만들 수 없으며 기존 레코드 집합이 수정 또는 삭제되지 않습니다.

Azure Portal을 통해 영역 수준의 리소스 잠금을 만들 수 있습니다.  DNS 영역 블레이드에서 [잠금]을 클릭한 다음 [추가]를 클릭합니다.

![Azure Portal을 통한 영역 수준 리소스 잠금](./media/dns-protect-zones-recordsets/locks1.png)

또한 Azure PowerShell을 통해 영역 수준 리소스 잠금을 만들 수 있습니다.

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

현재는 Azure CLI를 통해 Azure 리소스 잠금을 구성할 수 없습니다.

### <a name="protecting-individual-records"></a>개별 레코드 보호

기존 DNS 레코드 집합이 수정되지 않도록 하려면 레코드 집합에 ReadOnly 잠금을 적용합니다.

> [!NOTE]
> 레코드 집합에 DoNotDelete 잠금을 적용하는 것은 효과적인 관리 방법이 아닙니다. 이 방식에서는 레코드 집합을 삭제할 수 없지만 수정은 방지할 수 없습니다.  허용되는 수정 작업에는 레코드 집합을 '비우기' 위해 레코드를 모두 제거하는 작업을 포함하여 레코드 집합에서 레코드를 추가 및 제거하는 작업이 포함됩니다. 이 경우 DNS 확인 관점에서 레코드 집합을 삭제하는 것과 같은 효과를 가집니다.

레코드 집합 수준 리소스 잠금은 현재 Azure PowerShell을 사용해야 구성할 수 있으며  Azure Portal 또는 Azure CLI에서 지원되지 않습니다.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>영역 삭제로부터 보호

Azure DNS에서 영역이 삭제되면 해당 영역의 모든 레코드 집합도 삭제됩니다.  이 작업은 취소할 수 없습니다.  중요 영역을 실수로 삭제할 경우 비즈니스에 심각한 영향을 미칩니다.  따라서 실수로 영역을 삭제하지 않도록 보호하는 것이 매우 중요합니다.

영역에 DoNotDelete 잠금을 적용할 경우 영역이 삭제되지 않습니다.  하지만 하위 리소스에서 잠금을 상속하기 때문에 영역에 있는 레코드 집합을 삭제할 수 없게 되며 이러한 상황은 바람직하지 않을 수 있습니다.  또한 위 참고 사항에서 설명한 바와 같이 기존 레코드 집합에서 여전히 레코드를 제거할 수 있기 때문에 비효과적입니다.

대신 영역의 레코드 집합(예: SOA 레코드 집합)에 DoNotDelete 잠금을 적용해 보십시오.  영역을 삭제하면 레코드 집합도 삭제되기 때문에 이 잠금을 적용하면 영역이 삭제되지 않도록 보호하는 동시에 영역 내 레코드 집합을 자유롭게 수정할 수 있습니다. 영역을 삭제하려고 시도할 경우 Azure Resource Manager에서 SOA 레코드 집합도 삭제되는 것을 감지하고 SOA가 잠기므로 호출을 차단합니다.  삭제되는 레코드 집합이 없습니다.

다음 PowerShell 명령은 지정된 영역의 SOA 레코드에 대해 DoNotDelete 잠금을 만듭니다.

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

실수로 영역을 삭제하지 못하게 하는 또 다른 방법으로 사용자 지정 역할을 사용하여 영역 관리에 사용되는 운영자 및 서비스 계정에 영역 삭제 권한이 없는지 확인합니다. 영역을 삭제해야 하는 경우 2단계 삭제 작업을 수행하는데, 먼저 영역 범위에서 잘못된 영역을 삭제하지 않도록 하는 영역 삭제 권한을 부여하고, 다음으로 영역을 삭제하면 됩니다.

이 두 번째 방법은 잠금을 만들지 않고도 해당 계정에서 액세스하는 모든 영역에서 작동한다는 장점이 있습니다. 구독 소유자와 같이 영역 삭제 권한이 있는 계정은 여전히 실수로 중요한 영역을 삭제할 수 있다는 단점이 있습니다.

DNS 영역 보호에 대한 심층적 방어 접근 방식으로서 리소스 잠금과 사용자 지정 역할을 동시에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* RBAC를 사용하는 방법은 [Azure Portal에서 액세스 관리 시작](../role-based-access-control/overview.md)을 참조하세요.
* 리소스 잠금에 대한 자세한 내용은[ Azure Resource Manager를 사용하여 리소스 잠그기](../azure-resource-manager/resource-group-lock-resources.md)를 참조하세요.

