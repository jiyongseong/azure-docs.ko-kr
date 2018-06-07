---
title: 'Azure Cosmos DB: SQL 비동기 Java API, SDK 및 리소스 | Microsoft Docs'
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL 비동기 Java SDK의 각 버전 간 변경 내용을 포함하여 SQL 비동기 Java API 및 SDK에 대한 모든 것을 알아봅니다.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/18/2018
ms.author: sngun
ms.openlocfilehash: 9dae401bc007b78d8ee3c6993735650e3b26b9d1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359529"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK for SQL API: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API 비동기 Java SDK는 [Netty 라이브러리](http://netty.io/)를 지원하고 비동기 작업을 제공하기 때문에 SQL API Java SDK와는 다릅니다. 기존 [SQL API Java SDK](sql-api-sdk-java.md)는 비동기 작업을 지원하지 않습니다. 

<table>

<tr><td>**SDK 다운로드**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API 설명서**</td><td>[Java API 참조 설명서](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**SDK에 참여**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**시작**</td><td>[비동기 Java SDK 시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**코드 샘플**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**성능 팁**</td><td>[Github 추가 정보](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**지원되는 최소 런타임**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* 고유 인덱스 정책에 대한 지원이 추가되었습니다.
* 피드 옵션에서 응답 연속 토큰 크기를 제한할 수 있는 지원이 추가되었습니다.
* 교차 파티션 쿼리에서 파티션 분할에 대한 지원이 추가되었습니다.
* Json 타임스탬프 serialization의 버그를 수정했습니다([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Json 열거형 serialization의 버그를 수정했습니다.
* 크기가 2MB인 문서 관리의 버그를 수정했습니다([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* 버그 때문에 com.fasterxml.jackson.core:jackson-databind 종속성이 2.9.5로 업그레이드되었습니다([jackson-databind: github #1599](https://github.com/FasterXML/jackson-databind/issues/1599)).
* 버그 때문에 rxjava-extras 종속성이 0.8.0.17로 업그레이드되었습니다([rxjava-extras: github #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* pom 파일의 메타데이터 설명이 설명서의 나머지 부분과 일치하도록 업데이트되었습니다.
* 구문이 개선되었습니다([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 쿼리에서 백 압력 지원이 추가되었습니다.
* 쿼리에서 파티션 키 범위 ID에 대한 지원이 추가되었습니다.
* 요청 헤더에서 더 큰 연속 토큰을 허용하도록 수정합니다(bugfix github #24).
* 주 스레드가 완료된 후 JVM이 종료하도록 4.1.22.Final로 Netty 종속성이 업그레이드되었습니다.
* 마스터 리소스를 읽을 때 세션 토큰을 전달하지 못하도록 수정합니다.
* 더 많은 예제가 추가되었습니다.
* 더 많은 벤치마킹 시나리오가 추가되었습니다.
* 적절한 java 문서 생성을 위한 Java 헤더 파일을 수정했습니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 게이트웨이 모드에서 [Netty 라이브러리](http://netty.io/)를 사용하여 비차단 IO에 대한 종단 간 지원을 제공하는 GA SDK입니다. 

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가됩니다. 따라서 가능한 빨리 항상 최신 SDK 버전으로 업그레이드하는 것이 좋습니다.

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.0.2](#1.0.2) |2018년 5월 18일|--- |
| [1.0.1](#1.0.1) |2018년 4월 20일|--- |
| [1.0.0](#1.0.0) |2018년 2월 27일|--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

