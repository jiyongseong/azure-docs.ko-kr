---
title: B2B 트랜잭션 모니터링 및 로깅 설정 - Azure Logic Apps | Microsoft Docs
description: AS2, X12 및 EDIFACT 메시지 모니터링, 통합 계정에 대한 진단 로깅 시작
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
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 6afab12b9e2d6e8686ecbc95be9743afbe70d98c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>통합 계정에서 B2B 통신에 대한 진단 로깅 모니터링 및 설정

통합 계정을 통해 실행 중인 두 비즈니스 프로세스 또는 응용 프로그램 간의 B2B 통신을 설정한 후 해당 엔터티는 서로 메시지를 교환할 수 있습니다. 이 통신이 예상대로 작동하는지 확인하기 위해 [Azure Log Analytics](../log-analytics/log-analytics-overview.md) 서비스를 통해 통합 계정에 대한 진단 로깅과 함께 AS2, X12 및 EDIFACT 메시지에 대한 모니터링을 설정할 수 있습니다. 이 서비스는 해당 가용성 및 성능을 유지할 수 있도록 클라우드 및 온-프레미스 환경을 모니터링하고 런타임 세부 정보 및 보다 풍부한 디버깅에 대한 이벤트를 수집합니다. 또한 Azure Storage 및 Azure Event Hub와 같은 [다른 서비스와 함께 진단 데이터를 사용](#extend-diagnostic-data)할 수도 있습니다.

## <a name="requirements"></a>요구 사항

* 진단 로깅과 함께 설정된 논리 앱. [해당 논리 앱에 대한 로깅을 설정하는 방법](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)을 알아봅니다.

  > [!NOTE]
  > 이 요구 사항을 충족한 후 Log Analytics에 작업 영역이 있어야 합니다. 통합 계정에 대한 로깅을 설정할 때와 동일한 Log Analytics 작업 영역을 사용해야 합니다. Log Analytics 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../log-analytics/log-analytics-quick-create-workspace.md)을 알아봅니다.

