---
title: Azure 로깅 및 감사 | Microsoft Docs
description: 로깅 데이터를 사용하여 응용 프로그램에 대해 깊이 이해할 수 있는 방법에 대해 알아봅니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 2b8b5095fceaa369ae8b7a426ca04685c2d86109
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057936"
---
# <a name="azure-logging-and-auditing"></a>Azure 로깅 및 감사
## <a name="introduction"></a>소개
### <a name="overview"></a>개요
현재 및 미래의 Azure 고객이 Azure 플랫폼에서 사용할 수 있는 다양한 보안 관련 기능을 이해하고 사용할 수 있도록 Microsoft는 일련의 백서, 보안 개요, 모범 사례 및 검사 목록을 개발했습니다. 주제는 폭과 깊이에 따라 다양하게 다루어지며 정기적으로 업데이트됩니다. 이 문서는 다음의 요약 섹션에서 설명하는 시리즈의 일부입니다.
### <a name="azure-platform"></a>Azure 플랫폼
Azure는 매우 다양한 운영 체제, 프로그래밍 언어, 프레임워크, 도구, 데이터베이스 및 장치를 지원하는 개방적이고 유연한 클라우드 서비스 플랫폼입니다.

예를 들어 다음을 수행할 수 있습니다.
-   Docker 통합으로 Linux 컨테이너를 실행합니다.

-   JavaScript, Python, .NET, PHP, Java 및 Node.js를 사용하여 앱을 빌드합니다.

-   iOS, Android 및 Windows 장치용 백 엔드를 빌드합니다.

Azure 공용 클라우드 서비스는 수백만의 개발자 및 IT 전문가가 이미 믿고 사용하고 있는 동일한 수준의 기술을 지원합니다.

IT 자산을 만들거나 클라우드 공급자로 마이그레이션하는 경우 조직에서 제공하는 서비스와 제어를 통해 응용 프로그램과 데이터를 보호할 수 있는 해당 조직의 능력에 의존하여 클라우드 기반 자산의 보안을 관리합니다.

Azure의 인프라는 수백만 명의 고객을 동시에 호스팅하기 위한 시설에서 응용 프로그램에 이르는 인프라를 설계하며 비즈니스의 보안 요구 사항을 충족하는 신뢰할 수 있는 기반을 제공합니다. 또한 Azure는 다양하게 구성 가능한 보안 옵션 및 제어 능력을 제공하므로 배포에 대한 고유한 요구 사항에 맞게 보안을 사용자 지정할 수 있습니다. 이 문서는 이러한 요구 사항을 충족하는 데 도움이 됩니다.

### <a name="abstract"></a>요약
보안 관련 이벤트의 감사와 로깅 및 관련 경고는 효과적인 데이터 보호 전략의 중요한 구성 요소입니다. 보안 로그 및 보고서는 의심스러운 활동에 대한 전자 기록을 제공하며, 내부 공격뿐만 아니라 네트워크의 외부 침투 시도 또는 성공을 나타낼 수 있는 패턴을 검색할 수 있도록 도움을 줍니다. 감사를 사용하여 사용자 활동을 모니터링하고, 규제 준수를 문서화하며, 법정 분석 등을 수행할 수 있습니다. 경고는 보안 이벤트가 발생할 때 즉각적인 알림을 제공합니다.

Microsoft Azure 서비스 및 제품은 구성 가능한 보안 감사 및 로깅 옵션을 제공하여 보안 정책과 메커니즘의 차이를 식별하고 이러한 차이를 해결하여 위반을 방지할 수 있게 합니다. Microsoft 서비스는 옵션, 즉 연속적인 가시성을 제공하는 중앙 집중식 모니터링, 로깅 및 분석 시스템, 시의 적절한 경고 및 장치와 서비스에서 생성하는 많은 양의 정보를 관리하는 데 도움이 되는 보고서 중 일부(및 경우에 따라 모두)를 제공합니다.

Microsoft Azure 로그 데이터는 분석을 위해 SIEM(Security Incident and Event Management) 시스템으로 내보내고 타사 감사 솔루션과 통합할 수 있습니다.

이 백서에서는 Azure에서 호스팅되는 서비스의 보안 로그 생성, 수집 및 분석을 소개하며, Azure 배포에 대한 보안을 깊이 이해할 수 있습니다. 이 백서의 범위는 Azure에서 작성되고 배포된 응용 프로그램과 서비스로 제한됩니다.

> [!Note]
> 여기에 포함된 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스 사용량이 증가하고, 라이선스 또는 구독 비용이 증가할 수 있습니다.

