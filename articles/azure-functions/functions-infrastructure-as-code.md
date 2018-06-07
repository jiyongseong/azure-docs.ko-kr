---
title: Azure Functions의 함수 앱에 대한 리소스 배포 자동화 | Microsoft Docs
description: 함수 앱을 배포하는 Azure Resource Manager 템플릿을 빌드하는 방법을 알아봅니다.
services: Functions
documtationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 함수, 서버 없는 아키텍처, 코드로서의 인프라, Azure Resource Manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 28b2f5aba69e5c058feb7119eb31352220922998
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions의 함수 앱에 대한 리소스 배포 자동화

Azure Resource Manager 템플릿을 사용하여 함수 앱을 배포할 수 있습니다. 이 문서에서는 이 작업을 수행하는 데 필요한 리소스와 매개 변수를 간략히 설명합니다. 함수 앱의 [트리거 및 바인딩](functions-triggers-bindings.md)에 따라 추가 리소스를 배포해야 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성하기](../azure-resource-manager/resource-group-authoring-templates.md)를 참조하십시오.

샘플 템플릿은 다음을 참조하세요.
- [소비 계획의 함수 앱]
- [Azure App Service 계획의 함수 앱]

## <a name="required-resources"></a>필요한 리소스

함수 앱에는 다음 리소스가 필요합니다.

* [Azure Storage](../storage/index.yml) 계정
* 호스팅 계획(소비 계획 또는 App Service 계획)
* 함수 앱 

### <a name="storage-account"></a>Storage 계정

함수 앱에는 Azure Storage 계정이 필요합니다. Blob, 테이블, 큐 및 파일을 지원하는 일반 용도의 계정이 있어야 합니다. 자세한 내용은 [Azure Functions 저장소 계정 요구 사항](functions-create-function-app-portal.md#storage-account-requirements)을 참조하세요.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

또한 속성 `AzureWebJobsStorage`가 사이트 구성에서 앱 설정으로 지정되어야 합니다. 함수 앱이 Application Insights를 모니터링에 사용하지 않으면 `AzureWebJobsDashboard`도 앱 설정으로 지정되어야 합니다.

Azure Functions 런타임에서는 `AzureWebJobsStorage` 연결 문자열을 사용하여 내부 큐를 만듭니다.  Application Insights가 활성화되지 않은 경우 런타임은 `AzureWebJobsDashboard` 연결 문자열을 사용하여 Azure Table Storage에 로그온하고 포털의 **모니터** 탭에 전원을 공급합니다.

이러한 속성은 `siteConfig` 개체의 `appSettings` 컬렉션에서 지정됩니다.

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>호스팅 계획

호스팅 계획의 정의는 소비 계획을 사용하는지, App Service 계획을 사용하는지에 따라 달라집니다. [소비 계획의 함수 앱 배포](#consumption) 및 [App Service 계획의 함수 앱 배포](#app-service-plan)를 참조하세요.

### <a name="function-app"></a>함수 앱

함수 앱 리소스는 다음과 같이 **Microsoft.Web/Site** 유형과 **functionapp** 종류의 리소스를 사용하여 정의됩니다.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>소비 계획의 함수 앱 배포

함수 앱은 두 가지 모드 즉, 소비 계획 및 App Service 계획으로 실행할 수 있습니다. 소비 계획은 코드가 실행 중일 때 계산 용량을 자동으로 할당하고, 로드를 처리하는 데 필요한 만큼 확장한 다음 코드가 실행되지 않을 때 축소합니다. 따라서 유휴 VM에 대한 요금을 지불하고 용량을 미리 예약할 필요가 없습니다. 호스팅 계획에 대해 자세히 알아보려면 [Azure Functions 소비 및 App Service 계획](functions-scale.md)을 참조하세요.

샘플 Azure Resource Manager 템플릿은 [소비 계획의 함수 앱]을 참조하세요.

### <a name="create-a-consumption-plan"></a>소비 계획 만들기

소비 계획은 특수한 유형의 "서버 팜" 리소스입니다. `computeMode` 및 `sku` 속성에 `Dynamic` 값을 사용하여 지정합니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기

또한 소비 계획은 사이트 구성에 두 가지 추가 설정 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 및 `WEBSITE_CONTENTSHARE`가 필요합니다. 이러한 속성은 함수 앱 코드와 구성이 저장되는 저장소 계정 및 파일 경로를 구성합니다.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>App Service 계획의 함수 앱 배포

App Service 계획에서 함수 앱은 웹앱과 유사하게 기본, 표준, 프리미엄 SKU의 전용 VM에서 실행됩니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 

샘플 Azure Resource Manager 템플릿은 [Azure App Service 계획의 함수 앱]을 참조하세요.

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기 

규모 조정 옵션을 선택한 후에는 함수 앱을 만듭니다. 앱은 모든 함수를 포함하는 컨테이너입니다.

함수 앱에는 앱 설정 및 소스 제어 옵션을 포함하여 배포에 사용할 수 있는 자식 리소스가 많이 있습니다. 또한 **sourcecontrols** 자식 리소스를 제거하고 대신에 다른 [배포 옵션](functions-continuous-deployment.md)을 사용하도록 선택할 수 있습니다.

> [!IMPORTANT]
> Azure Resource Manager를 사용하여 응용 프로그램을 성공적으로 배포하려면 Azure에서 리소스가 배포되는 방식을 이해하는 것이 중요합니다. 다음 예제에서는 **siteConfig**를 사용하여 최상위 수준 구성을 적용합니다. Functions 런타임 및 배포 엔진에 정보를 전달하기 때문에 최상위 수준에서 이러한 구성을 설정하는 것이 중요합니다. **sourcecontrols/web** 자식 리소스를 적용하기 전에 최상위 수준 정보가 필요합니다. 자식 수준 **config/appSettings** 리소스에 이러한 설정을 구성할 수 있지만 **config/appSettings**가 적용되기 *전에* 함수 앱이 배포되어야 하는 경우도 있습니다. 예를 들어 [Logic Apps](../logic-apps/index.yml)에서 함수를 사용하는 경우 함수는 다른 리소스의 종속성입니다.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 이 템플릿은 Functions 배포 엔진(Kudu)에서 배치할 수 있는 코드를 검색할 기본 디렉터리를 설정하는 [프로젝트](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 앱 설정 값을 사용합니다. 리포지토리에서 함수는 **src** 폴더의 하위 폴더에 있습니다. 따라서 앞의 예제에서 앱 설정 값은 `src`로 설정합니다. 함수가 리포지토리의 루트에 있거나 소스 제어에서 배포하지 않는 경우 이 앱 설정 값을 제거할 수 있습니다.

## <a name="deploy-your-template"></a>템플릿 배포

다음 방법 중 하나를 사용하여 템플릿을 배포할 수 있습니다.

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Azure 단추에 배포

```<url-encoded-path-to-azuredeploy-json>```을 GitHub에 있는 `azuredeploy.json` 파일의 원시 경로에 대한 [URL 인코딩](https://www.bing.com/search?q=url+encode) 버전으로 바꿉니다.

markdown을 사용하는 예는 다음과 같습니다.

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

HTML을 사용하는 예는 다음과 같습니다.

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>다음 단계

Azure Functions를 개발하고 구성하는 방법에 대해 자세히 알아봅니다.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure 함수 앱 설정을 구성하는 방법](functions-how-to-use-azure-function-app-settings.md)
* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)

<!-- LINKS -->

[소비 계획의 함수 앱]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Azure App Service 계획의 함수 앱]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
