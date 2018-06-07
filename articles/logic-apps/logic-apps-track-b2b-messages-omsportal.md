---
title: Azure Log Analytics으로 B2B 메시지 추적 - Azure Logic Apps | Microsoft Docs
description: Azure Log Analytics를 사용하여 통합 계정 및 논리 앱에 대한 B2B 통신 추적
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 48523e1f1bc8d5b810cc7c9d1a7308f1aaadf8bb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="track-b2b-communication-with-azure-log-analytics"></a>Azure Log Analytics를 사용하여 B2B 통신 추적

통합 계정을 통해 실행 중인 두 비즈니스 프로세스 또는 응용 프로그램 간의 B2B 통신을 설정한 후 해당 엔터티는 서로 메시지를 교환할 수 있습니다. 이러한 메시지가 올바르게 처리되는지 여부를 확인하기 위해 [Azure Log Analytics](../log-analytics/log-analytics-overview.md)를 사용하여 AS2, X12 및 EDIFACT 메시지를 추적할 수 있습니다. 예를 들어 메시지 추적에 대해 이러한 웹 기반 추적 기능을 사용할 수 있습니다.

* 메시지 수 및 상태
* 승인 상태
* 승인된 메시지 상호 연결
* 실패에 대한 자세한 오류 설명
* 검색 기능

## <a name="requirements"></a>요구 사항

* 진단 로깅과 함께 설정된 논리 앱. [논리 앱을 만드는 방법](quickstart-create-first-logic-app-workflow.md) 및 [해당 논리 앱에 대한 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)을 알아봅니다.

