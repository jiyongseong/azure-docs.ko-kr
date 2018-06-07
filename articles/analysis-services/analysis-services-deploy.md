---
title: SSDT를 사용하여 Azure Analysis Services에 배포 | Microsoft Docs
description: SSDT를 사용하여 Azure Analysis Services 서버에 테이블 형식 모델을 배포하는 방법에 대해 알아봅니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a25066ef8446449148bc0ca95989dc6ca3ca6839
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-a-model-from-ssdt"></a>SSDT에서 모델 배포
Azure 구독에서 서버를 만들면 여기에 테이블 형식 모델 데이터베이스를 배포할 준비가 되었습니다. SSDT(SQL Server 데이터 도구)를 사용하여 작업하는 테이블 형식 모델 프로젝트를 빌드하고 배포할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
시작하려면 다음이 필요합니다.

* Azure의 **Analysis Services 서버** - 자세한 내용은 [Azure Analysis Services 서버 만들기](analysis-services-create-server.md)를 참조하세요.
* SSDT의 **테이블 형식 모델 프로젝트** 또는 1200 이상 호환성 수준의 기존 테이블 형식 모델 만들어 본 적이 없나요? [Adventure Works Internet Sales Tabular Modeling 자습서](https://msdn.microsoft.com/library/hh231691.aspx)를 사용해 보세요.
* **온-프레미스 게이트웨이** - 하나 이상의 데이터 원본이 조직 네트워크의 온-프레미스에 있는 경우 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치해야 합니다. 온-프레미스 데이터 원본에 대한 클라우드 연결에 있는 서버가 모델에서 데이터를 처리하고 새로 고치는 데 게이트웨이가 필요합니다.

> [!TIP]
> 배포하기 전에 테이블에서 데이터를 처리할 수 있는지 확인합니다. SSDT에서 **모델** > **처리** > **모두 처리**를 클릭합니다. 처리가 실패하는 경우 성공적으로 배포할 수 없습니다.
> 
> 

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>SSDT에서 테이블 형식 모델을 배포하려면

1. 배포하기 전에 서버 이름을 가져와야 합니다. **Azure Portal** > 서버 > **개요** > **서버 이름**에서 서버 이름을 복사합니다.
   
    ![Azure에서 서버 이름 가져오기](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSDT > **솔루션 탐색기**에서 프로젝트 > **속성**을 마우스 오른쪽 단추로 클릭합니다. 그런 다음 **배포** > **서버**에서 서버 이름을 붙여 넣습니다.   
   
    ![배포 서버 속성에 서버 이름을 붙여 넣습니다](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. **솔루션 탐색기**에서 **속성**을 마우스 오른쪽 단추로 클릭한 후 **배포**를 클릭합니다. Azure에 로그인하라는 메시지가 표시될 수 있습니다.
   
    ![서버에 배포](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    배포 상태는 출력 창 및 배포 모두에 표시됩니다.
   
    ![배포 상태](./media/analysis-services-deploy/aas-deploy-status.png)

이제 모든 작업을 마쳤습니다.


## <a name="troubleshooting"></a>문제 해결
메타데이터를 배포하는 데 실패한 경우 SSDT가 서버에 연결할 수 없기 때문일 수 있습니다. SSMS를 사용하여 서버에 연결할 수 있는지 확인합니다. 프로젝트에 대한 배포 서버 속성이 정확한지 확인합니다.

테이블에서 배포에 실패한 경우 서버가 데이터 원본에 연결할 수 없기 때문일 수 있습니다. 데이터 원본이 조직의 온-프레미스에 있는 경우 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치해야 합니다.

## <a name="next-steps"></a>다음 단계
테이블 형식 모델을 서버에 배포했으므로 연결할 준비가 되었습니다. [SSMS과 연결](analysis-services-manage.md)하여 관리할 수 있습니다. 그리고 Power BI, Power BI Desktop 또는 Excel과 같은 [클라이언트 도구를 사용하여 연결](analysis-services-connect.md)하고 보고서를 만들기 시작할 수 있습니다.

