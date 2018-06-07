---
title: Azure Monitor의 새로운 경고 환경 탐색 | Microsoft Docs
description: Azure에서 새로운 단순하고 확장 가능한 경고 환경으로 경고 작성, 보기 및 관리를 더 쉽게 만드는 방법 이해
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: c3622b4699ef532f204231c76aa3436be3676763
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Azure Monitor의 새로운 경고 환경

## <a name="overview"></a>개요

> [!NOTE]
> 이 문서에서는 최신 경고를 설명합니다. 이전 클래식 Azure Monitor 경고는 [클래식 경고 개요](monitoring-overview-alerts.md)에 설명되어 있습니다. 
>
>

새로운 경고 환경이 도입되었습니다. 이전 경고 환경은 이제 경고(클래식) 탭 아래에 있습니다. 새로운 경고 환경은 경고(클래식) 환경에 비해 다음과 같은 이점이 있습니다.

-   **향상된 알림 시스템**: 모든 최신 경고는 여러 경고에서 다시 사용할 수 있는 알림 및 작업으로 명명된 그룹인 [작업 그룹]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)을 사용합니다.  클래식 메트릭 경고 및 이전 Log Analytics 경고는 작업 그룹을 사용하지 않습니다. 
- **통합 작성 환경** - Azure Monitor, Log Analytics, Application Insights 전체의 메트릭, 로그 및 활동 로그에 대한 모든 경고가 한곳에서 생성됩니다. 
- **Azure Portal에서 발생한 Log Analytics 경고 보기** - 이제 구독에서 발생한 Log Analytics 경고를 볼 수도 있습니다. 이전에는 별도의 포털에 있었습니다. 
- **발생한 경고 및 경고 규칙의 분리** - 경고 규칙(경고를 트리거하는 조건의 정의)과 발생한 경고(경고 규칙 발생의 인스턴스)가 구별되므로 운영 및 구성 보기가 구분됩니다.
- **향상된 워크플로** - 새로운 경고 작성 환경은 알림을 받을 항목을 더 간단하게 검색할 수 있는 경고 규칙 구성 프로세스를 사용자에게 안내합니다.
 
최신 메트릭 경고에는 구체적으로 다음과 같은 개선 사항이 있습니다.
-   **개선된 대기 시간**: 최신 메트릭 경고는 1분 간격으로 자주 실행할 수 있습니다. 이전 메트릭 경고는 항상 5분 주기로 실행됩니다. 로그를 수집 하는 데 걸리는 시간으로 인해 경고 로그는 1분 이상 지연됩니다. 
-   **다차원 메트릭에 대한 지원**: 메트릭의 흥미로운 세그먼트를 모니터링할 수 있도록 차원이 포함된 메트릭에서 경고를 수행할 수 있습니다.
-   **메트릭 조건 세부 제어**: 경고 규칙을 다양하게 정의할 수 있습니다. 최신 경고는 메트릭의 최대, 최소, 평균 및 전체 값을 모니터링하도록 지원합니다.
-   **여러 가지 메트릭의 결합 모니터링**: 단일 규칙을 사용하여 다중 메트릭(현재 최대 2개)을 모니터링할 수 있습니다. 두 메트릭이 지정된 기간 동안 해당 임계값을 위반하면 경고가 트리거됩니다.
-   **로그의 메트릭**(제한된 공용 미리 보기): 이제 Log Analytics로 이동하는 일부 로그 데이터를 추출하고 Azure Monitor 메트릭으로 변환한 다음, 다른 메트릭과 마찬가지로 경고할 수 있습니다. 



다음 섹션에서는 새로운 환경이 작동하는 방법을 더 자세히 설명합니다.

## <a name="alert-rules-terminology"></a>경고 규칙 용어
새로운 경고 환경은 여러 경고 유형의 작성 환경을 통합하는 동시에 다음 개념을 사용하여 경고 규칙 및 발생한 경고 개체를 구별합니다.

- **대상 리소스** - 대상은 Azure 리소스일 수 있습니다. 대상 리소스는 경고에 사용할 수 있는 범위 및 신호를 정의합니다. 예제 대상: 가상 머신, 저장소 계정, 가상 머신 확장 집합, Log Analytics 작업 영역 또는 Application Insights 리소스

