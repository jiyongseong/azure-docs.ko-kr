---
title: 'Azure Analysis Services 자습서 단원 4: 관계 만들기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 관계를 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 694043454ba2564900416135e1f143bdeb63c202
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-relationships"></a>관계 만들기

이 단원에서는 데이터를 가져오고 서로 다른 테이블 간에 새 관계를 추가할 때 자동으로 생성된 관계를 확인합니다. 관계는 해당 테이블의 데이터가 상호 관련되는 방식을 설정하는 두 테이블 간의 연결입니다. 예를 들어 DimProduct 테이블과 DimProductSubcategory 테이블은 각 제품이 하위 범주에 속한다는 사실에 따라 관계를 포함합니다. 자세한 내용은 [관계](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular)를 참조하세요.
  
이 단원을 완료하기 위한 예상 시간: **10분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 3: 날짜 테이블로 표시](../tutorials/aas-lesson-3-mark-as-date-table.md)를 모두 완료해야 합니다. 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>기존 관계 검토 및 새 관계 추가  
데이터 가져오기를 사용하여 데이터를 가져올 때 AdventureWorksDW2014 데이터베이스에서 7개의 테이블을 가져왔습니다. 일반적으로 관계형 원본에서 데이터를 가져올 때 데이터와 함께 기존 관계가 자동으로 가져오기 됩니다. 데이터 가져오기가 데이터 모델에 자동으로 관계를 만들려면 데이터 원본에 있는 테이블 간 관계가 있어야 합니다.

모델 작성을 진행하기 전에 테이블 간의 관계가 적절히 생성되었는지 확인해야 합니다. 이 자습서에서는 3가지 새로운 관계를 추가합니다.  

  
#### <a name="to-review-existing-relationships"></a>기존 관계를 검토하려면  
  
1.  **모델** 메뉴 > **모델 뷰** > **다이어그램 뷰**를 클릭합니다.  

    이제 모델 디자이너가 다이어그램 뷰에 표시됩니다. 다이어그램 뷰는 가져온 모든 테이블을 테이블 간의 선으로 표시하는 그래픽 형식입니다. 테이블 간의 선은 데이터를 가져올 때 자동으로 생성된 관계를 나타냅니다.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > 테이블 간의 관계가 표시되지 않으면, 데이터 원본에서 해당 테이블 간의 관계가 없음을 의미할 가능성이 높습니다.

    모델 디자이너의 오른쪽 아래 모서리에 있는 미니맵 컨트롤을 사용하여 가능한 많은 테이블을 포함합니다. 테이블을 다른 위치로 클릭하여 끌어 가깝게 가져오거나 특정 순서로 배치할 수도 있습니다. 테이블을 이동해도 테이블 간 관계에는 영향을 주지 않습니다. 특정 테이블의 모든 열을 보려면 테이블 가장자리를 클릭하고 끌어 확장하거나 축소합니다.  
  
2.  **DimCustomer** 테이블 및 **DimGeography** 테이블 사이의 실선을 클릭합니다. 이러한 두 테이블 간의 실선은 이 관계가 활성 상태임을 나타내므로 기본적으로 DAX 수식을 계산할 때 사용됩니다.  
  
    이제 **DimCustomer** 테이블 **GeographyKey** 열 및 **DimGeography** 테이블의 **GeographyKey** 열이 모두 각각 상자 내에 표시됩니다. 이러한 열은 관계에 사용됩니다. 관계의 속성도 **속성** 창에 표시됩니다.  
  
    > [!TIP]  
    > 다이어그램 뷰에서 모델 디자이너를 사용하는 것 외에도 테이블 형식으로 모든 테이블 간에 관계를 표시하려면 관계 관리 대화 상자를 사용할 수도 있습니다. 테이블 형식 모델 탐색기에서 **관계** > **관계 관리**를 마우스 오른쪽 단추로 클릭합니다.
  
3.  AdventureWorksDW 데이터베이스에서 각 테이블을 가져올 때 다음 관계가 생성되었는지 확인합니다.  
  
    |Active|테이블|관련된 조회 테이블|  
    |----------|---------|------------------------|  
    |예|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |예|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |예|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |예|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |예|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    관계가 없는 경우 사용자 모델에 DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory 및 FactInternetSales 테이블이 포함되어 있는지 확인합니다. 동일한 데이터 원본 연결의 테이블을 별도의 시간에 가져오는 경우 해당 테이블 간의 관계가 만들어지지 않으므로 수동으로 만들어야 합니다. 관계 없음이 표시되는 경우 데이터 원본에 관계가 없음을 의미합니다. 데이터 모델에서 수동으로 만들 수 있습니다.

### <a name="take-a-closer-look"></a>자세히 보기
다이어그램 뷰에는 화살표, 별표 및 테이블 간의 관계를 보여 주는 선 수를 알 수 있습니다.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

화살표는 필터 방향을 나타냅니다. 별표는 이 테이블이 관계의 카디널리티에서 많은 면인 것을 보여 주며, 1은 이 테이블이 관계에서 한 면인 것을 보여 줍니다. 관계를 편집해야 하는 경우, 예를 들어 관계의 필터 방향 또는 카디널리티를 변경하는 경우 관계 선을 두 번 클릭하여 관계 편집 대화 상자를 엽니다.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

이러한 기능은 고급 데이터 모델링에 대한 내용이며 이 자습서에서는 다루지 않습니다. 자세한 내용은 [Analysis Services에서 테이블 형식 모델에 대한 양방향 크로스 필터](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services)를 참조하세요.

일부 경우 특정 비즈니스 논리를 지원하기 위해서는 모델에서 테이블 간의 추가 관계를 만들어야 할 수 있습니다. 이 자습서에서는 FactInternetSales 테이블 및 DimDate 테이블 간에 세 개의 관계를 만들어야 합니다.  
  
#### <a name="to-add-new-relationships-between-tables"></a>테이블 간에 새로운 관계를 추가하려면  
  
1.  모델 디자이너의 **FactInternetSales** 테이블에서 **OrderDate** 열을 클릭한 채로 커서를 **DimDate** 테이블의 **Date** 열로 끌어다 놓습니다.  

    **Internet Sales** 테이블의 **OrderDate** 열과 **Date** 테이블의 **Date** 열 간에 활성 관계가 만들어졌음을 보여 주는 실선이 나타납니다. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > 관계를 만들 때는 기본 테이블과 관련된 조회 테이블 간에 카디널리티 및 필터 방향이 자동으로 선택됩니다.  
  
2.  **FactInternetSales** 테이블에서 **DueDate** 열을 클릭한 채로 커서를 **DimDate** 테이블의 **Date** 열로 끌어다 놓습니다.  
  
    **FactInternetSales** 테이블의 **DueDate** 열과 **DimDate** 테이블의 **Date** 열 간에 비활성 관계가 만들어졌음을 보여 주는 점선이 나타납니다. 테이블 간에 여러 관계를 포함할 수 있지만 한 번에 하나의 관계만 활성화될 수 있습니다. 사용자 지정 DAX 식에서 특별한 집계를 수행하기 위해 비활성 관계를 활성화할 수 있습니다.  
  
3.  마지막으로 관계를 하나 더 만듭니다. **FactInternetSales** 테이블에서 **ShipDate** 열을 클릭한 채로 커서를 **DimDate** 테이블의 **Date** 열로 끌어다 놓습니다.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>다음 작업
[단원 5: 계산된 열 만들기](../tutorials/aas-lesson-5-create-calculated-columns.md)
  
  
  
