---
title: Azure Analysis Services 서버에서 샘플 테이블 형식 모델 추가 | Microsoft Docs
description: Azure Analysis Services에서 샘플 모델을 추가하는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>자습서: 샘플 모델 추가

이 자습서에서는 샘플 Adventure Works 모델을 서버에 추가합니다. 샘플 모델은 Adventure Works 인터넷 판매(1200) 데이터 모델링 자습서의 완성된 버전입니다. 샘플 모델은 모델 관리를 테스트하고, 도구 및 클라이언트 응용 프로그램과 연결하며, 모델 데이터를 쿼리하는 데 유용합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 완료하려면 다음이 필요합니다.

- Azure Analysis Services 서버
- 서버 관리자 권한

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-sample-model"></a>샘플 모델 만들기

1. 서버 **개요**에서 **새 모델**을 클릭합니다.

    ![샘플 모델 만들기](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. **새 모델** > **데이터 원본 선택**에서 **샘플 데이터**가 선택되어 있는지 확인한 다음, **추가**를 클릭합니다.

    ![샘플 데이터 선택](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. **개요**에서 `adventureworks` 샘플이 만들어졌는지 확인합니다.

    ![샘플 데이터 선택](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>리소스 정리

샘플 모델은 캐시 메모리 리소스를 사용합니다. 따라서 샘플 모델을 테스트하는 데 사용하지 않는 경우 서버에서 이 모델을 제거해야 합니다.

> [!NOTE]
> 다음 단계에서는 SSMS를 사용하여 서버에서 모델을 삭제하는 방법을 설명합니다. 또한 모델은 미리 보기 웹 디자이너 기능을 사용하여 삭제할 수도 있습니다.

1. SSMS > **개체 탐색기**에서 **연결** > **Analysis Services**를 클릭합니다.

2. **서버에 연결**에서 서버 이름을 붙여넣고, **인증**에서 **Active Directory - MFA 지원을 통한 유니버설 인증**을 선택하고, 사용자 이름을 입력한 다음, **연결**을 클릭합니다.

    ![로그인](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. **개체 탐색기**에서 `adventureworks` 샘플 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음, **삭제**를 클릭합니다.

    ![샘플 데이터베이스 삭제](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>다음 단계 

[Power BI Desktop에서 연결](analysis-services-connect-pbi.md)   
[데이터베이스 역할 및 사용자 관리](analysis-services-database-users.md)


