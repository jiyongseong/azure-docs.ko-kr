---
title: REST API 및 템플릿으로 리소스 배포 | Microsoft Docs
description: Azure Resource Manager와 REST API를 사용하여 Azure에 리소스를 배포합니다. 리소스는 Resource Manager 템플릿에 정의됩니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 6915395abfedb75ec3ec0a5bd8f569ef2490e5be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>리소스 관리자 템플릿과 리소스 관리자 REST API로 리소스 배포

이 문서에서는 리소스 관리자 템플릿으로 리소스 관리자 REST API를 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다.  

> [!TIP]
> 배포 중 발생하는 오류 디버깅에 대한 도움을 받으려면 다음을 참조하세요.
> 
> * [배포 작업 보기](resource-manager-deployment-operations.md)에서 오류를 해결하는 데 유용한 정보를 알 수 있습니다.
> * [Azure Resource Manager로 Azure에 리소스를 배포할 때 발생하는 일반적인 오류 해결](resource-manager-common-deployment-errors.md) 에서 일반적인 배포 오류를 해결하는 방법을 알 수 있습니다.
> 
> 

템플릿은 로컬 파일이거나 URI를 통해 사용 가능한 외부 파일일 수 있습니다. 템플릿이 저장소 계정에 상주하는 경우, 템플릿에 대한 액세스를 제한하고 배포 중에 공유 액세스 서명(SAS) 토큰을 제공할 수 있습니다.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>REST API를 사용하여 배포
1. 인증 토큰을 포함하여 [공통 매개 변수 및 헤더](/rest/api/azure/)를 설정합니다.

2. 기본 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다. 솔루션에 필요한 구독 ID, 새 리소스 그룹 이름 및 위치를 제공합니다. 자세한 내용은 [리소스 그룹 만들기](/rest/api/resources/resourcegroups/createorupdate)를 참조하세요.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. 배포를 실행하기 전에 [템플릿 배포 유효성 검사](/rest/api/resources/deployments/validate) 작업을 실행하여 배포 유효성을 검사합니다. 배포를 테스트할 때는 배포를 실행할 때처럼 정확하게 매개 변수를 제공합니다(다음 단계에 표시됨).

4. 배포를 만듭니다. 템플릿의 구독 ID, 리소스 그룹 이름, 배포 이름 및 템플릿 링크를 제공합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](#parameter-file)을 참조하세요. 리소스 그룹을 만드는 REST API에 대한 정보는 [템플릿 배포 만들기](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate)를 참조하세요. **mode**가 **Incremental**로 설정되어 있습니다. 전체 배포를 실행하려면 **mode**를 **Complete**로 설정합니다. 이 완전한 모드를 사용할 때는 템플릿에 없는 리소스를 실수로 삭제할 수 있으므로 주의해야 합니다.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
  ```

    요청 콘텐츠와 응답 콘텐츠 중 하나 또는 둘 다를 기록하려면 요청에 **debugSetting** 을 포함합니다.

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    공유 액세스 서명(SAS) 토큰을 사용하여 저장소 계정을 설정할 수 있습니다. 자세한 내용은 [공유 액세스 서명을 사용하여 액세스 위임](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)을 참조하세요.

5. 템플릿 배포의 상태를 가져옵니다. 자세한 내용은 [템플릿 배포에 대한 정보 가져오기](/rest/api/resources/deployments/get)를 참조하세요.

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

배포가 실패하는 경우, 배포 기록의 이전 배포가 자동으로 다시 배포되도록 지정할 수 있습니다. 이 옵션을 사용하려면 배포가 배포 기록에서 식별될 수 있도록 고유한 이름을 지정해야 합니다. 고유한 이름이 없으면 현재 실패한 배포가 기록에서 이전에 성공한 배포를 덮어쓸 수 있습니다. 루트 수준 배포에만 이 옵션을 사용할 수 있습니다. 중첩된 템플릿의 배포는 다시 배포할 수 없습니다.

현재 배포가 실패할 경우 마지막으로 성공적으로 배포를 다시 배포하려면 다음을 사용합니다.

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

현재 배포가 실패할 경우 특정 배포를 다시 배포하려면 다음을 사용합니다.

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
}
```

지정된 배포는 분명히 성공했을 것입니다.

## <a name="parameter-file"></a>매개 변수 파일 

배포 중에 매개 변수 값을 전달하는 데 매개 변수 파일을 사용하려면 다음 예와 유사한 형식의 JSON 파일을 만들어야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

매개 변수 파일 크기는 64KB보다 클 수 없습니다.

매개 변수에 대해 중요한 값(예: 암호)을 제공해야 할 경우 해당 값을 주요 자격 증명 모음에 추가합니다. 앞의 예제에 표시된 대로 배포하는 동안 주요 자격 증명 모음을 검색합니다. 자세한 내용은 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
* 비동기 REST 작업 처리에 대해 알아보려면 [Azure 비동기 작업 추적](resource-manager-async-operations.md)을 참조하세요.
* .NET 클라이언트 라이브러리를 통한 리소스 배포의 예제를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](resource-group-authoring-templates.md#parameters)을 참조하세요.
* 다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](solution-dev-test-environments.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.

