---
title: Azure Logic Apps에 Oracle 데이터베이스 커넥터 추가 | Microsoft Docs
description: 논리 앱에서 Oracle 데이터베이스 커넥터 사용
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: ''
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: estfan; ladocs
ms.openlocfilehash: 57931b6c2f96debeb9ab7f1a3b6700f23e7d2a7e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-oracle-database-connector"></a>Oracle 데이터베이스 커넥터 시작

Oracle 데이터베이스 커넥터를 사용하여 기존 데이터베이스의 데이터를 사용하는 조직 워크플로를 만듭니다. 이 커넥터는 온-프레미스 Oracle 데이터베이스 또는 Oracle 데이터베이스가 설치된 Azure 가상 컴퓨터에 연결할 수 있습니다. 이 커넥터를 사용하여 다음을 수행할 수 있습니다.

* 고객 데이터베이스에 새 고객을 추가하거나 주문 데이터베이스에서 주문을 업데이트하여 워크플로를 작성합니다.
* 데이터의 행을 가져오고, 새 행을 삽입하고, 삭제하는 작업을 사용합니다. 예를 들어 Dynamics CRM Online에서 레코드가 만들어지면(트리거) Oracle 데이터베이스에 행을 삽입합니다(작업). 

이 아티클에서는 논리 앱에서 Oracle 데이터베이스 커넥터를 사용하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

* 지원되는 Oracle 버전: 
    * Oracle 9 이상
    * Oracle 클라이언트 소프트웨어 8.1.7 이상

* 온-프레미스 데이터 게이트웨이 설치 [논리 앱에서 온-프레미스 데이터에 연결](../logic-apps/logic-apps-gateway-connection.md)에 단계가 나열되어 있습니다. 게이트웨이는 온-프레미스 Oracle 데이터베이스 또는 Oracle DB가 설치된 Azure VM에 연결하는 데 필요합니다. 

    > [!NOTE]
    > 온-프레미스 데이터 게이트웨이는 브리지 역할로, 온-프레미스 데이터(클라우드의 데이터가 아님)와 논리 앱 간의 보안 데이터 전송을 제공합니다. 여러 서비스 및 데이터 원본에 동일한 게이트웨이를 사용할 수 있습니다. 따라서 게이트웨이를 한 번만 설치해야 할 수 있습니다.

