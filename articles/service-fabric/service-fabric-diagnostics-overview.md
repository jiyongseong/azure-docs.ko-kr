---
title: Azure Service Fabric 모니터링 및 진단 개요 | Microsoft Docs
description: Azure Service Fabric 클러스터, 응용 프로그램 및 서비스에 대한 모니터링 및 진단에 대해 알아봅니다.
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
ms.date: 04/25/2018
ms.author: srrengar
ms.openlocfilehash: 4fcf8c514cb785dbb0a149e5b3073fc72937b68a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric 모니터링 및 진단

이 문서는 Azure Service Fabric의 모니터링 및 진단에 대한 개요를 제공합니다. 모니터링 및 진단은 모든 클라우드 환경에서 워크로드를 개발, 테스트 및 배포하는 데 중요합니다. 모니터링을 사용하면 응용 프로그램 사용 현황, 리소스 사용률 및 전반적인 클러스터 상태를 추적할 수 있습니다. 이 정보를 사용하여 문제를 진단하고 수정할 수 있으며 나중에 문제가 발생하지 않도록 방지할 수 있습니다. 

## <a name="application-monitoring"></a>응용 프로그램 모니터링
응용 프로그램 모니터링은 응용 프로그램의 기능 및 구성 요소가 사용되는 방식을 추적합니다. 응용 프로그램을 모니터링하여 사용자에게 영향을 주는 문제가 발견되었는지 확인하려고 합니다. 응용 프로그램 모니터링은 다음 시나리오에서 유용할 수 있습니다.
* 응용 프로그램 부하 및 사용자 트래픽 확인 - 사용자 수요를 충족하거나 응용 프로그램의 잠재적 병목 상태를 해결하기 위해 서비스를 확장해야 하나요?
* 클러스터에서 서비스 통신 및 원격 문제 식별
* 사용자가 응용 프로그램에서 수행 중인 작업 확인 - 응용 프로그램에서 원격 분석 데이터를 수집하면 이후의 기능 개발과 앱 오류에 대한 진단 향상에 도움이 될 수 있습니다.
* 실행 중인 컨테이너 내부 상황 모니터링

Service Fabric은 적절한 추적 및 원격 분석을 통해 응용 프로그램 코드를 계측할 수 있는 많은 옵션을 지원합니다. AI(Application Insights)를 사용하는 것이 좋습니다. AI와 서비스 패브릭의 통합에는 Visual Studio 및 Azure Portal에 대한 도구 경험뿐만 아니라, 기본 제공되는 포괄적인 로깅 환경을 제공하는 Service Fabric 관련 메트릭이 포함됩니다. AI를 통해 많은 로그가 자동으로 생성되고 수집되지만 응용 프로그램에 사용자 지정 로깅을 추가하여 보다 풍부한 진단 환경을 만드는 것이 좋습니다. Service Fabric과 함께 Application Insights를 시작하는 방법에 대한 자세한 내용은 [Application Insights를 사용한 이벤트 분석](service-fabric-diagnostics-event-analysis-appinsights.md)을 참조하세요.

## <a name="platform-cluster-monitoring"></a>플랫폼(클러스터) 모니터링
Service Fabric 클러스터 모니터링은 플랫폼 및 모든 워크로드가 의도한 대로 실행되고 있는지 확인하는 데 중요합니다. Service Fabric의 목표 중 하나는 응용 프로그램을 하드웨어 오류에 대해 복원력 있게 유지하는 것입니다. 이 목표는 플랫폼의 시스템 서비스가 인프라 문제를 감지하고 워크로드를 클러스터의 다른 노드로 빠르게 장애 조치(failover)할 수 있기 때문에 실현이 가능합니다. 그러나 이러한 특정 상황에서 시스템 서비스 자체에 문제가 있다면 어떻게 될까요? 또는 워크로드를 이동할 때 서비스 배치 규칙에 위배되면 어떻게 될까요? 클러스터를 모니터링하면 클러스터에서 발생하는 활동에 대한 정보가 계속 제공되므로 효과적으로 문제를 진단하고 해결하는 데 도움이 됩니다. 확인해야 할 몇 가지 주요 사항은 다음과 같습니다.
* 응용 프로그램 배치 및 클러스터의 작업 부하 분산 측면에서 Service Fabric이 예상대로 동작하고 있나요? 
* 클러스터에서 수행한 사용자 작업이 예상대로 승인되고 실행되나요? 이는 클러스터를 확장할 때 특히 관련이 있습니다.
* Service Fabric이 사용자 데이터와 클러스터 내부의 서비스-서비스 통신을 올바르게 처리하고 있나요?

