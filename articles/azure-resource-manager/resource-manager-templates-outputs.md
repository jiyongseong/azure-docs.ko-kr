---
title: Azure Resource Manager 템플릿 출력 | Microsoft Docs
description: 선언적 JSON 구문을 사용하여 Azure Resource Manager 템플릿에서 출력을 정의하는 방법을 설명합니다.
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
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: e3c5a581b02f1dd7b7415ebd93de0e425ac2f8ae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 출력 섹션
Outputs 섹션에서, 배포에서 반환되는 값을 지정합니다. 예를 들어, 배포된 리소스에 액세스하기 위한 URI를 반환할 수 있습니다.

## <a name="define-and-use-output-values"></a>출력 값 정의 및 사용

다음 예제에서는 공용 IP 주소의 리소스 ID를 반환하는 방법을 보여줍니다.

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

배포 후에 스크립트를 사용하여 값을 검색할 수 있습니다. PowerShell의 경우 다음을 사용합니다.

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI의 경우 

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

[참조](resource-group-template-functions-resource.md#reference) 함수를 사용하여 연결된 템플릿에서 출력 값을 검색할 수 있습니다. 연결된 템플릿에서 출력 값을 가져오려면 `"[reference('<name-of-deployment>').outputs.<property-name>.value]"` 같은 구문으로 속성 값을 검색합니다.

예를 들어 연결된 템플릿에서 값을 검색하여 부하 분산 장치에 대한 IP 주소를 설정할 수 있습니다.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

`reference` 함수는 [중첩된 템플릿](resource-group-linked-templates.md#link-or-nest-a-template)의 출력 섹션에 사용할 수 없습니다. 중첩된 템플릿에서 배포된 리소스의 값을 반환하려면 중첩된 템플릿을 연결된 템플릿으로 변환합니다.

## <a name="available-properties"></a>사용 가능한 속성

다음 예제에서는 출력 정의의 구조를 보여줍니다.

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| outputName |예 |출력 값의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| 형식 |예 |출력 값의 유형입니다. 출력 값은 템플릿 입력 매개 변수와 동일한 유형을 지원합니다. |
| 값 |예 |출력 값으로 계산되어 반환되는 템플릿 언어 식입니다. |

## <a name="recommendations"></a>권장 사항

템플릿을 사용하여 공용 IP 주소를 만드는 경우 IP 주소 및 정규화된 도메인 이름(FQDN)의 세부 정보를 반환하는 출력 섹션을 포함합니다. 출력 값을 사용하여 배포 후 공용 IP 주소 및 FQDN에 대한 세부 정보를 쉽게 검색할 수 있습니다.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>예제 템플릿


|Template  |설명  |
|---------|---------|
|[변수 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 복잡한 변수를 만들고 해당 값을 출력합니다. 여기서는 리소스를 배포하지 않습니다. |
|[공용 IP 주소](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 공용 IP 주소를 만들고 리소스 ID를 출력합니다. |
|[부하 분산 장치](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 이전 템플릿에 연결됩니다. 부하 분산 장치를 만들 때 출력에 리소스 ID를 사용합니다. |


## <a name="next-steps"></a>다음 단계
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 배포 중 여러 템플릿을 결합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 다른 리소스 그룹 내에 있는 리소스를 사용해야 할 수도 있습니다. 이 시나리오에서는 일반적으로 여러 리소스 그룹에서 공유하는 저장소 계정 또는 가상 네트워크에서 작업합니다. 자세한 내용은 [resourceId 함수](resource-group-template-functions-resource.md#resourceid)를 참조하세요.