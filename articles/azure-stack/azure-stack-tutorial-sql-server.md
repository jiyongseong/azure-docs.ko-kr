---
title: Azure 스택 사용자에 게 SQL 데이터베이스를 사용할 수 있게 | Microsoft Docs
description: SQL Server 리소스 공급자를 설치 하 고 만드는 자습서에서는 Azure 스택 사용자가 SQL 데이터베이스를 만들 수 있는 제공 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0e1eed2601946ddff6fa15f1a1f82398706c920d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Azure 스택 사용자에 게 SQL 데이터베이스를 사용할 수 있게
Azure 스택 클라우드 관리자로 서 사용자가 제공을 만들 수 있습니다 (테 넌 트) 클라우드-네이티브 앱, 웹 사이트 및 작업 함께 사용할 수 있는 SQL 데이터베이스를 만듭니다. 이러한 사용자 지정, 주문형, 클라우드 기반 데이터베이스를 사용자에 게 제공 하 여 시간 및 리소스가 저장할 수 있습니다. 이 설정 하려면 다음을 수행 합니다.

> [!div class="checklist"]
> * SQL Server 리소스 공급자를 배포 합니다.
> * 제품 만들기
> * 제품 테스트

## <a name="deploy-the-sql-server-resource-provider"></a>SQL Server 리소스 공급자를 배포 합니다.

배포 프로세스에 자세히 설명 되어는 [Azure 스택 아티클에서 사용 하 여 SQL 데이터베이스](azure-stack-sql-resource-provider-deploy.md), 다음과 같은 기본 단계로 구성 되 고 있습니다.

1. [SQL 리소스 공급자를 배포](azure-stack-sql-resource-provider-deploy.md)합니다.
2. [배포 확인](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)합니다.
3. 호스팅 SQL server에 연결 하 여 용량을 제공 합니다.

## <a name="create-an-offer"></a>제품 만들기

1.  [할당량 설정](azure-stack-setting-quotas.md) 하 고 이름을 *SQLServerQuota*합니다. 선택 **Microsoft.SQLAdapter** 에 대 한는 **Namespace** 필드입니다.
2.  [계획 만들기](azure-stack-create-plan.md)합니다. 이름을 *TestSQLServerPlan*을 선택는 **Microsoft.SQLAdapter** 서비스 및 **SQLServerQuota** 할당량입니다.

    > [!NOTE]
    > 사용자가 다른 응용 프로그램을 만들 수 있도록, 다른 서비스 계획에 필요할 수 있습니다. 예를 들어 Azure 함수는 계획을 포함 해야는 **Microsoft.Storage** Wordpress 반면 서비스 **Microsoft.MySQLAdapter**합니다.
    > 
    >

3.  [제안 만들기](azure-stack-create-offer.md), 이름을 **TestSQLServerOffer** 선택 하 고는 **TestSQLServerPlan** 계획 합니다.

## <a name="test-the-offer"></a>제품 테스트

SQL Server 리소스 공급자를 배포 하 고 제공 하는 서비스를 만들면 한 했으므로 사용자로 로그인 하 고, 제품을 구독 하 고, 데이터베이스를 만들 수 있습니다.

### <a name="subscribe-to-the-offer"></a>제품 구독
1. Azure 스택 포털에 로그인 (https://portal.local.azurestack.external) 테 넌 트로 합니다.
2. 클릭 **구독** 한 다음 입력 **TestSQLServerSubscription** 아래 **표시 이름**합니다.
3. 클릭 **제안을 선택** > **TestSQLServerOffer** > **만들기**합니다.
4. 클릭 **더 많은 서비스** > **구독** > **TestSQLServerSubscription** > **리소스 공급자**합니다.
5. 클릭 **등록** 옆에 **Microsoft.SQLAdapter** 공급자입니다.

### <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

1. 클릭 **+**  >  **데이터 + 저장소** > **SQL 데이터베이스**합니다.
2. 필드에 대 한 기본값을 그대로 둡니다 또는 이러한 예제를 사용할 수 있습니다.
    - **데이터베이스 이름**: SQLdb
    - **최대 크기 (MB)**: 100
    - **구독**: TestSQLOffer
    - **리소스 그룹**: SQL RG
3. 클릭 **로그인 설정**, 데이터베이스에 대 한 자격 증명을 입력 하 고 클릭 **확인**합니다.
4. 클릭 **SKU** > SQL 호스팅 서버에 대해 만든 SQL SKU 선택 > **확인**합니다.
5. **만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * SQL Server 리소스 공급자를 배포 합니다.
> * 제품 만들기
> * 제품 테스트

자세한 내용은 다음 자습서로 이동 하는 방법:

> [!div class="nextstepaction"]
> [웹, 모바일 및 API 앱 사용자가을 사용할 수 있도록]( azure-stack-tutorial-app-service.md)

