---
title: REST를 사용한 역할 기반 액세스 제어 - Azure AD | Microsoft Docs
description: REST API를 사용하여 역할 기반 액세스 제어 관리
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: rolyon
ms.openlocfilehash: e520b4b85ff7c840c46175cae5b853ec6979c04a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>REST API를 사용하여 역할 기반 Access Control 관리
> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)

RBAC(역할 기반 액세스 제어)를 통해 특정 범위에서 역할을 할당하여 사용자, 그룹, 서비스 주체에 대한 액세스를 정의합니다. 이 문서에서는 REST API를 사용하여 액세스를 관리하는 방법을 설명합니다.

## <a name="list-all-role-assignments"></a>모든 역할 할당 나열
지정된 범위 및 하위 범위에서 모든 역할 할당을 나열합니다.

역할 할당을 나열하려면 범위에서 `Microsoft.Authorization/roleAssignments/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **GET** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{api-version}* 을 2015-07-01로 바꿉니다.
3. *{filter}* 를 역할 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

   * 하위 범위에서는 역할 할당을 포함시키지 않고 지정된 범위에 대해서만 역할 할당 나열: `atScope()`    
   * 특정 사용자, 그룹 또는 응용 프로그램에 대해서만 역할 할당 나열: `principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * 그룹에서 상속된 역할 할당을 포함하여 특정 사용자에 대한 역할 할당 나열 | `assignedTo('{objectId of user}')`

### <a name="response"></a>response
상태 코드: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>역할 할당에 대한 정보 가져오기
역할 할당 식별자에서 지정한 단일 역할 할당에 대한 정보를 가져옵니다.

역할 할당에 대한 정보를 가져오려면 `Microsoft.Authorization/roleAssignments/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **GET** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-assignment-id}* 를 역할 할당의 GUID 식별자로 바꿉니다.
3. *{api-version}* 을 2015-07-01로 바꿉니다.

### <a name="response"></a>response
상태 코드: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>역할 할당 만들기
지정된 역할을 부여하는 지정된 보안 주체에 대해 지정된 범위에서 역할 할당을 만듭니다.

역할 할당을 만들려면 `Microsoft.Authorization/roleAssignments/write` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **PUT** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 를 역할 할당을 만들려는 범위로 바꿉니다. 부모 범위에서 역할 할당을 만들 때 모든 자식 범위는 같은 역할 할당을 상속합니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1   
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-assignment-id}* 를 새 역할 할당의 GUID 식별자가 되는 새 GUID로 바꿉니다.
3. *{api-version}* 을 2015-07-01로 바꿉니다.

요청 본문에 대해 다음과 같은 형식으로 값을 제공합니다.

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| 요소 이름 | 필수 | 형식 | 설명 |
| --- | --- | --- | --- |
| roleDefinitionId |예 |문자열 |역할의 식별자입니다. 식별자의 형식은 `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |예 |문자열 |역할을 할당할 Azure AD 보안 주체(사용자, 그룹 또는 서비스 사용자)의 objectId입니다. |

### <a name="response"></a>response
상태 코드: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>역할 할당 삭제
지정된 범위에서 역할 할당을 삭제합니다.

역할 할당을 삭제하려면 `Microsoft.Authorization/roleAssignments/delete` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **DELETE** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 를 역할 할당을 만들려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-assignment-id}* 를 역할 할당 id GUID로 바꿉니다.
3. *{api-version}* 을 2015-07-01로 바꿉니다.

### <a name="response"></a>response
상태 코드: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>모든 역할 나열
지정된 범위에서 할당에 사용할 수 있는 모든 역할을 나열합니다.

