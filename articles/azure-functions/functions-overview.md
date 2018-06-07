---
title: "Azure Functions 개요 | Microsoft Docs"
description: "Azure Functions를 사용하여 비동기 워크로드를 몇 분 이내에 최적화하는 방법에 대해 이해합니다."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d60c898225b944801504f38d536262134a31e021
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions 소개  
Azure Functions는 클라우드에서 작은 코드 또는 "함수"를 실행하기 위한 솔루션입니다. 실행할 전체 응용 프로그램 또는 인프라를 염려하지 않고 당면한 문제에 필요한 코드를 작성할 수 있습니다. Azure Functions는 개발 생산성을 높일 수 있으며 C#, F#, Node.js, Java, PHP 등 선택한 개발 언어를 사용할 수 있습니다. 코드를 실행한 시간에 따라 지불하고 Azure를 신뢰하여 필요에 따라 크기를 조정합니다. Azure Functions를 사용하면 Microsoft Azure에서 [서버를 사용하지 않는](https://azure.microsoft.com/overview/serverless-computing/) 응용 프로그램을 개발할 수 있습니다.

이 항목에서는 Azure Functions에 대한 간략한 개요를 제공합니다. Azure Functions를 바로 시작하려면 [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)를 시작합니다. Functions에 대한 자세한 기술 정보는 [개발자 참조](functions-reference.md)를 참조하세요.

## <a name="features"></a>기능
Azure Functions의 몇 가지 주요 기능은 다음과 같습니다.

