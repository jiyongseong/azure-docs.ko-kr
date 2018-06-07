---
title: Azure Resource Manager 템플릿에서 논리 앱 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 논리 앱 워크플로 만들기 및 배포
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 69be968f34d756cc725f5353dcf074c01fad65ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772554"
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 논리 앱 만들기 및 배포

Azure Logic Apps는 사용할 수 있는 Azure Resource Manager 템플릿을 제공하여 워크플로를 자동화하기 위한 논리 앱을 만들 뿐만 아니라 배포에 사용되는 리소스 및 매개 변수를 정의합니다. 이 템플릿을 자체 비즈니스 시나리오에 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다. [논리 앱의 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) 및 [Azure Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/resource-group-authoring-templates.md)에 대해 자세히 알아봅니다.

## <a name="define-the-logic-app"></a>논리 앱 정의

이 예제 논리 앱 정의는 한 시간에 한 번 실행되며, `testUri` 매개 변수에서 지정된 위치를 ping합니다.
템플릿은 논리 앱 이름(```logicAppName```) 및 테스트할 ping 위치(```testUri```)에 매개 변수 값을 사용합니다. [템플릿에서 이러한 매개 변수를 정의](#define-parameters)하는 방법에 대해 자세히 알아봅니다. 또한 템플릿은 논리 앱의 위치를 Azure 리소스 그룹과 같은 위치로 설정합니다. 

``` json
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2016-06-01",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "tags": {
      "displayName": "LogicApp"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "testURI": {
               "type": "string",
               "defaultValue": "[parameters('testUri')]"
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Hour",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Http": {
              "type": "Http",
              "inputs": {
                  "method": "GET",
                  "uri": "@parameters('testUri')"
              },
              "runAfter": {}
           }
         },
         "outputs": {}
      },
      "parameters": {}
   }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>매개 변수 정의

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

템플릿에서 매개 변수에 대한 설명은 다음과 같습니다.

| 매개 변수 | 설명 | JSON 정의 예제 | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | 템플릿에서 만든 논리 앱의 이름을 정의합니다. | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | 테스트를 위해 ping할 위치를 정의합니다. | "testUri": { "type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

[Logic Apps 워크플로 정의 및 속성에 대한 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 및 [JSON으로 논리 앱 정의에 대한 빌드](logic-apps-author-definitions.md)에 대해 자세히 알아봅니다.

## <a name="deploy-logic-apps-automatically"></a>자동으로 논리 앱 배포

논리 앱을 만들고 자동으로 Azure에 배포하려면 여기에서 **Azure에 배포**를 선택합니다.

[![Azure에 배포](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

이 작업을 통해 논리 앱의 세부 정보를 제공하고 템플릿 또는 매개 변수를 변경할 수 있는 Azure Portal에 로그인합니다. 예를 들어 Azure Portal에서는 다음과 같은 세부 정보를 묻는 메시지를 표시합니다.

* Azure 구독 이름
* 사용하려는 리소스 그룹
* 논리 앱 위치
* 논리 앱의 이름
* 테스트 URI
* 지정된 사용 약관 동의 여부

## <a name="deploy-logic-apps-with-commands"></a>명령을 사용하여 논리 앱 배포

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)