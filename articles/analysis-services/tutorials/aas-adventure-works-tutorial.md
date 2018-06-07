---
title: Azure Analysis Services Adventure Works 자습서 | Microsoft Docs
description: Azure Analysis Services에 대한 Adventure Works 자습서 소개
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3c95e755c66cfd1bc0a4e4e55bf1d05f08214925
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services - Adventure Works 자습서

이 자습서에서는 [SSDT(SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)가 있는 Visual Studio를 사용하여 1400 호환성 수준에서 테이블 형식 모델을 만들고 배포하는 방법에 대한 단원을 제공합니다.  

Analysis Services 및 테이블 형식 모델링을 처음 사용하는 경우 이 자습서를 완료하는 것이 Visual Studio를 사용하여 기본 테이블 형식 모델을 만들고 배포하는 방법을 익힐 수 있는 가장 빠른 방법입니다. 필수 구성 요소를 마련한 후 완료하는 데 2-3시간 사이가 소요됩니다.  
  
## <a name="what-you-learn"></a>학습 내용   
  
-   SSDT가 있는 Visual Studio에서 **1400 호환성 수준**으로 새 테이블 형식 모델 프로젝트를 만드는 방법
  
-   관계형 데이터베이스에서 테이블 형식 모델 프로젝트 작업 영역 데이터베이스로 데이터를 가져오는 방법  
  
-   모델의 테이블 간에 관계를 만들고 관리하는 방법  
  
-   계산된 열, 측정값 및 사용자가 중요한 비즈니스 메트릭을 분석하는 데 도움이 되는 핵심 성과 지표를 만드는 방법  
  
-   비즈니스 및 응용 프로그램별 관점을 제공하여 사용자가 모델 데이터를 보다 쉽게 찾아볼 수 있도록 큐브 뷰 및 계층 구조를 만들고 관리하는 방법  
  
-   다른 파티션과 독립적으로 처리할 수 있는 더 작은 논리 부분으로 테이블 데이터를 분할하는 파티션을 만드는 방법  
  
-   사용자 멤버와 역할을 만들어 모델 개체와 데이터를 보호하는 방법  
  
-   SSDT를 사용하여 **Azure Analysis Services** 서버 또는 **SQL Server 2017 Analysis Services** 서버에 테이블 형식 모델을 배포하는 방법  
  
## <a name="prerequisites"></a>필수 조건  
이 자습서를 완료하려면 다음이 필요합니다.  
  
-   Azure Analysis Services 서버. [Azure Analysis Services 평가판](https://azure.microsoft.com/services/analysis-services/)을 등록하고 [서버를 만듭니다](../analysis-services-create-server.md). 

-   **AdventureWorksDW 샘플 데이터베이스**가 있는 [Azure SQL 데이터 웨어하우스](../../sql-data-warehouse/create-data-warehouse-portal.md) 또는 [Adventure Works 샘플 데이터베이스](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)가 있는 SQL Server 데이터 웨어하우스

    **중요:** 샘플 데이터베이스를 온-프레미스 SQL Server 데이터 웨어하우스에 설치하고, 모델을 Azure Analysis Services 서버에 배포하는 경우 [온-프레미스 데이터 게이트웨이](../analysis-services-gateway.md)가 필요합니다.

-   최신 버전의 Visual Studio용 [SSDT(SQL Server 데이터 도구)](https://msdn.microsoft.com/library/mt204009.aspx).

-   최신 버전의 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).    

-   [Power BI Desktop](https://powerbi.microsoft.com/desktop/) 또는 Excel과 같은 클라이언트 응용 프로그램. 

## <a name="scenario"></a>시나리오  
이 자습서는 가상의 회사인 Adventure Works Cycles를 기반으로 합니다. Adventure Works는 자전거, 부품 및 액세서리를 생산하여 북아메리카, 유럽 및 아시아의 상업 시장에 유통하는 대규모 다국적 제조 회사입니다. 회사는 500명의 작업자를 고용합니다. 또한 Adventure Works는 시장 기반 전체에 대한 여러 지역별 영업 팀을 고용하고 있습니다. 프로젝트는 AdventureWorksDW 데이터베이스에서 영업 및 마케팅 사용자가 인터넷 판매 데이터를 분석하기 위한 테이블 형식의 모델을 작성하는 것입니다.  
  
자습서를 완료하려면 다양한 단원을 완료해야 합니다. 각 단원에는 작업이 있습니다. 단원을 완료하려면 각 작업을 순서대로 완료해야 합니다. 특정 단원에서 유사한 결과를 얻는 몇 가지 작업이 있을 수 있지만 각 작업을 완료하는 방법은 약간 다릅니다. 이 메서드는 보통 작업을 완료하고 이전 단원 및 작업에서 배운 기술을 사용하여 문제를 해결할 수 있는 방법이 여러 가지가 있음을 보여 줍니다.  
  
이 단원의 목적은 SSDT에 포함된 다양한 기능을 사용하여 기본 테이블 형식 모델을 작성하는 과정을 안내하는 것입니다. 각 단원은 이전 단원을 토대로 작성되므로 단원을 순서대로 완료해야 합니다.
  
이 자습서는 SSMS를 사용하여 서버 또는 데이터베이스를 관리하거나 클라이언트 응용 프로그램을 사용하여 모델 데이터를 검색하는 Azure Portal의 서버를 관리하는 방법에 대한 단원을 제공하지 않습니다. 


## <a name="lessons"></a>단원  
이 자습서에는 다음 단원이 포함되어 있습니다.  
  
|단원|예상 완료 시간|  
|----------|------------------------------|  
|[1 - 새 테이블 형식 모델 프로젝트 만들기](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10분|  
|[2 - 데이터 가져오기](../tutorials/aas-lesson-2-get-data.md)|10분|  
|[3 - 날짜 테이블로 표시](../tutorials/aas-lesson-3-mark-as-date-table.md)|3분|  
|[4 - 관계 만들기](../tutorials/aas-lesson-4-create-relationships.md)|10분|  
|[5 - 계산된 열 만들기](../tutorials/aas-lesson-5-create-calculated-columns.md)|15분|
|[6 - 측정값 만들기](../tutorials/aas-lesson-6-create-measures.md)|30분|  
|[7 - KPI(핵심 성과 지표) 만들기](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15분|  
|[8 - 큐브 뷰 만들기](../tutorials/aas-lesson-8-create-perspectives.md)|5분|  
|[9 - 계층 구조 만들기](../tutorials/aas-lesson-9-create-hierarchies.md)|20분|  
|[10 - 파티션 만들기](../tutorials/aas-lesson-10-create-partitions.md)|15분|  
|[11 - 역할 만들기](../tutorials/aas-lesson-11-create-roles.md)|15분|  
|[12 - Excel에서 분석](../tutorials/aas-lesson-12-analyze-in-excel.md)|5분| 
|[13 - 배포](../tutorials/aas-lesson-13-deploy.md)|5분|  
  
## <a name="supplemental-lessons"></a>추가 단원  
자습서를 완료하는 데 이러한 단원이 필요하지는 않지만 고급 테이블 형식 모델 작성 기능을 이해하는 데 도움이 될 수 있습니다.  
  
|단원|예상 완료 시간|  
|----------|------------------------------|  
|[세부 정보 행](../tutorials/aas-supplemental-lesson-detail-rows.md)|10분|
|[동적 보안](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30분|
|[불규칙한 계층 구조](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20분| 

  
## <a name="next-steps"></a>다음 단계  
시작하려면 [단원 1: 새 테이블 형식 모델 프로젝트 만들기](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)를 참조하세요.  
  
  
  

