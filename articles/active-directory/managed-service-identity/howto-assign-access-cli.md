---
title: Azure CLI를 사용하여 MSI에 Azure 리소스 액세스 권한을 할당하는 방법
description: Azure CLI를 사용하여 특정 리소스에 MSI를 할당하고 다른 리소스에 대한 액세스 권한을 할당하기 위한 단계별 지침을 제공합니다.
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
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: 947e0140c7943954be5eb285bb7ec514b74e9022
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Azure CLI를 사용하여 MSI(관리 서비스 ID)에 리소스 액세스 권한 할당

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

MSI를 사용하여 Azure 리소스를 구성한 후에는 모든 보안 주체와 마찬가지로 MSI에 다른 리소스 액세스 권한을 제공할 수 있습니다. 이 예제에서는 Azure CLI를 사용하여 Azure 가상 머신 또는 가상 머신 확장 집합의 MSI 액세스 권한을 Azure 저장소 계정에 부여하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
- 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.13 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC를 사용하여 MSI에 다른 리소스 액세스 권한 할당

[Azure 가상 머신](qs-configure-cli-windows-vm.md) 또는 [Azure 가상 머신 확장 집합](qs-configure-cli-windows-vmss.md)과 같은 Azure 리소스에서 MSI를 사용하도록 설정되면 다음을 수행합니다. 

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az_login)을 사용하여 먼저 Azure에 로그인합니다. VM 또는 가상 머신 확장 집합을 배포하려는 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. 다음 예제에서는 저장소 계정 액세스 권한을 Azure 가상 머신에 부여합니다. 먼저 [az resource list](/cli/azure/resource/#az_resource_list)를 사용하여 "myVM"이라는 가상 머신에 대한 서비스 사용자를 가져옵니다.

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Azure 가상 머신 확장 집합의 경우 "DevTestVMSS"라는 가상 머신 확장 집합에 대한 서비스 사용자를 가져오는 것을 제외하고는 명령이 동일합니다.
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. 서비스 사용자 ID를 가져오면 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 가상 머신 또는 가상 머신 확장 집합 "읽기 권한자" 액세스 권한을 "myStorageAcct"라는 저장소 계정에 부여합니다.

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>문제 해결

리소스의 MSI가 사용 가능한 ID 목록에 표시되지 않으면 MSI가 올바르게 사용하도록 설정되었는지 확인하세요. 여기서는 [Azure Portal](https://portal.azure.com)에 설정된 Azure 가상 머신 또는 가상 머신 확장 집합으로 돌아가서 다음을 수행할 수 있습니다.

- "구성" 페이지에서 MSI enabled = "Yes"인지 확인합니다.
- "확장" 페이지를 살펴보고 MSI 확장이 성공적으로 배포되었는지 확인합니다(**확장** 페이지는 Azure 가상 머신 확장 집합에서 사용할 수 없음).

이 두 항목 중 하나가 올바르지 않으면 리소스에서 MSI를 재배포하거나 배포 오류 관련 문제를 해결해야 할 수 있습니다.

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.
- Azure 가상 머신에서 MSI를 사용하도록 설정하려면 [Azure CLI를 사용하여 Azure VM MSI(관리 서비스 ID) 구성](qs-configure-cli-windows-vm.md)을 참조하세요.
- Azure 가상 머신 확장 집합에서 MSI를 사용하도록 설정하려면 [Azure Portal을 사용하여 Azure 가상 머신 확장 집합 MSI(관리 서비스 ID) 구성](qs-configure-portal-windows-vmss.md)을 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

