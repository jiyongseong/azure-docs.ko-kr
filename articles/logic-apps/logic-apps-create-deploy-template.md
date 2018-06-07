---
title: Azure Logic Apps용 배포 템플릿 만들기 | Microsoft Docs
description: 논리 앱 배포를 위한 Azure Resource Manager 템플릿 만들기
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: SyntaxC4
editor: ''
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 91d93a02bb9bf48c5bda0304c9d3d52c22e30209
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>논리 앱 배포를 위한 Azure Resource Manager 템플릿 만들기

논리 앱을 만든 후 Azure Resource Manager 템플릿으로 만들 수 있습니다.
이 방식을 사용하면 필요에 따라 어떤 환경이나 리소스 그룹에도 논리 앱을 손쉽게 배포할 수 있습니다.
Resource Manager 템플릿에 대한 자세한 내용은 [Azure Resource Manager 작성](../azure-resource-manager/resource-group-authoring-templates.md) 및 [Azure Resource Manager 템플릿을 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)를 확인하세요.

## <a name="logic-app-deployment-template"></a>논리 앱 배포 템플릿

논리 앱은 세 가지 기본 구성 요소가 있습니다.

* **논리 앱 리소스**: 가격 책정 정책, 위치, 워크플로 정의 등의 정보가 포함됩니다.
* **워크플로 정의**: 논리 앱 워크플로 단계 및 Logic Apps 엔진이 워크플로를 실행하는 방법을 설명합니다.
논리 앱의 **코드 보기** 창에서 이 정의를 볼 수 있습니다.
논리 앱 리소스의 `definition` 속성에서 이 정의를 찾을 수 있습니다.
* **연결**: 연결 문자열 및 액세스 토큰과 같은 커넥터 연결과 관련된 메타데이터를 안전하게 보관하는 별도의 리소스를 참조합니다.
논리 앱 리소스에서 논리 앱은 `parameters` 섹션의 이러한 리소스를 참조합니다.

