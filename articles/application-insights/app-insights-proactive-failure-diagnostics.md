---
title: Application Insights의 스마트 감지 - 실패 | Microsoft Docs
description: 웹앱에 요청 실패율의 비정상적인 변경 내용에 대해 경고하고 진단 분석을 제공합니다. 구성이 필요하지 않습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: mbullwin; yossiy
ms.openlocfilehash: 29ae81551d4bd4be4123c8e7780b8b5ecc259f09
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="smart-detection---failure-anomalies"></a>스마트 감지 - 실패
[Application Insights](app-insights-overview.md)는 웹앱에서 실패한 요청이 비정상적으로 증가하는 경우 거의 실시간으로 자동으로 알립니다. 실패했다고 보고된 HTTP 요청 또는 종속성 호출의 비율이 비정상적으로 증가하는 것을 감지합니다. 요청의 경우 실패한 요청은 일반적으로 응답 코드 400 이상입니다. 문제를 심사하고 진단할 수 있도록 실패 및 관련된 원격 분석의 특성에 대한 분석이 알림 영역에서 제공됩니다. 또한 추가 진단을 위해 Application Insights 포털에 링크가 제공됩니다. 기능이 Machine Learning 알고리즘을 사용하여 일반 실패율을 예측하려면 설정 또는 구성이 필요하지 않습니다.