* 온-프레미스 데이터 게이트웨이를 설치한 컴퓨터에 Oracle 클라이언트를 설치합니다. Oracle에서 제공하는 64비트 .NET용 Oracle Data Provider를 설치해야 합니다.  

  [Windows x64용 64비트 ODAC 12c 릴리스 4(12.1.0.2.4)](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Oracle 클라이언트가 설치되지 않으면 연결을 생성하거나 사용할 때 오류가 발생합니다. 이 아티클의 일반적인 오류를 참조하세요.


## <a name="add-the-connector"></a>커넥터 추가

> [!IMPORTANT]
> 이 연결에는 트리거가 필요하지 않습니다. 작업만 있습니다. 따라서 논리 앱을 만들 때 **일정 - 되풀이** 또는 **요청/응답 - 응답**처럼 논리 앱을 시작하는 다른 트리거를 추가합니다. 

1. [Azure Portal](https://portal.azure.com)에서 빈 논리 앱을 만듭니다.

2. 논리 앱 시작 시 **요청/응답 - 요청** 트리거를 선택합니다. 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. **저장**을 선택합니다. 저장하면 요청 URL이 자동으로 생성됩니다. 

4. **새 단계**를 선택한 다음 **작업 추가**를 선택합니다. 사용 가능한 작업을 보려면 `oracle`을 입력합니다. 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > 이 방법은 커넥터에 대해 사용 가능한 트리거 및 작업을 확인하는 가장 빠른 방법이기도 합니다. 커넥터 이름의 일부를 입력합니다(예: `oracle`). 디자이너에 모든 트리거 및 작업이 나열됩니다. 

5. 작업 중 하나를 선택합니다(예: **Oracle 데이터베이스 - 행 가져오기**). **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택합니다. Oracle 서버 이름, 인증 방법, 사용자 이름, 암호를 입력하고 게이트웨이를 선택합니다.

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. 연결되었으면 목록에서 테이블을 선택하고 테이블에 행 ID를 입력합니다. 테이블에 대한 식별자를 알아야 합니다. 모르는 경우 Oracle DB 관리자에게 연락하고 `select * from yourTableName`에서 출력을 가져옵니다. 그러면 진행하는 데 필요한 식별 가능한 정보가 제공됩니다.

    다음 예제에서는 인사 관리 데이터베이스에서 작업 데이터가 반환됩니다. 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. 다음 단계에서는 다른 커넥터를 사용하여 워크플로를 작성할 수 있습니다. Oracle에서 데이터 가져오기를 테스트하려면 전자 메일 보내기 커넥터(예: Office 365 또는 Gmail) 중 하나를 사용하여 Oracle 데이터가 포함된 전자 메일을 직접 보냅니다. Oracle 테이블의 동적 토큰을 사용하여 전자 메일의 `Subject` 및 `Body`을 작성합니다.

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. 논리 앱을 **저장**한 후 **실행**을 선택합니다. 디자이너를 선택하고 실행 기록에서 상태를 확인합니다. 실패하면 실패한 메시지 행을 선택합니다. 디자이너가 열리고 어떤 단계에서 실패했는지와 오류 정보가 표시됩니다. 성공하면 추가된 정보와 함께 전자 메일을 받게 됩니다.


### <a name="workflow-ideas"></a>워크플로 아이디어

* \#oracle 해시 태그를 모니터하고 데이터베이스에 트윗을 배치하여 다른 응용 프로그램 내에서 사용하고 쿼리할 수 있도록 하려고 합니다. 논리 앱에서 `Twitter - When a new tweet is posted` 트리거를 추가하고 **#oracle** 해시 태그를 입력합니다. 그런 다음 `Oracle Database - Insert row` 작업을 추가하고 테이블을 선택합니다.

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* 메시지가 Service Bus 큐에 전송됩니다. 이러한 메시지를 가져와 데이터베이스에 넣으려고 합니다. 논리 앱에서 `Service Bus - when a message is received in a queue` 트리거를 추가하고 큐를 선택합니다. 그런 다음 `Oracle Database - Insert row` 작업을 추가하고 테이블을 선택합니다.

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>일반 오류

#### <a name="error-cannot-reach-the-gateway"></a>**오류**: 게이트웨이에 연결할 수 없습니다.

**원인**: 온-프레미스 데이터 게이트웨이가 클라우드에 연결할 수 없습니다. 

**완화**: 게이트웨이가 설치되어 있는 온-프레미스 컴퓨터에서 실행 중이며 인터넷에 연결할 수 있는지 확인합니다.  꺼져 있거나 절전 모드 상태일 수 있는 컴퓨터에는 게이트웨이를 설치하지 않는 것이 좋습니다. 온-프레미스 데이터 게이트웨이 서비스(PBIEgwService)를 다시 시작할 수도 있습니다.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**오류**: 사용 중인 공급자가 사용 중단됨: 'System.Data.OracleClient에 Oracle 클라이언트 소프트웨어 버전 8.1.7 이상이 필요합니다.' [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376)을 참조하여 공식 공급자를 설치합니다.

**원인**: 온-프레미스 데이터 게이트웨이가 실행 중인 컴퓨터에 Oracle 클라이언트 SDK가 설치되지 않았습니다.  

**해결 방법**: 온-프레미스 데이터 게이트웨이와 동일한 컴퓨터에 Oracle 클라이언트 SDK를 다운로드하여 설치합니다.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**오류**: 테이블 '[Tablename]'에 키 열이 정의되어 있지 않습니다.

**원인**: 테이블에 기본 키가 없습니다.  

**해결 방법**: Oracle 데이터베이스 커넥터에는 기본 키 열이 있는 테이블을 사용해야 합니다.

#### <a name="currently-not-supported"></a>현재 지원되지 않음

* 뷰 및 저장 프로시저 
* 복합 키가 있는 모든 테이블
* 테이블의 중첩된 개체 유형
 
## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/oracle/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="get-some-help"></a>도움 받기

[Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)에서는 질문하고, 질문에 답변하고, 다른 Logic Apps 사용자가 어떤 일을 하는지 확인할 수 있습니다. 

[http://aka.ms/logicapps-wish](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출해 주시면 Logic Apps 및 커넥터를 개선하는 데 도움이 될 수 있습니다. 


## <a name="next-steps"></a>다음 단계
[논리 앱을 만들고](../logic-apps/quickstart-create-first-logic-app-workflow.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 커넥터를 탐색합니다.
