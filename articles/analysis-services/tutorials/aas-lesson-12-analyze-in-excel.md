---
title: 'Azure Analysis Services 자습서 단원 12: Excel에서 분석 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 Excel에서 분석을 사용하는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 17dba7be2acab18108dc50a5c16d55e0e720cbcc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-in-excel"></a>Excel에서 분석

이 단원에서는 Excel에서 분석 기능을 사용하여 Microsoft Excel을 열고 모델 작업 영역에 대한 연결을 자동으로 만들고 피벗 테이블을 워크시트에 자동으로 추가합니다. Excel에서 분석 기능은 모델을 배포하기 전에 모델 디자인의 효율성을 테스트하기 위한 쉽고 빠른 방법을 제공합니다. 이 단원에서는 어떠한 데이터 분석도 수행하지 않습니다. 이 단원의 목적은 모델 작성자인 사용자가 모델 디자인을 테스트하는 데 사용할 수 있는 도구를 습득하게 하는 것입니다.   
  
이 단원을 완료하려면 Excel이 Visual Studio와 동일한 컴퓨터에 설치되어 있어야 합니다.
  
이 단원을 완료하기 위한 예상 시간: **5분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 11: 역할 만들기](../tutorials/aas-lesson-11-create-roles.md)를 완료해야 합니다.  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>기본 및 인터넷 판매 큐브 뷰를 사용하여 찾아보기  
다음 첫 번째 작업에서는 모든 모델 개체를 포함하는 기본 큐브 뷰와 이전의 인터넷 판매 큐브 뷰를 모두 사용하여 모델을 찾아봅니다. 인터넷 판매 큐브 뷰에는 고객 테이블 개체가 제외됩니다.  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>기본 큐브 뷰를 사용하여 찾아보려면  
  
1.  **모델** 메뉴 > **Excel에서 분석**을 클릭합니다.  
  
2.  **Excel에서 분석** 대화 상자에서 **확인**을 클릭합니다.  
  
    Excel에서 새 통합 문서가 열립니다. 데이터 원본 연결이 현재 사용자 계정을 사용하여 생성되고 볼 수 있는 필드를 정의하는 데 기본 큐브 뷰가 사용됩니다. 피벗 테이블은 워크시트에 자동으로 추가됩니다.  
  
3.  Excel에서 **피벗 테이블 필드 목록**에 **DimDate** 및 **FactInternetSales** 측정값 그룹이 나타납니다. 해당 열과 함께 **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** 및 **FactInternetSales** 테이블도 나타납니다.  
  
4.  통합 문서를 저장하지 않고 Excel을 닫습니다.  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>인터넷 판매 큐브 뷰를 사용하여 찾아보려면  
  
1.  **모델** 메뉴를 클릭한 후 **Excel에서 분석**을 클릭합니다.  
  
2.  **Excel에서 분석** 대화 상자에서 **현재 Windows 사용자**가 선택된 상태로 두고 **큐브 뷰** 드롭다운 목록 상자에서 **인터넷 판매**를 선택한 후 **확인**을 클릭합니다. 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  Excel의 **피벗 테이블 필드**에서 DimCustomer 테이블이 필드 목록에서 제외되는 것을 확인합니다.  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  통합 문서를 저장하지 않고 Excel을 닫습니다.  
  
## <a name="browse-by-using-roles"></a>역할을 사용하여 찾아보기  
역할은 테이블 형식 모델의 중요한 부분입니다. 사용자가 멤버로 추가되는 역할이 하나도 경우 사용자는 모델을 사용하여 데이터에 액세스 및 분석할 수 없습니다. Excel에서 분석 기능은 정의한 역할을 테스트하는 방법을 제공합니다.  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Sales Manager 사용자 역할을 사용하여 찾아보려면  
  
1.  SSDT에서 **모델** 메뉴를 클릭한 후 **Excel에서 분석**을 클릭합니다.  
  
2.  **모델에 연결하기 위해 사용할 사용자 이름 또는 역할 지정**에서 **역할**을 선택한 후 드롭다운 목록 상자에서 **Sales Manager**를 선택한 후 **확인**을 클릭합니다.  
  
    Excel에서 새 통합 문서가 열립니다. 피벗 테이블이 자동으로 만들어집니다. 피벗 테이블 필드 목록에는 새 모델에 사용할 수 있는 모든 데이터 필드가 포함됩니다.  
      
3.  통합 문서를 저장하지 않고 Excel을 닫습니다.  
  
## <a name="whats-next"></a>다음 작업
다음 단원인 [단원 13: 배포](../tutorials/aas-lesson-13-deploy.md)로 이동합니다.

  
  
  
