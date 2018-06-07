---
title: Azure에서 Analysis Services 서버 만들기 | Microsoft Docs
description: Azure에서 Analysis Services 서버 인스턴스를 만드는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c49e886ee5b980e8fd059d72eb2e4a3f0dc895c4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150023"
---
# <a name="create-an-analysis-services-server-in-azure-portal"></a>Azure Portal에서 Analysis Services 서버 만들기
이 문서에서는 Azure 구독에서 Analysis Services 서버 리소스를 만드는 과정을 안내합니다.

이 작업을 시작하려면 다음이 필요합니다. 

* **Azure 구독**: [Azure 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/)으로 이동하여 계정을 만들 수 있습니다.
* **Azure Active Directory**: 구독이 Azure Active Directory와 연결되어야 합니다. 또한 해당 Azure Active Directory의 계정으로 Azure에 로그인해야 합니다. 자세한 내용은 [인증 및 사용자 권한](analysis-services-manage-users.md)을 참조하세요.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인 

[Azure Portal](https://portal.azure.com)에 로그인


## <a name="create-a-server"></a>서버 만들기

1. **+ 리소스 만들기** > **데이터 + 분석** > **Analysis Services**를 클릭합니다.

    ![포털](./media/analysis-services-create-server/aas-create-server-portal.png)

2. **Analysis Services**에서 필수 필드를 입력한 다음, **만들기**를 누릅니다.
   
    ![서버 만들기](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **서버 이름**: 서버를 참조하는 데 사용되는 고유한 이름을 입력합니다.
   * **구독**: 이 서버와 연결할 구독을 선택합니다.
   * **리소스 그룹**: 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 리소스 그룹은 Azure 리소스 컬렉션을 관리할 수 있도록 디자인되었습니다. 자세한 내용은 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 참조하세요.
   * **위치**: 이 Azure 데이터 센터 위치는 서버를 호스팅합니다. 가장 큰 사용자 기반에 가장 가까운 위치를 선택합니다.
   * **가격 책정 계층**: 가격 책정 계층을 선택합니다. 테스트하고 샘플 모델 데이터베이스를 설치하려는 경우 체험판 **D1** 계층을 선택합니다. 자세한 내용은 [Azure Analysis Services 가격 책정](https://azure.microsoft.com/pricing/details/analysis-services/)을 참조하세요. 
    * **관리자**: 기본적으로 로그인하는 계정이 됩니다. Azure Active Directory에서 다른 계정을 선택할 수 있습니다.
    * **백업 저장소 설정**: 선택 사항입니다. [저장소 계정](../storage/common/storage-introduction.md)이 이미 있는 경우 모델 데이터베이스 백업에 대한 기본값으로 지정할 수 있습니다. [백업 및 복원](analysis-services-backup.md) 설정을 나중에 지정할 수도 있습니다.
    * **저장소 키 만료**: 선택 사항입니다. 저장소 키 만료 시간을 지정합니다.
3. **만들기**를 클릭합니다.

만들기는 일반적으로 1분 이하로 걸립니다. **포털에 추가**를 선택한 경우 새 서버를 보려면 포털로 이동합니다. 또는 **모든 서비스** > **Analysis Services**로 이동하여 서버가 준비되었는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 서버를 삭제합니다. 서버의 **개요**에서 **삭제**를 클릭합니다. 

 ![정리](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>다음 단계

서버에 [예제 데이터 모델을 추가합니다](analysis-services-create-sample-model.md).  
온-프레미스 데이터 원본에 데이터 모델을 연결하는 경우 [온-프레미스 데이터 게이트웨이를 설치합니다](analysis-services-gateway-install.md).  
Visual Studio에서 [테이블 형식 모델 프로젝트를 배포합니다](analysis-services-deploy.md).   


