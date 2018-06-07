---
title: Azure Policy를 사용하여 프로그래밍 방식으로 정책 및 보기 규정 준수 데이터 만들기
description: 이 문서는 Azure Policy에 대해 프로그래밍 방식으로 정책을 만들고 관리하는 방법을 설명합니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 5405566b5254c553eac584acc1653449b51ddffc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195882"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>프로그래밍 방식으로 정책 및 보기 규정 준수 데이터 만들기

이 문서는 프로그래밍 방식으로 정책을 만들고 관리하는 방법을 설명합니다. 또한 리소스 규정 준수 상태 및 정책을 보는 방법을 보여줍니다. 정책 정의는 리소스에 대해 다양한 규칙과 효과를 적용합니다. 적용은 리소스가 회사 표준 및 서비스 수준 계약을 준수하는지 확인합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 필수 조건을 충족시키는지 확인합니다.

1. [ARMClient](https://github.com/projectkudu/ARMClient)를 아직 설치하지 않은 경우 설치합니다. Azure Resource Manager 기반 API에 HTTP 요청을 전송하는 도구입니다.
2. AzureRM PowerShell 모듈을 최신 버전으로 업데이트합니다. 최신 버전에 대한 자세한 내용은 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)을 참조하세요.
3. 구독이 리소스 공급자와 함께 작동하도록 Azure PowerShell을 사용하여 Policy Insights 리소스 공급자를 등록합니다. 리소스 공급자를 등록하려면 리소스 공급자에 대해 작업 등록을 수행할 권한이 있어야 합니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다. 리소스 공급자를 등록하는 다음 명령을 실행합니다.

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  리소스 공급자를 등록하고 살펴보는 방법에 대한 내용은 [리소스 공급자 및 종류](../azure-resource-manager/resource-manager-supported-services.md)를 참조하세요.
4. 아직 Azure CLI를 설치하지 않았다면 설치합니다. [Windows에 Azure CLI 2.0 설치](/cli/azure/install-azure-cli-windows)에서 최신 버전을 얻을 수 있습니다.

## <a name="create-and-assign-a-policy-definition"></a>정책 정의 만들기 및 할당

리소스의 가시성을 향상시키는 첫 번째 단계는 리소스에 대한 정책을 만들어 할당하는 것입니다. 다음은 프로그래밍 방식으로 정책을 만들고 할당 하는 방법을 알아보는 단계입니다. 예제 정책은 PowerShell, Azure CLI 및 HTTP 요청을 사용하여 모든 공용 네트워크에 개방된 저장소 계정을 감사합니다.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>PowerShell을 사용하여 정책 정의 만들기 및 할당

1. 다음 JSON 코드 조각을 사용하여 AuditStorageAccounts.json 이름의 JSON 파일을 만듭니다.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  정책 정의 작성에 대한 자세한 내용은 [Azure Policy 정의 구조](policy-definition.md)를 참조합니다.
2. AuditStorageAccounts.json 파일을 사용하여 정책 정의를 만들려면 다음 명령을 실행합니다.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  이 명령은 _공용 네트워크에 대해 열린 저장소 계정 감사_라는 정책 정의를 작성합니다. 사용할 수 있는 다른 매개 변수에 대한 자세한 내용은 [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition)을 참조하세요.
3. 정책 정의를 만든 후 다음 명령을 실행하여 정책 할당을 만들 수 있습니다.

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  _ContosoRG_를 원하는 리소스 그룹의 이름으로 바꿉니다.

