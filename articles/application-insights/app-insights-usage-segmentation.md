---
title: Azure Application Insights의 사용자, 세션 및 이벤트 분석 | Microsoft Docs
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
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 3ac738d6b3c0f1f3579a9b644a03a01f1509173a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Application Insights의 사용자, 세션 및 이벤트 분석

사람들이 사용자의 웹앱을 사용하는 경우, 가장 관심 있는 페이지, 사용자가 있는 위치, 사용하는 브라우저 및 운영 체제를 알아봅니다. [Azure Application Insights](app-insights-overview.md)를 사용하여 업무 및 원격 사용을 분석합니다.

![Application Insights 사용자의 스크린샷](./media/app-insights-usage-segmentation/0001-users.png)

## <a name="get-started"></a>시작하기

Application Insights 포털에 사용자, 세션 또는 이벤트 블레이드에서 아직 데이터가 표시되지 않으면 [사용 도구 시작 방법을 알아봅니다](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>사용자, 세션 및 이벤트 구분 도구

이러한 세 가지 사용 블레이드는 동일한 도구를 사용하여 3가지 관점에서 웹앱의 원격 분석을 분할 및 분석합니다. 데이터를 필터링하고 분할하여 여러 다른 페이지 및 기능의 상대적 사용을 이해할 수 있습니다.

* **사용자 도구**: 사용자의 앱 및 해당 기능을 사용한 사람의 수.  사용자 수는 브라우저 쿠키에 저장되는 익명 ID를 사용하여 계산합니다. 여러 브라우저 또는 컴퓨터를 사용하는 한 사람은 둘 이상의 사용자로 계산됩니다.
* **세션 도구**: 앱의 특정 페이지 및 기능을 포함하는 사용자 활동 세션의 수. 사용자의 비활동 상태가 30분 경과된 이후 또는 연속해서 24시간 사용한 후에 세션 수가 계산됩니다.
* **이벤트 도구**: 앱의 특정 페이지 및 기능이 사용되는 빈도. 사용자가 [앱을 계측](app-insights-javascript.md)한 경우 페이지 보기 횟수는 브라우저가 앱에서 페이지를 로드할 때 계산됩니다. 

    사용자 지정 이벤트는 앱에서 어떤 동작이 한 번 발생하는 경우를 나타냅니다. 주로 단추 클릭 또는 특정 작업 완료 등과 같은 사용자 상호 작용이 여기에 해당합니다. 앱에 [사용자 지정 이벤트를 생성](app-insights-api-custom-events-metrics.md#trackevent)하는 코드를 삽입합니다.

## <a name="querying-for-certain-users"></a>특정 사용자에 대한 쿼리

사용자 도구 맨 위에 있는 쿼리 옵션을 조정하여 다른 사용자 그룹을 탐색합니다.

* 표시: 분석할 사용자의 코호트를 선택합니다.
* 사용한 사람: 사용자 지정 이벤트 및 페이지 보기를 선택합니다.
* 기간: 시간 범위를 선택합니다.
* 기준: 기간 또는 기타 속성(예: 브라우저 또는 도시) 중에서 데이터를 버킷팅하는 방법을 선택합니다.
* 분할 기준: 데이터를 분할하거나 분리하는 기준 속성을 선택합니다. 
* 필터 추가: 속성(예: 브라우저 또는 도시)을 기준으로 특정 사용자, 세션 또는 이벤트로 쿼리를 제한합니다. 
 
## <a name="saving-and-sharing-reports"></a>보고서 저장 및 공유 
사용자 보고서를 내 보고서 섹션에서 사용자 본인만 볼 수 있게 저장하거나, 공유 보고서 섹션에서 이 Application Insights 리소스에 액세스할 수 있는 모든 사용자와 공유할 수 있습니다.

사용자, 세션 또는 이벤트 보고서에 대한 링크를 공유하려면 도구 모음에서 **공유**를 클릭한 다음, 링크를 복사합니다.

사용자, 세션 또는 이벤트 보고서의 데이터 사본을 공유하려면 도구 모음에서 **공유**를 클릭한 다음, **Word 아이콘**을 클릭하여 해당 데이터가 포함된 Word 문서를 만듭니다. 또는 메인 차트 위의 **Word 아이콘**을 클릭합니다.

## <a name="meet-your-users"></a>사용자 충족

**사용자 충족** 섹션에는 현재 쿼리와 일치하는 약 5개 샘플 사용자에 대한 정보가 표시됩니다. 집계 외에, 개인의 동작을 고려하고 탐색하면 사람들이 실제로 앱을 사용하는 방식을 이해할 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계

- 사용 현황 환경을 활성화하려면 [사용자 지정 이벤트](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) 또는 [페이지 보기](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views) 보내기를 시작합니다.
- 사용자 지정 이벤트 또는 페이지 보기를 이미 보낸 경우 사용자가 서비스를 사용하는 방법에 대해 알아보려면 사용 현황 도구를 살펴봅니다.
    - [깔때기](usage-funnels.md)
    - [보존](app-insights-usage-retention.md)
    - [사용자 흐름](app-insights-usage-flows.md)
    - [통합 문서](app-insights-usage-workbooks.md)
    - [사용자 컨텍스트 추가](app-insights-usage-send-user-context.md)