---
title: Azure Log Analytics의 IT Service Management Connector | Microsoft Docs
description: 이 문서에서는 ITSMC(IT 서비스 관리 커넥터)의 개요 및 이 솔루션을 사용하여 Azure Log Analytics의 ITSM 작업 항목을 중앙에서 모니터링하고 관리하며 모든 문제를 신속하게 해결하는 방법에 대한 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: 8fb75484537d577cb19b04fa091bab69d6723c9b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>IT 서비스 관리 커넥터를 사용하여 ITSM 도구에 Azure 연결

![IT Service Management Connector 기호](./media/log-analytics-itsmc/itsmc-symbol.png)

ITSMC(IT 서비스 관리 커넥터)를 사용하면 Azure와 지원되는 ITSM(IT 서비스 관리) 제품/서비스를 연결할 수 있습니다.

Log Analytics 및 Azure Monitor와 같은 Azure 서비스는 Azure 및 Azure 이외의 리소스를 사용하여 문제를 검색, 분석 및 해결할 수 있는 도구를 제공합니다. 그러나 일반적으로 문제와 관련 작업 항목은 ITSM 제품/서비스에 있습니다. ITSM 커넥터는 Azure와 ITSM 도구 간에 양방향 연결을 제공하여 문제를 더 빨리 해결할 수 있도록 도와줍니다.

ITSMC는 다음 ITSM 도구와의 연결을 지원합니다.

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

ITSMC를 사용하면 다음 작업을 수행할 수 있습니다.

-  Azure 경고(메트릭 경고, 활동 로그 경고 및 Log Analytics 경고)에 따라 ITSM 도구에서 작업 항목을 만듭니다.
-  필요에 따라 인시던트 및 변경 요청 데이터를 ITSM 도구에서 Azure Log Analytics 작업 영역으로 동기화할 수 있습니다.


다음 단계를 통해 ITSM 커넥터를 사용하여 시작하면 됩니다.

