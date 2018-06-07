---
title: Azure RBAC의 역할 정의 이해 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어)의 역할 정의 및 Azure에서 리소스에 대해 세분화된 액세스를 관리하기 위해 사용자 지정 역할을 정의하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161816"
---
# <a name="understand-role-definitions"></a>역할 정의 이해

역할이 작동하는 방식을 이해하려거나 사용자 고유의 [사용자 지정 역할](custom-roles.md)을 만드는 경우 역할을 정의하는 방법을 이해하는 데 도움이 됩니다. 이 문서에서는 역할 정의에 대한 자세한 내용을 설명하고 몇 가지 예제를 제공합니다.

## <a name="role-definition-structure"></a>역할 정의 구조

*역할 정의*는 권한의 모음입니다. 때로는 *역할*이라고 합니다. 역할 정의에는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업이 나열됩니다. 또한 수행할 수 없는 작업도 나열할 수 있습니다. 역할 정의의 구조는 다음과 같습니다.

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

작업은 다음과 같은 형식의 문자열로 지정됩니다.

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

작업 문자열의 `{action}` 부분은 리소스 종류에서 수행할 수 있는 작업의 유형을 지정합니다. 예를 들어 `{action}`에 표시되는 부분 문자열은 다음과 같습니다.

| 작업 부분 문자열    | 설명         |
| ------------------- | ------------------- |
| `*` | 와일드카드 문자는 문자열과 일치하는 모든 작업에 대한 액세스 권한을 부여합니다. |
| `read` | 읽기 작업(GET)을 사용하도록 설정합니다. |
| `write` | 쓰기 작업(PUT, POST 및 PATCH)을 사용하도록 설정합니다. |
| `delete` | 삭제 작업(DELETE)을 사용하도록 설정합니다. |

