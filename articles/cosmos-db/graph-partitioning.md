---
title: Graph API 분할 | Microsoft Docs
description: Azure Cosmos DB에서 분할된 그래프를 사용하는 방법을 알아봅니다.
services: cosmos-db
author: luisbosquez
documentationcenter: ''
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: db41efeee467d2cda89f62e0a18cf89cec2d9e63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Azure Cosmos DB에서 분할된 그래프 사용

Azure Cosmos DB에서 Graph API의 주요 기능 중 하나는 수평 확장을 통해 대규모 그래프를 처리하는 기능입니다. 이 프로세스는 저장소 및 처리량 측면에서 독립적으로 확장될 수 있는 컨테이너라고도 하는 컬렉션을 활용하는 [Azure Cosmos DB의 분할 기능](partition-data.md#how-does-partitioning-work)을 통해 진행됩니다. Azure Cosmos DB는 모든 API에서 다음 유형의 컨테이너를 지원합니다.

- **고정 컬렉션**: 이러한 컬렉션은 초당 최대 10,000개 요청 단위를 갖는 최대 10GB 크기의 그래프 데이터베이스를 저장할 수 있습니다. 고정 컬렉션을 만들기 위해 데이터에 파티션 키 속성을 지정할 필요는 없습니다.

- **무제한 컬렉션**: 이러한 컬렉션은 수평 분할을 통해 10GB 제한을 초과하여 그래프를 저장하도록 자동으로 확장될 수 있습니다. 각 파티션은 10GB를 저장하며, 데이터는 무제한 컬렉션을 사용할 때 필요한 매개 변수인 **지정된 파티션 키**에 따라 자동으로 부하가 분산됩니다. 이 유형의 컨테이너는 거의 제한없는 크기의 데이터를 저장할 수 있으며 [지원 서비스에 문의하여](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request) 초당 최대 100,000개 이상으로 요청 단위를 늘릴 수 있습니다.

이 문서에서 그래프 데이터베이스가 분할되는 방식에 대한 사양은 꼭짓점(또는 절점) 및 모서리를 활용해서 설명됩니다.

## <a name="requirements-for-partitioned-graph"></a>분할된 그래프에 대한 요구 사항

다음은 분할된 그래프 컨테이너를 만들 때 이해해야 하는 세부 사항입니다.
- 컬렉션 크기가 10GB보다 커질 수 있고 10,000개 이상의 초당 요청 단위를(RU/s)를 할당해야 할 경우 **분할을 설정해야 합니다**.
- **꼭짓점 및 모서리 둘 다 JSON 문서**로 Azure Cosmos DB Graph API 컨테이너의 백 엔드에 저장됩니다.
- **꼭짓점에는 파티션 키가 필요합니다**. 이 키는 해시 알고리즘을 통해 꼭짓점이 저장되는 파티션을 결정합니다. 이 파티션 키의 이름은 공백이나 특수 문자 없는 단일 단어 문자열이고, 포털에서`/partitioning-key-name` 형식을 사용하여 새 컬렉션을 만들 때 정의됩니다.
- **모서리는 원본 꼭짓점과 함께 저장됩니다**. 즉, 각 꼭짓점에 대해, 해당 파티션 키는 돌출된 모서리와 함께 저장되는 위치를 정의합니다. 이것은 그래프 쿼리에서 `out()` 카디널리티를 사용할 때 교차 분할 쿼리를 방지하기 위한 것입니다.
- **그래프 쿼리는 파티션 키를 지정해야 합니다**. Azure Cosmos DB에서 수평 분할을 최대한 활용하려면, 가능할 경우 단일 꼭짓점을 선택할 때마다 파티션 키를 지정해야 합니다. 다음은 분할된 그래프에서 하나 이상의 꼭짓점을 선택하기 위한 쿼리입니다.

    - ID로 꼭짓점을 선택한 후 **`.has()` 단계를 사용하여 파티션 키 속성 지정** 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - **파티션 키 값 및 ID를 포함하는 튜플을 지정**하여 꼭짓점 선택 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - **파티션 키 값 및 ID 튜플 배열** 지정
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - 꼭짓점 집합을 선택하고 **파티션 키 값 목록 지정** 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>분할된 그래프를 사용할 때의 모범 사례

다음은 무제한 컬렉션에서 분할된 그래프를 사용하는 경우 가장 효율적인 성능 및 확장성을 보장하기 위해 수행해야 하는 지침입니다.
- **꼭짓점을 쿼리할 때 항상 파티션 키 값을 지정합니다**. 알려진 분할에서 꼭짓점을 가져오는 것이 성능 측면에서 가장 효율적인 방법입니다.
- **가능한 경우 모서리를 쿼리할 때마다 돌출된 방향을 사용합니다**. 위에서 언급한 것처럼 모서리는 돌출 방향의 원본 꼭짓점과 함께 저장됩니다. 즉, 이 패턴을 염두에 두고 데이터 및 쿼리를 설계하면 교차 파티션 쿼리를 다시 정렬할 가능성이 최소화됩니다.
- **데이터를 파티션에 균등하게 분산하는 파티션 키를 선택합니다**. 이러한 선택은 솔루션의 데이터 모델에 크게 좌우됩니다. [Azure Cosmos DB에서 분할 및 확장](partition-data.md)에서 해당 파티션 키를 만드는 방법에 대해 자세히 알아봅니다.
- **가능한 경우 파티션 경계 내의 데이터를 가져오도록 쿼리를 최적화합니다**. 최적의 분할 전략은 쿼리 패턴에 맞추는 것입니다. 단일 파티션의 데이터를 가져오는 쿼리는 가능한 최상의 성능을 제공합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Cosmos DB API를 사용한 분할의 개념 및 모범 사례를 간단히 제공했습니다. 

* [Azure Cosmos DB에서 분할 및 확장](partition-data.md)에 대해 알아봅니다.
* [Graph API의 Gremlin 지원](gremlin-support.md)에 대해 알아봅니다.
* [Graph API 소개](graph-introduction.md)에 대해 알아봅니다.
