---
title: "Analytics - Azure Application Insights의 강력한 검색 및 쿼리 도구 | Microsoft Docs"
description: "Application Insights의 강력한 진단 검색 도구인 분석의 개요입니다. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 5f324051a2eeedd35a22f77c771793af9c90c434
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="analytics-in-application-insights"></a>Application Insights의 분석
Analytics는 [Application Insights](app-insights-overview.md)의 강력한 검색 및 쿼리 도구입니다. Analytics는 웹 도구이므로 설치가 필요하지 않습니다. 앱 중 하나에 대해 Application Insights를 이미 구성한 경우 앱의 [개요 블레이드](app-insights-dashboards.md)에서 Analytics를 열어 앱의 데이터를 분석할 수 있습니다.

![portal.azure.com에서 Application Insights 리소스를 열고 분석을 클릭합니다.](./media/app-insights-analytics/001.png)

여러 샘플 데이터가 있는 무료 데모 환경인 [Analytics 실습](https://go.microsoft.com/fwlink/?linkid=859557)을 사용할 수도 있습니다.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Analytics의 쿼리 데이터
일반적인 쿼리는 테이블 이름으로 시작하며 그 뒤에 `|`로 구분된 일련의 *연산자*가 있습니다.
예를 들어, 지난 3시간 동안 앱이 다른 국가에서 받은 요청의 개수를 살펴보겠습니다.
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

테이블 이름 *requests*로 시작하고 필요에 따라 파이프된 요소를 추가합니다.  먼저 시간 필터를 정의하여 지난 3시간의 레코드를 검토합니다.
그런 다음 국가당 레코드 수를 계산합니다(열 *client_CountryOrRegion*에서 해당 데이터를 찾을 수 있음). 마지막으로 원형 차트에서 결과를 렌더링합니다.
<br>

![쿼리 결과](./media/app-insights-analytics/030.png)

언어에 다음과 같은 많은 유용한 기능이 있습니다.

* [필터링](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) 합니다.
* [조인](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/join-operator) - 요청을 페이지 뷰, 종속성 호출, 예외 및 로그 추적과 상호 연결합니다.
* 강력한 통계 [집계](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions)기능이 있습니다.
* 즉각적이고 강력하게 시각화합니다.
* 예를 들어 PowerShell에서 프로그래밍 방식으로 쿼리를 실행하는 데 사용할 수 있는 [REST API](https://dev.applicationinsights.io/)가 있습니다.

[전체 언어 참조](https://go.microsoft.com/fwlink/?linkid=856079)는 지원되는 모든 명령을 자세히 설명하고 정기적으로 업데이트합니다.

## <a name="next-steps"></a>다음 단계
* [Analytics 포털](https://go.microsoft.com/fwlink/?linkid=856587) 시작
* [쿼리 작성](https://go.microsoft.com/fwlink/?linkid=856078) 시작
* 가장 일반적인 관용구의 번역에 대한 [SQL 사용자 치트 시트](https://aka.ms/sql-analytics)를 검토합니다.
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우, [실습](https://analytics.applicationinsights.io/demo)에 대한 Analytics를 테스트합니다.
* [소개 비디오](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)를 봅니다.