* 모니터링 및 로깅을 사용하여 설정된 통합 계정. [통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 및 [해당 계정에 대한 모니터링 및 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-b2b-message.md)을 알아봅니다.

* 아직 없는 경우 [Log Analytics에 진단 데이터를 게시](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.

> [!NOTE]
> 이전 요구 사항을 충족한 후에는 Log Analytics에 작업 영역이 있어야 합니다. Log Analytics에서 B2B 통신 추적에 대해 동일한 작업 영역을 사용해야 합니다. 
>  
> Log Analytics 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../log-analytics/log-analytics-quick-create-workspace.md)을 알아봅니다.

## <a name="add-the-logic-apps-b2b-solution-to-log-analytics"></a>Log Analytics에 Logic Apps B2B 솔루션 추가

Log Analytics가 논리 앱에 대한 B2B 메시지를 추적하도록 하려면 OMS 포털에 **Logic Apps B2B** 솔루션을 추가해야 합니다. [Log Analytics에 솔루션 추가](../log-analytics/log-analytics-quick-create-workspace.md)에 대해 자세히 알아봅니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**를 선택합니다. "로그 분석"에 대해 검색한 후 다음과 같이 **Log Analytics**를 선택합니다.

   ![Log Analytics 찾기](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. **Log Analytics** 아래에서 Log Analytics 작업 영역을 찾고 선택합니다. 

   ![Log Analytics 작업 영역 선택](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. **관리** 아래에서 **OMS 포털**을 선택합니다.

   ![OMS 포털 선택](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. 홈페이지를 연 후 **솔루션 갤러리**를 선택합니다.    

   ![솔루션 갤러리 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. **모든 솔루션** 아래에서 **Logic Apps B2B**를 찾고 선택합니다.     

   ![Logic Apps B2B 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. **Logic Apps B2B** 아래에서 **추가**를 선택합니다.

   ![[추가] 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   홈페이지에 **Logic Apps B2B 메시지**에 대한 타일이 이제 나타납니다. 
   이 타일은 B2B 메시지가 처리되는 경우 메시지 수를 업데이트합니다.

   ![홈페이지, Logic Apps B2B 메시지 타일](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-log-analytics"></a>Log Analytics에서 메시지 상태 및 세부 정보 추적

1. B2B 메시지가 처리된 후 해당 메시지에 대한 상태 및 세부 정보를 볼 수 있습니다. 홈페이지에서 **Logic Apps B2B 메시지** 타일을 선택합니다.

   ![업데이트된 메시지 수](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > 기본적으로 **Logic Apps B2B 메시지** 타일은 일별 데이터를 표시합니다. 데이터 범위를 다른 간격으로 변경하려면 페이지 맨 위에 있는 범위 컨트롤을 선택합니다.
   > 
   > ![데이터 범위 변경](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. 메시지 상태 대시보드가 나타난 후 일별 데이터를 표시하는 특정 메시지 유형에 대한 자세한 정보를 볼 수 있습니다. **AS2**, **X12** 또는 **EDIFACT**에 대한 타일을 선택합니다.

   ![메시지 상태 보기](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   선택한 타일에 대한 메시지 목록이 표시됩니다. 
   각 메시지 유형에 대한 속성에 대해 자세히 알아보려면 이러한 메시지 속성 설명을 참조하세요.

   * [AS2 메시지 속성](#as2-message-properties)
   * [X12 메시지 속성](#x12-message-properties)
   * [EDIFACT 메시지 속성](#EDIFACT-message-properties)

   예를 들어 AS2 메시지 목록은 다음과 같습니다.

   ![AS2 메시지 보기](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. 특정 메시지에 대한 입력 및 출력을 보거나 내보내려면 해당 메시지를 선택하고 **다운로드**를 선택합니다. 메시지가 나타나면 로컬 컴퓨터에 .zip 파일을 저장한 다음 해당 파일을 추출합니다. 

   추출된 폴더는 선택된 각 메시지에 대한 폴더를 포함합니다. 
   승인을 설정한 경우 메시지 폴더는 승인 세부 정보가 있는 파일도 포함합니다. 
   각 메시지 폴더에는 적어도 다음 파일이 있습니다. 
   
   * 입력 페이로드 및 출력 페이로드 세부 정보가 있는 사람이 읽을 수 파일
   * 입력 및 출력이 있는 인코딩된 파일

   각 메시지 유형의 경우 여기에서 폴더 및 파일 이름 형식을 찾을 수 있습니다.

   * [AS2 폴더 및 파일 이름 형식](#as2-folder-file-names)
   * [X12 폴더 및 파일 이름 형식](#x12-folder-file-names)
   * [EDIFACT 폴더 및 파일 이름 형식](#edifact-folder-file-names)

   ![메시지 파일 다운로드](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. 동일한 실행 ID를 가진 모든 작업을 보려면 **로그 검색** 페이지의 메시지 목록에서 메시지를 선택합니다.

   특정 결과에 대해 열 또는 검색별로 이러한 작업을 정렬할 수 있습니다.

   ![동일한 실행 ID가 있는 작업](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * 미리 작성된 쿼리를 사용하여 결과를 검색하려면 **즐겨찾기**를 선택합니다.

   * [필터를 추가하여 쿼리를 작성하는 방법](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)에 대해 알아봅니다. 
   또는 [Log Analytics에서 로그 검색을 사용하여 데이터를 찾는 방법](../log-analytics/log-analytics-log-searches.md)에 대해 자세히 알아봅니다.

   * 검색 상자에서 쿼리를 변경하려면 필터로 사용하려는 열 및 값으로 쿼리를 업데이트합니다.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2, X12 및 EDIFACT 메시지에 대한 속성 설명 및 이름 형식

각 메시지 유형의 경우 다운로드한 메시지 파일에 대한 속성 설명 및 이름 형식은 다음과 같습니다.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 메시지 속성 설명

다음은 각 AS2 메시지에 대한 속성 설명입니다.

| 자산 | 설명 |
| --- | --- |
| 보낸 사람 | **수신 설정**에 지정된 게스트 파트너 또는 AS2 규약의 **송신 설정**에 지정된 호스트 파트너 |
| 받는 사람 | **수신 설정**에 지정된 호스트 파트너 또는 AS2 규약의 **송신 설정**에 지정된 게스트 파트너 |
| 논리 앱 | AS2 작업이 설정된 논리 앱 |
| 상태 | AS2 메시지 상태 <br>성공 = 유효한 AS2 메시지를 받거나 보냈습니다. MDN이 설정되지 않습니다. <br>성공 = 유효한 AS2 메시지를 받거나 보냈습니다. MDN이 설정 및 수신됩니다. 또는 MDN이 전송됩니다. <br>실패 = 유효하지 않은 AS2 메시지를 받았습니다. MDN이 설정되지 않습니다. <br>보류 중 = 유효한 AS2 메시지를 받거나 보냈습니다. MDN이 설정되었고 MDN을 기다립니다. |
| Ack | MDN 메시지 상태 <br>수락됨 = 양의 MDN을 받거나 보냈습니다. <br>보류 중 = MDN을 받거나 보낼 때까지 대기 중입니다. <br>거부됨 = 음의 MDN을 받거나 보냈습니다. <br>필요하지 않음 = 규약에 MDN이 설정되지 않습니다. |
| 방향 | AS2 메시지 방향 |
| 상관관계 ID | 논리 앱의 모든 트리거와 작업을 상호 연결하는 ID |
| 메시지 ID | AS2 메시지 헤더의 AS2 메시지 ID |
| 타임 스탬프 | AS2 작업이 메시지를 처리한 시간 |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>다운로드한 메시지 파일에 대한 AS2 이름 형식

다운로드한 각 AS2 메시지 폴더 및 파일에 대한 이름 형식은 다음과 같습니다.

| 폴더 또는 파일 | 이름 형식 |
| :------------- | :---------- |
| 메시지 폴더 | [보낸 사람]\_[받는 사람]\_AS2\_[상관 관계-ID]\_[메시지-ID]\_[타임스탬프] |
| 입력, 출력 및 설정한 경우 승인 파일 | **입력 페이로드**: [보낸 사람]\_[받는 사람]\_AS2\_[상관 관계-ID]\_입력_페이로드.txt </p>**출력 페이로드**: [보낸 사람]\_[받는 사람]\_AS2\_[상관 관계-ID]\_출력\_페이로드.txt </p></p>**입력**: [보낸 사람]\_[받는 사람]\_AS2\_[상관 관계-ID]\_입력.txt </p></p>**출력**: [보낸 사람]\_[받는 사람]\_AS2\_[상관 관계-ID]\_출력.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 메시지 속성 설명

다음은 각 X12 메시지에 대한 속성 설명입니다.

| 자산 | 설명 |
| --- | --- |
| 보낸 사람 | **수신 설정**에 지정된 게스트 파트너 또는 X12 규약의 **송신 설정**에 지정된 호스트 파트너 |
| 받는 사람 | **수신 설정**에 지정된 호스트 파트너 또는 X12 규약의 **송신 설정**에 지정된 게스트 파트너 |
| 논리 앱 | X12 작업이 설정된 논리 앱 |
| 상태 | X12 메시지 상태 <br>성공 = 유효한 X12 메시지를 받거나 보냈습니다. 기능 Ack가 설정되지 않습니다. <br>성공 = 유효한 X12 메시지를 받거나 보냈습니다. 기능 Ack가 설정되고 수신되었거나 기능 Ack가 전송되었습니다. <br>실패 = 유효하지 않은 X12 메시지를 받거나 보냈습니다. <br>보류 중 = 유효한 X12 메시지를 받거나 보냈습니다. 기능 Ack가 설정되었고 기능 Ack를 기다립니다. |
| Ack | 기능 Ack(997) 상태 <br>수락됨 = 양의 기능 Ack를 받거나 보냈습니다. <br>거부됨 = 음의 기능 Ack를 받거나 보냈습니다. <br>보류 중 = 기능 Ack를 기다렸지만 받지 못했습니다. <br>보류 중 = 기능 Ack를 생성했지만 파트너에게 보낼 수 없습니다. <br>필요하지 않음 = 기능 Ack가 설정되지 않습니다. |
| 방향 | X12 메시지 방향 |
| 상관관계 ID | 논리 앱의 모든 트리거와 작업을 상호 연결하는 ID |
| 메시지 유형 | EDI X12 메시지 유형 |
| ICN | X12 메시지의 교환 컨트롤 번호 |
| TSCN | X12 메시지의 트랜잭션 집합 컨트롤 번호 |
| 타임 스탬프 | X12 작업이 메시지를 처리한 시간 |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>다운로드한 메시지 파일에 대한 X12 이름 형식

다운로드한 각 X12 메시지 폴더 및 파일에 대한 이름 형식은 다음과 같습니다.

| 폴더 또는 파일 | 이름 형식 |
| :------------- | :---------- |
| 메시지 폴더 | [보낸 사람]\_[받는 사람]\_X12\_[교환-컨트롤-번호]\_[글로벌-컨트롤-번호]\_[트랜잭션-집합-컨트롤-번호]\_[타임스탬프] |
| 입력, 출력 및 설정한 경우 승인 파일 | **입력 페이로드**: [보낸 사람]\_[받는 사람]\_X12\_[교환-컨트롤-번호]\_입력_페이로드.txt </p>**출력 페이로드**: [보낸 사람]\_[받는 사람]\_X12\_[교환-컨트롤-번호]\_출력\_페이로드.txt </p></p>**입력**: [보낸 사람]\_[받는 사람]\_X12\_[교환-컨트롤-번호]\_입력.txt </p></p>**출력**: [보낸 사람]\_[받는 사람]\_X12\_[교환-컨트롤-번호]\_출력.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT 메시지 속성 설명

다음은 각 EDIFACT 메시지에 대한 속성 설명입니다.

| 자산 | 설명 |
| --- | --- |
| 보낸 사람 | **수신 설정**에 지정된 게스트 파트너 또는 EDIFACT 규약의 **송신 설정**에 지정된 호스트 파트너 |
| 받는 사람 | **수신 설정**에 지정된 호스트 파트너 또는 EDIFACT 규약의 **송신 설정**에 지정된 게스트 파트너 |
| 논리 앱 | EDIFACT 작업이 설정된 논리 앱 |
| 상태 | EDIFACT 메시지 상태 <br>성공 = 유효한 EDIFACT 메시지를 받거나 보냈습니다. 기능 Ack가 설정되지 않습니다. <br>성공 = 유효한 EDIFACT 메시지를 받거나 보냈습니다. 기능 Ack가 설정되고 수신되었거나 기능 Ack가 전송되었습니다. <br>실패 = 유효하지 않은 EDIFACT 메시지를 받거나 보냈습니다. <br>보류 중 = 유효한 EDIFACT 메시지를 받거나 보냈습니다. 기능 Ack가 설정되었고 기능 Ack를 기다립니다. |
| Ack | 기능 Ack(997) 상태 <br>수락됨 = 양의 기능 Ack를 받거나 보냈습니다. <br>거부됨 = 음의 기능 Ack를 받거나 보냈습니다. <br>보류 중 = 기능 Ack를 기다렸지만 받지 못했습니다. <br>보류 중 = 기능 Ack를 생성했지만 파트너에게 보낼 수 없습니다. <br>필요하지 않음 = 기능 Ack가 설정되지 않습니다. |
| 방향 | EDIFACT 메시지 방향 |
| 상관관계 ID | 논리 앱의 모든 트리거와 작업을 상호 연결하는 ID |
| 메시지 유형 | EDIFACT 메시지 형식 |
| ICN | EDIFACT 메시지의 교환 컨트롤 번호 |
| TSCN | EDIFACT 메시지의 트랜잭션 집합 컨트롤 번호 |
| 타임 스탬프 | EDIFACT 작업이 메시지를 처리한 시간 |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>다운로드한 메시지 파일에 대한 EDIFACT 이름 형식

다운로드한 각 EDIFACT 메시지 폴더 및 파일에 대한 이름 형식은 다음과 같습니다.

| 폴더 또는 파일 | 이름 형식 |
| :------------- | :---------- |
| 메시지 폴더 | [보낸 사람]\_[받는 사람]\_EDIFACT\_[교환-컨트롤-번호]\_[글로벌-컨트롤-번호]\_[트랜잭션-집합-컨트롤-번호]\_[타임스탬프] |
| 입력, 출력 및 설정한 경우 승인 파일 | **입력 페이로드**: [보낸 사람]\_[받는 사람]\_EDIFACT\_[교환-컨트롤-번호]\_입력_페이로드.txt </p>**출력 페이로드**: [보낸 사람]\_[받는 사람]\_EDIFACT\_[교환-컨트롤-번호]\_출력\_페이로드.txt </p></p>**입력**: [보낸 사람]\_[받는 사람]\_EDIFACT\_[교환-컨트롤-번호]\_입력.txt </p></p>**출력**: [보낸 사람]\_[받는 사람]\_EDIFACT\_[교환-컨트롤-번호]\_출력.txt |
|          |             |

## <a name="next-steps"></a>다음 단계

* [Log Analytics에서 B2B 메시지 쿼리](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)