- **조건** - 조건은 대상 리소스에 적용된 신호와 논리의 조합입니다. 예: CPU의 백분율 > 70%, 서버 응답 시간 > 4ms, 로그 쿼리의 결과 개수 > 100 등 

- **신호** - 신호는 대상 리소스에 의해 발생되며 여러 유형일 수 있습니다. 지원되는 신호 유형은 **메트릭**, **활동 로그**, **Application Insights** 및 **로그**입니다.

- **논리** - 신호가 예상된 범위/값 내에 있는지 확인하는 사용자 정의 논리입니다.  
 
- **작업** - 경고가 발생할 때 수행되는 특정 작업입니다. 예를 들어 메일 주소로 메일로 보내거나 웹후크 URL을 호출합니다. 경고가 발생할 때 여러 작업을 수행할 수도 있습니다. 이러한 경고는 작업 그룹을 지원합니다.  
 
- **경고 규칙** - 경고를 트리거하는 조건입니다. 경고 규칙은 경고의 대상 및 조건을 캡처합니다. 경고 규칙은 사용 또는 사용 안 함 상태에 있을 수 있습니다.
 
    > [!NOTE]
    > 이와 달리, 경고(클래식) 환경에서는 경고가 규칙 및 발생한 경고를 둘 다 나타내므로 경고, 활성 또는 사용 안 함 상태 중 하나일 수 있습니다.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>경고를 보고 관리하는 단일 위치
경고 환경의 목표는 모든 Azure 경고를 보고 관리하는 단일 위치가 되는 것입니다. 다음 하위 섹션은 새 환경의 각 개별 화면의 기능을 설명합니다.

### <a name="alerts-overview-page"></a>경고 개요 페이지
**모니터 - 경고** 개요 페이지는 발생한 모든 경고의 집계 요약 및 총 구성/설정 경고 규칙을 표시합니다. 또한 발생한 모든 경고의 목록을 보여 줍니다. 구독 또는 필터 매개 변수를 변경하면 집계 및 발생한 경고 목록을 업데이트합니다.

