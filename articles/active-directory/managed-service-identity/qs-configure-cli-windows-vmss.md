---
title: Azure CLI를 사용하여 Azure VMSS에서 시스템 및 사용자 할당 ID를 구성하는 방법
description: Azure CLI를 사용하여 Azure VMSS에서 시스템 및 사용자 할당 ID를 구성하기 위한 단계별 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: faf526082a9a38d5d98443ff2b74eac4eef1ca08
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157458"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Azure CLI를 사용하여 가상 머신 확장 집합 MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure CLI를 사용하여 Azure VMSS(Virtual Machine Scale Set)에서 다음과 같은 관리 서비스 ID 작업을 수행하는 방법을 알아봅니다.
- Azure VMSS에서 시스템 할당 ID를 사용 및 사용하지 않도록 설정
- Azure VMSS에서 사용자 할당 ID 추가 및 제거


## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
- 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.13 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>시스템 할당 ID

이 섹션에서는 Azure CLI를 사용하여 Azure VMSS에 시스템 할당 ID를 사용 및 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합을 만드는 동안 시스템 할당 ID를 사용하도록 설정

시스템 할당 ID를 사용하도록 설정된 가상 머신 확장 집합을 만들려면:

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az_login)을 사용하여 먼저 Azure에 로그인합니다. 가상 머신 확장 집합을 배포하려는 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az_group_create)를 사용하여 가상 머신 확장 집합 및 관련 리소스를 포함하고 배포하기 위한 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vmss create](/cli/azure/vmss/#az_vmss_create)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수에서 요청한 대로 시스템 할당 ID를 사용하여 *myVMSS*라는 가상 머신 확장 집합을 만듭니다. `--admin-username`및 `--admin-password` 매개 변수는 가상 머신 로그인을 위한 관리자 이름 및 암호 계정을 지정합니다. 이러한 값은 사용자 환경에 적절하게 업데이트합니다. 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하도록 설정

기존 Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하도록 설정해야 하는 경우:

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az_login)을 사용하여 먼저 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az vm identity assign](/cli/azure/vmss/identity/#az_vmss_identity_assign) 명령을 사용하여 기존 VM에 시스템 할당 ID를 사용하도록 설정합니다.

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하지 않도록 설정

> [!NOTE]
> 가상 머신 확장 집합에서 관리 서비스 ID를 사용하지 않도록 설정하는 기능은 현재 지원되지 않습니다. 그 사이에는 시스템 할당 및 사용자 할당 ID를 전환할 수 있습니다. 업데이트를 다시 확인하세요.

시스템 할당 ID는 더 이상 필요하지 않고 사용자 할당 ID는 여전히 필요한 가상 머신 확장 집합이 있는 경우 다음 명령을 수행합니다.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

MSI VM 확장을 제거하려면 [az vmss identity remove](/cli/azure/vmss/identity/#az_vmss_remove_identity) 명령을 사용하여 VMSS에서 시스템 할당 ID를 제거합니다.

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>사용자 할당 ID

이 섹션에서는 Azure CLI를 사용하여 사용자 할당 ID를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Azure VMSS를 만드는 동안 사용자 할당 ID 할당

이 섹션에서는 VMSS를 만들고 사용자 할당 ID를 이 VMSS에 할당하는 과정을 설명합니다. 사용하려는 VMSS가 이미 있는 경우 이 섹션을 건너뛰고 그 다음 단계를 진행합니다.

1. 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다. [az group create](/cli/azure/group/#az_group_create)를 사용하여 사용자 할당 ID를 포함하고 배포하는 데 사용할 [리소스 그룹](~/articles/azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. `<RESOURCE GROUP>` 및 `<LOCATION>` 매개 변수 값을 원하는 값으로 바꾸세요. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az-identity-create)를 사용하여 사용자 할당 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    > [!IMPORTANT]
    > 사용자 할당 ID 만들기는 영숫자와 하이픈(0-9 또는 a-z 또는 A-Z 또는 -) 문자만 지원합니다. 또한 VM/VMSS에 대한 할당이 제대로 작동하려면 이름의 길이가 24자로 제한되어야 합니다. 업데이트를 다시 확인하세요. 자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
응답에는 다음과 같이 생성된 사용자 할당 ID에 대한 세부 정보가 포함됩니다. 사용자 할당 ID에 할당된 리소스 `id` 값은 다음 단계에서 사용됩니다.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [az vmss create](/cli/azure/vmss/#az-vmss-create) 명령을 사용하여 VMSS를 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수에서 지정한 대로 새 사용자 할당 ID와 연결된 VMSS를 만듭니다. `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` 및 `<USER ASSIGNED IDENTITY ID>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY ID>`의 경우 이전 단계에서 만든 사용자 할당 ID의 리소스 `id` 속성을 사용합니다. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>기존 Azure VM에 사용자 할당 ID 할당

1. [az identity create](/cli/azure/identity#az-identity-create)를 사용하여 사용자 할당 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    > [!IMPORTANT]
    > 이름에 특수 문자(예: 밑줄)가 있는 사용자 할당 ID를 만드는 기능은 현재 지원되지 않습니다. 영숫자 문자를 사용하세요. 업데이트를 다시 확인하세요.  자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
응답에는 다음과 같이 생성된 사용자 할당 ID에 대한 세부 정보가 포함됩니다. 사용자 할당 ID에 할당된 리소스 `id` 값은 다음 단계에서 사용됩니다.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [az vmss identity assign](/cli/azure/vmss/identity#az_vm_assign_identity)을 사용하여 VMSS에 사용자 할당 ID를 할당합니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY ID>`는 이전 단계에서 만든 대로 사용자 할당 ID의 리소스 `id` 속성입니다.

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Azure VMSS에서 사용자 할당 ID 제거

> [!NOTE]
>  시스템 할당 ID가 없다면, 가상 머신 확장 집합에서 모든 사용자 할당 ID를 제거하는 것은 현재 지원되지 않습니다. 

VMSS에 여러 사용자 할당 ID가 있는 경우 [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove)를 사용하여 마지막 ID만 남기고 모두 제거할 수 있습니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<MSI NAME>`은 사용자 할당 ID의 이름 속성이며 `az vm show`를 사용하여 VM의 ID 섹션에서 찾을 수 있습니다.

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
VMSS에 시스템 할당 및 사용자 할당 ID가 모두 있는 경우 시스템 할당만 사용하도록 전환하여 모든 사용자 할당 ID를 제거할 수 있습니다. 다음 명령을 사용합니다. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>다음 단계

- [관리 서비스 ID 개요](overview.md)
- Azure 가상 머신 확장 집합 만들기 빠른 시작은 다음을 참조하세요. 

  - [CLI를 사용하여 가상 머신 확장 집합 만들기](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















