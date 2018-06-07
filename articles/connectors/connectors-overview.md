---
title: "Logic Apps 커넥터 개요 | Microsoft Docs"
description: "논리 앱에서 사용할 수 있는 커넥터의 개요"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 6f7d8f99bfa09847c01831a06efa8b94c1c0a89a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="using-connectors-in-a-logic-app"></a>논리 앱에서 커넥터 사용
커넥터는 서비스, 프로토콜 및 플랫폼에 걸쳐 이벤트, 데이터 및 작업에 빠르게 액세스할 수 있도록 합니다.  Logic Apps가 지원하는 커넥터의 전체 목록은 [여기에서 찾을 수 있습니다](apis-list.md).  커넥터는 논리 앱에서 트리거 또는 작업으로 사용할 수 있으며 사용하기 위해 구성된 *연결* 이 필요합니다(예: 사용자 대신 액세스하거나 게시할 수 있는 권한을 Twitter 계정에 부여).

## <a name="basics"></a>기본 사항
커넥터는 Dynamics, Azure, Salesforce, [등](apis-list.md)의 다른 서비스와 통합하기 위해 논리 앱의 일부로 액세스할 수 있는 호스트된 서비스입니다.  이러한 커넥터는 Microsoft에서 배포 및 관리하므로, 규모, 처리량 및 보안이 관리되는 통합 워크플로를 작성할 수 있습니다.  **Microsoft 관리 API 표시**에서 커넥터 작업 또는 트리거를 검색하고 선택하여 논리 앱에 커넥터를 추가할 수 있습니다.

![트리거를 선택하기 위한 작업 메뉴][1]

각 커넥터 작업 또는 트리거에는 구성할 속성 집합이 있습니다.  정보 단추를 클릭하여 작업에 대해 자세히 알아보거나 해당 설명서에서 [자세한 내용을 확인](apis-list.md)할 수 있습니다.

아직 커넥터가 아닌 서비스 또는 API와 통합하려면 [사용자 지정 커넥터](../logic-apps/logic-apps-create-api-app.md) 를 통해 논리 앱을 확장하거나 HTTP와 같은 프로토콜을 통해 서비스를 직접 호출할 수도 있습니다.

## <a name="triggers"></a>트리거
일부 커넥터에는 트리거가 있습니다. 이것은 해당 커넥터의 이벤트가 논리 앱을 실행하고 트리거의 일부로 데이터를 전달함을 의미합니다.  트리거는 항상 논리 앱의 첫 번째 단계입니다.  인기 있는 트리거에는 다음과 같은 작업이 포함됩니다.

* 되풀이 - 1시간마다 실행
* HTTP 요청을 받은 경우
* 큐에 항목이 추가될 때
* 전자 메일이 수신될 때

논리 앱에 대한 알림을 통해 이벤트가 발생하는 즉시 실행되는 트리거도 있고, 논리 앱이 서비스에서 이벤트를 확인하는 빈도(최대 15초 간격)에 대한 되풀이 간격이 구성되어야 하는 트리거도 있습니다.  

이벤트가 수신되면 논리 앱이 실행되고 워크플로의 작업이 시작됩니다.  워크플로 전체에서 트리거의 데이터에 액세스할 수도 있습니다(예를 들어 '새 트윗' 트리거는 실행에 트윗을 전달함).

## <a name="actions"></a>동작
대부분의 커넥터에는 워크플로의 일부로 실행할 수 있는 작업이 하나 이상 있습니다.  작업은 트리거에서 실행이 발생한 후에 발생하는 모든 단계입니다.  작업을 추가하려면 **새 단계** 단추를 클릭하고 사용하려는 커넥터를 검색합니다.  일단 선택하면(필요할 수 있는 모든 [연결](#connections) 을 구성한 후) 구성할 수 있는 작업 카드가 표시됩니다.  출력을 위해 토큰 중 하나를 클릭하여 이전 단계의 데이터를 선택하거나 필요에 따라 다른 구성을 입력할 수 있습니다.

![커넥터 작업 구성][2]

## <a name="connections"></a>연결
커넥터를 사용하려면 먼저 *연결* 을 구성해야 합니다.  *연결* 은 커넥터에 액세스하는 데 필요한 모든 로그인 또는 연결 구성입니다.  OAuth를 사용하는 커넥터의 경우 연결을 만드는 것은 액세스 토큰을 암호화한 후 Azure 암호 저장소에 안전하게 저장할 수 있는 서비스(예: Office 365, Salesforce 또는 GitHub)에 로그인하는 것을 의미합니다.  다른 커넥터(예: FTP 및 SQL)에는 서버 주소, 사용자 이름 및 암호와 같은 구성을 포함하는 연결이 필요합니다.  이러한 연결 구성 세부 정보 또한 암호화된 후 안전하게 저장됩니다.  연결은 서비스가 허용하는 한 서비스에 액세스할 수 있습니다.  Azure Active Directory OAuth 연결(예: Office 365 및 Dynamics)의 경우 액세스 토큰을 무제한으로 계속 새로 고칠 수 있습니다.  다른 서비스는 토큰을 새로 고치지 않고 사용할 수 있는 기간이 한정되어 있을 수 있습니다.  일반적으로 암호 변경과 같은 특정 작업을 수행하면 모든 액세스 토큰이 무효화됩니다.  

Azure에서 **찾아보기**를 클릭하고 **API 연결**을 선택하여 연결을 보고 관리할 수 있습니다.  API 연결 리소스에서 만든 연결을 보거나, 편집하거나, 업데이트하거나, 권한을 다시 부여할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [논리 앱의 일반 용도 및 예제에 대해 자세히 알아보기](../logic-apps/logic-apps-examples-and-scenarios.md)
* [엔터프라이즈 통합 트리거 및 작업 시작](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
