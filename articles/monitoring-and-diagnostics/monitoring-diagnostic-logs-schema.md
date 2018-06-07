---
title: Azure 진단 로그 지원 서비스 및 스키마 | Microsoft Docs
description: Azure 진단 로그에 대해 지원되는 서비스 및 이벤트 스키마에 대해 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2018
ms.author: johnkem
ms.openlocfilehash: 91c3f1507bb4fb64d5395917e8e431951f77e72b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 진단 로그에 대해 지원되는 서비스, 스키마 및 범주

[Azure 리소스 진단 로그](monitoring-overview-of-diagnostic-logs.md)는 해당 리소스의 작업을 설명하는 Azure 리소스에서 내보낸 로그입니다. 이러한 로그는 리소스 종류별로 다릅니다. 이 문서에서는 각 서비스에서 내보내는 이벤트에 대해 지원되는 서비스 및 이벤트 스키마 집합에 대해 간략히 설명합니다. 또한 이 문서에는 리소스 종류당 사용 가능한 로그 범주의 전체 목록이 포함되어 있습니다.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>리소스 진단 로그에 대해 지원되는 서비스 및 스키마
리소스 진단 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다.   

| 서비스 | 스키마 및 문서 |
| --- | --- |
| Analysis Services | 스키마를 사용할 수 없음 |
| API Management | [API 관리 진단 로그](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway |[Application Gateway에 대한 진단 로깅](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation에 대한 Log Analytics](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 진단 로깅](../batch/batch-diagnostics.md) |
| Customer Insights | 스키마를 사용할 수 없음 |
| Content Delivery Network | 스키마를 사용할 수 없음 |
| CosmosDB | [Azure Cosmos DB 로깅](../cosmos-db/logging.md) |
| Data Lake Analytics |[Azure Data Lake Analytics에 대한 진단 로그에 액세스](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store에 대한 진단 로그에 액세스](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs 진단 로그](../event-hubs/event-hubs-diagnostic-logs.md) |
| IoT 허브 | [IoT Hub 작업](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault 로깅](../key-vault/key-vault-logging.md) |
| Load Balancer |[Azure Load Balancer에 대한 Log analytics](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS 보호 | 스키마를 사용할 수 없음 |
| Recovery Services | [Azure Backup용 데이터 모델](../backup/backup-azure-reports-data-model.md)|
| 검색 |[검색 트래픽 분석 설정 및 사용](../search/search-traffic-analytics.md) |
| 서버 관리 | 스키마를 사용할 수 없음 |
| Service Bus |[Azure Service Bus 진단 로그](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 진단 로깅](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[작업 진단 로그](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Virtual Network | 스키마를 사용할 수 없음 |

## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주
|리소스 종류|Category|범주 표시 이름|
|---|---|---|
|Microsoft.AnalysisServices/servers|엔진|엔진|
|Microsoft.AnalysisServices/servers|서비스|서비스|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 게이트웨이 관련 로그|
|Microsoft.Automation/automationAccounts|JobLogs|작업 로그|
|Microsoft.Automation/automationAccounts|JobStreams|작업 스트림|
|Microsoft.Automation/automationAccounts|DscNodeStatus|디스크 노드 상태|
|Microsoft.Batch/batchAccounts|ServiceLog|서비스 로그|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|끝점의 메트릭(예: 대역폭, 송신 등)을 가져옵니다.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|파이프라인 작업 실행 로그|
|Microsoft.DataFactory/factories|PipelineRuns|파이프라인 실행 로그|
|Microsoft.DataFactory/factories|TriggerRuns|트리거 실행 로그|
|Microsoft.DataLakeAnalytics/accounts|감사|감사 로그|
|Microsoft.DataLakeAnalytics/accounts|요청|요청 로그|
|Microsoft.DataLakeStore/accounts|감사|감사 로그|
|Microsoft.DataLakeStore/accounts|요청|요청 로그|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL 서버 로그|
|Microsoft.Devices/IotHubs|연결|연결|
|Microsoft.Devices/IotHubs|DeviceTelemetry|장치 원격 분석|
|Microsoft.Devices/IotHubs|C2DCommands|C2D 명령|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|장치 ID 작업|
|Microsoft.Devices/IotHubs|FileUploadOperations|파일 업로드 작업|
|Microsoft.Devices/IotHubs|경로|경로|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D 쌍 작업|
|Microsoft.Devices/IotHubs|TwinQueries|쌍 쿼리|
|Microsoft.Devices/IotHubs|JobsOperations|작업 연산|
|Microsoft.Devices/IotHubs|DirectMethods|직접 메서드|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E 진단(미리 보기)|
|Microsoft.Devices/provisioningServices|DeviceOperations|장치 작업|
|Microsoft.Devices/provisioningServices|ServiceOperations|서비스 작동|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|보관 로그|
|Microsoft.EventHub/namespaces|OperationalLogs|작업 로그|
|Microsoft.EventHub/namespaces|AutoScaleLogs|자동 크기 조정 로그|
|Microsoft.KeyVault/vaults|AuditEvent|감사 로그|
|Microsoft.Logic/workflows|WorkflowRuntime|워크플로 런타임 진단 이벤트|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|네트워크 보안 그룹 이벤트|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|네트워크 보안 그룹 규칙 카운터|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 보호 알림|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 보호 경고|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway 액세스 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway 성능 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway 방화벽 로그|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|게이트웨이 진단 로그|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|터널 진단 로그|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|경로 진단 로그|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 진단 로그|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2P 진단 로그|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager 프로브 상태 결과 이벤트|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|GWM 카운터 테이블|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup 보고 데이터|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 작업|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 이벤트|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 복제된 항목|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery 복제 통계|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 복구 지점|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 복제 데이터 업로드 속도|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 보호된 디스크 데이터 변동|
|Microsoft.Search/searchServices|OperationLogs|작업 로그|
|Microsoft.ServiceBus/namespaces|OperationalLogs|작업 로그|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|
|Microsoft.Sql/servers/databases|오류|오류|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|데이터베이스 대기 통계|
|Microsoft.Sql/servers/databases|시간 제한|시간 제한|
|Microsoft.Sql/servers/databases|블록|블록|
|Microsoft.Sql/servers/databases|SQLInsights|SQL 정보|
|Microsoft.Sql/servers/databases|감사|감사 로그|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|
|Microsoft.StreamAnalytics/streamingjobs|실행|실행|
|Microsoft.StreamAnalytics/streamingjobs|작성|작성|

## <a name="next-steps"></a>다음 단계

* [진단 로그에 대해 자세히 알아보기](monitoring-overview-of-diagnostic-logs.md)
* [**Event Hubs**로 리소스 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API를 사용하여 리소스 진단 설정 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage.md)