역할을 나열하려면 범위에서 `Microsoft.Authorization/roleDefinitions/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **GET** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 를 역할을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{api-version}* 을 2015-07-01로 바꿉니다.
3. *{filter}* 를 역할 목록을 필터링하기 위해 적용할 조건으로 바꿉니다.

   * 지정된 범위 및 해당 자식 범위에서 할당에 사용할 수 있는 역할 나열: `atScopeAndBelow()`
   * 정확한 표시 이름을 사용하여 역할 검색: `roleName%20eq%20'{role-display-name}'`역할의 정확한 표시 이름에 대한 URL 인코딩 형식을 사용합니다. 역할의 정확한 표시 이름에 대한 URL 인코딩 형식을 사용합니다. 예를 들어, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>response
상태 코드: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>역할에 대한 정보 가져오기
역할 정의 식별자에서 지정한 단일 역할에 대한 정보를 가져옵니다. 해당 표시 이름을 사용하여 단일 역할에 대한 정보를 가져오려면 [모든 역할 나열](role-assignments-rest.md#list-all-roles)을 참조하세요.

역할에 대한 정보를 가져오려면 `Microsoft.Authorization/roleDefinitions/read` 작업에 대한 액세스 권한이 있어야 합니다. 모든 기본 제공 역할에는 이 작업에 대한 액세스 권한이 부여되어 있습니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **GET** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* 를 역할 정의의 GUID 식별자로 바꿉니다.
3. *{api-version}* 을 2015-07-01로 바꿉니다.

### <a name="response"></a>response
상태 코드: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기
사용자 지정 역할을 만듭니다.

사용자 지정 역할을 만들려면 해당하는 모든 `AssignableScopes`에서 `Microsoft.Authorization/roleDefinitions/write` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **PUT** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 을 사용자 지정 역할의 첫 번째 *AssignableScope*으로 바꿉니다. 다음 예제는 서로 다른 수준에 대한 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* 를 새 사용자 지정 역할의 GUID 식별자가 되는 새 GUID로 바꿉니다.
3. *{api-version}* 을 2015-07-01로 바꿉니다.

요청 본문에 대해 다음과 같은 형식으로 값을 제공합니다.

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 요소 이름 | 필수 | 형식 | 설명 |
| --- | --- | --- | --- |
| 이름 |예 |문자열 |사용자 지정 역할의 GUID 식별자입니다. |
| properties.roleName |예 |문자열 |사용자 지정 역할의 표시 이름입니다. 최대 128자입니다. |
| properties.description |아니오 |문자열 |사용자 지정 역할에 대한 설명입니다. 최대 1024자입니다. |
| properties.type |예 |문자열 |"CustomRole"로 설정합니다. |
| properties.permissions.actions |예 |문자열[] |사용자 지정 역할이 권한을 부여하는 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.permissions.notActions |아니오 |문자열[] |사용자 지정 역할이 권한을 부여하는 작업에서 제외할 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.assignableScopes |예 |문자열[] |사용자 지정 역할을 사용할 수 있는 범위의 배열입니다. |

### <a name="response"></a>response
상태 코드: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트
사용자 지정 역할을 수정합니다.

사용자 지정 역할을 수정하려면 해당하는 모든 `AssignableScopes`에서 `Microsoft.Authorization/roleDefinitions/write` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **PUT** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 을 사용자 지정 역할의 첫 번째 *AssignableScope*으로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* 를 사용자 지정 역할의 GUID 식별자로 바꿉니다.
3. *{api-version}* 을 2015-07-01로 바꿉니다.

요청 본문에 대해 다음과 같은 형식으로 값을 제공합니다.

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 요소 이름 | 필수 | 형식 | 설명 |
| --- | --- | --- | --- |
| 이름 |예 |문자열 |사용자 지정 역할의 GUID 식별자입니다. |
| properties.roleName |예 |문자열 |업데이트된 사용자 지정 역할의 표시 이름입니다. |
| properties.description |아니오 |문자열 |업데이트된 사용자 지정 역할의 설명입니다. |
| properties.type |예 |문자열 |"CustomRole"로 설정합니다. |
| properties.permissions.actions |예 |문자열[] |업데이트된 사용자 지정 역할이 액세스 권한을 부여하는 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.permissions.notActions |아니오 |문자열[] |업데이트된 사용자 지정 역할이 권한을 부여하는 작업에서 제외할 작업을 지정하는 동작 문자열의 배열입니다. |
| properties.assignableScopes |예 |문자열[] |업데이트된 사용자 지정 역할을 사용할 수 있는 범위의 배열입니다. |

### <a name="response"></a>response
상태 코드: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제
사용자 지정 역할을 삭제합니다.

사용자 지정 역할을 삭제하려면 해당하는 모든 `AssignableScopes`에서 `Microsoft.Authorization/roleDefinitions/delete` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 *소유자* 및 *사용자 액세스 관리자*에게만 이러한 작업의 권한이 부여됩니다. Azure 리소스에 대한 액세스 관리 및 역할 할당에 대한 자세한 내용은 [Azure 역할 기반 Access Control](role-assignments-portal.md)을 참조하세요.

### <a name="request"></a>요청
다음 URI와 함께 **DELETE** 메서드를 사용합니다.

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 내에서 다음을 대체하여 요청을 사용자 지정합니다.

1. *{scope}* 를 역할 정의를 삭제하려는 범위로 바꿉니다. 다음 예제에서는 서로 다른 수준에 대해 범위를 지정하는 방법을 보여 줍니다.

   * 구독: /subscriptions/{subscription-id}  
   * 리소스 그룹: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 리소스: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* 를 사용자 지정 역할의 GUID 역할 정의 ID로 바꿉니다.
3. *{api-version}* 을 2015-07-01로 바꿉니다.

### <a name="response"></a>response
상태 코드: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
