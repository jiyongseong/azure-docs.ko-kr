---
title: Azure Log Analytics 보기의 필터 | Microsoft Docs
description: Log Analytics 보기에서 필터를 사용하면 사용자가 뷰 자체를 수정하지 않고 특정 속성의 값으로 뷰에서 데이터를 필터링할 수 있습니다.  이 문서에서는 필터를 사용하여 사용자 지정 보기에 추가하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="filters-in-log-analytics-views"></a>Log Analytics 보기의 필터
[Log Analytics 보기](log-analytics-view-designer.md)에서 **필터**를 사용하면 사용자가 뷰 자체를 수정하지 않고 특정 속성의 값으로 뷰에서 데이터를 필터링할 수 있습니다.  예를 들어, 보기의 사용자가 특정 컴퓨터 또는 컴퓨터 집합의 데이터에 대한 보기를 필터링하도록 허용할 수 있습니다.  단일 보기에 여러 필터를 만들어서 사용자가 여러 속성별로 필터링하도록 허용할 수 있습니다.  이 문서에서는 필터를 사용하여 사용자 지정 보기에 추가하는 방법을 설명합니다.

## <a name="using-a-filter"></a>필터 사용
**필터**를 클릭하여 보기에 대한 필터 창을 엽니다.  그러면 보기에 사용할 수 있는 모든 필터에 대한 시간 범위 및 값을 선택할 수 있습니다.  필터를 선택하면 사용 가능한 값의 목록이 표시됩니다.  하나 이상의 값을 선택하거나 입력할 수 있습니다. 보기는 지정된 값을 필터링하도록 자동으로 업데이트됩니다. 

필터에 대한 값을 선택하지 않으면 해당 필터가 보기에 적용되지 않습니다.  필터에 대한 모든 값을 제거하면 해당 필터가 더 이상 적용되지 않습니다.


![필터 예제](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>필터 만들기

[보기를 편집](log-analytics-view-designer.md)할 때 **필터** 탭에서 필터를 만듭니다.  필터는 보기에 대해 전역적이며 보기에서 모든 부분에 적용됩니다.  

![필터 설정](media/log-analytics-view-designer/filters-settings.png)

다음 표에서는 필터의 설정을 설명합니다.

| 설정 | 설명 |
|:---|:---|
| 필드 이름 | 필터링에 사용되는 필드의 이름입니다.  **값에 대한 쿼리**에서 요약 필드와 일치해야 합니다. |
| 값에 대한 쿼리 | 쿼리를 실행하여 사용자에 대한 필터 드롭다운을 채웁니다.  그러려면 [요약](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 또는 [고유](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) 중 하나를 사용하여 특정 필드에 대한 고유 값을 제공해야 하며 **필드 이름**과 일치해야 합니다.  [정렬](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)을 사용하여 사용자에게 표시되는 값을 정렬할 수 있습니다. |
| 태그 | 필터를 지원하는 쿼리에서 사용되는 필터의 이름은 사용자에게도 표시됩니다. |

### <a name="examples"></a>예

다음 표에는 몇 가지 공통 필터가 포함되어 있습니다.  

| 필드 이름 | 값에 대한 쿼리 | 태그 |
|:--|:--|:--|
| Computer   | 하트비트 &#124; 고유한 컴퓨터 &#124; 컴퓨터 오름차순 정렬 | 컴퓨터 |
| EventLevelName | 이벤트 &#124; 고유한 EventLevelName | 심각도 |
| SeverityLevel | Syslog &#124; 고유한 SeverityLevel | 심각도 |
| SvcChangeType | ConfigurationChange &#124; 고유한 svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>보기 쿼리 수정

적용되는 필터의 경우 보기에서 모든 쿼리를 수정하여 선택한 값을 필터링해야 합니다.  보기에서 쿼리를 수정하지 않으면 사용자가 선택한 모든 값은 적용되지 않습니다.

쿼리에서 필터 값을 사용하기 위한 구문은 다음과 같습니다. 

    where ${filter name}  

예를 들어 보기에 이벤트를 반환하고 컴퓨터라는 필터를 사용하는 쿼리가 있는 경우 다음을 사용할 수 있습니다.

    Event | where ${Computers} | summarize count() by EventLevelName

심각도라는 다른 필터를 추가한 경우 두 필터를 모두 사용하는 다음 쿼리를 사용할 수 있습니다.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>다음 단계
* 사용자 지정 보기에 추가할 수 있는 [시각화 파트](log-analytics-view-designer-parts.md)에 대해 자세히 알아봅니다.