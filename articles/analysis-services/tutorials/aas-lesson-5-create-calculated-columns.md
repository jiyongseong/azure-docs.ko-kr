---
title: 'Azure Analysis Services 자습서 단원 5: 계산된 열 만들기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 계산된 열을 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fa4a092fd811572136bf355de3545a2c847a5ed9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-calculated-columns"></a>계산된 열 만들기

이 단원에서는 계산된 열을 추가하여 모델에서 데이터를 만듭니다. 쿼리 편집기를 사용하거나 여기에서처럼 나중에 모델 디자이너에서 데이터 가져오기를 사용할 때 계산된 열(사용자 지정 열로)을 만들 수 있습니다. 자세한 내용은 [계산된 열](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns)을 참조하세요.
  
서로 다른 세 테이블에 5개의 새 계산된 열을 만듭니다. 단계는 열을 생성하고 이름을 바꾸며 테이블의 여러 위치에 배치하는 여러 가지 방법이 있음을 보여 주는 각 작업에 대해 약간 다릅니다.  

이 단원에서는 먼저 DAX(Data Analysis Expressions)를 사용합니다. DAX는 테이블 형식 모델에 대해 사용자 지정 가능한 수식을 만드는 특별한 언어입니다. 이 자습서에서는 DAX를 사용하여 계산된 열, 측정값 및 역할 필터를 만듭니다. 자세한 내용은 [테이블 형식 모델의 DAX](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular)를 참조하세요. 
  
이 단원을 완료하기 위한 예상 시간: **15분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 4: 관계 만들기](../tutorials/aas-lesson-4-create-relationships.md)를 완료해야 합니다. 
  
## <a name="create-calculated-columns"></a>계산된 열 만들기  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>DimDate 테이블에서 MonthCalendar 계산된 열 만들기  
  
1.  **모델** 메뉴 > **모델 뷰** > **데이터 뷰**를 클릭합니다.  
  
    데이터 뷰에서는 모델 디자이너를 사용하여 계산된 열만 만들 수 있습니다.  
  
2.  모델 디자이너에서 **DimDate** 테이블(탭)을 클릭합니다.  
  
3.  **CalendarQuarter** 열 머리글을 마우스 오른쪽 단추로 클릭한 후 **열 삽입**을 클릭합니다.  
  
    **계산된 열 1**이라는 새 열이 **사분기** 열의 왼쪽에 삽입됩니다.  
  
4.  테이블 위의 수식 입력줄에 다음 DAX 수식을 입력합니다. 자동 완성을 통해 열 및 테이블의 정규화된 이름을 입력하고 사용 가능한 함수 목록을 보여 줍니다.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    그러면 계산된 열의 모든 행에 대한 값이 채워집니다. 테이블을 아래로 스크롤하면, 각 행의 데이터에 따라 행이 이 열에 대해 서로 다른 값을 포함할 수 있는 것을 알게 됩니다.    
  
5.  이 열의 이름을 **MonthCalendar**로 변경합니다. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
MonthCalendar 계산된 열은 Month(월)에 대한 정렬 가능한 이름을 제공합니다.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>DimDate 테이블에서 DayOfWeek 계산된 열 만들기  
  
1.  **DimDate** 테이블이 활성 상태인 동안 **열** 메뉴를 클릭한 후 **열 추가**를 클릭합니다.  
  
2.  수식 입력줄에 다음 수식을 입력합니다.  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    수식 작성을 마쳤으면 Enter 키를 누릅니다. 새 열은 테이블의 오른쪽 끝에 추가됩니다.  
  
3.  열 이름을 **DayOfWeek**로 바꿉니다.  
  
4.  열 머리글을 클릭하고 열을 **EnglishDayNameOfWeek** 열과 **DayNumberOfMonth** 열 사이로 끌어옵니다.  
  
    > [!TIP]  
    > 테이블에서 열을 이동하여 쉽게 탐색할 수 있습니다.  
  
DayOfWeek 계산된 열은 요일에 대한 정렬 가능한 이름을 제공합니다.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>DimProduct 테이블의 ProductSubcategoryName 계산된 열 만들기  
  
  
1.  **DimProduct** 테이블에서 오른쪽 끝으로 스크롤합니다. 맨 오른쪽 열 이름이 **열 추가**(기울임꼴)인 것을 확인하고 열 머리글을 클릭합니다.  
  
2.  수식 입력줄에 다음 수식을 입력합니다.  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  열 이름을 **ProductSubcategoryName**으로 바꿉니다.  
  
ProductSubcategoryName 계산된 열은 DimProductSubcategory 테이블에서 EnglishProductSubcategoryName 열의 데이터를 포함하는 DimProduct 테이블의 계층 구조를 만드는 데 사용됩니다. 계층 구조는 둘 이상의 테이블에 걸쳐 있을 수 없습니다. 계층 구조는 단원 9에서 나중에 만듭니다.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>DimProduct 테이블의 ProductCategoryName 계산된 열 만들기  
  
1.  **DimProduct** 테이블이 활성 상태인 동안 **열** 메뉴를 클릭한 후 **열 추가**를 클릭합니다.  
  
2.  수식 입력줄에 다음 수식을 입력합니다.  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  열 이름을 **ProductCategoryName**으로 바꿉니다.  
  
ProductCategoryName 계산된 열은 DimProductCategory 테이블에서 EnglishProductCategoryName 열의 데이터를 포함하는 DimProduct 테이블의 계층 구조를 만드는 데 사용됩니다. 계층 구조는 둘 이상의 테이블에 걸쳐 있을 수 없습니다.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>FactInternetSales 테이블에서 Margin 계산된 열 만들기  
  
1.  모델 디자이너에서 **FactInternetSales** 테이블을 선택합니다.  
  
2.  **SalesAmount** 열과 **TaxAmt** 열 사이에 새로운 계산된 열을 만듭니다.  
  
3.  수식 입력줄에 다음 수식을 입력합니다.  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  열 이름을 **Margin**으로 바꿉니다.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    Margin 계산된 열은 각 판매의 이익률을 분석하는 데 사용됩니다.  
  
## <a name="whats-next"></a>다음 작업
[단원 6: 측정값 만들기](../tutorials/aas-lesson-6-create-measures.md)
  
  
  
