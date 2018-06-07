---
title: Azure CLI를 사용하여 리소스 관리 | Microsoft Docs
description: Azure 명령줄 인터페이스 (CLI)를 사용하여 Azure 리소스 및 그룹 관리
editor: ''
manager: timlt
documentationcenter: ''
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: 90dd1b6b7e65178f6b339e4ac0bb781fb74a25a6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Azure 리소스 및 리소스 그룹 관리를 위해 Azure CLI 사용

이 문서에서는 Azure CLI 및 Azure Resource Manager를 사용하여 솔루션을 관리하는 방법을 알아봅니다. Resource Manager에 익숙하지 않은 경우에는 [Resource Manager 개요](resource-group-overview.md) 참조하세요. 이 문서에서는 관리 작업에 중점을 둡니다. 다음을 수행합니다.

1. 리소스 그룹 만들기
2. 리소스 그룹에 리소스 추가
3. 리소스에 태그 추가
4. 이름 또는 태그 값을 기반으로 리소스 쿼리
5. 리소스에 대하 잠금 적용 및 제거
6. 리소스 그룹 삭제

이 문서에서는 Resource Manager 템플릿을 구독에 배포하는 방법을 보여 주지 않습니다. 해당 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](resource-group-template-deploy-cli.md)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬에서 설치하고 사용하려면 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="set-subscription"></a>구독 설정

구독이 둘 이상인 경우에는 다른 구독으로 전환할 수 있습니다. 우선 계정에 대한 모든 구독을 살펴보겠습니다.

```azurecli-interactive
az account list
```

사용되는 구독 및 사용이 해제된 구독 목록이 반환됩니다.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

구독 하나가 기본 구독으로 표시됩니다. 이 구독은 작업의 현재 컨텍스트입니다. 다른 구독으로 전환하려면 **az account set** 명령을 사용하여 구독 이름을 제공합니다.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

현재 구독 컨텍스트를 표시하려면 매개 변수 없이 **az account show**를 사용합니다.

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

구독에 리소스를 배포하려면 리소스를 포함하는 리소스 그룹을 만들어야 합니다.

리소스 그룹을 만들려면 **az group create** 명령을 사용합니다. 이 명령은 **name** 매개 변수를 사용하여 리소스 그룹에 대한 이름을 지정하고 **location** 매개 변수를 사용하여 위치를 지정합니다.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

다음 형식으로 출력됩니다.

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

나중에 리소스 그룹을 검색해야 하는 경우 다음 명령을 사용합니다.

```azurecli-interactive
az group show --name TestRG1
```

구독의 리소스 그룹을 모두 가져오려면 다음 명령을 사용합니다.

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>리소스 그룹에 리소스 추가

리소스를 리소스 그룹에 추가하려면 **az resource create** 명령을 사용하거나 만드는 리소스의 종류에 해당하는 명령(예: **az storage account create**)을 사용합니다. 리소스의 종류에 해당하는 명령에는 새 리소스에 필요한 속성의 매개 변수가 포함되기 때문에 이 명령을 사용하는 것이 보다 간편할 수 있습니다. **az resource create**를 사용하는 경우 속성을 설정하라는 메시지를 표시하지 않으려면 설정할 속성을 모두 알아야 합니다.

하지만 스크립트를 통해 리소스를 추가하면 새 리소스가 Resource Manager 템플릿에 존재하지 않기 때문에 나중에 혼동을 일으킬 수 있습니다. 템플릿을 사용하면 솔루션을 안정적이고 반복적으로 배포할 수 있습니다.

다음 명령을 사용해 저장소 계정을 만듭니다. 예제에 표시된 이름을 사용하는 대신 저장소 계정에 대한 고유 이름을 제공합니다. 이름은 길이가 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다. 예제에 표시된 이름을 사용하면 해당 이름을 이미 사용 중이기 때문에 오류가 표시됩니다.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

나중에 이 리소스를 검색해야 하는 경우 다음 명령을 사용합니다.

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>태그 추가

태그를 사용하면 다양한 속성에 따라 리소스를 구성할 수 있습니다. 예를 들어 동일한 부서에 속하는 여러 리소스 그룹에 몇 가지 리소스를 둘 수 있습니다. 리소스에 부서 태그 및 값을 적용하여 동일한 범주에 속하는 것으로 표시할 수 있습니다. 또는 리소스가 프로덕션 환경에서 사용되는지 또는 테스트 환경에서 사용되는지를 표시할 수 있습니다. 이 문서에서는 태그를 하나의 리소스에만 적용하지만, 사용자 환경에서는 모든 리소스에 적용하는 것이 가장 적합합니다.

다음 명령은 저장소 계정에 두 개의 태그를 적용합니다.

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

태그는 단일 개체로 업데이트됩니다. 이미 태그가 포함된 리소스에 태그를 추가하려면 우선 기존 태그를 검색합니다. 기존 태그가 포함된 개체에 새 태그를 추가하고 리소스에 모든 태그를 다시 적용합니다.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>리소스 검색

다양한 검색 조건으로 리소스를 검색하려면 **az resource list** 명령을 사용합니다.

* 이름별로 리소스를 가져오려면 **name** 매개 변수를 제공합니다.

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* 리소스 그룹의 리소스를 모두 가져오려면 **resource-group** 매개 변수를 제공합니다.

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* 태그 이름 및 값을 사용하여 리소스를 모두 가져오려면 **tag** 매개 변수를 제공합니다.

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* 특정 리소스 종류에 해당하는 리소스를 모두 가져오려면 **resource-type** 매개 변수를 제공합니다.

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>리소스 ID 가져오기

많은 명령에서 리소스 ID를 매개 변수로 사용합니다. 리소스 ID를 가져와서 변수에 저장하려면 다음을 사용합니다.

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>리소스 잠금

중요한 리소스가 실수로 삭제되거나 수정되지 않도록 해야 하는 경우에는 리소스에 잠금을 적용합니다. **CanNotDelete** 또는 **ReadOnly**를 지정할 수 있습니다.

관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/*` 또는 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 소유자 및 사용자 액세스 관리자에게만 이러한 작업의 권한이 부여됩니다.

잠금을 적용하려면 다음 명령을 사용합니다.

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

앞의 예제에서 잠긴 리소스는 잠금이 제거될 때까지 삭제될 수 없습니다. 잠금을 제거하려면 다음을 사용합니다.

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

잠금 설정에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

## <a name="remove-resources-or-resource-group"></a>리소스 또는 리소스 그룹 제거
리소스 또는 리소스 그룹을 제거할 수 있습니다. 리소스 그룹을 제거하면 리소스 그룹에 포함된 리소스도 모두 제거됩니다.

* 리소스 그룹에서 리소스를 삭제하려면 삭제할 리소스 종류에 대해 delete 명령을 사용합니다. 이 명령은 리소스를 삭제하지만 리소스 그룹은 삭제하지 않습니다.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* 리소스 그룹 및 모든 해당 리소스를 삭제하려면 **az group delete** 명령을 사용합니다.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

두 명령 실행 시에는 모두 리소스 또는 리소스 그룹을 제거할지를 묻는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계
* Resource Manager 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 템플릿 배포에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy-cli.md)를 참조하세요.
* 기존 리소스를 새 리소스 그룹으로 이동할 수 있습니다. 예제를 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.