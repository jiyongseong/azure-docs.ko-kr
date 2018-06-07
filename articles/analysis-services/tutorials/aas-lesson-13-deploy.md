---
title: 'Azure Analysis Services 자습서 단원 13: 배포 | Microsoft Docs'
description: Azure Analysis Services에 자습서 프로젝트를 배포하는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 889cc4224e2ffe31e15590c04c3dbf4b656bc386
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="deploy"></a>배포

이 단원에서는 배포 속성을 구성합니다. 즉, 배포할 Azure Analysis Services 서버 및 모델 이름을 지정합니다. 그런 다음 해당 인스턴스에 모델을 배포합니다. 모델을 배포한 후에는 사용자가 보고 클라이언트 응용 프로그램을 사용하여 모델에 연결할 수 있습니다. 자세한 내용은 [Azure Analysis Services에 배포](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy)를 참조하세요.  
  
이 단원을 완료하기 위한 예상 시간: **5분**  
  
## <a name="prerequisites"></a>필수 조건  
이 문서는 테이블 형식 모델링 자습서에 포함되며 순서대로 완료해야 합니다. 이 단원의 작업을 수행하기 전에 이전 단원인 [단원 12: Excel에서 분석](../tutorials/aas-lesson-12-analyze-in-excel.md)을 완료해야 합니다.  

> [!IMPORTANT]  
> 원격 Analysis Services 서버에 배포하려면 [관리자 권한](../analysis-services-server-admins.md)이 있어야 합니다.  

> [!IMPORTANT]  
> 온-프레미스 SQL Server에 AdventureWorksDW2014 샘플 데이터베이스를 설치했고 Azure Analysis Services 서버에 모델을 배포하는 경우 [온-프레미스 데이터 게이트웨이](../analysis-services-gateway.md)가 필요합니다.
  
## <a name="deploy-the-model"></a>모델 배포  
  
#### <a name="to-configure-deployment-properties"></a>배포 속성을 구성하려면  

  
1.  **솔루션 탐색기**에서 **AW Internet Sales** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.  
  
2.  **AW Internet Sales 속성 페이지** 대화 상자의 **배포 서버** 아래의 **서버** 속성에서 전체 서버를 입력합니다.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  **데이터베이스** 속성에 **Adventure Works Internet Sales**를 입력합니다.  
  
4.  **모델 이름** 속성에 **Adventure Works Internet Sales Model**를 입력합니다.  
  
5.  선택 내용을 확인한 다음 **확인**을 클릭합니다.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Adventure Works Internet Sales를 배포하려면
  
1.  **솔루션 탐색기**에서 **AW Internet Sales** 프로젝트 > **빌드**를 마우스 오른쪽 단추로 클릭합니다.  

2.  **AW Internet Sales** 프로젝트 > **배포**를 마우스 오른쪽 단추로 클릭합니다.

    Azure Analysis Services에 배포할 때는 사용자 계정을 입력하라는 메시지가 표시될 수 있습니다. 조직의 계정 및 암호를 입력합니다(예: nancy@adventureworks.com). 이 계정은 서버에서 관리자여야 합니다.
  
    배포 대화 상자가 나타나고 메타데이터와 모델에 포함된 각 테이블의 배포 상태가 표시됩니다.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. 배포가 성공적으로 완료되면 진행하고 **닫기**를 클릭합니다.  
  

이 단원에서는 SSDT에서 테이블 형식 모델을 배포하는 가장 일반적이고 쉬운 방법을 설명합니다. 배포 마법사 또는 XMLA 및 AMO를 사용하여 자동화하는 고급 배포 옵션은 매우 우수한 유연성, 일관성 및 예약된 배포를 제공합니다. 자세한 내용은 [테이블 형식 모델 솔루션 배포](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular)를 참조하세요.

## <a name="conclusion"></a>결론  
축하합니다! 첫 번째 Analysis Services 테이블 형식 모델의 작성 및 배포가 완료되었습니다. 이 자습서는 테이블 형식 모델을 만드는 가장 일반적인 작업을 완료하는 과정을 안내합니다. 이제 Adventure Works Internet Sales 모델을 배포했으므로, SQL Server Management Studio를 사용하여 모델을 관리하고 프로세스 스크립트 및 백업 계획을 만들 수 있습니다. 이제 사용자가 Microsoft Excel 또는 Power BI와 같은 보고 클라이언트 응용 프로그램을 사용하여 모델에 연결할 수 있습니다.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>다음 작업
[Power BI Desktop으로 연결](../analysis-services-connect-pbi.md)   
[추가 단원 - 동적 보안](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[추가 단원 - 세부 정보 행](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[추가 단원 - 불규칙한 계층 구조](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
