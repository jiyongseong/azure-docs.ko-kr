---
title: 예제 및 일반적인 시나리오 - Azure Logic Apps | Microsoft Docs
description: 예제, 시나리오, 자습서 및 연습을 통해 논리 앱에 대해 자세히 알아보세요.
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/18
ms.author: LADocs; jehollan
ms.openlocfilehash: 02690284e5487dba8e5fb5d973958f944bfa339f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps의 일반적인 시나리오, 예제, 자습서 및 연습

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 온-프레미스 SQL Server 또는 SAP에서 Microsoft Cognitive Services에 이르는 [100개 이상의 사용 가능한 커넥터](../connectors/apis-list.md)를 제공하기 때문에 다양한 서비스를 오케스트레이션하고 통합하는 데 도움이 됩니다. Logic Apps 서비스는 "서버가 없으므로" 규모 나 인스턴스를 걱정할 필요가 없습니다. 트리거가 있는 워크플로와 워크플로가 수행하는 작업을 정의하기만 하면 됩니다. 기본 플랫폼에서 규모, 가용성 및 성능을 처리합니다. Logic Apps는 여러 시스템에서 다수의 작업을 조정해야 하는 사용 사례 및 시나리오에 특히 유용합니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)가 지원하는 다양한 패턴 및 기능에 대해 자세히 알아볼 수 있는 일반적인 예제 및 시나리오는 다음과 같습니다.

## <a name="popular-starting-points-for-logic-app-workflows"></a>논리 앱 워크플로에 가장 많이 사용되는 시작점

