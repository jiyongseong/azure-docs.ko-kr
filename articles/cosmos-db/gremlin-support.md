---
title: Azure Cosmos DB Gremlin 지원 | Microsoft Docs
description: Apache TinkerPop에서 Gremlin 언어를 알아봅니다. Azure Cosmos DB에서 어떤 기능과 단계를 사용할 수 있는지 알아보기
services: cosmos-db
documentationcenter: ''
author: LuisBosquez
manager: kfile
ms.assetid: 6016ccba-0fb9-4218-892e-8f32a1bcc590
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: c3d80fcaa38d0f1d7fa1770879ca9b40642bb796
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Azure Cosmos DB Gremlin 그래프 지원
Azure Cosmos DB는 [Apache Tinkerpop](http://tinkerpop.apache.org)의 그래프 통과 언어로서, 그래프 엔터티를 만들고 그래프 쿼리를 수행하기 위한 Graph API인 [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)을 지원합니다. Gremlin 언어를 사용하여 그래프 엔터티(예: 꼭짓점 및 에지)를 만들고, 해당 엔터티 내에서 속성을 수정하고, 쿼리 및 순회를 수행하고, 엔터티를 삭제할 수 있습니다. 

Azure Cosmos DB는 그래프 데이터베이스에 엔터프라이즈급 기능을 제공합니다. 여기에는 전역 배포, 독립적인 저장소 및 처리량 크기 조정, 예측 가능한 1자리 밀리초 대기 시간, 자동 인덱싱, SLA, 둘 이상의 Azure 지역에 걸친 데이터베이스 계정에 대한 읽기 가용성이 포함됩니다. Azure Cosmos DB는 TinkerPop/Gremlin을 지원하므로 코드를 변경하지 않고도 다른 그래프 데이터베이스를 사용하여 작성된 응용 프로그램을 쉽게 마이그레이션할 수 있습니다. 또한 Gremlin 지원을 통해, Azure Cosmos DB는 [Apache Spark GraphX](http://spark.apache.org/graphx/)와 같은 TinkerPop 지원 분석 프레임워크와 원활하게 통합됩니다. 

이 문서에서는 Gremlin을 빠르게 연습해볼 수 있는 과정을 제공하고, Graph API에서 지원되는 Gremlin 기능 및 단계를 설명합니다.

## <a name="gremlin-by-example"></a>Gremlin 예제
샘플 그래프를 사용하여 Gremlin에서 쿼리를 표현할 수 있는 방법을 살펴봅니다. 다음 그림에서는 사용자, 관심 영역 및 장치에 대한 데이터를 그래프 형태로 관리하는 비즈니스 응용 프로그램을 보여 줍니다.  

![사람, 장치 및 관심 분야를 보여 주는 샘플 데이터베이스](./media/gremlin-support/sample-graph.png) 

이 그래프에는 다음과 같은 꼭짓점 유형(Gremlin의 "레이블")이 있습니다.

- 사람: 그래프에는 Robin, Thomas 및 Ben 세 사람이 있음
- 관심 분야: 관심 있는 분야로 이 예제에서는 축구 게임
- 장치: 사람들이 사용하는 장치
- 운영 체제: 장치가 실행되는 운영 체제

위 엔터티 간 관계는 다음 에지 유형/레이블을 통해 나타냅니다.

- Knows: 예: "Thomas knows Robin"
- Interested: 그래프에서 사람의 관심 분야를 나타내는 데 사용합니다(예: "Ben is interested in Football").
- RunsOS: 랩톱은 Windows OS를 실행합니다.
- Uses: 사람이 사용하는 장치를 나타냅니다. 예를 들어 Robin은 일련 번호가 77인 Motorola 휴대폰을 사용합니다.

[Gremlin 콘솔](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console)을 사용하여 이 그래프에 대해 일부 작업을 실행해 보겠습니다. 또한 원하는 플랫폼(Java, Node.js, Python 또는 .NET)에서 Gremlin 드라이버를 사용하여 이러한 작업을 수행할 수도 있습니다.  Azure Cosmos DB에서 지원되는 기능을 살펴보기 전에 구문에 익숙해질 수 있는 몇 가지 예제를 확인해 보겠습니다.

먼저 CRUD를 살펴보겠습니다. 다음 Gremlin 문은 "Thomas" 꼭짓점을 그래프에 삽입합니다.

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

그런 후 다음 Gremlin 문은 Thomas와 Robin 사이에 "knows" 에지를 삽입합니다.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

다음 쿼리는 이름의 내림차순으로 "person" 꼭짓점을 반환합니다.
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

그래프의 강점은 "Thomas의 친구들은 어떤 운영 체제를 사용하는가?"와 같은 질문에 답변해야 할 경우에 잘 나타납니다. 다음의 간단한 Gremlin 순회를 실행하여 그래프에서 해당 정보를 가져올 수 있습니다.

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
이제 Azure Cosmos DB에서 Gremlin 개발자에게 제공하는 기능을 살펴보겠습니다.

## <a name="gremlin-features"></a>Gremlin 기능
TinkerPop은 광범위한 그래프 기술을 지원하는 표준입니다. 따라서 그래프 공급자가 제공하는 기능을 설명하기 위한 표준 용어를 포함합니다. Azure Cosmos DB는 여러 서버 또는 클러스터에서 분할될 수 있는 지속적이고 높은 동시성을 갖는 쓰기 가능한 그래프 데이터베이스를 제공합니다. 

다음 표에는 Azure Cosmos DB를 통해 구현되는 TinkerPop 기능이 나와 있습니다. 

| Category | Azure Cosmos DB 구현 |  메모 | 
| --- | --- | --- |
| 그래프 기능 | Persistence 및 ConcurrentAccess를 제공합니다. 트랜잭션을 지원하도록 설계되었습니다. | 컴퓨터 메서드를 Spark 커넥터를 통해 구현할 수 있습니다. |
| 변수 기능 | 부울, 정수, 바이트, Double, Float, Long, 문자열을 지원합니다. | 기본 형식을 지원하고, 데이터 모델을 통해 복잡한 형식과 호환됩니다. |
| 꼭짓점 기능 | RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty를 지원합니다.  | 꼭짓점 만들기, 수정 및 삭제를 지원합니다. |
| 꼭짓점 속성 기능 | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | 꼭짓점 속성 만들기, 수정 및 삭제를 지원합니다. |
| 에지 기능 | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | 에지 만들기, 수정 및 삭제를 지원합니다. |
| 에지 속성 기능 | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | 에지 속성 만들기, 수정 및 삭제를 지원합니다. |

## <a name="gremlin-wire-format-graphson"></a>Gremlin 통신 형식: GraphSON

Azure Cosmos DB는 Gremlin 작업의 결과를 반환할 때 [GraphSON 형식](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format)을 사용합니다. GraphSON은 JSON을 사용하여 꼭짓점, 에지 및 속성(단일 및 다중 값 속성)을 나타내기 위한 Gremlin 표준 형식입니다. 

예를 들어 다음 코드 조각은 Azure Cosmos DB에서 *클라이언트로 반환되는* 꼭짓점의 GraphSON 표현을 보여 줍니다. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

꼭짓점에 대해 GraphSON에서 사용되는 속성은 다음과 같습니다.

| 자산 | 설명 |
| --- | --- |
| id | 꼭짓점의 ID입니다. 고유해야 합니다(해당되는 경우 _partition 값과 조합). |
| label | 꼭짓점의 레이블입니다. 선택 사항이며, 엔터티 형식을 설명하는 데 사용됩니다. |
| 형식 | 비그래프 문서의 꼭짓점을 구별하는 데 사용됩니다. |
| properties | 꼭짓점과 연결된 사용자 정의 속성의 모음입니다. 각 속성에는 값이 여러 개 있을 수 있습니다. |
| _partition(구성 가능) | 꼭짓점의 파티션 키입니다. 그래프를 여러 서버로 확장하는 데 사용할 수 있습니다. |
| outE | 여기에는 꼭짓점의 바깥 에지 목록이 포함됩니다. 꼭짓점과 함께 인접 정보를 저장하므로 순회를 빠르게 실행할 수 있습니다. 에지는 해당 레이블을 기준으로 그룹화됩니다. |

또한 에지에는 그래프의 다른 부분으로 탐색하는 데 도움이 되는 다음 정보가 포함됩니다.

| 자산 | 설명 |
| --- | --- |
| id | 에지의 ID입니다. 고유해야 합니다(해당되는 경우 _partition 값과 조합). |
| label | 에지의 레이블입니다. 이 속성은 선택 사항이며, 관계 유형을 설명하는 데 사용됩니다. |
| inV | 여기에는 에지의 모든 꼭지점 목록이 포함됩니다. 에지와 함께 인접 정보를 저장하므로 순회를 빠르게 실행할 수 있습니다. 꼭짓점은 해당 레이블을 기준으로 그룹화됩니다. |
| properties | 에지와 연결된 사용자 정의 속성의 모음입니다. 각 속성에는 값이 여러 개 있을 수 있습니다. |

각 속성은 배열 내에 여러 값을 저장할 수 있습니다. 

| 자산 | 설명 |
| --- | --- |
| 값 | 속성의 값입니다.

## <a name="gremlin-steps"></a>Gremlin 단계
이제 Azure Cosmos DB에서 지원되는 Gremlin 단계를 살펴보겠습니다. Gremlin에 대한 전체 참조는 [TinkerPop 참조](http://tinkerpop.apache.org/docs/current/reference)를 참조하세요.

| 단계 | 설명 | TinkerPop 3.2 설명서 |
| --- | --- | --- |
| `addE` | 두 꼭짓점 사이에 에지를 추가합니다. | [addE 단계](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | 그래프에 꼭짓점을 추가합니다. | [addV 단계](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | 모든 순회가 값을 반환하는지 확인합니다. | [and 단계](http://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | 단계의 출력에 변수를 할당하는 단계 변조기 | [as 단계](http://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | `group` 및 `order`에서 사용되는 단계 변조기 | [by 단계](http://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | 결과를 반환하는 첫 번째 순회를 반환합니다. | [coalesce 단계](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | 상수 값을 반환합니다. `coalesce`에 사용됩니다.| [constant 단계](http://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | 순회에서 해당 개수를 반환합니다. | [count 단계](http://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | 중복 항목을 제거하고 값을 반환합니다. | [dedup 단계](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | 값(꼭짓점/에지)을 삭제합니다. | [drop 단계](http://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | 결과의 집계를 계산하는 장벽으로 작동합니다.| [fold 단계](http://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | 지정된 레이블을 기준으로 값을 그룹화합니다.| [group 단계](http://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | 속성, 꼭짓점, 에지를 필터링하는 데 사용됩니다. `hasLabel`, `hasId`, `hasNot` 및 `has` 변형을 지원합니다. | [has 단계](http://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | 스트림에 값을 삽입합니다.| [inject 단계](http://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | 부울 식을 사용하여 필터를 수행하는 데 사용됩니다. | [is 단계](http://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | 순회의 항목 수를 제한하는 데 사용됩니다.| [limit 단계](http://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | local은 하위 쿼리와 비슷하게 순회 섹션을 래핑합니다. | [local 단계](http://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | 필터의 부정을 생성하는 데 사용됩니다. | [not 단계](http://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | 결과를 생성하는 경우 지정된 순회의 결과를 반환하고, 그렇지 않으면 호출하는 요소를 반환합니다. | [optional 단계](http://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | 순회 중 하나 이상이 값을 반환하도록 합니다. | [or 단계](http://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | 결과를 지정된 정렬 순서로 반환합니다. | [order 단계](http://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | 순회의 전체 경로를 반환합니다. | [path 단계](http://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | 속성을 맵으로 투영합니다. | [project 단계](http://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | 지정된 레이블에 대한 속성을 반환합니다. | [properties 단계](http://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | 지정된 값 범위로 필터링합니다.| [range 단계](http://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | 지정된 횟수 동안 단계를 반복합니다. 반복에 사용됩니다. | [repeat 단계](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | 순회의 결과를 샘플링하는 데 사용됩니다. | [sample 단계](http://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | 순회의 결과를 투영하는 데 사용됩니다. |  [select 단계](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | 순회의 비차단 집계에 사용됩니다. | [store 단계](http://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | 꼭짓점에서의 경로를 트리로 집계합니다. | [tree 단계](http://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | 반복기를 단계로 언롤합니다.| [unfold 단계](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | 여러 순회의 결과를 병합합니다.| [union 단계](http://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | 꼭짓점 및 에지 `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, `otherV` 간 순회에 필요한 단계가 포함되어 있습니다. | [vertex 단계](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | 순회의 결과를 필터링하는 데 사용. `eq`, `neq`, `lt`, `lte`, `gt`, `gte` 및 `between` 연산자 지원  | [where 단계](http://tinkerpop.apache.org/docs/current/reference/#where-step) |

Azure Cosmos DB에서 제공하는 쓰기 최적화 엔진은 기본적으로 꼭짓점 및 에지 내의 모든 속성에 대한 자동 인덱싱을 지원합니다. 따라서 필터가 있는 쿼리, 범위 쿼리, 속성에 대한 정렬 또는 집계가 인덱스에서 처리되고 효율적으로 제공됩니다. Azure Cosmos DB에서 인덱싱이 작동하는 방식에 대한 자세한 내용은 [스키마 독립적 인덱싱](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [SDK를 사용하여](create-graph-dotnet.md) 그래프 응용 프로그램 빌드 시작 
* Azure Cosmos DB에서 [그래프 지원](graph-introduction.md)에 대해 알아보기
