---
title: Azure Analysis Services에서 데이터 모델 호환성 수준 | Microsoft Docs
description: 테이블 형식 데이터 모델 호환성 수준을 이해합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 231a7ea42560aac3fba0ccf27e27123320f219a3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Analysis Services 테이블 형식 모델에 대한 호환성 수준

*호환성 수준*은 Analysis Services 엔진의 릴리스 관련 동작을 가리킵니다. 호환성 수준에 대한 변경은 일반적으로 주요 버전의 SQL Server와 일치합니다. 이러한 변경은 두 플랫폼 간의 패리티를 유지하기 위해 Azure Analysis Services에도 구현됩니다. 호환성 수준 변경은 테이블 형식 모델에서 사용할 수 있는 기능에도 영향을 줍니다. 예를 들어 DirectQuery 및 테이블 형식 개체 메타데이터는 호환성 수준에 따라 서로 다른 구현을 갖습니다. 

Azure Analysis Services는 1200 및 1400 호환성 수준의 테이블 형식 모델을 지원합니다.

최신 호환성 수준은 1400입니다. 이 수준은 SQL Server 2017 Analysis Services와 일치합니다. 1400 호환성 수준에서 주요 기능은 다음과 같습니다.

*  TOM API 및 TMSL 스크립트에 대한 지원으로 데이터 연결 및 테이블 형식 모델로 가져오기에 대한 새로운 인프라. 이 새로운 기능을 통해 Azure Blob 저장소와 같은 추가 데이터 원본을 지원할 수 있습니다.
*  데이터 가져오기 및 M 식을 사용하여 데이터 변환 및 데이터 매시업 기능.
*  측정값은 DAX 식을 사용하여 세부 정보 행 속성을 지원합니다. 이 속성은 Microsoft Excel과 같은 클라이언트 도구를 활성화하여 집계된 보고서에서 자세한 데이터로 드릴다운합니다. 예를 들어 사용자가 지역 및 월에 대한 총 판매액을 볼 때 관련된 주문 세부 정보를 볼 수 있습니다. 
*  해당 항목 내의 데이터 외의 테이블 및 열 이름에 대한 개체 수준 보안.
*  불균형 계층 구조에 대한 향상된 지원.
*  성능 및 모니터링 향상.
  
## <a name="set-compatibility-level"></a>호환성 수준 설정 
 SSDT에서 새 테이블 형식 모델 프로젝트를 만들 때 **테이블 형식 모델 디자이너** 대화 상자에서 호환성 수준을 지정할 수 있습니다. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 **이 메시지를 다시 표시 안 함** 옵션을 선택하는 경우 모든 후속 프로젝트는 기본적으로 지정한 호환성 수준을 사용합니다. **도구** > **옵션**의 SSDT에서 기본 호환성 수준을 변경할 수 있습니다.  
  
 SSDT에서 기존 테이블 형식 모델 프로젝트를 업그레이드하려면 모델 **속성** 창에서 **호환성 수준** 속성을 설정합니다. 호환성 수준 업그레이드는 취소할 수 없습니다.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>SQL Server Management Studio에서 테이블 형식 모델 데이터베이스에 대한 호환성 수준 확인 
 SSMS에서 데이터베이스 이름 > **속성** > **호환성 수준**을 마우스 오른쪽 단추로 클릭합니다.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>SSMS에서 서버에 대해 지원되는 호환성 수준 확인  
 SSMS에서 서버 이름 > **속성** > **지원되는 호환성 수준**을 마우스 오른쪽 단추로 클릭합니다.  
  
 이 속성을 서버에서 실행되는 데이터베이스의 가장 높은 호환성 수준을 지정합니다(미리 보기 제외). 지원되는 호환성 수준을 변경할 수 없습니다.  

## <a name="next-steps"></a>다음 단계
  [Azure Portal에서 모델 만들기](analysis-services-create-model-portal.md)   
  [Analysis Services 관리](analysis-services-manage.md)  
