---
title: Azure Resource Manager 템플릿 구조 및 구문 | Microsoft Docs
description: 선언적 JSON 구문을 사용하여 Azure Resource Manager 템플릿의 구조 및 속성을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 4fa610f144277b73bb6d555d46e63a01c413e07e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 구조 및 구문 이해
이 문서에서는 Azure Resource Manager 템플릿의 구조에 대해 설명합니다. 여기서는 템플릿의 다른 섹션 및 해당 섹션에서 사용할 수 있는 속성을 보여 줍니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다. 템플릿 만들기에 관한 단계별 연습은 [첫 번째 Azure Resource Manager 템플릿 만들기](resource-manager-create-first-template.md)를 참조하세요.

## <a name="template-format"></a>템플릿 형식
가장 간단한 구조의 템플릿에 포함되는 요소는 다음과 같습니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| $schema |예 |템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다. 위 예제에서 보여 주는 URL을 사용합니다. |
| contentVersion |예 |템플릿의 버전입니다(예: 1.0.0.0). 이 요소에 값을 제공할 수 있습니다. 템플릿을 사용하여 리소스를 배포할 때 이 값을 사용하면 정확한 템플릿이 사용되도록 할 수 있습니다. |
| 매개 변수 |아니오 |배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값입니다. |
| variables |아니오 |템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값입니다. |
| functions |아니오 |템플릿 내에서 사용할 수 있는 사용자 정의 함수입니다. |
| 리소스 |예 |리소스 그룹에 배포 또는 업데이트되는 리소스 종류입니다. |
| outputs |아니오 |배포 후 반환되는 값입니다. |

각 요소에는 사용자가 설정할 수 있는 속성이 포함되어 있습니다. 다음 예제에서는 템플릿에 대한 전체 구문이 포함됩니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
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
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

이 기사에서는 템플릿의 섹션에 대해 자세히 설명합니다.

## <a name="syntax"></a>구문
템플릿의 기본 구문은 JSON이지만, 식 및 함수를 사용하면 템플릿에서 사용할 수 있는 JSON 값을 확장할 수 있습니다.  식은 JSON 문자열 리터럴 내에서 작성되며, 첫 번째 및 마지막 문자가 각각 대괄호 `[` 및 `]`입니다. 식의 값은 템플릿을 배포할 때 평가됩니다. 문자열 리터럴로 작성되지만 식의 평가 결과는 실제 식에 따라 다른 JSON 형식(예: 배열 또는 정수)일 수 있습니다.  리터럴 문자열을 대괄호 `[`로 시작하되, 식으로 해석되지 않게 하려면 문자 `[[`로 시작하도록 추가 대괄호를 추가합니다.

일반적으로 배포를 구성하기 위한 작업을 수행하는 함수를 식과 함께 사용합니다. JavaScript에서와 마찬가지로 함수 호출은 `functionName(arg1,arg2,arg3)`과 같이 형식이 지정됩니다. 점과 [인덱스] 연산자를 사용하여 속성을 참조할 수 있습니다.

다음 예제에서는 값을 생성할 때 여러 함수를 사용하는 방법을 보여 줍니다.

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](resource-group-template-functions.md)를 참조하세요. 

## <a name="parameters"></a>매개 변수
템플릿의 매개 변수 섹션에서는 리소스를 배포할 때 입력할 수 있는 값을 지정합니다. 이러한 매개 변수 값을 사용하여 개발, 테스트 및 프로덕션 등의 특정 환경에 맞게 조정되는 값을 제공함으로써 배포를 사용자 지정할 수 있습니다. 템플릿에서 매개 변수를 제공할 필요는 없지만 매개 변수가 없으면 템플릿이 항상 이름, 위치 및 속성이 같은 동일한 리소스를 배포합니다.

다음 예제에서는 간단한 매개 변수 정의를 보여 줍니다.

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

매개 변수 정의에 대한 자세한 내용은 [Azure Resource Manager 템플릿의 매개 변수 섹션](resource-manager-templates-parameters.md)을 참조하세요.

## <a name="variables"></a>variables
변수 섹션에서 템플릿을 통해 사용할 수 있는 값을 생성합니다. 변수를 정의할 필요는 없지만 종종 변수를 통해 복잡한 식을 줄이면 템플릿이 단순화됩니다.

다음 예제는 간단한 변수 정의를 보여 줍니다.

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

변수 정의에 대한 자세한 내용은 [Azure Resource Manager 템플릿의 변수 섹션](resource-manager-templates-variables.md)을 참조하세요.

## <a name="functions"></a>Functions

템플릿 내에서 함수를 직접 만들 수 있습니다. 이러한 함수는 템플릿에서 사용할 수 있습니다. 일반적으로 템플릿 전체에서 반복하지 않으려는 복잡한 식을 정의합니다. 템플릿에서 지원되는 식 및 [함수](resource-group-template-functions.md)에서 사용자 정의 함수를 만듭니다.

사용자 함수를 정의할 때는 다음과 같은 몇 가지 제한 사항이 있습니다.

* 함수는 변수에 액세스할 수 없습니다.
* 함수는 [참조 함수](resource-group-template-functions-resource.md#reference)를 사용할 수 없습니다.
* 함수의 매개 변수는 기본값을 가질 수 없습니다.

함수는 템플릿 함수와 이름 충돌을 피하기 위해 네임스페이스 값이 필요합니다. 다음 예제에서는 저장소 계정 이름을 반환하는 함수를 보여줍니다.

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

다음을 사용하여 함수를 호출합니다.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
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
]
```

## <a name="resources"></a>리소스
리소스 섹션에서 배포되거나 업데이트되는 리소스를 정의합니다. 여기서는 올바른 값을 제공하기 위해 배포하는 유형을 이해해야 하기 때문에 템플릿이 더 복잡해질 수 있습니다.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

자세한 내용은 [Azure Resource Manager 템플릿의 리소스 섹션](resource-manager-templates-resources.md)을 참조하세요.

## <a name="outputs"></a>outputs
Outputs 섹션에서, 배포에서 반환되는 값을 지정합니다. 예를 들어, 배포된 리소스에 액세스하기 위한 URI를 반환할 수 있습니다.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

자세한 내용은 [Azure Resource Manager 템플릿의 출력 섹션](resource-manager-templates-outputs.md)을 참조하세요.

## <a name="template-limits"></a>템플릿 제한

템플릿의 크기는 1MB로, 각 매개 변수 파일의 크기는 64KB로 제한됩니다. 1MB의 제한은 반복적인 리소스 정의로 확장된 후 템플릿의 마지막 상태와 변수 및 매개변수 값에 적용됩니다. 

또한 다음으로 제한됩니다.

* 매개 변수 256개
* 변수 256개
* 리소스 800개(인쇄 매수 포함)
* 출력 값 64개
* 템플릿 식의 문자 24,576자

중첩된 템플릿을 사용하여 일부 템플릿 제한을 초과할 수 있습니다. 자세한 내용은 [Azure 리소스를 배포할 때 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요. 매개 변수, 변수 또는 출력의 수를 줄이려면 개체에 여러 값을 결합할 수 있습니다. 자세한 내용은 [매개 변수로 개체 사용](resource-manager-objects-as-parameters.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 배포 중 여러 템플릿을 결합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 다른 리소스 그룹 내에 있는 리소스를 사용해야 할 수도 있습니다. 이 시나리오에서는 일반적으로 여러 리소스 그룹에서 공유하는 저장소 계정 또는 가상 네트워크에서 작업합니다. 자세한 내용은 [resourceId 함수](resource-group-template-functions-resource.md#resourceid)를 참조하세요.
