---
title: 'Azure Analysis Services 자습서 단원 11: 역할 만들기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 역할을 만드는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7a34884482a53a9b96dac87dc30803be08fdc806
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-roles"></a>역할 만들기

이 단원에서는 역할을 만듭니다. 역할은 역할 멤버인 Sa 사용자로만 액세스를 제한하여 모델 데이터베이스 개체 및 데이터 보안을 제공합니다. 각 역할은 단일 사용 권한(없음, 읽기, 읽기 및 프로세스, 프로세스 또는 관리자)으로 정의됩니다. 역할 관리자를 사용하여 모델 작성 중에 역할을 정의할 수 있습니다. 모델을 배포한 후에는 SSMS(SQL Server Management Studio)를 사용하여 역할을 관리할 수 있습니다. 자세한 내용은 [역할](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular)을 참조하세요.
  
> [!NOTE]  
> 이 자습서를 완료하는 데 역할 만들기가 반드시 필요한 것은 아닙니다. 기본적으로 현재 로그인한 계정은 모델에 대해 관리자 권한을 포함하게 됩니다. 그러나 조직의 다른 사용자가 보고 클라이언트를 사용하여 탐색하도록 하려면 읽기 권한을 가진 역할을 하나 이상 만들고 해당 사용자를 멤버로 추가해야 합니다.  
  
세 가지 역할을 만듭니다.  
  
-   **Sales Manager** – 이 역할에는 모든 모델 개체 및 데이터에 대해 읽기 권한을 보유하도록 하려는 조직의 사용자가 포함될 수 있습니다.  
  
-   **Sales Analyst US** – 이 역할에는 미국 내 판매와 관련된 데이터를 찾아볼 수 있도록 하려는 조직의 사용자가 포함될 수 있습니다. 이 역할의 경우 DAX 수식을 사용하여 멤버가 미국에 대한 데이터만 찾아보도록 제한하는 *행 필터*를 정의합니다.  
  
-   **Administrator** – 이 역할에는 모델 데이터베이스에 대해 관리 작업을 수행하도록 무제한 액세스 및 권한을 허용하는 관리자 권한을 가진 사용자가 포함될 수 있습니다.  
  
조직의 Windows 사용자 및 그룹 계정은 고유하므로 특정 조직에서 멤버로 계정을 추가할 수 있습니다. 그러나 이 자습서에서는 멤버를 비워 둬도 됩니다. 나중에 단원 12: Excel에서 분석에서 각 역할의 효과를 테스트합니다.  
  
이 단원을 완료하기 위한 예상 시간: **15분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 10: 파티션 만들기](../tutorials/aas-lesson-10-create-partitions.md)를 완료해야 합니다.  
  
## <a name="create-roles"></a>역할 만들기  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Sales Manager 사용자 역할을 만들려면  
  
1.  테이블 형식 모델 탐색기에서 **역할** > **역할**을 마우스 오른쪽 단추로 클릭합니다.  
  
2.  역할 관리자에서 **새로 만들기**를 클릭합니다.  
  
3.  새 역할을 클릭한 후 **이름** 열에서 역할의 이름을 **Sales Manager**로 변경합니다.  
  
4.  **사용 권한** 열에서 드롭다운 목록을 클릭한 후 **읽기** 권한을 선택합니다. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  선택 사항: **멤버** 탭을 클릭한 다음 **추가**를 클릭합니다. **사용자 또는 그룹 선택** 대화 상자에서 역할에 포함하려는 조직에서 Windows 사용자 또는 그룹을 입력합니다.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Sales Analyst US 사용자 역할을 만들려면  
  
1.  역할 관리자에서 **새로 만들기**를 클릭합니다.    
  
2.  역할의 이름을 **Sales Analyst US**로 변경합니다.  
  
3.  이 역할에 **읽기** 권한을 부여합니다.  
  
4.  행 필터 탭을 클릭한 다음 **DimGeography** 테이블에 대한 DAX 필터 열에 다음 수식을 입력합니다.  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    행 필터 수식은 부울(TRUE/FALSE) 값으로 확인되어야 합니다. 이 수식을 사용하여 Country Region Code 값이 “US”인 행만 사용자에게 표시되도록 지정합니다.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  선택 사항: **멤버** 탭을 클릭한 다음 **추가**를 클릭합니다. **사용자 또는 그룹 선택** 대화 상자에서 역할에 포함하려는 조직에서 Windows 사용자 또는 그룹을 입력합니다.  
  
#### <a name="to-create-an-administrator-user-role"></a>Administrator 사용자 역할을 만들려면  
  
1.  **새로 만들기**를 클릭합니다.  
  
2.  역할의 이름을 **Administrator**로 변경합니다.  
  
3.  이 역할에 **관리자** 권한을 부여합니다.  
  
4.  선택 사항: **멤버** 탭을 클릭한 다음 **추가**를 클릭합니다. **사용자 또는 그룹 선택** 대화 상자에서 역할에 포함하려는 조직에서 Windows 사용자 또는 그룹을 입력합니다. 
  
  
## <a name="whats-next"></a>다음 작업
[단원 12: Excel에서 분석](../tutorials/aas-lesson-12-analyze-in-excel.md)

  
  
