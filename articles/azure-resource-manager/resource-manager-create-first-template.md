---
title: 첫 번째 Azure Resource Manager 템플릿 만들기 | Microsoft Docs
description: 첫 번째 Azure Resource Manager 템플릿을 만드는 단계별 가이드입니다. 저장소 계정에 대한 템플릿 참조를 사용하여 템플릿을 만드는 방법을 보여 줍니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/30/2018
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 32ff8621eb446f9bcc45398173f47b761b929ac5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358050"
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>첫 번째 Azure Resource Manager 템플릿을 만들고 배포
이 항목에서는 첫 번째 Azure Resource Manager 템플릿을 만드는 단계를 안내합니다. Resource Manager 템플릿은 솔루션에 배포해야 하는 리소스를 정의하는 JSON 파일입니다. Azure 솔루션 배포 및 관리와 관련된 개념을 이해하려면 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요. 기존 리소스가 있고 해당 리소스에 대한 템플릿을 가져오려는 경우 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.

템플릿을 만들고 수정하려면 JSON 편집기가 필요합니다. [Visual Studio Code](https://code.visualstudio.com/)는 간단한 오픈 소스 크로스 플랫폼 코드 편집기입니다. Visual Studio Code를 사용하여 Resource Manager 템플릿을 만드는 것이 좋습니다. 이 문서에서는 VS Code를 사용한다고 가정합니다. 다른 JSON 편집기(예: Visual Studio)가 있는 경우 해당 편집기를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Visual Studio Code. 필요한 경우 [https://code.visualstudio.com/](https://code.visualstudio.com/)에서 Visual Studio Code를 추가합니다.
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 로컬로 설치된 [Azure PowerShell](/powershell/azure/install-azurerm-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli). 템플릿이 로컬 파일로 저장되기 때문에 이 자습서에 대한 로컬 설치가 필요합니다. Cloud Shell을 사용하려면 [저장소 계정에 템플릿을 로드](resource-group-template-deploy-cli.md#deploy-template-from-cloud-shell)해야 합니다.

## <a name="create-template"></a>템플릿 만들기

구독에 저장소 계정을 배포하는 간단한 템플릿부터 시작하겠습니다.

1. **파일** > **새 파일**을 선택합니다. 

   ![새 파일](./media/resource-manager-create-first-template/new-file.png)

2. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Storage 계정 이름에는 설정을 어렵게 하는 몇 가지 제한 사항이 있습니다. 이름은 길이가 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용하고 고유해야 합니다. 이전 템플릿은 [uniqueString](resource-group-template-functions-string.md#uniquestring) 함수를 사용하여 해시 값을 생성합니다. 이 해시 값에 더 많은 의미를 부여하기 위해 *storage*라는 접두사를 추가합니다. 

3. 이 파일을 로컬 폴더에 **azuredeploy.json**으로 저장합니다.

   ![템플릿 저장](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>템플릿 배포

이제 이 템플릿을 배포할 수 있습니다. PowerShell 또는 Azure CLI를 사용하여 리소스 그룹을 만듭니다. 그런 다음 리소스 그룹에 저장소 계정을 배포합니다.

* PowerShell의 경우 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.

   ```powershell
   Connect-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* 로컬에 설치된 Azure CLI의 경우 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

배포가 완료되면 저장소 계정이 리소스 그룹에 있습니다.

## <a name="customize-the-template"></a>템플릿 사용자 지정

템플릿이 정상적으로 작동하지만 유연하지가 않습니다. 로컬 중복 저장소를 항상 미국 중남부에 배포합니다. 이름으로는 항상 *storage* 뒤에 해시 값이 붙습니다. 템플릿을 다양한 시나리오에 사용할 수 있도록 템플릿에 매개 변수를 추가합니다.

다음 예제에서는 두 매개 변수가 있는 매개 변수 섹션을 보여 줍니다. 첫 번째 `storageSKU` 매개 변수는 중복 유형을 지정할 수 있습니다. 저장소 계정에 유효한 값으로 전달할 수 있는 값을 제한합니다. 또한 기본값을 지정합니다. 두 번째 `storageNamePrefix` 매개 변수는 최대 11자를 허용하도록 설정됩니다. 기본값을 지정합니다.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

변수 섹션에서 `storageName` 변수를 추가합니다. 이 변수는 매개 변수의 접두사 값과 [uniqueString](resource-group-template-functions-string.md#uniquestring) 함수의 해시 값을 결합합니다. 또한 [toLower](resource-group-template-functions-string.md#tolower) 함수를 사용하여 모든 문자를 소문자로 변환합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

저장소 계정에 이러한 새 값을 사용하려면 리소스 정의를 변경합니다.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

이제 저장소 계정 이름은 사용자가 추가한 변수로 설정됩니다. SKU 이름은 매개 변수 값으로 설정됩니다. 위치는 리소스 그룹과 동일한 위치로 설정됩니다.

파일을 저장합니다. 

템플릿은 이제 다음과 같이 표시됩니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>템플릿 다시 배포

다른 값을 사용하여 템플릿을 다시 배포합니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Azure CLI의 경우 

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>자동 완성 사용

지금까지 템플릿에서 한 작업은 이 문서에서 JSON을 복사하고 붙여 넣는 방법으로 구성되었습니다. 그러나 고유한 템플릿을 개발할 경우 리소스 형식에 사용할 수 있는 속성 및 값을 찾고 지정하는 것이 좋습니다. VS Code는 리소스 유형에 대한 스키마를 읽고 속성 및 값을 제공합니다. 자동 완성 기능을 확인하려면 템플릿의 속성 요소로 이동하고 새 줄을 추가합니다. 따옴표를 입력하면 VS Code에서 즉시 속성 요소 내에서 사용할 수 있는 이름을 제공합니다.

![사용 가능한 속성 표시](./media/resource-manager-create-first-template/show-properties.png)

**암호화**를 선택합니다. 콜론(:)을 입력하면 VS Code에서 새 개체를 추가하도록 합니다.

![개체 추가](./media/resource-manager-create-first-template/add-object.png)

탭 키 또는 Enter 키를 눌러서 개체를 추가합니다.

다시 따옴표를 입력하면 VS Code에서 암호화에 사용할 수 있는 속성을 제안합니다.

![암호화 속성 표시](./media/resource-manager-create-first-template/show-encryption-properties.png)

**서비스**를 선택하고 다음이 표시될 때까지 VS Code 확장에 따라 값을 추가합니다.

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

저장소 계정에 Blob 암호화를 사용하도록 설정했습니다. 그러나 VS Code가 문제를 식별했습니다. 해당 암호화에서 경고를 표시합니다.

![암호화 경고](./media/resource-manager-create-first-template/encryption-warning.png)

경고를 보려면 녹색 줄 위로 마우스를 가리킵니다.

![누락된 속성](./media/resource-manager-create-first-template/missing-property.png)

암호화 요소는 keySource 속성을 필요로 합니다. 서비스 개체 뒤에 쉼표를 추가하고 keySource 속성을 추가합니다. VS Code는 **"Microsoft.Storage"** 를 유효한 값으로 제공합니다. 완료되면 속성 요소는 다음과 같습니다.

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

최종 템플릿은 다음과 같습니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>암호화된 저장소 배포

다시 템플릿을 배포하고 새 저장소 계정 이름을 제공합니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Azure CLI의 경우 

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요 없는 경우 리소스 그룹을 삭제하여 배포된 리소스를 정리합니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Azure CLI의 경우 

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>다음 단계
* VS Code 확장을 설치하면 템플릿을 개발하는 데 도움이 됩니다. 자세한 내용은 [Visual Studio Code 확장을 사용하여 Azure Resource Manager 템플릿 만들기](resource-manager-vscode-extension.md)를 참조하세요.
* 템플릿 구조에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿 작성하기](resource-group-authoring-templates.md)를 참조하세요.
* 저장소 계정의 속성에 대한 자세한 내용은 [저장소 계정 템플릿 참조](/azure/templates/microsoft.storage/storageaccounts)를 참조하세요.
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
