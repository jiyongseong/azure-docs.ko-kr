---
title: Azure 응용 프로그램 및 리소스 모니터링 | Microsoft Docs
description: Azure 서비스 및 응용 프로그램에 대한 전체 모니터링 전략에 작용하는 Microsoft 서비스 및 기능을 대략적으로 설명합니다.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: robb,bwren
ms.openlocfilehash: 27d3835b5574492fdb62df0131ca3a542dd21ec3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777555"
---
# <a name="monitoring-azure-applications-and-resources"></a>Azure 응용 프로그램 및 리소스 모니터링

모니터링은 비즈니스 응용 프로그램의 성능, 상태 및 가용성과 그것이 의존하는 리소스 결정하기 위해 데이터를 수집하고 분석하는 행위입니다. 효과적인 모니터링 전략은 응용 프로그램 구성 요소의 세부 작업을 이해하는 데 도움이 됩니다. 또한 중요한 문제를 사전에 알려줘서 문제가 커지기 전에 해결하여 작동 시간을 늘리는 데 도움이 됩니다.

Azure에는 모니터링 공간에서 특정 역할이나 태스크를 개별적으로 수행하는 여러 서비스가 포함됩니다. 이러한 서비스는 응용 프로그램 및 응용 프로그램을 지원하는 Azure 리소스로부터 원격 분석데이터를 수집하고 분석하고 조치를 취하는 포괄적인 솔루션을 제공합니다. 이러한 서비스가 하이브리드 모니터링 환경을 제공하기 위해 중요한 온-프레미스 리소스를 모니터링하기 위해 작동할 수도 있습니다. 사용 가능한 도구와 데이터를 이해하는 것이 응용 프로그램에 대한 전체 모니터링 전략을 개발하는 첫 번째 단계입니다.

다음 다이어그램은 Azure 리소스 모니터링을 제공하기 위해 함께 작동하는 구성 요소를 개념적으로 보여줍니다. 다음 섹션에서는 이러한 구성 요소를 설명하고 자세한 기술 정보에 대한 링크도 제공합니다.

![모니터링 개요](media/monitoring-overview/monitoring-products-overview.png)


## <a name="shared-capabilities"></a>공유 기능
코어 및 심층 모니터링 서비스는 다음과 같은 기능을 제공하는 기능을 공유합니다.

### <a name="alerts"></a>Alerts
[Azure 경고](../monitoring-and-diagnostics/monitoring-overview-alerts.md)는 위험 조건을 사전에 알리고 잠재적으로 정정 작업을 수행합니다. 경고 규칙은 메트릭 및 로그를 포함하여 여러 원본의 데이터를 사용할 수 있습니다. 또한 고유한 수신자 집합과 경고에 대한 작업을 포함하는 [작업 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md)을 사용합니다. 사용자의 요구 사항에 따라, 웹후크를 사용하여 경고가 외부 작업을 시작하고 ITSM 도구와 통합되도록 할 수 있습니다.

### <a name="dashboards"></a>대시보드
[Azure 대시보드](../azure-portal/azure-portal-dashboards.md)에서는 다양한 종류의 데이터를 [Azure Portal](https://portal.azure.com)의 단일 창으로 결합할 수 있습니다. 그런 다음, 다른 Azure 사용자와 대시보드를 공유할 수 있습니다.

예를 들어 다음 항목이 결합된 대시보드를 만들 수 있습니다.
- 메트릭 그래프를 표시하는 타일
- 활동 로그 테이블
- Application Insights의 사용 현황 차트
- Log Analytics의 로그 검색 결과

Log Analytics 데이터를 [Power BI](https://docs.microsoft.com/power-bi/)로 내보낼 수도 있습니다. 여기에서 추가적인 시각화를 활용할 수 있습니다. 조직 내부 및 외부의 다른 사람들이 데이터를 사용할 수 있게 만들 수도 있습니다.

### <a name="metrics-explorer"></a>메트릭 탐색기
[메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md)은 Azure 리소스에서 생성된 숫자 값으로, 리소스의 작동 및 성능을 이해하는 데 도움이 됩니다. 메트릭 탐색기를 사용하면, 다른 소스의 데이터를 분석하기 위해 Log Analytics로 메트릭을 전송할 수 있습니다.


## <a name="core-monitoring"></a>핵심 모니터링
핵심 모니터링은 Azure 리소스 전반에 걸쳐 필수적이고 기본적인 모니터링을 제공합니다. 이러한 서비스는 최소한의 구성만 필요하며, 프리미엄 모니터링 서비스에 사용되는 핵심 원격 분석 데이터를 수집합니다.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)는 [메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) 및 [진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)를 수집하도록 하여 Azure 서비스에 대해 핵심적인 모니터링을 수행할 수 있도록 합니다. 예를 들어, 활동 로그는 새 리소스가 만들어지거나 수정되는 시기를 알려줍니다.

다양한 리소스와 가상 머신 내부의 운영 체제에 대한 성능 통계를 제공하는 메트릭을 사용할 수 있습니다. Azure Portal에서 탐색기 중 하나를 사용하여 이 데이터를 보고 이러한 메트릭을 기반으로 경고를 만들 수 있습니다. Azure Monitor는 가장 빠른 메트릭 파이프라인을 제공하므로(5분에서 1분으로 ) 시간이 중요한 경고 및 알림에 사용해야 합니다.

추세 파악 및 자세한 분석을 위해 이러한 메트릭 및 로그를 Azure Log Analytics로 전송하거나 해당 분석의 결과로 중대한 문제를 사전에 알리기 위한 추가 경고 규칙을 만들 수 있습니다.  

> [!NOTE]
> 진단 설정을 통한 Log Analytics에 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 Log Analytics에 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md)는 리소스 구성 및 사용량 원격 분석 데이터를 지속적으로 모니터링합니다. 그런 다음, 모범 사례를 기반으로 맞춤형 권장 사항을 제공합니다. 이러한 권장 사항을 따르면 응용 프로그램을 지원하는 리소스의 성능, 보안 및 가용성을 향상시킬 수 있습니다.

