---
title: Azure Policy를 사용하여 조직의 규정 준수를 적용하는 정책 만들기 및 관리
description: Azure Policy를 사용하여 표준을 적용하고, 규정 준수 및 감사 요구 사항을 충족하며, 비용을 통제하고, 보안 및 성능 일관성을 유지하며, 엔터프라이즈 수준 디자인 원칙을 적용합니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 1d13c5c0099b8e4c9ed83221edf6a2ee92fc3c8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194872"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>규정 준수를 적용하는 정책 만들기 및 관리

회사 표준 및 서비스 수준 계약의 준수를 유지하는 데 있어 Azure에서 정책을 만들고 관리하는 방법을 이해하는 것이 중요합니다. 이 자습서에서는 Azure Policy를 사용하여 조직 전체에서 정책을 생성, 할당 및 관리하는 것과 관련된 보다 일반적인 작업 중 일부를 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - 나중에 만들 리소스에 대한 조건을 적용하는 정책 할당
> - 여러 리소스에 대한 규정 준수를 추적하기 위한 이니셔티브 정의 만들기 및 할당
> - 규정 비준수 또는 거부된 리소스 해결
> - 조직 전체에서 새 정책 구현

기존 리소스의 현재 규정 준수 상태를 식별하는 정책을 할당하려는 경우 이 빠른 시작 문서를 통해 방법을 살펴보세요. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="assign-a-policy"></a>정책 할당

Azure Policy 준수를 적용하기 위한 첫 번째 단계는 정책 정의를 할당하는 것입니다. 정책 정의는 정책이 적용되는 조건과 효과를 정의합니다. 이 예제에서는 *SQL Server 버전 12.0 필요*라는 기본 제공 정책 정의를 할당하여 모든 SQL Server 데이터베이스에서 v12.0을 준수해야 한다는 조건을 적용합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

   ![정책 검색](media/create-manage-policy/search-policy.png)

2. Azure Policy 페이지의 왼쪽에서 **할당**을 선택합니다. 할당은 특정 범위 내에서 수행하도록 할당된 정책입니다.
3. **정책 - 할당** 창의 위쪽에서 **정책 할당**을 선택합니다.

   ![정책 정의 할당](media/create-manage-policy/select-assign-policy.png)

4. **정책 할당** 페이지에서 줄임표를 클릭하고 구독(필수) 및 리소스 그룹(선택 사항)을 선택하여 **범위**를 선택합니다. 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다.  그 후 **범위** 페이지의 맨 위에서 **선택**을 클릭합니다.

   이 예제에서는 **Contoso 구독**을 사용합니다. 구독을 사용합니다.

5. 하나 이상의 구독(범위를 한 구독으로 지정한 경우) 또는 리소스 그룹의 특정 리소스(두 범위 지정 경우)를 제외하려면 정책 할당에서 **제외**를 구성하면 됩니다. 지금은 비워 둡니다.

6. **정책 정의** 줄임표를 선택하여 사용 가능한 정의 목록을 엽니다. 정책 정의 **유형**을 *기본 제공*으로 필터링하여 모든 정책을 살펴보고 설명을 읽을 수 있습니다.

7. **SQL Server 버전 12.0 필요**를 선택합니다. 즉시 찾을 수 없는 경우 검색 상자에 **sql server 필요**를 입력하고 ENTER 키를 누르거나 검색 상자에서 클릭합니다. 정책 정의를 찾아서 선택했으면 **사용 가능한 정의** 페이지의 맨 아래에서 **선택**을 클릭합니다.

   ![정책 찾기](media/create-manage-policy/select-available-definition.png)

8. 선택한 이름이 **할당 이름**에 자동으로 채워지지만, 할당 이름을 변경할 수 있습니다. 이 예에서는 *SQL Server 버전 12.0 필요*를 사용하겠습니다. 선택적인 **설명**을 추가할 수도 있습니다. 설명은 이 정책 할당에 대한 세부 정보를 제공합니다.

9. **할당**을 클릭합니다.

## <a name="implement-a-new-custom-policy"></a>새 사용자 지정 정책 구현

기본 제공 정책 정의를 할당했으니, 이제 Azure Policy로 더 많은 일을 할 수 있습니다. 다음으로, 환경 내에서 만든 VM을 G 시리즈에 사용할 수 없도록 하여 비용을 절감하는 새로운 사용자 지정 정책을 만듭니다. 이렇게 하면 조직 내 사용자가 G 시리즈에서 VM을 만들려고 시도할 때마다 요청이 거부됩니다.

1. Azure Policy 페이지의 왼쪽에 있는 **작성**에서 **정의**를 선택합니다.

   ![제작 아래의 정의](media/create-manage-policy/definition-under-authoring.png)

