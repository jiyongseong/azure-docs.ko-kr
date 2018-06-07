---
title: Application Insights를 사용하여 Azure Service Fabric 이벤트 분석 | Microsoft Docs
description: Azure Service Fabric 클러스터의 모니터링 및 진단을 위해 Application Insights를 사용하여 이벤트를 시각화 및 분석하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: srrengar
ms.openlocfilehash: 29adf362fdacdb793af071fa6d7bd59214536374
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Application Insights를 사용하여 이벤트 분석 및 시각화

Azure Application Insights는 응용 프로그램 모니터링 및 진단을 위한 확장 가능한 플랫폼입니다. 여기에는 강력한 분석 및 쿼리 도구, 사용자 지정 가능한 대시보드 및 시각화, 자동화된 경고 등의 추가 옵션이 포함됩니다. 이는 Service Fabric 응용 프로그램 및 서비스에 대한 모니터링과 진단을 위해 권장되는 플랫폼입니다. 이 문서에서는 다음과 같은 일반적인 질문에 답변합니다.

* 응용 프로그램 및 서비스 내부에서 어떤 작업이 진행되는지 파악하고 원격 분석 데이터를 수집하려면 어떻게 해야 하나요?
* 응용 프로그램, 특히 서로 통신하는 서비스 문제를 해결하려면 어떻게 해야 하나요?
* 서비스 성능(예: 페이지 로드 시간, http 요청)에 대한 메트릭을 얻으려면 어떻게 해야 하나요?

이 문서의 목적은 App Insights 내에서 인사이트를 획득하고 문제를 해결하는 방법을 보여주는 것입니다. Service Fabric을 사용하여 AI를 설정 및 구성하는 방법을 알아보려면 [자습서](service-fabric-tutorial-monitoring-aspnet.md)를 살펴보세요.

## <a name="monitoring-in-app-insights"></a>App Insights에서 모니터링

Application Insights는 Service Fabric을 사용할 때 바로 사용 가능한 환경을 제공합니다. 개요 페이지에서, AI는 응답 시간, 처리된 요청 수 등 서비스에 대한 핵심 정보를 제공합니다. 맨 위에서 '검색' 단추를 클릭하면 응용 프로그램의 최근 요청 목록을 볼 수 있습니다. 또한 여기에 실패한 요청을 보고 어떤 오류가 발생했는지 진단할 수 있습니다.