Service Fabric은 구입 즉시 포괄적인 이벤트 집합을 제공합니다. 이러한 [Service Fabric 이벤트](service-fabric-diagnostics-events.md)는 EventStore API 또는 작동 채널(플랫폼에서 노출하는 이벤트 채널)을 통해 액세스할 수 있습니다. 
* EventStore - EventStore(Windows 버전 6.2 이상에서 사용 가능하고, 이 문서가 마지막으로 업데이트된 날짜를 기준으로 Linux는 아직 진행 중)는 API(REST 엔드포인트 또는 클라이언트 라이브러리를 통해 액세스 가능) 집합을 통해 이러한 이벤트를 노출합니다. [EventStore 개요](service-fabric-diagnostics-eventstore.md)에서 EventStore에 대해 자세히 읽어보세요.
* Service Fabric 이벤트 채널 - Windows에서 Service Fabric 이벤트는 작동 채널과 데이터 및 메시지 채널 사이에서 선택하는 데 사용되는 관련 `logLevelKeywordFilters` 집합을 사용하여 단일 ETW 공급자가 제공합니다. 이러한 방식으로 나가는 Service Fabric 이벤트를 격리하여 필요에 따라 필터링합니다. Linux에서는 Service Fabric 이벤트가 LTTng를 통해 수신되어 단일 Storage 테이블에 배치되며, 필요에 따라 어디서나 필터링할 수 있습니다. 이러한 채널에는 클러스터 상태 이해에 도움이 되는 구조화된 조정 이벤트가 포함되어 있습니다. 진단은 클러스터 생성 시 기본적으로 사용이 가능하며, 나중에 쿼리할 수 있도록 이러한 채널의 이벤트가 전송되는 Azure Storage 테이블이 만들어집니다. 

