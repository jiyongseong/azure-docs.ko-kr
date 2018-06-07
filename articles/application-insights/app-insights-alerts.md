---
title: "Azure Application Insights에서 경고 설정 | Microsoft Docs"
description: "느린 응답 시간, 예외 및 웹앱의 기타 성능 또는 사용 변경에 대한 알림을 받습니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: b0e4828b2cb6bbcb4329381e77db3b187d659706
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="set-alerts-in-application-insights"></a>Application Insights에서 경고 설정
[Azure Application Insights][start]는 웹앱의 성능 및 사용 메트릭이 변경되면 사용자에게 경고할 수 있습니다. 

Application Insights는 [다양한 플랫폼][platforms]에서 라이브 앱을 모니터링하여 성능 문제를 진단하고 사용 패턴을 이해할 수 있습니다.

경고의 종류는 세 가지입니다.

* **메트릭 경고**는 메트릭이 응답 시간, 예외 수, CPU 사용량 또는 페이지 보기 등의 임계값을 일정 기간 동안 초과하면 그 사실을 알려 줍니다. 
* [**웹 테스트**][availability]는 인터넷에서 사이트를 사용할 수 없거나 응답 속도가 느려지면 그 사실을 알려 줍니다. [자세한 정보][availability].
* [**사전 진단**](app-insights-proactive-diagnostics.md)은 비정상적인 성능 패턴에 대해 알려 주기 위해 자동으로 구성됩니다.

이 문서에서는 메트릭 경고에 집중합니다.

## <a name="set-a-metric-alert"></a>메트릭 경고 설정
경고 규칙 블레이드를 연 다음 추가 단추를 사용합니다. 

![경고 규칙 블레이드에서 경고 추가를 선택합니다. 측정할 리소스로 앱을 설정하고, 경고의 이름을 입력하고, 메트릭을 선택합니다.](./media/app-insights-alerts/01-set-metric.png)

* 다른 속성에 앞서 리소스를 설정합니다. **"(구성 요소)" 리소스 선택** 성능 또는 사용 메트릭에 대한 경고를 설정하려는 경우.
* 경고에 입력하는 이름은 리소스 그룹(응용 프로그램 아님) 내에서 고유한 이름이어야 합니다.
* 임계값을 입력하라는 단위에 주의하세요.
* "메일 소유자..." 확인란을 선택하면 이 리소스 그룹에 액세스하는 모든 사용자에게 메일로 경고가 보내집니다. 사용자 집합을 확장하려면 [리소스 그룹 또는 구독](app-insights-resources-roles-access-control.md) (리소스 아님)에 추가합니다.
* "추가 메일"을 지정하면 "메일 소유자..." 확인란의 선택 여부와 상관없이 이러한 개인 또는 그룹에게 경고가 보내집니다. 
* 경고에 응답하는 웹앱을 설정한 경우 [웹후크 주소](../monitoring-and-diagnostics/insights-webhooks-alerts.md)를 설정합니다. 경고가 활성화될 때 및 해결될 때 모두 호출됩니다. 그러나 현재 쿼리 매개 변수는 웹후크 속성으로 전달되지 않습니다.
* 블레이드 맨 위에 있는 단추를 참조하여 경고를 사용 또는 사용 안 함으로 설정할 수 있습니다.

*경고 추가 단추가 보이지 않습니다.* 

* 조직 계정을 사용 중이신가요? 이 응용 프로그램 리소스에 소유자 또는 참가자 액세스가 가능하면 경고를 설정할 수 있습니다. Access Control 블레이드를 살펴봅니다. [액세스 제어에 대해 자세히 알아보세요][roles].

> [!NOTE]
> 경고 블레이드에 [사전 진단](app-insights-proactive-failure-diagnostics.md) 경고 설정이 이미 표시됩니다. 자동 경고는 특정 메트릭, 요청 실패율을 모니터링합니다. 사전 경고를 사용하지 않으려는 경우가 아니면 요청 실패율에 대한 자체 경고를 설정할 필요가 없습니다. 
> 
> 

## <a name="see-your-alerts"></a>경고 보기
알림 상태가 비활성 및 활성 간에 변경될 때 전자 메일이 제공됩니다. 

각 알림의 현재 상태는 알림 규칙 블레이드에 표시됩니다.

경고 드롭다운에 최신 활동에 대한 요약이 있습니다.

![경고 드롭다운](./media/app-insights-alerts/010-alert-drop.png)

상태 변경 내역은 활동 로그에 있습니다.

