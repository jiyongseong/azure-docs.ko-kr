---
title: Azure CLI 및 템플릿으로 리소스 배포 | Microsoft Docs
description: Azure Resource Manager와 Azure CLI를 사용하여 Azure에 리소스를 배포합니다. 리소스는 Resource Manager 템플릿에 정의됩니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: bcab9c7a890ea415401a54c2db8352e056cc8774
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>리소스 관리자 템플릿과 Azure CLI로 리소스 배포

이 문서에서는 Resource Manager 템플릿과 Azure CLI 2.0을 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다. Azure 솔루션 배포 및 관리와 관련된 개념에 익숙하지 않은 경우 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.  

배포하는 Resource Manager 템플릿은 컴퓨터의 로컬 파일 또는 GitHub와 같은 저장소에 있는 외부 파일일 수도 있습니다. 이 문서에서 배포하는 템플릿은 [샘플 템플릿](#sample-template) 섹션 또는 [GitHub의 저장소 계정 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)으로 사용할 수 있습니다.

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Azure CLI가 설치되어 있지 않으면 [Cloud Shell](#deploy-template-from-cloud-shell)을 사용할 수 있습니다.

## <a name="deploy-local-template"></a>로컬 템플릿 배포

Azure에 리소스를 배포할 때 다음을 수행합니다.

1. Azure 계정에 로그인합니다.
2. 배포된 리소스에 대한 컨테이너 역할을 하는 리소스 그룹을 만듭니다. 리소스 그룹의 이름은 영숫자, 마침표, 밑줄, 하이픈 및 괄호만 포함할 수 있습니다. 최대 90자까지 가능합니다. 마침표로 끝날 수 없습니다.
3. 만들려는 리소스를 정의하는 템플릿을 리소스 그룹에 배포

템플릿에는 템플릿 배포를 사용자 지정할 수 있도록 하는 매개 변수가 포함될 수 있습니다. 예를 들어 특정 환경(예: 개발, 테스트 및 프로덕션)에 맞게 조정되는 값을 제공할 수 있습니다. 샘플 템플릿은 저장소 계정 SKU에 대한 매개 변수를 정의합니다. 

다음 예제에서는 리소스 그룹을 만들고 로컬 컴퓨터에서 템플릿을 배포합니다.

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 메시지가 표시됩니다.

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>외부 템플릿 배포

로컬 컴퓨터에 Resource Manager 템플릿을 저장하는 대신, 외부 위치에 저장할 수 있습니다. 원본 제어 리포지토리(예: GitHub)에 템플릿을 저장할 수 있습니다. 또는 조직에서 공유 액세스에 대한 Azure Storage 계정에 저장할 수 있습니다.

외부 템플릿을 배포하려면 **template-uri** 매개 변수를 사용합니다. 예제의 URI를 사용하여 GitHub에서 샘플 템플릿을 배포합니다.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

앞의 예제에서는 템플릿에 중요한 데이터가 포함되어 있지 않으므로 대부분의 시나리오에 적합한 이 템플릿에 대해 공개적으로 액세스할 수 있는 URI가 필요합니다. 중요한 데이터(예: 관리자 암호)를 지정해야 하는 경우 해당 값을 안전한 매개 변수로 전달합니다. 그러나 템플릿에 공개적으로 액세스할 수 있도록 하지 않으려면 개인 저장소 컨테이너에 저장하여 보호할 수 있습니다. SAS(공유 액세스 서명) 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 개인 템플릿 배포](resource-manager-cli-sas-token.md)를 참조하세요.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Cloud Shell에서 다음 명령을 사용합니다.

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>둘 이상의 리소스 그룹 또는 구독에 배포

일반적으로 단일 리소스 그룹에 템플릿의 모든 리소스를 배포합니다. 그러나 일단의 리소스를 함께 배포하고 다른 리소스 그룹 또는 구독에 배치하려는 시나리오가 있습니다. 단일 배포의 5개 리소스 그룹에만 배포할 수 있습니다. 자세한 내용은 [둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포](resource-manager-cross-resource-group-deployment.md)를 참조하세요.

## <a name="parameter-files"></a>매개 변수 파일

매개 변수를 스크립트에 인라인 값으로 전달하는 것보다는, 매개 변수 값이 포함된 JSON 파일을 사용하는 것이 더 쉬울 수 있습니다. 매개 변수 파일은 다음과 같은 형식이어야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

매개 변수 섹션에는 템플릿에 정의된 매개 변수(storageAccountType)와 일치하는 매개 변수 이름이 포함되어 있습니다. 매개 변수 파일에는 매개 변수의 값이 포함됩니다. 이 값은 배포 동안 템플릿에 자동으로 전달됩니다. 다양한 배포 시나리오를 위해 여러 매개 변수 파일을 만든 후 적절한 매개 변수 파일을 전달할 수 있습니다. 

앞의 예제를 복사하고 `storage.parameters.json`이라는 파일로 저장합니다.

로컬 매개 변수 파일을 전달하려면 `@`을 사용하여 storage.parameters.json이라는 로컬 파일을 지정합니다.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>템플릿 배포 테스트

리소스를 실제로 배포하지 않고 템플릿과 매개 변수 값을 테스트하려면 [az 그룹 배포 유효성 검사](/cli/azure/group/deployment#az_group_deployment_validate)를 사용합니다. 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

오류가 감지되지 않으면 이 명령은 테스트 배포에 대한 정보를 반환합니다. 특히 **error** 값은 null입니다.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

오류가 감지되면 명령은 오류 메시지를 반환합니다. 예를 들어 저장소 계정 SKU에 대해 잘못된 값을 제공하려고 하면 다음 오류가 반환됩니다.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

템플릿에 구문 오류가 있으면 명령은 템플릿을 구문 분석할 수 없다는 오류를 반환합니다. 메시지는 줄 번호 및 구문 분석 오류의 위치를 나타냅니다.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

완료 모드를 사용하려면 `mode` 매개 변수를 사용합니다.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>샘플 템플릿

다음 템플릿은 이 문서의 예제에 사용됩니다. 복사한 후 storage.json이라는 파일로 저장합니다. 이 템플릿을 만드는 방법을 이해하려면 [첫 번째 Azure Resource Manager 템플릿 만들기](resource-manager-create-first-template.md)를 참조하세요.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>다음 단계
* 이 문서의 예제에서는 리소스를 기본 구독의 리소스 그룹으로 배포합니다. 다른 구독을 사용하려면 [여러 Azure 구독 관리](/cli/azure/manage-azure-subscriptions-azure-cli)를 참조하세요.
* 템플릿을 배포하는 전체 샘플 스크립트는 [Resource Manager 템플릿 배포 스크립트](resource-manager-samples-cli-deploy.md)를 참조하세요.
* 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 개인 템플릿 배포](resource-manager-cli-sas-token.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.
