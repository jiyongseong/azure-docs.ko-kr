---
title: Azure CLI를 사용하여 Azure 환경 내에서 규정 비준수 리소스를 식별하는 정책 할당 만들기
description: PowerShell을 사용하여 규정 비준수 리소스를 식별하는 Azure Policy 할당을 만듭니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 27f00f24c1c644e340ff8a2843b56e863136c368
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194811"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure 환경 내에서 규정 비준수 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다. 이 빠른 시작은 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당 만들기 과정을 단계별로 안내합니다.

이 프로세스가 끝나면 관리 디스크를 사용하지 않는 가상 머신이 식별됩니다. 이 가상 머신은 정책 할당과 *비호환*됩니다.

Azure CLI는 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 Azure 환경 내에서 규정 비준수 리소스를 식별하고 정책 할당을 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 빠른 시작을 수행하려면 Azure CLI 버전 2.0.4 이상을 실행하여 로컬로 CLI를 설치하고 사용해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure CLI를 사용하여 Policy Insights 리소스 공급자를 등록합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자와 함께 작동하는지 확인할 수 있습니다. 리소스 공급자를 등록하려면 리소스 공급자에 대해 작업 등록을 수행할 권한이 있어야 합니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다. 리소스 공급자를 등록하는 다음 명령을 실행합니다.

```azurecli-interactive
az provider register –-namespace 'Microsoft.PolicyInsights'
```

리소스 공급자를 등록하고 살펴보는 방법에 대한 내용은 [리소스 공급자 및 종류](../azure-resource-manager/resource-manager-supported-services.md)를 참조하세요.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 Managed Disks가 없는 Virtual Machines 감사 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정한 조건을 준수하지 않는 리소스를 식별합니다.

정책 할당을 만들려면 다음 명령을 실행합니다.

```azurecli-interactive
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

이전 명령은 다음 정보를 사용합니다.

- **이름** - 정책 할당에 대한 이름을 표시합니다. 이 경우에 *Managed Disks 할당이 없는 Virtual Machines 감사*를 사용합니다.
- **정책** – 할당을 만드는 데 기준으로 사용되는 정책 정의 ID입니다. 이 경우 정책 정의는 *Managed Disks가 없는 Virtual Machines 감사*입니다. 정책 정의 ID를 가져오려면 이 명령을 실행합니다. `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 구독에서 리소스 그룹까지 다양한 범위가 있습니다. 리소스 그룹의 이름으로 &lt;범위&gt;를 바꿉니다.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

이 새 할당에서 준수되지 않는 리소스를 보려면 다음 명령을 실행하여 정책 할당 ID를 가져옵니다.

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit Virtual Machines without Managed Disks' }
$policyAssignment.PolicyAssignmentId
```

정책 할당 ID에 대한 자세한 내용은 [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment)를 참조합니다.

다음으로 JSON 파일로 출력되는 비준수 리소스의 리소스 ID를 가져오려면 다음 명령을 실행합니다.

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

결과는 다음 예제와 유사합니다.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

결과는 Azure Portal 보기에서 **비준수 리소스** 아래 나열된 것과 비교할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 가이드는 이 빠른 시작에 기반하여 작성되었습니다. 후속 자습서를 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속 사용하지 않으려면 다음 명령을 실행하여 만든 할당을 삭제합니다.

```azurecli-interactive
az policy assignment delete –name 'Audit Virtual Machines without Managed Disks Assignment' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

**앞으로** 만들 리소스가 규정을 준수하게 하고 정책 할당에 대해 자세히 알아보려면 다음 주제에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](create-manage-policy.md)