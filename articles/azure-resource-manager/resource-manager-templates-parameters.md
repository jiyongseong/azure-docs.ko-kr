---
title: Azure Resource Manager 템플릿 매개 변수 섹션 | Microsoft Docs
description: 선언적 JSON 구문을 사용하여 Azure Resource Manager 템플릿의 매개 변수 섹션에 대해 설명합니다.
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
ms.date: 05/18/2018
ms.author: tomfitz
ms.openlocfilehash: 193e74d94017cf0ca8ec0600c7e5a3dc4b7a6dea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359206"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 매개 변수 섹션
템플릿의 매개 변수 섹션에서는 리소스를 배포할 때 입력할 수 있는 값을 지정합니다. 이러한 매개 변수 값을 사용하여 개발, 테스트 및 프로덕션 등의 특정 환경에 맞게 조정되는 값을 제공함으로써 배포를 사용자 지정할 수 있습니다. 템플릿에서 매개 변수를 제공할 필요는 없지만 매개 변수가 없으면 템플릿이 항상 이름, 위치 및 속성이 같은 동일한 리소스를 배포합니다.

템플릿에서 매개 변수는 255개로 제한됩니다. 이 문서에 표시된 것처럼 여러 속성이 포함된 개체를 사용하여 매개 변수 수를 줄일 수 있습니다.

## <a name="define-and-use-a-parameter"></a>매개 변수 정의 및 사용

