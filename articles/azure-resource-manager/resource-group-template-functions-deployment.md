---
title: Azure Resource Manager 템플릿 함수 - 배포 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 배포 정보를 검색하는 데 사용할 수 있는 함수에 대해 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 725bc41f96359d4bf0d9d570f73f91dba5da2cab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿용 배포 함수 

Resource Manager는 템플릿의 섹션에서 값을 가져오고 배포와 관련된 값을 가져오기 위한 다음 함수를 제공합니다.

* [deployment](#deployment)
* [매개 변수](#parameters)
* [variables](#variables)

리소스, 리소스 그룹 또는 구독에서 값을 가져오려면 [리소스 함수](resource-group-template-functions-resource.md)를 참조하세요.

<a id="deployment" />

## <a name="deployment"></a>배포
`deployment()`

현재 배포 작업에 대한 정보를 반환합니다.

### <a name="return-value"></a>반환 값

이 함수는 배포하는 동안 전달되는 개체를 반환합니다. 반환된 개체의 속성은 배포 개체가 링크로 전달되는지 아니면 인라인 개체로 전달되는지에 따라 다릅니다. 배포 개체가 로컬 파일을 가리키기 위해 Azure PowerShell의 **-TemplateFile** 매개 변수를 사용할 때와 같이 인라인으로 전달되는 경우 개체는 다음 형식으로 반환됩니다.

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

개체가 원격 개체를 가리키기 위해 **-TemplateUri** 매개 변수를 사용할 때와 같이 링크로 전달되는 경우 개체는 다음 형식으로 반환됩니다. 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>설명

deployment()를 사용하여 부모 템플릿의 URI를 기반으로 하는 다른 템플릿에 연결할 수 있습니다.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="example"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json)에서는 배포 개체를 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

앞의 예제에서는 다음 개체를 반환합니다.

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Azure CLI에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

PowerShell에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

<a id="parameters" />

## <a name="parameters"></a>매개 변수
`parameters(parameterName)`

매개 변수 값을 반환합니다. 템플릿의 매개 변수 섹션에서 지정된 매개 변수 이름을 정의해야 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| parameterName |예 |string |반환할 매개 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정한 매개 변수의 값입니다.

### <a name="remarks"></a>설명

일반적으로 매개 변수를 사용하여 리소스 값을 설정합니다. 다음 예제에서는 웹 사이트의 이름을 배포 중에 전달된 매개 변수 값으로 설정합니다.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json)에서는 매개 변수 함수의 간소화된 사용을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | type | 값 |
| ---- | ---- | ----- |
| stringOutput | 문자열 | 옵션 1 |
| intOutput | int | 1 |
| objectOutput | Object | {“one”: “a”, “two”: “b”} |
| arrayOutput | 배열 | [1, 2, 3] |
| crossOutput | 문자열 | 옵션 1 |

Azure CLI에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

PowerShell에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

변수의 값을 반환합니다. 템플릿의 변수 섹션에서 지정된 변수 이름을 정의해야 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| variableName |예 |문자열 |반환할 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정한 변수의 값입니다.

### <a name="remarks"></a>설명

일반적으로 복잡한 값을 한 번만 구성하여 템플릿을 간소화하기 위해 변수를 사용합니다. 다음 예제에서는 저장소 계정에 대한 고유한 이름을 생성합니다.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json)은 각기 다른 변수 값을 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | type | 값 |
| ---- | ---- | ----- |
| exampleOutput1 | 문자열 | myVariable |
| exampleOutput2 | 배열 | [1, 2, 3, 4] |
| exampleOutput3 | 문자열 | myVariable |
| exampleOutput4 |  Object | {“property1”: “value1”, “property2”: “value2”} |

Azure CLI에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

PowerShell에서 이 예제 템플릿을 배포하려면 다음 기능을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>다음 단계
* Azure Resource Manager 템플릿의 섹션에 대한 설명은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