신속한 분석을 EventStore를 사용하여 클러스터가 어떤 방식으로 작동하는지, 동작이 예상대로 발생하는지 파악하는 것이 좋습니다. 클러스터에서 생성되는 로그 및 이벤트 수집에는 일반적으로 [Azure 진단 확장](service-fabric-diagnostics-event-aggregation-wad.md)을 사용하는 것이 좋습니다. OMS Log Analytics의 Service Fabric 전용 솔루션인 Service Fabric 분석과 통합되어 Service Fabric 클러스터 모니터링을 위한 사용자 지정 대시보드를 제공하며 클러스터 이벤트를 쿼리하고 경고를 설정할 수 있습니다. 자세한 내용은 [OMS를 사용한 이벤트 분석](service-fabric-diagnostics-event-analysis-oms.md)을 참조하세요. 

 클러스터 모니터링에 대한 자세한 내용은 [플랫폼 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-infra.md)을 참조하세요.


 ![OMS SF 솔루션](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

## <a name="performance-monitoring"></a>성능 모니터링
기본 인프라 모니터링은 클러스터 상태 및 리소스 사용률을 이해하는 데 중요한 부분입니다. 시스템 성능 측정은 여러 요인에 따라 달라지며, 각 요인은 일반적으로 KPI(핵심 성과 지표)를 통해 측정됩니다. 클러스터의 노드에서 성능 카운터로 수집할 수 있는 메트릭에 Service Fabric 관련 KPI를 매핑할 수 있습니다.
이 KPI는 다음 측면에서 유용할 수 있습니다.
* 리소스 사용률 및 부하 이해 - 클러스터를 확장하거나 서비스 프로세스를 최적화하는 데 사용됩니다.
* 인프라 문제 예측 - 문제에 앞서 성능이 갑자기 변경(저하)되는 경우가 많으므로 네트워크 I/O, CPU 사용률 등의 KPI를 사용하여 인프라 문제를 예측하고 진단할 수 있습니다.

인프라 수준에서 수집해야 하는 성능 카운터 목록은 [성능 메트릭](service-fabric-diagnostics-event-generation-perf.md)에서 확인할 수 있습니다. 

Service Fabric은 Reliable Services 및 행위자 프로그래밍 모델에 대한 일련의 성능 카운터를 제공합니다. 두 모델 중 하나를 사용하는 경우, 이러한 성능 카운터는 행위자가 올바르게 스핀 업/스핀 다운하고 있는지 또는 Reliable Services 요청이 빠르게 처리되고 있는지 확인하는 데 유용한 KPI를 제공할 수 있습니다. 자세한 내용은 [Reliable Service Remoting 모니터링](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) 및 [Reliable Actors 성능 모니터링](service-fabric-reliable-actors-diagnostics.md#performance-counters)을 참조하세요. 이외에도 Application Insights에는 응용 프로그램에서 구성된 경우 수집되는 일련의 성능 메트릭이 있습니다.

[OMS 에이전트](service-fabric-diagnostics-oms-agent.md)를 사용하여 적절한 성능 카운터를 수집하고 OMS Log Analytics에서 이러한 KPI를 볼 수 있습니다.

![진단 개요 차트](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>상태 모니터링
Service Fabric 플랫폼에는 클러스터의 엔터티 상태에 대한 확장 가능한 상태 보고를 제공하는 상태 모델이 포함되어 있습니다. 각 노드, 응용 프로그램, 서비스, 파티션, 복제본 또는 인스턴스에는 지속적으로 업데이트 가능한 상태가 있습니다. 시스템 상태는 “정상”, “경고” 또는 “오류”일 수 있습니다. 클러스터의 문제에 따라 각 엔터티에 대해 내보내지는 상태 보고서를 통해 상태가 변경됩니다. 엔터티 상태는 아래와 같이 SFX(Service Fabric Explorer)에서 언제든지 확인하거나 플랫폼의 상태 API를 통해 쿼리할 수 있습니다. 자체 상태 보고서를 추가하거나 상태 API를 사용하여 상태 보고서를 사용자 지정하고 엔터티 상태를 수정할 수도 있습니다. 상태 모델에 대한 자세한 내용은 [Service Fabric 상태 모니터링 소개](service-fabric-health-introduction.md)를 참조하세요.

![SFX 상태 대시보드](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

SFX에서 최신 상태 보고서를 볼 수 있는 것은 물론, 각 보고서가 이벤트로도 제공됩니다. 운영 채널을 통해 상태 이벤트를 수집([Azure 진단을 사용한 이벤트 집계](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) 참조)하고 나중에 경고 및 쿼리하기 위해 Log Analytics에 저장할 수 있습니다. 이는 응용 프로그램 가용성에 영향을 줄 수 있는 문제 감지에 도움이 되므로 적절한 오류 시나리오에 대한 경고(Log Analytics를 통한 사용자 지정 경고)를 설정하는 것이 좋습니다.

## <a name="other-logging-solutions"></a>다른 로깅 솔루션

권장되는 두 가지 솔루션인 [OMS](service-fabric-diagnostics-event-analysis-oms.md)와 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)는 Service Fabric과 통합되어 있지만 많은 이벤트가 etw 공급자를 통해 작성되고 다른 로깅 솔루션으로 확장될 수 있습니다. [Elastic Stack](https://www.elastic.co/products)(특히 오프라인 환경에서 클러스터를 실행하려는 경우), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/) 또는 기타 원하는 플랫폼도 살펴봐야 합니다. 

플랫폼을 선택할 때는 사용자 인터페이스 및 쿼리 옵션에 대한 친숙도, 데이터를 시각화하고 쉽게 읽을 수 있는 대시보드를 만드는 기능 및 자동 경고와 같이 모니터링을 향상시키기 위해 제공하는 추가 도구를 고려해야 합니다.

## <a name="next-steps"></a>다음 단계

* 응용 프로그램 계측을 시작하려면 [응용 프로그램 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-app.md)을 참조하세요.
* Service Fabric이 제공하는 플랫폼 및 이벤트 모니터링에 대한 자세한 내용은 [플랫폼 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-infra.md)을 참조하세요.
* [Service Fabric에서 ASP.NET Core 응용 프로그램 모니터링 및 진단](service-fabric-tutorial-monitoring-aspnet.md)에서 응용 프로그램용 AI를 설정하는 단계를 진행합니다.
* 모니터링 컨테이너에 OMS Log Analytics를 설정하는 방법을 알아봅니다. [Azure Service Fabric의 Windows 컨테이너에 대한 모니터링 및 진단](service-fabric-tutorial-monitoring-wincontainers.md)
* Azure 리소스에 대한 일반적인 모니터링 권장 사항을 알아봅니다. [모범 사례 - 모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) 