2. 페이지 위쪽에서 **+ 정책 정의**를 선택합니다. **정책 정의** 페이지가 열립니다.
3. 다음을 입력합니다.

   - 정책 정의가 저장되는 관리 그룹 또는 구독. **정의 위치**에 있는 줄임표를 사용하여 선택합니다.

     > [!NOTE]
     > 이 정책 정의를 여러 구독에 적용하려는 경우 위치는 정책을 할당할 구독이 포함된 관리 그룹이어야 합니다. 이니셔티브 정의도 마찬가지입니다.

   - 정책 정의 이름 - *G 시리즈보다 작은 VM SKU 필요*
   - 정책 정의의 용도에 대한 설명 - *여기서는 이 범위에서 만든 모든 VM에서 G 시리즈보다 작은 SKU를 사용하여 비용을 줄인다는 것을 정책 정의에 적용하고 있습니다.*
   - 기존 옵션 중에서 선택하거나 이 정책 정의에 대한 새 범주를 만듭니다.
   - 다음 json 코드를 복사한 다음 필요에 맞게 업데이트합니다.
      - 정책 매개 변수
      - 정책 규칙/조건 - 여기서는 'VM SKU 크기가 G 시리즈와 같음'입니다.
      - 정책 효과 - 여기서는 **거부**입니다.

    json 코드는 다음과 같습니다. 수정된 코드를 Azure Portal에 붙여 넣습니다.

    ```json
    {
        "policyRule": {
            "if": {
                "allOf": [{
                        "field": "type",
                        "equals": "Microsoft.Compute/virtualMachines"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/sku.name",
                        "like": "Standard_G*"
                    }
                ]
            },
            "then": {
                "effect": "deny"
            }
        }
    }
    ```

    정책 규칙의 *필드 속성* 값은 이름, 유형, 위치, 태그 또는 별칭 중 하나여야 합니다. 별칭의 예로 `"Microsoft.Compute/VirtualMachines/Size"`를 들 수 있습니다.

    더 많은 Azure 정책 샘플을 보려면 [Azure Policy 템플릿](json-samples.md)을 참조하세요.

4. **저장**을 선택합니다.

## <a name="create-a-policy-definition-with-rest-api"></a>REST API를 사용하여 정책 정의 만들기

정책 정의에 대한 REST API를 사용하여 정책을 만들 수 있습니다. REST API를 사용하여 정책 정의를 만들고, 삭제하고, 기존 정의에 관한 정보를 가져올 수 있습니다.
정책 정의를 만들려면 다음 예제를 사용합니다.