![AI 개요](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

이전 이미지의 오른쪽 패널을 보면 목록에 두 가지 주요 유형인 요청과 이벤트가 있습니다. 이 예에서 요청은 HTTP 요청을 통해 앱의 API로 호출되고, 호출은 코드의 아무 위치에나 추가할 수 있는 원격 분석 데이터 역할을 하는 사용자 지정 이벤트입니다. [사용자 지정 이벤트 및 메트릭용 Application Insights API](../application-insights/app-insights-api-custom-events-metrics.md)에서 응용 프로그램 계측을 추가로 탐색할 수 있습니다. 요청을 클릭하면 다음 이미지처럼 AI Service Fabric nuget 패키지에서 수집되는 Service Fabric 관련 데이터를 포함하여 자세한 정보가 표시됩니다. 이 정보는 문제를 해결하고 응용 프로그램 상태를 파악하는 데 유용하며, 이 모든 정보는 Application Insights 내에서 검색할 수 있습니다.

![AI 요청 세부 정보](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights에는 들어오는 모든 데이터를 쿼리하기 위해 지정된 보기가 있습니다. 개요 페이지의 맨 위에서 "메트릭 탐색기"를 클릭하면 AI 포털로 이동됩니다. 여기서 Kusto 쿼리 언어를 사용하여 앞에서 언급한 사용자 지정 이벤트, 요청, 예외, 성능 카운터 및 기타 메트릭에 대해 쿼리를 실행할 수 있습니다. 다음 예제는 지난 1시간의 모든 요청을 보여줍니다.

![AI 요청 세부 정보](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

App Insights 포털의 기능을 좀 더 자세히 살펴보려면 [Application Insights 포털 설명서](../application-insights/app-insights-dashboards.md)로 이동하세요.

### <a name="configuring-ai-with-wad"></a>WAD를 사용하여 AI 구성

>[!NOTE]
>이는 해당 시점의 Windows 클러스터에만 적용됩니다.

WAD에서 Azure AI로 데이터를 전송하는 두 가지 기본적인 방법이 있습니다. 이 방법은 [이 문서](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)에 설명된 대로 AI 싱크를 WAD 구성에 추가하여 이루어집니다.

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Azure Portal에서 클러스터를 만들 때 AI 계측 키 추가

![AIKey 추가](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

클러스터를 만들 때 진단이 "설정"으로 설정되면 Application Insights 계측 키를 입력할 수 있는 선택 필드가 나타납니다. 여기에 AI 키를 붙여넣으면 클러스터를 배포하는 데 사용되는 Resource Manager 템플릿에서 AI 싱크가 자동으로 구성됩니다.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Resource Manager 템플릿에 AI 싱크 추가

Resource Manager 템플릿의 "WadCfg"에서 다음 두 가지 변경 사항을 포함하여 "Sink"를 추가합니다.

1. `DiagnosticMonitorConfiguration` 선언이 완료된 직후에 싱크 구성을 추가합니다.

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. `WadCfg`의 `DiagnosticMonitorConfiguration`에 다음 줄을 추가하여(`EtwProviders` 선언 직전) `DiagnosticMonitorConfiguration`에 Sink를 포함합니다.

    ```json
    "sinks": "applicationInsights"
    ```

위의 두 코드 조각에서 "applicationInsights"라는 이름은 싱크를 설명하는 데 사용되었습니다. 이는 요구 사항은 아니며 싱크의 이름이 "sinks"에 포함되어 있는 한 이름을 임의의 문자열로 설정할 수 있습니다.

현재 클러스터의 로그는 AI의 로그 뷰어에 **추적**으로 표시됩니다. 플랫폼에서 발생하는 대부분의 추적은 "정보" 수준이므로 "위험" 또는 "오류" 유형의 로그만 보내도록 싱크 구성을 변경할 수도 있습니다. 이 작업은 [이 문서](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)에서 설명한 것처럼 싱크에 "채널"을 추가하여 수행할 수 있습니다.

>[!NOTE]
>포털 또는 Resource Manager 템플릿에서 잘못된 AI 키를 사용하는 경우 수동으로 키를 변경하고 클러스터를 업데이트/재배포해야 합니다.

### <a name="configuring-ai-with-eventflow"></a>EventFlow를 사용하여 AI 구성

EventFlow를 사용하여 이벤트를 집계하는 경우 `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet 패키지를 가져와야 합니다. 다음 코드는 *eventFlowConfig.json*의 *outputs* 섹션에 필요합니다.

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

필터를 필요한 대로 변경하고 다른 입력(각각의 NuGet 패키지와 함께)도 포함해야 합니다.

## <a name="aisdk"></a>AI.SDK

EventFlow 및 WAD는 진단 및 모니터링에 좀 더 모듈 방식으로 접근할 수 있으므로 집계 솔루션으로 사용하는 것이 좋습니다. 즉, EventFlow의 출력을 변경하려는 경우 실제 계측을 변경할 필요 없이 구성 파일을 간단히 수정하기만 하면 됩니다. 그러나 Application Insights를 사용하기로 결정하고 다른 플랫폼으로 변경할 가능성이 없는 경우 이벤트를 집계하여 AI에 보내는 작업에 대해 AI의 새 SDK 사용에 대해 살펴봐야 합니다. 즉, 데이터를 AI로 보내도록 EventFlow를 구성할 필요가 없으며 대신 ApplicationInsight의 Service Fabric NuGet 패키지를 설치합니다. 패키지에 대한 자세한 내용은 [여기](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)를 참조하세요.

[Application Insights support for Microservices and Containers](https://azure.microsoft.com/blog/app-insights-microservices/)(마이크로 서비스 및 컨테이너에 대한 Application Insights 지원)에서는 작업 중인 새로운 기능 중 일부(현재 베타 버전)를 보여 주며 이를 통해 AI에서 보다 풍부한 기본 모니터링 옵션을 사용할 수 있습니다. 여기에는 종속성 추적(클러스터의 모든 서비스 및 응용 프로그램의 AppMap을 빌드하고 그 사이의 통신을 설정하는 데 사용됨) 및 서비스에서 발생한 추적의 상관 관계가 포함됩니다(앱 또는 서비스의 워크플로 문제를 정확히 발견 가능).

.NET에서 개발 중이고 Service Fabric의 프로그래밍 모델 중 일부를 사용하고 있으며 이벤트 및 로그 데이터를 시각화하고 분석하기 위한 플랫폼으로 AI를 사용하려는 경우 모니터링 및 진단 워크플로인 AI SDK 경로를 통해 이동하는 것이 좋습니다. AI를 사용하여 로그를 수집 및 표시하려면 [이 문서](../application-insights/app-insights-asp-net-more.md) 및 [이 문서](../application-insights/app-insights-asp-net-trace-logs.md)를 참조하세요.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Azure Portal에서 AI 리소스 탐색

AI를 이벤트 및 로그의 출력으로 구성했으면 몇 분 내에 AI 리소스에 정보가 표시되기 시작합니다. AI 리소스로 이동하면 AI 리소스 대시보드가 표시됩니다. AI 작업 표시줄에서 **검색**을 클릭하여 수신한 최신 추적을 보고 필터링할 수 있습니다.

*메트릭 탐색기*는 응용 프로그램, 서비스 및 클러스터가 보고할 수 있는 메트릭을 기반으로 사용자 지정 대시보드를 만드는 데 유용한 도구입니다. 수집 중인 데이터를 기반으로 직접 몇 가지 차트를 설정하려면 [Application Insights에서 메트릭 탐색](../application-insights/app-insights-metrics-explorer.md)을 참조하세요.

**분석**을 클릭하면 Application Insights 분석 포털로 이동합니다. 이 포털에서는 보다 넓은 범위 및 다양한 옵션으로 이벤트 및 추적을 쿼리할 수 있습니다. 자세한 내용은 [Application Insights의 분석](../application-insights/app-insights-analytics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [AI에 경고 설정](../application-insights/app-insights-alerts.md) - 성능 또는 사용 변경에 대한 알림 받기
* [Application Insights의 스마트 감지](../application-insights/app-insights-proactive-diagnostics.md) - 잠재적인 성능 문제를 경고하기 위해 AI에 전송되는 원격 분석에 대한 사전 분석 수행
