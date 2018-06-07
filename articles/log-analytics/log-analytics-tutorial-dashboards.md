---
title: Azure Log Analytics 데이터의 대시보드 만들기 및 공유 | Microsoft Docs
description: 이 자습서는 Log Analytics 대시보드가 저장된 모든 로그 검색을 시각화하여 환경을 보는 단일 렌즈를 제공하는 방법을 이해하는 데 도움이 됩니다.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: f6a70f33b143a5b9354411af9717d2a21e24af0c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Log Analytics 데이터의 대시보드 만들기 및 공유

Log Analytics 대시보드를 통해 저장된 모든 로그 검색을 시각화하고 조직의 IT 운영 데이터를 찾고, 상호 연결하며 공유하는 기능을 제공할 수 있습니다.  이 자습서는 IT 운영 지원 팀에서 액세스하는 공유 대시보드를 지원하는 데 사용할 로그 검색을 만드는 방법을 설명합니다.  다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 공유 대시보드 만들기
> * 성능 로그 검색 시각화 
> * 공유 대시보드에 로그 검색 추가 
> * 공유 대시보드에서 타일 사용자 지정

이 자습서의 예제를 완료하려면 [Log Analytics 작업 영역에 연결된](log-analytics-quick-collect-azurevm.md) 기존 가상 머신이 있어야 합니다.  
 
## <a name="log-in-to-azure-portal"></a>Azure Portal에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다. 

## <a name="create-a-shared-dashboard"></a>공유 대시보드 만들기

Microsoft Azure Portal에 로그인한 후 가장 먼저 표시되는 것이 [대시보드](../azure-portal/azure-portal-dashboards.md)입니다.<br> ![Azure Portal 대시보드](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Azure Log Analytics의 원격 분석을 포함하여, 모든 Azure 리소스에서 IT에 가장 중요한 운영 데이터를 이곳에 한데 모을 수 있습니다.  로그 검색 시각화를 단계별로 실행하기 전에 먼저 대시보드를 만들어 공유해 보겠습니다.  이렇게 하면 꺾은선형 차트로 렌더링될 예제 성능 로그 검색을 수행하기 전에 이를 마무리하여 대시보드에 추가할 수 있습니다.  

대시보드를 만들려면 현재 대시보드 이름 옆에 있는 **새 대시보드** 단추를 선택합니다.<br> ![Azure Portal에서 새 대시보드 만들기](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

이 작업은 새 비어 있는 개인 대시보드를 만들고 대시보드의 이름을 지정하고 타일을 추가하거나 다시 정렬할 수 있는 사용자 지정 모드를 설정합니다. 대시보드 이름을 편집하고 이 자습서에 대해 *샘플 대시보드*를 지정한 후 **사용자 지정 완료**를 선택합니다.<br><br> ![사용자 지정된 Azure 대시보드 저장](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

대시보드를 만들 때 기본적으로 비공개입니다. 즉, 볼 수 있는 유일한 사람은 사용자입니다. 다른 사람이 볼 수 있도록 하려면 다른 대시보드 명령 옆에 나타나는 **공유** 단추를 사용합니다.<br> ![Azure Portal에서 새 대시보드 공유](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

게시하려는 대시보드에 대한 구독 및 리소스 그룹을 선택하라는 메시지가 표시됩니다. 편의를 위해 포털의 게시 환경에서는 **대시보드**라는 리소스 그룹에 대시보드를 배치한 위치의 패턴을 안내합니다.  선택한 구독을 확인한 후 **게시**를 클릭합니다.  대시보드에 표시되는 정보에 대한 액세스는 [Azure 리소스 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md)로 제어합니다.   

## <a name="visualize-a-log-search"></a>로그 검색 시각화

Azure Portal에서 로그 검색 포털의 한 줄에 대한 기본 쿼리를 만들 수 있습니다. 로그 검색 포털은 외부 포털을 시작하지 않고도 사용할 수 있으며, 로그 검색을 통해 경고 규칙 생성, 컴퓨터 그룹 생성 및 쿼리 결과 내보내기와 같은 다양한 기능을 수행할 수 있습니다. 

[고급 분석 포털](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal)은 로그 검색 포털에서 사용할 수 없는 고급 기능을 제공하는 전용 포털입니다. 여기에는 여러 줄, 선택적인 실행 코드, 상황에 맞는 중요한 Intellisense 및 스마트 분석에서 쿼리를 편집하는 기능이 포함됩니다. 고급 분석 포털에서 성능 보기를 그래프 양식으로 만들고 이후 검색을 위해 저장하며 이전에 만든 공유 대시보드에 고정합니다.   

로그 검색 포털의 링크에서 고급 분석 포털을 시작합니다.<br> ![고급 분석 포털 시작](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

분석 포털에서 다음 쿼리를 입력하여 Windows 및 Linux 컴퓨터에 대한 프로세스 사용률 레코드만 Computer 및 TimeGenerated로 그룹화하고, 시각적 차트로 표시하여 반환합니다.

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

오른쪽 맨 위에서 **쿼리 저장** 단추를 선택하여 쿼리를 저장합니다.<br> ![고급 분석 포털에서 쿼리 저장](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> **쿼리 저장** 제어판에서 이름을 제공하고(예: *Azure VM - 프로세서 사용률*) **저장**을 클릭합니다.  이러한 방식으로 완전히 다시 작성하지 않고 검색할 일반적인 쿼리 라이브러리를 만들거나 수정할 수 있습니다.  마지막으로, 이 페이지의 오른쪽 가운데에 있는 **Pin chart to your Azure dashboard(Azure 대시보드에 차트 고정)** 단추를 선택하여 이전에 만든 공유 대시보드에 고정합니다.  

이제 대시보드에 쿼리가 고정되었으며 그 아래 일반적인 제목과 설명이 있음을 확인할 수 있습니다.<br> ![Azure 대시보드 샘플](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  보는 사람이 쉽게 이해할 수 있는 의미있는 이름으로 바꾸어야 합니다.  타일을 마우스 오른쪽 단추로 클릭하고 **타일 편집**을 선택합니다.  타일의 제목 및 부제목을 사용자 지정한 후 **업데이트**를 클릭합니다.  변경 내용을 게시하거나 취소할지 묻는 배너가 표시됩니다.  **변경 내용 게시**를 클릭한 후 **타일 편집** 제어 창을 닫습니다.  

![샘플 대시보드의 구성 완료됨](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 대시보드를 만들고 로그 검색을 추가하는 방법을 배웠습니다.  다음 자습서에서는 로그 검색 결과를 기반으로 구현할 수 있는 다양한 응답에 대해 알아봅니다.  

> [!div class="nextstepaction"]
> [Log Analytics 경고로 이벤트에 응답](log-analytics-tutorial-response.md)