---
title: Azure 리소스 잠금으로 변경 방지 | Microsoft Docs
description: 모든 사용자 및 역할에 대해 잠금을 적용하여 사용자가 중요한 Azure 리소스를 업데이트하거나 삭제하지 못하도록 합니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 57e1226a263a045784f0278c68246c0e953f6e22
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>예기치 않은 변경을 방지하기 위해 리소스 잠그기 

관리자는 구독, 리소스 그룹 또는 리소스에 잠금을 설정하여 조직의 다른 사용자가 실수로 중요한 리소스를 삭제 또는 수정하지 못하게 할 수 있습니다. 잠금 수준을 **CanNotDelete** 또는 **ReadOnly**로 설정할 수 있습니다. 포털에서 잠금은 각각 **삭제** 및 **읽기 전용**으로 지칭됩니다.

* **CanNotDelete**는 권한이 부여된 사용자가 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다. 
* **ReadOnly**는 권한이 부여된 사용자가 리소스를 읽을 수 있지만 해당 리소스를 삭제하거나 업데이트할 수 없음을 의미합니다. 이 잠금을 적용하는 것은 권한 있는 모든 사용자에게 **판독기** 역할에서 부여한 권한을 제한하는 것과 유사합니다. 

## <a name="how-locks-are-applied"></a>잠금을 적용하는 방법

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

역할 기반 액세스 제어와 달리 관리 잠금을 사용하여 모든 사용자와 역할에 걸쳐 제한을 적용합니다. 사용자 및 역할에 대한 권한 설정에 대해 알아보려면 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

리소스 관리자 잠금은 `https://management.azure.com`에 전송된 작업으로 구성되는 관리 수준에서 발생된 작업에만 적용됩니다. 잠금은 리소스 자체 기능을 수행하는 방법을 제한하지 않습니다. 리소스 변경은 제한되지만 리소스 작업은 제한되지 않습니다. 예를 들어 SQL Database에 대한 읽기 전용 잠금을 사용하면 데이터베이스를 삭제하거나 수정하지 못하지만, 데이터베이스에서 데이터를 만들기, 업데이트 또는 삭제하지 못합니다. 이러한 작업이 `https://management.azure.com`로 전송되지 않기 때문에 데이터 전송이 허용됩니다.

**ReadOnly** 를 적용하면 읽기 작업처럼 보이는 일부 작업에 실제로 추가 작업이 필요하기 때문에 예기치 않은 결과가 발생할 수 있습니다. 예를 들어 저장소 계정에 **ReadOnly** 잠금을 설정하면 모든 사용자가 키를 나열하지 않도록 방지합니다. 반환되는 키를 쓰기 작업에 사용할 수 있기 때문에 목록 키 작업은 POST 요청을 통해 처리됩니다. 또 다른 예로 App Service 리소스에 **ReadOnly** 잠금을 설정하면 해당 상호 작용이 쓰기 액세스를 필요로 하기 때문에 Visual Studio 서버 탐색기가 리소스에 대한 파일을 표시하지 않도록 방지합니다.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>조직에서 잠금을 만들거나 삭제할 수 있는 사람
관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/*` 또는 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다.

## <a name="portal"></a>포털
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Template
다음 예제에서는 웹 사이트에 App Service 계획, 웹 사이트 및 잠금을 만드는 템플릿을 보여 줍니다. 잠금의 리소스 종류는 잠그려는 리소스의 리소스 종류로, **입니다**. 잠금의 이름은 리소스 이름을 **/Microsoft.Authorization/** 과 연결하여 만들어집니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

PowerShell에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

Azure CLI에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
[New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) 명령을 사용하여 Azure PowerShell을 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

잠금에 대한 정보를 가져오려면 [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock)를 사용합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```powershell
Get-AzureRmResourceLock
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

잠금을 삭제하려면 다음을 사용합니다.

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

[az lock create](/cli/azure/lock#az_lock_create) 명령을 사용하여 Azure CLI를 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

잠금에 대한 정보를 가져오려면 [az lock list](/cli/azure/lock#az_lock_list)를 사용합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup
```

잠금을 삭제하려면 다음을 사용합니다.

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
[관리 잠금을 위한 REST API](https://docs.microsoft.com/rest/api/resources/managementlocks)를 사용하여 배포된 리소스를 잠글 수 있습니다. REST API를 사용하여 잠금을 만들고, 삭제하고, 기존 잠금에 대한 정보를 검색할 수 있습니다.

잠금을 만들려면 다음을 실행합니다.

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. lock-name은 원하는 잠금 이름입니다. api-version에는 **2015-01-01**을 사용합니다.

요청에서 잠금에 대한 속성을 지정하는 JSON 개체를 포함합니다.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>다음 단계
* 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)
* 리소스가 존재하는 리소스 그룹을 변경하려면 [새 리소스 그룹으로 리소스 이동](resource-group-move-resources.md)
* 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 자세한 내용은 [Azure Policy란?](../azure-policy/azure-policy-introduction.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.

