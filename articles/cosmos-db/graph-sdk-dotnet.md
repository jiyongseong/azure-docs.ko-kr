---
title: Azure CosmosDB Graph API .NET SDK 및 리소스 | Microsoft Docs
description: 릴리스 날짜, 사용 중지 날짜 및 각 버전 간에 변경된 내용을 포함하는 Azure CosmosDB Graph API에 대한 모든 것을 알아봅니다.
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 41608e76216f63ccf3165cc3959787c661d71fa0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure Cosmos DB Graph .NET API: 다운로드 및 릴리스 정보

|   |   |
|---|---|
|**SDK 다운로드**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**API 설명서**|[.NET API 참조 설명서](https://aka.ms/acdbgraphapiref)|
|**빠른 시작**|[Azure Cosmos DB: .NET 및 Graph API를 사용하여 그래프 앱 만들기](create-graph-dotnet.md)|
|**자습서**|[Azure CosmosDB: Graph API로 컨테이너 만들기](tutorial-develop-graph-dotnet.md)|
|**현재 지원되는 프레임워크**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>릴리스 정보

.NET, Gremlin.Net에 대해 오픈 소스 Gremlin 드라이버를 사용하는 것이 좋습니다. [Azure Cosmos DB: .NET 및 Graph API를 사용하여 그래프 앱 만들기](create-graph-dotnet.md)에서 시작하는 방법에 대해 알아봅니다.

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-preview

#### <a name="bug-fixes"></a>버그 수정
* 필요에 따라 로드에 고정`appsettings.json`(`netstandard1.6`)

#### <a name="whats-new"></a>새로운 기능
* Microsoft.Azure.Graphs를 대상 플랫폼 AnyCPU로 전환합니다.
* `net461` 패키지 매니페스트에서 Mono 어셈블리를 제거합니다.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>새로운 기능
* `.netstandard 1.6`에 대한 지원이 추가됨
  * `Microsoft.Azure.DocumentDB.Core >= 1.5.1` 필요
* 기존 구문 분석기 교체를 위해 새 `gremlin-groovy` 구문 분석기가 추가되었습니다. 이 구문 분석기는 Tinkerpop의 `gremlin-groovy` 구문의 하위 집합을 지원하며, 다음을 포함합니다.
  * 구문 분석 성능이 2배로 향상됩니다.
  * 이전 구문 분석기의 문자열에서의 문자 이스케이프, 잘못 처리된 리터럴 값 및 기타 이상값과 관련된 여러 문제가 해결되었습니다.
* 에지 조건자를 사용하는 순회에 대한 최적화가 추가되었습니다.
  *  필터를 사용하는 순회 홉은 이 개선 사항이 표시됩니다. 예: `g.V('1').outE().has('name', 'marko').inV()`.
* `limit()` 단계를 사용하는 순회에 대한 최적화가 추가되었습니다.

#### <a name="breaking-changes"></a>주요 변경 내용
* .NET Framework 4.5.1에 대한 지원이 제거됨

* 새로운 구문 분석기는 `gremlin-groovy` 문법에 부합합니다. 결과적으로, 이전에 작업한 일부 식은 새로운 구문 분석기에 대해 모호합니다. 다음은 한 가지 중요 사례입니다.
  * `in` 및 `as`는 `gremlin-groovy`에서 예약된 키워드이므로 구문 오류를 방지하기 위해 이러한 단계는 `.in()` 또는 `.as()`로 정규화되어야 합니다. 예: `g.V().repeat(in()).times(2)` -> _구문 오류 발생_  
 `g.V().repeat(__.in()).times(2)` -> _성공_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* 초기 미리 보기 릴리스입니다.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Azure Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [0.3.1-preview](#0.3.1-preview) |2017년 10월 17일 |--- |
| [0.3.0-preview](#0.3.0-preview) |2017년 10월 2일 |--- |
| [0.2.4-preview](#0.2.4-preview) |2017년 8월 4일 |--- |
| [0.2.2-preview](#0.2.2-preview) |2017년 6월 23일 |--- |
| [0.2.1-preview](#0.2.2-preview) |2017년 6월 8일 |--- |
| [0.2.0-preview](#0.2.2-preview) |2017년 5월 10일 |--- |
| [0.1.0-preview](#0.1.0-preview) |2017년 5월 8일 |--- |

## <a name="see-also"></a>참고 항목
Azure DB Cosmos Graph API에 대한 자세한 내용은 [Azure Cosmos DB: Graph API 소개](graph-introduction.md)를 참조하세요. 