## <a name="types-of-logs-in-azure"></a>Azure의 로그 유형
클라우드 응용 프로그램은 이동하는 부분이 많아 복잡합니다. 로그는 응용 프로그램을 유지하고 정상 상태에서 실행하도록 데이터를 제공합니다. 또한 잠재적 문제를 방지하거나 지난 문제를 해결할 수 있습니다. 또한 로깅 데이터를 사용하여 응용 프로그램에 대해 깊이 이해할 수 있습니다. 이러한 정보를 통해 응용 프로그램 성능이나 유지 관리를 개선하거나 그렇지 않으면 수동 개입이 필요한 작업을 자동화하는 데 도움이 될 수 있습니다.

Azure에서는 모든 Azure 서비스에 대해 광범위한 로깅을 생성합니다. 이러한 로그는 다음과 같은 유형으로 분류됩니다.
-   **제어/관리 로그** - Azure Resource Manager의 CREATE, UPDATE 및 DELETE 작업에 대한 가시성을 제공합니다. [Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)가 이러한 로그 유형의 예입니다.

-   **데이터 평면 로그** - Azure 리소스 사용의 일부로 발생하는 이벤트에 대한 가시성을 제공합니다. 가상 머신의 Windows 이벤트 시스템, 보안 및 응용 프로그램 로그 및 Azure Monitor를 통해 구성된 [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)가 이러한 로그 유형의 예입니다.


