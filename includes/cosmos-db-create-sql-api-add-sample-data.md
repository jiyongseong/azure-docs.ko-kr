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
ms.openlocfilehash: 92b739424d1d6f0e1eb89d5993718f5c36162204
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
이제 데이터 탐색기를 사용하여 새 컬렉션에 데이터를 추가할 수 있습니다.

1. 데이터 탐색기에서 새 데이터베이스가 컬렉션 창에 나타납니다. **작업** 데이터베이스를 확장하고 **Items** 컬렉션을 확장하고 **문서**를 클릭한 후 **새 문서**를 클릭합니다. 

   ![Azure Portal의 데이터 탐색기에서 새 문서 만들기](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. 이제 다음과 같은 구조를 사용하여 컬렉션에 문서를 추가 합니다.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. **문서** 탭에 Json을 추가했으면 **저장**을 클릭합니다.

    ![Azure Portal의 데이터 탐색기에서 Json 데이터를 복사하고 저장을 클릭합니다.](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  `id` 속성에 대한 고유한 값을 삽입하는 경우 둘 이상의 문서를 만들고 저장하며 다른 속성을 적합하게 변경합니다. Azure Cosmos DB가 데이터에 어떠한 스키마도 적용하지 않으므로 새 문서는 사용자가 원하는 어떠한 구조든 가질 수 있습니다.