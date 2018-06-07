---
title: Azure Analysis Services 웹 디자이너를 사용하여 테이블 형식 모델 만들기 | Microsoft Docs
description: Azure Portal에서 웹 디자이너를 사용하여 Azure Analysis Services 테이블 형식 모델을 만드는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4b1d4cffc3571297f2b74674156cb7f3bad7c2c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149897"
---
# <a name="create-a-model-in-azure-portal"></a>Azure Portal에서 모델 만들기

Azure Portal의 Azure Analysis Services 웹 디자이너(미리 보기) 기능은 브라우저에서 바로 테이블 형식 모델을 쉽고 빠르게 생성 및 편집하고 모델 데이터를 쿼리하는 방법을 제공합니다. 

현재 웹 디자이너는 **미리 보기**입니다. 기능이 제한됩니다. 고급 모델을 개발하고 테스트하려면 Visual Studio(SSDT) 및 SSMS(SQL Server Management Studio)를 사용하는 것이 가장 좋습니다.

## <a name="before-you-begin"></a>시작하기 전에

- 표준 또는 개발자 계층의 Azure Analysis Services 서버. 웹 디자이너를 사용하여 만드는 새 모델은 이러한 계층에서만 지원되는 DirectQuery입니다.
- 데이터 원본으로 Azure SQL Database, Azure SQL Data Warehouse 또는 Power BI Desktop(.pbix) 파일. Power BI Desktop 파일로 만드는 새 모델은 Azure SQL Database 및 Azure SQL Data Warehouse를 지원합니다.
- Azure SQL Database 또는 Azure SQL Data Warehouse 데이터 원본에 연결하기 위한 SQL Server 계정 및 암호.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="to-create-a-new-tabular-model"></a>새 테이블 형식 모델을 만들려면

1. 서버 **개요** > **웹 디자이너**에서 **열기**를 클릭합니다.

    ![Azure Portal에서 모델 만들기](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. **웹 디자이너** > **모델**에서 **+ 추가**를 클릭합니다.

    ![Azure Portal에서 모델 만들기](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. **새 모델**에서 모델 이름을 입력한 다음 데이터 원본을 선택합니다.

    ![Azure Portal의 새 모델 대화 상자](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. **연결**에서 연결 속성을 입력합니다. 사용자 이름 및 암호는 SQL Server 계정이어야 합니다.

     ![Azure Portal의 연결 대화 상자](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. **테이블 및 보기**에서 모델에 포함할 테이블을 선택한 다음 **만들기**를 클릭합니다. 키 쌍을 사용하여 테이블 간의 관계가 자동으로 생성됩니다.

     ![테이블 및 보기 선택](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

새 모델이 브라우저에 나타납니다. 여기서 다음과 같은 일을 할 수 있습니다.   

- 필드를 쿼리 디자이너로 끌어서 필터를 추가하여 모델 데이터를 쿼리합니다.
- 테이블에 새 측정값을 만듭니다.
- json 편집기를 사용하여 모델 메타데이터를 편집합니다.
- Visual Studio(SSDT), Power BI Desktop 또는 Excel에서 모델을 엽니다.

![테이블 및 보기 선택](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> 브라우저에서 모델 메타데이터를 편집하거나 새 측정값을 만들면 Azure의 모델에 이러한 변경 내용이 저장됩니다. SSDT, Power BI Desktop 또는 Excel에서 모델을 작업하는 경우에도 모델이 동기화되지 않을 수 있습니다.


## <a name="next-steps"></a>다음 단계 
[데이터베이스 역할 및 사용자 관리](analysis-services-database-users.md)  
[Excel과 연결](analysis-services-connect-excel.md)  


