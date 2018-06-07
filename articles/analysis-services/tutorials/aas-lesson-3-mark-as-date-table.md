---
title: 'Azure Analysis Services 자습서 단원 3: 날짜 테이블로 표시 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 날짜 테이블로 표시하는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1b7f6252faef02676be6ccb22653f5d4805020df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="mark-as-date-table"></a>날짜 테이블로 표시

2단원: 데이터 가져오기에서는 이름이 DimDate인 차원 테이블을 가져왔습니다. 모델에서 이 테이블의 이름은 DimDate이지만 날짜 및 시간 데이터가 포함되어 있으므로 *날짜 테이블*이라고도 합니다.  
  
DAX 시간 인텔리전스 함수를 사용할 때마다, 나중에 측정값을 만들 때와 같이 *날짜 테이블* 및 해당 테이블에 고유 식별자 *날짜 열*을 포함하는 속성을 지정해야 합니다.
  
이 단원에서는 DimDate 테이블을 *날짜 테이블*로, 날짜 열(날짜 테이블의)을 *날짜 열*(고유 식별자)로 표시합니다.  

날짜 테이블 및 날짜 열을 표시하기 전에 모델을 더 쉽게 이해하려면 약간 관리 유지 작업을 수행하는 것이 좋습니다. DimDate 테이블에서 **FullDateAlternateKey**라는 열을 확인합니다. 이 열은 테이블에 포함된 각 달력 연도의 모든 날에 대한 하나의 행을 포함합니다. 측정 수식 및 보고서에서 이 열을 많이 사용합니다. 하지만 실제로 FullDateAlternateKey는 이 열에 대한 적절한 식별자가 아닙니다. 수식에서 쉽게 식별하고 포함할 수 있도록 이름을 **Date**로 변경합니다. 가능하다면, 테이블 및 열과 같은 개체의 이름을 변경하여 SSDT 및 클라이언트 보고 응용 프로그램(Power BI 및 Excel)에서 쉽게 식별할 수 있도록 하는 것이 좋습니다. 
  
이 단원을 완료하기 위한 예상 시간: **3분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 2: 데이터 가져오기](../tutorials/aas-lesson-2-get-data.md)를 완료해야 합니다. 

### <a name="to-rename-the-fulldatealternatekey-column"></a>FullDateAlternateKey 열 이름을 바꾸려면

1.  모델 디자이너에서 **DimDate** 테이블을 클릭합니다.

2.  **FullDateAlternateKey** 열에 대한 머리글을 두 번 클릭하고 **Date**로 이름을 변경합니다.

  
### <a name="to-set-mark-as-date-table"></a>날짜 테이블로 표시를 설정하려면  
  
1.  **날짜** 열을 선택한 다음 **속성** 창의 **데이터 형식** 아래에서인 **Date**가 선택되어 있는지 확인합니다.  
  
2.  **테이블** 메뉴를 클릭한 다음 **날짜**를 클릭하고 **날짜 테이블로 표시**를 클릭합니다.  
  
3.  **날짜 테이블로 표시** 대화 상자의 **날짜** 목록 상자에서 **날짜** 열을 고유 식별자로 선택합니다. 보통은 기본적으로 선택되어 있습니다. **확인**을 클릭합니다. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>다음 작업
[단원 4: 관계 만들기](../tutorials/aas-lesson-4-create-relationships.md)
  