-   **처리된 이벤트** - 사용자를 위해 처리된 분석 이벤트/경고에 대한 정보를 제공합니다. [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에서 구독을 처리 및 분석하고 간결한 보안 경고를 제공하는 [Azure Security Center 경고](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)가 이러한 로그 유형의 예입니다.

다음 표에서는 Azure에서 사용할 수 있는 가장 중요한 유형의 로그를 나열합니다.

| 로그 범주 | 로그 형식 | 사용 | 통합 |
| ------------ | -------- | ------ | ----------- |
|[활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Azure Resource Manager 리소스에 대한 제어 평면 이벤트|   구독의 리소스에서 수행된 작업에 대한 정보를 제공합니다.| Rest API 및 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure 진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|구독에서 Azure Resource Manager 리소스 작업에 대한 빈도 데이터| 리소스 자체에서 수행한 작업에 대한 정보를 제공합니다.| Azure Monitor, [스트림](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[AAD 보고](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|로그 및 보고서|사용자 로그인 활동 및 사용자와 그룹 관리에 대한 시스템 활동 정보|[그래프 API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[가상 머신 및 Cloud Services](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-quick-collect-azurevm)|Windows 이벤트 로그 및 Linux Syslog| 가상 머신에서 시스템 데이터와 로깅 데이터를 캡처하고 사용자가 선택한 저장소 계정으로 해당 데이터를 전송합니다.|   Azure 모니터에서 [MAD](https://docs.microsoft.com/azure/azure-diagnostics)(Microsoft Azure 진단 저장소)와 Linux를 사용하는 Windows|
|[저장소 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|저장소 로깅을 수행하고, Storage 계정에 대한 메트릭 데이터를 제공합니다.|추적 요청에 대한 정보를 제공하고, 사용 추세를 분석하며, 저장소 계정으로 문제를 진단합니다.|    REST API 또는 [클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[NSG(네트워크 보안 그룹) 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON 형식이며, 규칙에 따라 아웃바운드 및 인바운드 흐름을 보여 줍니다.|네트워크 보안 그룹을 통한 수신 및 송신 IP 트래픽에 대한 정보를 보여 줍니다.|[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insight](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|로그, 예외 및 사용자 지정 진단|    여러 플랫폼의 웹 개발자를 위한 APM(Application Performance Management) 서비스| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|데이터 처리/보안 경고| Azure Security Center 경고, Log Analytics 경고|   보안 정보 및 경고입니다.|   REST API, JSON|

### <a name="activity-log"></a>활동 로그
[Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 정보를 제공합니다. 활동 로그는 구독에 대한 [제어 평면 이벤트](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/)를 보고하기 때문에 이전에는 "감사 로그" 또는 "작업 로그"로 알려져 있었습니다. 활동 로그를 사용하면 구독의 리소스에서 수행한 모든 쓰기 작업(PUT, POST, DELETE)에 대한 '무엇을, 누가, 언제'를 판단할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 활동 로그에는 읽기(GET) 작업은 포함되지 않습니다.

여기서 PUT, POST, DELETE는 리소스에 포함된 쓰기 작업 활동 로그를 모두 참조합니다. 예를 들어 활동 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

![활동 로그](./media/azure-log-audit/azure-log-audit-fig1.png)


Azure Portal, [CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell cmdlet 및 [Azure Monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough)를 사용하여 활동 로그에서 이벤트를 검색할 수 있습니다. 활동 로그에는 19일간의 데이터 보존 기간이 있습니다.

통합 시나리오
-   [활동 로그 이벤트를 트리거 해제하는 전자 메일 또는 웹후크 경고를 만듭니다.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)에서 수집하도록 [Event Hub로 스트리밍합니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs).

-   [PowerBI 콘텐츠 팩](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 PowerBI에서 분석합니다.

-   [보관 또는 수동 검사를 위해 Storage 계정에 저장합니다.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) 로그 프로필을 사용하여 보존 기간(일)을 지정할 수 있습니다.

-   Azure Portal에서 쿼리하고 봅니다.

-   PowerShell Cmdlet, CLI 또는 REST API를 통해 쿼리합니다.

-   로그 프로필을 사용하여 활동 로그를 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)로 내보냅니다.

하나의 내보내는 로그와 동일한 구독에 있지 않은 저장소 계정 또는 [Event Hub 네임스페이스](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive)를 사용할 수 있습니다. 설정을 구성하는 사용자는 두 구독 모두에 대해 적절한 [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 액세스 권한을 가지고 있어야 합니다.
### <a name="azure-diagnostic-logs"></a>Azure 진단 로그
Azure 진단 로그는 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 리소스에서 내보냅니다. 이러한 로그의 내용은 리소스 종류(예를 들어 [Windows 이벤트 시스템 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)는 VM에 대한 진단 로그의 한 범주이며, [Blob, 테이블 및 큐 로그](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)는 저장소 계정에 대한 진단 로그의 범주임)에 따라 다르며, 구독에 있는 리소스에서 수행된 작업에 대한 정보를 제공하는 활동 로그와는 다릅니다.

![Azure 진단 로그](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure 진단 로그는 PowerShell, CLI(명령줄 인터페이스) 및 REST API를 사용하여 Azure Portal과 같은 여러 가지 구성 옵션을 제공합니다.

**통합 시나리오**
-   감사 또는 수동 검사를 위해 [Storage 계정](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) 에 저장합니다. [진단 설정]을 사용하여 보존 기간(일)을 지정할 수 있습니다.

-   타사 서비스 또는 사용자 지정 분석 솔루션(예: [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/))에서 수집하도록 [Event Hubs로 스트리밍합니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs).

-   [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)를 사용하여 분석

**지원되는 서비스, 진단 로그용 스키마 및 지원되는 리소스 종류별 로그 범주**


| 서비스 | 스키마 및 문서 | 리소스 종류 | Category |
| ------- | ------------- | ------------- | -------- |
|Load Balancer| [Azure 부하 분산 장치에 대한 로그 분석(미리보기)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|네트워크 보안 그룹|[NSG(네트워크 보안 그룹)에 대한 로그 분석](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Application Gateway|[Application Gateway에 대한 진단 로깅](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Key Vault|[Azure Key Vault 로깅](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[검색 트래픽 분석 설정 및 사용](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Azure Data Lake Store에 대한 진단 로그에 액세스](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|감사|
|Data Lake Analytics|[Azure Data Lake Analytics에 대한 진단 로그에 액세스](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|감사|
|||Microsoft.DataLakeAnalytics/accounts|요청|
|||Microsoft.DataLakeStore/accounts|요청|
|Logic Apps|[Logic Apps B2B 사용자 지정 추적 스키마](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch 진단 로깅](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Automation에 대한 Log Analytics](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Azure Event Hubs 진단 로그](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[작업 진단 로그](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|실행|
|||Microsoft.StreamAnalytics/streamingjobs|작성|
|Service Bus|[Azure Service Bus 진단 로그](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory 보고
Azure AD(Azure Active Directory)에는 디렉터리에 대한 보안, 활동 및 감사 보고서가 포함되어 있습니다. [Azure Active Directory 감사 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)를 사용하면 고객이 자신의 Azure Active Directory에서 발생한 권한 있는 작업을 식별할 수 있습니다. 권한 있는 작업에는 권한 상승 변경(예: 역할 만들기 또는 암호 재설정), 정책 구성 변경(예: 암호 정책) 또는 디렉터리 구성 변경(예: 도메인 페더레이션 설정 변경)이 포함됩니다.

보고서는 이벤트 이름의 감사 레코드, 작업을 수행한 행위자, 변경으로 인한 영향을 받은 대상 리소스, 날짜 및 시간(UTC)을 제공합니다. 고객은 [감사 로그 보기](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)에서 설명한 대로 [Azure Portal](https://portal.azure.com/)을 통해 Azure Active Directory에 대한 감사 이벤트 목록을 검색할 수 있습니다. 포함된 보고서 목록은 다음과 같습니다.

| 보안 보고서 | 작업 보고서 | 감사 보고서 |
| :--------------- | :--------------- | :------------ |
|알 수 없는 원본에서 로그인| 응용 프로그램 사용: 요약| 디렉터리 감사 보고서|
|여러 번의 실패 후 로그인|  응용 프로그램 사용: 세부||
|여러 지역에서의 로그인|    응용 프로그램 대시보드||
|의심스러운 작업이 있는 IP 주소에서 로그인|   계정 프로비전 오류||
|비정상적인 로그인 작업|    개별 사용자 장치||
|감염 가능성이 있는 장치에서 로그인|   개별 사용자 활동||
|비정상적인 로그인 활동을 포함하는 사용자| 그룹 활동 보고서||
||암호 재설정 등록 활동 보고서||
||암호 재설정 활동|||

이러한 보고서의 데이터는 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 응용 프로그램에 유용할 수 있습니다. Azure AD Reporting API는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 이러한 [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started)는 다양한 프로그래밍 언어와 도구에서 호출할 수 있습니다.

Azure AD 감사 보고서의 이벤트는 180일 동안 보존됩니다.

> [!Note]
> 보고서 보존에 대한 자세한 내용은 [Azure Active Directory 보고서 보존 정책](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)을 참조하세요.

장기 보존 기간 동안 감사 이벤트를 저장하려는 고객의 경우 보고 API를 사용하여 [감사 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events)를 정기적으로 별도의 데이터 저장소로 가져올 수 있습니다.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Azure 진단을 사용하는 Virtual Machine 로그
[Azure 진단](https://docs.microsoft.com/azure/azure-diagnostics)은 배포된 응용 프로그램에서 진단 데이터를 수집할 수 있도록 하는 Azure의 기능입니다. 별도의 여러 원본에서 진단 확장을 사용할 수 있습니다. [Azure 클라우드 서비스 웹 및 작업자 역할](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)이 현재 지원되고 있습니다.

![Azure 진단을 사용하는 Virtual Machine 로그](./media/azure-log-audit/azure-log-audit-fig3.png)

Microsoft Windows를 실행하는 [Azure Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) 및 [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)입니다.

다음을 사용하여 가상 컴퓨터에서 Azure 진단을 사용하도록 설정할 수 있습니다.

-   Visual Studio를 사용하려면 [Visual Studio를 사용하여 Azure Virtual Machines 추적](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)을 참조하세요.

-   [Azure Virtual Machine에서 원격으로 Azure 진단 설정](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [PowerShell을 사용하여 Azure Virtual Machines에서 진단 설정](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 기능으로 Windows 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>저장소 분석
[Azure Storage 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)은 로깅을 수행하며 Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 계정에 대한 문제를 진단할 수 있습니다. Storage 분석 로깅은 [Blob, 큐 및 Table service](https://docs.microsoft.com/azure/storage/storage-introduction)에서 사용할 수 있습니다. 저장소 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다.

이 정로를 사용하면 개별 요청을 모니터링하고 저장소 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다. 서비스 끝점에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 저장소 계정의 활동이 Blob 끝점에는 있지만 테이블 또는 큐 끝점에는 없는 경우 Blob service와 관련된 로그만 만들어집니다.

저장소 분석을 사용하려면 모니터링할 각 서비스에 대해 저장소 분석을 개별적으로 사용하도록 설정해야 합니다. 이 작업은 [Azure portal](https://portal.azure.com/)에서 수행할 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. [서비스 속성 설정] 작업을 사용하여 각 서비스에 대해 개별적으로 저장소 분석을 사용하도록 설정합니다.

집계된 데이터는 알려진 로깅용 Blob 및 알려진 메트릭용 테이블에 저장됩니다. Blob service 및 Table service API를 사용하면 이러한 Blob와 테이블에 액세스할 수 있습니다.

저장소 분석에 저장되는 데이터의 양은 총 Storage 계정의 총 한도와 관계없이 20TB로 제한됩니다. 모든 로그는 $logs라는 컨테이너의 [블록 Blob](https://docs.microsoft.com/azure/storage/storage-analytics)에 저장되며, Storage 계정에 대해 저장소 분석을 사용하도록 설정하면 자동으로 만들어집니다.

> [!Note]
> 청구 및 데이터 보존 정책에 대한 자세한 내용은 [저장소 분석 및 청구](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)를 참조하세요.
>
> [!Note]
> 저장소 계정 제한에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/storage-scalability-targets)를 참조하세요.

다음과 같은 유형의 인증된 요청 및 익명 요청이 기록됩니다.



| 인증됨  | 익명|
| :------------- | :-------------|
| 성공한 요청 | 성공한 요청 |
|실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류) | SAS(공유 액세스 서명)를 사용하는 요청(실패한 요청 및 성공한 요청 포함) |
| SAS(공유 액세스 서명)를 사용하는 요청(실패한 요청 및 성공한 요청 포함) |클라이언트와 서버 모두에 대한 시간 제한 오류 |
|   분석 데이터에 대한 요청 |    오류 코드가 304(수정되지 않음)인 실패한 GET 요청 |
| 로그 만들기/삭제 등 저장소 분석 자체에서 수행한 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) 항목에 나와 있습니다. | 기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)에서 설명하고 있습니다. |

### <a name="azure-networking-logs"></a>Azure 네트워킹 로그
Azure의 네트워킹 로깅 및 모니터링은 포괄적이며 다음 두 가지 범주를 포함합니다.

-   [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) - 시나리오 기반 모니터링이 Network Watcher 기능과 함께 제공됩니다. 이 서비스에는 패킷 캡처, 다음 홉, IP 흐름 확인, 보안 그룹 보기, NSG 흐름 로그가 포함되어 있습니다. 시나리오 수준 모니터링에서는 개별 네트워크 리소스 모니터링과 달리 네트워크 리소스의 종단 간 보기를 제공합니다.

-   [리소스 모니터링](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) - 리소스 수준 모니터링은 진단 로그, 메트릭, 문제 해결 및 리소스 상태의 네 가지 기능으로 구성됩니다. 이러한 모든 기능은 네트워크 리소스 수준에서 구현됩니다.

![Azure 네트워킹 로그](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다.

**NSG 흐름 로깅** - 네트워크 보안 그룹의 흐름 로그를 사용하면 그룹의 보안 규칙으로 허용되거나 거부되는 트래픽과 관련된 로그를 캡처할 수 있습니다. 이러한 흐름 로그는 JSON 형식으로 작성되고, 규칙별 아웃바운드/인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜) 및 트래픽이 허용되거나 거부된 경우를 보여 줍니다.

### <a name="network-security-group-flow-logging"></a>네트워크 보안 그룹 흐름 로깅

[네트워크 보안 그룹 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)는 네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다. 이러한 흐름 로그는 JSON 형식으로 작성되고, 규칙별 아웃바운드/인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜) 및 트래픽이 허용되거나 거부된 경우를 보여 줍니다.

흐름 로그는 네트워크 보안 그룹을 대상으로 하지만 다른 로그와 동일하게 표시되지 않습니다. 흐름 로그는 저장소 계정에만 저장됩니다.

다른 로그에서 보듯이 흐름 로그에 동일한 보존 정책을 적용합니다. 로그에는 1일에서 365일까지 설정할 수 있는 보존 정책이 있습니다. 보존 정책을 설정하지 않으면 로그는 계속 유지됩니다.

**진단 로그**

정기적이고 자동적인 이벤트가 네트워크 리소스에서 만들어지고 저장소 계정에 기록되어 이벤트 허브 또는 Log Analytics로 전송됩니다. 이러한 로그는 리소스 상태에 대한 정보를 제공하며, Power BI 및 Log Analytics와 같은 도구에서 볼 수 있습니다. 진단 로그를 보는 방법을 알아보려면 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)를 방문하세요.

![진단 로그](./media/azure-log-audit/azure-log-audit-fig5.png)

진단 로그는 [부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), 경로 및 [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)에서 사용할 수 있습니다.

Network Watcher는 진단 로그 보기를 제공합니다. 이 보기에는 진단 로깅을 지원하는 모든 네트워킹 리소스가 포함됩니다. 이 보기에서 네트워킹 리소스를 빠르고 편리하게 사용하거나 사용하지 않도록 설정할 수 있습니다.


앞서의 로깅 기능 외에도 Network Watcher에는 현재 다음과 같은 기능이 있습니다.
- [토폴로지](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) - 리소스 그룹의 네트워크 리소스 간 다양한 연결 및 상호 연결을 보여 주는 네트워크 수준 보기를 제공합니다.

- [변수 패킷 캡처](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) - 가상 컴퓨터의 내부 및 외부 패킷 데이터를 캡처합니다. 고급 필터링 옵션과 세밀하게 조정되는 제어(예: 시간 및 크기 제한 설정 가능)가 다양하게 제공됩니다. 패킷 데이터는 Blob 저장소 또는 .cap 형식의 로컬 디스크에 저장할 수 있습니다.

-   [IP 흐름 확인](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - 흐름 정보의 5개 튜플 패킷 매개 변수(대상 IP, 원본 IP, 대상 포트, 원본 포트 및 프로토콜)에 따라 패킷이 허용되거나 거부되는지 여부를 확인합니다. 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙과 그룹이 반환됩니다.

-   [다음 홉](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) - Azure 네트워크 패브릭에서 라우팅되는 패킷의 다음 홉을 결정하여 잘못 구성된 사용자 정의 경로를 진단할 수 있습니다.

-   [보안 그룹 보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) - VM에 적용되는 효과적이고 실용적인 보안 규칙을 가져옵니다.

-   [Virtual Network 게이트웨이 및 연결 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) - Virtual Network 게이트웨이 및 연결 문제를 해결하는 기능을 제공합니다.

-   [네트워크 구독 제한](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) - 제한과 대조한 네트워크 리소스 사용량을 볼 수 있습니다.

### <a name="application-insight"></a>Application Insight

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장성 있는 APM(Application Performance Management) 서비스입니다. 이를 사용하여 라이브 웹 응용 프로그램을 모니터링합니다. 성능 이상을 자동으로 검색합니다. 사용자가 문제를 진단하고 앱을 사용하여 실제로 수행할 작업을 이해할 수 있도록 돕는 강력한 분석 도구를 포함합니다.

 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

 .NET, Node.js 및 J2EE, 호스팅된 온-프레미스 또는 클라우드의 다양한 플랫폼에서 앱과 함께 사용합니다. devOps 프로세스와 통합되며, 다양한 개발 도구에 대한 연결 지점이 있습니다.

![Application Insight](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights는 응용 프로그램 팀에서 앱의 작동 방식과 사용 방식을 이해하는 데 도움을 주기 위해 고안되었습니다. 다음 사항을 모니터링합니다.

-   **요청 속도, 응답 시간 및 실패율** - 하루 중 어느 시간에 어떤 페이지를 가장 많이 방문하는지, 사용자가 어디에 있는지 확인합니다. 어떤 페이지가 가장 성능이 우수한지 확인합니다. 요청이 더 있는데 응답 시간과 실패율이 높아지면 아마도 리소스 문제가 있는 것입니다.

-   **종속성 비율, 응답 시간 및 실패율** - 외부 서비스 때문에 속도가 느려지는지 확인합니다.

-   **예외** - 집계된 통계를 분석하거나 특정 인스턴스를 선택하여 스택 추적 및 관련 요청을 자세히 분석합니다. 서버 및 브라우저 예외가 전부 보고됩니다.

-   **페이지 보기 및 로드 성능** - 사용자의 브라우저에서 보고합니다.

-   웹 페이지의 **AJAX 호출** - 속도, 응답 시간 및 실패율.

-   **사용자 및 세션 수**.

-   Windows 또는 Linux 서버 컴퓨터의 **성능 카운터** - CPU, 메모리, 네트워크 사용량 등.

-   Docker 또는 Azure의 **호스트 진단**.

-   앱의 **진단 추적 로그** - 추적 이벤트를 요청과 상호 연결하는 데 사용됩니다.

-   판매된 품목, 승리한 게임 등의 비즈니스 이벤트를 추적하기 위해 개발자가 직접 클라이언트 또는 서버 코드로 작성하는 **사용자 지정 이벤트 및 메트릭**.

**통합 시나리오 및 설명 목록:**

| 통합 시나리오 | 설명 |
| --------------------- | :---------- |
|[응용 프로그램 맵](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|주요 메트릭 및 경고가 포함된 앱의 구성 요소입니다.||
|[인스턴스 데이터에 대한 진단 검색](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| 요청, 예외, 종속성 호출, 로그 추적 및 페이지 보기와 같은 이벤트를 검색하고 필터링할 수 있습니다.||
|[집계된 데이터에 대한 메트릭 탐색기](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|요청, 오류 및 예외의 비율과 응답 시간, 페이지 로드 시간과 같은 집계된 데이터를 탐색, 필터링 및 분할할 수 있습니다.||
|[대시보드](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|여러 리소스의 데이터를 매시업한 후 다른 사용자와 공유할 수 있습니다. 다중 구성 요소 응용 프로그램에서 사용하거나 단체방에 연속으로 표시하는 데 유용합니다.||
|[라이브 메트릭 스트림](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|새 빌드를 배포할 때 이러한 실시간에 가까운 성능 표시기를 확인하여 모든 항목이 예상대로 작동하는지 알 수 있습니다.||
|[분석](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|이 강력한 쿼리 언어를 사용하여 앱의 성능 및 사용 현황에 대한 까다로운 질문에 답변할 수 있습니다.||
|[자동 및 수동 경고](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|자동 경고는 앱의 일반적인 원격 분석 패턴에 맞게 조정되고, 일반적인 패턴을 벗어나는 항목이 있으면 트리거합니다. 특정 수준의 사용자 지정 또는 표준 메트릭에 대해 경고를 설정할 수도 있습니다.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|코드의 성능 데이터를 참조하세요. 스택 추적의 코드로 이동하세요.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|사용 현황 메트릭을 다른 비즈니스 인텔리전스와 통합합니다.||
|[REST API](https://dev.applicationinsights.io/)|메트릭 및 원시 데이터에 대한 쿼리를 실행하는 코드를 작성합니다.||
|[연속 내보내기](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|원시 데이터가 도착하는 즉시 대량으로 저장소에 내보냅니다.||

### <a name="azure-security-center-alerts"></a>Azure Security Center 경고
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)는 Azure 리소스, 네트워크 및 연결된 파트너 솔루션(예: 방화벽 및 끝점 보호 솔루션)의 로그 데이터를 자동으로 수집, 분석 및 통합하여 실제 위협을 검색하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다.

보안 센터 위협 감지는 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 보안 정보를 자동으로 수집하여 작동합니다. 위협을 식별하도록 종종 여러 소스의 정보를 상호 연결하는 이 정보를 분석합니다. 보안 경고는 위협을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에서 우선 순위가 지정됩니다.

![Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

보안 센터는 서명 기반 방식을 뛰어 넘는 고급 보안 분석을 사용합니다. 수동 접근 방법을 사용하여 식별할 수 없는 위협을 검색하고 공격의 진화를 예측하는 대규모 데이터 및 [기계 학습](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)의 혁신적인 기술을 적용하여 클라우드 패브릭 전체에 대한 이벤트를 평가합니다. 이러한 보안 분석은 다음과 같습니다.

-   **통합된 위협 인텔리전스**: Microsoft 제품과 서비스, Microsoft DCU(Digital Crimes Unit), MSRC(Microsoft 보안 대응 센터) 및 외부 피드에서 글로벌 위협 인텔리전스를 적용하여 알려진 악성 프로그램을 찾습니다.

-   **동작 분석**: 알려진 패턴을 적용하여 악의적인 동작을 검색합니다.

-   **변칙 검색**: 통계적 프로파일링을 사용하여 기록 초기 계획을 만듭니다. 잠재적 공격 벡터를 준수하는 설정된 기준에서 편차에 대해 경고합니다.


많은 보안 작업 및 사고 대응 팀은 보안 경고를 조사하고 심사하기 위한 시작점으로 SIEM(보안 정보 및 이벤트 관리) 솔루션을 활용합니다. Azure 로그 통합을 사용하여 고객은 Azure 진단 및 Azure 감사 로그에 수집된 Azure Security Center 경고와 가상 머신 보안 이벤트를 거의 실시간으로 로그 분석 또는 SIEM 솔루션과 동기화할 수 있습니다.


## <a name="log-analytics"></a>Log Analytics

Log Analytics는 클라우드 및 온-프레미스 환경의 리소스에서 생성된 데이터를 수집 및 분석하도록 도와주는 Azure의 서비스입니다. 통합 검색 및 사용자 지정 대시보드를 사용하여 실제 위치에 관계없이 모든 워크로드 및 서버에서 수백만 개의 레코드를 쉽게 분석할 수 있는 실시간 정보를 제공합니다.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Log Analytics의 핵심은 Azure 클라우드에서 호스트되는 Log Analytics 작업 영역입니다. 데이터 원본을 구성하고 구독에 솔루션을 추가하면 연결된 원본에서 작업 영역으로 데이터가 수집됩니다. 데이터 원본 및 솔루션은 각각 고유한 속성 집합을 가진 서로 다른 레코드 유형을 만들지만 작업 영역에 대한 쿼리에서 여전히 함께 분석할 수 있습니다. 따라서 동일한 도구 및 메서드를 사용하여 다양한 원본에서 수집된 여러 종류의 데이터로 작업할 수 있습니다.

연결된 원본은 Log Analytics에서 수집된 데이터를 생성하는 컴퓨터 및 기타 리소스입니다. 여기에는 직접 연결되어 있는 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) 및 [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) 컴퓨터에 설치된 에이전트 또는 [연결된 System Center Operations Manager 관리 그룹](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)의 에이전트가 포함될 수 있습니다. 또한 Log Analytics는 [Azure 저장소](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)에서 데이터를 수집할 수도 있습니다.

[데이터 원본](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) 은 각각의 연결된 원본에서 수집되는 여러 종류의 데이터입니다. 여기에는 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) 및 Linux 에이전트의 이벤트 및 [성능 데이터](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)뿐만 아니라 [IIS 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) 및 [사용자 지정 텍스트 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs)와 같은 원본이 포함됩니다. 수집할 각 데이터 원본을 구성하면 각 연결된 원본에 구성이 자동으로 전달됩니다.

[Azure 서비스에 대한 로그 및 메트릭을 수집](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)하는 방법에는 다음 네 가지가 있습니다.
1.  Azure 진단에서 Log Analytics로 직접 연결(다음 표의 진단)

2.  Azure 진단, Azure Storage, Log Analytics 순서로 연결(다음 표의 저장소)

3.  Azure 서비스에 대한 커넥터(다음 표의 커넥터)

4.  데이터를 수집한 후 Log Analytics에 게시하기 위한 스크립트(다음 표에서 비어 있으며, 나열되지 않은 서비스에 해당)

| 서비스 | 리소스 종류 | 로그 | 메트릭 | 해결 방법 |
| :------ | :------------ | :--- | :------ | :------- |
|응용 프로그램 게이트웨이|  Microsoft.Network/<br>applicationGateways|  진단|진단|    [Azure Application](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application insights||     커넥터|  커넥터|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [커넥터(미리 보기)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Automation 계정|   Microsoft.Automation/<br>AutomationAccounts|    진단||       [자세한 정보](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Batch 계정|    Microsoft.Batch/<br>batchAccounts|  진단|    진단||
|클래식 Cloud Services||       Storage||       [자세한 정보](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>계정|       진단|||
|Data Lake Analytics|   Microsoft.DataLakeAnalytics/<br>계정|   진단|||
|Data Lake Store|   Microsoft.DataLakeStore/<br>계정|   진단|||
|이벤트 허브 네임스페이스|   Microsoft.EventHub/<br>namespaces|  진단|    진단||
|IoT Hub|  Microsoft.Devices/<br>IotHubs||     진단||
|Key Vault| Microsoft.KeyVault/<br>vaults|  진단  || [KeyVault 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|부하 분산 장치|    Microsoft.Network/<br>loadBalancers|    진단|||
|Logic Apps|    Microsoft.Logic/<br>workflows|  진단|    진단||
||Microsoft.Logic/<br>integrationAccounts||||
|네트워크 보안 그룹|   Microsoft.Network/<br>networksecuritygroups|진단||   [Azure 네트워크 보안 그룹 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|복구 자격 증명|   Microsoft.RecoveryServices/<br>vaults|||[Azure Recovery Services 분석(미리 보기)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Search 서비스|   Microsoft.Search/<br>searchServices|    진단|    진단||
|Service Bus 네임스페이스| Microsoft.ServiceBus/<br>namespaces|    진단|진단|    [Service Bus 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric 분석(미리 보기)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL(v12)| Microsoft.Sql/<br>servers/<br>데이터베이스||       진단||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Storage|||         스크립트| [Azure Storage 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtual Machines|  Microsoft.Compute/<br>virtualMachines|  내선 번호|  내선 번호||
||||진단||
|Virtual Machines 확장 집합|   Microsoft.Compute/<br>virtualMachines    ||진단||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|웹 서버 팜|Microsoft.Web/<br>serverfarms||   진단
|웹 사이트| Microsoft.Web/<br>sites ||      진단|    [자세한 정보](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>온-프레미스 SIEM 시스템과 로그 통합
[Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324)을 사용하면 Azure 리소스의 원시 로그를 온-프레미스 **SIEM(Security Information and Event Management) 시스템**에 통합할 수 있습니다.

![로그 통합](./media/azure-log-audit/azure-log-audit-fig9.png)

Azure 로그 통합은 Windows(WAD) 가상 머신, Azure 활동 로그, Azure Security Center 경고 및 Azure 리소스 공급자 로그에서 Azure 진단을 수집합니다. 이 통합은 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 대시보드를 제공합니다.



Azure 로그 통합은 현재 Azure 활동 로그, Azure 구독의 Windows 가상 머신에서 제공된 Windows 이벤트 로그, Azure Security Center 경고, Azure 진단 로그 및 Azure Active Directory 감사 로그를 통합하도록 지원합니다.

| 로그 형식 | JSON(Splunk, ArcSight, Qradar)을 지원하는 Log Analytics |
| :------- | :-------------------------------------------------------- |
|AAD 감사 로그|    예|
|활동 로그| 예|
|ASC 경고 |예|
|진단 로그(리소스 로그)|  예|
|VM 로그|   예(JSON을 통하지 않고 전달된 이벤트를 통해)|


다음 표에서는 로그 범주 및 SIEM 통합 세부 정보를 설명합니다.

[Azure 로그 통합 시작](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) - 이 자습서에서는 Azure 로그 통합을 설치하고, Azure WAD 저장소, Azure 활동 로그, Azure Security Center 경고 및 Azure Active Directory 감사 로그를 통합하는 방법을 단계별로 안내합니다.

통합 시나리오

-   [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure 로그 통합을 구성하는 방법을 보여줍니다.

-   [Azure 로그 통합 FAQ(질문과 대답)](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.

-   [보안 센터 경고를 Azure 로그 통합과 통합](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) - 이 문서에서는 Azure 진단 및 Azure 감사 로그에 수집된 가상 컴퓨터 보안 이벤트와 함께 보안 센터 경고를 로그 분석 또는 SIEM 솔루션과 동기화하는 방법을 보여줍니다.

## <a name="next-steps"></a>다음 단계

- [감사 및 로깅](https://www.microsoft.com/trustcenter/security/auditingandlogging)

가시성을 유지하고 시기 적절한 보안 경고에 빠르게 대응하여 데이터를 보호합니다.

- [Azure 내 보안 로깅 및 감사 로그 수집](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Azure 인스턴스에서 올바른 보안 및 감사 로그를 수집하고 있는지 확인하기 위해 적용해야 하는 설정을 다룹니다.

- [사이트 모음에 대한 감사 설정 구성](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

사이트 모음 관리자에서 특정 사용자가 수행한 작업 기록을 검색할 수 있으며, 특정 날짜 범위 동안 수행된 작업 기록도 검색할 수 있습니다. 

- [Office 365 보안 및 준수 센터에서 감사 로그 검색](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Office 365 보안 및 준수 센터를 통해 통합 감사 로그를 검색하여 Office 365 조직에서 사용자 및 관리자 활동을 확인할 수 있습니다.