* 논리 앱에 연결된 통합 계정. [논리 앱에 대한 링크와 함께 통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 알아봅니다.

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>통합 계정에 대한 진단 로깅 켜기

통합 계정에서 직접 또는 [Azure Monitor 서비스를 통해](#azure-monitor-service) 로깅을 설정할 수 있습니다. Azure Monitor는 인프라 수준의 데이터와 함께 기본 모니터링을 제공합니다. [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)에 대해 자세히 알아봅니다.

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>통합 계정에서 직접 진단 로깅 켜기

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **모니터링** 아래에서 다음과 같이 **진단 로그**를 선택합니다.

   ![통합 계정을 찾고 선택하고 "진단 로그"를 선택합니다.](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. 통합 계정을 선택하면 다음 값이 자동으로 선택됩니다. 이러한 값이 올바르면 **진단 켜기**를 선택합니다. 그렇지 않은 경우 원하는 값을 선택합니다.

   1. **구독** 아래에서 통합 계정과 함께 사용하는 Azure 구독을 선택합니다.
   2. **리소스 그룹** 아래에서 통합 계정과 함께 사용하는 리소스 그룹을 선택합니다.
   3. **리소스 종류** 아래에서 **통합 계정**을 선택합니다. 
   4. **리소스** 아래에서 통합 계정을 선택합니다. 
   5. **진단 켜기**를 선택합니다.

   ![통합 계정에 대한 진단 설정](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. **진단 설정**, **상태** 아래에서 **켜기**를 선택합니다.

   ![Azure 진단 켜기](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. 이제 표시된 것처럼 로깅에 사용할 Log Analytics 작업 영역 및 데이터를 선택합니다.

   1. **Log Analytics에 보내기**를 선택합니다. 
   2. **Log Analytics** 아래에서 **구성**을 선택합니다. 
   3. **OMS 작업 영역** 아래에서 로깅에 사용할 Log Analytics 작업 영역을 선택합니다.
   4. **로그** 아래에서 **IntegrationAccountTrackingEvents** 범주를 선택합니다.
   5. **저장**을 선택합니다.

   ![로그에 진단 데이터를 보낼 수 있도록 Log Analytics 설정](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. 이제 [Log Analytics에서 B2B 메시지에 대한 추적을 설정](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Azure Monitor를 통해 진단 로깅 켜기

1. [Azure Portal](https://portal.azure.com)의 주요 Azure 메뉴에서 **모니터링**, **진단 로그**를 선택합니다. 그런 다음 다음과 같이 통합 계정을 선택합니다.

   !["모니터링", "진단 로그"를 선택하고 통합 계정을 선택합니다.](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. 통합 계정을 선택하면 다음 값이 자동으로 선택됩니다. 이러한 값이 올바르면 **진단 켜기**를 선택합니다. 그렇지 않은 경우 원하는 값을 선택합니다.

   1. **구독** 아래에서 통합 계정과 함께 사용하는 Azure 구독을 선택합니다.
   2. **리소스 그룹** 아래에서 통합 계정과 함께 사용하는 리소스 그룹을 선택합니다.
   3. **리소스 종류** 아래에서 **통합 계정**을 선택합니다.
   4. **리소스** 아래에서 통합 계정을 선택합니다.
   5. **진단 켜기**를 선택합니다.

   ![통합 계정에 대한 진단 설정](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. **진단 설정** 아래에서 **켜기**를 선택합니다.

   ![Azure 진단 켜기](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. 이제 표시된 것처럼 로깅에 대한 Log Analytics 작업 영역 및 이벤트 범주를 선택합니다.

   1. **Log Analytics에 보내기**를 선택합니다. 
   2. **Log Analytics** 아래에서 **구성**을 선택합니다. 
   3. **OMS 작업 영역** 아래에서 로깅에 사용할 Log Analytics 작업 영역을 선택합니다.
   4. **로그** 아래에서 **IntegrationAccountTrackingEvents** 범주를 선택합니다.
   5. 완료하면 **저장**을 선택합니다.

   ![로그에 진단 데이터를 보낼 수 있도록 Log Analytics 설정](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. 이제 [Log Analytics에서 B2B 메시지에 대한 추적을 설정](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>다른 서비스와 함께 진단 데이터를 사용하는 방법 및 위치 확장

Azure Log Analytics와 마찬가지로 다른 Azure 서비스와 함께 논리 앱의 진단 데이터를 사용하는 방법을 다음과 같이 확장할 수 있습니다. 

* [Azure Storage에 Azure 진단 로그 보관](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Azure Event Hubs로 Azure 진단 로그 스트림](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

그런 다음 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 및 [Power BI](../log-analytics/log-analytics-powerbi.md)와 같은 다른 서비스의 원격 분석 및 분석을 사용하여 실시간으로 모니터링할 수 있습니다. 예: 

* [Event Hub에서 Stream Analytics로 데이터 스트림](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream Analytics를 사용하여 스트리밍 데이터 분석 및 Power BI에서 실시간 분석 대시보드 만들기](../stream-analytics/stream-analytics-power-bi-dashboard.md)

설정하려는 옵션에 따라 먼저 [Azure 저장소 계정을 만들](../storage/common/storage-create-storage-account.md)거나 [Azure 이벤트 허브를 만들](../event-hubs/event-hubs-create.md)어야 합니다. 그런 다음 진단 데이터를 전송하려는 위치에 대한 옵션을 선택합니다.

![Azure 저장소 계정 또는 이벤트 허브로 데이터 보내기](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> 보존 기간은 저장소 계정을 사용하도록 선택한 경우에만 적용됩니다.

## <a name="supported-tracking-schemas"></a>지원되는 추적 스키마

Azure는 이러한 추적 스키마 형식을 지원하며 사용자 지정 유형을 제외한 고정된 스키마를 포함합니다.

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [Log Analytics에서 B2B 메시지 추적](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "OMS에서 B2B 메시지 추적")
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