### <a name="service-health"></a>서비스 상태
응용 프로그램의 상태는 종속되는 Azure 서비스에 따라 좌우됩니다. [Azure Service Health](../service-health/service-health-overview.md) 응용 프로그램에 영향을 줄 수 있는 Azure 서비스의 문제를 식별합니다. Service Health는 예약된 유지 관리를 계획하는 데에도 도움이 됩니다.

### <a name="activity-log"></a>활동 로그
[활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)는 Azure 리소스의 작동에 대한 데이터를 제공합니다. 여기에는 다음 정보가 포함됩니다.
- 리소스에 대한 구성 변경.
- 서비스 상태 인시던트.
- 리소스 활용 향상을 위한 권장 사항.
- 자동 크기 조정 작업과 관련된 정보.

Azure Portal에서 해당 페이지의 특정 리소스에 대한 로그를 볼 수 있습니다. 또는 활동 로그 탐색기에서 여러 리소스의 로그를 볼 수 있습니다.

Log Analytics에 활동 로그 항목을 보낼 수도 있습니다. 여기에서 관리 솔루션, 가상 머신의 에이전트 및 기타 소스에서 수집한 데이터를 사용하여 로그를 분석할 수 있습니다.

## <a name="deep-monitoring-services"></a>심층 모니터링 서비스
다음과 같은 Azure 서비스는 모니터링 데이터를 수집 및 분석하기 위해 더 깊은 수준에서 풍부한 기능을 제공합니다. 이러한 서비스는 핵심 모니터링을 기반으로 구축되며 Azure의 공통 기능을 활용합니다. 수집된 데이터를 강력하게 분석하여 응용 프로그램 및 인프라에 대해 독창적인 통찰력을 제공합니다. 다양한 사용자를 대상으로 하는 시나리오의 컨텍스트에 맞게 데이터를 제공합니다.

## <a name="deep-application-monitoring"></a>심층 응용 프로그램 모니터링
### <a name="application-insights"></a>Application Insights
[Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights)를 사용하면 클라우드 또는 온-프레미스에서 호스트되는 응용 프로그램의 가용성, 성능 및 사용량을 모니터링할 수 있습니다.

Application Insights로 작업하도록 응용 프로그램을 계측하면 깊은 통찰력을 얻고 DevOps 시나리오를 구현할 수 있습니다. 사용자가 보고할 때까지 기다리지 않고 오류를 빠르게 식별하고 진단할 수 있습니다. 수집한 정보를 사용하여, 응용 프로그램의 유지 관리 및 개선에 대해 현명한 결정을 내릴 수 있습니다.

Application Insight에는 수집하는 데이터와 상호 작용할 수 있는 광범위한 도구가 있습니다. Application Insights는 공용 저장소에 데이터를 저장합니다. Log Analytics 쿼리 언어를 사용하여 경고, 대시보드 및 심층 분석과 같은 공유된 기능을 활용할 수 있습니다.

## <a name="deep-infrastructure-monitoring"></a>심층 인프라 모니터링
### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)는 다양한 리소스(타사 도구 포함)의 데이터를 단일 리포지토리에 수집함으로써 Azure 모니터링에서 중심 역할을 수행합니다. 여기에 강력한 쿼리 언어를 사용하여 데이터를 분석할 수 있습니다.

