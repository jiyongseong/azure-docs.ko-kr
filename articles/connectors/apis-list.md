---
title: Azure Logic Apps용 커넥터 | Microsoft Docs
description: 사용 가능한 모든 Microsoft 커넥터 중에 선택하여 논리 앱을 빌드하고 만들기
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: estfan; ladocs
ms.openlocfilehash: a17d887e829252231e0f2e0bac137bd63a24e0d9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="connectors-list"></a>커넥터 목록
각 커넥터의 Swagger 설명과 모든 커넥터 제한에서 정의된 트리거 및 작업을 찾으려면 [커넥터 세부 정보](/connectors/)를 참조하세요.

커넥터는 논리 앱을 만들 때 필수적인 부분입니다. 이러한 커넥터를 사용하면 온-프레미스 및 클라우드 응용 프로그램을 확장하여 새로 만든 데이터와 기존에 갖고 있던 데이터로 다양한 일을 할 수 있습니다. 커넥터는 기본 제공 작업 또는 관리 커넥터로 사용할 수 있습니다.

**기본 제공 작업**: Logic Apps 엔진 자체는 끝점에 대한 통신 및 태스크 수행을 위한 기본 제공 작업을 제공합니다. 예를 들어, HTTP 끝점, Azure Functions, Azure API Management 작업 호출 뿐만 아니라 데이터 작업 및 변수를 사용하여 메시지를 조작하기 위해 이러한 작업을 사용할 수 있습니다.

**관리 커넥터**: Logic Apps 서비스에서 호스트하고 관리하는 API 연결을 만들어 다양한 서비스를 위한 API에 대한 액세스를 제공합니다. 관리 커넥터는 다음 범주로 분류됩니다.

* **표준 커넥터**: 논리 앱을 만들 때 자동으로 제공되고 포함됩니다. Service Bus, Power BI, OneDrive 등을 예로 들 수 있습니다.

* **온-프레미스 커넥터**: [온-프레미스 데이터 게이트웨이][gatewaydoc]를 사용하여 온-프레미스 서버 응용 프로그램에 연결합니다. 온-프레미스 커넥터는 SharePoint Server, SQL Server, Oracle DB, 파일 공유 등과 같은 서버 응용 프로그램에 대한 연결을 포함합니다.

* **통합 계정 커넥터**: 통합 계정을 구입할 때 제공됩니다. 이러한 커넥터를 사용하면 XML을 변환하여 유효성을 검사하고, AS2/X12/EDIFACT를 사용하여 기업 간 메시지를 처리하고, 플랫 파일을 인코딩 및 디코딩할 수 있습니다. BizTalk Server를 작업할 때 이러한 커넥터는 BizTalk 워크플로를 Azure로 확장하는 데 적합합니다.  

    또한 BizTalk Server에는 논리 앱과의 송수신을 포함하는 [Logic Apps 어댑터](https://msdn.microsoft.com/library/mt787163.aspx)가 있습니다.

* **엔터프라이즈 커넥터**: MQ 및 SAP가 포함됩니다. 추가 비용 지불 시 사용할 수 있습니다. 

비용에 대한 자세한 내용은 Logic Apps에 대한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/logic-apps/) 및 [가격 책정 모델](../logic-apps/logic-apps-pricing.md)을 참조하세요. 

## <a name="popular-connectors"></a>인기 있는 커넥터
이러한 커넥터를 사용하여 데이터와 정보를 성공적으로 처리하는 수천 개의 응용 프로그램과 수백만 개의 실행이 있습니다. 

### <a name="built-in-actions"></a>기본 제공 작업
Logic Apps 엔진은 데이터를 조작하고, HTTP를 통해 통신하고, 논리 앱 정의의 흐름을 제어할 수 있는 작업을 제공합니다. 이러한 작업 중 일부는 다음을 포함합니다.