* **언어 선택** - C#, F# 또는 Javascript를 사용하여 함수를 작성합니다. 다른 옵션은 [지원되는 언어](supported-languages.md)를 참조하세요.
* **사용량에 따른 가격 책정 모델** - 코드를 실행하는 데 사용한 시간에 대해서만 지불합니다. [가격 책정 섹션](#pricing)에서 소비 호스팅 요금제 옵션을 참조하세요.  
* **고유한 종속성 가져오기** - Functions는 NuGet 및 NPM을 지원하므로 즐겨찾는 라이브러리를 사용할 수 있습니다.  
* **통합 보안** - Azure Active Directory, Facebook, Google, Twitter, Microsoft 계정 등의 OAuth 공급자를 사용하여 HTTP 트리거 함수를 보호합니다.  
* **통합 간소화** - Azure 서비스 및 SaaS(software-as-a-service) 제품을 손쉽게 활용합니다. 몇 가지 예제는 [통합 섹션](#integrations)을 참조하세요.  
* **유연한 개발** - [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md) 및 기타 [지원되는 개발 도구](../app-service/app-service-deploy-local-git.md)를 통해 포털에 바로 함수를 코딩하거나 지속적인 통합을 설정하고 코드를 배포합니다.  
* **오픈 소스** - Functions 런타임은 오픈 소스이며 [GitHub에서 사용](https://github.com/azure/azure-webjobs-sdk-script)할 수 있습니다.  

## <a name="what-can-i-do-with-functions"></a>함수로 할 수 있는 작업은 무엇인가요?
Azure Functions는 데이터 처리, 시스템 통합, IoT(사물 인터넷) 작업 및 간단한 API/마이크로 서비스 빌드를 위한 훌륭한 솔루션입니다. Functions는 이미지 또는 주문 처리, 파일 유지 관리와 같은 작업 또는 일정에 따라 실행하려는 작업에 사용하는 것이 좋습니다. 

Functions는 다음을 포함한 주요 시나리오로 시작할 수 있는 템플릿을 제공합니다.

* **HTTPTrigger** - HTTP 요청을 사용하여 코드 실행을 트리거합니다. 예제를 보려면 [첫 번째 함수 만들기](functions-create-first-azure-function.md)를 참조하세요.
* **TimerTrigger** - 사전 정의된 일정에 따라 정리 또는 다른 배치 작업을 실행합니다. 예제를 보려면 [타이머를 통해 트리거되는 함수 만들기](functions-create-scheduled-function.md)를 참조하세요.
* **GitHub 웹후크** - GitHub 리포지토리에서 발생하는 이벤트에 대응합니다. 예제를 보려면 [GitHub 웹후크를 통해 트리거되는 함수 만들기](functions-create-github-webhook-triggered-function.md)를 참조하세요.
* **일반 웹후크** - 웹후크를 지원하는 모든 서비스의 웹후크 HTTP 요청을 처리합니다. 예제를 보려면 [일반 웹후크를 통해 트리거되는 함수 만들기](functions-create-generic-webhook-triggered-function.md)를 참조하세요.
* **CosmosDBTrigger** - NoSQL 데이터베이스의 컬렉션에 추가되거나 업데이트될 때 Azure Cosmos DB 문서를 처리합니다. 예를 들어 [Azure Cosmos DB에서 트리거되는 함수 만들기](functions-create-cosmos-db-triggered-function.md)를 참조하세요.
* **BlobTrigger** - 컨테이너에 Azure Storage Blob을 추가할 때 해당 Blob을 처리합니다. 이 함수를 이미지 크기 조정에 사용할 수 있습니다. 자세한 내용은 [Blob Storage 바인딩](functions-bindings-storage-blob.md)을 참조하세요.
* **QueueTrigger** - Azure Storage 큐에 도착하는 메시지에 대응합니다. 예를 들어 [Azure 큐 저장소에서 트리거되는 함수 만들기](functions-create-storage-queue-triggered-function.md)를 참조하세요.
* **EventHubTrigger** - Azure Event Hub에 전달된 이벤트에 응답합니다. 응용 프로그램 계측, 사용자 경험 또는 워크플로 처리 및 사물 인터넷(IoT) 시나리오에서 특히 유용합니다. 자세한 내용은 [Event Hubs 바인딩](functions-bindings-event-hubs.md)을 참조하세요.
* **ServiceBusQueueTrigger** - 메시지 큐를 수신하여 다른 Azure 서비스 또는 온-프레미스 서비스에 코드를 연결합니다. 자세한 내용은 [Service Bus 바인딩](functions-bindings-service-bus.md)을 참조하세요.
* **ServiceBusTopicTrigger** - 항목을 구독하여 다른 Azure 서비스 또는 온-프레미스 서비스에 코드를 연결합니다. 자세한 내용은 [Service Bus 바인딩](functions-bindings-service-bus.md)을 참조하세요.

Azure Functions는 코드 실행을 시작하는 방법인 *트리거*와 입력 및 출력 데이터의 코딩을 간소화하는 방법인 *바인딩*을 지원합니다. Azure Functions가 제공하는 트리거 및 바인딩에 대한 자세한 설명은 [Azure Functions 트리거 및 바인딩 개발자 참조](functions-triggers-bindings.md)를 참조하세요.

## <a name="integrations"></a>통합
Azure Functions는 다양한 Azure 및 타사 서비스와 통합됩니다. 이러한 서비스는 함수 및 시작 실행을 트리거하거나 코드에 대한 입력 및 출력으로 사용할 수 있습니다. Azure Functions에서 지원하는 서비스 통합은 다음과 같습니다.

* Azure Cosmos DB
* Azure Event Hubs 
* Azure Event Grid
* Azure Mobile Apps(테이블)
* Azure Notification Hubs
* Azure Service Bus(큐 및 항목)
* Azure Storage(Blob, 쿠, 테이블) 
* GitHub(웹후크)
* 온-프레미스(Service Bus 사용)
* Twilio(SMS 메시지)

## <a name="pricing"></a>Functions 사용 비용
Azure Functions에는 두 종류의 가격 책정 계획이 있습니다. 사용자의 요구에 가장 잘 맞는 하나를 선택합니다. 

* **소비 요금제** - 함수가 실행되면 Azure에서 필요한 모든 계산적 리소스를 제공합니다. 리소스 관리에 대해 염려하지 않아도 되며 코드가 실행된 시간에 대해서만 비용을 지불합니다. 
* **App Service 계획** - 웹, 모바일, API 앱 등의 함수를 실행합니다. 다른 응용 프로그램에 이미 App Service를 사용하고 있는 경우 추가 비용 없이 동일한 계획으로 함수를 실행할 수 있습니다. 

호스팅 계획에 대한 자세한 내용은 [Azure Functions 호스팅 계획 비교](functions-scale.md)를 참조하세요. 전체 가격 책정 정보는 [Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)페이지에 나와 있습니다.

## <a name="next-steps"></a>다음 단계
* [첫 번째 Azure 함수 만들기](functions-create-first-azure-function.md)  
  Azure Functions 빠른 시작을 사용하여 바로 첫 번째 함수를 만듭니다. 
* [Azure Functions 개발자 참조](functions-reference.md)  
  Azure Functions 런타임, 함수 코딩, 트리거 및 바인딩 정의에 대한 참조에 대해 더욱 기술적인 정보를 제공합니다.
* [Azure Functions 테스트](functions-test-a-function.md)  
  함수를 테스트하는 다양한 도구와 기법을 설명합니다.
* [Azure Functions 크기 조정 방법](functions-scale.md)  
  소비 호스팅 요금제, 올바른 요금제 선택 방법을 포함하여 Azure Functions에서 사용 가능한 서비스 요금제에 대해 설명합니다. 
* [Azure App Service에 대해 자세히 알아보기](../app-service/app-service-web-overview.md)  
  Azure Functions는 개발, 환경 변수, 진단 등의 주요 함수에 대한 Azure App Service를 활용합니다. 

