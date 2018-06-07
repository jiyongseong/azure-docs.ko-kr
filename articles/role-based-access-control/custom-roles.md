---
title: Azure RBAC에 대한 사용자 지정 역할 만들기 | Microsoft Docs
description: Azure 구독에 보다 정확한 ID 관리를 위해 Azure 역할 기반 Access Control로 사용자 지정 역할을 정의하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e2ea46ea1a6b5bd3f50d4d4c15492c16c5241c0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161059"
---
# <a name="create-custom-roles-in-azure"></a>Azure에서 사용자 지정 역할 만들기

[기본 제공 역할](built-in-roles.md)이 액세스 요구 사항을 충족하지 못하는 경우 자체적인 사용자 지정 역할을 만들 수 있습니다. 기본 제공 역할과 마찬가지로 구독, 리소스 그룹 및 리소스 범위에서 사용자 지정 역할을 사용자, 그룹 및 서비스 주체에 할당할 수 있습니다. 사용자 지정 역할은 Azure AD(Active Directory) 테넌트에 저장되며 구독에서 공유할 수 있습니다. Azure PowerShell, Azure CLI 또는 REST API를 사용하여 사용자 지정 역할을 만들 수 있습니다. 이 문서에서는 PowerShell 및 Azure CLI를 사용하여 사용자 지정 역할 만들기를 시작하는 방법의 예를 설명합니다.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>PowerShell을 사용하여 지원 요청을 여는 사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 기본 제공 역할로 시작하여 역할을 편집한 다음, 새 역할을 만들 수 있습니다. 이 예제에서는 기본 제공 [Reader](built-in-roles.md#reader) 역할을 사용자 지정하여 "Reader 지원 티켓 액세스 수준"이라는 사용자 지정 역할을 만듭니다. 이를 통해 사용자는 구독에서 모든 것을 볼 수 있고 지원 요청을 열 수도 있습니다.

> [!NOTE]
> 사용자가 지원 요청을 열 수 있는 두 가지 기본 제공 역할은 [소유자](built-in-roles.md#owner) 및 [기여자](built-in-roles.md#contributor)입니다. 사용자가 지원 요청을 열 수 있으려면 구독 범위에서 역할을 할당 받아야 합니다. 모든 지원 요청은 Azure 구독을 기반으로 만들어지기 때문입니다.

PowerShell에서는 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 명령을 사용하여 [reader](built-in-roles.md#reader) 역할을 JSON 형식으로 내보냅니다.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

다음은 [Reader](built-in-roles.md#reader) 역할에 대한 JSON 출력을 보여줍니다. 일반적인 역할은 `Actions`, `NotActions`, `AssignableScopes`의 세 가지 주요 섹션으로 구성됩니다. `Actions` 섹션에는 역할에 허용된 모든 작업이 나열됩니다. `Actions`에서 작업을 제외하려면 작업을 `NotActions`에 추가합니다. 유효 권한은 `Actions` 작업에서 `NotActions` 작업을 제외하여 계산됩니다.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

그런 다음, JSON 출력을 편집하여 사용자 지정 역할을 만듭니다. 이 예에서는 지원 티켓을 만들려면 `Microsoft.Support/*` 작업이 추가되어야 합니다. 각 작업은 리소스 공급자에서 제공됩니다. 리소스 공급자에 대한 작업 목록을 가져오려면 [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) 명령을 사용하거나 [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)을 참조하세요.

역할이 사용되는 경우 명시적인 구독 ID가 포함되어 있어야 합니다. 구독 ID가 `AssignableScopes` 아래에 나열됩니다. 나열되지 않으면 역할을 구독으로 가져올 수 없습니다.

마지막으로, 이 역할을 사용자 지정 역할로 지정하기 위해 `IsCustom` 속성을 `true`로 설정해야 합니다.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

새 사용자 지정 역할을 만들려면 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 명령을 사용하여 업데이트된 JSON 역할 정의 파일을 제공합니다.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

[New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 명령을 실행하면 새 사용자 지정 역할을 Azure Portal에서 사용할 수 있으며 사용자에게 할당할 수 있습니다.

![Azure Portal에서 가져온 사용자 지정 역할의 스크린샷](./media/custom-roles/18.png)

![가져온 사용자 지정 역할을 동일한 디렉터리의 사용자에게 할당하는 스크린샷](./media/custom-roles/19.png)

![가져온 사용자 지정 역할에 대한 사용 권한 스크린샷](./media/custom-roles/20.png)

이 사용자 지정 역할이 있는 사용자는 새로운 지원 요청을 만들 수 있습니다.

![지원 요청을 만드는 사용자 지정 역할의 스크린샷](./media/custom-roles/21.png)

이 사용자 지정 역할이 있는 사용자는 VM 만들기 또는 리소스 그룹 만들기와 같은 다른 작업을 수행할 수 없습니다.

![VM을 만들 수 없는 사용자 지정 역할의 스크린샷](./media/custom-roles/22.png)

![새 RG를 만들 수 없는 사용자 지정 역할의 스크린샷](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Azure CLI를 사용하여 지원 요청을 여는 사용자 지정 역할 만들기

Azure CLI를 사용하여 사용자 지정 역할을 만드는 단계는 JSON 출력이 다른 것을 제외하고 PowerShell을 사용하는 것과 비슷합니다.

이 예제에서는 기본 제공 [reader](built-in-roles.md#reader) 역할을 사용하여 시작할 수 있습니다. [Reader](built-in-roles.md#reader) 역할의 작업을 나열하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list) 명령을 나열합니다.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

다음 형식으로 JSON 파일을 만듭니다. 이 사용자가 reader로 머무는 동안 지원 요청을 열 수 있도록 `Microsoft.Support/*` 작업이 `Actions` 섹션에 추가되었습니다. `AssignableScopes` 섹션에서 이 역할이 사용되는 경우 구독 ID를 추가해야 합니다.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

새 사용자 지정 역할을 만들려면 [az role definition create](/cli/azure/role/definition#az_role_definition_create) 명령을 사용합니다.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

이제 Azure Portal에서 새 사용자 지정 역할을 사용할 수 있고 이 역할을 사용하는 프로세스는 이전의 PowerShell 섹션과 동일합니다.

![CLI 1.0을 사용하여 만든 사용자 지정 역할의 Azure Portal 스크린샷](./media/custom-roles/26.png)


## <a name="see-also"></a>참고 항목
- [역할 정의 이해](role-definitions.md)
- [Azure PowerShell을 사용하여 역할 기반 액세스 제어 관리](role-assignments-powershell.md)
- [Azure CLI를 사용하여 역할 기반 액세스 제어 관리](role-assignments-cli.md)
- [REST API를 사용하여 역할 기반 액세스 제어 관리](role-assignments-rest.md)
