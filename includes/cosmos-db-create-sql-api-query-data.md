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
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
이제 데이터 탐색기에서 쿼리를 사용하여 데이터를 검색하고 필터링할 수 있습니다.

1. 기본적으로 쿼리가 `SELECT * FROM c`로 설정되어 있는지 확인합니다. 이 기본 쿼리는 컬렉션에서 모든 문서를 검색하고 표시합니다. 

    ![데이터 탐색기에서 기본 쿼리는 `SELECT * FROM c`입니다.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. 계속해서 **문서** 탭에서 **필터 편집** 단추를 클릭하고, 쿼리 조건자 상자에 `ORDER BY c._ts DESC`를 추가하고, **필터 적용**을 클릭하여 쿼리를 변경합니다.

    ![ORDER BY c._ts DESC를 추가하고 필터 적용을 클릭하여 기본 쿼리를 변경합니다.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

이 수정된 쿼리는 해당 타임스탬프에 따라 내림차순으로 문서를 나열합니다. 따라서 이제 두 번째 문서가 첫 번째로 나열됩니다. SQL 구문에 익숙한 경우 이 상자에 지원되는 [SQL 쿼리](../articles/cosmos-db/sql-api-sql-query.md)를 입력할 수 있습니다. 

그러면 데이터 탐색기에서 작업을 완료합니다. 코드를 사용하는 작업으로 이동하기 전에 서버 쪽 비즈니스 논리를 수행하고 처리량을 확장할 수 있도록 데이터 탐색기를 사용하여 저장 프로시저, UDF 및 트리거를 만들 수도 있습니다. 데이터 탐색기는 API에서 사용할 수 있는 모든 기본 제공 프로그래밍 방식 데이터 액세스를 표시하지만 Azure Portal의 데이터에도 쉽게 액세스할 수 있습니다.