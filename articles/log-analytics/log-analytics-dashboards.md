---
title: Azure Log Analytics에서 사용자 지정 대시보드 만들기 | Microsoft Docs
description: 이 가이드는 Log Analytics 대시보드가 저장된 모든 로그 검색을 시각화하여 환경을 보는 단일 렌즈를 제공하는 방법을 이해하는 데 도움이 됩니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7eaa74655b009e3154e2cc4f09d5599040ec4589
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Log Analytics에서 사용할 사용자 지정 대시보드 만들기

이 가이드는 Log Analytics 대시보드가 저장된 모든 로그 검색을 시각화하여 환경을 보는 단일 렌즈를 제공하는 방법을 이해하는 데 도움이 됩니다.

>[!NOTE]
> 더 이상 기존의 **내 대시보드**를 편집할 수 없습니다. 이 기능은 점점 사용하지 않고 있습니다.

![예제 대시보드](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

OMS 포털에서 만든 모든 사용자 지정 대시보드는 OMS 모바일 앱에서도 사용할 수 있습니다. 앱에 대한 자세한 내용은 다음 페이지를 참조하십시오.

* [Microsoft 스토어의 OMS 모바일 앱](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Apple iTunes의 OMS 모바일 앱](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![모바일 대시보드](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>내 대시보드를 만드는 방법
시작하려면 OMS 개요 페이지로 이동합니다. 왼쪽에 **내 대시보드** 타일이 표시됩니다. 타일을 클릭하여 대시보드로 드릴다운합니다.

![개요](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>타일 추가
대시보드에서 타일은 저장된 로그 검색을 기반으로 합니다. OMS에는 여러 저장된 로그 검색이 미리 만들어져 있으므로 바로 시작할 수 있습니다. 시작 방법에 대해 개괄적으로 설명하는 다음 단계를 따릅니다.

내 대시보드 보기에서 **사용자 지정**을 클릭하기만 하면 사용자 지정 모드가 시작됩니다.

![그림](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 페이지 오른쪽에 열리는 패널에는 작업 영역의 저장된 로그 검색이 모두 표시됩니다. 저장된 로그 검색을 타일로 시각화하려면 저장된 검색 위에 마우스를 가져간 다음 **더하기** 기호를 클릭합니다.

![타일 추가 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

**더하기** 기호를 클릭하면 내 대시보드 보기에 새 타일이 표시됩니다.

![타일 추가 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>타일 편집
내 대시보드 보기에서 **사용자 지정**을 클릭하기만 하면 사용자 지정 모드가 시작됩니다. 편집하려는 타일을 클릭합니다. 오른쪽 패널이 편집할 수 있도록 바뀌고 다양한 옵션을 제공합니다.

![타일 편집](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![타일 편집](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>타일 시각화
다음 세 종류의 타일 시각화 중에서 선택할 수 있습니다.

| 차트 종류 | 수행하는 작업 |
| --- | --- |
| ![가로 막대형 차트](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |로그 검색 결과가 필드에 따라 집계되는지 여부에 따라 저장된 로그 검색 결과의 타임라인이 막대형 그래프 또는 필드별 결과 목록 형태로 표시됩니다. |
| ![메트릭](./media/log-analytics-dashboards/oms-dashboards-metric.png) |총 로그 검색 결과 적중 횟수를 타일에 숫자로 표시합니다. 메트릭 타일을 사용하면 임계값에 도달할 때 타일이 강조 표시되도록 임계값을 설정할 수 있습니다. |
| ![line](./media/log-analytics-dashboards/oms-dashboards-line.png) |저장된 로그 검색 결과 적중의 타임라인이 값과 함께 꺾은선 그래프로 표시됩니다. |

### <a name="threshold"></a>임계값
메트릭 시각화를 사용하여 타일에 임계값을 만들 수 있습니다. 타일에 임계값을 만들려면 선택합니다. 값이 선택된 임계값을 초과하거나 미만일 때 타일을 강조 표시할지 여부를 선택하고 아래에서 임계값을 설정합니다.

## <a name="organizing-the-dashboard"></a>대시보드 구성
대시보드를 구성하려면 내 대시보드 보기로 이동한 다음 **사용자 지정**을 클릭하여 사용자 지정 모드로 전환합니다. 이동할 타일을 클릭하고 끌어서 원하는 위치로 타일을 이동합니다.

![대시보드 구성](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>타일 제거
타일을 제거하려면 내 대시보드 보기로 이동한 다음 **사용자 지정**을 클릭하여 사용자 지정 모드로 전환합니다. 제거할 타일을 선택한 다음 오른쪽 패널에서 **타일 제거**를 선택합니다.

![타일 제거](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>다음 단계
* 알림을 생성하고 문제를 해결하기 위해 Log Analytics에서 [경고](log-analytics-alerts.md)를 만듭니다.
