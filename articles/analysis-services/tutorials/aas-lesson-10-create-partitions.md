---
title: 'Azure Analysis Services 자습서 단원 10: 파티션 만들기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 파티션을 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 743eff21154502fc4ff651177abb8df91edb7494
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-partitions"></a>파티션 만들기

이 단원에서는 FactInternetSales 테이블을 다른 파티션과 독립적으로 처리(새로 고침)할 수 있는 더 작은 논리적 부분으로 나누는 파티션을 만듭니다. 기본적으로 모델에 포함된 모든 테이블에는 테이블의 행과 열을 모두 포함하는 한 개의 파티션이 있습니다. FactInternetSales 테이블에 대해, 5년마다 한 개의 파티션으로, 연도별로 데이터를 나누려고 합니다. 그러면 각 파티션은 독립적으로 처리할 수 있습니다. 자세한 내용은 [파티션](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular)을 참조하세요. 
  
이 단원을 완료하기 위한 예상 시간: **15분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 9: 계층 구조 만들기](../tutorials/aas-lesson-9-create-hierarchies.md)를 완료해야 합니다.  
  
## <a name="create-partitions"></a>파티션 만들기  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>FactInternetSales 테이블에서 파티션을 만들려면  
  
1.  테이블 형식 모델 탐색기에서 **테이블**를 확장하고 **FactInternetSales** > **파티션**을 마우스 오른쪽 단추로 클릭합니다.  
  
2.  파티션 관리자에서 **복사**를 클릭하고 이름을 **FactInternetSales2010**으로 변경합니다.
  
    파티션에 2010년이라는 특정 기간 내의 행만 포함하도록 하고 싶으므로 쿼리 식을 수정해야 합니다.
  
4.  **디자인**을 클릭하여 쿼리 편집기를 열고 **FactInternetSales2010** 쿼리를 클릭합니다.

5.  미리 보기에서 **OrderDate** 열 머리글의 아래쪽 화살표를 클릭한 후 **날짜/시간 필터** > **사이**를 클릭합니다.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  행 필터 대화 상자의 **찾을 조건: OrderDate**에서 **이후 또는 같음**을 그대로 두고 날짜 필드에서 **1/1/2010**을 입력합니다. **And** 연산자를 선택한 상태로 **이전**을 선택하고 날짜 필드에서 **1/1/2011**을 입력한 후 **확인**을 클릭합니다.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    쿼리 편집기의 적용 단계에서 필터링된 행이라는 다른 단계가 표시됩니다. 이 필터는 2010에서 주문 날짜만 선택하는 것입니다.

8.  **가져오기**를 클릭합니다.

    이제 파티션 관리자에서, 쿼리 식에 필터링된 행 절이 추가로 포함된 것을 알 수 있습니다.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    이 문은 파티션이 필터링된 행 절에 지정된 대로 OrderDate가 2010년인 행의 데이터만 포함하도록 지정합니다.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>2011년에 대한 파티션을 만들려면  
  
1.  파티션 목록에서 생성한 **FactInternetSales2010** 파티션을 클릭하고 **복사**를 클릭합니다.  파티션 이름을 **FactInternetSales2011**로 변경합니다. 

    새 필터링된 행 절을 만드는 데 쿼리 편집기를 사용하지 않아도 됩니다. 2010년에 대한 쿼리 복사본을 만들었으므로 2011년에 대한 쿼리는 약간만 변경하면 됩니다.
  
2.  **쿼리 식**에서 이 파티션이 2011년에 대한 행만 포함하도록 하려면 필터링된 행 절에서 연도를 다음과 같이 각각 **2011** 및 **2012**로 바꿉니다.  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>2012, 2013 및 2014년에 대한 파티션을 만들려면  
  
- 이전 단계에 따라 2012, 2013 및 2014년에 대한 파티션을 만들고 해당 연도에 대한 행만 포함하도록 필터링된 행 절에서 연도를 변경합니다. 
  

## <a name="delete-the-factinternetsales-partition"></a>FactInternetSales 파티션 삭제
이제 각 연도에 대한 파티션이 생겼고 FactInternetSales 파티션을 삭제하여 파티션 처리 시 모두 처리를 선택했을 때 겹침을 방지합니다.

#### <a name="to-delete-the-factinternetsales-partition"></a>FactInternetSales 파티션을 삭제하려면
-  FactInternetSales 파티션을 클릭한 다음 **삭제**를 클릭합니다.



## <a name="process-partitions"></a>파티션 처리  
파티션 관리자에서, 생성한 새로운 파티션 각각에 대한 **마지막 처리** 열에 이러한 파티션이 처리되지 않은 것을 확인할 수 있습니다. 파티션을 만들 때, 해당 파티션에서 데이터를 새로 고치려면 파티션 처리 또는 테이블 처리 작업을 실행해야 합니다.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>FactInternetSales 파티션을 처리하려면  
  
1.  **확인**을 클릭하여 파티션 관리자를 닫습니다.  
  
2.  **FactInternetSales** 테이블을 클릭한 후 **모델** 메뉴 > **처리** > **파티션 처리**를 클릭합니다.  
  
3.  파티션 처리 대화 상자에서 **모드**가 **기본값 처리**로 설정되어 있는지 확인합니다.  
  
4.  생성한 5개의 파티션 각각에 대한 **처리** 열에서 확인란을 선택한 후 **확인**을 클릭합니다.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    가장 자격 증명을 묻는 메시지가 표시되면 단원 2에서 지정한 Windows 사용자 이름 및 암호를 입력합니다.  
  
    **데이터 처리** 대화 상자가 나타나고 각 파티션에 대한 처리 정보가 표시됩니다. 파티션마다 서로 다른 행 수가 전송된 것을 알 수 있습니다. 각 파티션은 SQL 문의 WHERE 절에 지정된 연도의 행만 포함합니다. 처리가 완료되면 계속 진행하고 데이터 처리 대화 상자를 닫습니다.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>다음 작업
다음 단원 [단원 11: 역할 만들기](../tutorials/aas-lesson-11-create-roles.md)로 이동합니다. 
