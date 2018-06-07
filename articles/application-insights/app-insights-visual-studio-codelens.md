---
title: "Visual Studio CodeLens에서 Application Insights 원격 분석 | Microsoft Docs"
description: "Visual Studio에서 CodeLens를 사용하여 Application Insights 요청 및 예외 원격 분석에 빠르게 액세스합니다."
services: application-insights
documentationcenter: .net
author: numberbycolors
manager: carmonm
ms.assetid: 93559e44-23cb-4b9d-8425-60f7f0d0a82c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: 418c391f896ac60f509b963f0aff70ad699fc43b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-telemetry-in-visual-studio-codelens"></a>Visual Studio CodeLens에서 Application Insights 원격 분석
웹앱의 코드에 있는 메서드는 런타임 예외 및 요청 응답 시간에 대한 원격 분석을 사용하여 주석이 추가될 수 있습니다. 응용 프로그램에 [Azure Application Insights](app-insights-overview.md)를 설치하는 경우 원격 분석은 Visual Studio [CodeLens](https://msdn.microsoft.com/library/dn269218.aspx)에 표시됩니다. 즉, 각 함수의 상단에 있는 메모에서 해당 함수가 참조된 횟수 또는 마지막으로 편집한 사용자 등 유용한 정보를 볼 수 있습니다.

![CodeLens](./media/app-insights-visual-studio-codelens/codelens-overview.png)

> [!NOTE]
> CodeLens에서 Application Insights는 Visual Studio 2015 업데이트 3 이상에서 또는 최신 버전의 [개발자 분석 도구 확장](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a)과 함께 사용할 수 있습니다. CodeLens는 Visual Studio의 Enterprise 및 Professional edition에서 사용할 수 있습니다.
> 
> 

## <a name="where-to-find-application-insights-data"></a>Application Insights 데이터의 위치
웹 응용 프로그램의 공용 요청 메서드의 CodeLens 지표에서 Application Insights 원격 분석을 찾습니다. CodeLens 지표는 C# 및 Visual Basic 코드로 위의 메서드 및 다른 선언을 보여 줍니다. Application Insights 데이터를 메서드에 사용할 수 있는 경우 "100개의 요청, 1% 실패함" 또는 "10개의 예외"와 같은 요청 및 예외에 대한 표시를 볼 수 있습니다. 자세한 내용은 CodeLens 지표를 클릭합니다. 

> [!TIP]
> Application Insights 요청 및 예외 지표를 로드하려면 다른 CodeLens 지표가 표시된 후에 몇 초가 더 걸릴 수 있습니다.
> 
> 

## <a name="exceptions-in-codelens"></a>CodeLens의 예외
![TBD](./media/app-insights-visual-studio-codelens/codelens-exceptions.png)

예외 CodeLens 지표는 해당 기간 동안 응용 프로그램에서 가장 자주 발생한 15개의 예외 지난 24시간 동안 발생한 예외의 수를 보여 주고 메서드에서 제공한 요청을 처리합니다.

자세한 세부 정보를 보려면 예외 CodeLens 지표를 클릭합니다.

* 가장 최근 24시간 동안 발생한 예외의 수에서 발생한 비율 변화는 전기 24시간에 비해 상대적입니다.
* **코드로 이동** 을 선택하여 예외를 throw하는 함수에 대한 소스 코드로 이동합니다.
* **검색** 을 선택하여 지난 24시간 동안 발생한 이러한 예외의 모든 인스턴스를 쿼리합니다.
* **추세** 를 선택하여 지난 24시간 동안 발생한 이러한 예외의 추세 시각화를 봅니다.
* **이 앱에서 모든 예외 보기** 를 선택하여 지난 24시간 동안 발생한 모든 예외를 쿼리합니다.
* **예외 추세 탐색** 을 선택하여 지난 24시간 동안 발생한 모든 예외의 추세 시각화를 봅니다. 

> [!TIP]
> CodeLens에서 "0개의 예외"가 표시되지만 예외가 있다는 점을 아는 경우 CodeLens에서 올바른 Application Insights 리소스가 선택되었는지 확인합니다. 다른 리소스를 선택하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights > 원격 분석 원본 선택**을 선택합니다. CodeLens는 지난 24시간 동안 응용 프로그램에서 가장 자주 발생한 15개의 예외에 대해서만 표시되므로, 가장 자주 발생하는 16번째 이후의 예외인 경우 "0 exceptions(0개의 예외)"가 표시됩니다. ASP.NET 보기에서 예외는 해당 보기를 생성하는 컨트롤러 메서드에 나타나지 않을 수 있습니다.
> 
> [!TIP]
> ”가 표시되면? CodeLens에 "?개의 예외"가 표시되면 Azure 계정을 Visual Studio에 연결해야 합니다. 또는 Azure 계정 자격 증명이 만료되었을 수 있습니다. 두 경우 모두 "? 예외"를 클릭하고 **계정 추가...**를 선택하여 자격 증명을 입력합니다.
> 
> 

## <a name="requests-in-codelens"></a>CodeLens에서 요청
![TBD](./media/app-insights-visual-studio-codelens/codelens-requests.png)

요청 CodeLens 지표는 지난 24시간 동안 메서드에서 제공한 HTTP 요청의 수와 실패한 해당 요청의 비율을 보여 줍니다.

자세한 세부 정보를 보려면 요청 CodeLens 지표를 클릭합니다.

* 전기 24시간에 비한 지난 24시간 동안 발생한 요청, 실패한 요청의 수 및 평균 응답 시간 절대 값과 비율 변경
* 지난 24시간 동안 실패하지 않은 요청의 백분율로 계산된 메서드의 안정성
* 요청 또는 실패한 요청에 대한 **검색** 선택하여 지난 24시간 동안 발생한 모든(실패한) 요청을 쿼리합니다.
* **추세** 를 선택하여 지난 24시간 동안 발생한 요청, 실패한 요청 또는 평균 응답 시간에 대한 추세 시각화를 봅니다.
* CodeLens 세부 정보 보기의 왼쪽 위 모퉁이에 있는 Application Insights 리소스의 이름을 선택하여 CodeLens 데이터의 원본인 리소스를 변경합니다.

## <a name="next"></a>다음 단계
|  |  |
| --- | --- |
| **[Visual Studio Online에서 Application Insights로 작업](app-insights-visual-studio.md)**<br/>원격 분석을 검색하고, CodeLens에서 데이터를 확인하며, Application Insights를 구성합니다. Visual Studio 내에서 모두 수행할 수 있습니다. |![프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 및 검색을 선택합니다.](./media/app-insights-visual-studio-codelens/34.png) |
| **[더 많은 데이터 추가](app-insights-asp-net-more.md)**<br/>사용량, 가용성, 종속성, 예외를 모니터링합니다. 로깅 프레임 워크의 추적을 통합합니다. 사용자 지정 원격 분석을 작성합니다. |![Visual studio](./media/app-insights-visual-studio-codelens/64.png) |
| **[Application Insights 포털 사용](app-insights-dashboards.md)**<br/>대시보드, 강력한 분석 및 진단 도구, 경고, 응용 프로그램의 라이브 종속성 맵 및 원격 분석 내보내기입니다. |![Visual studio](./media/app-insights-visual-studio-codelens/62.png) |