Application Insights 및 Azure Security Center는 Log Analytics 데이터 저장소에 데이터를 저장하고 분석 엔진을 사용합니다. 데이터는 또한 Azure Monitor, 관리 솔루션 및 클라우드 또는 온-프레미스의 가상 머신에 설치된 에이전트에서 수집됩니다. 이렇게 공유된 기능은 환경에 대한 전체적인 그림을 만드는 데 도움이 됩니다.

### <a name="management-solutions"></a>관리 솔루션
[관리 솔루션](../log-analytics/log-analytics-add-solutions.md)은 특정 응용 프로그램이나 서비스를 깊이 있게 이해하도록 하는 패키지된 논리 집합입니다. 이 솔루션은 수집한 모니터링 데이터를 저장 및 분석하기 위해 Log Analytics를 활용합니다.

관리 솔루션은 Microsoft 및 다양한 Azure 및 타사 서비스에 대한 모니터링을 제공하는 파트너 업체를 통해 받을 수 있습니다. 모니터링 솔루션의 예는 다음과 같습니다.
* [컨테이너 모니터링](../log-analytics/log-analytics-containers.md): 컨테이너 호스트를 확인하고 관리하는 데 도움이 됩니다.
* [Azure SQL 분석](../log-analytics/log-analytics-azure-sql.md): Azure SQL 데이터베이스의 성능 메트릭을 수집하고 시각화합니다.

*모니터* 화면의 Azure Portal에서 모든 사용 가능한 관리 솔루션을 볼 수 있습니다.

### <a name="network-monitoring"></a>네트워크 모니터링
Azure 또는 온-프레미스에서 네트워크의 다양한 측면을 모니터링하기 위해 함께 작동하는 여러 도구가 있습니다.  

[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)는 Azure의 여러 다른 네트워크 시나리오에 대한 시나리오 기반 모니터링 및 진단을 제공합니다. 추가 분석을 위해 Azure 메트릭 및 진단에 데이터를 저장합니다. 네트워크의 다양한 측면을 모니터링하기 위해 다음 솔루션과 함께 작동합니다.

[NPM(네트워크 성능 모니터)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/)은 공용 클라우드, 데이터 센터 및 온-프레미스 환경에서 연결을 모니터링하는 클라우드 기반 네트워크 모니터링 솔루션입니다.

[ExpressRoute 모니터](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/)는 Azure ExpressRoute 회로를 통해 종단 간 연결 및 성능을 모니터링하는 NPM 기능입니다.

[DNS 분석](../log-analytics/log-analytics-dns.md)은 DNS 서버를 기반으로 보안, 성능 및 작업 관련 자세한 정보를 제공하는 솔루션입니다.

[서비스 엔드포인트 모니터링](../networking/network-monitoring-overview.md)은 응용 프로그램의 연결을 테스트하고, 온-프레미스, 이동 통신 사업자 네트워크 및 클라우드/개인 데이터 센터에서 성능 병목 상태를 검색합니다.


### <a name="service-map"></a>서비스 맵
[서비스 맵](../operations-management-suite/operations-management-suite-service-map.md)은 다른 컴퓨터의 다른 프로세스 및 종속성과 외부 프로세스를 통해 가상 머신을 분석하여 IaaS 환경을 이해할 수 있습니다. Log Analytics에서 이벤트, 성능 데이터 및 관리 솔루션을 통합합니다. 그런 다음이 각 컴퓨터의 컨텍스트 및 나머지 환경과의 관계에서 데이터를 볼 수 있습니다.

서비스 맵은 [Application Insights의 응용 프로그램 맵](../application-insights/app-insights-app-map.md)과 유사합니다. 응용 프로그램을 지원하는 인프라 구성 요소에 중점을 둡니다.


## <a name="example-scenarios"></a>예제 시나리오
다음은 다양한 시나리오에서 Azure의 여러 다른 모니터링 도구를 사용하는 방법을 보여 주는 개략적인 예제입니다.

### <a name="monitoring-a-web-application"></a>웹 응용 프로그램 모니터링
Azure App Services, Azure Storage 및 SQL Database를 통해 Azure에 배포된 웹 응용 프로그램을 고려합니다. 먼저 Azure Portal의 해당 페이지에서 이러한 리소스에 대한 [메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md) 및 [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)에 액세스합니다. 응용 프로그램에 대한 요청 수 및 평균 응답 시간과 같은 중요한 정보를 찾습니다. 또한 구성 변경 사항을 파악합니다.

다양한 리소스에 대한 메트릭 및 로그를 보려면 포털의 모니터로 이동합니다. 메트릭의 표준 매개 변수를 결정할 때 [경고 규칙을 만듭니다](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). 이러한 규칙은 예를 들어 평균 응답 시간이 임계값을 초과하여 증가하는 경우 사전에 알려줍니다. 응용 프로그램의 일별 성능을 빠르게 나타내려면 중요한 KPI를 나타내는 메트릭 그래프를 표시하는 Azure 대시보드를 만듭니다.

