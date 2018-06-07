---
title: "새 Azure Application Insights 리소스 만들기 | Microsoft Docs"
description: "새 라이브 응용 프로그램에 대한 Application Insights 모니터링을 수동으로 설정합니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 9023f3d9ae3ddd4d75b5853a08177cba7718cec1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기
Azure Application Insights는 Microsoft Azure *리소스*에 응용 프로그램에 대한 데이터를 표시합니다. 따라서 새 리소스 만들기는 [새 응용 프로그램을 모니터링하도록 Application Insights를 설정][start]하는 과정에 포함됩니다. 대부분의 경우에 리소스를 만드는 작업은 IDE에 의해 자동으로 할 수 있습니다. 하지만 일부 경우에는 리소스를 수동으로 만듭니다. 예를 들어, 응용 프로그램의 제품 개발과 빌드를 위한 별도의 리소스가 있습니다.

리소스를 만든 후에 해당 계측 키를 가져오고 이 키를 사용하여 응용 프로그램에서 SDK를 구성합니다. 리소스 키는 원격 분석을 리소스로 연결합니다.

## <a name="sign-up-to-microsoft-azure"></a>Microsoft Azure에 등록
[Microsoft 계정이 없는 경우 계정을 만듭니다](http://live.com). Outlook.com, OneDrive, Windows Phone 또는 XBox Live 등의 서비스를 이용하는 경우 Microsoft 계정이 이미 있습니다.

또한 [Microsoft Azure](http://azure.com)를 구독해야 합니다. 팀 또는 조직이 Azure를 구독하는 경우 소유자가 사용자의 Windows Live ID를 사용하여 사용자를 구독에 추가할 수 있습니다. 사용하는 구독에 대해서만 요금이 부과됩니다. 기본 계획은 무료로 일정 시험 사용을 허용합니다.

구독에 액세스할 수 있으면 [http://portal.azure.com](https://portal.azure.com)에서 Application Insights에 로그인하고 Live ID를 사용하여 로그인합니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기
[portal.azure.com](https://portal.azure.com)에서 Application Insights 리소스를 추가합니다.

![새로 만들기, Application Insights 클릭](./media/app-insights-create-new-resource/01-new.png)

* **응용 프로그램 유형**은 개요 블레이드에 표시되는 내용 및 [메트릭 탐색기][metrics]에서 사용할 수 있는 속성에 영향을 줍니다. 앱 유형이 표시되지 않으면 일반을 선택합니다.
* **구독** 은 Azure의 지불 계정입니다.
* **리소스 그룹** 은 액세스 제어와 같은 속성을 관리하기 위한 편의 기능입니다. 이미 다른 Azure 리소스를 만든 경우 동일한 그룹에 이 새 리소스를 두도록 선택할 수 있습니다.
* **위치** 는 데이터를 보관하는 곳입니다.
* **대시보드에 고정**은 Azure 홈 페이지의 리소스에 대한 빠른 액세스 타일을 넣습니다. 권장됩니다.

앱이 만들어지면 새 블레이드가 열립니다. 이 블레이드에서 앱의 성능 및 사용량 데이터를 볼 수 있습니다. 

다음에 Azure에 로그인할 때 이곳으로 다시 돌아오려면 시작 보드(홈 화면)에서 앱의 빠른 시작 타일을 찾습니다. 또는 찾아보기를 클릭하여 찾습니다.

## <a name="copy-the-instrumentation-key"></a>계측 키 복사
계측 키는 사용자가 만든 리소스를 식별합니다. SDK를 제공하기 위해 필요합니다.

![Essentials과 계측 키를 차례로 클릭하고, CTRL + C 누릅니다.](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>응용 프로그램에 SDK를 설치합니다.
Application Insights SDK를 응용 프로그램에 설치합니다. 이 단계는 응용 프로그램의 형식에 따라 크게 달라 집니다. 

계측 키를 사용하여 [응용 프로그램에 설치한 SDK][start]를 구성합니다.

SDK는 표준 모듈을 포함하고 있기 때문에 원격 분석을 전송할 때 코드를 작성할 필요가 없습니다. 사용자 작업을 추적하거나 문제를 보다 세부적으로 진단하려면 [API를 사용][api]하여 사용자 고유의 원격 분석을 보내도록 합니다.

## <a name="monitor"></a>원격 분석 데이터 보기
빠른 시작 블레이드를 닫으면 Azure 포털의 사용자 응용 프로그램 블레이드로 돌아갑니다.

검색 타일을 클릭하여 첫 번째 이벤트가 표시되는 [진단 검색][diagnostic]을 확인합니다. 

더 많은 데이터를 보려면 몇 초 후에 **새로 고침**을 클릭합니다.

## <a name="creating-a-resource-automatically"></a>자동으로 리소스 만들기
리소스를 자동으로 만드는 [PowerShell 스크립트](app-insights-powershell.md) 를 작성할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [대시보드 만들기](app-insights-dashboards.md)
* [진단 검색](app-insights-diagnostic-search.md)
* [메트릭 탐색](app-insights-metrics-explorer.md)
* [분석 쿼리 작성](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

