---
title: Azure Application Insights 원격 분석 상관 관계 | Microsoft 문서
description: Application Insights 원격 분석 상관 관계
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2018
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 12b46b4abaa17fe9dd0e9055bca5463312bbd15d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights의 원격 분석 상관 관계

마이크로 서비스의 세상에서 모든 논리 작업에는 다양한 서비스 구성 요소로 이루어지는 작업이 필요합니다. 이러한 각 구성 요소는 [Application Insights](app-insights-overview.md)에서 개별적으로 모니터링할 수 있습니다. 웹앱 구성 요소는 인증 공급자 구성 요소와 통신하여 사용자 자격 증명의 유효성을 검사하고, API 구성 요소를 사용하여 시각화할 데이터를 가져옵니다. 이에 따라 API 구성 요소는 다른 서비스의 데이터를 쿼리하고, 캐시 공급자 구성 요소를 사용하고, 이 호출에 대해 청구 구성 요소에 알릴 수 있습니다. Application Insights는 분산 원격 분석 상관 관계를 지원합니다. 이를 통해 실패하거나 성능 저하된 구성 요소를 검색할 수 있습니다.

이 문서에서는 여러 구성 요소에서 보낸 원격 분석의 상관 관계를 지정하기 위해 Application Insights에서 사용되는 데이터 모델에 대해 설명합니다. 컨텍스트 전파 기술 및 프로토콜을 다룹니다. 다양한 언어와 플랫폼에 대한 상관 관계 개념 구현도 다룹니다.

## <a name="telemetry-correlation-data-model"></a>원격 분석 상관 관계 데이터 모델

Application Insights는 분산 원격 분석 상관 관계에 대한 [데이터 모델](application-insights-data-model.md)을 정의합니다. 원격 분석을 논리 작업과 연결하기 위해 모든 원격 분석 항목에 `operation_Id`라는 컨텍스트 필드가 있습니다. 이 식별자는 분산 추적의 모든 원격 분석 항목에서 공유됩니다. 따라서 단일 계층에서 원격 분석이 손실되더라도 다른 구성 요소에서 보고한 원격 분석을 연결할 수 있습니다.

일반적으로 분산 논리 작업은 구성 요소 중 하나에서 처리되는 요청인 더 작은 작업의 집합으로 구성됩니다. 이러한 작업은 [요청 원격 분석](application-insights-data-model-request-telemetry.md)을 통해 정의됩니다. 모든 요청 원격 분석에는 전체적으로 고유하게 식별되는 자체 `id`가 있습니다. 또한 이 요청과 연결된 모든 원격 분석(추적, 예외 등)은 `operation_parentId`를 요청 `id`의 값으로 설정해야 합니다.

다른 구성 요소에 대한 HTTP 호출 같은 모든 나가는 작업은 [종속성 원격 분석](application-insights-data-model-dependency-telemetry.md)을 통해 표시됩니다. 종속성 원격 분석은 전체적으로 고유한 자체 `id`도 정의합니다. 이 종속성 호출을 통해 시작된 요청 원격 분석은 이를 `operation_parentId`로 사용합니다.

`operation_Id`, `operation_parentId` 및 `request.id`를 `dependency.id`와 함께 사용하여 분산 논리 작업의 보기를 빌드할 수 있습니다. 해당 필드는 원격 분석 호출의 인과 관계 순서를 정의합니다.

마이크로 서비스 환경에서 구성 요소의 추적은 다른 저장소로 이동할 수 있습니다. 모든 구성 요소는 Application Insights에 자체 계측 키가 있을 수 있습니다. 논리 작업에 대한 원격 분석을 가져오려면 모든 저장소에서 데이터를 쿼리해야 합니다. 저장소 수가 너무 큰 경우에는 다음 찾을 위치에 대한 힌트를 포함해야 합니다.

Application Insights 데이터 모델에서는 이 문제를 해결하기 위해 두 가지 필드, 즉 `request.source` 및 `dependency.target` 필드를 정의합니다. 첫 번째 필드는 종속성 요청을 시작한 구성 요소를 식별하고, 두 번째 필드는 종속성 호출의 응답을 반환한 구성 요소를 식별합니다.


## <a name="example"></a>예