```http-interactive
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

다음 예제와 비슷한 요청 본문을 포함합니다.

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>PowerShell을 사용하여 정책 정의 만들기

PowerShell 예제를 진행하기 전에 최신 버전의 Azure PowerShell을 설치했는지 확인합니다. 정책 매개 변수가 버전 3.6.0에 추가되었습니다. 이전 버전이 있는 경우 예제에서는 매개 변수를 찾을 수 없음을 나타내는 오류를 반환합니다.

`New-AzureRmPolicyDefinition` cmdlet을 사용하여 정책 정의를 만들 수 있습니다.

파일에서 정책 정의를 만들려면 경로를 파일에 전달합니다. 외부 파일에 대해서는 다음 예제를 사용합니다.

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

로컬 파일에 대해서는 다음 예제를 사용합니다.

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

인라인 규칙을 사용하여 정책 정의를 만들려면 다음 예제를 사용합니다.

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "not": {
                    "field": "Microsoft.Storage/storageAccounts/accessTier",
                    "equals": "cool"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

출력 `$definition` 개체에 저장되어 정책 할당 중에 사용됩니다.
다음 예제에서는 매개 변수를 포함하는 정책 정의를 만듭니다.

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>PowerShell을 사용하여 정책 정의 보기

구독에서 모든 정책 정의를 보려면 다음 명령을 사용합니다.

```azurepowershell-interactive
Get-AzureRmPolicyDefinition
```

기본 제공 정책을 비롯한 사용 가능한 모든 정책 정의를 반환합니다. 각 정책은 다음 형식으로 반환됩니다.

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Azure CLI를 사용하여 정책 정의 만들기

정책 정의 명령과 함께 Azure CLI를 사용하여 정책 정의를 만들 수 있습니다.
인라인 규칙을 사용하여 정책 정의를 만들려면 다음 예제를 사용합니다.

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "not": {
                    "field": "Microsoft.Storage/storageAccounts/accessTier",
                    "equals": "cool"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Azure CLI를 사용하여 정책 정의 보기

구독에서 모든 정책 정의를 보려면 다음 명령을 사용합니다.

```azurecli-interactive
az policy definition list
```

기본 제공 정책을 비롯한 사용 가능한 모든 정책 정의를 반환합니다. 각 정책은 다음 형식으로 반환됩니다.

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>이니셔티브 정의 만들기 및 할당

이니셔티브 정의를 사용하면 여러 정책 정의를 그룹화하여 매우 중요한 하나의 목표를 달성할 수 있습니다. 정의 범위 내의 리소스에서 이니셔티브 정의를 구성하는 정책 정의의 준수를 유지하도록 하는 이니셔티브 정의를 만듭니다.  이니셔티브 정의에 대한 자세한 내용은 [Azure Policy 개요](azure-policy-introduction.md)를 참조하세요.

### <a name="create-an-initiative-definition"></a>이니셔티브 정의 만들기

1. Azure Policy 페이지의 왼쪽에 있는 **작성**에서 **정의**를 선택합니다.

   ![정의 선택](media/create-manage-policy/select-definitions.png)

2. 페이지 위쪽에서 **+ 이니셔티브 정의**를 선택하여 **이니셔티브 정의** 페이지를 엽니다.

   ![이니셔티브 정의](media/create-manage-policy/initiative-definition.png)

3. **정의 위치** 줄임표를 사용하여 정의를 저장할 구독을 선택합니다.

4. 이니셔티브의 **이름**과 **설명**을 입력합니다.

   이 예제는 리소스가 보안 강화에 대한 정책 정의를 준수하도록 보장합니다. 따라서 이니셔티브의 이름은 **보안 강화**이고, 설명은 **이 이니셔티브는 리소스 보안과 관련된 모든 정책 정의를 처리하기 위해 만들어졌습니다**가 됩니다.

5. **범주**로는 기존 옵션에서 선택하거나 새 범주를 만듭니다.

6. **사용 가능한 정의** 목록(**이니셔티브 정의** 페이지의 오른쪽 절반)을 검색하고 이 이니셔티브에 추가할 정책 정의를 선택합니다. **보안 강화** 이니셔티브의 경우 정책 정의 정보 옆에 있는 **+** 기호를 클릭하거나 정책 정의 행을 클릭하고 세부 정보 페이지에서 **+ 추가**를 클릭하여 다음과 같은 기본 제공 정의를 추가합니다.
   - SQL Server 버전 12.0 필요
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   목록에서 정책 정의를 선택하면 **정책 및 매개 변수** 아래에 추가됩니다.

   ![이니셔티브 정의](media/create-manage-policy/initiative-definition-2.png)

7. **저장**을 클릭합니다.

### <a name="assign-an-initiative-definition"></a>이니셔티브 정의 할당

1. Azure Policy 페이지의 왼쪽에 있는 **작성**에서 **정의**를 선택합니다.
2. 앞에서 만든 **보안 강화** 이니셔티브 정의를 찾아서 선택합니다.
3. 페이지 위쪽에서 **할당**을 선택하여 **보안 강화: 이니셔티브 할당** 페이지를 엽니다.

   ![정의 할당](media/create-manage-policy/assign-definition.png)

   또는 선택한 행을 마우스 오른쪽 단추로 클릭하거나 행 끝에 있는 줄임표(...)를 마우스 왼쪽 단추로 클릭하여 상황에 맞는 메뉴를 표시할 수 있습니다.  그리고 **할당**을 선택합니다.

   ![행을 마우스 오른쪽 단추로 클릭](media/create-manage-policy/select-right-click.png)

4. 다음 예제 정보를 입력하여 **보안 강화: 이니셔티브 할당** 페이지를 채웁니다. 사용자 고유의 정보를 사용해도 됩니다.

   - 범위: 이니셔티브를 저장한 구독은 기본 구독이 됩니다.  구독 저장 위치 내의 리소스 그룹에 이니셔티브를 할당하도록 범위를 변경할 수 있습니다.
   - 제외: 범위 내의 리소스에 이니셔티브 할당이 적용되지 않도록 리소스를 구성합니다.
   - 이니셔티브 정의 및 할당 이름: 보안 강화(할당되는 이니셔티브의 이름으로 미리 채워짐).
   - 설명: 이 이니셔티브 할당은 이 정책 정의 그룹을 적용하도록 조정됩니다.

5. **할당**을 클릭합니다.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>배제를 사용하여 규정 비준수 또는 거부된 리소스 제외

위의 예제에 따라 SQL Server 버전 12.0을 요구하도록 정책 정의를 할당한 후에는 다른 12.0 버전으로 만든 SQL Server가 거부됩니다. 이 섹션에서는 단일 리소스 그룹에 대한 제외를 만들어서 SQL 서버 생성 시도 거부 문제를 해결하는 방법을 단계별로 안내합니다. 제외는 해당 리소스에 정책(또는 이니셔티브)이 적용되지 않도록 방지합니다. 다음 예제에서는 모든 SQL 서버 버전이 단일 리소스 그룹에 허용됩니다. 리소스 그룹에 제외를 적용하거나 제외 범위를 개별 리소스로 좁힐 수 있습니다.

할당된 정책 또는 이니셔티브로 인해 금지된 배포는 다음과 같은 두 위치에서 볼 수 있습니다.

- 배포의 대상인 리소스 그룹: 페이지 왼쪽에서 **배포**를 선택하고 실패한 배포에서 **배포 이름**을 클릭합니다. 거부된 리소스가 _사용할 수 없음_ 상태와 함께 나열됩니다. 리소스를 거부한 정책 또는 이니셔티브와 할당을 확인하려면 개요 페이지에서 **실패했습니다. 자세한 내용을 보려면 여기를 클릭하세요. ->** 를 클릭합니다. 페이지 오른쪽에 창이 열리고 오류 정보가 표시됩니다. **오류 세부 정보** 아래에 관련 정책 개체의 GUID가 있습니다.

   ![정책 할당에 의해 거부된 배포](media/create-manage-policy/rg-deployment-denied.png)

- Azure Policy 페이지의 왼쪽에서 **준수**를 선택하고 **SQL Server 버전 12.0 필요** 정책을 클릭합니다. 열리는 페이지에서 **거부** 횟수가 증가하는 것을 볼 수 있습니다. 또한 **이벤트** 탭 아래를 보면 정책에서 거부한 배포를 시도한 사람이 누구인지 확인할 수 있습니다.

   ![할당된 정책의 규정 준수 정책](media/create-manage-policy/compliance-overview.png)

이 예제에서는 Contoso의 선임 가상화 전문가 중 한 명인 Trent Baker가 필수 작업을 수행하고 있었습니다. 이 사람에게 예외를 부여해야 하는데, 그 어떤 리소스 그룹에서도 12.0 SQL 서버 이외의 버전은 허용하지 않으려 합니다. 그래서 **SQLServers_Excluded**라고 하는 새 리소스 그룹을 만들었으며 이제 이 정책 할당에 예외를 부여할 것입니다.

### <a name="update-assignment-with-exclusion"></a>할당에 제외 업데이트

1. Azure Policy 페이지의 왼쪽에 있는 **작성**에서 **할당**을 선택합니다.
2. 모든 정책 할당을 검색하고 *SQL Server 버전 12.0 필요* 할당을 엽니다.
3. 줄임표를 클릭하고 제외할 리소스 그룹(이 예에서는 *SQLServers_Excluded*)을 선택하여 **제외**를 설정합니다.

   ![제외 요청](media/create-manage-policy/request-exclusion.png)

   > [!NOTE]
   > 정책 및 그 영향에 따라 할당 범위 내의 리소스 그룹에 속한 특정 리소스에 제외를 부여할 수도 있습니다. 이 자습서에서 사용한 **거부** 효과와 마찬가지로, 이미 있는 특정 리소스에 제외를 설정하는 것은 의미가 없습니다.

4. **선택**을 클릭한 다음 **저장**을 클릭합니다.

이 섹션에서는 단일 리소스 그룹에 대한 제외를 만들어서 금지된 SQL 서버 버전 생성 시도 거부 문제를 해결했습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 리소스를 모두 마쳤으면 다음 단계를 사용하여 위에서 만든 할당 또는 정의를 삭제합니다.

1. Azure Policy 페이지의 왼쪽 창에 있는 **작성** 아래에서 **정의**를 선택합니다(또는 할당을 삭제하려는 경우 **할당**을 선택).
2. 제거할 새 이니셔티브 또는 정책 정의(또는 할당)를 검색합니다.
3. 행을 마우스 오른쪽 단추로 클릭하거나 정의(또는 할당) 끝에 있는 줄임표를 선택하고 **정의 삭제**(또는 **할당 삭제**)를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서 다음 작업을 성공적으로 완료했습니다.

> [!div class="checklist"]
> - 나중에 만들 리소스에 대한 조건을 적용하는 정책 할당
> - 여러 리소스에 대한 규정 준수를 추적하기 위한 이니셔티브 정의 만들기 및 할당
> - 규정 비준수 또는 거부된 리소스 해결
> - 조직 전체에서 새 정책 구현

정책 정의의 구조에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Policy 정의 구조](policy-definition.md)