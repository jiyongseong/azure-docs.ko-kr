---
title: 'Azure Analysis Services 자습서 단원 8: 큐브 뷰 만들기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 큐브 뷰를 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb6599fefaa03e2ca0fec2788dca27240dbad8c6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-perspectives"></a>큐브 뷰 만들기

이 단원에서는 인터넷 판매 큐브 뷰를 만듭니다. 큐브 뷰는 포커스가 있는, 비즈니스 또는 응용 프로그램별 관점을 제공하는 모델의 볼 수 있는 하위 집합을 정의합니다. 사용자가 큐브 뷰를 사용하여 모델에 연결하면 해당 큐브 뷰에 정의된 필드로 해당 모델 개체(테이블, 열, 측정값, 계층 구조 및 KPI)만 표시됩니다. 자세한 내용은 [큐브 뷰](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular)를 참조하세요.
  
이 단원에서 만드는 인터넷 판매 큐브 뷰에서는 DimCustomer 테이블 개체가 제외됩니다. 뷰에서 특정 개체가 제외된 큐브 뷰를 만드는 경우 이 개체는 모델에 계속 존재합니다. 그러나 보고 클라이언트 필드 목록에 표시되지 않습니다. 큐브 뷰에 포함되었거나 포함되지 않은 계산된 열과 측정값은 여전히 제외된 개체 데이터에서 계산할 수 있습니다.  
  
이 단원의 목적은 큐브 뷰를 만드는 방법을 설명하고 사용자가 테이블 형식 모델 작성 도구를 습득하도록 하는 것입니다. 나중에 다른 테이블을 포함하도록 이 모델을 확장하면 모델의 다양한 관점(예: 재고 및 판매)을 정의하는 추가 큐브 뷰를 만들 수 있습니다.  
  
이 단원을 완료하기 위한 예상 시간: **5분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 7: 핵심 성과 지표 만들기](../tutorials/aas-lesson-7-create-key-performance-indicators.md)를 완료해야 합니다.  
  
## <a name="create-perspectives"></a>큐브 뷰 만들기  
  
#### <a name="to-create-an-internet-sales-perspective"></a>인터넷 판매 큐브 뷰를 만들려면  
  
1.  **모델** 메뉴 > **큐브 뷰** > **만들기 및 관리**를 클릭합니다.  
  
2.  **큐브 뷰** 대화 상자에서 **새 큐브 뷰**를 클릭합니다.  
  
3.  **새 큐브 뷰** 열 머리글을 두 번 클릭한 후 **Internet Sales**로 이름을 변경합니다.  
  
4.  **DimCustomer**를 *제외*하고 모든 테이블을 선택합니다.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    이후 단원에서는 Excel에서 분석 기능을 사용하여 이 큐브 뷰를 테스트합니다. Excel 피벗 테이블 필드 목록에는 DimCustomer 테이블을 제외한 각 테이블이 포함됩니다.  

## <a name="whats-next"></a>다음 작업
[단원 9: 계층 구조 만들기](../tutorials/aas-lesson-9-create-hierarchies.md)
  
  
  
  
