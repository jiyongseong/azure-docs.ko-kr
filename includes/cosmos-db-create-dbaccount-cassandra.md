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
ms.openlocfilehash: 3ad250ca2e641ebbf1e280e25aa53ff6f0ad6bd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
1. 새 브라우저 창에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** > **데이터베이스** > **Azure Cosmos DB**를 클릭합니다.
   
   ![Azure Portal 데이터베이스 창](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)

3. **새 계정** 페이지에서 새 Azure Cosmos DB 계정에 대한 설정을 입력합니다. 
 
    설정|제안 값|설명
    ---|---|---
    ID|*고유한 이름 입력*|이 Azure Cosmos DB 계정을 식별하는 고유한 이름을 입력합니다. 접점을 만들기 위해 제공하는 ID에 *cassandra.cosmosdb.azure.com*이 추가되므로 식별할 수 있는 고유한 ID를 사용해야 합니다.<br><br>ID는 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-50자를 포함해야 합니다.
    API|Cassandra|API는 만들 계정의 형식을 결정합니다. Azure Cosmos DB는 응용 프로그램의 요구 사항을 충족하기 위해 SQL(문서 데이터베이스), Gremlin(그래프 데이터베이스), MongoDB(문서 데이터베이스), Azure Table 및 Cassandra라는 다섯 가지 API를 제공합니다. 현재 각각에는 별도의 계정이 필요합니다. <br><br>이 빠른 시작에서는 CQL 구문을 사용하여 쿼리할 수 있는 넓은 열 데이터베이스를 만들기 때문에 **Cassandra**를 선택합니다.<br><br>Cassandra(넓은 열)가 목록에 표시되지 않으면 Cassandra API 미리 보기 프로그램 [가입을 신청](../articles/cosmos-db/cassandra-introduction.md#sign-up-now)해야 합니다.<br><br> [Cassandra API에 대한 자세한 정보](../articles/cosmos-db/cassandra-introduction.md)|
    구독|*사용자의 구독*|이 Azure Cosmos DB 계정에 사용할 Azure 구독을 선택합니다. 
    리소스 그룹|새로 만들기<br><br>*그런 후 ID에서 위에 제공된 동일한 고유한 이름을 입력합니다*.|**새로 만들기**를 선택하고 사용자 계정에 대한 새 리소스 그룹 이름을 입력합니다. 간단히 하기 위해 ID와 동일한 이름을 사용할 수 있습니다. 
    위치|*사용자와 가장 가까운 지역 선택*|Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 데이터에 가장 빨리 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.
    대시보드에 고정 | 여기서 | 쉬운 액세스를 위해 새 데이터베이스 계정이 포털 대시보드에 추가되도록 이 상자를 선택합니다.

    그런 다음 **Create**를 클릭합니다.

    ![Azure Cosmos DB에 대한 새 계정 페이지](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. 계정 생성에는 몇 분 정도가 소요됩니다. 포털에서 **축하합니다! Azure Cosmos DB 계정이 만들어졌습니다.** 페이지가 표시될 때까지 기다립니다.