> [!NOTE]
> 경고에 표시된 발생한 경고는 지원되는 메트릭 및 활동 로그 경고로 제한됩니다. Azure Monitor 개요는 이전 Azure Alerts의 경고를 포함하여 발생한 경고 개수를 보여 줍니다.

 ![alerts-overview](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>경고 규칙 관리
**모니터 - 경고>규칙**은 Azure 구독에서 모든 경고 규칙을 관리하는 단일 페이지입니다. 모든 경고 규칙(활성화 또는 비활성화)을 나열하고 대상 리소스, 리소스 그룹, 규칙 이름 또는 상태에 따라 정렬될 수 있습니다. 또한 경고 규칙을 비활성화/활성화하거나 이 페이지에서 편집할 수 있습니다.  

 ![alerts-rules](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>모든 모니터링 소스에서 하나의 경고 제작 환경
새로운 경고 환경에서 경고는 모니터링 서비스 또는 신호 형식에 관계없이 일관된 방식으로 작성될 수 있습니다. 발생한 모든 경고 및 관련된 자세한 정보는 단일 페이지에서 찾을 수 있습니다.  
 
경고 작성은 3단계 작업입니다. 사용자는 먼저 경고에 대산 대상을 선택하고 올바른 신호를 선택한 다음 경고 규칙의 일부로 신호에 적용할 논리를 지정합니다. 이 간소화된 제작 프로세스로 사용자는 Azure 리소스를 선택하기 전에 모니터링 원본 또는 지원되는 신호를 더 이상 알 필요가 없습니다. 일반적인 제작 환경은 선택한 대상 리소스에 따라 사용 가능한 신호의 목록을 자동으로 필터링하고 경고 논리의 생성을 안내합니다.

[여기](monitor-alerts-unified-usage.md)에서 다음 경고 유형을 만드는 방법에 대해 자세히 알아볼 수 있습니다.
- 메트릭 경고
- 로그 경고(Log Analytics)
- 로그 경고(활동 로그)
- 로그 경고(Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>새 환경에서 지원되는 경고
경고는 여러 Azure 모니터링 서비스전체 에서 제공됩니다. 이러한 서비스 사용 방법 및 시기에 대한 내용은 [이 문서를 참조하세요](./monitoring-overview.md). Azure에서 사용 가능한 경고 유형의 분석 및 새로운 경고 환경에서 현재 지원되는 것은 다음과 같습니다. 


| **신호 유형** | **모니터 원본** | **설명** | 
|-------------|----------------|-------------|
| 메트릭 | Azure Monitor | [근 실시간 메트릭 경고](monitoring-near-real-time-metric-alerts.md)라고도 하는 메트릭 경고는 1분의 주기로 메트릭 조건 평가를 지원하고 다중 메트릭 및 다차원 메트릭 규칙을 허용합니다. 지원되는 리소스 종류의 목록은 [여기](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)에서 사용 가능합니다. |
| 메트릭 | Azure Monitor | [이전 클래식 메트릭 경고](monitoring-overview-alerts.md)는 새로운 경고 환경에서 지원되지 않습니다. Azure Portal의 경고(클래식) 아래에서 찾을 수 있습니다. 클래식 경고는 최신 경고로 아직 이동되지 않은 일부 메트릭 유형도 지원합니다. 전체 목록은 [지원되는 메트릭](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics)을 참조하세요.
| 로그  | Log Analytics | 메트릭 및/또는 이벤트 데이터에 대한 로그 검색 쿼리가 특정 기준에 부합하면 알림을 받거나 자동화된 작업을 실행합니다. 이전 Log Analytics 경고는 여전히 사용할 수 있지만 [새로운 환경으로 복사되고](monitoring-alerts-extend.md) 있습니다. 또한 *메트릭으로 Log Analytics 로그*의 [미리 보기](monitoring-alerts-extend-tool.md)를 사용할 수 있습니다. 미리 보기를 통해 일부 유형의 로그를 사용하고 메트릭으로 변환할 수 있으며 여기에서 새로운 경고 환경을 사용하여 경고할 수 있습니다. 미리 보기는 기본 Azure Monitor 메트릭과 함께 가져오려는 비 Azure 로그가 있는 경우에 유용합니다. |
| 활동 로그 | 활동 로그(일반) | 선택한 대상(리소스/리소스 그룹/구독)을 통해 수행된 모든 Create, Update 및 Delete 작업의 레코드를 포함합니다. |
| 활동 로그  | 서비스 상태 | 새 경고 환경에서 지원되지 않습니다. [서비스 알림에 대한 활동 로그 경고 만들기](monitoring-activity-log-alerts-on-service-notifications.md)를 참조하세요.  |
| 로그  | Application Insights | 응용 프로그램의 성능 세부 정보가 있는 로그를 포함합니다. 분석 쿼리를 사용하여 응용 프로그램 데이터를 기반으로 수행될 작업에 대한 조건을 정의할 수 있습니다. |
| 메트릭 | Application Insights | 새 경고 환경에서 지원되지 않습니다. [메트릭 경고](../application-insights/app-insights-alerts.md)를 참조하세요. |
| 웹 가용성 테스트 | Application Insights | 경고 환경에서 지원되지 않습니다.  [웹 테스트 경고](../application-insights/app-insights-monitor-web-app-availability.md)를 참조하세요. Application Insights에 데이터를 보내도록 계측되는 모든 웹 사이트에 사용할 수 있습니다. 웹 사이트의 가용성이나 응답성이 기대 이하이면 알림을 받습니다. |




## <a name="next-steps"></a>다음 단계
- [새로운 경고 환경을 사용하여 경고를 만들고, 보고, 관리하는 방법에 대한 자세한 정보](monitor-alerts-unified-usage.md)
- [경고 환경의 로그 경고에 대한 자세한 정보](monitor-alerts-unified-log.md)
- [경고 환경의 메트릭 경고에 대한 자세한 정보](monitoring-near-real-time-metric-alerts.md)
- [경고 환경의 활동 로그 경고에 대한 자세한 정보](monitoring-activity-log-alerts-new-experience.md)
