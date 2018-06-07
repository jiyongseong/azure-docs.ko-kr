---
title: "Application Insights에 대한 릴리스 주석 | Microsoft Docs"
description: "Application Insights에서 배포 또는 빌드 표식을 메트릭 탐색기 차트에 추가합니다."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: a479fa553d64f3820ae8513353484e72b57d30e4
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights의 메트릭 차트에 대한 주석
[메트릭 탐색기](app-insights-metrics-explorer.md) 차트의 주석은 새 빌드를 배포한 위치 또는 다른 중요한 이벤트를 표시합니다. 릴리스 주석으로 변경 내용이 응용 프로그램의 성능에 영향을 주는지 여부를 쉽게 확인할 수 있습니다. 릴리스 주석은 [Visual Studio Team Services 빌드 시스템](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs)에서 자동으로 만들 수 있습니다. [PowerShell에서 만들어](#create-annotations-from-powershell) 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

![서버 응답 시간과 상관 관계가 표시된 주석 예제](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>VSTS 빌드를 사용한 릴리스 주석

릴리스 주석은 클라우드 기반 빌드 기능 및 Visual Studio Team Services 릴리스 서비스입니다. 

### <a name="install-the-annotations-extension-one-time"></a>주석 확장 설치(한 번)
릴리스 주석을 만들려면 Visual Studio Marketplace에서 사용 가능한 여러 Team Service 확장 중 하나를 설치해야 합니다.

1. [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) 프로젝트에 로그인합니다.
2. Visual Studio Marketplace의 [릴리스 주석 확장을 가져와서](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)팀 서비스 계정에 추가합니다.

![Team Services 웹 페이지의 오른쪽 위에서 Marketplace를 엽니다. Visual Team Services를 선택하고 빌드 및 릴리스에서 자세히 보기를 선택합니다.](./media/app-insights-annotations/10.png)

Visual Studio Team Services 계정에 대해 이 작업을 한 번만 수행하면 됩니다. 이제 계정의 모든 프로젝트에 대해 릴리스 주석을 구성할 수 있습니다. 

### <a name="configure-release-annotations"></a>릴리스 주석 구성

각 VSTS 릴리스 템플릿에 대한 별도의 API 키를 가져와야 합니다.

1. [Microsoft Azure Portal](https://portal.azure.com) 에 로그인하고 응용 프로그램을 모니터링하는 Application Insights 리소스를 엽니다. (또는 아직 만들지 않은 경우 [지금 만듭니다](app-insights-overview.md).)
2. **API 액세스**, **Application Insights ID**를 차례로 엽니다.
   
    ![portal.azure.com에서 Application Insights 리소스를 열고 설정을 선택합니다. API 액세스를 엽니다. 응용 프로그램 ID를 복사합니다.](./media/app-insights-annotations/20.png)

4. 별도의 브라우저 창에서, Visual Studio Team Services에서 배포를 관리하는 릴리스 템플릿을 열거나 만듭니다. 
   
    작업을 추가하고 메뉴에서 Application Insights 릴리스 주석 작업을 선택합니다.
   
    API 액세스 블레이드에서 복사한 **응용 프로그램 ID** 를 붙여넣습니다.
   
    ![Visual Studio Team Services에서 릴리스를 열고 릴리스 정의를 선택하고 편집을 선택합니다. 작업 추가를 클릭하고 Application Insights 릴리스 주석을 선택합니다. Application Insights ID를 붙여넣습니다.](./media/app-insights-annotations/30.png)
4. **APIKey** 필드를 변수 `$(ApiKey)`로 설정합니다.

5. Azure 창으로 돌아가 새 API 키를 만들어 복사합니다.
   
    ![Azure 창의 API 액세스 블레이드에서 API 키 만들기를 클릭합니다. 설명을 입력하고 주석 쓰기를 선택하고 키 생성을 클릭합니다. 새 키를 복사합니다.](./media/app-insights-annotations/40.png)

6. 릴리스 템플릿의 구성 탭을 엽니다.
   
    `ApiKey`에 대한 변수 정의를 만듭니다.
   
    ApiKey 변수 정의에 API 키를 붙여넣습니다.
   
    ![Team Services 창에서 구성 탭을 선택하고 변수 추가를 클릭합니다. ApiKey에 대한 이름을 설정하고 값에 방금 생성한 키를 붙여넣은 후 잠금 아이콘을 클릭합니다.](./media/app-insights-annotations/50.png)
7. 마지막으로 릴리스 정의를 **저장** 합니다.


## <a name="view-annotations"></a>주석 보기
이제 릴리스 템플릿을 사용하여 새 릴리스를 배포할 때마다 주석이 Application Insights로 전송됩니다. 주석은 메트릭 탐색기의 차트에 표시됩니다.

주석 마커를 클릭하면 요청자, 소스 제어 분기, 릴리스 정의, 환경 등 릴리스에 대한 세부 정보가 열립니다.

![릴리스 주석 마커를 클릭합니다.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell에서 사용자 지정 주석 만들기
VS Team 시스템을 사용하지 않고 원하는 모든 프로세스에서 주석을 만들 수도 있습니다. 


1. [GitHub에서 Powershell 스크립트](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)의 로컬 복사본을 만듭니다.

2. 응용 프로그램 ID를 가져오고 API 액세스 블레이드에서 API 키를 만듭니다.

3. 다음과 같은 스크립트를 호출합니다.

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

과거에 대한 주석을 만드는 등 이 스크립트를 쉽게 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](app-insights-diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](app-insights-powershell.md)
