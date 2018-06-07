---
title: Azure Resource Manager 템플릿 변수 | Microsoft Docs
description: 선언적 JSON 구문을 사용하여 Azure Resource Manager 템플릿에서 변수를 정의하는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 08728a3c0b4d4578939004e2d1b1ee2d30a682ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 변수 섹션
변수 섹션에서 템플릿을 통해 사용할 수 있는 값을 생성합니다. 변수를 정의할 필요는 없지만 종종 변수를 통해 복잡한 식을 줄이면 템플릿이 단순화됩니다.

## <a name="define-and-use-a-variable"></a>변수를 정의하고 사용합니다.

다음 예제는 변수 정의를 보여 줍니다. 저장소 계정 이름의 문자열 값을 만듭니다. 여러 템플릿 함수를 사용하여 매개 변수 값을 가져오고 고유 문자열에 연결합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

리소스를 정의할 때 변수를 사용합니다.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>사용 가능한 정의

앞의 예제에서는 변수를 정의하는 방법을 살펴보았습니다. 다음 정의 중 하나를 사용할 수 있습니다.

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>구성 변수

복합 JSON 형식을 사용하여 환경에 대한 관련 값을 정의할 수 있습니다. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

매개 변수에서 사용할 구성 값을 나타내는 값을 만듭니다.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

다음과 같이 현재 설정을 검색합니다.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>변수 정의에서 copy 요소 사용

**copy** 구문을 사용하여 여러 요소의 배열이 포함된 변수를 만들 수 있습니다. 요소 수에 대한 개수를 제공합니다. 각 요소에는 **input** 개체 내의 속성이 포함됩니다. 변수 내에 copy를 사용하거나 변수를 만들 수 있습니다. 변수를 정의하고 해당 변수 내에서 **copy**를 사용할 경우 배열 속성이 있는 개체를 만듭니다. 최상위 수준에서 **copy**를 사용하고 이 요소 내에서 하나 이상의 변수를 정의할 경우 하나 이상의 배열을 만듭니다. 다음 예제에서는 두 가지 방법을 모두 보여 줍니다.

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('disks')]"
                }
            }
        ]
    },
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

**disk-array-on-object** 변수는 **disks** 배열이 있는 다음 개체를 포함합니다.

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

**disks-top-level-array** 변수는 다음 배열을 포함합니다.

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

복사를 사용하여 변수를 만들 때 개체를 둘 이상 지정할 수도 있습니다. 다음 예제에서는 두 개의 배열을 변수로 정의합니다. 하나는 **disks-top-level-array**이며 5개의 요소가 있습니다. 다른 하나는 **a-different-array**이며 3개의 요소가 있습니다.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

이 방법은 매개 변수 값을 사용하고 템플릿 값이 올바른 형식인지 확인해야 하는 경우 적합합니다. 다음 예제에서는 보안 규칙 정의에 사용할 매개 변수 값의 형식을 지정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="recommendations"></a>권장 사항
다음 정보는 변수로 작업하는 경우 도움이 될 수 있습니다.

* 두 번 이상 사용해야 하는 값의 경우 템플릿에서 변수를 사용합니다. 값을 한 번만 사용할 경우에는 하드 코드된 값을 사용해야 템플릿을 더 쉽게 읽을 수 있습니다.
* 템플릿의 **변수** 섹션에는 [reference](resource-group-template-functions-resource.md#reference) 함수를 사용할 수 없습니다. **reference** 함수는 리소스의 런타임 상태에서 해당 값을 파생합니다. 하지만 템플릿의 초기 구문 분석 중에 변수가 확인됩니다. 템플릿의 **resources** 또는 **outputs** 섹션에서 직접 **reference** 함수에 필요한 값을 생성합니다.
* 고유해야 하는 리소스 이름에 대한 변수를 포함합니다.

## <a name="example-templates"></a>예제 템플릿

이러한 예제 템플릿은 변수 사용에 대한 일부 시나리오를 보여 줍니다. 변수가 다른 시나리오에서 처리되는 방식을 테스트하려면 변수를 배포하세요. 

|Template  |설명  |
|---------|---------|
| [변수 정의](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 다양한 변수 형식을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [구성 변수](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 구성 값을 정의하는 변수의 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [네트워크 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 및 [매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 보안 규칙을 네트워크 보안 그룹에 할당하기 위한 올바른 형식으로 배열을 구성합니다. |


## <a name="next-steps"></a>다음 단계
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 배포 중 여러 템플릿을 결합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 다른 리소스 그룹 내에 있는 리소스를 사용해야 할 수도 있습니다. 이 시나리오에서는 일반적으로 여러 리소스 그룹에서 공유하는 저장소 계정 또는 가상 네트워크에서 작업합니다. 자세한 내용은 [resourceId 함수](resource-group-template-functions-resource.md#resourceid)를 참조하세요.