| |  |  |  |
| --- | --- | --- | --- |
| [![API 아이콘][HTTPicon]<br/>**HTTP**][httpdoc] | 논리 앱을 사용하여 HTTP를 통해 끝점과 통신합니다.| [![API 아이콘][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | C# 또는 node.js의 사용자 지정 코드 조각을 실행하는 함수를 만들고 논리 앱에서 이러한 함수를 사용합니다.  |
| [![API 아이콘][HTTP-Requesticon]<br/>**요청**][HTTP-Requestdoc] | 일반적으로 다른 응용 프로그램의 webhook로 사용되는 호출 가능한 HTTPS URL을 제공합니다. 논리 앱이 이 URL에 대한 요청을 받으면 논리 앱이 시작됩니다. | [![API 아이콘][Recurrenceicon]<br/>**일정**][recurrencedoc] | 간단하거나 복잡한 되풀이 일정에 따라 논리 앱을 시작합니다. 예를 들어, 오전 9시와 오후 5시 사이의 매월 마지막 주 금요일에서 매일 되풀이에서 매시간 되풀이까지 간단한 일정을 만듭니다. |
| [![API 아이콘][CallLogicApp-icon]<br/>**호출<br/>논리 앱**][nested-logic-appdoc] | 중첩된 논리 앱을 호출합니다. 요청 트리거를 사용하여 중첩된 논리 앱으로 논리 앱을 호출할 수 있습니다.| [![API 아이콘][API/Web-Appicon]<br/>**API 앱**][api/web-appdoc] | App Service API 앱을 호출합니다. Swagger를 사용하는 API Apps는 다른 첫 번째 클래스 작업과 같이 렌더링합니다.|

### <a name="standard-connectors"></a>표준 커넥터
다음 테이블에는 사용자들에게 가장 인기 있고 많이 사용되는 일부 커넥터가 나열되어 있습니다.

| |  |  |  |
| --- | --- | --- | --- |
| [![API 아이콘][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | 저장소 계정을 사용하여 작업을 자동화하려는 경우 이 커넥터를 자세히 살펴보아야 합니다. CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 지원합니다. | [![API 아이콘][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | 가장 많이 요청되는 커넥터 중 하나입니다. 리드가 포함된 워크플로를 자동화하는 데 도움이 되는 트리거와 작업 등을 갖고 있습니다. |
| [![API 아이콘][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Event Hub의 이벤트를 사용하고 게시합니다. 예를 들어 Event Hubs를 사용하여 논리 앱에서 출력을 가져온 다음 실시간 분석 공급자에게 보낼 수 있습니다. | [![API 아이콘][FTPicon]<br/>**FTP**][FTPdoc] | 인터넷에서 FTP 서버에 액세스할 수 있는 경우 파일 및 폴더를 작업하도록 워크플로를 자동화할 수 있습니다. <br/><br/>SFTP는 SFTP 커넥터에도 제공됩니다. |
| [![API 아이콘][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | 워크플로 내에서 Office 365 전자 메일 및 이벤트를 사용하는 많은 트리거와 많은 작업이 있습니다. <br/><br/>이 커넥터는 휴가 요청, 경비 보고서 등을 승인하는 *승인 전자 메일* 작업을 포함하고 있습니다. <br/><br/>Office 365 사용자에게는 Office 365 사용자 커넥터도 제공됩니다.| [![API 아이콘][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Salesforce 계정으로 간편하게 로그인하여 리드 등의 개체에 액세스할 수 있습니다. | 
| [![API 아이콘][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | 논리 앱 내에서 가장 인기 있는 커넥터이며 비동기 메시지를 수행하고 큐, 구독 및 토픽을 게시/구독하는 트리거와 작업을 포함하고 있습니다. |  [![API 아이콘][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | SharePoint로 작업을 수행하며 자동화가 도움이 되는 경우 이 커넥터를 자세히 살펴보시기 바랍니다. 온-프레미스 SharePoint 및 SharePoint Online과 함께 사용할 수 있습니다. |
| [![API 아이콘][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | 가장 많이 사용되는 커넥터 중 하나이며, 온-프레미스 SQL Server 및 Azure SQL Database에 연결할 수 있습니다. | [![API 아이콘][Twittericon]<br/>**Twitter**][Twitterdoc] | Twitter 계정으로 간단하게 로그인한 후 새 트윗이 게시될 때 워크플로를 시작합니다. 그런 다음 SQL 데이터베이스 또는 SharePoint 목록에 이러한 트윗을 저장합니다. | 

### <a name="on-premises-connectors"></a>온-프레미스 커넥터 

온-프레미스 커넥터는 온-프레미스 서버의 데이터에 대한 액세스를 제공합니다.  온-프레미스 서버에 대한 연결에는 네트워크 인프라를 구성할 필요가 없는 보안 통신 채널을 제공하는 [온-프레미스 데이터 게이트웨이][gatewaydoc]가 필요합니다.  커넥터의 일부는 다음을 포함합니다.

|  |  |  |  |
| --- | --- | --- | --- |
| [![API 아이콘][db2icon]<br/>**DB2**][db2doc] | [![API 아이콘][oracle-DB-icon]<br/>**Oracle DB**][oracle-db-doc] | [![API 아이콘][sharepointicon]<br/>**SharePoint</br> 서버**][sharepointserver] | [![API 아이콘][filesystem-icon]<br/>**파일</br> 시스템**][filesystemdoc] |
[![API 아이콘][sql-servericon]<br/>**SQL</br> Server**][sql-serverdoc] | ![API 아이콘][Biztalk-Servericon]<br/>**BizTalk</br> Server**| |

### <a name="integration-account-connectors"></a>통합 계정 커넥터 

EIP(엔터프라이즈 통합 팩)는 BizTalk Server 커뮤니티에 잘 알려진 커넥터를 포함하고 있습니다. [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 구입할 때 다음 커넥터도 함께 제공됩니다. 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API 아이콘][as2icon]<br/>**AS2</br> 디코딩**][as2decode] | [![API 아이콘][as2icon]<br/>**AS2</br> 인코딩**][as2encode] | [![API 아이콘][x12icon]<br/>**EDIFACT</br> 디코딩**][EDIFACTdecode] | [![API 아이콘][x12icon]<br/>**EDIFACT</br> 인코딩**][EDIFACTencode] |
[![API 아이콘][flatfileicon]<br/>**플랫 파일</br> 인코딩**][flatfiledoc] | [![API 아이콘][flatfiledecodeicon]<br/>**플랫 파일</br> 디코딩**][flatfiledecodedoc] | [![API 아이콘][integrationaccounticon]<br/>**통합<br/>계정**][integrationaccountdoc] | [![API 아이콘][xmltransformicon]<br/>**변형<br/>XML**][xmltransformdoc] |
| [![API 아이콘][x12icon]<br/>**X12</br> 디코딩**][x12decode] | [![API 아이콘][x12icon]<br/>**X12</br> 인코딩**][x12encode] | [![API 아이콘][xmlvalidateicon]<br/>**XML <br/>유효성 검사**][xmlvalidatedoc] | [![API 아이콘][liquidicon]<br/>**JSON<br/> 변환**][JSONliquidtransformdoc] |

### <a name="enterprise-connectors"></a>엔터프라이즈 커넥터

논리 앱 내의 엔터프라이즈 응용 프로그램에 연결합니다.

|  |  |
| --- | --- |
|[![API 아이콘][MQicon]<br/>**MQ**][mqdoc]|[![API 아이콘][SAPicon]<br/>**SAP**][sapconnector]|

> [!TIP]
> Azure 계정에 등록하기 전에 Azure Logic Apps를 시작하려면 [Logic Apps 사용해 보기](https://tryappservice.azure.com/?appservice=logic)로 이동하세요. 단기 시작 논리 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## <a name="connectors-as-triggers-and-actions"></a>트리거 및 작업으로 사용되는 커넥터

**트리거**는 논리 앱의 인스턴스를 시작하거나 실행합니다. 일부 커넥터는 특정 이벤트가 발생하면 앱에 알리는 트리거를 제공합니다. 예를 들어 FTP 커넥터에는 파일이 업데이트되면 논리 앱을 시작하는 `OnUpdatedFile` 트리거가 있습니다. 

Logic Apps에는 다음과 같은 종류의 트리거가 포함되어 있습니다.  

* *폴링 트리거*: 이 트리거는 지정된 빈도로 서비스를 폴링하여 새 데이터를 확인합니다. 

    새 데이터를 사용할 수 있게 되면 논리 앱의 새 인스턴스가 해당 데이터를 입력으로 사용하여 실행됩니다. 

* *트리거 푸시*: 이 트리거는 끝점의 데이터 또는 발생할 이벤트를 수신한 다음 논리 앱의 새 인스턴스를 트리거합니다.

* *되풀이 트리거*: 이 트리거는 정해진 일정에 따라 논리 앱의 인스턴스를 인스턴스화합니다.

또한 커넥터는 워크플로에서 사용할 수 있는 **작업**을 제공합니다. 예를 들어 논리 앱은 데이터를 조회한 후 나중에 이 데이터를 논리 앱에서 사용할 수 있습니다. 좀 더 구체적으로 말해서, SQL 데이터베이스에서 고객 데이터를 조회한 후 이 고객 데이터를 사용하여 워크플로를 빌드할 수 있습니다. 

> [!TIP]
> [커넥터 개요](connectors-overview.md)는 트리거 및 작업에 대한 세부 정보를 제공합니다. 


## <a name="message-manipulation-actions"></a>메시지 조작 작업

논리 앱에는 페이로드 데이터를 변경 또는 조작할 수 있는 기본 제공 작업이 포함되어 있습니다. 기본 제공 **데이터 작업** 커넥터는 다음과 같은 작업을 포함하고 있습니다. 

| | |
|---|---|
| **작성** | 나중에 또는 워크플로를 빌드할 때 사용할 값 또는 개체를 빌드하거나 생성합니다. 예를 들어 여러 단계의 값을 사용하여 JSON 개체를 작성하거나 나중에 논리 앱을 실행할 때 참조할 상수를 계산할 수 있습니다. |
| **CSV 테이블 만들기**<br/>**HTML 테이블 만들기** | 배열 결과 집합을 CSV 또는 HTML 테이블로 변환합니다. 예를 들어 CRM "레코드 나열" 작업을 추가하고, 오늘 추가된 레코드에 대한 필터를 추가합니다. 그런 다음 결과를 전자 메일의 HTML 테이블로 보냅니다. |
| **배열 필터링**(쿼리) | 결과 집합을 관심 있는 항목으로 필터링합니다. 예를 들어 `#Azure`로 모든 트윗을 검색한 후 반환된 트윗을 "필터링"하여 `Tweeted_by_followers > 50`인 결과만 반환합니다. |
| **Join** | 구분 기호를 사용하여 배열을 조인합니다. 예를 들어 핵심 문구 검색 작업은 핵심 문구 배열을 반환합니다. `,` 또는 비슷한 것을 사용하여 핵심 문구를 "조인"할 수 있습니다. 따라서 `["Some", "Phrase"]` 대신 `"Some, Phrase"`가 있습니다. |
| **JSON 구문 분석** | 디자이너에서 JSON 개체의 값을 구문 분석하고 액세스합니다. 예를 들어 Azure Function이 JSON 페이로드를 반환하는 경우 해당 페이로드를 구문 분석하여 나중에 다른 단계에서 JSON 속성에 액세스할 수 있습니다. 또한 이 작업은 JSON이 런타임에 지정된 스키마와 일치하는지 확인합니다. | 
| **Select** | 추가 처리를 위해 배열의 특정 속성을 선택합니다. SQL에서 "레코드 나열" 작업을 수행했더니 15개 열이 반환되는 경우 그 중에서 추가로 처리할 열 몇 개만 선택합니다. 출력은 사용자가 선택하는 속성만 포함하는 배열입니다. |

## <a name="custom-connectors-and-azure-certification"></a>사용자 지정 커넥터 및 Azure 인증 

사용자 지정 코드를 실행하거나 커넥터로 사용할 수 없는 API를 호출하려면 [REST 기반 API Apps를 만들어](../logic-apps/logic-apps-create-api-app.md) Logic Apps 플랫폼을 확장할 수 있습니다. 구독에서 모든 논리 앱을 사용할 수 있도록 하는 고유한 [사용자 지정 커넥터](../logic-apps/custom-connector-overview.md)를 만들 수도 있습니다.

사용자 지정 API Apps를 Azure에서 공개하고 사용할 수 있게 하려면 [Microsoft 인증을 위해 커넥터를 제출](../logic-apps/custom-connector-submit-certification.md)합니다.

## <a name="get-help"></a>도움말 보기

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 확인하려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Azure Logic Apps 및 커넥터 개선에 도움을 주려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.

커넥터 항목이나 중요하다고 생각 한 세부 정보를 빠뜨린 것이 있습니까? 그렇다면 이 기존 항목에 추가하거나 직접 작성해주세요. 설명서는 오픈 소스이며 GitHub에서 호스트됩니다. [GitHub 리포지토리](https://github.com/Microsoft/azure-docs)에서 시작하세요. 

## <a name="next-steps"></a>다음 단계
* [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [논리 앱에 대한 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)
* [논리 앱 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[gatewaydoc]: ../logic-apps/logic-apps-gateway-connection.md "온-프레미스 데이터 게이트웨이를 사용하여 논리 앱에서 온-프레미스 데이터 원본에 연결"
[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "App Service API Apps과 논리 앱 통합"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage 커넥터와 Blob 컨테이너의 파일 관리"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "논리 앱을 Azure Functions와 통합"
[db2doc]: ./connectors-create-api-db2.md "클라우드 또는 온-프레미스에서 IBM DB2에 연결합니다. 행 업데이트, 테이블 가져오기 등"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Dynamics CRM Online에 연결하여 CRM Online 데이터를 사용할 수 있습니다."
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs에 연결합니다. Logic Apps과 Event Hubs 간 이벤트 주고 받기"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "온-프레미스 파일 시스템에 연결"
[ftpdoc]: ./connectors-create-api-ftp.md "파일 업로드, 가져오기, 삭제 등과 같은 FTP 태스크의 경우 FTP / FTPS 서버에 연결"
[httpdoc]: ./connectors-native-http.md "HTTP 커넥터를 사용하여 HTTP 호출"
[http-requestdoc]: ./connectors-native-reqres.md "HTTP 요청에 작업 추가 및 논리 앱에 대한 응답"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "HTTP + Swagger 커넥터를 사용하여 HTTP 호출"
[informixdoc]: ./connectors-create-api-informix.md "클라우드 또는 온-프레미스에서 Informix에 연결합니다. 행 읽기, 테이블 나열 등"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "중첩된 워크플로와 논리 앱 통합"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Office 365 계정에 연결합니다. 메일 전송과 수신, 일정과 연락처 관리 등"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle 데이터베이스에 연결하여 행 추가, 삽입, 삭제 등"
[mqdoc]: ./connectors-create-api-mq.md "MQ 온-프레미스 또는 Azure에 연결하고 메시지 보내기 및 받기"
[recurrencedoc]:  ./connectors-native-recurrence.md "논리 앱에 대한 되풀이 작업 트리거"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Salesforce 계정에 연결합니다. 계정, 잠재 고객, 영업 기회 관리 등"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "온-프레미스 SAP 시스템에 연결"
[service-busdoc]: ./connectors-create-api-servicebus.md "Service Bus 큐 및 항목에서 메시지를 보내고 Service Bus 큐 및 구독에서 메시지를 받습니다."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "SharePoint Online에 연결합니다. 문서 관리, 항목 나열 등"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "온-프레미스 서버의 SharePoint에 연결합니다. 문서 관리, 항목 나열 등"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Azure SQL Database 또는 SQL server에 연결합니다. SQL Database 테이블에서 항목 생성, 업데이트, 가져오기 및 삭제"
[twitterdoc]: ./connectors-create-api-twitter.md "Twitter에 연결합니다. 타임라인 가져오기, 트윗에 게시 등"
[webhookdoc]: ./connectors-native-webhook.md "논리 앱에 Webhook 작업 및 트리거 추가"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "엔터프라이즈 통합 AS2에 대해 알아봅니다."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "엔터프라이즈 통합 X12에 대해 알아봅니다."
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "엔터프라이즈 통합 XML 유효성 검사에 대해 알아봅니다."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "엔터프라이즈 통합 변환에 대해 알아봅니다."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "엔터프라이즈 통합 AS2 디코딩에 대해 알아봅니다."
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "엔터프라이즈 통합 AS2 인코딩에 대해 알아봅니다."
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "엔터프라이즈 통합 X12 디코딩에 대해 알아봅니다."
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "엔터프라이즈 통합 X12 인코딩에 대해 알아봅니다."
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "엔터프라이즈 통합 EDIFACT 디코딩에 대해 알아봅니다."
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "엔터프라이즈 통합 EDIFACT 인코딩에 대해 알아봅니다."
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정에서 스키마, 맵, 파트너 등을 조회"
[JSONliquidtransformdoc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid를 사용한 JSON 변환에 대해 알아보기"


[boxDoc]: ./connectors-create-api-box.md "상자에 연결합니다. 파일 업로드, 가져오기, 삭제, 나열 등"
[delaydoc]: ./connectors-native-delay.md "지연된 작업 수행"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Dropbox에 연결합니다. 파일 업로드, 가져오기, 삭제, 나열 등"
[facebookdoc]: ./connectors-create-api-facebook.md "Facebook에 연결합니다. 타임라인에 게시, 페이지 피드 가져오기 등"
[githubdoc]: ./connectors-create-api-github.md "GitHub에 연결 및 문제 추적"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Google 드라이브에 연결하여 데이터를 사용할 수 있습니다."
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Google 시트에 연결하여 시트를 수정할 수 있습니다."
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Google 작업에 연결하여 태스크를 관리할 수 있습니다."
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Google 달력에 연결하고 달력을 관리할 수 있습니다."
[http-responsedoc]: ./connectors-native-reqres.md "HTTP 요청에 작업 추가 및 논리 앱에 대한 응답"
[instagramdoc]: ./connectors-create-api-instagram.md "Instagram에 연결합니다. 이벤트에서 트리거 또는 작업"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "MailChimp 계정에 연결합니다. 메일 관리 및 자동화"
[mandrilldoc]: ./connectors-create-api-mandrill.md "통신을 위해 Mandrill에 연결"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Microsoft Translator에 연결합니다. 텍스트 번역, 언어 감지 등" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Office 365 비디오에 대한 비디오 정보, 비디오 목록과 채널 및 재생 URL 가져오기"
[onedrivedoc]: ./connectors-create-api-onedrive.md "개인 Microsoft OneDrive에 연결합니다. 파일 업로드, 삭제, 나열 등"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "비즈니스 Microsoft OneDrive에 연결합니다. 파일 업로드, 삭제, 나열 등"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Outlook 사서함에 연결합니다. 전자 메일, 일정, 연락처 관리 등"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Microsoft Project Online에 연결합니다. 프로젝트, 태스크, 리소스 관리 등"
[querydoc]: ./connectors-native-query.md "쿼리 작업을 사용하여 배열 선택 및 필터링"
[rssdoc]: ./connectors-create-api-rss.md "피드 항목을 게시하고 검색하며 새 항목을 RSS 피드에 게시할 때 작업을 트리거합니다."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "SendGrid에 연결합니다. 전자 메일 보내기 및 수신자 목록 관리"
[sftpdoc]: ./connectors-create-api-sftp.md "SFTP 계정에 연결합니다. 파일 업로드, 가져오기, 삭제 등"
[slackdoc]: ./connectors-create-api-slack.md "Slack에 연결하고 Slack 채널에 메시지 게시"
[smtpdoc]: ./connectors-create-api-smtp.md "SMTP 서버에 연결 및 첨부 파일과 함께 전자 메일 보내기"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "통신을 위해 SparkPost에 연결"
[trellodoc]: ./connectors-create-api-trello.md "Trello에 연결합니다. 프로젝트 관리 및 다른 사람과 작업 구성"
[twiliodoc]: ./connectors-create-api-twilio.md "Twilio에 연결합니다. 메시지 보내기 및 가져오기, 사용 가능한 번호 가져오기 수신 전화 번호 관리 등"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Wunderlist에 연결합니다. 태스크 및 할 일 목록 관리, 동기화된 업무 유지 등"
[yammerdoc]: ./connectors-create-api-yammer.md "Yammer에 연결합니다. 메시지 게시, 새 메시지 가져오기 등"
[youtubedoc]: ./connectors-create-api-youtube.md "YouTube에 연결합니다. 비디오 및 채널 관리"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[AppServices-icon]: ./media/apis-list/AppServices.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FileSystem-icon]: ./media/apis-list/filesystem.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/appservices.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[CallLogicApp-icon]: ./media/apis-list/calllogicapp.png
[Delayicon]: ./media/apis-list/delay.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
[liquidicon]: ./media/apis-list/liquidtransform.png
