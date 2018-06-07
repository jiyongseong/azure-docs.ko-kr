---
title: 'Azure Analysis Services 자습서 단원 6: 측정값 만들기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 측정값을 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31531abb5c2d2309317f1b88a1a794392b914f90
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-measures"></a>측정값 만들기

이 단원에서는 모델에 포함할 측정값을 만듭니다. 생성한 계산된 열과 마찬가지로, 측정값은 DAX 수식을 사용하여 만든 계산입니다. 그러나 계산된 열과 달리 측정값은 사용자가 선택한 *필터*를 기반으로 평가됩니다. 예를 들어 특정 열 또는 슬라이서가 피벗 테이블의 행 레이블 필드에 추가되었습니다. 그러면 필터의 각 셀에 대한 값이 적용된 측정값으로 계산됩니다. 측정값은 수치 데이터에 동적인 계산을 수행하기 위해 거의 모든 테이블 형식 모델에 포함하려는 강력하고 유연한 계산입니다. 자세한 내용은 [측정값](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular)을 참조하세요.
  
측정값을 만들려면 *측정값 표*를 사용합니다. 기본적으로 각 테이블에는 빈 측정값 표가 있습니다. 그러나 일반적으로 모든 테이블에 대해 측정값을 만들지 않습니다. 측정값 표는 데이터 뷰의 디자이너에서 테이블 아래에 나타납니다. 테이블에 대한 측정값 표를 숨기거나 표시하려면 **테이블** 메뉴를 클릭한 다음 **측정값 표 표시**를 클릭합니다.  
  
측정값 표에서 빈 셀을 클릭하고 수식 입력줄에 DAX 수식을 입력하여 측정값을 만들 수 있습니다. Enter 키를 눌러 수식을 완성하면 측정값이 셀에 나타납니다. 열을 클릭한 후 도구 모음에서 자동 합계 단추(**∑**)를 클릭하여 표준 집계 함수로 측정값을 만들 수도 있습니다. 자동 합계 기능을 사용하여 만든 측정값은 해당 열 바로 아래에 있는 측정값 표 셀에 나타나지만 이동할 수 있습니다.  
  
이 단원에서는 수식 입력줄에 DAX 수식을 입력하고 자동 합계 기능을 사용하는 두 가지 방법으로 측정값을 만듭니다.  
  
이 단원을 완료하기 위한 예상 시간: **30분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 5: 계산된 열 만들기](../tutorials/aas-lesson-5-create-calculated-columns.md)를 완료해야 합니다.  
  
## <a name="create-measures"></a>측정값 만들기  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>DimDate 테이블에 DaysCurrentQuarterToDate 측정값을 만들려면  
  
1.  모델 디자이너에서 **DimDate** 테이블을 클릭합니다.  
  
2.  측정값 표에서 왼쪽 위 빈 셀을 클릭합니다.  
  
3.  수식 입력줄에 다음 수식을 입력합니다.  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    왼쪽 위 셀에는 이제 측정값 이름인 **DaysCurrentQuarterToDate**와 결과인 **92**가 차례로 포함됩니다. 사용자 필터가 적용되지 않았으므로 이 지점에서 결과는 관련이 없습니다.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    계산된 열과 달리 측정값 수식은, 측정값 이름, 콜론, 수식 순서로 입력할 수 있습니다.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>DimDate 테이블에 DaysInCurrentQuarter 측정값을 만들려면  
  
1.  모델 디자이너에서 **DimDate** 테이블이 활성 상태인 동안 측정값 표에서 만든 측정값 아래의 빈 셀을 클릭합니다.  
  
2.  수식 입력줄에 다음 수식을 입력합니다.  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    불완전한 기간과 이전 기간 사이의 비교 비율을 만드는 경우 수식은 경과된 기간의 비율을 계산하고 이전 기간의 동일한 부분과 비교해야 합니다. 이 경우 [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter]는 현재 기간에서 경과한 비율을 제공합니다.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>FactInternetSales 테이블에서 InternetDistinctCountSalesOrder 측정값을 만들려면  
  
1.  **FactInternetSales** 테이블을 클릭합니다.   
  
2.  **SalesOrderNumber** 열 머리글을 클릭합니다.  
  
3.  도구 모음에서 자동 합계(**∑**) 단추 옆에 있는 아래쪽 화살표를 클릭한 다음 **DistinctCount**를 선택합니다.  
  
    자동 합계 기능은 DistinctCount 표준 집계 수식을 사용하여 선택된 열에 대한 측정값을 자동으로 만듭니다.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  측정값 표에서 새 측정값을 클릭한 다음 **속성** 창의 **측정값 이름**에서 측정값의 이름을 **InternetDistinctCountSalesOrder**로 변경합니다. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>FactInternetSales 테이블에 측정값을 추가로 만들려면  
  
1.  자동 합계 기능을 사용하여 다음 측정값을 만들고 이름을 지정합니다.  

    |열|측정값 이름|자동 합계(∑)|수식|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|개수|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|합계|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|합계|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|합계|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|합계|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|합계|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|합계|=SUM([TaxAmt])|  
    |Freight|InternetTotalFreight|합계|=SUM([Freight])|  
  
2.  측정값 표에서 빈 셀을 클릭하고 수식 입력줄을 사용하여 다음 사용자 지정 측정값을 순서대로 만듭니다.  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
FactInternetSales 테이블에 대해 만든 측정값을 사용하여 사용자가 선택한 필터로 정의된 항목에 대해 매출, 비용 및 이익률과 같은 중요한 회계 데이터를 분석할 수 있습니다.  
  
## <a name="whats-next"></a>다음 작업
[단원 7: 핵심 성과 지표 만들기](../tutorials/aas-lesson-7-create-key-performance-indicators.md)  

  
