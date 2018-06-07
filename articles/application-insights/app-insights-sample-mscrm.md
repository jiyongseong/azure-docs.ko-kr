---
title: Microsoft Dynamics CRM 및 Azure Application Insights | Microsoft Docs
description: Application Insights를 사용하여 Microsoft Dynamics CRM Online에서 원격 분석 가져오기 설정, 데이터 가져오기, 시각화 및 내보내기의 연습입니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: mbullwin; mazhar
ms.openlocfilehash: 0080217f718d8df9b62c7bc305fbc1365477cc4d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>연습: Application Insights를 사용하여 Microsoft Dynamics CRM Online 작업에 대한 원격 분석 설정
이 문서는 [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)를 사용하여 [Microsoft Dynamics CRM Online](https://www.dynamics.com/)에서 원격 분석 데이터를 가져오는 방법을 보여 줍니다. 응용 프로그램에 Application Insights 스크립트 추가, 데이터 캡처 및 데이터 시각화의 전체 프로세스를 연습합니다.

> [!NOTE]
> [샘플 솔루션을 탐색합니다](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>기존 또는 새 CRM Online 인스턴스에 Application Insights를 추가합니다.
응용 프로그램을 모니터링하려면 응용 프로그램에 Application Insights SDK를 추가합니다. SDK는 [Application Insights 포털](https://portal.azure.com)로 원격 분석을 보내며 여기서 강력한 분석 및 진단 도구를 사용하고 저장소로 데이터를 내보낼 수 있습니다.

### <a name="create-an-application-insights-resource-in-azure"></a>Azure에서 Application Insights 리소스 만들기
1. [Microsoft Azure에서 계정](http://azure.com/pricing)을 만듭니다. 
2. [Azure 포털](https://portal.azure.com) 에 로그인한 다음 새 Application Insights 리소스를 추가합니다. 데이터가 처리되어 표시될 위치입니다.

    ![+, 개발자 서비스, Application Insights를 클릭합니다.](./media/app-insights-sample-mscrm/01.png)

    응용 프로그램 유형으로 ASP.NET을 선택합니다.
3. 시작 페이지를 열고 “클라이언트 쪽 모니터링 및 진단”을 엽니다.

    ![웹 페이지에서 삽입에 대한 코드 조각](./media/app-insights-sample-mscrm/03.png)

**코드 페이지를 열린 상태로 유지** 합니다. 코드가 곧 필요합니다. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Microsoft Dynamics CRM의 JavaScript 웹 리소스 만들기
1. CRM Online 인스턴스를 열고 관리자 권한으로 로그인합니다.
2. Microsoft Dynamics CDM 설정, 사용자 지정, 시스템 사용자 지정을 엽니다.

    ![Microsoft Dynamics CRM 설정](./media/app-insights-sample-mscrm/00001.png)

    ![설정 > 사용자 지정](./media/app-insights-sample-mscrm/00002.png)

1. JavaScript 리소스를 만듭니다.

    ![새 웹 리소스 대화 상자](./media/app-insights-sample-mscrm/07.png)

    이름을 지정하고 **스크립트(JScript)** 를 선택하고 텍스트 편집기를 엽니다.

    ![텍스트 편집기 열기](./media/app-insights-sample-mscrm/00004.png)
2. Application Insights에서 코드 복사 복사하는 동안 스크립트 태그를 무시합니다. 아래 스크린샷을 참조하세요.

    ![계측 키 설정](./media/app-insights-sample-mscrm/000005.png)

    코드는 Application insights 리소스를 식별하는 계측 키를 포함합니다.
3. 저장하고 게시합니다.

    ![저장 및 게시](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>계측 양식
1. Microsoft CRM Online에서 계정 양식을 엽니다.

    ![계정 양식](./media/app-insights-sample-mscrm/00007.png)
2. 양식 속성 열기

    ![양식 속성](./media/app-insights-sample-mscrm/00008.png)
3. 만든 JavaScript 웹 리소스를 추가합니다.

    ![추가 메뉴](./media/app-insights-sample-mscrm/13.png)

4. 양식 사용자 지정 항목을 저장하고 게시합니다.

## <a name="metrics-captured"></a>캡처된 메트릭
이제 양식에 대한 원격 분석 캡처를 설정했습니다. 사용할 때마다 Application Insights 리소스에 데이터가 전송됩니다.

표시되는 데이터의 샘플은 다음과 같습니다.

#### <a name="application-health"></a>응용 프로그램 상태
![예제 페이지 로드 시간](./media/app-insights-sample-mscrm/15.png)

![예제 페이지 보기 차트](./media/app-insights-sample-mscrm/16.png)

브라우저 예외:

![브라우저 예외 차트](./media/app-insights-sample-mscrm/17.png)

차트를 클릭하여 자세한 내용을 봅니다.

![예외 목록](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>사용 현황
![사용자, 세션 및 페이지 보기](./media/app-insights-sample-mscrm/19.png)

![세션 차트](./media/app-insights-sample-mscrm/20.png)

![브라우저 버전](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>브라우저
![페이지 로드 시간 분석](./media/app-insights-sample-mscrm/22.png)

![브라우저 버전별 세션 수](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>지리적 위치
![국가별 세션 수](./media/app-insights-sample-mscrm/24.png)

![국가별 세션 및 사용자](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>내부 페이지 보기 요청
![페이지 보기 요약](./media/app-insights-sample-mscrm/26.png)

![페이지 보기 이벤트 검색](./media/app-insights-sample-mscrm/27.png)

![비슷한 페이지 보기](./media/app-insights-sample-mscrm/28.png)

![페이지 보기 속성](./media/app-insights-sample-mscrm/29.png)

![세션별 페이지](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>샘플 코드
[샘플 코드를 탐색합니다](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
[Microsoft Power BI에 데이터를 내보내는](app-insights-export-power-bi.md)경우 훨씬 심도 깊은 분석을 수행할 수 있습니다.

## <a name="sample-microsoft-dynamics-crm-solution"></a>샘플 Microsoft Dynamics CRM 솔루션
[다음은 Microsoft Dynamics CRM에서 구현된 샘플 솔루션입니다.](https://dynamicsandappinsights.codeplex.com/)

## <a name="learn-more"></a>자세한 정보
* [Application Insights란?](app-insights-overview.md)
* [웹 페이지용 Application Insights](app-insights-javascript.md)
* [추가 샘플 및 연습](app-insights-code-samples.md)
