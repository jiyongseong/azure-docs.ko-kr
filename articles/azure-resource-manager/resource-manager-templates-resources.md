---
title: Azure Resource Manager 템플릿 리소스 | Microsoft Docs
description: 선언적 JSON 구문을 사용하여 Azure Resource Manager 템플릿의 리소스 섹션을 설명합니다.
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
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 12dc5921cc1977b53f0457d89537193eadded188
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 리소스 섹션

리소스 섹션에서 배포되거나 업데이트되는 리소스를 정의합니다. 여기서는 올바른 값을 제공하기 위해 배포하는 유형을 이해해야 하기 때문에 템플릿이 더 복잡해질 수 있습니다.

## <a name="available-properties"></a>사용 가능한 속성

다음과 같은 구조를 사용하여 리소스를 정의합니다.

```json
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
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| condition | 아니오 | 리소스 배포 여부를 나타내는 부울 값입니다. |
| apiVersion |예 |리소스를 만들 때 사용하는 REST API의 버전입니다. |
| 형식 |예 |리소스 유형입니다. 이 값은 리소스 공급자의 네임스페이스와 리소스 형식을 조합한 값입니다(예: **Microsoft.Storage/storageAccounts**). |
| 이름 |예 |리소스의 이름입니다. 이 이름은 RFC3986에 정의된 URI 구성 요소 제한을 따라야 합니다. 또한 리소스 이름을 외부에 노출하는 Azure 서비스는 다른 ID를 스푸핑하려는 시도가 아님을 확인하기 위해 이름의 유효성을 확인합니다. |
| location |다름 |제공된 리소스의 지역적 위치를 지원합니다. 사용 가능한 위치 중 하나를 선택할 수 있지만 대개는 사용자에게 가까운 하나를 선택하는 것이 좋습니다. 일반적으로 동일한 지역에서 서로 상호 작용하도록 리소스를 배치하는 것도 좋습니다. 대부분의 리소스 종류에는 위치가 필요하지만 일부 종류(예: 역할 할당)에는 위치가 필요하지 않습니다. |
| tags |아니오 |리소스와 연결된 태그입니다. 태그를 적용하여 구독에서 리소스를 논리적으로 구성합니다. |
| 설명 |아니오 |템플릿에서 리소스를 문서화하는 내용에 대한 참고 |
| 복사 |아니오 |인스턴스가 둘 이상 필요한 경우 만드는 리소스의 수입니다. 기본 모드는 병렬입니다. 일부 리소스를 동시에 배포하지 않으려면 직렬 모드를 지정합니다. 자세한 내용은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요. |
| dependsOn |아니오 |이 리소스를 배포하기 전에 배포해야 하는 리소스입니다. Resource Manager는 리소스 간의 종속성을 평가한 후 올바른 순서에 따라 리소스를 배포합니다. 리소스는 서로 종속되지 않을 경우 병렬로 배포됩니다. 이 값은 리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록입니다. 이 템플릿에 배포된 리소스만 나열합니다. 이 템플릿에 정의되지 않은 리소스는 이미 존재해야 합니다. 불필요한 종속성은 배포 속도를 느리게 만들고 순환 종속성을 만들기 때문에 추가하지 않습니다. 종속성 설정에 대한 지침은 [Azure Resource Manager 템플릿에서 종속성 정의](resource-group-define-dependencies.md)를 참조하세요. |
| properties |아니오 |리소스별 구성 설정입니다. 속성의 값은 리소스를 만들기 위해 REST API 작업(PUT 메서드)에 대한 요청 본문에 제공하는 값과 동일합니다. 복사 배열을 지정하여 속성의 여러 인스턴스를 만들 수도 있습니다. |
| sku | 아니오 | 일부 리소스에서는 SKU를 정의하는 값을 허용합니다. 예를 들어 저장소 계정에 대한 중복 유형을 지정할 수 있습니다. |
| kind | 아니오 | 일부 리소스에서는 배포하는 리소스 종류를 정의하는 값을 허용합니다. 예를 들어 만들 Cosmos DB 종류를 지정할 수 있습니다. |
| 계획 | 아니오 | 일부 리소스에서는 배포할 계획을 정의하는 값을 허용합니다. 예를 들어 가상 머신에 대한 마켓플레이스 이미지를 지정할 수 있습니다. | 
| 리소스 |아니오 |정의 중인 리소스에 종속되는 하위 리소스입니다. 부모 리소스의 스키마에서 허용되는 리소스 유형만 제공합니다. 자식 리소스의 정규화된 유형에는 부모 리소스 유형이 포함됩니다(예: **Microsoft.Web/sites/extensions**). 부모 리소스에 대한 종속성은 암시되지 않습니다. 해당 종속성을 명시적으로 정의해야 합니다. |

## <a name="resource-specific-values"></a>리소스별 값

**apiVersion**, **type** 및 **properties** 요소는 각 리소스 종류마다 다릅니다. 일부 리소스 종류에는 **sku**, **kind** 및 **plan** 요소를 사용할 수 있지만, 전부는 아닙니다. 이러한 속성의 값을 확인하려면 [템플릿 참조](/azure/templates/)를 참조하세요.

## <a name="resource-names"></a>리소스 이름
일반적으로 Resource Manager에서는 세 가지 유형의 리소스 이름으로 작업합니다.

* 고유해야 하는 리소스 이름
* 고유해야 할 필요는 없지만 리소스를 식별하는 데 도움이 될 수 있는 이름을 제공하도록 선택합니다.
* 일반적일 수 있는 리소스 이름

### <a name="unique-resource-names"></a>고유한 리소스 이름
데이터 액세스 끝점이 있는 모든 리소스 유형에 대해 고유한 리소스 이름을 제공해야 합니다. 고유 이름이 필요한 일반적인 리소스 유형은 다음과 같습니다.

* Azure Storage<sup>1</sup> 
* Azure App Service의 Web Apps 기능
* SQL Server
* Azure Key Vault
* Azure Redis 캐시(영문)
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> Storage 계정 이름은 24자 이내의 소문자여야 하며 하이픈은 포함할 수 없습니다.

이름을 설정할 때 고유한 이름을 수동으로 만들거나 [uniqueString()](resource-group-template-functions-string.md#uniquestring) 함수를 사용하여 이름을 생성할 수 있습니다. 또한 **uniqueString** 결과에 접두사 또는 접미사를 추가할 수도 있습니다. 고유한 이름을 수정하면 이름으로 리소스 유형을 보다 쉽게 식별할 수 있습니다. 예를 들어 다음 변수를 사용하여 저장소 계정에 대한 고유 이름을 생성할 수 있습니다.

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>식별을 위한 리소스 이름
일부 리소스 유형의 경우 이름을 지정할 수 있지만 이름이 고유해야 할 필요는 없습니다. 이러한 리소스 유형의 경우 리소스 컨텍스트와 리소스 유형 모두를 식별하는 이름을 제공할 수 있습니다.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>일반 리소스 이름
주로 다른 리소스를 통해 액세스하는 리소스 유형의 경우 템플릿에 하드 코드된 일반 이름을 사용할 수 있습니다. 예를 들어 SQL Server에서 방화벽 규칙에 대해 표준 일반 이름을 설정할 수 있습니다.

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>위치
템플릿을 배포할 때는 각 리소스의 위치를 지정해야 합니다. 다양한 리소스 형식이 다양한 위치에서 지원됩니다. 특정 리소스 형식에 대한 구독에서 사용할 수 있는 위치 목록을 보려면 Azure PowerShell 또는 Azure CLI를 사용합니다. 

다음 예제에서는 PowerShell을 사용하여 `Microsoft.Web\sites` 리소스 유형에 대한 위치를 가져옵니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

다음 예제에서는 Azure CLI 2.0을 사용하여 `Microsoft.Web\sites` 리소스 유형에 대한 위치를 가져옵니다.

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

리소스에 대해 지원되는 위치를 확인한 후 템플릿에서 해당 위치를 설정합니다. 이 값을 설정하는 가장 쉬운 방법은 리소스 유형을 지원하는 위치에 리소스 그룹을 만들고 해당 위치를 `[resourceGroup().location]`으로 설정합니다. 여러 다른 위치의 리소스 그룹에 템플릿을 다시 배포하고 템플릿 또는 매개 변수의 값을 변경하지 않을 수 있습니다. 

다음 예제에서는 리소스 그룹과 같은 위치에 배포되는 저장소 계정을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

템플릿에서 해당 위치를 하드 코드해야 할 경우 지원되는 하위 지역 중 하나의 이름을 제공합니다. 다음 예제에서는 항상 미국 중북부에 배포되는 저장소 계정을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>태그들
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>템플릿에 태그 추가

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>자식 리소스

일부 리소스 형식 내에서 자식 리소스의 배열도 정의할 수 있습니다. 자식 리소스는 다른 리소스의 컨텍스트 내에만 존재하는 리소스입니다. 예를 들어 SQL 데이터베이스는 SQL Server 없이 존재할 수 없으므로 데이터베이스의 서버의 자식 요소입니다. 서버에 대한 정의 내에서 데이터베이스를 정의할 수 있습니다.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

중첩된 경우 형식은 `databases`로 설정되지만 전체 리소스 형식은 `Microsoft.Sql/servers/databases`입니다. `Microsoft.Sql/servers/`는 입력하지 않습니다. 부모 리소스 유형에서 유추되기 때문입니다. 자식 리소스 이름은 `exampledatabase`로 설정되지만 전체 이름에는 부모 이름이 포함됩니다. `exampleserver`는 입력하지 않습니다. 부모 리소스에서 유추되기 때문입니다.

자식 리소스 유형의 형식은 다음과 같습니다. `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