응용 프로그램의 심층 모니터링을 수행하려면 [Application Insights용으로 구성](../application-insights/quick-monitor-portal.md)합니다. 이제 응용 프로그램의 작동 및 성능에 대한 자세한 정보를 제공하는 추가 데이터를 수집할 수 있습니다. Application Insights는 앱 구성 요소 사이의 기본 관계를 검색합니다. [종단 간 추적](../application-insights/app-insights-transaction-diagnostics.md) 기능과 결합된 [응용 프로그램 맵](../application-insights/app-insights-app-map.md)을 통해 구현된 시각적 표현으로 문제가 발생한 정확한 구성 요소, 종속성 또는 예외를 진단할 수 있도록 합니다.

사전에 여러 다른 지역에서 응용 프로그램을 테스트하는 [가용성 테스트](../application-insights/app-insights-monitor-web-app-availability.md)를 만듭니다. 프로그램 개발자를 지원하기 위해, 특정 코드 줄을 따라 요청 및 예외를 추적할 수 있는 [Profiler를 사용하도록 설정](../application-insights/enable-profiler-compute.md)합니다. 응용 프로그램에서 사용되는 서비스에 대한 추가 가시성을 확보하려면 [SQL 분석 솔루션](../log-analytics/log-analytics-azure-sql.md)을 추가하여 Log Analytics에 추가 데이터를 수집합니다.

일정 시간이 지난 후 사이트의 성능이 임계값 아래로 떨어지는 기간 동안 근본 원인을 조사하기로 결정합니다. Log Analytics를 사용하여 쿼리를 작성합니다. Application Insights에서 수집된 사용량 및 성능 데이터와 응용 프로그램을 지원하는 Azure 리소스의 구성 및 성능 데이터 간의 상관 관계를 구성하는 데 도움이 됩니다.


### <a name="monitoring-virtual-machines"></a>가상 머신 모니터링
Azure에서는 Windows 및 Linux 가상 머신이 혼합되어 실행됩니다. Azure Monitor를 사용하여 [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) 및 [호스트 수준 메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md)을 봅니다. 게스트 운영 체제에서 메트릭을 수집할 수 있도록 [Azure 진단 확장](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension)을 가상 머신에 추가합니다. 그런 후 프로세서 사용량 및 메모리와 같은 기본 메트릭이 임계값을 초과할 때 사용자에게 사전에 알리는 [경고 규칙](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)을 만듭니다.

비즈니스 응용 프로그램을 실행하는 가상 머신에 대한 자세한 정보를 수집하려면 각 컴퓨터에서 [Log Analytics 작업 영역을 만들고 VM 확장을 사용하도록 설정](../log-analytics/log-analytics-quick-collect-azurevm.md)합니다. 응용 프로그램에 대해 [여러 다른 데이터 원본 컬렉션](../log-analytics/log-analytics-data-sources.md)을 구성하고, 일별 작업 및 성능을 보고하는 [보기를 만듭니다](../log-analytics/log-analytics-view-designer.md). 그런 후 특정 오류 이벤트가 수신될 때 알리는 [경고 규칙을 만듭니다](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md).

설치된 에이전트의 상태를 지속적으로 모니터링하려면 [에이전트 상태 관리 솔루션](../operations-management-suite/oms-solution-agenthealth.md)을 추가합니다. 응용 프로그램을 보다 자세히 이해하려면 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md)에 추가할 수 있게 가상 머신에 [종속성 에이전트를 추가](../operations-management-suite/operations-management-suite-service-map-configure.md)합니다. 서비스 맵은 중요 프로세스를 검색하고 다른 서비스와 컴퓨터 간의 연결을 식별합니다.

보고된 작동 중단 후, 서비스 맵을 통해 법정 분석을 수행하여 문제가 발생한 특정 컴퓨터를 식별합니다. 그런 다음, [Log Analytics 데이터에 대한 쿼리](../log-analytics/log-analytics-log-search-new.md)를 만들어서 나중에 문제를 식별합니다. 또한 조건이 검색되면 사전에 알려주는 경고 규칙을 만듭니다.



## <a name="next-steps"></a>다음 단계
다음에 대해 자세히 알아봅니다.

* 핵심 모니터링 메트릭 및 경고를 시작하는 [Azure Monitor](https://azure.microsoft.com/services/monitor/)
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) - App Service 웹앱에서 문제를 진단하려는 경우
* 수집한 모니터링 데이터 및 로그를 분석하기 위한 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)
