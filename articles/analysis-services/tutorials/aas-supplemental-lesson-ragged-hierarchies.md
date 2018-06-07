---
title: 'Azure Analysis Services 자습서 추가 단원: 불규칙한 계층 구조 | Microsoft Docs'
description: Azure Analysis Services 자습서에서 불규칙한 계층 구조를 수정하는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 955bc57bbf5f8bc3f0d91350f885d5dc77db93a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>추가 단원 - 불규칙한 계층 구조

이 추가 단원에서는 다양한 수준에서 빈 값(멤버)을 포함하는 계층 구조를 피벗할 때 발생하는 일반적인 문제를 해결합니다. 예를 들어, 고위 관리자가 부하 직원으로 부서별 관리자와 비관리자를 포함하는 조직을 들 수 있습니다. 또는 워싱턴 D.C., 바티칸 시티처럼 일부 도시는 상위 시/도가 없는 국가-지역-도시로 구성된 지리적 계층 구조가 있습니다. 계층 구조에 빈 멤버가 있는 경우 보통 서로 다르거나 불규칙한 수준으로 내려갑니다.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

1400 호환성 수준의 테이블 형식 모델에는 계층 구조에 대한 추가 **멤버 숨기기** 속성이 있습니다. **기본** 설정에서는 어떤 수준에도 빈 멤버가 없다고 가정합니다. **빈 멤버 숨기기** 설정은 피벗 테이블 또는 보고서에 계층 구조가 추가된 경우 계층 구조에서 빈 멤버를 제외합니다.  
  
이 단원을 완료하기 위한 예상 시간: **20분**  
  
## <a name="prerequisites"></a>필수 조건  
이 추가 단원 항목은 테이블 형식 모델링 자습서에 포함됩니다. 이 추가 단원의 작업을 수행하기 전에 이전의 모든 단원을 완료하거나 완료된 Adventure Works Internet Sales 샘플 모델 프로젝트가 있어야 합니다. 

자습서의 일부로 AW Internet Sales 프로젝트를 만들었으면 모델에는 아직 데이터 또는 불규칙한 계층 구조가 포함되지 않습니다. 이 추가 단원을 완료하려면 먼저 몇 가지 테이블을 더 추가하고, 관계, 계산된 열, 측정값 및 새 조직 계층 구조를 만들어서 문제를 만들어야 합니다. 이 부분에는 약 15분 정도 걸립니다. 그런 다음 몇 분 안에 문제를 해결합니다.  

## <a name="add-tables-and-objects"></a>테이블 및 개체 추가
  
### <a name="to-add-new-tables-to-your-model"></a>모델에 새 테이블을 추가하려면
  
1.  테이블 형식 모델 탐색기에서 **데이터 원본**을 확장한 후 연결을 마우스 오른쪽 단추로 클릭한 다음 > **새 테이블 가져오기**를 클릭합니다.
  
2.  탐색기에서 **DimEmployee** 및 **FactResellerSales**를 클릭하고 **확인**을 클릭합니다.

3.  쿼리 편집기에서 **가져오기**를 클릭합니다.

4.  다음 [관계](../tutorials/aas-lesson-4-create-relationships.md)를 만듭니다.

    | 표 1           | 열       | 필터 방향   | 표 2     | 열      | Active |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | 기본값            | DimDate     | Date        | 예    |
    | FactResellerSales | DueDate      | 기본값            | DimDate     | Date        | 아니오     |
    | FactResellerSales | ShipDateKey  | 기본값            | DimDate     | Date        | 아니오     |
    | FactResellerSales | ProductKey   | 기본값            | DimProduct  | ProductKey  | 예    |
    | FactResellerSales | EmployeeKey  | 두 테이블로 | DimEmployee | EmployeeKey | 예    |

5. **DimEmployee** 테이블에서 다음 [계산된 열](../tutorials/aas-lesson-5-create-calculated-columns.md)을 만듭니다. 

    **Path** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  **DimEmployee** 테이블에서 **Organization**이라는 이름의 [계층 구조](../tutorials/aas-lesson-9-create-hierarchies.md)를 만듭니다. 다음 열을 순서대로 추가합니다. **Level1**, **Level2**, **Level3**, **Level4**, **Level5**

7.  **FactResellerSales** 테이블에서 다음 [측정값](../tutorials/aas-lesson-6-create-measures.md)을 만듭니다.

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  [Excel에서 분석](../tutorials/aas-lesson-12-analyze-in-excel.md)을 사용하여 Excel을 열고 피벗 테이블을 자동으로 만듭니다.

9.  **피벗 테이블 필드**에서 **DimEmployee** 테이블의 **Organization** 계층 구조를 **Rows**에 추가하고 **FactResellerSales** 테이블의 **ResellerTotalSales** 측정값을 **Values**에 추가합니다.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    피벗 테이블에서 볼 수 있는 것처럼 계층 구조에 불규칙한 행이 표시됩니다. 빈 멤버가 표시된 많은 행이 있습니다.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>멤버 숨기기 속성을 설정하여 불규칙한 계층 구조를 수정하려면

1.  **테이블 형식 모델 탐색기**에서 **테이블** > **DimEmployee** > **계층 구조** > **조직**을 확장합니다.

2.  **속성** > **멤버 숨기기**에서 **빈 멤버 숨기기**를 선택합니다. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  다시 Excel에서 피벗 테이블을 새로 고칩니다. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    이제 훨씬 좋아 보입니다.

## <a name="see-also"></a>참고 항목   
[단원 9: 계층 구조 만들기](../tutorials/aas-lesson-9-create-hierarchies.md)  
[추가 단원 - 동적 보안](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[추가 단원 - 세부 정보 행](../tutorials/aas-supplemental-lesson-detail-rows.md)  