자식 리소스 이름의 형식은 다음과 같습니다. `{parent-resource-name}/{child-resource-name}`

그러나 서버 내에 데이터베이스를 정의할 필요는 없습니다. 최상위 수준에 자식 리소스를 정의할 수 있습니다. 부모 리소스가 동일한 템플릿에 배포되지 않은 경우 `copy`를 사용하여 여러 자식 리소스를 만들려는 경우 이 방법을 사용할 수 있습니다. 이 방법을 사용하는 경우 전체 리소스 유형을 입력하고 자식 리소스 이름에 부모 리소스 이름을 포함해야 합니다.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

리소스에 대한 정규화된 참조를 생성할 때 형식과 이름으로 세그먼트를 결합하는 순서는 단순히 두 항목의 연결이 아닙니다.  대신, 네임스페이스 뒤에 구체성이 낮은 순으로 *형식/이름* 쌍의 시퀀스를 사용합니다.

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

예: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`는 올바릅니다. `Microsoft.Compute/virtualMachines/extensions/myVM/myExt`는 올바르지 않습니다.

## <a name="recommendations"></a>권장 사항
다음 정보는 리소스로 작업하는 경우 도움이 될 수 있습니다.

* 다른 참가자들이 리소스의 용도를 이해하도록 하려면 템플릿에 각 리소스에 대한 **설명**을 지정합니다.
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* 템플릿에서 *공용 끝점*(예: Azure Blob Storage 공용 끝점)을 사용하는 경우 네임스페이스를 *하드 코딩하지 마세요*. **reference** 함수를 사용하여 네임스페이스를 동적으로 검색합니다. 이 방법을 사용하면 템플릿의 끝점을 수동으로 변경하지 않고 다른 공용 네임스페이스 환경에 템플릿을 배포할 수 있습니다. 템플릿에서 저장소 계정에 사용하는 것과 동일한 버전으로 API 버전을 설정합니다.
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   저장소 계정이 생성 중인 것과 동일한 템플릿에 배포되면 리소스를 참조할 때 공급자 네임스페이스를 지정할 필요가 없습니다. 다음 예제에서는 간소화된 구문을 보여 줍니다.
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   템플릿의 다른 값을 공용 네임스페이스를 사용하도록 구성한 경우 동일한 **reference** 함수를 반영하도록 이러한 값을 변경합니다. 가상 머신 진단 프로필의 **storageUri** 속성을 설정할 수 있습니다.
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   다른 리소스 그룹에서 기존 저장소 계정을 참조할 수도 있습니다.

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* 응용 프로그램에 필요한 경우에만 공용 IP 주소를 가상 머신에 할당합니다. 디버깅, 관리 또는 관리 목적으로 가상 컴퓨터(VM)에 연결하려면 인바운드 NAT 규칙, 가상 네트워크 게이트웨이 또는 Jumpbox를 사용합니다.
   
     가상 머신에 연결하는 방법에 대한 자세한 내용은 다음을 참조하세요.
   
   * [Azure에서 N 계층 아키텍처에 대한 VM 실행](../guidance/guidance-compute-n-tier-vm.md)
   * [Azure Resource Manager에서 VM에 대한 WinRM 액세스 설정](../virtual-machines/windows/winrm.md)
   * [Azure Portal을 사용하여 VM에 대한 외부 액세스 허용](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell을 사용하여 VM에 대한 외부 액세스 허용](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLI를 사용하여 Linux VM에 대한 외부 액세스 허용](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* 공용 IP 주소에 대한 **domainNameLabel** 속성은 고유해야 합니다. **domainNameLabel** 값은 3~63자 사이여야 하고 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` 정규식으로 지정된 규칙을 따라야 합니다. **uniqueString** 함수는 13자 길이의 문자열을 생성하고, **dnsPrefixString** 매개 변수는 50자로 제한됩니다.

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* 사용자 지정 스크립트 확장에 암호를 추가하는 경우 **protectedSettings** 속성에서 **commandToExecute** 속성을 사용합니다.
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > VM 및 확장에 매개 변수로 전달되는 비밀이 암호화되도록 하려면 관련 확장의 **protectedSettings** 속성을 사용합니다.
   > 
   > 


## <a name="next-steps"></a>다음 단계
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 배포 중 여러 템플릿을 결합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 다른 리소스 그룹 내에 있는 리소스를 사용해야 할 수도 있습니다. 이 시나리오에서는 일반적으로 여러 리소스 그룹에서 공유하는 저장소 계정 또는 가상 네트워크에서 작업합니다. 자세한 내용은 [resourceId 함수](resource-group-template-functions-resource.md#resourceid)를 참조하세요.
* 리소스 이름 제한에 대한 자세한 내용은 [Azure 리소스에 대한 권장 명명 규칙](../guidance/guidance-naming-conventions.md)을 참조하세요.