STOCKS API라는 외부 API를 사용하여 주식의 현재 시가를 보여 주는 응용 프로그램 STOCK PRICES의 예제를 살펴보겠습니다. STOCK PRICES 응용 프로그램에는 `GET /Home/Stock`를 사용하여 클라이언트 웹 브라우저에서 연 `Stock page` 페이지가 있습니다. 응용 프로그램에서 `GET /api/stock/value` HTTP 호출을 사용하여 STOCK API를 조회합니다.

쿼리를 실행하여 결과 원격 분석을 분석할 수 있습니다.

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

결과 보기에서 모든 원격 분석 항목은 루트 `operation_Id`를 공유합니다. Ajax 호출이 페이지에서 실행된 경우 새로운 고유 ID `qJSXU`가 종속성 원격 분석에 할당되고 pageView의 ID가 `operation_ParentId`로 사용됩니다. 따라서 서버 요청은 Ajax의 ID를 `operation_ParentId`로 사용합니다.

| itemType   | 이름                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

이제 호출 `GET /api/stock/value`가 외부 서비스에 대해 실행될 경우 해당 서버의 ID를 알아야 합니다. 따라서 `dependency.target` 필드를 적절하게 설정할 수 있습니다. 외부 서비스가 모니터링을 지원하지 않을 경우 `target`은 서비스의 호스트 이름으로 설정됩니다(예: `stock-prices-api.com`). 하지만 해당 서비스가 미리 정의된 HTTP 헤더를 반환하는 방식으로 자신을 식별하면 `target`에는 Application Insights가 해당 서비스에서 원격 분석을 쿼리하여 분산 추적을 빌드하는 데 사용되는 서비스 ID가 포함됩니다. 

## <a name="correlation-headers"></a>상관 관계 헤더

현재 [correlation HTTP protocol](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md)(상관 관계 HTTP 프로토콜)에 대한 RFC 제안 작업을 진행하고 있습니다. 이 제안은 다음 두 가지 헤더를 정의합니다.

- `Request-Id`는 호출의 전체적으로 고유한 ID를 전달합니다.
- `Correlation-Context` - 분산 추적 속성의 이름-값 쌍 컬렉션을 전달합니다.

표준은 `Request-Id` 생성의 두 가지 스키마인 플랫 및 계층 구조를 정의합니다. 플랫 스키마의 경우 `Correlation-Context` 컬렉션에 대한 잘 알려진 `Id` 키가 있습니다.

Application Insights에서는 상관 관계 HTTP 프로토콜에 대한 [extension](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)(확장)을 정의합니다. `Request-Context` 이름-값 쌍을 사용하여 즉각적인 호출자 또는 호출 수신자에서 사용된 속성 컬렉션을 전파합니다. Application Insights SDK는 이 헤더를 사용하여 `dependency.target` 및 `request.source` 필드를 설정합니다.

## <a name="open-tracing-and-application-insights"></a>Open Tracing 및 Application Insights

[Open Tracing](http://opentracing.io/) 및 Application Insights 데이터 모델은 다음과 같이 표시됩니다. 

- `request`, `pageView`가 **Span**으로 매핑되고 `span.kind = server`입니다.
- `dependency`가 **Span**을 매핑되고 `span.kind = client`입니다.
- `request` 및 `dependency`의 `id`가 **Span.Id**로 매핑됩니다.
- `operation_Id`가 **TraceId**로 매핑됩니다.
- `operation_ParentId`가 `ChildOf` 형식의 **Reference**로 매핑됩니다.

Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](application-insights-data-model.md)을 참조하세요.

Open Tracing 개념의 정의는 [specification](https://github.com/opentracing/specification/blob/master/specification.md)(사양) 및 [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)를 참조하세요.


## <a name="telemetry-correlation-in-net"></a>.NET의 원격 분석 상관 관계

시간이 지나면서 .NET에서는 원격 분석과 진단 로그를 상호 연결하는 수많은 방법을 정의했습니다. [LogicalOperationStack and ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)(LogicalOperationStack 및 ActivityId)를 추적할 수 있는 `System.Diagnostics.CorrelationManager`가 있습니다. `System.Diagnostics.Tracing.EventSource` 및 Windows ETW는 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) 메서드를 정의합니다. `ILogger`는 [Log Scopes](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)(로그 범위)를 사용합니다. WCF 및 HTTP는 "현재" 컨텍스트 전파를 연결합니다.

하지만 해당 메서드는 자동 분산 추적 지원을 사용하지 않았습니다. `DiagnosticsSource`는 자동 컴퓨터 간 상관 관계를 지원하는 한 가지 방법입니다. .NET 라이브러리는 Diagnostics Source를 지원하고 HTTP 같은 전송을 통해 상관 관계 컨텍스트의 자동 컴퓨터 간 전파를 허용합니다.

