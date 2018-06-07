---
title: 서버를 사용하지 않는 시나리오 - Azure를 사용하여 Customer Insights 대시보드 만들기 | Microsoft Docs
description: Azure Logic Apps 및 Azure Functions를 사용한 고객 대시보드를 빌드하여 고객 피드백, 소셜 미디어 데이터 등을 관리할 수 있는 방법에 대해 알아봅니다.
keywords: ''
services: logic-apps
author: jeffhollan
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jehollan; LADocs
ms.openlocfilehash: 0a31a71305a4729575c5266b3a6138004d2dbdc6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Azure Logic Apps 및 Azure Functions를 사용하여 스트리밍 Customer Insights 대시보드 만들기

Azure는 인프라에 대한 염려 없이 클라우드에서 앱을 빠르게 빌드하고 호스팅하는 데 도움이 되는 서버를 사용하지 않는 도구를 제공합니다. 이 자습서에서는 고객 피드백을 트리거하고 Machine Learning으로 피드백을 분석하고 Power BI 또는 Azure Data Lake와 같은 원본 정보를 게시하는 대시보드를 만들 수 있습니다.

이 솔루션의 경우 서버를 사용하지 않는 앱을 위해 [Azure Functions](https://azure.microsoft.com/services/functions/) 및 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 핵심 Azure 구성 요소를 사용합니다.
Azure Logic Apps는 서버가 없는 구성 요소에서 오케스트레이션을 만들고 200개 이상의 서비스 및 API에 연결할 수 있도록 클라우드에서 서버를 사용하지 않는 워크플로 엔진을 제공합니다. Azure Functions는 클라우드에 서버를 사용하지 않는 계산을 제공합니다. 이 솔루션은 미리 정의된 키워드를 기반으로 고객 트윗에 플래그를 지정하도록 Azure Functions를 사용합니다.

이 시나리오에서는 고객의 피드백을 찾도록 트리거하는 논리 앱을 만듭니다. 고객 피드백에 응답하는 데 도움이 되는 일부 커넥터에는 Outlook.com, Office 365, Survey Monkey, Twitter 및 [웹 형식의 HTTP 요청](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/)이 포함됩니다. 사용자가 만든 워크플로는 Twitter에서 해시태그를 모니터링합니다.

[Visual Studio에서 전체 솔루션을 빌드](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)하고 [Azure Resource Manager 템플릿을 사용하여 솔루션을 배포](../logic-apps/logic-apps-create-deploy-template.md)할 수 있습니다. 이 솔루션을 만드는 방법을 보여 주는 동영상 연습은 [이 채널 9 비디오를 시청](http://aka.ms/logicappsdemo)하세요. 

## <a name="trigger-on-customer-data"></a>고객 데이터에서 트리거

1. Azure Portal 또는 Visual Studio에서 빈 논리 앱을 만듭니다. 

   논리 앱을 처음 접하는 경우 [Azure Portal을 위한 빠른 시작](../logic-apps/quickstart-create-first-logic-app-workflow.md) 또는 [Visual Studio을 위한 빠른 시작](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)을 검토하세요.

2. Logic Apps 디자이너에서 **새 트윗이 게시될 때**라는 동작이 있는 Twitter 트리거를 찾아서 추가합니다.

3. 키워드 또는 해시태그를 기준으로 트윗을 수신하도록 트리거를 설정합니다.

   Twitter 트리거와 같은 폴링 기반 트리거에서는 되풀이 속성이 논리 앱이 새 항목을 확인하는 주기를 결정합니다.

   ![Twitter 트리거의 예][1]

이 논리 앱은 이제 모든 새 트윗에 적용됩니다. 그런 다음, 표현된 감정을 더 잘 이해할 수 있도록 트윗 데이터를 가져와 분석할 수 있습니다. 

## <a name="analyze-tweet-text"></a>트윗 텍스트 분석

[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 사용하여 일부 텍스트에 숨겨져 있는 감정을 검색할 수 있습니다.

1. Logic Apps 디자이너에서 트리거에 있는 **새 단계**를 선택합니다.

2. **텍스트 분석** 커넥터를 찾습니다.

3. **감정 검색** 작업을 선택합니다.

4. 메시지가 나타나면 텍스트 분석 서비스에 대한 올바른 Cognitive Services 키를 입력합니다.

5. **요청 본문**에서 분석을 위한 입력으로 트윗 텍스트를 제공하는 **트윗 텍스트** 필드를 선택합니다.

트윗 데이터 및 트윗에 관한 정보를 가져오면 이제 다른 여러 가지 관련 커넥터 및 해당 작업을 사용할 수 있습니다.

* **Power BI - 스트리밍 데이터 집합에 행 추가**: Power BI 대시보드에서 들어오는 트윗을 봅니다.
* **Azure Data Lake - 파일 추가**: 분석 작업에 포함할 Azure Data Lake 데이터 집합에 고객 데이터를 추가합니다.
* **SQL - 행 추가**: 나중의 검색을 위해 데이터베이스에 데이터를 저장합니다.
* **Slack - 메시지 보내기**: 작업을 필요로 하는 부정적인 피드백에 대한 Slack 채널을 알립니다.

데이터에 대해 사용자 지정 처리를 수행할 수 있도록 Azure 함수를 만들 수 있습니다. 

## <a name="process-data-with-azure-functions"></a>Azure Functions를 사용한 데이터 처리

함수를 만들기 전에 Azure 구독에서 함수 앱을 만듭니다. 또한 논리 앱이 직접 함수를 호출하려면 함수에 예를 들어 **HttpTrigger** 템플릿을 사용하는 HTTP 트리거 바인딩이 있어야 합니다. [Azure Portal에서 첫 번째 함수 앱 및 함수를 만드는 방법](../azure-functions/functions-create-first-azure-function-azure-portal.md)에 대해 알아봅니다.

이 시나리오의 경우 Azure 함수의 요청 본문으로 트윗 텍스트를 사용합니다. 함수 코드에서 트윗 텍스트에 키워드 또는 구가 포함되는지 여부를 결정하는 논리를 정의합니다. 시나리오에 필요한 대로 함수를 간단하거나 복잡하게 유지합니다.
함수의 끝에서 일부 데이터를 사용하여 논리 앱에 대한 응답을 반환합니다(예: `containsKeyword`와 같은 간단한 부울 값 또는 복합 개체).

> [!TIP]
> 논리 앱의 함수에서 복잡한 응답에 액세스하려면 **JSON 구문 분석** 작업을 사용합니다.

완료되면 함수를 저장한 다음, 빌드하는 논리 앱의 작업으로 함수를 추가합니다.

## <a name="add-azure-function-to-logic-app"></a>논리 앱에 Azure 함수 추가

1. Logic Apps 디자이너의 **감정 검색** 작업에서 **새 단계**를 선택합니다.

2. **Azure Functions** 커넥터를 찾은 다음, 만든 함수를 선택합니다.

3. **요청 본문**에서 **트윗 텍스트**를 선택합니다.

![구성된 Azure 함수 단계][2]

## <a name="run-and-monitor-your-logic-app"></a>논리 앱 실행 및 모니터링

논리 앱에 대한 현재 또는 이전 실행을 검토하려면 Azure Portal, Visual Studio 또는 Azure REST API와 SDK를 통해 Azure Logic Apps에서 제공하는 다양한 디버깅 및 모니터링 기능을 사용할 수 있습니다.

논리 앱을 손쉽게 테스트하려면 Logic Apps 디자이너에서 **트리거 실행**을 선택합니다. 조건을 충족하는 트윗을 찾을 때까지 지정한 일정에 따라 트리거가 트윗을 폴링합니다. 실행이 진행되는 동안 디자이너에 해당 실행에 대한 라이브 뷰가 표시됩니다.

Visual Studio 또는 Azure Portal에서 이전 실행 내역을 보려면 다음을 수행합니다. 

* Visual Studio 클라우드 탐색기를 엽니다. 논리 앱을 찾아 앱의 바로 가기 메뉴를 엽니다. **실행 기록 열기**를 선택합니다.

* Azure Portal에서 논리 앱을 찾습니다. 논리 앱의 메뉴에서 **개요**를 선택합니다. 

## <a name="create-automated-deployment-templates"></a>자동화된 배포 템플릿 만들기

논리 앱 솔루션을 만든 후 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-overview.md#template-deployment)으로 앱을 캡처하여 전 세계의 Azure 지역에 배포합니다. 이 기능을 사용하여 앱의 다른 버전을 만들고 솔루션을 빌드 및 릴리스 파이프라인에 통합하기 위해 매개 변수를 수정할 수 있습니다. 모든 종속성과 함께 전체 솔루션을 단일 템플릿으로 관리할 수 있도록 Azure Functions를 배포 템플릿에 포함할 수 있습니다. [논리 앱 배포 템플릿 만드는 방법](../logic-apps/logic-apps-create-deploy-template.md)을 알아보세요.

Azure 함수를 사용한 배포 템플릿의 예는 [Azure 빠른 시작 템플릿 리포지토리](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)를 확인해 보세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps를 위한 다른 예제 및 시나리오 찾기](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png