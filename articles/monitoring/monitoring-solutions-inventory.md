---
title: Azure의 관리 솔루션에 대한 데이터 수집 상세 정보 | Microsoft Docs
description: Azure의 관리 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 논리, 시각화 및 데이터 취득 규칙의 컬렉션입니다.  이 문서에서는 Microsoft에서 제공하는 관리 솔루션 목록과 메서드 및 데이터 수집 빈도에 대한 세부 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bwren
ms.openlocfilehash: ab07a11883b3462c4b9d0f9adab6c55e4fe49d78
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Azure의 관리 솔루션에 대한 데이터 수집 상세 정보
이 문서에는 Microsoft에서 제공하는 [관리 솔루션](monitoring-solutions.md) 목록과 자세한 설명서 링크가 포함되어 있습니다.  메서드 및 Log Analytics로 데이터를 수집하는 빈도에 대한 정보도 제공합니다.  이 문서의 정보를 사용하여 제공되는 여러 솔루션을 식별하고 여러 관리 솔루션의 데이터 흐름 및 연결 요구 사항을 이해할 수 있습니다. 

## <a name="list-of-management-solutions"></a>관리 솔루션 목록

다음 표에는 Microsoft에서 제공하는 Azure의 [관리 솔루션](monitoring-solutions.md) 목록이 나열되어 있습니다. 열의 항목은 솔루션이 해당 메서드를 사용하여 Log Analytics로 데이터를 수집한다는 것을 의미합니다.  솔루션에서 선택된 열이 없으면 해당 솔루션은 다른 Azure 서비스에서 Log Analytics에 바로 데이터를 씁니다. 자세한 내용을 보려면 각 솔루션의 링크를 따라 자세한 설명서로 이동하세요.

열에 대한 설명은 다음과 같습니다.

- **Microsoft 모니터링 에이전트** - Windows 및 Linux에서 SCOM의 관리 팩과 Azure의 관리 솔루션을 실행하기 위해 사용되는 에이전트입니다. 이 구성에서 에이전트는 Operations Manager 관리 그룹에 연결되지 않고 Log Analytics에 직접 연결됩니다. 
- **Operations Manager** - Microsoft 모니터링 에이전트와 동일한 에이전트입니다. 이 구성에서는 Log Analytics에 연결된 [Operations Manager 관리 그룹에 연결](../log-analytics/log-analytics-om-agents.md)됩니다. 
-  **Azure Storage** -솔루션이 Azure 저장소 계정에서 데이터를 수집합니다. 
- **Operations Manager 필요 여부** - 연결된 Operations Manager 관리 그룹은 관리 솔루션에서 데이터를 수집하는 데 필요합니다. 
- **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** - 에이전트가 [SCOM 관리 그룹에 연결된 경우](../log-analytics/log-analytics-om-agents.md) 데이터가 관리 서버에서 Log Analytics로 전송됩니다. 이 경우 에이전트가 Log Analytics에 직접 연결할 필요가 없습니다. 이 상자를 선택하지 않으면 에이전트가 SCOM 관리 그룹에 연결되어 있어도 데이터가 에이전트에서 Log Analytics로 직접 전송됩니다. 에이전트가 [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 통해 Log Analytics와 통신할 수 있어야 합니다.
- **수집 빈도** - 관리 솔루션에서 데이터를 수집하는 빈도를 지정합니다. 



| **관리 솔루션** | **플랫폼** | **Microsoft 모니터링 에이전트** | **Operations Manager 에이전트** | **Azure 저장소** | **Operations Manager 필요 여부** | **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** | **수집 빈도** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [활동 로그 분석](../log-analytics/log-analytics-activity.md) | Azure | | | | | | 알림 시 |
| [AD 평가](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 일 |
| [AD 복제 상태](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5일 |
| [에이전트 상태](../operations-management-suite/oms-solution-agenthealth.md) | Windows 및 Linux | &#8226; | &#8226; | | | &#8226; | 1분 |
| [경고 관리](../log-analytics/log-analytics-solution-alert-management.md)(Nagios) |Linux |&#8226; | | | | |도착 시 |
| [경고 관리](../log-analytics/log-analytics-solution-alert-management.md)(Zabbix) |Linux |&#8226; | | | | |1분 |
| [경고 관리](../log-analytics/log-analytics-solution-alert-management.md)(Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3분 |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | 해당 없음 |
| [Application Insights 커넥터(미리 보기)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | 알림 시 |
| [Automation Hybrid Worker](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | 해당 없음 |
| [Azure Application Gateway 분석](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | 알림 시 |
| **관리 솔루션** | **플랫폼** | **Microsoft 모니터링 에이전트** | **Operations Manager 에이전트** | **Azure 저장소** | **Operations Manager 필요 여부** | **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** | **수집 빈도** |
| [Azure 네트워크 보안 그룹 분석](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | 알림 시 |
| [Azure SQL Analytics(미리 보기)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1분 |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 알림 시 |
| [용량 및 성능(미리 보기)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |도착 시 |
| [변경 내용 추적](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |매시간 |
| [변경 내용 추적](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |매시간 |
| [컨테이너](../log-analytics/log-analytics-containers.md) | Windows 및 Linux | &#8226; | &#8226; |  |  |  | 3분 |
| [Key Vault 분석](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |알림 시 |
| [맬웨어 평가](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |매시간 |
| [네트워크 성능 모니터](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP는 5초마다 핸드셰이크를 수행하며 3분마다 데이터가 전송됩니다. |
| [Office 365 분석(미리 보기)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |알림 시 |
| **관리 솔루션** | **플랫폼** | **Microsoft 모니터링 에이전트** | **Operations Manager 에이전트** | **Azure 저장소** | **Operations Manager 필요 여부** | **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** | **수집 빈도** |
| [보안 및 감사](../operations-management-suite/oms-security-getting-started.md)(Syslog) | Linux | &#8226; | | |  |  | 도착 시 |
| [보안 및 감사](../operations-management-suite/oms-security-getting-started.md)(보안 이벤트 로그) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 도착 시 |
| [보안 및 감사](../operations-management-suite/oms-security-getting-started.md)(방화벽 로그) |Windows |&#8226; |&#8226; |  |  |  |도착 시 |
| [Service Fabric 분석(미리 보기)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5분 |
| [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) | Windows 및 Linux | &#8226; | &#8226; |  |  |  | 15초 |
| [SQL 평가](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 일 |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |도착 시 |
| [System Center Operations Manager 평가(미리 보기)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7일 |
| [업데이트 관리](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |하루에 최소 2회 및 업데이트를 설치하고 15분 후 |
| [업그레이드 준비](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2일 |
| [VMware 모니터링(미리 보기)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3분 |
| [Wire Data 2.0(미리 보기)](../log-analytics/log-analytics-wire-data.md) |Windows(2012 R2/8.1 이상) |&#8226; |&#8226; | | | | 1분 |




## <a name="next-steps"></a>다음 단계
* 관리 솔루션에서 수집한 데이터를 분석하는 [쿼리 만들기](../log-analytics/log-analytics-log-searches.md) 방법을 알아봅니다.
