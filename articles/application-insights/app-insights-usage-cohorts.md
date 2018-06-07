---
title: Azure Application Insights 사용 코호트 | Microsoft Docs
description: 공통점이 있는 다양한 집합 또는 사용자, 세션, 이벤트 또는 작업 분석
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: f8d566f552c86f749b914ffed70512209ad76ab7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193801"
---
# <a name="application-insights-cohorts"></a>Application Insights 코호트

코호트는 공통점이 있는 다양한 집합 또는 사용자, 세션, 이벤트 또는 작업입니다. Azure Application Insights에서 코호트는 분석 쿼리에 의해 정의됩니다. 특정 사용자 또는 이벤트 집합을 반복적으로 분석해야 하는 경우 코호트 기능이 사용자가 관심을 두는 집합을 보다 유연하게 정확히 나타낼 수 있습니다.

![코호트 창](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>코호트 및 기본 필터

코호트는 필터와 비슷한 방법으로 사용됩니다. 하지만 코호트의 정의는 사용자 지정 분석 쿼리에서 빌드되므로 훨씬 더 적응성이 뛰어나고 복잡합니다. 필터와 달리, 코호트는 팀의 다른 멤버가 재사용할 수 있게 저장할 수 있습니다.

앱에서 새로운 기능을 모두 사용해본 사용자의 코호트를 정의할 수 있습니다. Application Insights 리소스에 이 코호트를 저장할 수 있습니다. 나중에 이 저장된 특정 사용자의 그룹을 분석하기 쉽습니다.

> [!NOTE]
> 코호트가 만들어지면 사용자, 세션, 이벤트 및 사용자 흐름 도구에서 사용할 수 있습니다.

## <a name="example-engaged-users"></a>예: 참여한 사용자

팀에서는 특정 달 안에 앱을 5번 이상 사용하는 모든 사용자를 참여한 사용자로 정의하고 있습니다. 이 섹션에서는 이러한 참여한 사용자의 코호트를 정의합니다.

1. 코호트 도구를 엽니다.

2. **템플릿 갤러리** 탭을 선택합니다. 다양한 코호트에 대한 템플릿 컬렉션이 표시됩니다.

3. **참여한 사용자 – 사용한 일 기준**을 선택합니다.

    이 코호트에 대해 다음 세 가지 매개 변수가 있습니다.
    * **Activities**: “사용량”으로 계산되는 이벤트 및 페이지 보기를 선택할 수 있습니다.
    * **Period**: 한 달에 대한 정의입니다.
    * **UsedAtleastCustom**: 참여한 사용자로 계산되기 위해 기간 내에 사용해야 하는 시간입니다.

4. **UsedAtleastCustom**을 **5+ days**로 변경하고, **Period**를 기본값인 28일로 둡니다.

    ![참여한 사용자](.\media\app-insights-usage-cohorts\003.png)

    이제 이 코호트는 지난 28일 중에서 별도의 5일 동안의 사용자 지정 이벤트 또는 페이지 보기와 함께 전송된 모든 사용자 ID를 나타냅니다.

5. **저장**을 선택합니다.

   > [!TIP]
   >  코호트에 "참여한 사용자(5+ 일)"와 같은 이름을 지정합니다. 이 Appication Insights 리소스에 대해 액세스 권한이 있는 다른 사람들이 이 코호트를 볼 수 있게 할지 여부에 따라 "내 보고서" 또는 “공유 보고서”에 저장합니다.

6. **갤러리로 돌아가기**를 선택합니다.

### <a name="what-can-you-do-by-using-this-cohort"></a>이 코호트를 사용하여 무엇을 할 수 있나요?

사용자 도구를 엽니다. **표시** 드롭다운 상자의 **속하는 사용자** 아래에서 만든 코호트를 선택합니다.

이제 사용자 도구는 다음 사용자 코호트로 필터링됩니다.

![특정 코호트로 필터링된 사용자 창](.\media\app-insights-usage-cohorts\004.png)

다음 몇 가지 사항에 유의하세요.
* 기본 필터를 통해 이 집합을 만들 수 없습니다. 날짜 논리의 수준이 좀 더 높습니다.
* 사용자 도구에서 일반 필터를 사용하여 이 코호트를 추가로 필터링할 수 있습니다. 따라서 코호트가 28일 기간으로 정의되어도 사용자 도구에서 시간 범위를 30, 60 또는 90일로 조정할 수 있습니다.

이러한 필터는 쿼리 작성기로 표현할 수 없는 더 복잡한 질문을 지원합니다. 예를 들어 다음과 같습니다. _지난 28일 동안 참여한 사람입니다. 이러한 동일한 사람은 지난 60일 동안 어떻게 동작했나요?_

## <a name="example-events-cohort"></a>예: 이벤트 코호트

이벤트의 코호트를 만들 수도 있습니다. 이 섹션에서는 이벤트 및 페이지 보기의 코호트를 정의합니다. 그러면 다른 도구에서 사용하는 방법을 알게 됩니다. 이 코호트는 팀에서 _활성 사용_으로 간주하는 이벤트 집합 또는 특정 새 기능과 관련된 집합을 정의할 수 있습니다.

1. 코호트 도구를 엽니다.

2. **템플릿 갤러리** 탭을 선택합니다. 다양한 코호트에 대한 템플릿 컬렉션이 표시됩니다.

3. **이벤트 선택**을 선택합니다.

    ![이벤트 선택 스크린샷](.\media\app-insights-usage-cohorts\006.png)

4. **활동** 드롭다운 상자에서 코호트에 포함하려는 이벤트를 선택합니다.

5. 코호트에 이름을 지정하여 저장합니다.

## <a name="example-active-users-where-you-modify-a-query"></a>예: 쿼리를 수정하는 경우의 활성 사용자

앞에 나온 두 코호트는 드롭다운 상자를 사용하여 정의되었습니다. 하지만 전체적인 유연성을 위해 분석 쿼리를 사용하여 코호트를 정의할 수도 있습니다. 방법을 알아보기 위해 영국의 사용자 코호트를 만듭니다.

![코호트 도구를 사용하여 만든 걸어다니는 애니메이션 이미지](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. 코호트 도구를 열고, **템플릿 갤러리** 탭을 선택하고, **빈 사용자 코호트**를 선택합니다.

    ![빈 사용자 코호트](.\media\app-insights-usage-cohorts\001.png)

    여기에는 다음 3개의 섹션이 있습니다.
    * Markdown 텍스트 섹션: 여기서 팀의 다른 사용자를 위해 코호트를 좀 더 자세히 설명합니다.

    * 매개 변수 섹션: 여기서 **Activities**와 같은 자체 매개 변수와 앞의 두 예제에 나오는 다른 드롭다운 상자를 만듭니다.

    * 쿼리 섹션: 여기서 분석 쿼리를 사용하여 코호트를 정의합니다.

    쿼리 섹션에서 [분석 쿼리를 작성](https://docs.loganalytics.io/index)합니다. 쿼리는 정의하려는 코호트를 설명하는 특정 행 집합을 선택합니다. 그러면 코호트 도구는 “| summarize by user_Id” 절을 쿼리에 암시적으로 추가합니다. 이 데이터는 테이블의 쿼리 아래에서 미리 볼 수 있으므로 쿼리가 올바른 결과를 반환하도록 할 수 있습니다.

    > [!NOTE]
    > 이 쿼리가 보이지 않으면 섹션을 더 길게 늘리고 쿼리를 표시합니다. 이 섹션 맨 처음에 나오는 애니메이션 .gif는 크기 조정 동작을 보여 줍니다.

2. 다음 텍스트를 복사하여 쿼리 편집기에 붙여넣습니다.

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. **쿼리 실행**을 선택합니다. 테이블에 사용자 ID가 표시되지 않는 경우 응용 프로그램에 사용자가 있는 국가로 변경합니다.

4. 코호트를 저장하고 이름을 지정합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

_특정 국가의 사용자 코호트를 정의했습니다. 사용자 도구의 이 코호트를 사용할 때와 해당 국가에 대해 필터링을 설정할 때 다른 결과를 얻었습니다. 그 이유는 무엇일까요?_

코호트 및 필터는 다릅니다. 영국의 사용자로 이루어진 코호트가 있고(이전 예제와 같이 정의됨) 해당 결과를 필터 “Country or region = United Kingdom”을 설정할 때의 결과와 비교한다고 가정해보겠습니다.

* 코호트 버전은 현재 시간 범위에서 영국으로부터 하나 이상의 이벤트를 전송한 사용자의 모든 이벤트를 표시합니다. 국가 또는 지역별로 분할하면 많은 국가와 지역이 표시될 수 있습니다.
* 필터 버전은 영국의 이벤트만 표시합니다. 하지만 국가 또는 지역별로 분할해도 영국만 표시됩니다.

## <a name="learn-more"></a>자세한 정보
- [Analytics 쿼리 언어](https://go.microsoft.com/fwlink/?linkid=856587)
- [사용자, 세션, 이벤트](app-insights-usage-segmentation.md)
- [사용자 흐름](app-insights-usage-flows.md)
- [사용 현황 개요](app-insights-usage-overview.md)