---
title: 'Azure Analysis Services 자습서 단원 2: 데이터 가져오기 | Microsoft Docs'
description: Azure Analysis Services 자습서 프로젝트에서 데이터를 가져오는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: be3368b5207669d7cc5b03e7eeb6953b757c786d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="get-data"></a>데이터 가져오기

이 단원에서는 SSDT의 데이터 가져오기를 사용하여 Adventure Works 샘플 데이터베이스에 연결하고, 데이터, 미리 보기 및 필터를 선택한 다음, 모델 작업 영역으로 가져옵니다.  
  
데이터 가져오기를 사용하여 Azure SQL Database, Oracle, Sybase, OData Feed, Teradata, 파일 등 다양한 원본에서 데이터를 가져올 수 있습니다. 파워 쿼리 M 수식을 사용하여 데이터를 쿼리할 수도 있습니다.

> [!NOTE]
> 이 자습서의 작업 및 이미지는 온-프레미스 서버에서 AdventureWorksDW2014 데이터베이스에 대한 연결을 보여 줍니다. 경우에 따라 Azure의 Adventure Works 데이터베이스가 다를 수 있습니다.
  
이 단원을 완료하기 위한 예상 시간: **10분**  
  
## <a name="prerequisites"></a>필수 조건  
이 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 1: 새 테이블 형식 모델 프로젝트 만들기](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)를 완료해야 합니다.  
  
## <a name="create-a-connection"></a>연결 만들기  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>AdventureWorksDW2014 데이터베이스에 대한 연결을 만들려면  
  
1.  테이블 형식 모델 탐색기에서 **데이터 원본** > **데이터 원본에서 가져오기**를 마우스 오른쪽 단추로 클릭합니다.  
  
    그러면 데이터 가져오기가 시작되고 데이터 원본에 연결하는 과정을 안내합니다. 테이블 형식 모델 탐색기가 표시되지 않으면 **솔루션 탐색기**에서 **Model.bim**을 두 번 클릭하여 디자이너에서 모델을 엽니다. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  데이터 가져오기에서 **데이터베이스** > **SQL Server 데이터베이스** > **연결**을 클릭합니다.  
  
3.  **SQL Server 데이터베이스** 대화 상자의 **서버**에서 AdventureWorksDW2014 데이터베이스를 설치한 서버 이름을 입력한 후 **연결**을 클릭합니다.  

4.  자격 증명을 입력하라는 메시지가 표시되면 데이터를 가져오고 처리할 때 Analysis Services가 데이터 원본에 연결하는 데 사용할 자격 증명을 지정해야 합니다. **가장 모드**에서 **계정 가장**을 선택한 다음 자격 증명을 입력하고 **연결**을 클릭합니다. 암호 만료되지 않은 계정을 사용하는 것이 좋습니다.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Windows 사용자 계정 및 암호를 사용하면 데이터 원본에 연결하는 가장 안전한 방법이 제공됩니다.
  
5.  탐색기에서 **AdventureWorksDW2014** 데이터베이스를 선택한 다음 **확인**을 클릭합니다. 그러면 데이터베이스에 대한 연결이 생성됩니다. 
  
6.  탐색기에서 다음 테이블에 대한 확인란을 선택합니다. **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** 및 **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
확인을 클릭하면 쿼리 편집기가 열립니다. 다음 섹션에서 가져오려는 데이터만 선택합니다.

  
## <a name="filter-the-table-data"></a>테이블 데이터 필터링  
AdventureWorksDW2014 예제 데이터베이스의 테이블에는 모델에 포함할 필요가 없는 데이터가 있습니다. 가능하면, 모델에 사용된 메모리 내 공간을 절약하기 위해 불필요한 데이터를 필터링하려고 합니다. 테이블에서 열의 일부를 필터링하여 작업 영역 데이터베이스나 모델 데이터베이스(배포 후)로 가져오지 못하게 합니다. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>가져오기 전에 테이블 데이터를 필터링하려면  
  
1.  쿼리 편집기에서 **DimCustomer** 테이블을 선택합니다. 데이터 원본(AdventureWorksDW2014 예제 데이터베이스)에서 DimCustomer 테이블 뷰가 나타납니다. 
  
2.  **SpanishEducation**, **FrenchEducation**, **SpanishOccupation**, **FrenchOccupation**을 다중 선택(Ctrl + 클릭)한 후 마우스 오른쪽 단추를 클릭한 후 **열 제거**를 클릭합니다. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    이러한 열에 대한 값은 인터넷 판매 분석과 관련이 없으므로 이러한 열을 가져올 필요가 없습니다. 불필요한 열을 제거하여 모델을 더 작고 효율적으로 만듭니다.  

    > [!TIP]
    > 실수한 경우 **적용된 단계**에서 단계를 삭제하여 백업할 수 있습니다.   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  각 테이블에서 다음 열을 제거하여 나머지 테이블을 필터링합니다.  
    
    **DimDate**
    
      |열|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |열|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |열|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |열|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |열|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      제거된 열이 없습니다.
  
## <a name="Import"></a>선택한 테이블 및 열 데이터 가져오기  
이제 불필요한 데이터를 미리보고 필터링했으며 원하는 나머지 데이터를 가져올 수 있습니다. 마법사가 테이블 간의 관계와 함께 테이블 데이터를 가져옵니다. 모델에 새 테이블 및 열이 생성되고 필터링된 데이터는 가져오지 않습니다.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>선택한 테이블 및 열 데이터를 가져오려면  
  
1.  선택 항목을 검토합니다. 잘못된 항목이 없으면, **가져오기**를 클릭합니다. 데이터 처리 대화 상자에 데이터 원본에서 작업 영역 데이터베이스로 가져온 데이터 상태가 표시됩니다.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  **닫기**를 클릭합니다.  

  
## <a name="save-your-model-project"></a>모델 프로젝트를 저장합니다.  
모델 프로젝트를 자주 저장해야 합니다.  
  
#### <a name="to-save-the-model-project"></a>모델 프로젝트를 저장하려면  
  
-   **파일** > **모두 저장**을 클릭합니다.  
  
## <a name="whats-next"></a>다음 작업
[단원 3: 날짜 테이블로 표시](../tutorials/aas-lesson-3-mark-as-date-table.md)

  
  