[Azure Resource Explorer](http://resources.azure.com)와 같은 도구를 사용하여 기존 Logic Apps에 대한 이런 모든 내용을 볼 수 있습니다.

리소스 그룹 배포에 사용할 수 있는 논리 앱 템플릿을 만들려면 리소스를 정의하고 필요에 따라 매개 변수화해야 합니다.
예를 들어, 개발, 테스트 및 프로덕션 환경에 배포하는 경우 각 환경에서 SQL Database에 대해 다른 연결 문자열을 사용할 수 있습니다.
또는, 다른 구독이나 리소스 그룹 내에 배포할 수 있습니다.  

## <a name="create-a-logic-app-deployment-template"></a>논리 앱 배포 템플릿 만들기

유효한 논리 앱 배포 템플릿을 생성하는 가장 쉬운 방법은 [Logic Apps용 Visual Studio Tools](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)를 사용하는 것입니다.
Visual Studio Tools는 구독 또는 위치에서 사용할 수 있는 올바른 배포 템플릿을 생성합니다.

논리 앱 배포 템플릿을 만들 때 도움을 받을 수 있는 다른 몇 가지 도구가 있습니다.
템플릿은 손으로 작성할 수 있습니다. 즉, 여기서 논의했던 리소스를 사용하여 필요에 따라 매개 변수를 만듭니다.
[논리 앱 템플릿 작성기](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell 모듈을 활용하는 옵션도 있습니다. 이 오픈 소스 모듈은 논리 앱과 논리 앱에서 사용하는 모든 연결을 평가하고, 배포에 필요한 매개 변수와 함께 템플릿 리소스를 생성합니다.
예를 들어, Azure Service Bus 큐에서 메시지를 수신하고 Azure SQL Database에 데이터를 추가하는 논리 앱이 있는 경우 이 도구는 모든 오케스트레이션 논리를 보존하고 SQL 및 Service Bus 연결 문자열을 매개 변수화하여 배포 시 설정할 수 있도록 합니다.

> [!NOTE]
> 연결은 논리 앱과 동일한 리소스 그룹 내에 있어야 합니다.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>논리 앱 템플릿 PowerShell 모듈 설치
가장 쉬운 설치 방법은 `Install-Module -Name LogicAppTemplate` 명령을 사용하여 [PowerShell 갤러리](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)를 통하는 것입니다.  

또한, PowerShell 모듈을 수동으로 설치할 수 있습니다.

1. [논리 앱 템플릿 작성기](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)의 최신 릴리스를 다운로드합니다.  
2. PowerShell 모듈 폴더에 폴더를 추출합니다(일반적으로 `%UserProfile%\Documents\WindowsPowerShell\Modules`).

모듈에서 모든 테넌트 및 구독 액세스 토큰에 대한 작업을 하려면 [ARMClient](https://github.com/projectkudu/ARMClient) 명령줄 도구로 사용하는 것이 좋습니다.  이 [블로그 게시물](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)은 ARMClient에 대해 자세히 설명 합니다.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>PowerShell을 사용하여 논리 앱 템플릿 생성
PowerShell을 설치한 후 다음 명령을 사용하여 템플릿을 생성합니다.

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 은(는) Azure 구독 ID입니다. 이 줄은 ARMClient를 통해 액세스 토큰을 가져온 다음 PowerShell 스크립트로 실행시키고, JSON 파일로 템플릿을 만듭니다.

## <a name="add-parameters-to-a-logic-app-template"></a>논리 앱 템플릿에 매개 변수 추가
논리 앱 템플릿을 만든 다음 필요한 매개 변수를 추가하거나 수정할 수 있습니다. 예를 들어 정의에 단일 배포로 배포할 계획인 중첩된 워크플로 또는 Azure Function에 대한 리소스 ID가 포함된 경우, 템플릿에 추가 리소스를 추가하고 필요에 따라 ID를 매개 변수화할 수 있습니다. 각 리소스 그룹과 함께 배포할 예정인 Swagger 끝점 또는 사용자 지정 API에 대한 참조도 마찬가지입니다.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Visual Studio 배포 템플릿에 종속 리소스에 대한 참조 추가

논리 앱에서 종속 리소스를 참조하려는 경우 논리 앱 배포 템플릿에서 [Azure Resource Manager 템플릿 함수](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)를 사용할 수 있습니다. 예를 들어, 논리 앱에서 논리 앱과 함께 배포하려는 Azure Function 또는 통합 계정을 참조하려고 합니다. Logic App Designer가 올바르게 렌더링하도록 배포 템플릿에서 매개 변수를 사용하는 방법에 대한 다음 지침을 따르세요. 

이러한 종류의 트리거 및 작업에서 논리 앱 매개 변수를 사용할 수 있습니다.

*   하위 워크플로
*   함수 앱
*   APIM 호출
*   API 연결 런타임 URL
*   API 연결 경로

parameters, variables, resourceId, concat 등과 같은 템플릿 함수를 사용할 수 있습니다. 예를 들어, Azure Function 리소스 ID를 대체하는 방법은 다음과 같습니다.

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

그리고 매개 변수를 사용하는 위치는 다음과 같습니다.

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
또 다른 예로, Service Bus 메시지 전송 작업을 매개 변수화할 수 있습니다.

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl은 선택 사항이며 있는 경우 템플릿에서 제거할 수 있습니다.
> 


> [!NOTE] 
> 매개 변수를 사용할 때 Logic App Designer를 작동시키려면 다음과 같은 기본값을 제공해야 합니다.
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>논리 앱을 기존 리소스 그룹 프로젝트에 추가

기존 리소스 그룹 프로젝트가 있는 경우 JSON 개요 창에서 해당 프로젝트에 논리 앱을 추가할 수 있습니다. 이전에 만든 앱과 함께 다른 논리 앱을 추가할 수도 있습니다.

1. `<template>.json` 파일을 엽니다.

2. JSON 개요 창을 열려면 **보기** > **다른 창** > **JSON 개요**로 이동합니다.

3. 템플릿 파일에 리소스를 추가하려면 JSON 개요 창의 위쪽에서 **리소스 추가**를 클릭합니다. 또는 JSON 개요 창에서 **리소스**를 마우스 오른쪽 단추로 클릭하고 **새 리소스 추가**를 선택합니다.

    ![JSON 개요 창](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. **리소스 추가** 대화 상자에서 **Logic App**을 찾고 선택합니다. 논리 앱의 이름을 지정하고 **추가**를 선택합니다.

    ![리소스 추가](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>논리 앱 템플릿 배포

템플릿을 만든 후에는 PowerShell, REST API, [Visual Studio Team Services Release Management](#team-services), Azure Portal을 통한 템플릿 배포와 같은 여러 도구를 사용하여 배포할 수 있습니다.
또한, 매개 변수 값을 저장할 [매개 변수 파일](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)을 만드는 것이 좋습니다.
[Azure Resource Manager 템플릿 및 PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md) 또는 [Azure Resource Manager 템플릿 및 Azure Portal로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-portal.md) 방법을 알아봅니다.

### <a name="authorize-oauth-connections"></a>OAuth 연결 권한 부여

배포 후 논리 앱은 유효한 매개 변수와 함께 종단 간에 적용됩니다.
그러나 OAuth 연결이 유효한 액세스 토큰을 생성하도록 권한을 부여해야 합니다.
OAuth 연결에 권한을 부여하려면 Logic Apps Designer에서 논리 앱을 열고 이러한 연결에 권한을 부여합니다. 또는, 자동화 배포의 경우 스크립트를 사용하여 각 OAuth 연결을 승인할 수 있습니다.
GitHub에 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 프로젝트라는 예시가 있습니다.

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services 릴리스 관리

환경을 배포 및 관리하는 일반적인 시나리오는 논리 앱 배포 템플릿과 함께 Visual Studio Team Services의 릴리스 관리 등의 도구를 사용하는 것입니다. Visual Studio Team Services에는 모든 빌드 또는 릴리스 파이프라인에 추가할 수 있는 [Azure 리소스 그룹 배포](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) 작업이 포함됩니다. 배포 권한 부여를 위해서는 [서비스 주체](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) 가 필요하며 그 다음 릴리스 정의를 생성할 수 있습니다.

1. 릴리스 관리에서 빈 정의를 생성할 수 있도록 **비어 있음**을 선택합니다.

    ![빈 정의 만들기][1]

2. 수동으로 또는 빌드 프로세스의 일부로 생성된 논리 앱 템플릿을 포함할 가능성이 높은 필요한 리소스를 선택합니다.
3. **Azure 리소스 그룹 배포** 작업을 추가합니다.
4. [서비스 주체](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)로 구성하고 템플릿 및 템플릿 매개 변수 파일을 참조합니다.
5. 릴리스 프로세스에서 다른 환경, 자동화된 테스트 또는 승인자에 대한 단계를 필요에 따라 계속 작성합니다.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