Azure Resource Manager PowerShell 모듈을 사용하여 리소스 정책 관리에 대한 자세한 내용은 [AzureRM.Resources](/powershell/module/azurerm.resources/#policies)를 참조하세요.

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>ARMClient를 사용하여 정책 정의 만들기 및 할당

다음 절차에 따라 정책 정의를 만듭니다.

1. 다음 JSON 코드 조각을 복사하여 JSON 파일을 만듭니다. 다음 단계에서 파일을 호출합니다.

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. 다음 호출을 사용하여 정책 정의를 만듭니다.

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  앞의 &lt;subscriptionId&gt;을 원하는 구독 ID로 바꿉니다.

쿼리 구조에 대한 자세한 내용은 [정책 정의 - 만들기 또는 업데이트](/rest/api/resources/policydefinitions/createorupdate)를 참조하세요.

다음 절차를 사용하여 정책 할당을 만들고 리소스 그룹 수준에서 정책 정의를 할당합니다.

1. 다음 JSON 코드 조각을 복사하여 JSON 정책 할당 파일을 만듭니다. &lt;&gt; 기호의 예제 정보를 자신의 고유 값으로 바꿉니다.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      }
  }
  ```

2. 다음 호출을 사용하여 정책 할당을 만듭니다.

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  &lt;&gt; 기호의 예제 정보를 자신의 고유 값으로 바꿉니다.

  REST API에 HTTP 호출하는 방법에 대한 자세한 내용은 [Azure REST API 리소스](/rest/api/resources/)를 참조합니다.

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Azure CLI를 사용해 정책 정의 만들기 및 할당

정책 정의를 만들려면 다음 절차를 사용합니다.

1. 다음 JSON 코드 조각을 복사하여 JSON 정책 할당 파일을 만듭니다.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. 정책 정의를 만들려면 다음 명령을 실행합니다.

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. 정책 할당을 만들려면 다음 명령을 사용합니다. &lt;&gt; 기호의 예제 정보를 자신의 고유 값으로 바꿉니다.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

다음 명령으로 PowerShell을 사용하여 정책 정의 ID를 가져올 수 있습니다.

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

만든 정책 정의에 대한 정책 정의 ID는 다음 예제와 유사해야 합니다.

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Azure CLI를 사용하여 리소스 정책을 관리하는 방법에 대한 자세한 내용은 [Azure CLI 리소스 정책](/cli/azure/policy?view=azure-cli-latest)을 참조하세요.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

할당에서 리소스가 정책 또는 이니셔티브 규칙을 따르지 않으면 비준수 리소스입니다. 다음 표는 다양한 정책 효과가 결과 규정 준수 상태에 대한 조건 평가와 어떻게 작동하는지 보여줍니다.

| 리소스 상태 | 결과 | 정책 평가 | 규정 준수 상태 |
| --- | --- | --- | --- |
| exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | 비준수 |
| exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | 준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | True | 비준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | False | 준수 |

\* Append, DeployIfNotExist 및 AuditIfNotExist 효과는 IF 문이 TRUE여야 합니다. 또한 이 효과는 비준수가 되려면 존재 조건이 FALSE가 되어야 합니다. TRUE인 경우 IF 조건이 관련 리소스에 대한 존재 조건의 평가를 트리거합니다.

리소스가 비준수로 플래그 지정되는 방법을 더 잘 이해하려면 위에서 만든 정책 할당 예제를 사용합시다.

예를 들어, 공용 네트워크에 노출되는 일부 저장소 계정(빨간색으로 강조 표시됨)이 있는 리소스 그룹이 ContosoRG라고 가정해 보겠습니다.

![공용 네트워크에 노출된 저장소 계정](media/policy-insights/resource-group01.png)

이 예에서는 보안 위험에 주의해야 합니다. 정책 할당을 만들었으므로 ContosoRG 리소스 그룹에서 모든 저장소 계정에 대해 평가합니다. 비준수 저장소 계정 3개를 감사한 뒤 결국 해당 상태를 **비준수**로 변경합니다.

![감사된 비준수 저장소 계정](media/policy-insights/resource-group03.png)

리소스 그룹에서 정책 할당을 준수하지 않는 리소스를 식별하기 위해 다음 절차를 사용합니다. 예제에서 리소스는 ContosoRG 리소스 그룹의 저장소 계정입니다.

1. 다음 명령을 실행하여 정책 할당 ID를 가져옵니다.

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks' }
  $policyAssignment.PolicyAssignmentId
  ```

  정책 할당 ID를 가져오는 방법에 대한 자세한 내용은 [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment)를 참조하세요.

2. JSON 파일로 복사된 비준수 리소스의 리소스 ID를 얻으려면 다음 명령을 실행합니다.

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. 결과는 다음 예제와 유사합니다.

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

결과는 [Azure Portal 보기](assign-policy-definition.md#identify-non-compliant-resources)에서 **비준수 리소스** 아래 나열된 것과 동일합니다.

현재 비준수 리소스는 HTTP 요청과 함께 Azure Portal을 사용하여서만 식별됩니다. 정책 상태를 쿼리하는 방법에 대한 자세한 내용은 [정책 상태](/rest/api/policy-insights/policystates) API 참조 문서를 참조합니다.

## <a name="view-policy-events"></a>정책 이벤트 보기

리소스가 생성되거나 업데이트되면 정책 평가 결과가 생성됩니다. 생성된 결과를 _정책 이벤트_라고 합니다. 정책 할당과 관련된 모든 정책 이벤트를 보려면 다음 쿼리를 실행합니다.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

결과는 다음 예제와 유사합니다.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

정책 상태와 마찬가지로 HTTP 요청을 사용하면 정책 이벤트만 볼 수 있습니다. 정책 이벤트를 쿼리하는 방법에 대한 자세한 내용은 [정책 이벤트](/rest/api/policy-insights/policyevents) 참조 문서를 참조합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 제시된 명령 및 쿼리에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure REST API 리소스](/rest/api/resources/)
- [Azure RM PowerShell 모듈](/powershell/module/azurerm.resources/#policies)
- [Azure CLI 정책 명령](/cli/azure/policy?view=azure-cli-latest)
- [Policy Insights 리소스 공급자 REST API 참조](/rest/api/policy-insights)