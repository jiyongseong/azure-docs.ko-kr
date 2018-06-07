---
title: Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning 실험 만들기 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning 실험 계정을 만드는 예제를 제공합니다.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 6f3e08cf36a27d8f42291d13e82458e3fcebc03f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Azure Machine Learning 실험 서비스 구성

## <a name="overview"></a>개요
Azure Machine Learning 실험 서비스 계정, 작업 영역 및 프로젝트는 Azure 리소스입니다. 그처럼 리소스를 Resource Manager 템플릿을 사용하여 배포할 수 있습니다. Resource Manager 템플릿은 솔루션에 배포해야 하는 리소스를 정의하는 JSON 파일입니다. Azure 솔루션 배포 및 관리와 관련된 개념을 이해하려면 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.

## <a name="deploy-a-template"></a>템플릿 배포
템플릿을 배포하려면 Azure 명령줄 인터페이스 또는 Azure Portal에서 단지 몇 단계만 필요합니다.

### <a name="deploy-a-template-from-the-command-line"></a>명령줄에서 템플릿 배포
명령줄 인터페이스를 사용하면 단일 명령으로 템플릿을 기존 리소스 그룹에 배포할 수 있습니다.
템플릿 만들기에 관한 정보는 다음을 참조하세요.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Azure Portal에서 템플릿 배포
또한 선호하는 경우 Azure Portal을 사용하여 템플릿을 만들어 배포할 수 있습니다. 다음과 같이 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. **모든 서비스**를 선택하고 "템플릿"을 검색합니다.
3. **템플릿**을 선택합니다.
4. **+ 추가**를 클릭하고 템플릿 정보를 복사합니다. 
5. #4 단계에서 만든 템플릿을 선택하고 **배포**를 클릭합니다.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Azure Portal에서 기존 Azure 리소스로부터 템플릿 만들기
사용할 수 있는 Azure 컴퓨터 실험 계정이 이미 있다면 [Azure Portal](https://portal.azure.com)에서 해당 리소스로부터 템플릿을 생성할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 Azure 실험 계정으로 이동합니다.
2. **설정** 아래에서 **자동화 스크립트**를 클릭합니다.
3. 템플릿을 다운로드합니다. 

또는 템플릿 파일을 수동으로 편집할 수 있습니다. 템플릿 및 매개 변수 파일의 예제에 대해 다음을 참조합니다. 

### <a name="template-file-example"></a>템플릿 파일 예제
콘텐츠 아래에 "template-file.json"이라는 파일을 만듭니다. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>매개 변수 
아래 콘텐츠로 파일을 만들고 <parameters.json>으로 저장합니다. 

세 가지 값을 변경할 수 있습니다. 
* AccountName: 실험 계정 이름입니다.
* 위치: 지원되는 Azure 지역 중 하나.
* 저장소 계정 SKU: Azure ML은 프리미엄이 아닌 표준 저장소만 지원합니다. 자세한 내용은 [저장소 소개](https://docs.microsoft.com/azure/storage/common/storage-introduction)를 참조하세요. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>다음 단계
* [Azure Machine Learning 만들기 및 설치](../service/quickstart-installation.md)
