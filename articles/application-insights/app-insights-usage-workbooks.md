---
title: Azure Application Insights에서 대화형 통합 문서를 사용하여 사용 현황 데이터 조사 및 공유 | Microsoft docs
description: 웹앱의 사용자 인구 통계 분석입니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin; daviste
ms.openlocfilehash: a871378b3e2cc0b34c925593c6f01952de3aa08e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Application Insights에서 대화형 통합 문서를 사용하여 사용 현황 데이터 조사 및 공유

통합 문서는 [Azure Application Insights](app-insights-overview.md) 데이터 시각화, [분석 쿼리](app-insights-analytics.md) 및 텍스트를 대화형 문서로 결합합니다. 통합 문서는 동일한 Azure 리소스에 대한 액세스로 다른 팀 멤버가 편집할 수 있습니다. 즉 통합 문서를 만드는 데 사용되는 쿼리 및 컨트롤을 통합 문서를 읽고 오류를 탐색, 확장 및 확인하기 쉽게 만드는 다른 사용자가 사용할 수 있음을 의미합니다.

통합 문서는 다음과 같은 시나리오에 유용합니다.

* 관심 있는 메트릭을 사전에 모르는 경우 앱의 사용 현황 탐색: 사용자 수, 재방문 주기율, 변환율 등 Application Insights의 다른 사용 현황 분석 도구와는 달리 통합 문서를 통해 이러한 종류의 자유 형식 탐색에 매우 유용하도록 여러 종류의 시각화 및 분석을 결합할 수 있습니다.
* 팀에 주요 상호 작용 및 다른 메트릭에 대한 사용자 수를 보여 주어 새로 릴리스된 기능이 수행되는 방법을 설명합니다.
* 앱에서 A/B 실험의 결과를 팀의 다른 멤버와 공유합니다. 텍스트로 실험에 대한 목표를 설명한 다음 각 메트릭이 위 또는 아래-대상이었는지 여부에 대한 분명한 설명선과 함께 실험을 평가하는 데 사용되는 각 사용 현황 메트릭 및 분석 쿼리를 보여줄 수 있습니다.
* 데이터, 텍스트 설명 및 차후의 중단을 방지하기 위한 다음 단계의 논의를 결합하여 앱의 사용 현황에 대한 중단의 영향을 보고합니다.

> [!NOTE]
> Application Insights 리소스는 통합 문서를 사용하기 위한 페이지 뷰 또는 사용자 지정 이벤트를 포함해야 합니다. [Application Insights JavaScript SDK를 사용하여 자동으로 페이지 뷰를 수집하도록 앱을 설정하는 방법에 대해 알아봅니다](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>통합 문서 섹션 편집, 다시 정렬, 복제 및 삭제

통합 문서는 개별 편집 가능한 사용 현황 시각화, 차트, 테이블, 텍스트 또는 분석 쿼리 결과의 섹션으로 구성됩니다.

통합 문서 섹션의 콘텐츠를 편집하려면 아래쪽의 **편집** 단추를 클릭하고 통합 문서 섹션의 오른쪽을 클릭합니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/app-insights-usage-workbooks/editing-controls.png)

1. 섹션 편집을 완료하면 섹션의 왼쪽 아래 모서리에 있는 **편집 완료**를 클릭합니다.

2. 중복 섹션을 만들려면 **이 섹션 복제** 아이콘을 클릭합니다. 중복 섹션을 만드는 것은 이전 반복을 손실하지 않고 쿼리를 반복하는 좋은 방법입니다.

3. 통합 문서에서 섹션을 이동하려면 **위로 이동** 또는 **아래로 이동** 아이콘을 클릭합니다.

4. 섹션을 영구적으로 제거하려면 **제거** 아이콘을 클릭합니다.

## <a name="adding-usage-data-visualization-sections"></a>사용 현황 데이터 시각화 섹션 추가

통합 문서는 네 가지 유형의 기본 제공 사용 현황 분석 시각화를 제공합니다. 각각은 앱의 사용 현황에 대한 일반적인 질문에 응답합니다. 이러한 4개의 섹션 이외의 테이블 및 차트를 추가하려면 분석 쿼리 섹션을 추가합니다(아래 참조).

통합 문서에 사용자, 세션, 이벤트 또는 재방문 주기 섹션을 추가하려면 **사용자 추가** 또는 통합 문서의 맨 아래 또는 섹션의 맨 아래에 있는 기타 해당 단추를 사용합니다.

![통합 문서의 사용자 섹션](./media/app-insights-usage-workbooks/users-section.png)

**사용자** 섹션은 "내 사이트의 일부 페이지를 보거나 일부 기능을 사용한 사용자의 수는?"에 응답합니다.

**세션** 섹션은 "사용자가 내 사이트의 일부 페이지를 보거나 일부 기능을 사용하는 데 소비한 세션의 수는?"에 응답합니다.

**이벤트** 섹션은 "사용자가 내 사이트의 일부 페이지를 보거나 일부 기능을 사용한 시간은?"에 응답합니다.

이러한 각 세 가지 섹션 유형은 동일한 컨트롤 및 시각화 집합을 제공합니다.

