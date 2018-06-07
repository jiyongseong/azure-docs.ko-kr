---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 690c5f07a2b0f7a88e16f0b0bbbaa9ca78e37317
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
1. 새 창에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **리소스 만들기**, **데이터베이스**를 차례로 클릭한 다음, **Azure Cosmos DB**에서 **만들기**를 클릭합니다.
   
   ![더 많은 서비스와 Azure Cosmos DB를 강조 표시한 Azure Portal의 스크린샷](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. **새 계정** 블레이드에서 **MongoDB**를 API로 지정하고, Azure Cosmos DB 계정에 대해 원하는 구성을 작성합니다.
 
    * **ID**는 Azure Cosmos DB 계정을 식별하는 데 사용하려는 고유한 이름이어야 합니다. 소문자, 숫자, '-' 문자만 포함할 수 있으며, 3-50자여야 합니다.
    * **구독**은 사용자의 Azure 구독입니다. 해당 사용자에 대한 구독을 작성합니다.
    * **리소스 그룹**은 Azure Cosmos DB 계정의 리소스 그룹 이름입니다. **새로 만들기**를 선택하고 사용자 계정에 대한 새 리소스 그룹 이름을 입력합니다. 간단히 하기 위해 ID와 동일한 이름을 사용할 수 있습니다.
    * **위치**는 Azure Cosmos DB 인스턴스가 있는 지리적 위치입니다. 사용자에게 가장 가까운 위치를 선택합니다.

    그런 다음 **Create**를 클릭합니다.

    ![Azure Cosmos DB에 대한 새 계정 페이지](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. 계정 생성에는 몇 분 정도가 소요됩니다. 포털에서 **축하합니다! MongoDB API를 포함하는 Azure Cosmos DB 계정이 준비되었습니다** 페이지가 표시될 때까지 기다립니다.

    ![Azure Portal 알림 창](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
