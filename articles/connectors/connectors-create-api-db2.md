---
title: "Logic Apps에서 DB2 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 DB2 커넥터 개요"
services: 
documentationcenter: 
author: gplarsen
manager: erikre
editor: 
tags: connectors
ms.assetid: 1c6b010c-beee-496d-943a-a99e168c99aa
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.openlocfilehash: 7ad246ede7e891de42235443c1bc6a90b88ad5ac
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-db2-connector"></a>DB2 커넥터 시작
DB2용 Microsoft 커넥터는 Logic Apps를 IBM DB2 데이터베이스에 저장된 리소스에 연결합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 DB2 서버 컴퓨터와 통신하는 Microsoft 클라이언트를 포함합니다. Azure 가상화에서 실행되는 Windows용 IBM Bluemix dashDB 또는 IBM DB2와 같은 클라우드 데이터베이스, 온-프레미스 데이터 게이트웨이를 사용하는 온-프레미스 데이터베이스를 포함합니다. IBM DB2 플랫폼 및 버전의 [지원되는](connectors-create-api-db2.md#supported-db2-platforms-and-versions) 목록을 참조하세요(이 항목에서).

DB2 커넥터에서 지원하는 데이터베이스 작업은 다음과 같습니다.

* 데이터베이스 테이블 목록 표시
* SELECT를 사용하여 단일 행 읽기
* SELECT를 사용하여 전체 행 읽기
* INSERT를 사용하여 단일 행 추가
* UPDATE를 사용하여 단일 행 변경
* DELETE를 사용하여 단일 행 삭제

이 항목에서는 논리 앱에서 커넥터를 사용하여 데이터베이스 작업을 처리하는 방법을 보여 줍니다.

Logic Apps에 대해 자세히 알아보려면 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="available-actions"></a>사용 가능한 작업
DB2 커넥터에서 지원하는 논리 앱 작업은 다음과 같습니다.

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>테이블 나열
작업에 대한 논리 앱 만들기는 Microsoft Azure 포털을 통해 수행되는 여러 단계들로 구성됩니다.

논리 앱 내에서 DB2 데이터베이스에 테이블을 나열하는 작업을 추가할 수 있습니다. 작업은 커넥터가 `CALL SYSIBM.SQLTABLES`와(과) 같은 DB2 스키마 문을 처리하도록 지시합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `Db2getTables`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 설정하여 **7**을 입력합니다.  
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 `db2`를 입력한 다음 **DB2 - 테이블 가져오기(미리 보기)**를 선택합니다.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. **DB2 - 테이블 가져오기** 구성 창에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 사용할 수 있게 하는 **확인란**을 선택합니다. 설정이 클라우드에서 온-프레미스로 변경되었는지 확인합니다.
   
   * **서버**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `ibmserver01:50000`을(를) 입력합니다.
   * **데이터베이스**에 대한 값을 입력합니다. 예를 들어 `nwind`을(를) 입력합니다.
   * **인증**값을 선택합니다. 예를 들어 **기본**을 선택합니다.
   * **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `db2admin`을(를) 입력합니다.
   * **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
   * **게이트웨이**에 대한 값을 선택합니다. 예를 들어 **datagateway01**을 선택합니다.
7. **만들기**를 선택한 다음 **저장**을 선택합니다. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. **Db2getTables** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
9. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_tables**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 테이블 목록이 포함되어 있어야 합니다.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>연결 만들기
이 커넥터는 다음 연결 속성을 사용하는 호스팅되는 온-프레미스와 클라우드에서 데이터베이스에 대한 연결을 지원합니다. 

| 자산 | 설명 |
| --- | --- |
| 서버 |필수 사항입니다. 콜론으로 구분된 TCP/IP 포트 번호가 뒤에 붙는 IPv4 또는 IPv6 형식 중 하나로 구성되어 TCP/IP 주소 또는 별칭을 나타내는 문자열 값이 허용됩니다. |
| 데이터베이스 |필수 사항입니다. DRDA 관계형 데이터베이스 이름(RDBNAM)을 나타내는 문자열 값이 허용됩니다. z/OS용 IBM DB2 위치로 인식되는 데이터베이스인 z/OS용 DB2에는 16바이트 문자열이 허용됩니다. i 관계형 데이터베이스용 IBM DB2 데이터베이스로 인식되는 데이터베이스인 i5/OS용 DB2에는 18바이트 문자열이 허용됩니다. LUW용 DB2에는 8바이트 문자열이 허용됩니다. |
| 인증 |선택 사항입니다. 목록 항목 값, 즉 기본 또는 Windows (kerberos)가 허용됩니다. |
| 사용자 이름 |필수 사항입니다. 문자열 값을 허용합니다. z/OS용 DB2에는 8바이트 문자열이 허용됩니다. i용 DB2에는 10바이트 문자열이 허용됩니다. Linux 또는 UNIX용 DB2에는 8바이트 문자열이 허용됩니다. Windows용 DB2에는 30바이트 문자열이 허용됩니다. |
| 암호 |필수 사항입니다. 문자열 값을 허용합니다. |
| 게이트웨이 |필수 사항입니다. 저장소 그룹의 Logic Apps에 정의된 온-프레미스 데이터 게이트웨이를 나타내는 목록 항목 값이 허용됩니다. |

## <a name="create-the-on-premises-gateway-connection"></a>온-프레미스 게이트웨이 연결 만들기
이 커넥터는 온-프레미스 게이트웨이를 통해 온-프레미스 DB2 데이터베이스에 액세스할 수 있습니다. 자세한 내용은 게이트웨이 항목을 참조하세요. 

1. **게이트웨이** 구성 창에서 **게이트웨이를 통해 연결**을 사용할 수 있게 하는 **확인란**을 선택합니다. 설정이 클라우드에서 온-프레미스로 변경되었는지 확인합니다.
2. **서버**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `ibmserver01:50000`을(를) 입력합니다.
3. **데이터베이스**에 대한 값을 입력합니다. 예를 들어 `nwind`을(를) 입력합니다.
4. **인증**값을 선택합니다. 예를 들어 **기본**을 선택합니다.
5. **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `db2admin`을(를) 입력합니다.
6. **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
7. **게이트웨이**에 대한 값을 선택합니다. 예를 들어 **datagateway01**을 선택합니다.
8. **만들기** 를 선택하여 계속합니다. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>클라우드 연결 만들기
이 커넥터는 클라우드 DB2 데이터베이스에 액세스할 수 있습니다. 

1. **게이트웨이** 구성 창에서 **게이트웨이를 통해 연결** **확인란**을 비워둡니다. 
2. **연결 이름**에 대한 값을 입력합니다. 예를 들어 `hisdemo2`을(를) 입력합니다.
3. **DB2 서버 이름**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `hisdemo2.cloudapp.net:50000`을(를) 입력합니다.
4. **DB2 데이터베이스 이름**값을 입력합니다. 예를 들어 `nwind`을(를) 입력합니다.
5. **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `db2admin`을(를) 입력합니다.
6. **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
7. **만들기** 를 선택하여 계속합니다. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>SELECT를 사용하여 전체 행 가져오기
DB2 테이블의 모든 행을 가져오는 논리 앱 작업을 정의할 수 있습니다. 커넥터가 `SELECT * FROM AREA`와(과) 같은 DB2 SELECT 문을 처리하도록 지시합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `Db2getRows`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 `db2`를 입력한 다음 **DB2 - 행 가져오기(미리 보기)**를 선택합니다.
6. **행 가져오기(미리 보기)** 작업에서 **연결 변경**을 선택합니다.
7. **연결** 구성 창에서 **새로 만들기**를 선택합니다. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. **게이트웨이** 구성 창에서 **게이트웨이를 통해 연결** **확인란**을 비워둡니다.
   
   * **연결 이름**에 대한 값을 입력합니다. 예를 들어 `HISDEMO2`을(를) 입력합니다.
   * **DB2 서버 이름**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `HISDEMO2.cloudapp.net:50000`을(를) 입력합니다.
   * **DB2 데이터베이스 이름**값을 입력합니다. 예를 들어 `NWIND`을(를) 입력합니다.
   * **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `db2admin`을(를) 입력합니다.
   * **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
9. **만들기** 를 선택하여 계속합니다.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
11. 필요에 따라 **고급 옵션 표시** 를 선택하여 쿼리 옵션을 지정합니다.
12. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. **Db2getRows** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
14. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 행 목록이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>INSERT를 사용하여 단일 행 추가
DB2 테이블에서 한 행을 추가하는 논리 앱 작업을 정의할 수 있습니다. 이 작업은 커넥터가 `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`와(과) 같은 DB2 INSERT 문을 처리하도록 지시합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `Db2insertRow`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **db2**를 입력한 다음 **DB2 - 행 삽입(미리 보기)**을 선택합니다.
6. **DB2 - 행 삽입(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 연결을 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 `Area 99999` 유형의 **AREAID**에는 `99999`을(를) 입력하고 **REGIONID**에는 `102`을(를) 입력합니다. 
10. **저장**을 선택합니다.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. **Db2insertRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
12. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 새 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>SELECT를 사용하여 단일 행 가져오기
DB2 테이블에서 한 행을 가져오는 논리 앱 작업을 정의할 수 있습니다. 이 작업은 커넥터가 `SELECT FROM AREA WHERE AREAID = '99999'`와(과) 같은 DB2 SELECT WHERE 문을 처리하도록 지시합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: "**Db2getRow**"), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다. 
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **db2**를 입력한 다음 **DB2 - 행 가져오기(미리 보기)**를 선택합니다.
6. **행 가져오기(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 기존 연결을 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 **AREAID**에는 `99999`을(를) 입력합니다. 
10. 필요에 따라 **고급 옵션 표시** 를 선택하여 쿼리 옵션을 지정합니다.
11. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. **Db2getRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
13. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>UPDATE를 사용하여 단일 행 변경
DB2 테이블에서 한 행을 변경하는 논리 앱 작업을 정의할 수 있습니다. 이 작업은 커넥터가 `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`와(과) 같은 DB2 UPDATE 문을 처리하도록 지시합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `Db2updateRow`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **db2**를 입력한 다음 **DB2 - 행 업데이트(미리 보기)**를 선택합니다.
6. **DB2 - 행 삽입(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 기존 연결을 선택하려면 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 `Updated 99999` 유형의 **AREAID**에는 `99999`을(를) 입력하고 **REGIONID**에는 `102`을(를) 입력합니다. 
10. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. **Db2updateRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
12. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 새 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>DELETE를 사용하여 단일 행 삭제
DB2 테이블에서 한 행을 제거하는 논리 앱 작업을 정의할 수 있습니다. 이 작업은 커넥터가 `DELETE FROM AREA WHERE AREAID = '99999'`와(과) 같은 DB2 DELETE 문을 처리하도록 지시합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `Db2deleteRow`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다. 
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에서 **db2**를 선택한 다음 **DB2 - 행 삭제(미리 보기)**를 선택합니다.
6. **DB2 - 행 삭제(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 기존 연결을 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 **AREAID**에는 `99999`을(를) 입력합니다. 
10. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. **Db2deleteRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
12. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 삭제된 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>지원되는 DB2 플랫폼 및 버전
DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) 버전 10 및 11을 지원하는 IBM DB2 호환 제품(예: IBM Bluemix dashDB) 외에도 이 커넥터에서 지원하는 IBM DB2 플랫폼 및 버전은 다음과 같습니다.

* z/OS용 IBM DB2 11.1
* z/OS용 IBM DB2 10.1
* i용 IBM DB2 7.3
* i용 IBM DB2 7.2
* i용 IBM DB2 7.1
* LUW용 IBM DB2 11
* LUW용 IBM DB2 10.5

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/db2/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.

