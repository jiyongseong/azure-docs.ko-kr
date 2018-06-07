---
title: 'Azure Analysis Services 자습서 단원 9: 계층 구조 만들기 | Microsoft Docs'
description: 테이블 형식 모델에서 계층 구조를 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb32736e5837c939b696e131746f9af2b9340acb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-hierarchies"></a>계층 구조 만들기

이 단원에서는 계층 구조를 만듭니다. 계층은 수준별로 정렬된 열 그룹입니다. 예를 들어 Geography 계층 구조는 Country, State, County 및 City에 대한 하위 수준을 포함할 수 있습니다. 계층 구조는 보고 클라이언트 응용 프로그램 필드 목록에서 다른 열과 별도로 표시될 수 있으므로 클라이언트 사용자가 보고서에서 쉽게 탐색 및 포함할 수 있습니다. 자세한 내용은 [계층 구조](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)를 참조하세요.
  
계층 구조를 만들려면 *다이어그램 뷰*에서 모델 디자이너를 사용합니다. 데이터 뷰에서 계층 구조 만들기 및 관리는 지원되지 않습니다.  
  
이 단원을 완료하기 위한 예상 시간: **20분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 8: 큐브 뷰 만들기](../tutorials/aas-lesson-8-create-perspectives.md)를 완료해야 합니다.  
  
## <a name="create-hierarchies"></a>계층 구조 만들기  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>DimProduct 테이블에 Category 계층 구조를 만들려면  
  
1.  모델 디자이너(다이어그램 뷰)에서 **DimProduct** 테이블 > **계층 구조 만들기**를 마우스 오른쪽 단추로 클릭합니다. 새 계층 구조가 테이블 창 맨 아래에 나타납니다. 계층 구조 이름을 **Category**로 변경합니다.  
  
2.  **ProductCategoryName** 열을 클릭하여 새 **Category** 계층 구조로 끌어옵니다.  
  
3.  **Category** 계층 구조에서 **ProductCategoryName** > **이름 바꾸기**를 마우스 오른쪽 단추로 클릭한 후 **Category**를 입력합니다.  
  
    > [!NOTE]  
    > 계층 구조에서 열 이름을 변경해도 테이블의 열 이름은 변경되지 않습니다. 계층 구조의 열은 테이블의 열을 표현한 것일 뿐입니다.  
  
4.  **ProductSubcategoryName** 열을 클릭하여 **Category** 계층 구조로 끌어옵니다. **Subcategory**로 이름을 바꿉니다. 
  
5.  **ModelName** 열 > **계층 구조에 추가**를 마우스 오른쪽 단추로 클릭한 후 **Category**를 선택합니다. **Model**로 이름을 바꿉니다.

6.  마지막으로, **EnglishProductName**을 Category 계층 구조에 추가합니다. **Product**로 이름을 바꿉니다.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>DimDate 테이블에서 계층 구조를 만들려면  
  
1.  **DimDate** 테이블에서 **Calendar**라는 계층 구조를 만듭니다.  
  
3.  다음 열을 순서대로 추가합니다.

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  **DimDate** 테이블에서 **Fiscal** 계층 구조를 만듭니다. 다음 열을 순서대로 포함합니다.  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  마지막으로, **DimDate** 테이블에서 **ProductionCalendar** 계층 구조를 만듭니다. 다음 열을 순서대로 포함합니다.  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>다음 작업
[단원 10: 파티션 만들기](../tutorials/aas-lesson-10-create-partitions.md) 
  
  
