---
title: 'Azure Analysis Services 자습서 추가 단원: 동적 보안 | Microsoft Docs'
description: Azure Analysis Services 자습서의 행 필터를 사용하여 동적 보안을 사용하는 방법에 대해 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5084bb0785e22a4b38f7f80f4c3b29a0ad92dadb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="supplemental-lesson---dynamic-security"></a>추가 단원 - 동적 보안

이 추가 단원에서는 동적 보안을 구현하는 추가 역할을 만듭니다. 동적 보안은 현재 로그온한 사용자의 사용자 이름 또는 로그인 ID를 기반으로 행 수준 보안을 제공합니다. 
  
동적 보안을 구현하려면 모델에 연결하여 모델 개체 및 데이터를 찾아볼 수 있는 사용자의 사용자 이름이 들어있는 모델에 테이블을 추가합니다. 이 자습서를 사용하여 만드는 모델은 Adventure Works의 컨텍스트에 있지만 이 단원을 완료하려면 사용자 고유의 도메인에서 사용자가 포함된 테이블을 추가해야 합니다. 추가된 사용자 이름에 대한 암호는 필요하지 않습니다. 사용자 고유 도메인의 작은 사용자 샘플과 함께 EmployeeSecurity 테이블을 만들려면 붙여넣기 기능을 사용하여 Excel 스프레드시트에서 직원 데이터를 붙여넣습니다. 실제 시나리오에서는 사용자 이름이 들어있는 테이블이 일반적으로 데이터 원본으로 사용되는 실제 데이터베이스의 테이블입니다(예: 실제 DimEmployee 테이블).  
  