![개요 블레이드에서 설정, 감사 로그를 클릭합니다.](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>경고 작동 방식
* 경고에는 "활성화되지 않음", "활성화됨", "해결됨"의 세 가지 상태가 있습니다. 활성화됨은 마지막으로 평가되었을 때 지정한 조건이 true였음을 의미합니다.
* 경고 상태가 변경되면 알림이 생성됩니다. (경고를 만들었을 때 이미 경고 조건이 true인 경우 조건이 false가 될 때까지 알림이 발생되지 않을 수 있습니다.)
* 메일 상자를 선택했거나 메일 주소를 제공한 경우 각 알림은 메일을 생성합니다. 알림 드롭다운 목록에서 찾아볼 수도 있습니다.
* 메트릭이 도착할 때마다 경고가 평가되고 그렇지 않은 경우는 평가되지 않습니다.
* 평가는 이전 기간에 대한 메트릭을 집계한 다음 임계값과 비교하여 새 상태를 결정합니다.
* 선택하는 기간으로 메트릭이 집계되는 간격이 지정됩니다. 기간은 경고가 평가되는 빈도에는 영향을 주지 않습니다. 이는 메트릭 도착 빈도에 따라 다릅니다.
* 일정 시간 동안 특정 메트릭에 대해 데이터가 도착하지 않는 경우 해당 간격은 메트릭 탐색기의 차트와 경고 평가에 다른 영향을 미칩니다. 메트릭 탐색기에서 데이터가 차트의 샘플링 간격보다 오랫동안 표시되지 않는 경우 차트 값에는 0이 표시됩니다. 하지만 동일한 메트릭에 따른 경고는 재평가되지 않으며 경고의 상태는 변경되지 않고 그대로 유지됩니다. 
  
    마침내 데이터가 도착하면 차트는 0이 아닌 값으로 이동합니다. 경고는 지정된 기간 동안 사용 가능한 데이터에 따라 평가합니다. 새 데이터 요소를 해당 기간에 하나만 사용할 수 있는 경우 집계는 해당 데이터 요소만 기반으로 합니다.
* 경고는 긴 기간을 설정한 경우에도 경고와 정상 상태 사이에서 자주 깜박거릴 수 있습니다. 메트릭 값이 임계값 주위를 가리키는 경우 이런 현상이 발생할 수 있습니다. 임계값에는 이력이 없습니다. 경고로 전환되는 것과 정상으로 전환되는 것이 같은 값에서 발생합니다.

## <a name="what-are-good-alerts-to-set"></a>어떤 경고를 설정하면 좋은가요?
응용 프로그램에 따라 다릅니다. 처음에는 경고를 너무 많이 설정하지 않는 것이 좋습니다. 일단 앱이 실행되는 동안 메트릭 차트를 살펴보면서 정상적으로 작동할 때의 상태를 숙지합니다. 이렇게 하면 성능을 개선하는 방법을 찾는 데 도움이 됩니다. 그런 다음 메트릭이 정상 영역을 벗어나면 알려주는 경고를 설정합니다. 

다음은 많은 사람들이 사용하는 경고입니다.

* [브라우저 메트릭][client], 특히 브라우저 **페이지 로드 시간**은 웹 응용 프로그램에 매우 유용합니다. 페이지에 스크립트가 많은 경우 **브라우저 예외**를 확인해야 합니다. 이러한 메트릭 및 경고를 가져오려면 [웹 페이지 모니터링][client]을 설정해야 합니다.
* 서버 쪽 웹 응용 프로그램에 대한 **서버 응답 시간**. 경고 설정 외에도 이 메트릭을 주시하여 메트릭이 불균형적으로 변하고 요청 속도가 빠른지 살펴보는 것이 좋습니다. 변형은 앱에 리소스가 부족하다는 의미일 수 있습니다. 
* **서버 예외** 를 보려면 일부 [추가 설치](app-insights-asp-net-exceptions.md)작업을 수행해야 합니다.

[자동 관리 실패율 진단](app-insights-proactive-failure-diagnostics.md)은 앱이 오류 코드로 요청에 응답하는 속도를 자동으로 모니터링한다는 것을 잊지 마십시오. 

## <a name="automation"></a>Automation
* [PowerShell을 사용하여 경고 설정 자동화](app-insights-powershell-alerts.md)
* [Webhook를 사용하여 경고에 대한 응답 자동화](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>참고 항목
* [가용성 웹 테스트](app-insights-monitor-web-app-availability.md)
* [경고 설정 자동화](app-insights-powershell-alerts.md)
* [사전 진단](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