* [사용자, 세션 및 이벤트 섹션 편집에 대한 자세한 정보](app-insights-usage-segmentation.md)
* 각 섹션 맨 위에 있는 **차트 표시**, **표 표시**, **Insights 표시** 및 **이러한 사용자의 샘플** 확인란을 선택하여 기본 차트, 히스토그램 표, 자동 insights 및 샘플 사용자 시각화를 설정/해제합니다.

![통합 문서의 재방문 주기 섹션](./media/app-insights-usage-workbooks/retention-section.png)

**재방문 주기** 섹션은 "1일 또는 1주일에 일부 페이지를 보거나 일부 기능을 사용한 사용자 중 다음 날이나 다음 주에 다시 방문한 사용자 수는?"에 응답합니다.

* [재방문 주기 섹션 편집에 대한 자세한 정보](app-insights-usage-retention.md)
* 섹션 맨 위에 있는 **전체 재방문 주기 차트 표시** 확인란을 사용하여 선택 가능한 전체 재방문 주기 차트를 설정/해제합니다.

## <a name="adding-application-insights-analytics-sections"></a>Application Insights 분석 섹션 추가

![통합 문서의 분석 섹션](./media/app-insights-usage-workbooks/analytics-section.png)

통합 문서에 Application Insights 분석 섹션을 추가하려면 통합 문서의 맨 아래 또는 섹션의 맨 아래에 있는 **분석 쿼리 추가** 단추를 사용합니다.

분석 쿼리 섹션을 사용하면 통합 문서에 Application Insights 데이터에 대해 임의 쿼리를 추가할 수 있습니다. 이러한 유연성은 분석 쿼리 섹션이 다음과 같은 사용자, 세션, 이벤트 및 재방문 주기에 대해 위에 나열된 4개 이외의 다른 사이트에 대한 질문에 응답하기 위한 go-to(즐겨찾기)가 되어야 함을 의미합니다.

* 사용 현황의 감소와 동일한 기간 동안 사이트에서 throw한 예외의 수는?
* 일부 페이지를 보는 사용자에 대한 페이지 로드 시간의 분포는 무엇이었습니까?
* 사이트의 일부 페이지 집합을 봤지만 다른 일부 페이지 집합을 보지 않은 사용자의 수는? 사이트 기능의 여러 하위 집합을 사용하는 사용자의 클러스터가 있는지 이해하는 데 유용할 수 있습니다(Log Analytics 쿼리 언어에서 `kind=leftanti` 한정자가 포함된 `join` 연산자 사용).

[Log Analytics 쿼리 언어 참조](https://docs.loganalytics.io/)를 사용하여 쿼리 작성에 대해 자세히 알아봅니다.

## <a name="adding-text-and-markdown-sections"></a>텍스트 및 Markdown 섹션 추가

통합 문서에 제목, 설명 및 해설을 추가하면 테이블 및 차트의 집합을 설명으로 전환할 수 있습니다. 통합 문서의 텍스트 섹션은 머리글, 굵게, 기울임꼴 및 글머리 기호 목록과 같은 텍스트 서식 지정에 대해 [Markdown 구문](https://daringfireball.net/projects/markdown/)을 지원합니다.

통합 문서에 텍스트 섹션을 추가하려면 통합 문서의 맨 아래 또는 섹션의 맨 아래에 있는 **텍스트 추가** 단추를 사용합니다.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>팀과 통합 문서 저장 및 공유

통합 문서는 사용자 전용인 **내 보고서** 섹션 또는 Application Insights 리소스에 대한 액세스가 있는 모든 사용자에게 액세스가 허용되는 **공유 보고서** 섹션에서 Application Insights 리소스 내에 저장됩니다. 리소스에서 모든 통합 문서를 보려면 작업 모음에서 **열기** 단추를 클릭합니다.

현재 **내 보고서**에 있는 통합 문서를 공유하려면:

1. 작업 모음에서 **열기**를 클릭합니다.
2. 공유하려는 통합 문서 옆에 있는 "..." 단추를 클릭합니다.
3. **공유 보고서로 이동**을 클릭합니다.

링크 또는 전자 메일을 통해 통합 문서를 공유하려면 작업 모음에서 **공유**를 클릭합니다. 통합 문서를 보려면 링크의 수신자는 Azure Portal에서 이 리소스에 대한 액세스가 필요함을 명심하세요. 편집하려면 수신자는 최소한 리소스에 대한 참가자 권한이 필요합니다.

Azure 대시보드에 통합 문서에 대한 링크를 고정하려면:

1. 작업 모음에서 **열기**를 클릭합니다.
2. 고정하려는 통합 문서 옆에 있는 "..." 단추를 클릭합니다.
3. **대시보드에 고정**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

## <a name="next-steps"></a>다음 단계
- 사용 현황 환경을 활성화하려면 [사용자 지정 이벤트](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) 또는 [페이지 보기](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views) 보내기를 시작합니다.
- 사용자 지정 이벤트 또는 페이지 보기를 이미 보낸 경우 사용자가 서비스를 사용하는 방법에 대해 알아보려면 사용 현황 도구를 살펴봅니다.
    - [사용자, 세션, 이벤트](app-insights-usage-segmentation.md)
    - [깔때기](usage-funnels.md)
    - [보존](app-insights-usage-retention.md)
    - [사용자 흐름](app-insights-usage-flows.md)
    - [사용자 컨텍스트 추가](app-insights-usage-send-user-context.md)
    