동적 보안을 구현하려면 두 개의 DAX 함수 즉, [USERNAME 함수(DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) 및 [LOOKUPVALUE 함수(DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab)를 사용합니다. 행 필터 수식에 적용된 이러한 함수는 새 역할에 정의됩니다. LOOKUPVALUE 함수를 사용하여 수식은 EmployeeSecurity 테이블에서 값을 지정합니다. 그런 다음 수식은 이 역할에 속해 있는 로그온한 사용자의 사용자 이름을 지정하는 USERNAME 함수에 해당 값을 전달합니다. 그러면 사용자는 역할의 행 필터에 지정된 데이터만 찾을 수 있습니다. 이 시나리오에서는 영업 직원이 자신이 속한 판매 지역의 인터넷 판매 데이터만 찾아볼 수 있도록 지정합니다.  
  
이러한 작업은 이 Adventure Works 테이블 형식 모델 시나리오에는 고유하지만 실제 시나리오에 반드시 적용되는 것은 아닙니다. 각 작업에는 작업의 목적을 설명하는 추가 정보가 포함됩니다.  
  
이 단원을 완료하기 위한 예상 시간: **30분**  
  
## <a name="prerequisites"></a>필수 조건  
이 추가 단원 항목은 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 추가 단원의 작업을 수행하기 전에 이전의 단원을 모두 완료해야 합니다.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>DimSalesTerritory 테이블을 AW Internet Sales Tabular Model 프로젝트에 추가  
이 Adventure Works 시나리오에 대한 동적 보안을 구현하려면 모델에 두 개의 테이블을 더 추가해야 합니다. 추가할 첫 번째 테이블은 동일한 AdventureWorksDW 데이터베이스의 DimSalesTerritory(판매 지역으로)입니다. 로그온한 사용자가 찾아볼 수 있는 특정 데이터를 정의하는 SalesTerritory 테이블에 행 필터를 나중에 적용합니다.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>DimSalesTerritory 테이블을 추가하려면  
  
1.  테이블 형식 모델 탐색기의 > **데이터 원본**에서 해당 연결을 마우스 오른쪽 단추로 클릭한 다음 **새 테이블 가져오기**를 클릭합니다.  

    [가장 자격 증명] 대화 상자가 나타나면 2단원: 데이터 추가에 사용된 가장 자격 증명을 입력합니다.
  
2.  탐색기에서 **DimSalesTerritory** 테이블을 선택한 후 **확인**을 클릭합니다.    
  
3.  쿼리 편집기에서 **DimSalesTerritory** 쿼리를 클릭한 후 **SalesTerritoryAlternateKey** 열을 제거합니다.  
  
7.  **가져오기**를 클릭합니다.  
  
    새 테이블이 모델 작업 영역에 추가됩니다. 그러면 원본 DimSalesTerritory 테이블의 개체 및 데이터를 AW Internet Sales Tabular Model로 가져옵니다.  
  
9. 테이블을 성공적으로 가져온 후 **닫기**를 클릭합니다.  

## <a name="add-a-table-with-user-name-data"></a>사용자 이름 데이터가 있는 테이블 추가  
AdventureWorksDW 샘플 데이터베이스의 DimEmployee 테이블은 AdventureWorks 도메인의 사용자를 포함합니다. 해당 사용자 이름은 사용자 환경에 존재하지 않습니다. 모델에 조직에서 실제 사용자의 작은 샘플(3개 이상)을 포함하는 테이블을 만들어야 합니다. 그런 다음 이러한 사용자를 새 역할의 멤버로 추가합니다. 샘플 사용자 이름에 대한 암호는 필요하지 않지만 사용자 고유 도메인의 실제 Windows 사용자 이름은 필요합니다.  
  
#### <a name="to-add-an-employeesecurity-table"></a>EmployeeSecurity 테이블을 추가하려면  
  
1.  Microsoft Excel을 열어 워크시트를 만듭니다.  
  
2.  머리글 행을 포함하여 다음 테이블을 복사한 후 워크시트에 붙여넣습니다.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  성명 및 domain\username을 조직에서 세 사용자의 이름 및 로그인 ID로 바꿉니다. EmployeeId 1에 대해 이 사용자가 둘 이상의 영업 지역에 속함을 보여 주는 처음 두 행에 동일한 사용자를 넣습니다. EmployeeId 및 SalesTerritoryId 필드는 그대로 둡니다.  
  
4.  워크시트를 **SampleEmployee**로 저장합니다.  
  
5.  워크시트에서 머리글을 포함하여 직원 데이터가 있는 모든 셀을 선택한 후 선택한 데이터를 마우스 오른쪽 단추로 클릭한 후 **복사**를 클릭합니다.  
  
6.  SSDT에서 **편집** 메뉴를 클릭한 다음 **붙여넣기**를 클릭합니다.  
  
    붙여넣기가 회색으로 표시되면 모델 디자이너 창에서 모든 테이블의 모든 행을 클릭하고 다시 시도합니다.  
  
7.  **붙여넣기 미리 보기** 대화 상자의 **테이블 이름**에 **EmployeeSecurity**를 입력합니다.  
  
8.  **붙여 넣을 데이터**에서 데이터에 SampleEmployee 워크시트의 모든 사용자 데이터 및 머리글이 포함되어 있는지 확인합니다.  
  
9. **첫 행을 열 머리글로 사용**이 선택되어 있는지 확인한 후 **확인**을 클릭합니다.  
  
    SampleEmployee 워크시트에서 복사된 직원 데이터가 있는 EmployeeSecurity라는 새 테이블이 만들어집니다.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>FactInternetSales, DimGeography 및 DimSalesTerritory 테이블 간의 관계 만들기  
FactInternetSales, DimGeography 및 DimSalesTerritory 테이블은 모두 SalesTerritoryId라는 공통의 열을 포함합니다. DimSalesTerritory 테이블의 SalesTerritoryId 열에는 판매 지역마다 다른 ID가 있는 값을 포함합니다.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>FactInternetSales, DimGeography 및 DimSalesTerritory 테이블 간의 관계를 만들려면  
  
1.  다이어그램 뷰에서 **DimGeography** 테이블의 **SalesTerritoryId** 열을 클릭한 채로 커서를 **DimSalesTerritory** 테이블의 **SalesTerritoryId** 열로 끌어다 놓습니다.  
  
2.  **FactInternetSales** 테이블에서 **SalesTerritoryId** 열을 클릭한 채로 커서를 **DimSalesTerritory** 테이블의 **SalesTerritoryId** 열로 끌어다 놓습니다.  
  
    이 관계의 활성 속성은 False이며 비활성을 의미합니다. FactInternetSales 테이블에는 이미 다른 활성 관계가 있습니다.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>클라이언트 응용 프로그램에서 EmployeeSecurity 테이블 숨기기  
이 작업에서는 EmployeeSecurity 테이블을 숨겨서 클라이언트 응용 프로그램의 필드 목록에 나타나지 않도록 합니다. 테이블을 숨긴다고 해서 안전한 것은 아닙니다. 사용자는 EmployeeSecurity 테이블 데이터를 계속 쿼리할 수 있습니다(알고 있는 경우). 사용자가 어떤 데이터도 쿼리하지 못하도록 하고 EmployeeSecurity 테이블 데이터의 보안을 유지하려면 나중 작업에 필터를 적용합니다.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>클라이언트 응용 프로그램에서 EmployeeSecurity 테이블을 숨기려면  
  
-   모델 디자이너의 다이어그램 뷰에서 **Employee** 테이블 머리글을 마우스 오른쪽 단추로 클릭한 후 **클라이언트 도구에서 숨기기**를 클릭합니다.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Sales Employees by Territory(지역별 영업 직원) 사용자 역할 만들기  
이 작업에서는 사용자 역할을 만듭니다. 이 역할은 사용자에게 표시되는 DimSalesTerritory 테이블의 행을 정의하는 행 필터를 포함합니다. 필터는 DimSalesTerritory와 관련된 모든 다른 테이블에 대해 일대다 관계 방향으로 적용됩니다. 또한 해당 역할의 멤버인 모든 사용자가 전체 EmployeeSecurity 테이블을 쿼리할 수 없도록 보호하는 필터도 적용합니다.  
  
> [!NOTE]  
> 이 단원에서 만든 Sales Employees by Territory(지역별 영업 직원) 역할은 멤버가 사용자가 속한 판매 지역에 대한 판매 데이터만 찾아보도록(또는 쿼리) 제한합니다. [단원 11: 역할 만들기](../tutorials/aas-lesson-11-create-roles.md)에서 생성된 역할의 멤버로 존재하는 Sales Employees by Territory(지역별 영업 직원) 역할에 사용자를 멤버로 추가하는 경우 결합된 권한을 갖게 됩니다. 사용자가 여러 역할, 권한 및 각 역할에 대해 정의 된 행 필터의 멤버인 경우 누적 됩니다. 즉, 이 사용자는 역할의 조합으로 결정된 더 큰 권한을 갖습니다.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Sales Employees by Territory(지역별 영업 직원) 사용자 역할을 만들려면  
  
1.  SSDT에서 **모델** 메뉴를 클릭한 후 **역할**을 클릭합니다.  
  
2.  **역할 관리자**에서 **새로 만들기**를 클릭합니다.  
  
    권한이 없는 새 역할이 목록에 추가됩니다.  
  
3.  새 역할을 클릭한 후 **이름** 열에서 역할의 이름을 **Sales Employees by Territory(지역별 영업 직원)**로 변경합니다.  
  
4.  **사용 권한** 열에서 드롭다운 목록을 클릭한 후 **읽기** 권한을 선택합니다.  
  
5.  **멤버** 탭을 클릭한 다음 **추가**를 클릭합니다.  
  
6.  **사용자 또는 그룹 선택** 대화 상자의 **선택할 개체 이름 입력**에서 EmployeeSecurity 테이블을 만들 때 사용한 첫 번째 예제 사용자 이름을 입력합니다. **이름 확인**을 클릭하여 사용자 이름이 올바른지 확인한 후 **확인**을 클릭합니다.  
  
    이 단계를 반복하여 EmployeeSecurity 테이블을 만들 때 사용한 다른 예제 사용자 이름을 추가합니다.  
  
7.  **행 필터** 탭을 클릭합니다.  
  
8.  **EmployeeSecurity** 테이블의 **DAX 필터** 열에서 다음 수식을 입력합니다.  
  
    ```
      =FALSE()  
    ```
  
    이 수식은 false 부울 조건으로 확인하는 모든 열을 지정합니다. EmployeeSecurity 테이블에 대한 열은 Territory 사용자 역할로 판매 직원의 멤버로 쿼리될 수 없습니다.  
  
9. **DimSalesTerritory** 테이블에 대해 다음 수식을 입력합니다.  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    이 수식에서 LOOKUPVALUE 함수는 DimEmployeeSecurity[SalesTerritoryId] 열에 대한 모든 값을 반환합니다. 여기서 EmployeeSecurity[LoginId]는 현재 로그온한 Windows 사용자 이름과 같고 EmployeeSecurity[SalesTerritoryId]는 DimSalesTerritory[SalesTerritoryId]와 같습니다.  
  
    LOOKUPVALUE에서 반환된 판매 지역 ID 집합은 DimSalesTerritory 테이블에 표시된 행을 제한하는 데 사용됩니다. 행의 SalesTerritoryID가 LOOKUPVALUE 함수에서 반환한 ID 집합에 있는 행만 표시됩니다.  
  
10. 역할 관리자에서 **확인**을 클릭합니다.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Sales Employees by Territory(지역별 영업 직원) 사용자 역할 테스트  
이 작업에서는 SSDT에서 Excel에서 분석 기능을 사용하여 Sales Employees by Territory(지역별 영업 직원) 사용자 역할의 효율성을 테스트합니다. EmployeeSecurity 테이블에 추가한 사용자 이름 중 하나를 역할의 멤버로 지정합니다. 그러면 이 사용자 이름이 Excel과 모델 간에 생성된 연결의 유효한 사용자 이름으로 사용됩니다.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Sales Employees by Territory(지역별 영업 직원) 사용자 역할을 테스트하려면  
  
1.  SSDT에서 **모델** 메뉴를 클릭한 후 **Excel에서 분석**을 클릭합니다.  
  
2.  **Excel에서 분석** 대화 상자의 **모델에 연결하기 위해 사용할 사용자 이름 또는 역할 지정**에서 **다른 Windows 사용자**를 선택한 후 **찾아보기**를 클릭합니다.  
  
3.  **사용자 또는 그룹 선택** 대화 상자의 **선택할 개체 이름 입력**에서 EmployeeSecurity 테이블에 포함된 사용자 이름을 입력한 후 **이름 확인**을 클릭합니다.  
  
4.  **확인**을 클릭하여 **사용자 또는 그룹 선택** 대화 상자를 닫은 후 **확인**을 클릭하여 **Excel에서 분석** 대화 상자를 닫습니다.  
  
    Excel에서 새 통합 문서가 열립니다. 피벗 테이블이 자동으로 만들어집니다. 피벗 테이블 필드 목록에는 새 모델에 사용할 수 있는 대부분의 데이터 필드가 포함됩니다.  
  
    EmployeeSecurity 테이블은 피벗 테이블 필드 목록에 표시되지 않습니다. 이전 작업의 클라이언트 도구에서 이 테이블을 숨겼습니다.  
  
5.  **필드** 목록의 **∑ 인터넷 판매**(측정값)에서 **InternetTotalSales** 측정값을 선택합니다. 측정값이 **값** 필드에 입력됩니다.  
  
6.  **DimSalesTerritory** 테이블에서 **SalesTerritoryId** 열을 선택합니다. 열이 **행 레이블** 필드에 입력됩니다.  
  
    인터넷 판매 수치는 사용한 유효 사용자 이름이 속하는 한 지역에 대해서만 표시됩니다. DimGeography 테이블에서 행 레이블 필드로 다른 열을 선택하는 경우(예를 들어 도시) 유효 사용자가 속한 판매 지역의 도시만 표시됩니다.  
  
    이 사용자는 자신이 속하지 않는 지역에 대한 인터넷 판매 데이터를 찾아보거나 쿼리할 수 없습니다. 이렇게 제한하는 이유는 Sales Employees by Territory(지역별 영업 직원) 사용자 역할에서 DimSalesTerritory 테이블에 대해 정의한 행 필터가 다른 판매 지역과 관련된 모든 데이터에 대해 데이터를 보호하기 때문입니다.  
  
## <a name="see-also"></a>참고 항목  
[USERNAME 함수(DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[LOOKUPVALUE 함수(DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[CUSTOMDATA 함수(DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  