Diagnostics Source의 [guide to Activities](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)(활동 가이드)에서는 활동 추적의 기본 사항을 설명합니다. 

ASP.NET Core 2.0에서는 HTTP 헤더 추출 및 새 활동 시작을 지원합니다. 

`System.Net.HttpClient` 시작 버전 `4.1.0`에서는 상관 관계 Http 헤더의 자동 삽입 및 http 호출을 활동으로 추적하는 기능을 지원합니다.

ASP.NET 클래식에 대한 새로운 HTTP 모듈 [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)이 있습니다. 이 모듈은 DiagnosticsSource를 사용하여 원격 분석 상관 관계를 구현합니다. 들어오는 요청 헤더를 기반으로 활동을 시작합니다. 또한 서로 다른 요청 처리 단계의 원격 분석을 상호 연결합니다. IIS 처리의 모든 단계가 서로 다른 관리 스레드에서 실행되는 경우에도 마찬가지입니다.

Application Insights SDK 시작 버전 `2.4.0-beta1`에서는 DiagnosticsSource 및 활동을 사용하여 원격 분석을 수집하고 이를 현재 활동과 연결합니다. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK의 원격 분석 상관 관계
[Application Insights Java SDK](app-insights-java-get-started.md)는 버전 `2.0.0`부터 원격 분석의 자동 상관 관계를 지원합니다. 요청 범위 내에서 실행된 모든 원격 분석(추적, 예외, 사용자 지정 이벤트 등)에 대한 `operation_id`를 자동으로 채웁니다. 또한 [Java SDK 에이전트](app-insights-java-agent.md)가 구성된 경우, HTTP를 통한 서비스 간 호출에 대한 상관 관계 헤더(위에 설명됨) 전파도 관리합니다. 참고: Apache HTTP 클라이언트를 통해 수행되는 호출에 대해서만 상관 관계 기능이 지원됩니다. Spring Rest Template 또는 Feign을 사용하는 경우, 내부적으로 둘 다 Apache HTTP 클라이언트에서 사용할 수 있습니다.

현재, 메시징 기술(예:: Kafka, RabbitMQ, Azure Service Bus)에서는 자동 컨텍스트 전파가 지원되지 않습니다. 그러나 `trackDependency` 및 `trackRequest` API를 사용하여 이러한 시나리오를 수동으로 코딩할 수 있습니다. 여기서 종속성 원격 분석은 생산자에 의해 큐에 추가되는 메시지를 나타내고 요청은 소비자에 의해 처리되는 메시지를 나타냅니다. 이 경우 `operation_id` 및 `operation_parentId` 둘 다 메시지의 속성에 전파되어야 합니다.

<a name="java-role-name"></a>
### <a name="role-name"></a>역할 이름
경우에 따라, [응용 프로그램 맵](app-insights-app-map.md)에 구성 요소 이름에 표시되는 방식을 사용자 지정하려고 할 수 있습니다. 이렇게 하려면 다음 중 하나를 수행하여 `cloud_roleName`을 수동으로 설정할 수 있습니다.

원격 분석 이니셜라이저를 통해(모든 원격 분석 항목에 태그 지정)
```Java
public class CloudRoleNameInitializer extends WebTelemetryInitializerBase {

    @Override
    protected void onInitializeTelemetry(Telemetry telemetry) {
        telemetry.getContext().getTags().put(ContextTagKeys.getKeys().getDeviceRoleName(), "My Component Name");
    }
  }
```
[장치 컨텍스트 클래스](https://docs.microsoft.com/et-ee/java/api/com.microsoft.applicationinsights.extensibility.context._device_context)를 통해(이 원격 분석 항목에만 태그 지정)
```Java
telemetry.getContext().getDevice().setRoleName("My Component Name");
```

## <a name="next-steps"></a>다음 단계

- [사용자 지정 원격 분석을 작성합니다](app-insights-api-custom-events-metrics.md).
- Application Insights에서 마이크로 서비스의 모든 구성 요소를 등록합니다. [지원되는 플랫폼](app-insights-platforms.md)을 확인합니다.
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](application-insights-data-model.md)을 참조하세요.
- [원격 분석을 확장 및 필터링](app-insights-api-filtering-sampling.md)하는 방법을 알아봅니다.
