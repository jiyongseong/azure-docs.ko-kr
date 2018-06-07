---
title: Log Analytics에서 B2B 메시지 쿼리 - Azure Logic Apps | Microsoft Docs
description: Log Analytics에서 AS2, X12 및 EDIFACT 메시지를 추적하는 쿼리 만들기
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
ms.openlocfilehash: 345857801035fb7f149a57a4f0d58e7668f35b81
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="query-for-as2-x12-and-edifact-messages-in-log-analytics"></a>Log Analytics에서 AS2, X12 및 EDIFACT 메시지 쿼리

[Azure Log Analytics](../log-analytics/log-analytics-overview.md)를 사용하여 추적 중인 AS2, X12 또는 EDIFACT 메시지를 찾기 위해 특정 조건에 따라 작업을 필터링하는 쿼리를 만들 수 있습니다. 예를 들어 특정 교환 컨트롤 번호에 따라 메시지를 찾을 수 있습니다.

## <a name="requirements"></a>요구 사항

* 진단 로깅과 함께 설정된 논리 앱. [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md) 및 [해당 논리 앱에 대한 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)을 알아봅니다.

* 모니터링 및 로깅을 사용하여 설정된 통합 계정. [통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 및 [해당 계정에 대한 모니터링 및 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-b2b-message.md)을 알아봅니다.

* 아직 없는 경우 [Log Analytics에 진단 데이터를 게시](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)하고 [Log Analytics에서 메시지 추적을 설정](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.

> [!NOTE]
> 이전 요구 사항을 충족한 후에는 Log Analytics에 작업 영역이 있어야 합니다. Log Analytics에서 B2B 통신 추적에 대해 동일한 작업 영역을 사용해야 합니다. 
>  
> Log Analytics 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../log-analytics/log-analytics-quick-create-workspace.md)을 알아봅니다.

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Log Analytics에서 필터로 메시지 쿼리 만들기

이 예제는 해당 교환 컨트롤 번호에 따라 메시지를 찾는 방법을 보여 줍니다.

> [!TIP] 
> Log Analytics 작업 영역 이름을 알고 있으면 작업 영역 홈페이지(`https://{your-workspace-name}.portal.mms.microsoft.com`)로 이동하고 4단계에서 시작합니다. 그렇지 않은 경우 1단계에서 시작합니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**를 선택합니다. "로그 분석"에 대해 검색한 후 다음과 같이 **Log Analytics**를 선택합니다.

   ![Log Analytics 찾기](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. **Log Analytics** 아래에서 Log Analytics 작업 영역을 찾고 선택합니다.

   ![Log Analytics 작업 영역 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. **관리** 아래에서 **OMS 포털**을 선택합니다.

   ![OMS 포털 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. 홈페이지에서 **로그 검색**을 선택합니다.

   ![홈페이지에서 "로그 검색" 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   또는

   ![메뉴에서 "로그 검색" 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. 검색 상자에 찾으려는 필드를 입력하고 **Enter** 키를 누릅니다. 입력을 시작할 때 Log Analytics는 사용할 수 있는 가능한 일치 및 작업을 보여 줍니다. [Log Analytics에서 데이터를 찾는 방법](../log-analytics/log-analytics-log-searches.md)에 대해 자세히 알아봅니다.

   이 예제에서는 **Type=AzureDiagnostics**로 이벤트를 검색합니다.

   ![쿼리 문자열 입력 시작](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. 왼쪽 모음에서 보려는 시간 프레임을 선택합니다. 쿼리에 필터를 추가하려면 **+추가**를 선택합니다.

   ![쿼리에 필터 추가](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. **필터 추가** 아래에서 원하는 필터를 찾을 수 있도록 필터 이름을 입력합니다. 필터를 선택하고 **+추가**를 선택합니다.

   교환 컨트롤 번호를 찾기 위해 이 예제에서는 "교환"이라는 단어를 검색하고 필터로 **event_record_messageProperties_interchangeControlNumber_s**를 선택합니다.

   ![필터 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. 왼쪽 모음에서 사용하려는 필터 값을 선택하고 **적용**을 선택합니다.

   이 예제에서는 원하는 메시지에 대한 교환 컨트롤 번호를 선택합니다.

   ![필터 값 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. 이제 작성 중인 쿼리로 돌아갑니다. 선택한 필터 이벤트 및 값으로 쿼리가 업데이트되었습니다. 이제 이전 결과 또한 필터링되어 있습니다.

    ![필터링된 결과와 함께 쿼리로 돌아가기](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>나중에 사용할 쿼리 저장

1. **로그 검색** 페이지의 쿼리에서 **저장**을 선택합니다. 쿼리에 이름을 지정하고 범주를 선택하고 **저장**을 선택합니다.

   ![쿼리에 이름 및 범주 지정](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. 쿼리를 보려면 **즐겨찾기**를 선택합니다.

   !["즐겨찾기" 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. **저장된 검색** 아래에서 결과를 볼 수 있도록 쿼리를 선택합니다. 서로 다른 결과를 찾을 수 있도록 쿼리를 업데이트하려면 쿼리를 편집합니다.

   ![쿼리 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Log Analytics에서 저장된 쿼리 찾기 및 실행

1. Log Analytics 작업 영역 홈페이지(`https://{your-workspace-name}.portal.mms.microsoft.com`)를 열고 **로그 검색**을 선택합니다.

   ![Log Analytics 홈페이지에서 "로그 검색" 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   또는

   ![메뉴에서 "로그 검색" 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. **로그 검색** 홈페이지에서 **즐겨찾기**를 선택합니다.

   !["즐겨찾기" 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. **저장된 검색** 아래에서 결과를 볼 수 있도록 쿼리를 선택합니다. 서로 다른 결과를 찾을 수 있도록 쿼리를 업데이트하려면 쿼리를 편집합니다.

   ![쿼리 선택](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>다음 단계

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)