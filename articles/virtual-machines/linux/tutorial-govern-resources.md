---
title: 자습서 - Azure CLI 2.0을 사용하여 Azure 가상 머신 제어 | Microsoft Docs
description: 이 자습서에서는 Azure CLI 2.0을 사용하여 RBAC, 정책, 잠금 및 태그를 적용하여 Azure 가상 머신을 관리하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 4ce2b133ed4266028f1d99151939538fb8ce60f5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli-20"></a>자습서: Azure CLI 2.0을 사용한 Linux 가상 머신 제어에 대해 알아보기

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="understand-scope"></a>범위 이해

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

이 자습서에서는 이러한 설정이 완료된 후 쉽게 제거할 수 있도록 모든 관리 설정을 리소스 그룹에 적용합니다.

해당 리소스 그룹을 만들어 보겠습니다.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

현재 리소스 그룹이 비어 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

조직의 사용자에게 이러한 리소스에 대한 적절한 수준의 액세스 권한이 있는지 확인하려고 합니다. 사용자에게 무제한 액세스 권한은 부여하지 않으면서 동시에 사용자가 작업을 수행할 수 있는지 확인해야 합니다. [역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 통해 범위에서 특정 작업을 완료할 수 있는 권한이 있는 사용자를 관리할 수 있습니다.

역할 할당을 만들고 제거하려면 사용자에게 `Microsoft.Authorization/roleAssignments/*` 액세스가 있어야 합니다. 이 액세스는 소유자 또는 사용자 액세스 관리자 역할을 통해 부여됩니다.

가상 머신 솔루션을 관리하기 위해서는 일반적으로 필요한 액세스 권한을 제공하는 세 가지 리소스 특정 역할이 있습니다.

* [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [네트워크 기여자](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

개별 사용자에게 역할을 할당하는 대신 비슷한 동작을 수행해야 하는 사용자에게 [Azure Active Directory 그룹을 만들기](../../active-directory/active-directory-groups-create-azure-portal.md)가 더 쉽습니다. 그런 다음, 해당 그룹에 적절한 역할을 할당합니다. 이 문서를 단순화하려면 구성원이 없는 Azure Active Directory 그룹을 만들 수 있습니다. 여전히 해당 그룹에 역할 범위를 할당할 수 있습니다. 

다음 예에서는 메일 별칭이 *vmDemoGroup*인 *VMDemoContributors*라는 Azure Active Directory 그룹을 만듭니다. 메일 별칭은 그룹에 대한 별칭으로 사용됩니다.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

명령 프롬프트가 반환된 후 그룹이 Azure Active Directory 전체에 전파되는 데 몇 분 정도 걸립니다. 20초 또는 30초 동안 기다린 후 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용하여 새로운 Azure Active Directory 그룹을 리소스 그룹에 대한 가상 머신 참가자 역할에 할당합니다.  전파하기 전에 다음 명령을 실행하면 **<guid>주체가 디렉터리에 존재하지 않는다**는 오류 메시지가 표시됩니다. 명령을 다시 실행합니다.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

일반적으로 *네트워크 참가자*와 *Storage 계정 참가자*를 위한 프로세스를 반복해 배포된 리소스를 관리하도록 사용자가 할당됐는지 확인합니다. 이 문서에서는 이러한 단계를 건너뛸 수 있습니다.

## <a name="azure-policies"></a>Azure 정책

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>정책 적용

사용 중인 구독에 이미 여러 개의 정책 정의가 있습니다. 사용 가능한 정책 정의를 보려면 [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list) 명령을 사용합니다.

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

기존 정책 정의를 참조할 수 있습니다. 해당 정책 유형은 **BuiltIn** 또는 **사용자 지정** 중 하나입니다. 할당하고자 하는 조건을 설명하는 정책에 대한 정의를 검토해 보세요. 이 문서에서는 다음과 같은 정책을 할당할 수 있습니다.

* 모든 리소스의 위치를 제한합니다.
* 가상 머신에 대한 SKU를 제한합니다.
* 관리 디스크를 사용하지 않는 가상 머신을 감사합니다.

다음 예제에서는 표시 이름을 기준으로 세 가지 정책 정의를 검색합니다. [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) 명령을 사용하여 해당 정의를 리소스 그룹에 할당합니다. 일부 정책의 경우 매개 변수 값을 제공하여 허용된 값을 지정합니다.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

앞의 예제는 정책에 대한 매개 변수를 이미 알고 있다고 가정합니다. 매개 변수를 확인해야 할 경우 다음을 사용합니다.

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>가상 머신 배포

솔루션을 배포할 수 있도록 역할 및 정책을 할당했습니다. 기본 크기는 허용된 SKU 중 하나인 Standard_DS1_v2입니다. 명령은 기본 위치에 SSH 키가 없는 경우 해당 키를 만듭니다.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

배포를 완료한 후 솔루션에 추가 관리 설정을 적용할 수 있습니다.

## <a name="lock-resources"></a>리소스 잠금

[리소스 잠금](../../azure-resource-manager/resource-group-lock-resources.md)은 조직의 사용자가 실수로 중요한 리소스를 삭제하거나 수정하는 것을 방지합니다. 역할 기반 액세스 제어와 달리 리소스 잠금은 모든 사용자와 역할 전반에 제한을 적용합니다. 잠금 수준을 *CanNotDelete* 또는 *ReadOnly*로 설정할 수 있습니다.

관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다.

가상 머신 및 네트워크 보안 그룹을 잠그려면 [az lock create](/cli/azure/lock#az_lock_create) 명령을 사용합니다.

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

잠금을 테스트하려면 다음 명령을 실행해 봅니다.

```azurecli-interactive 
az group delete --name myResourceGroup
```

삭제 작업이 잠금 때문에 수행할 수 없음을 나타내는 오류가 표시 됩니다. 리소스 그룹은 특별히 잠금을 제거하는 경우에 삭제할 수 있습니다. 해당 단계는 [리소스 정리](#clean-up-resources)에 표시됩니다.

## <a name="tag-resources"></a>리소스 태그 지정

Azure 리소스에 [태그](../../azure-resource-manager/resource-group-using-tags.md)를 적용하여 범주별로 논리적으로 구성합니다. 각 태그는 이름과 값으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

가상 머신에 태그를 적용하려면 [az resource tag](/cli/azure/resource#az_resource_tag) 명령을 사용합니다. 리소스의 기존 태그는 유지되지 않습니다.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>태그로 리소스 찾기

태그 이름 및 값을 사용하여 리소스를 찾으려면 [az resource list](/cli/azure/resource#az_resource_list) 명령을 사용합니다.

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

태그 값으로 모든 가상 머신 중지와 같은 관리 작업에 대한 반환 값을 사용할 수 있습니다.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>태그 값으로 비용 보기

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>리소스 정리

잠금이 해제될 때까지는 잠긴 네트워크 보안 그룹을 삭제할 수 없습니다. 잠금을 제거하려면 잠금의 ID를 검색하고 [az lock delete](/cli/azure/lock#az_lock_delete) 명령을 입력합니다.

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다. VM에 대한 SSH 세션을 종료한 후 다음과 같이 리소스를 삭제합니다.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 VM 이미지를 만들었습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 역할에 사용자 할당
> * 표준을 적용하는 정책 적용
> * 잠금을 사용하여 중요한 리소스 보호
> * 결제 및 관리에 대한 리소스 태그 지정

고가용성 가상 머신에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [가상 머신 모니터링](tutorial-monitoring.md)