다음 예제에서는 간단한 매개 변수 정의를 보여 줍니다. 매개 변수 이름을 정의하고 문자열 값을 사용하도록 지정합니다. 매개 변수는 용도에 적합한 값만 허용합니다. 매개 변수는 배포 중에 값을 제공하지 않으면 기본값을 지정합니다. 마지막으로 매개 변수에는 용도에 대한 설명이 포함됩니다. 

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
  }   
}
```

템플릿에서 다음 구문을 사용하여 매개 변수 값을 참조합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>사용 가능한 속성

위 예제에서는 매개 변수 섹션에서 사용할 수 있는 일부 속성만 보여 주었습니다. 사용 가능한 속성은 다음과 같습니다.

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| parameterName |예 |매개 변수의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| 형식 |예 |매개 변수 값의 유형입니다. 허용되는 유형 및 값은 **string**, **securestring**, **int**, **bool**, **object**, **secureObject** 및 **array**입니다. |
| defaultValue |아니오 |매개 변수 값을 제공하지 않는 경우 매개 변수의 기본값입니다. |
| allowedValues |아니오 |올바른 값을 제공하도록 매개 변수에 대해 허용되는 값의 배열입니다. |
| minValue |아니오 |Int 형식 매개 변수의 최소값이며, 이 값이 포함됩니다. |
| maxValue |아니오 |Int 형식 매개 변수의 최대값이며, 이 값이 포함됩니다. |
| minLength |아니오 |string, securestring 및 array 형식 매개 변수의 최소 길이이며, 이 값이 포함됩니다. |
| maxLength |아니오 |string, securestring 및 array 형식 매개 변수의 최대 길이이며, 이 값이 포함됩니다. |
| description |아니오 |포털에서 사용자에게 표시되는 매개 변수의 설명입니다. |

## <a name="template-functions-with-parameters"></a>매개 변수가 있는 템플릿 함수

매개 변수에 기본값을 제공하는 경우 대부분의 템플릿 함수를 사용할 수 있습니다. 다른 매개 변수 값을 사용하여 기본값을 빌드할 수 있습니다. 다음 템플릿은 기본값인 함수의 사용을 보여 줍니다.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

매개 변수 섹션에서는 `reference` 함수를 사용할 수 없습니다. 매개 변수는 배포 전에 평가되므로 `reference` 함수에서 리소스의 런타임 상태를 가져올 수 없습니다. 

## <a name="objects-as-parameters"></a>개체를 매개 변수로 사용

관련 값을 개체로 전달하면 더 쉽게 구성할 수 있습니다. 이렇게 하면 템플릿의 매개 변수 수도 줄어듭니다.

템플릿에서 매개 변수를 정의하고 배포 중에 단일 값 대신 JSON 개체를 지정하세요. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

그런 다음 점 연산자를 사용하여 매개 변수의 하위 속성을 참조하세요.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>권장 사항
다음 정보는 매개 변수로 작업하는 경우 도움이 될 수 있습니다.

* 매개 변수 사용을 최소화합니다. 가능하면 항상, 변수 또는 리터럴 값을 사용합니다. 다음과 같은 시나리오에 대해서만 매개 변수를 사용합니다.
   
   * 환경(SKU, 크기, 용량)에 따라 변수를 사용하려는 설정
   * 쉽게 식별할 수 있도록 지정하려는 리소스 이름
   * 다른 작업을 완료하는 데 자주 사용하는 값(예: 관리 사용자 이름)
   * 비밀(예: 암호)
   * 한 리소스 유형의 인스턴스를 여러 개 만들 때 사용할 값의 수 또는 배열
* 매개 변수 이름에 카멜식 대/소문자를 사용합니다.
* 매개 변수에 모든 메타데이터의 설명을 제공합니다.

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 매개 변수의 기본값을 정의합니다(암호 및 SSH 키 제외). 기본값을 제공하면 매개 변수가 배포 중에 선택 사항이 됩니다. 기본값은 빈 문자열일 수 있습니다. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 모든 암호(password) 및 암호(secret)에 대해 **securestring** 을 사용합니다. JSON 개체에 중요한 데이터를 전달하는 경우 **secureObject** 유형을 사용합니다. 리소스 배포 후에는 securestring 또는 secureObject 형식의 템플릿 매개 변수를 읽을 수 없습니다. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 매개 변수를 사용하여 위치를 지정하고, 동일한 위치에 있을 가능성이 있는 리소스와 가능한 많이 해당 매개 변수 값을 공유합니다. 이렇게 하면 사용자에게 위치 정보를 제공하라고 요청하는 횟수가 최소화됩니다. 리소스 종류가 제한된 수의 위치에서만 지원되는 경우 유효한 위치를 템플릿에 직접 지정하거나 다른 위치 매개 변수를 추가할 수 있습니다. 조직에서 사용자에 허용되는 지역을 제한하는 경우 **resourceGroup().location** 식으로 인해 사용자가 템플릿을 배포하지 못할 수 있습니다. 예를 들어 한 사용자가 한 지역에 리소스 그룹을 만듭니다. 두 번째 사용자는 해당 리소스 그룹에 배포해야 하지만 해당 지역에 대한 액세스 권한이 없습니다. 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* 리소스 유형의 API 버전에 대해서는 매개 변수나 변수를 사용하지 마세요. 리소스 속성 및 값은 버전 번호에 따라 달라질 수 있습니다. API 버전이 매개 변수 또는 변수로 설정되면 코드 편집기의 IntelliSense에서 올바른 스키마를 확인할 수 없습니다. 대신, 템플릿에 API 버전을 하드 코드합니다.
* 배포 명령의 매개 변수와 일치하는 매개 변수 이름을 템플릿에 지정하지 마세요. Resource Manager는 템플릿 매개 변수에 **FromTemplate**이라는 접미사를 추가하여 이러한 이름 충돌을 해결합니다. 예를 들어 템플릿에 **ResourceGroupName**이라는 매개 변수가 포함되면, [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet의 **ResourceGroupName** 매개 변수와 충돌합니다. 배포하는 동안 **ResourceGroupNameFromTemplate**에 대한 값을 제공하라는 메시지가 표시됩니다.

## <a name="example-templates"></a>예제 템플릿

이러한 예제 템플릿은 매개 변수 사용에 대한 일부 시나리오를 보여 줍니다. 다른 시나리오에서 처리되는 방식을 테스트하려면 매개 변수를 배포하세요.

|Template  |설명  |
|---------|---------|
|[parameters with functions for default values](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json)(기본값에 대한 함수가 있는 매개 변수) | 매개 변수의 기본값을 정의할 때 템플릿 함수를 사용하는 방법을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |
|[parameter object](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json)(매개 변수 개체) | 매개 변수에 대한 개체 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |

## <a name="next-steps"></a>다음 단계

* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 배포 중에 매개 변수 값을 입력하는 방법은 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요. 
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 매개 변수 개체 사용에 대한 자세한 내용은 [Use an object as a parameter in an Azure Resource Manager template](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)(Azure Resource Manager 템플릿에서 개체를 매개 변수로 사용)을 참조하세요.