이 기능은 자체 서버 또는 클라우드에서 호스팅되는 Java 및 ASP.NET 웹 앱에 대해 작동 합니다. 요청 원격 분석 또는 종속성 원격 분석을 생성하는 모든 앱에 대해 작동합니다(예를 들어 [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) 또는 [TrackDependency()](app-insights-api-custom-events-metrics.md#trackdependency)를 호출하는 작업자 역할이 있는 경우).

[프로젝트에 대한 Application Insights](app-insights-overview.md)를 설정한 후에 제공된 앱은 최소 일정량의 원격 분석을 생성합니다. 실패에 대한 스마트 감지는 전환되고 경고를 보낼 수 있기 전에 앱의 일반적인 동작을 알아보는 데 24시간이 걸립니다.

샘플 경고는 다음과 같습니다.

![실패에 대한 클러스터 분석을 보여주는 샘플 스마트 감지 경고](./media/app-insights-proactive-failure-diagnostics/013.png)

> [!NOTE]
> 기본적으로 이 예제보다 더 짧은 형식의 메일을 받게 됩니다. 그렇지만 [이 자세한 형식으로 전환](#configure-alerts)할 수도 있습니다.
>
>

여기서 알 수 있는 정보:

* 일반 앱 동작과 비교한 실패율
* 영향을 받는 사용자 수 - 고려해야 할 양을 알 수 있습니다.
* 오류와 관련된 특성 패턴. 이 예제에는 특정 응답 코드, 요청 이름(작업) 및 앱 버전이 있습니다. 코드의 어디부터 살펴보아야 하는지 즉시 알려 줍니다. 다른 가능성으로 특정 브라우저 또는 클라이언트 운영 체제가 있을 수 있습니다.
* 특징지어진 실패와 관련된 것으로 보이는 예외, 로그 추적 및 종속성 오류(데이터베이스 또는 다른 외부 구성 요소).
* Application Insights에서 원격 분석에 대한 관련 검색 직접 링크

## <a name="benefits-of-smart-detection"></a>스마트 감지의 이점
일반 [메트릭 경고](app-insights-alerts.md) 는 문제일 수 있음을 알려 줍니다. 하지만 스마트 감지는 진단 작업을 시작하여, 그렇지 않은 경우 사용자가 직접 수행해야 할 상당한 양의 분석을 수행합니다. 깔끔하게 정리된 결과를 얻고 문제의 원인을 신속하게 파악할 수 있습니다.

## <a name="how-it-works"></a>작동 방법
스마트 감지는 앱, 특히 실패 속도에서 받은 원격 분석을 모니터링합니다. 이 규칙은 `Successful request` 속성이 false인 요청 수와 `Successful call` 속이 false인 종속성 호출 수를 계산합니다. 요청의 경우 기본적으로 `Successful request == (resultCode < 400)`입니다(사용자 지정 코드를 [필터](app-insights-api-filtering-sampling.md#filtering)에 작성하거나 사용자 고유 [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest) 호출을 생성하지 않는 한). 

앱의 성능에는 일반적인 동작의 패턴이 있습니다. 일부 요청 또는 종속성 호출은 다른 요청보다 오류 발생 가능성이 높으며 로드가 증가함에 따라 전체 실패율이 상승할 수 있습니다. 스마트 감지는 이러한 이상을 발견하는 데 기계 학습을 사용합니다.

웹 앱에서 Application Insights로 원격 분석을 가져오면 스마트 감지에서 현재 동작을 과거 며칠 동안 확인된 패턴과 비교합니다. 이전 성능과 비교하여 실패율에 비정상적인 증가가 관찰되면 분석이 트리거됩니다.

분석이 트리거되는 경우 서비스는 실패의 특성을 만드는 값의 패턴을 확인하기 위해 실패한 요청에 대한 클러스터 분석을 수행합니다. 위의 예에서 분석은 대부분의 오류가 특정 결과 코드, 요청 이름, 서버 URL 호스트 및 역할 인스턴스에 대한 것임을 발견했습니다. 반면 분석은 클라이언트 운영 체제 속성이 여러 값을 통해 배포되고 있으므로 나열되지 않음을 발견했습니다.

서비스가 이러한 원격 분석 호출로 계측되는 경우 분석기는 해당 요구와 연관된 추적 로그의 예제와 함께 확인된 클러스터의 요청과 관련된 예외 및 종속성 오류를 찾습니다.

다르게 구성하지 않으면 결과 분석은 경고로 전송됩니다.

[수동으로 설정한 경고](app-insights-alerts.md)와 마찬가지로 경고의 상태를 검사하고 Application Insights 리소스의 경고 블레이드에서 구성할 수 있습니다. 하지만 다른 경고와 달리 스마트 감지를 설정하거나 구성할 필요가 없습니다. 원하는 경우 해당 대상 전자 메일 주소를 사용하지 않거나 변경할 수 있습니다.

## <a name="configure-alerts"></a>경고 구성
스마트 감지를 사용하지 않도록 설정하거나, 메일 받는 사람을 변경하거나, Webhook를 만들거나, 보다 자세한 경고 메시지를 옵트인(opt in)할 수 있습니다.

경고 페이지를 엽니다. 실패는 수동으로 설정된 경고와 함께 포함되며 현재 경고 상태인지 여부를 볼 수 있습니다.

![개요 페이지에서 경고 타일을 클릭합니다. 또는 메트릭 페이지에서 경고 단추를 클릭합니다.](./media/app-insights-proactive-failure-diagnostics/021.png)

경고를 클릭하여 구성합니다.

![구성](./media/app-insights-proactive-failure-diagnostics/032.png)

스마트 감지를 비활성화할 수 있지만 삭제할 수 없습니다(또는 다른 스마트 감지를 만들 수 없음).

#### <a name="detailed-alerts"></a>세부 경고
"좀 더 자세한 진단 받기"를 선택한 경우 메일에 자세한 진단 정보가 포함됩니다. 경우에 따라 메일의 데이터를 통해 문제를 진단할 수 있습니다.

더 세부적인 경고에는 예외 및 추적 메시지가 포함되기 때문에 약간의 위험이 따를 수 있습니다. 그러나 이러한 문제는 코드에서 해당 메시지에 중요한 정보를 허용할 수 있는 경우에만 발생합니다.

## <a name="triaging-and-diagnosing-an-alert"></a>경고 분류 및 진단
경고는 실패한 요청 속도에서 비정상적인 증가가 감지되었음을 나타냅니다. 앱 또는 해당 환경에 문제가 있을 가능성이 있습니다.

영향을 받는 요청의 백분율 및 사용자 수에서 문제가 얼마나 긴급한지 결정할 수 있습니다. 위의 예에서 22.5%의 실패율은 일반 비율 1%와 비교했을 때 무엇인가 잘못되었음을 나타냅니다. 반면에 11명의 사용자만 영향을 받았습니다. 앱에서 영향을 받은 경우 얼마나 심각한지 평가할 수 있습니다.

대부분의 경우 요청 이름, 예외, 종속성 오류 및 제공된 추적 데이터에서 신속하게 문제를 진단할 수 있습니다.

몇 가지 다른 단서가 있습니다. 예를 들어 이 예에서 종속성 실패율이 예외 속도(89.3%)와 같습니다. 예외가 종속성 오류에서 직접 발생하므로 코드의 어디 부분부터 살펴보아야 하는지 확실히 알 수 있습니다.

더 자세히 조사해야 하는 경우 각 섹션의 링크는 관련 요청, 예외, 종속성 또는 추적을 필터링하는 [검색 페이지](app-insights-diagnostic-search.md) 로 바로 이동합니다. 또는 [Azure 포털](https://portal.azure.com)을 열고 앱에 대한 Application Insights 리소스에 이동하며 오류 블레이드를 열 수 있습니다.

이 예제에서 '종속성 오류 세부 정보 보기' 링크를 클릭하면 Application Insights 검색 블레이드가 열립니다. 다음과 같은 근본 원인의 예가 있는 SQL 문이 표시됩니다. 필수 필드에 Null이 제공되었으며 저장 작업 동안 유효성 검사를 통과하지 못했습니다.

![진단 검색](./media/app-insights-proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>최근 경고 검토

**스마트 감지**를 클릭하여 가장 최근 경고로 이동합니다.

![경고 요약](./media/app-insights-proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>차이점은 무엇입니까...
실패에 대한 스마트 감지는 Application Insights의 다른 유사하지만 고유한 기능을 보완합니다.

* [메트릭 경고](app-insights-alerts.md) 는 사용자에 의해 설정되며 CPU 점유율, 요청 속도, 페이지 로드 시간 등과 같은 광범위한 메트릭을 모니터링할 수 있습니다. 예를 들어 더 많은 리소스를 추가해야 하는 경우 경고하는 데 사용할 수 있습니다. 반면, 실패에 대한 스마트 감지는 중요한 메트릭의 작은 범위를 다루며(현재 실패한 요청 속도만 해당) 웹앱의 실패한 요청 속도가 웹앱의 일반적인 동작에 비해 크게 증가하면 거의 실시간으로 알리도록 디자인되었습니다.

    스마트 감지에서 일반 조건에 대한 응답으로 임계값을 자동으로 조정합니다.

    스마트 감지는 진단 작업을 시작합니다.
* [성능 이상에 대한 스마트 감지](app-insights-proactive-performance-diagnostics.md)는 컴퓨터 인텔리전스를 사용하여 메트릭에서 특수한 패턴을 검색하고 사용자에 의한 구성은 필요하지 않습니다. 하지만 실패에 대한 스마트 감지와 달리 성능 이상에 대한 스마트 감지의 목적은 예를 들어 특정 형식의 브라우저에 있는 특정 페이지에서 잘못 제공될 수 있는 사용 현황 다기관의 세그먼트를 찾는 것입니다. 분석은 매일 수행되고 결과가 있으면 경고보다 긴급하지 않을 수 있습니다. 이와 반대로 실패에 대한 스마트 감지 분석은 들어오는 원격 분석에서 지속적으로 수행되고 서버 실패율이 예상보다 높은 경우 몇 분 내에 알립니다.

## <a name="if-you-receive-a-smart-detection-alert"></a>스마트 감지 경고를 받은 경우
*이 경고를 받은 이유는 무엇입니까?*

* 이전 기간의 일반적인 기준선에 비해 실패한 요청 속도가 비정상적으로 증가했음을 감지했습니다. 오류 및 관련 원격 분석을 분석한 후 살펴보아야 하는 문제가 있다는 것을 알게 됩니다.

*알림은 분명히 문제가 있음을 의미합니까?*

* 앱 또는 사용자에 대한 의미 체계와 영향을 완전히 이해할 수 있는데도 앱 중단 또는 성능 저하에 대한 경고를 시도합니다.

*그렇다면, 내 데이터를 확인하고 있습니까?*

* 번호 서비스는 완전 자동입니다. 사용자는 알림만 받게 됩니다. 사용자의 데이터는 [비공개](app-insights-data-retention-privacy.md)입니다.

*이 경고를 구독해야 하나요?*

* 번호 요청 원격 분석을 보내는 모든 응용 프로그램에 스마트 감지 경고 규칙이 있습니다.

*구독을 취소하거나 동료에게 대신 보낸 알림을 가져올 수 있습니까?*

* 예, 경고 규칙에서 이를 구성하려면 스마트 감지 규칙을 클릭합니다. 경고를 사용하지 않도록 설정하거나 경고에 대한 받는 사람을 변경할 수 있습니다.

*이메일이 삭제되었습니다. 포털의 어디에서 알림을 찾을 수 있습니까?*

* Azure 활동 로그입니다. Azure에서 앱에 대한 Application Insights 리소스를 열고 활동 로그를 선택합니다.

*경고의 일부가 알려진 문제이고 수신하고 싶지 않습니다.*

* 백로그에 경고 제거가 있습니다.

## <a name="next-steps"></a>다음 단계
이러한 진단 도구를 사용하면 앱에서 원격 분석을 검사할 수 있습니다.

* [메트릭 탐색기](app-insights-metrics-explorer.md)
* [검색 탐색기](app-insights-diagnostic-search.md)
* [분석 - 강력한 쿼리 언어](app-insights-analytics-tour.md)

스마트 감지는 완전히 자동으로 수행됩니다. 하지만 보다 많은 경고를 설정하고 싶을 수 있습니다.

* [수동으로 구성된 메트릭 경고](app-insights-alerts.md)
* [가용성 웹 테스트](app-insights-monitor-web-app-availability.md)
