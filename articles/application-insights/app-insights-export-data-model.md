---
title: "Azure Application Insights 데이터 모델 | Microsoft Docs"
description: "JSON의 연속 내보내기에서 내보내고 필터로 사용하는 속성을 설명합니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: 86aef6ba93224bbbb41bc7e651aaeec394fd8718
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-export-data-model"></a>Application Insights 데이터 모델 내보내기
이 테이블은 [Application Insights](app-insights-overview.md) SDK에서 포털로 전송된 원격 분석의 속성을 나열합니다.
이러한 속성이 [연속 내보내기](app-insights-export-telemetry.md)에서 데이터 출력에 표시됩니다
또한 [메트릭 탐색기](app-insights-metrics-explorer.md) 및 [진단 검색](app-insights-diagnostic-search.md)의 속성 필터에 나타납니다.

주의할 사항:

* `[0]` 은 인덱스를 삽입해야 하는 경로의 지점을 나타내지만 항상 0은 아닙니다.
* 기간의 단위는 10분의 1 마이크로초이므로 10000000은 1초입니다.
* 날짜와 시간은 UTC이며 ISO 형식 `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>예제
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Context
모든 유형의 원격 분석에는 컨텍스트 섹션이 함께 제공됩니다. 이러한 모든 필드가 모든 데이터 요소와 함께 전송되는 것은 아닙니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| context.custom.dimensions [0] |object [ ] |사용자 지정 속성 매개 변수에 의해 설정되는 키-값 문자열 쌍입니다. 키 최대 길이가 100이고, 값 최대 길이가 1024입니다. 100개 이상의 고유 값, 속성을 검색할 수 있지만 구분에 사용할 수 없습니다. ikey당 최대 키는 200개입니다. |
| context.custom.metrics [0] |object [ ] |사용자 지정 측정 매개 변수 및 TrackMetrics에 의해 설정된 키-값 쌍입니다. 키 최대 길이가 100이고, 값은 숫자가 될 수 있습니다. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |부울 |요청이 봇 또는 웹 테스트에서 들어오는 것 같습니다. |
| context.data.samplingRate |number |포털에 전송되는 SDK에 의해 생성된 원격 분석의 비율입니다. 범위는 0.0-100.0입니다. |
| context.device |object |클라이언트 장치 |
| context.device.browser |string |IE, Chrome, ... |
| context.device.browserVersion |string |Chrome 48.0, ... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| context.device.locale |string |en-GB, de-DE, ... |
| context.device.network |string | |
| context.device.oemName |string | |
| context.device.osVersion |string |호스트 OS |
| context.device.roleInstance |string |서버 호스트의 ID |
| context.device.roleName |string | |
| context.device.type |string |PC, 브라우저... |
| context.location |object |clientip에서 파생됩니다. |
| context.location.city |string |알 수 있는 경우 clientip에서 파생됩니다. |
| context.location.clientip |string |마지막 팔각형이 0으로 익명 처리됩니다. |
| context.location.continent |string | |
| context.location.country |string | |
| context.location.province |string |시/도 |
| context.operation.id |string |작업 ID가 동일한 항목은 포털에서 관련 항목으로 표시됩니다. 일반적으로 요청 ID입니다. |
| context.operation.name |string |URL 또는 요청 이름 |
| context.operation.parentId |string |중첩된 관련 항목을 허용합니다. |
| context.session.id |string |동일한 소스의 작업 그룹 ID입니다. 30분 동안 작업이 없으면 세션이 끝난 것입니다. |
| context.session.isFirst |부울 | |
| context.user.accountAcquisitionDate |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[인증된 사용자](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |부울 | |
| internal.data.documentVersion |string | |
| internal.data.id |string | |

## <a name="events"></a>이벤트
[TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent)에 의해 생성된 사용자 지정 이벤트입니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| event [0] count |정수 |100/([샘플링](app-insights-sampling.md) 속도) 예: 4 =&gt; 25%. |
| event [0] name |string |이벤트 이름입니다.  최대 길이 250 |
| event [0] url |string | |
| event [0] urlData.base |string | |
| event [0] urlData.host |string | |

## <a name="exceptions"></a>예외
서버 및 브라우저의 [예외](app-insights-asp-net-exceptions.md) 를 보고합니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| basicException [0] assembly |string | |
| basicException [0] count |정수 |100/([샘플링](app-insights-sampling.md) 속도) 예: 4 =&gt; 25%. |
| basicException [0] exceptionGroup |string | |
| basicException [0] exceptionType |string | |
| basicException [0] failedUserCodeMethod |string | |
| basicException [0] failedUserCodeAssembly |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |부울 | |
| basicException [0] id |string | |
| basicException [0] method |string | |
| basicException [0] message |string |예외 메시지입니다. 최대 길이 10000 |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| basicException [0] outerExceptionType |string | |
| basicException [0] outerId |string | |
| basicException [0] parsedStack [0] assembly |string | |
| basicException [0] parsedStack [0] fileName |string | |
| basicException [0] parsedStack [0] level |정수 | |
| basicException [0] parsedStack [0] line |정수 | |
| basicException [0] parsedStack [0] method |string | |
| basicException [0] stack |string |최대 길이 10000 |
| basicException [0] typeName |string | |

## <a name="trace-messages"></a>추적 메시지
[TrackTrace](app-insights-api-custom-events-metrics.md#tracktrace) 및 [로깅 어댑터](app-insights-asp-net-trace-logs.md)에서 전송합니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| message [0] loggerName |string | |
| message [0] parameters |string | |
| message [0] raw |string |로그 메시지입니다(최대 길이 10k). |
| message [0] severityLevel |string | |

## <a name="remote-dependency"></a>원격 종속성
TrackDependency에서 전송합니다. 서버의 [종속성에 대한 호출](app-insights-asp-net-dependencies.md) 과 브라우저의 AJAX 호출 성능 및 사용을 보고하는 데 사용됩니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| remoteDependency [0] async |부울 | |
| remoteDependency [0] baseName |string | |
| remoteDependency [0] commandName |string |예를 들어 "홈/인덱스" |
| remoteDependency [0] count |정수 |100/([샘플링](app-insights-sampling.md) 속도) 예: 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |number |호출부터 종속성의 응답 완료까지 걸리는 시간 |
| remoteDependency [0] id |string | |
| remoteDependency [0] name |string |Url. 최대 길이 250 |
| remoteDependency [0] resultCode |string |HTTP 종속성에서 |
| remoteDependency [0] success |부울 | |
| remoteDependency [0] type |string |Http, Sql,... |
| remoteDependency [0] url |string |최대 길이 2000 |
| remoteDependency [0] urlData.base |string |최대 길이 2000 |
| remoteDependency [0] urlData.hashTag |string | |
| remoteDependency [0] urlData.host |string |최대 길이 200 |

## <a name="requests"></a>요청
[TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest)에서 전송합니다. 표준 모듈이 서버에서 측정된 서버 응답 시간을 보고하는 데 사용됩니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| request [0] count |정수 |100/([샘플링](app-insights-sampling.md) 속도) 예: 4 =&gt; 25%. |
| request [0] durationMetric.value |number |요청부터 응답까지 걸리는 시간입니다. 1e7 == 1s |
| request [0] id |string |작업 ID |
| request [0] name |string |GET/POST + url 기본입니다.  최대 길이 250 |
| request [0] responseCode |정수 |클라이언트에 보낸 HTTP 응답 |
| request [0] success |부울 |기본값 == (responseCode &lt; 400) |
| request [0] url |string |호스트를 포함하지 않음 |
| request [0] urlData.base |string | |
| request [0] urlData.hashTag |string | |
| request [0] urlData.host |string | |

## <a name="page-view-performance"></a>페이지 보기 성능
브라우저에서 전송합니다. 사용자가 요청을 시작할 때부터 표시가 완료될 때까지 페이지 처리 시간을 측정합니다(비동기 AJAX 호출 제외).

컨텍스트 값은 클라이언트 OS 및 브라우저 버전을 표시합니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |정수 |HTML 수신 완료부터 페이지 표시까지 걸리는 시간입니다. |
| clientPerformance [0] name |string | |
| clientPerformance [0] networkConnection.value |정수 |네트워크 연결을 설정하는 데 걸리는 시간입니다. |
| clientPerformance [0] receiveRequest.value |정수 |요청 전송 완료부터 HTML 응답 수신까지 걸리는 시간입니다. |
| clientPerformance [0] sendRequest.value |정수 |HTTP 요청을 전송하는 데 걸리는 시간입니다. |
| clientPerformance [0] total.value |정수 |요청 전송 시작부터 페이지 표시까지 걸리는 시간입니다. |
| clientPerformance [0] url |string |이 요청의 URL |
| clientPerformance [0] urlData.base |string | |
| clientPerformance [0] urlData.hashTag |string | |
| clientPerformance [0] urlData.host |string | |
| clientPerformance [0] urlData.protocol |string | |

## <a name="page-views"></a>페이지 보기
trackPageView() 또는 [stopTrackPage](app-insights-api-custom-events-metrics.md#page-views)에서 전송

| Path | 형식 | 참고 |
| --- | --- | --- |
| view [0] count |정수 |100/([샘플링](app-insights-sampling.md) 속도) 예: 4 =&gt; 25%. |
| view [0] durationMetric.value |정수 |필요에 따라 trackPageView()에서 또는 startTrackPage() - stopTrackPage()에 의해 설정한 값입니다. clientPerformance 값과 다릅니다. |
| view [0] name |string |페이지 제목입니다.  최대 길이 250 |
| view [0] url |string | |
| view [0] urlData.base |string | |
| view [0] urlData.hashTag |string | |
| view [0] urlData.host |string | |

## <a name="availability"></a>Availability
[가용성 웹 테스트](app-insights-monitor-web-app-availability.md)를 보고합니다.

| Path | 형식 | 참고 |
| --- | --- | --- |
| availability [0] availabilityMetric.name |string |Availability |
| availability [0] availabilityMetric.value |number |1.0 또는 0.0 |
| availability [0] count |정수 |100/([샘플링](app-insights-sampling.md) 속도) 예: 4 =&gt; 25%. |
| availability [0] dataSizeMetric.name |string | |
| availability [0] dataSizeMetric.value |정수 | |
| availability [0] durationMetric.name |string | |
| availability [0] durationMetric.value |number |테스트 기간 1e7==1s |
| availability [0] message |string |오류 진단 |
| availability [0] result |string |성공/실패 |
| availability [0] runLocation |string |Http 요청의 지역 소스 |
| availability [0] testName |string | |
| availability [0] testRunId |string | |
| availability [0] testTimestamp |string | |

## <a name="metrics"></a>메트릭
TrackMetric()에서 생성합니다.

메트릭 값은 context.custom.metrics[0]에서 찾을 수 있습니다.

예:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>메트릭 값 정보
메트릭 보고서 및 기타 다른 곳의 메트릭 값은 모두 표준 개체 구조를 사용하여 보고됩니다. 예:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

현재는 표준 SDK 모듈에서 보고되는 모든 값 중에서 `count==1`과 `name` 및 `value` 필드만 유용합니다(향후 변경될 수 있음). 이러한 값이 달라지는 유일한 경우는 TrackMetric 호출을 직접 작성하고 다른 매개 변수를 설정하는 경우입니다.

다른 필드의 목적은 포털로 가는 트래픽을 줄이기 위해 SDK에 메트릭이 집계되도록 허용하는 것입니다. 예를 들어 각 메트릭 보고서를 보내기 전에 여러 연속 판독값의 평균을 낼 수 있습니다. 그런 다음 최소값, 최대값, 표준 편차 및 집계 값(합계 또는 평균)을 계산하고 개수를 보고서에서 표시한 판독값 수로 설정합니다.

위의 테이블에서는 거의 사용되지 않는 필드인 count, min, max, stdDev 및 sampledValue가 생략되었습니다.

원격 분석의 양을 줄여야 하는 경우 사전 집계 메트릭 대신 [샘플링](app-insights-sampling.md) 을 사용할 수 있습니다

### <a name="durations"></a>기간
달리 명시된 경우를 제외하고, 기간은 10분의 1 마이크로초로 표현되므로 10000000.0은 1초를 의미합니다.

## <a name="see-also"></a>참고 항목
* [Application Insights](app-insights-overview.md)
* [연속 내보내기](app-insights-export-telemetry.md)
* [코드 샘플](app-insights-export-telemetry.md#code-samples)
