---
title: "SIEM-파이프라인 구성에 Azure 보안 데이터 내보내기 [미리 보기] | Microsoft Docs"
description: "이 아티클은 Azure 보안 센터 로그를 SIEM으로 보낼 때 생성되는 결과를 기록합니다."
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: 7a0a72a25010952f13eb190f0e0a1a65cc6d42d3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>SIEM-파이프라인 구성에 Azure 보안 데이터 내보내기 [미리 보기]

이 문서는 Azure Security Center 보안 데이터를 SIEM에 내보내기 하는 절차를 자세히 설명합니다.

Azure Security Center에서 생성하는 처리된 이벤트는 Azure Monitor를 통해 사용할 수 있는 로그 형식 중 하나인 Azure [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)에 게시됩니다. Azure Monitor는 모니터링 데이터를 SIEM 도구에 라우팅하는 것에 대한 통합된 파이프라인을 제공합니다. 이렇게 하려면 해당 데이터를 Event Hubs에 스트리밍합니다. 그런 후 스트리밍한 데이터는 파트너 도구에 끌어올 수 있습니다.

이 파이프는 Azure 환경에서 모니터링 데이터에 대한 액세스 권한을 갖기 위해 [단일 파이프라인 Azure 모니터링](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)을 사용합니다. 그러면 데이터를 사용할 SIEM 및 모니터링 도구를 쉽게 설정할 수 있습니다.

다음 섹션에서는 데이터를 이벤트 허브로 스트리밍하도록 구성하는 방법에 대해 설명합니다. 다음 단계에서는 Azure Security Center가 Azure 구독에 이미 구성되어 있다고 가정합니다.

대략적인 개요

![대략적인 개요](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SIEM에 공개된 Azure 보안 데이터는 무엇입니까?

우리는 이 미리 보기 버전에 [보안 경고](../security-center/security-center-managing-and-responding-alerts.md)를 공개합니다. 이후 릴리스에는 보안 권장 사항이 포함된 데이터 집합을 보강합니다.

## <a name="how-to-setup-the-pipeline"></a>파이프라인을 설정하는 방법은? 

### <a name="create-an-event-hub"></a>이벤트 허브 만들기 

시작하기 전에 먼저 [Event Hubs 네임스페이스](../event-hubs/event-hubs-create.md)를 만들어야 합니다. 이 네임스페이스 및 이벤트 허브는 모든 모니터링 데이터에 대한 대상입니다.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Azure 활동 로그를 Event Hubs로 스트림

다음 아티클 [Event Hubs에 활동 로그 스트림하기](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)를 참조하세요.

### <a name="install-a-partner-siem-connector"></a>파트너 SIEM 커넥터 설치 

Azure Monitor를 사용하여 모니터링 데이터를 이벤트 허브로 라우팅하면 파트너 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다.

[지원되는 SIEM](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub) 목록을 보려면 다음 링크를 참조하세요.

## <a name="example-for-querying-data"></a>데이터 쿼리 예제 

경고 데이터를 끌어오기 위해 사용할 수 있는 몇 가지 Splunk 쿼리는 다음과 같습니다.

| **쿼리 설명**                                | **쿼리**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| All Alerts                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| 이름별로 작업 횟수를 요약합니다.             | operationName별 index=main sourcetype="amal:security" \| table operationName \| 통계치                                |
| 경고 정보 얻기: 시간, 이름, 상태, ID 및 구독 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>다음 단계

- [지원되는 SIEM](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [활동 로그를 Event Hubs로 스트림](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [보안 경고.](../security-center/security-center-managing-and-responding-alerts.md)