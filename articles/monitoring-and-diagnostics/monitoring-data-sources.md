---
title: Azure에서 모니터링 데이터 사용 | Microsoft Docs
description: 현재 Azure에서 사용할 수 있는 모든 모니터링 데이터 원본을 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2017
ms.author: johnkem
ms.openlocfilehash: b10e95cd6b335468201b4cd123b3a29fb5d1bcdb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="consume-monitoring-data-from-azure"></a>Azure에서 모니터링 데이터 사용

Azure 플랫폼 전반에 걸쳐 Azure Monitor 파이프라인을 사용하여 모니터링 데이터를 한 곳에 모았지만 일부 모니터링 데이터는 파이프라인에서 아직 사용할 수 없습니다. 이 문서에서는 Azure 서비스에서 프로그래밍 방식으로 모니터링 데이터에 액세스할 수 있는 여러 가지 방법을 요약합니다.

## <a name="options-for-data-consumption"></a>데이터 사용 옵션

| 데이터 형식 | Category | 지원되는 서비스 | 액세스 방법 |
| --- | --- | --- | --- |
| Azure Monitor 플랫폼 수준 메트릭 | 메트릭 | [여기에 나오는 목록 참조](monitoring-supported-metrics.md) | <ul><li>**REST API:** [Azure Monitor 메트릭 API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**저장소 blob 또는 이벤트 허브:** [진단 설정](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| 게스트 OS 메트릭 Compute(예: 성능 카운터) | 메트릭 | [Windows](../virtual-machines-dotnet-diagnostics.md) 및 Linux Virtual Machines(v2), [Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**저장소 테이블 또는 blob:** [Windows 또는 Linux Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**이벤트 허브:** [Windows Azure 진단](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| 사용자 지정 또는 응용 프로그램 메트릭 | 메트릭 | Application Insights를 사용하여 계측된 모든 응용 프로그램 | <ul><li>**REST API:** [Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage 메트릭 | 메트릭 | Azure Storage | <ul><li>**저장소 테이블:** [저장소 분석](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 청구 데이터 | 메트릭 | 모든 Azure 서비스 | <ul><li>**REST API:** [Azure 리소스 사용량 및 RateCard API](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| 활동 로그 | 이벤트 | 모든 Azure 서비스 | <ul><li>**REST API:** [Azure Monitor 이벤트 API](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**저장소 blob 또는 이벤트 허브:** [로그 프로필](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Azure Monitor 진단 로그 | 이벤트 | [여기에 나오는 목록 참조](monitoring-diagnostic-logs-schema.md) | <ul><li>**저장소 blob 또는 이벤트 허브:** [진단 설정](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| 게스트 OS 로그 계산(예: IIS, ETW, syslog) | 이벤트 | [Windows](../virtual-machines-dotnet-diagnostics.md) 및 Linux Virtual Machines(v2), [Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**저장소 테이블 또는 blob:** [Windows 또는 Linux Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**이벤트 허브:** [Windows Azure 진단](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| App Service 로그 | 이벤트 | App Services | <ul><li>**File, Table 또는 Blob Storage:** [웹앱 진단](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| 저장소 로그 | 이벤트 | Azure Storage | <ul><li>**저장소 테이블:** [저장소 분석](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Security Center 경고 | 이벤트 | Azure Security Center | <ul><li>**REST API:** [보안 경고](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory 보고 | 이벤트 | Azure Active Directory | <ul><li>**REST API:** [Azure Active Directory Graph API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Security Center 리소스 상태 | 상태 | [지원되는 리소스 모든](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API:** [보안 상태](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| 리소스 상태 | 상태 | 지원되는 서비스 | <ul><li>**REST API:** [Resource Health REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure Monitor 메트릭 경고 | 공지 | [여기에 나오는 목록 참조](monitoring-supported-metrics.md) | <ul><li>**웹후크:** [Azure 메트릭 경고](insights-webhooks-alerts.md)</li></ul> |
| Azure Monitor 활동 로그 경고 | 공지 | 모든 Azure 서비스 | <ul><li>**웹후크:** Azure 활동 로그 경고</li></ul> |
| 자동 크기 조정 알림 | 공지 | [여기에 나오는 목록 참조](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**웹후크:** [크기 자동 조정 알림 웹후크 페이로드 스키마](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| 로그 검색 쿼리 경고 | 공지 | Log Analytics | <ul><li>**웹후크:** [Log Analytics 경고](../log-analytics/log-analytics-alerts-actions.md#webhook-actions)</li></ul> |
| Application Insights 메트릭 경고 | 공지 | Application Insights | <ul><li>**웹후크:** [Application Insights 경고](../application-insights/app-insights-alerts.md)</li></ul> |
| Application Insights 웹 테스트 | 공지 | Application Insights | <ul><li>**웹후크:** [Application Insights 경고](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 메트릭](monitoring-overview-metrics.md)에 대해 자세히 알아보기
- [Azure 활동 로그](monitoring-overview-activity-logs.md)에 대해 자세히 알아보기
- [Azure 진단 로그](monitoring-overview-of-diagnostic-logs.md)에 대해 자세히 알아보기
