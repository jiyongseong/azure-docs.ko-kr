---
title: "Azure Application Insights를 최대한 활용하기 | Microsoft Docs"
description: "Application Insights를 시작한 경우 다음 기능을 사용할 수 있습니다."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 167f0175b2c5de804a4251307a7b16e5e40a516a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights의 추가 원격 분석
[ASP.NET 코드에 Application Insights를 추가](app-insights-asp-net.md)한 후 추가 원격 분석을 위해 수행할 수 있는 몇 가지 작업이 있습니다. 

| 동작 | 결과|
|---|---|
|(IIS 서버) 각 서버 컴퓨터에 [상태 모니터를 설치](http://go.microsoft.com/fwlink/?LinkId=506648)합니다.<br/>(Azure 웹앱) 웹앱의 Azure 제어판에서 Application Insights 블레이드를 엽니다.| [**성능 카운터**](app-insights-performance-counters.md)<br/>[**예외** ](app-insights-asp-net-exceptions.md) - 자세한 스택 추적<br/>[**종속성**](app-insights-asp-net-dependencies.md)|
|[웹 페이지에 JavaScript 코드 조각 추가](app-insights-javascript.md)|[성능 페이지](app-insights-web-track-usage.md), 브라우저 예외, AJAX 성능. 클라이언트쪽 사용자 지정 원격 분석.|
|[가용성 웹 테스트 만들기](app-insights-monitor-web-app-availability.md)|사이트를 사용할 수 없게 되면 알림 수신|
|MSBuild에서 [Ensure buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) 생성|[메트릭 차트의 빌드 주석](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[사용자 지정 이벤트 및 메트릭 작성](app-insights-api-custom-events-metrics.md)|비즈니스 이벤트 및 메트릭의 수를 계산하고, 자세한 사용 현황을 추적하는 등의 작업을 수행합니다.|
|[라이브 사이트 프로파일링](https://aka.ms/AIProfilerPreview)|라이브 웹앱의 자세한 함수 타이밍|