1.  [ITSM 커넥터 솔루션 추가](#adding-the-it-service-management-connector-solution)
2.  [ITSM 연결 만들기](#creating-an-itsm-connection)
3.  [연결 사용](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>IT Service Management Connector 솔루션 추가

연결을 만들기 전에 ITSM 커넥터 솔루션을 추가해야 합니다.

1.  Azure Portal에서 **+ 새로 만들기** 아이콘을 클릭합니다.

    ![Azure 새 리소스](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Marketplace의 **IT 서비스 관리 커넥터**를 검색하고 **만들기**를 클릭합니다.

    ![ITSMC 솔루션 추가](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  **OMS 작업 영역** 섹션에서 솔루션을 설치하려는 Azure Log Analytics 작업 영역을 선택합니다.
4.  **OMS 작업 영역 설정** 섹션에서 솔루션 리소스 만들려는 ResourceGroup을 선택합니다.

    ![ITSMC 작업 영역](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  **만들기**를 클릭합니다.

솔루션 리소스를 배포할 때 창의 오른쪽 상단에 알림이 표시됩니다.


## <a name="creating-an-itsm--connection"></a>ITSM 연결 만들기

솔루션을 설치하면 연결을 만들 수 있습니다.

연결을 만들려면 ITSM 커넥터 솔루션과의 연결을 허용하도록 ITSM 도구를 준비해야 합니다.  

연결하려는 ITSM 제품에 따라 다음 단계를 사용합니다.

- [SCSM(System Center Service Manager)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

ITSM 도구를 준비하고 나면 아래 단계에 따라 연결을 만듭니다.

1.  **모든 리소스**로 이동하여 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.
2.  왼쪽 창의 **작업 영역 데이터 원본**에서 **ITSM 연결**을 클릭합니다.
    ![ITSM 연결](./media/log-analytics-itsmc/itsm-connections.png)

    이 페이지에는 연결 목록이 표시됩니다.
3.  **연결 추가**를 클릭합니다.

    ![ITSM 연결 추가](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  [ITSM 제품/서비스 문서를 사용하여 ITSMC 연결 구성](log-analytics-itsmc-connections.md)에 설명된 대로 연결 설정을 지정합니다.

    > [!NOTE]

    > 기본적으로 ITSMC는 24시간 마다 한 번씩 연결의 구성 데이터를 새로 고칩니다. 적용한 편집 내용 또는 템플릿 업데이트에 대해 연결 데이터를 즉시 새로 고치려면 연결 옆에 표시된 “새로 고침” 단추를 클릭합니다.

    ![연결 새로 고침](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>솔루션 사용
   ITSM 커넥터 솔루션을 사용하면 Azure 경고, Log Analytics 경고 및 Log Analytics 로그 레코드에서 작업 항목을 만들 수 있습니다.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure Alerts에서 ITSM 작업 항목 만들기

ITSM 연결이 만들어지면 **작업 그룹**에서 **ITSM 작업**을 사용하여 Azure 경고를 기반으로 ITSM 도구에서 작업 항목을 만들 수 있습니다.

작업 그룹은 Azure Alerts를 위해 모듈 방식으로 다시 사용할 수 있는 방법을 제공합니다. Azure Portal에서 메트릭 경고, 활동 로그 경고 및 Azure Log Analytics 알림과 함께 작업 그룹을 사용할 수 있습니다.

이렇게 하려면 다음 절차를 수행합니다.

1. Azure Portal에서 **모니터**를 클릭합니다.
2. 왼쪽 창에서 **작업 그룹**을 클릭합니다. **작업 그룹 추가** 창이 나타납니다.

    ![작업 그룹](media/log-analytics-itsmc/action-groups.png)

3. 작업 그룹에 대해 **이름** 및 **짧은 이름**을 제공합니다. 작업 그룹을 만들려는 **리소스 그룹** 및 **구독**을 선택합니다.

    ![작업 그룹 세부 정보](media/log-analytics-itsmc/action-groups-details.png)

4. 작업 목록의 **작업 유형** 드롭다운 메뉴에서 **ITSM**을 선택합니다. 해당 작업에 대한 **이름**을 제공하고 **세부 정보 편집**을 클릭합니다.
5. Log Analytics 작업 영역이 있는 **구독**을 선택합니다. 작업 영역 이름 앞에 나오는 **연결** 이름(ITSM Connector 이름)을 선택합니다. 예를 들어 "MyITSMMConnector(MyWorkspace)"와 같습니다.

    ![ITSM 작업 세부 정보](./media/log-analytics-itsmc/itsm-action-details.png)

6. 드롭다운 메뉴에서 **작업 항목** 유형을 선택합니다.
   기존 템플릿을 사용하거나 ITSM 제품에 필요한 필드를 채우도록 선택합니다.
7. **확인**을 클릭합니다.

Azure 경고 규칙을 만들거나 편집할 때는 ITSM 작업이 있는 작업 그룹을 사용합니다. 경고가 트리거되면 작업 항목이 ITSM 도구에 만들어지거나 업데이트됩니다.

>[!NOTE]

> ITSM 작업의 가격 책정에 대한 자세한 내용은 작업 그룹의 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.


## <a name="create-itsm-work-items-from-log-analytics-alerts"></a>Log Analytics 경고에서 ITSM 작업 항목 만들기

다음 절차를 사용하여 Azure Log Analytics 포털에서 경고 규칙을 구성하여 ITSM 도구에서 작업 항목을 만들 수 있습니다.

1. **로그 검색** 창에서 로그 검색 쿼리를 실행하여 데이터를 봅니다. 쿼리 결과는 작업 항목의 원본입니다.
2. **로그 검색**에서 **경고**를 클릭하여 **경고 규칙 추가** 페이지를 엽니다.

    ![Log Analytics 화면](./media/log-analytics-itsmc/itsmc-work-items-for-azure-alerts.png)

3. **경고 규칙 추가** 창에서 **이름**, **심각도**, **검색 쿼리** 및 **경고 조건**(기간/메트릭 측정)에 대한 필수 정보를 제공합니다.
4. **ITSM 작업**에 대해 **예**를 선택합니다.
5. **연결 선택** 목록에서 ITSM 연결을 선택합니다.
6. 필요에 따라 세부 정보를 제공합니다.
7. 이 경고의 각 로그 항목에 대해 별도 작업 항목을 만들려면 **각 로그 항목에 대해 개별 작업 항목 만들기** 확인란을 선택합니다.

    또는

    이 경고에서 제한없는 수의 로그 항목에 대해 하나의 작업 항목만 만들려면 이 확인란을 선택 취소합니다.

7. **저장**을 클릭합니다.

**설정>경고**에서 만든 Log Analytics 경고를 볼 수 있습니다. 지정된 경고 조건이 충족되면 해당 ITSM 연결의 작업 항목이 만들어집니다.


## <a name="create-itsm-work-items-from-log-analytics-log-records"></a>Log Analytics 로그 레코드에서 ITSM 작업 항목 만들기

로그 레코드에서 직접 연결된 ITSM 원본의 작업 항목을 만들 수도 있습니다. 이는 연결이 제대로 작동하는 경우 테스트하는 데 사용할 수 있습니다.


1. **로그 검색**에서 필요한 데이터를 검색하고 세부 정보를 선택한 후 **작업 항목 만들기**를 클릭합니다.

    **ITSM 작업 항목 만들기** 창이 나타납니다.

    ![Log Analytics 화면](media/log-analytics-itsmc/itsmc-work-items-from-azure-logs.png)

2.   다음 세부 정보를 추가합니다.

  - **작업 항목 제목**: 작업 항목의 제목입니다.
  - **작업 항목 설명**: 새 작업 항목에 대한 설명입니다.
  - **영향을 받는 컴퓨터**: 이 로그 데이터가 있는 컴퓨터의 이름입니다.
  - **연결 선택**: 이 작업 항목을 만들려는 ITSM 연결입니다.
  - **작업 항목**: 작업 항목의 유형입니다.

3. 인시던트에 대한 기존 작업 항목 템플릿을 사용하려면 **템플릿을 기준으로 작업 항목 생성** 옵션에서 **예**를 클릭한 다음 **만들기**를 클릭합니다.

    또는

    사용자 지정한 값을 제공하려는 경우 **아니요**를 클릭합니다.

4. **연락처 유형**, **영향**, **긴급도**, **범주** 및 **하위 범주** 텍스트 상자에 적절한 값을 입력하고 **만들기**를 클릭합니다.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>인시던트 및 변경 요청 데이터 시각화 및 분석

연결을 설정할 때 구성에 따라 ITSM 커넥터는 최대 120일간 인시던트 및 변경 요청 데이터를 동기화할 수 있습니다. [다음 섹션](#additional-information)에서는 이 데이터에 대한 로그 레코드 스키마가 제공됩니다.

솔루션의 ITSM 커넥터 대시보드를 사용하여 인시던트 및 변경 요청 데이터를 시각화할 수 있습니다.

![Log Analytics 화면](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

대시보드는 커넥터 상태에 대한 정보도 제공하며 연결 ​​상태와 관련된 모든 문제를 분석하는 출발점으로 사용할 수 있습니다.

또한 서비스 맵 솔루션 내에서 영향 받는 컴퓨터에 대해 동기화된 인시던트를 시각화할 수 있습니다.

서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 따라서 생각처럼 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다. 서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다. [자세히 알아보기](../operations-management-suite/operations-management-suite-service-map.md).

서비스 맵 솔루션을 사용하는 경우 다음 예와 같이 ITSM 솔루션에서 만들어진 서비스 데스크 항목을 볼 수 있습니다.

![Log Analytics 화면](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

추가 정보: [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>추가 정보

### <a name="data-synced-from-itsm-product"></a>ITSM 제품에서 동기화된 데이터
인시던트 및 변경 요청은 연결 구성에 따라 ITSM 제품에서 Log Analytics 작업 영역으로 동기화됩니다.

다음 정보는 ITSMC에서 수집된 데이터 예제를 보여 줍니다.

> [!NOTE]

> Log Analytics로 가져온 작업 항목 형식에 따라 **ServiceDesk_CL**에는 다음 필드가 포함됩니다.

**작업 항목:** **인시던트**  
ServiceDeskWorkItemType_s="Incident"

**필드**

- ServiceDeskConnectionName
- 서비스 데스크 ID
- 시스템 상태
- 긴급도
- 영향
- 우선 순위
- 에스컬레이션
- 만든 사람
- 해결한 사람
- 종결한 사람
- 원본
- 할당 대상
- Category
- 제목
- 설명
- 만든 날짜
- 종결한 날짜
- 해결한 날짜
- 마지막으로 수정한 날짜
- Computer


**작업 항목:** **변경 요청**

ServiceDeskWorkItemType_s="ChangeRequest"

**필드**
- ServiceDeskConnectionName
- 서비스 데스크 ID
- 만든 사람
- 종결한 사람
- 원본
- 할당 대상
- 제목
- type
- Category
- 시스템 상태
- 에스컬레이션
- 충돌 상태
- 긴급도
- 우선 순위
- 위험
- 영향
- 할당 대상
- 만든 날짜
- 종결한 날짜
- 마지막으로 수정한 날짜
- 요청한 날짜
- 예상된 시작 날짜
- 예상된 종료 날짜
- 작업 시작 날짜
- 작업 종료 날짜
- 설명
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 인시던트에 대한 출력 데이터

| Log Analytics 필드 | ServiceNow 필드 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | 시스템 상태 |
| Urgency_s |긴급도 |
| Impact_s |영향|
| Priority_s | 우선 순위 |
| CreatedBy_s | 보고자 |
| ResolvedBy_s | 해결한 사람|
| ClosedBy_s  | 종결한 사람 |
| Source_s| 연락처 유형 |
| AssignedTo_s | 할당 대상  |
| Category_s | Category |
| Title_s|  간단한 설명 |
| Description_s|  메모 |
| CreatedDate_t|  열림 |
| ClosedDate_t| closed|
| ResolvedDate_t|해결됨|
| Computer  | 구성 항목 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 변경 요청에 대한 출력 데이터

| Log Analytics | ServieNow 필드 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 요청자 |
| ClosedBy_s | 종결한 사람 |
| AssignedTo_s | 할당 대상  |
| Title_s|  간단한 설명 |
| Type_s|  type |
| Category_s|  Category |
| CRState_s|  시스템 상태|
| Urgency_s|  긴급도 |
| Priority_s| 우선 순위|
| Risk_s| 위험|
| Impact_s| 영향|
| RequestedDate_t  | 요청한 날짜 |
| ClosedDate_t | 종결한 날짜 |
| PlannedStartDate_t  |     예상된 시작 날짜 |
| PlannedEndDate_t  |   예상된 종료 날짜 |
| WorkStartDate_t  | 실제 시작 날짜 |
| WorkEndDate_t | 실제 종료 날짜|
| Description_s | 설명 |
| Computer  | 구성 항목 |


## <a name="troubleshoot-itsm-connections"></a>ITSM 연결 문제 해결
1.  연결된 원본의 UI에서 **연결을 저장하는 동안 오류 발생** 메시지와 함께 연결에 실패하는 경우 다음 단계를 수행합니다.
 - ServiceNow, Cherwell 및 Provence 연결의 경우  
    - 각 연결에 대한 사용자 이름, 암호 클라이언트 ID 및 클라이언트 비밀을 올바르게 입력했는지 확인합니다.  
    - 해당 ITSM 제품에 연결하는 데 충분한 권한이 있는지 확인합니다.  
 - Service Manager 연결의 경우  
    - 웹앱이 성공적으로 배포되고 하이브리드 연결이 만들어졌는지 확인합니다. 온-프레미스 Service Manager 컴퓨터와의 연결이 성공적으로 설정되었는지 확인하려면 [하이브리드 연결](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) 설정 설명서에 따라 웹앱 URL을 방문합니다.  

2.  ServiceNow의 데이터가 Log Analytics와 동기화되지 않으면 ServiceNow 인스턴스가 중지 상태가 아닌지 확인합니다. ServiceNow Dev 인스턴스가 오랫동안 유휴 상태일 때 중지 상태로 전환되는 경우가 있습니다. 다른 문제를 보고합니다.
3.  OMS 경고가 발생하지만 ITSM 제품에 작업 항목이 만들어지지 않거나 구성 항목이 작업 항목에 또는 기타 일반적인 정보에 대해 만들어지거나 연결되지 않는 경우 다음 위치를 확인합니다.
 -  ITSMC: 연결/작업 항목/컴퓨터 등에 대한 요약을 보여 줍니다. **커넥터 상태**를 보여 주는 타일을 클릭하면 관련 쿼리가 있는 **로그 검색**으로 이동됩니다. 자세한 정보는 LogType_S가 ERROR(오류)인 로그 레코드를 살펴봅니다.
 - **로그 검색** 페이지: `*`ServiceDeskLog_CL`*` 쿼리를 사용하여 오류/관련 정보를 직접 확인합니다.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager 웹앱 배포 문제 해결
1.  웹앱 배포 문제가 발생한 경우 구독에 리소스 생성/배포 권한이 있는지 확인합니다.
2.  [스크립트](log-analytics-itsmc-service-manager-script.md)를 실행할 때 **"개체 참조가 개체의 인스턴스로 설정되지 않았습니다."** 오류가 발생하는 경우 **사용자 구성** 섹션에서 유효한 값을 입력했는지 확인합니다.
3.  Service Bus Relay 네임스페이스 만들기에 실패한 경우 구독에 필요한 리소스 공급자가 등록되어 있는지 확인합니다. 등록되지 않은 경우 Azure Portal에서 수동으로 서비스 버스 릴레이 네임스페이스를 만듭니다. Azure Portal에서 [하이브리드 연결을 만드는](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) 동안 만들 수도 있습니다.


## <a name="contact-us"></a>문의처

IT Service Management Connector에 대해 질문이나 의견이 있는 경우 [omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계
[ITSM 제품/서비스를 IT Service Management Connector에 추가](log-analytics-itsmc-connections.md).
