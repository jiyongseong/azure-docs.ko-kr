---
title: Azure Active Directory에서 전역 관리자에 대한 액세스 권한 상승 | Microsoft Docs
description: Azure Portal 또는 REST API를 사용하여 Azure Active Directory에서 전역 관리자에 대한 액세스 권한을 상승시키는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: b671ff6b473093e59bce18c7bf98b32e9849bbb0
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077210"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Azure Active Directory에서 전역 관리자에 대한 액세스 권한 상승

사용자가 Azure AD(Azure Active Directory)에서 [전역 관리자](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator)인 경우 다음을 수행하려는 경우가 있을 수 있습니다.

- 사용자가 액세스 권한을 상실할 때 Azure 구독에 대한 액세스 권한 다시 얻기
- 다른 사용자 또는 사용자 자신에게 Azure 구독에 대한 액세스 권한 부여
- 조직에서 모든 Azure 구독 확인
- 자동화 앱(예: 송장 또는 감사 앱)이 모든 Azure 구독에 액세스하도록 허용

기본적으로 Azure AD 관리자 역할 및 Azure RBAC(역할 기반 액세스 제어) 역할은 Azure AD 및 Azure를 포함하지 않습니다. 그러나 사용자가 Azure AD에서 전역 관리자인 경우 Azure 구독 및 관리 그룹을 관리하는 액세스 권한을 상승시킬 수 있습니다. 사용자가 액세스 권한을 상승시킬 때 특정 테넌트에 대한 모든 구독에서 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 역할(RBAC 역할)을 부여합니다. 사용자 액세스 관리자 역할을 사용하면 사용자가 다른 사용자에게 루트 범위(`/`)의 Azure 리소스에 대한 액세스 권한을 부여할 수 있습니다.

이러한 권한 상승은 임시로 필요할 때에만 수행되어야 합니다.

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Azure Portal을 사용하여 전역 관리자에 대한 액세스 권한 상승

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. 탐색 목록에서 **Azure Active Directory**를 클릭한 다음, **속성**을 클릭합니다.

   ![Azure AD 속성 - 스크린샷](./media/elevate-access-global-admin/aad-properties.png)

1. **전역 관리자는 Azure 구독 및 관리 그룹을 관리할 수 있습니다.** 에서 스위치를 **예**로 설정합니다.

   ![전역 관리자는 Azure 구독 및 관리 그룹을 관리할 수 있습니다. - 스크린샷](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   스위치를 **예**로 설정하면 전역 관리자 계정(현재 로그인한 사용자)이 루트 범위(`/`)에서 Azure RBAC의 사용자 액세스 관리자 역할에 추가됩니다. 여기에서는 Azure AD 테넌트와 연결된 모든 Azure 구독에서 보고 보고할 액세스할 수 있는 권한을 부여합니다.

   스위치를 **아니요**로 설정하면 전역 관리자 계정(현재 로그인한 사용자)이 Azure RBAC의 사용자 액세스 관리자 역할에서 제거됩니다. Azure AD 테넌트와 연결된 모든 Azure 구독을 볼 수 없습니다. 또한 액세스 권한이 부여된 Azure 구독만을 보고 관리할 수 있습니다.

1. **Save**를 클릭하여 설정을 저장합니다.

   이 설정은 전역 속성이 아니며 현재 로그인된 사용자에게만 적용됩니다.

1. 권한이 상승된 액세스를 확인하는 데 필요한 작업을 수행합니다. 작업이 완료되면 스위치를 다시 **아니요**로 설정합니다.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>PowerShell을 사용하여 루트 범위(/)에서 역할 할당 나열

루트 범위(`/`)에서 사용자에 대한 사용자 액세스 관리자 역할 할당을 나열하려면 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) 명령을 사용합니다.

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

## <a name="delete-a-role-assignment-at-the-root-scope--using-powershell"></a>PowerShell을 사용하여 루트 범위(/)에서 역할 할당 삭제

루트 범위(`/`)에서 사용자에 대한 사용자 액세스 관리자 역할 할당을 삭제하려면 [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) 명령을 사용합니다.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>REST API를 사용하여 전역 관리자에 대한 액세스 권한 상승

다음과 같은 기본 단계를 사용하여 REST API를 사용하는 전역 관리자에 대한 액세스 권한을 상승시킵니다.

1. REST를 사용하여 `elevateAccess`를 호출하면 루트 범위(`/`)에서 사용자 액세스 관리자 역할 권한을 부여합니다.

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. 모든 범위의 모든 역할을 할당하는 [역할 할당](/rest/api/authorization/roleassignments)을 만듭니다. 다음 예제에서는 루트 범위(`/`)에서 {roleDefinitionID} 역할을 할당하는 속성을 보여줍니다.

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. 사용자 액세스 관리자인 경우 루트 범위(`/`)에서 역할 할당을 삭제할 수도 있습니다.

1. 다시 필요할 때까지 사용자 액세스 관리자 권한을 취소합니다.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>REST API로 elevateAccess 작업을 취소하는 방법

`elevateAccess`를 호출하는 경우 해당 권한을 취소하려면 할당을 삭제해야 하므로 스스로 역할 할당을 만듭니다.

1. `roleName`이 사용자 액세스 관리자인 [GET roleDefinitions](/rest/api/authorization/roledefinitions/get)를 호출하여 사용자 액세스 관리자 역할의 ID 이름을 확인합니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` 같은 경우에 `name` 매개 변수의 ID를 저장합니다.

2. 또한 테넌트 범위에서 테넌트 관리자의 역할 할당을 나열해야 합니다. 액세스 권한 상승 호출을 수행한 테넌트 관리자의 `principalId`에 대한 테넌트 범위의 모든 할당을 나열합니다. 그러면 objectid에 대한 테넌트의 모든 할당이 나열됩니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >테넌트 관리자는 할당이 많지 않아야 합니다. 이전 쿼리에서 너무 많은 할당을 반환하는 경우, 테넌트 범위 수준의 모든 할당을 쿼리한 다음, 결과를 필터링할 수도 있습니다. `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. 이전 호출은 역할 할당 목록을 반환합니다. 범위가 "/"이고 `roleDefinitionId`가 1단계에서 찾은 역할 이름 ID로 끝나는 역할 할당을 찾고 `principalId`가 테넌트 관리자의 objectId와 일치합니다. 
    
    샘플 역할 할당:

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    e7dd75bc-06f6-4e71-9014-ee96a929d099 같은 경우에 `name` 매개 변수에서 ID를 다시 저장합니다.

    3. 마지막으로 역할 할당 ID를 사용하여 `elevateAccess`에 의해 추가된 할당을 삭제합니다.

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>다음 단계

- [REST를 사용하는 역할 기반 액세스 제어](role-assignments-rest.md)
- [액세스 할당 관리](role-assignments-users.md)