모든 논리 앱은 하나의 [*트리거*](../logic-apps/logic-apps-overview.md#logic-app-concepts)로 시작하며 논리 앱 워크플로를 시작하고 트리거의 일부로 데이터를 전달합니다. 일부 커넥터는 다음과 같은 유형의 트리거를 제공합니다.

* *폴링 트리거*: 서비스 끝점에서 정기적으로 새 데이터를 확인합니다. 새 데이터가 존재하면 트리거는 해당 데이터를 입력으로 사용하여 새 워크플로 인스턴스를 만들고 실행합니다.

* *푸시 트리거*: 서비스 끝점에서 데이터를 수신 대기하고 특정 이벤트가 발생할 때까지 대기합니다. 이벤트가 발생하면 트리거가 즉시 실행되어 사용 가능한 데이터를 입력으로 사용하는 새 워크플로 인스턴스를 만들고 실행합니다.

다음은 많이 사용되는 트리거 예제입니다.

* 폴링: 

  * [**일정 - 되풀이** 트리거](../connectors/connectors-native-recurrence.md)를 사용하면 논리 앱을 실행하는 시작 날짜와 시간 및 되풀이를 설정할 수 있습니다. 
  예를 들어 논리 앱을 트리거하는 요일과 시간을 선택할 수 있습니다.

  * "전자 메일이 수신될 때" 트리거를 사용하면 Logic Apps에 지원되는 모든 전자 메일 공급자(예: [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/) 등)의 새 전자 메일을 논리 앱에서 확인할 수 있습니다.

  * [**HTTP** 트리거](../connectors/connectors-native-http.md)를 사용하면 논리 앱은 HTTP 통신을 통해 지정된 서비스 끝점을 확인할 수 있습니다.
  
* 푸시:

  * [**요청/응답 - 요청** 트리거](../connectors/connectors-native-reqres.md)를 사용하면 논리 앱이 HTTP 요청을 수신하고 어떤 식으로든 이벤트에 실시간으로 응답할 수 있습니다.

  * [**HTTP 웹후크** 트리거](../connectors/connectors-native-webhook.md)는 해당 서비스에 *콜백 URL*을 등록하여 서비스 끝점을 구독합니다. 
  서비스가 이런 방식으로 지정된 이벤트가 발생할 때 트리거에 알릴 수 있으므로 트리거가 서비스를 폴링할 필요가 없습니다.

새 데이터 또는 이벤트에 대한 알림을 수신하면 트리거가 실행되고 새 논리 앱 워크플로 인스턴스가 만들어지고 워크플로에서 작업이 실행됩니다. 워크플로 전체 트리거의 모든 데이터에 액세스할 수 있습니다. 예를 들어 "새 트윗에서" 트리거는 트윗 콘텐츠를 논리 앱 실행으로 전달합니다. 

## <a name="respond-to-triggers-and-extend-actions"></a>트리거에 응답 및 작업 확장

게시된 커넥터를 포함하지 않을 수 있는 시스템 및 서비스에 대해 논리 앱을 확장할 수도 있습니다.

* [사용자 지정 트리거 또는 작업 만들기](../logic-apps/logic-apps-create-api-app.md)
* [워크플로 실행에 대해 장기 실행 작업 설정](../logic-apps/logic-apps-create-api-app.md)
* [웹후크로 외부 이벤트 및 작업에 응답](../logic-apps/logic-apps-create-api-app.md)
* [HTTP 요청에 대한 동기 응답을 포함하는 호출, 트리거 또는 중첩 워크플로](../logic-apps/logic-apps-http-endpoint.md)
* [자습서: Logic Apps 및 Power BI로 몇 분 안에 AI 기반 소셜 대시보드 빌드](http://aka.ms/logicappsdemo)
* [비디오: Twilio SMS 웹후크에 응답 및 텍스트 응답 보내기](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>제어 흐름, 오류 처리 및 로깅 기능

논리 앱에는 조건, 스위치, 루프 및 범위와 같은 고급 제어 흐름에 대한 다양한 기능을 포함합니다. 복원력 있는 솔루션을 보장하기 위해 워크플로에서 오류 및 예외 처리를 구현할 수도 있습니다. 워크플로 실행 상태에 대한 알림 및 진단 로그를 위해 Azure Logic Apps에서는 모니터링 및 경고도 제공합니다.

* [조건 문](../logic-apps/logic-apps-control-flow-conditional-statement.md)과 [Switch 문](../logic-apps/logic-apps-control-flow-switch-statement.md)을 기반으로 다양한 작업 수행
* [루프를 사용하여 배열 및 컬렉션의 항목을 처리하거나 단계를 반복](../logic-apps/logic-apps-control-flow-loops.md)
* [범위와 함께 작업 그룹화](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [워크플로에서 작성자 오류 및 예외 처리](../logic-apps/logic-apps-exception-handling.md)
* [사용 사례: 의료 회사에서 HL7 FHIR 워크플로에 대해 논리 앱 예외 처리를 사용하는 방법](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [기존 논리 앱에 대한 모니터링, 로깅 및 경고 켜기](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [논리 앱을 만들 때 모니터링 및 진단 로깅 켜기](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>논리 앱 배포 및 관리

Visual Studio, Visual Studio Team Services 또는 기타 소스 제어 및 자동화된 빌드 도구를 사용하여 논리 앱을 완전하게 개발 및 배포할 수 있습니다. 리소스 템플릿에서 워크플로 및 종속 연결에 대한 배포를 지원하기 위해 논리 앱은 Azure 리소스 배포 템플릿을 사용합니다. Visual Studio 도구는 이러한 템플릿을 자동으로 생성하므로 버전 관리를 위해 소스 제어에 체크인할 수 있습니다.

* [Visual Studio에서 논리 앱 만들기 및 배포](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [기존 논리 앱에 대한 모니터링, 로깅 및 경고 켜기](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [자동화된 배포 템플릿 만들기](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>콘텐츠 형식, 변환 및 실행 내에서 변형

Azure Logic Apps [워크플로 정의 언어](http://aka.ms/logicappsdocs)의 다양한 함수를 사용하여 여러 콘텐츠 유형을 액세스, 변환 및 변형시킬 수 있습니다. 예를 들어, `@json()` 및 `@xml()` 워크플로 식을 사용하여 문자열, JSON 및 XML 간에 변환할 수 있습니다. Logic Apps 엔진은 서비스 간에 무손실 방식으로 콘텐츠 전송을 지원하는 콘텐츠 형식을 유지합니다.

* [논리 앱에서 워크플로 식이 작동하는 방식](../logic-apps/logic-apps-author-definitions.md)
* [비JSON 콘텐츠 형식 처리](../logic-apps/logic-apps-content-type.md)(`application/xml`, `application/octet-stream` 및 `multipart/formdata`)
* [Azure Logic Apps에 대한 워크플로 정의 언어 스키마](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>기타 통합 및 기능

논리 앱에서는 Azure Functions, Azure API Management, Azure App Services와 같은 다양한 서비스 및 사용자 지정 HTTP 끝점(REST 및 SOAP)과 통합도 제공합니다.

* [Azure 서버를 사용하지 않고 실시간 소셜 대시보드 만들기](../logic-apps/logic-apps-scenario-social-serverless.md)
* [논리 앱에서 Azure Functions 호출](../logic-apps/logic-apps-azure-functions.md)
* [자습서: Azure Functions로 논리 앱 트리거](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [자습서: Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [자습서: Azure Logic Apps 및 Microsoft Cognitive Services와 통합하여 Twitter 게시물 감정을 분석하는 함수 만들기](../azure-functions/functions-twitter-email.md)
* [자습서: Azure Logic Apps로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [블로그: 논리 앱에서 SOAP 끝점 호출](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>종단 간 시나리오

* [백서: Azure 서비스(예: Logic Apps)와 종단 간 사례 관리 통합](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>고객 사례

Azure Logic Apps가 다른 Azure 서비스 및 Microsoft 제품과 함께 복잡한 프로세스를 간소화하고 구성하고 자동화하고 오케스트레이션하여 [기업](https://aka.ms/logic-apps-customer-stories)의 민첩성을 향상시키고 핵심 비즈니스에 집중하도록 도울 수 있는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [JSON으로 논리 앱 정의에 따라 빌드](../logic-apps/logic-apps-author-definitions.md)
* [논리 앱에서 오류 및 예외 처리](../logic-apps/logic-apps-exception-handling.md)
* [Azure Logic Apps를 개선을 위한 의견, 질문, 피드백 또는 제안 사항 제출](https://feedback.azure.com/forums/287593-logic-apps)