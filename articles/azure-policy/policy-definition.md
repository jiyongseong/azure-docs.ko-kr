---
title: Azure Policy 정의 구조
description: 정책이 언제 적용되고 어떤 영향이 있는지 설명함으로써 Azure Policy가 조직의 리소스에 대한 규칙을 설정하기 위해 리소스 정책 정의가 어떻게 사용되는지 설명합니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1937792290d973f3aee7fa3c0714f4667c21e79a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194651"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy 정의 구조

Azure Policy에서 사용되는 리소스 정책을 통해 정책이 언제 적용되고 어떤 영향이 있는지 설명함으로써 조직의 리소스에 대한 규칙을 설정할 수 있습니다. 규칙을 정의하여 비용을 제어하고 리소스를 보다 쉽게 관리할 수 있습니다. 예를 들어, 특정 유형의 가상 머신만 허용되게 지정할 수 있습니다. 또는 모든 리소스가 특정 태그를 갖도록 요구할 수 있습니다. 정책은 모든 자식 리소스에 의해 상속됩니다. 이에 따라 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 해당 정책을 적용할 수 있습니다.

여기에서는 Azure Policy에서 사용하는 스키마를 찾을 수 있습니다. [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

JSON을 사용하여 정책 정의를 만듭니다. 정책 정의에는 다음 요소가 포함됩니다.

- 모드
- 매개 변수
- 표시 이름
- description
- 정책 규칙
  - 논리 평가
  - 영향

예를 들어 다음 JSON에서는 리소스가 배포되는 위치를 제한하는 정책을 보여줍니다.

```json
{
    "properties": {
        "mode": "all",
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

모든 Azure Policy 템플릿 샘플은 [Azure Policy에 대한 템플릿](json-samples.md)에 있습니다.

## <a name="mode"></a>Mode

**mode**는 정책에 대해 평가할 리소스 종류를 결정합니다. 지원되는 모드는 다음과 같습니다.

- `all`: 리소스 그룹 및 모든 리소스 종류를 평가합니다.
- `indexed`: 태그 및 위치를 지원하는 리소스 종류만 평가합니다.

대부분 **mode**를 `all`로 설정하는 것이 좋습니다. 포털을 통해 생성된 모든 정책 정의는 `all` 모드를 사용합니다. PowerShell 또는 Azure CLI를 사용하는 경우 **mode** 매개 변수를 수동으로 지정할 수 있습니다. 정책 정의에 **mode** 값이 포함되어 있지 않으면 이전 버전과의 호환성을 위해 Azure PowerShell에서 `all` 및 Azure CLI에서 `null`(`indexed`와 동등함)로 기본값이 설정됩니다.

`indexed`는 태그 또는 위치를 시스템에 적용하는 정책을 만들 때 사용해야 합니다. 필수는 아니지만, 태그 및 위치를 지원하지 않는 리소스가 준수 결과에서 호환되지 않음으로 표시되지 않도록 방지합니다. 한 가지 예외는 **리소스 그룹**입니다. 리소스 그룹에서 위치 또는 태그를 적용하려고 하는 정책은 **모드**를 `all`로 설정하고 구체적으로 `Microsoft.Resources/subscriptions/resourceGroup` 형식을 대상으로 지정해야 합니다. 예를 들어 [리소스 그룹 태그 적용](scripts/enforce-tag-rg.md)을 참조하세요.

## <a name="parameters"></a>매개 변수

매개 변수는 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 양식의 필드 `name`, `address`, `city`, `state`와 같은 매개 변수에 관해 생각해 봅니다. 이러한 매개 변수는 항상 그대로 유지되지만, 그 값은 양식을 작성하는 개별 값에 기초하여 달라집니다. 매개 변수는 정책을 만들 때와 같은 방법으로 작동합니다. 정책 정의에 매개 변수를 포함함으로써 서로 다른 값을 사용하여 다양한 시나리오에 대해 해당 정책을 재사용할 수 있습니다.

예를 들어 리소스를 배포할 수 있는 위치를 제한하는 리소스 속성에 대한 정책을 정의할 수 있습니다. 이 경우 정책을 만들 때 다음 매개 변수를 선언합니다.

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

매개 변수의 형식은 문자열 또는 배열이 될 수 있습니다. 메타데이터 속성은 Azure Portal과 같은 도구에 사용되어 사용자 친화적 정보를 표시합니다.

메타데이터 속성 안에서 **strongType**을 사용하여 Azure Portal 내의 다중 선택 옵션 목록을 제공할 수 있습니다.  **strongType**에서 현재 허용되는 값은 다음과 같습니다.

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

정책 규칙에서 다음 구문을 사용하여 매개 변수를 참조할 수 있습니다.

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>정의 위치

이니셔티브 또는 정책 정의를 만드는 동안 정의 위치를 지정하는 것이 중요합니다.

정의 위치는 이니셔티브 또는 정책 정의를 할당할 수 있는 범위를 결정합니다. 관리 그룹이나 구독으로 위치를 지정할 수 있습니다.

> [!NOTE]
> 이 정책 정의를 여러 구독에 적용하려는 경우 위치는 이니셔티브 또는 정책을 할당할 구독이 포함된 관리 그룹이어야 합니다.

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **설명**을 사용하여 정책 정의를 식별하고 사용하는 시기에 대한 컨텍스트를 제공할 수 있습니다.

## <a name="policy-rule"></a>정책 규칙

정책 규칙은 **If** 및 **Then** 블록으로 이루어집니다. **If** 블록에서 정책이 적용되는 시점을 지정하는 하나 이상의 조건을 정의합니다. 이러한 조건에 논리 연산자를 적용하여 정책에 대한 시나리오를 정확하게 정의할 수 있습니다.

**Then** 블록에서 **If** 조건이 충족된 경우에 발생하는 효과를 정의합니다.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>논리 연산자

지원되는 논리 연산자는 다음과 같습니다.

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 구문은 조건의 결과를 반전시킵니다. **allOf** 구문(논리 **And** 작업과 비슷함)은 모든 조건이 true여야 합니다. **anyOf** 구문(논리 **Or** 작업과 비슷함)은 하나 이상의 조건이 true여야 합니다.

논리 연산자를 중첩할 수 있습니다. 다음 예제에서는 **allOf** 작업 내에 중첩된 **not** 작업을 표시합니다.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>조건

조건은 **field**에서 특정 기준을 충족하는지를 평가합니다. 지원되는 조건은 다음과 같습니다.

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

**like** 및 **notLike** 조건을 사용하는 경우 값에 와일드카드(*)를 제공할 수 있습니다.

**match** 및 **notMatch** 조건을 사용하는 경우 자릿수 하나를 나타내려면 `#`를, 문자 하나를 나타내려면 `?`를, 해당 실제 문자를 나타내려면 다른 문자를 입력합니다. 예를 들어 [여러 이름 패턴 허용](scripts/allow-multiple-name-patterns.md)을 참조하세요.

### <a name="fields"></a>필드

조건은 필드를 사용하여 구성됩니다. 필드는 리소스의 상태를 설명하는 데 사용되는 리소스 요청 페이로드의 속성을 나타냅니다.  

다음 필드가 지원됩니다.

- `name`
- `fullName`
  - 부모(예: "myServer/myDatabase")를 비롯한 리소스의 전체 이름을 반환합니다.
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - 이 대괄호 구문은 마침표가 포함된 태그 이름을 지원합니다.
- 속성 별칭 - 목록은 [별칭](#aliases)을 참조하세요.

### <a name="alternative-accessors"></a>대체 접근자

**필드**는 정책 규칙에서 사용되는 기본 접근자입니다. 평가되는 리소스를 직접 검사합니다. 그러나 정책에서는 다른 하나의 접근자인 **원본**을 지원합니다.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**원본**은 하나의 값인 **작업**만 지원합니다. 작업은 평가되는 요청의 권한 부여 동작을 반환합니다. 권한 부여 작업은 [활동 로그](../monitoring-and-diagnostics/monitoring-activity-log-schema.md)의 권한 부여 섹션에서 노출됩니다.

정책이 백그라운드에서 기존 리소스를 평가하는 경우 **작업**을 리소스 형식에 대한 `/write` 권한 부여 작업으로 설정합니다.

### <a name="effect"></a>결과

정책은 다음과 같은 형식의 결과 지원합니다.

- **거부**는 감사 로그에 이벤트를 생성하고 요청을 실패합니다.
- **감사**는 감사 로그에 경고 이벤트를 생성하지만 요청을 실패하지는 않습니다.
- **추가**는 정의된 필드 집합을 요청에 추가합니다.
- **AuditIfNotExists** - 리소스가 없으면 감사를 사용하도록 설정합니다.
- **DeployIfNotExists**: 리소스가 아직 없으면 배포합니다. 현재 이 결과는 기본 제공 정책을 통해서만 지원됩니다.

**append**의 경우 아래와 같이 details(세부 정보)를 제공해야 합니다.

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

값은 문자열 또는 JSON 형식의 개체일 수 있습니다.

**AuditIfNotExists** 및 **DeployIfNotExists**를 사용하면 관련 리소스의 존재 여부를 평가하고, 해당 리소스가 없을 경우 규칙과 그 결과를 적용할 수 있습니다. 예를 들어 모든 가상 네트워크에 대해 네트워크 감시자를 배포하도록 요구할 수 있습니다.
가상 머신 확장이 배포되지 않은 경우의 감사 예제는 [확장이 존재하지 않을 경우 감사](scripts/audit-ext-not-exist.md)를 참조하세요.

## <a name="aliases"></a>Aliases

리소스 유형에 대한 특정 속성에 액세스하려면 속성 별칭을 사용합니다. 별칭을 사용하면 리소스의 속성에 허용되는 값이나 조건을 제한할 수 있습니다. 각 별칭은 주어진 리소스 유형에 대해 서로 다른 API 버전의 경로에 매핑됩니다. 정책 평가 중에 정책 엔진은 해당 API 버전에 대한 속성 경로를 가져옵니다.

별칭의 목록은 항상 업데이트됩니다. 현재 Azure Policy에서 지원하는 별칭을 검색하려면 다음 방법 중 하나를 사용합니다.

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>이니셔티브

그룹을 단일 항목으로 작업할 수 있기 때문에 이니셔티브를 사용하면 여러 관련 정책 정의를 그룹화할 수 있어 할당 및 관리를 간소화합니다. 예를 들어 관련 태그 지정 정책 정의 모두를 단일 이니셔티브로 그룹화할 수 있습니다. 각 정책을 개별적으로 할당하는 대신 이니셔티브를 적용합니다.

다음 예제에서는 두 태그 `costCenter`과 `productName`를 처리하기 위한 이니셔티브를 만드는 방법을 보여 줍니다. 기본 태그 값을 적용하려면 두 가지 기본 제공 정책을 사용합니다.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy에 대한 템플릿](json-samples.md)에서 Azure Policy 템플릿 샘플을 검토합니다.