JSON 형식의 [기여자](built-in-roles.md#contributor) 역할 정의가 있습니다. `actions`에 포함된 와일드카드(`*`) 작업은 이 역할에 할당된 주체가 모든 작업을 수행할 수 있음, 즉 모든 항목을 관리할 수 있음을 나타냅니다. 여기에는 나중에 Azure에서 새 리소스 종류를 추가함에 따라 정의되는 작업이 포함됩니다. `notActions`에 속한 작업은 `actions`에서 제외됩니다. [기여자](built-in-roles.md#contributor) 역할의 경우 `notActions`는 리소스에 대한 액세스를 관리하고 할당하는 이 역할의 기능을 제거합니다.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>관리 작업

관리 작업에 대한 역할 기반 액세스 제어는 역할 정의의 `actions` 및 `notActions` 섹션에 지정됩니다. Azure의 관리 작업에 대한 몇 가지 예는 다음과 같습니다.

- 저장소 계정에 대한 액세스 관리
- Blob 컨테이너 만들기, 업데이트 또는 삭제
- 리소스 그룹 및 해당하는 모든 리소스 삭제

관리 액세스 권한은 데이터에 상속되지 않습니다. 이렇게 분리하면 와일드카드(`*`)가 있는 역할에서 데이터에 무제한으로 액세스할 수 없게 됩니다. 예를 들어 구독에 [읽기 권한자](built-in-roles.md#reader) 역할이 있는 사용자는 저장소 계정을 볼 수 있지만 기본 데이터는 볼 수 없습니다.

## <a name="actions"></a>actions

`actions` 권한은 역할에서 액세스 권한을 부여하는 관리 작업을 지정합니다. Azure 리소스 공급자의 보안 개체 작업을 식별하는 작업 문자열 모음입니다. `actions`에서 사용할 수 있는 관리 작업의 몇 가지 예제는 다음과 같습니다.

| 작업 문자열    | 설명         |
| ------------------- | ------------------- |
| `*/read` | 모든 Azure 리소스 공급자에 있는 모든 리소스 종류의 읽기 작업에 대한 액세스 권한을 부여합니다.|
| `Microsoft.Compute/*` | Microsoft.Compute 리소스 공급자에 있는 모든 리소스 종류의 모든 작업에 대한 액세스 권한을 부여합니다.|
| `Microsoft.Network/*/read` | Microsoft.Network 리소스 공급자에 있는 모든 리소스 종류의 읽기 작업에 대한 액세스 권한을 부여합니다.|
| `Microsoft.Compute/virtualMachines/*` | 가상 머신 및 해당 자식 리소스 종류의 모든 작업에 대한 액세스 권한을 부여합니다.|
| `microsoft.web/sites/restart/Action` | 웹앱을 다시 시작하기 위한 액세스 권한을 부여합니다.|

## <a name="notactions"></a>notActions

`notActions` 권한은 허용된 `actions`에서 제외되는 관리 작업을 지정합니다. 제한된 작업을 제외하여 허용하려는 작업 집합을 더 쉽게 정의하는 경우 `notActions` 권한을 사용합니다. 역할(유효 사용 권한)로 부여되는 액세스 권한은 `actions` 작업에서 `notActions` 작업을 제외하여 계산됩니다.

> [!NOTE]
> 사용자에게 `notActions`에서 작업을 제외하는 역할이 할당되고 동일한 작업에 대한 액세스 권한을 부여하는 두 번째 역할이 할당된 경우 사용자는 해당 작업을 수행할 수 있습니다. `notActions`는 거부 규칙이 아니며, 특정 작업을 제외해야 할 경우 허용되는 작업 집합을 만드는 편리한 방법일 뿐입니다.
>

## <a name="assignablescopes"></a>assignableScopes

`assignableScopes` 섹션에서는 역할을 할당할 수 있는 범위(관리 그룹(현재 미리 보기 상태임), 구독, 리소스 그룹 또는 리소스)를 지정합니다. 역할이 필요한 구독 또는 리소스 그룹에만 역할을 할당할 수 있도록 하며, 나머지 구독 또는 리소스 그룹에 대해 혼란스러운 사용자 환경이 아닙니다. 하나 이상의 관리 그룹, 구독, 리소스 그룹 또는 리소스 ID를 사용해야 합니다.

기본 제공 역할에는 루트 범위(`"/"`)로 설정된 `assignableScopes`가 있습니다. 루트 범위는 모든 범위에서 역할을 할당에 사용할 수 있음을 나타냅니다. 사용자 지정 역할에는 루트 범위를 사용할 수 없습니다. 시도하면 권한 부여 오류가 발생합니다.

유효한 할당 가능한 범위의 예는 다음과 같습니다.

| 시나리오 | 예 |
|----------|---------|
| 역할은 단일 구독에 할당할 수 있습니다. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| 역할은 두 개의 구독에 할당할 수 있습니다. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| 역할은 네트워크 리소스 그룹에만 할당할 수 있습니다. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| 역할은 모든 범위에 할당할 수 있습니다. | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes 및 사용자 지정 역할

사용자 지정 역할에 대한 `assignableScopes` 섹션에서는 사용자 지정 역할을 만들거나 삭제하거나 수정하거나 표시할 수 있는 사용자를 제어합니다.

| Task | 작업 | 설명 |
| --- | --- | --- |
| 사용자 지정 역할 만들기/삭제 | `Microsoft.Authorization/ roleDefinition/write` | 사용자 지정 역할의 모든 `assignableScopes`에 이 작업이 부여된 사용자는 해당 범위에서 사용할 사용자 지정 역할을 만들거나 삭제할 수 있습니다. 예를 들어 구독, 리소스 그룹 및 리소스의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)가 있습니다. |
| 사용자 지정 역할 수정 | `Microsoft.Authorization/ roleDefinition/write` | 사용자 지정 역할의 모든 `assignableScopes`에 이 작업이 부여된 사용자는 해당 범위에의 사용자 지정 역할을 수정할 수 있습니다. 예를 들어 구독, 리소스 그룹 및 리소스의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)가 있습니다. |
| 사용자 지정 역할 보기 | `Microsoft.Authorization/ roleDefinition/read` | 범위에서 이 작업이 부여된 사용자는 해당 범위에서 할당에 사용할 수 있는 사용자 지정 역할을 볼 수 있습니다. 모든 기본 제공 역할을 통해 사용자 지정 역할을 할당할 수 있습니다. |

## <a name="role-definition-examples"></a>역할 정의 예제

다음 예제에서는 Azure CLI를 사용하여 표시되는 [읽기 권한자](built-in-roles.md#reader) 역할 정의를 보여 줍니다.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

다음 예제에서는 Azure PowerShell을 사용하여 표시되는 가상 머신을 모니터링하고 다시 시작하기 위한 사용자 지정 역할을 보여 줍니다.

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>참고 항목

* [기본 제공 역할](built-in-roles.md)
* [사용자 지정 역할](custom-roles.md)
* [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)
