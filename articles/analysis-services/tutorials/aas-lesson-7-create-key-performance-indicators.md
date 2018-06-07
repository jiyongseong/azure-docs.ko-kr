---
title: 'Azure Analysis Services 자습서 단원 7: 핵심 성과 지표 만들기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 핵심 성과 지표를 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8534640822030a7aa93c8eafe2a1b1a4a8bc5bc4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-key-performance-indicators"></a>핵심 성과 지표 만들기

이 단원에서는 KPI(핵심 성과 지표)를 만듭니다. KPI는 *대상* 값에 대해, *기본* 측정값에 정의되고 측정값 또는 절대값으로도 정의된 값의 성과를 측정하는 데 사용됩니다. 보고 클라이언트 응용 프로그램에서, 비즈니스 전문가는 KPI를 통해 비즈니스 성공 요약 및 추세 파악을 신속하고 간편하게 파악할 수 있습니다. 자세한 내용은 [KPI](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular)를 참조하세요.
  
이 단원을 완료하기 위한 예상 시간: **15분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 6: 측정값 만들기](../tutorials/aas-lesson-6-create-measures.md)를 완료해야 합니다.   
  
## <a name="create-key-performance-indicators"></a>핵심 성과 지표 만들기  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>InternetCurrentQuarterSalesPerformance KPI를 만들려면  
  
1.  모델 디자이너에서 **FactInternetSales** 테이블을 클릭합니다.  
  
2.  측정값 표에서 빈 셀을 클릭합니다.  
  
3.  테이블 위의 수식 입력줄에 다음 수식을 입력합니다. 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    이 측정값을 KPI에 대한 기본 측정값으로 제공합니다.  
  
4.  측정값 표에서 **InternetCurrentQuarterSalesPerformance** > **KPI 만들기**를 마우스 오른쪽 단추로 클릭합니다.   
  
5.  KPI(핵심 성과 지표) 대화 상자의 **대상**에서 **절대값**을 선택한 후 **1.1**을 입력합니다.  
  
7.  왼쪽(낮음) 슬라이더 필드에 **1**을 입력한 후 오른쪽(높음) 슬라이더 필드에 **1.07**을 입력합니다.  
  
8.  **아이콘 스타일 선택**에서 다이아몬드(빨간색), 삼각형(노란색), 원형(녹색) 아이콘 유형을 선택합니다.
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > 제공되는 아이콘 스타일 아래 확장 가능한 **설명** 레이블이 있습니다. 다양한 KPI 요소에 대한 설명을 사용하여 클라이언트 응용 프로그램에서 쉽게 식별할 수 있도록 합니다.  
  
9. **확인**을 클릭하여 KPI를 완료합니다.  
  
    측정값 표에서 **InternetCurrentQuarterSalesPerformance** 측정값 옆에 아이콘이 있습니다. 이 아이콘은 이 측정값이 KPI에 대한 기본값으로 제공됨을 나타냅니다.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>InternetCurrentQuarterMarginPerformance KPI를 만들려면  
  
1.  **FactInternetSales** 테이블에 대한 측정값 표에서 빈 셀을 클릭합니다.  
  
2.  테이블 위의 수식 입력줄에 다음 수식을 입력합니다.  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  **InternetCurrentQuarterMarginPerformance** > **KPI 만들기**를 마우스 오른쪽 단추로 클릭합니다.  
  
4.  KPI(핵심 성과 지표) 대화 상자의 **대상**에서 **절대값**을 선택한 후 **1.25**를 입력합니다.   
  
5.  왼쪽(낮음) 슬라이더에서 필드에 **0.8**이 표시될 때까지 밀고 오른쪽(높음) 슬라이더에서 필드에 **1.03**이 표시될 때까지 밉니다.  
  
6.  **아이콘 스타일 선택**에서 다이아몬드(빨간색), 삼각형(노란색), 원형(녹색) 아이콘 유형을 선택하고 **확인**을 클릭합니다.  
  
## <a name="whats-next"></a>다음 작업
[단원 8: 큐브 뷰 만들기](../tutorials/aas-lesson-8-create-perspectives.md)
  
  
