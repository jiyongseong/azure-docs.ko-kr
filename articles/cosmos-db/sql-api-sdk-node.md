---
title: 'Azure Cosmos DB: SQL Node.js API, SDK 및 리소스 | Microsoft Docs'
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Node.js SDK의 각 버전 간 변경 내용을 포함하여 SQL Node.js API 및 SDK에 대한 모든 것을 알아봅니다.
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 5/3/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 271b090468dee306dc0bf2567aeff08eb0526642
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK for SQL API: 릴리스 정보 및 리소스
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

<table>

<tr><td>**SDK 다운로드**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**API 설명서**</td><td>[Node.js API 참조 설명서](https://docs.microsoft.com/javascript/api/documentdb/?view=azure-node-latest)</td></tr>

<tr><td>**SDK 설치 지침**</td><td>[설치 지침](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**SDK에 참여**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**샘플**</td><td>[Node.js 코드 샘플](sql-api-nodejs-samples.md)</td></tr>

<tr><td>**시작 자습서**</td><td>[Node.js SDK 시작](sql-api-nodejs-get-started.md)</td></tr>

<tr><td>**웹앱 자습서**</td><td>[Azure Cosmos DB를 사용하여 Node.js 웹 응용 프로그램 빌드](sql-api-nodejs-application.md)</td></tr>

<tr><td>**현재 지원되는 플랫폼**</td><td> 
[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

### <a name="1.14.4"/>1.14.4</a>
* npm 설명서가 수정되었습니다.

### <a name="1.14.3"/>1.14.3</a>
* 연결 문제에 대한 기본 재시도 지원이 추가되었습니다.
* 컬렉션 변경 피드 읽기에 대한 지원이 추가되었습니다.
* 일시적인 "읽기 세션을 사용할 수 없음" 세션 일관성 버그가 수정되었습니다.
* 쿼리 메트릭에 대한 지원이 추가되었습니다.
* http 에이전트의 최대 연결 수가 수정되었습니다.

### <a name="1.14.2"/>1.14.2</a>
* Azure DocumentDB 대신 Azure Cosmos DB를 참조하도록 업데이트된 설명서입니다.
* ConnectionPolicy에서 proxyUrl 설정에 대한 지원이 추가되었습니다.

### <a name="1.14.1"/>1.14.1</a>
* 대/소문자 구분 파일 시스템에 대해 사소한 수정이 있었습니다.

### <a name="1.14.0"/>1.14.0</a>
* 세션 일관성에 대한 지원이 추가되었습니다.
* 이 SDK 버전은 https://aka.ms/cosmosdb-emulator에서 다운로드할 수 있는 최신 버전의 Azure Cosmos DB 에뮬레이터가 필요합니다.

### <a name="1.13.0"/>1.13.0</a>
* 파티션 간 쿼리를 분할 검증했습니다
* 선행 및 후행 슬래시(및 해당 테스트)와의 리소스 링크 지원이 추가되었습니다.

### <a name="1.12.2"/>1.12.2</a>
*   npm 설명서가 수정되었습니다.

### <a name="1.12.1"/>1.12.1</a>
* 관련된 문서에 특수 유니코드 문자(LS, PS)가 있는 executeStoredProcedure의 버그를 수정했습니다.
* 파티션 키의 유니코드 문자를 사용하여 문서를 처리할 때 버그를 수정했습니다.
* 이름 미디어를 사용하여 컬렉션을 만들기 위한 지원을 수정했습니다. Github 문제 #114.
* 권한 부여 토큰에 대한 지원을 수정했습니다. Github 문제 #178.

### <a name="1.12.0"/>1.12.0</a>
* ConsistentPrefix라고 하는 새로운 [일관성 수준](consistency-levels.md)에 대한 지원이 추가되었습니다.
* UriFactory에 대한 지원이 추가되었습니다.
* 유니코드 지원 버그를 수정했습니다. GitHub 문제 #171.

### <a name="1.11.0"/>1.11.0</a>
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다.
* 파티션 간 쿼리에 대한 병렬 처리 수준을 제어하기 위한 옵션을 추가했습니다.
* Azure Cosmos DB 에뮬레이터에 대해 실행하는 경우 SSL 유효성 검사를 비활성화하기 위한 옵션을 추가했습니다.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.
* 단일 파티션 컬렉션에 대한 연속 토큰 버그를 수정했습니다. Github 문제 #107.
* 단일 매개 변수인 0을 처리하는 도중 executeStoredProcedure 버그를 수정했습니다. Github 문제 #155.

### <a name="1.10.2"/>1.10.2</a>
* SDK 버전을 포함하도록 수정된 사용자 에이전트 헤더입니다.
* 사소한 코드 정리입니다.

### <a name="1.10.1"/>1.10.1</a>
* SDK를 사용하여 에뮬레이터를 대상으로 지정할 때(hostname=localhost) SSL 유효성 검사를 사용하지 않도록 설정됩니다.
* 저장된 프로시저가 실행되는 동안 스크립트 로깅을 사용할 수 있도록 지원이 추가되었습니다.

### <a name="1.10.0"/>1.10.0</a>
* 파티션 간 병렬 쿼리에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDER BY 쿼리에 대한 지원이 추가되었습니다.

### <a name="1.9.0"/>1.9.0</a>
* 제한된 요청에 대한 재시도 정책 지원이 추가되었습니다. (제한된 요청은 오류 코드 429, 요청 속도가 너무 크다는 예외를 수신합니다.) 기본적으로 오류 코드 429가 발생하면 Azure Cosmos DB는 각 요청을 9번 다시 시도하며 응답 헤더에서 retryAfter 시간을 적용합니다. 이제 다시 시도 간의 서버에서 반환한 retryAfter 시간을 무시하려는 경우 고정된 다시 시도 간격 시간은 ConnectionPolicy 개체에서 RetryOptions 속성의 일부로 설정될 수 있습니다. Azure Cosmos DB는 제한된 요청 각각에 대해 30초 동안 대기하고(다시 시도 횟수와 관계 없이) 오류 코드 429를 포함하는 응답을 반환합니다. 이 시간도 ConnectionPolicy 개체에 있는 RetryOptions 속성에서 재정의할 수 있습니다.
* 이제 Cosmos DB는 x-ms-throttle-retry-count 및 x-ms-throttle-retry-wait-time-ms를 다시 시도 사이에 요청이 대기한 제한 다시 시도 수 및 누적 시간을 표시하는 모든 요청의 응답 헤더로 반환합니다.
* 기본 다시 시도 옵션의 일부를 재정의하는 데 사용할 수 있는 ConnectionPolicy 클래스에 RetryOptions 속성을 노출하여 RetryOptions 클래스를 추가했습니다.

### <a name="1.8.0"/>1.8.0</a>
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.

### <a name="1.7.0"/>1.7.0</a>
* 문서의 TTL(Time to Live) 기능에 대한 지원이 추가되었습니다.

### <a name="1.6.0"/>1.6.0</a>
* [분할된 컬렉션](partition-data.md) 및 [사용자 정의 성능 수준](performance-levels.md)이 구현되었습니다.

### <a name="1.5.6"/>1.5.6</a>
* 잘못된 concat 결과로 인해 링크를 반환하지 못했던 RangePartitionResolver.resolveForRead 버그가 수정되었습니다.

### <a name="1.5.5"/>1.5.5</a>
* hashParitionResolver resolveForRead() 해결: 예외를 throw하는 제공된 파티션 키가 없는 경우 모든 등록된 링크의 목록을 대신 반환합니다.

### <a name="1.5.4"/>1.5.4</a>
* 문제 해결 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - 전용 HTTPS 에이전트: Azure Cosmos DB 목적으로 전역 에이전트를 수정하지 않도록 합니다. 모든 lib의 요청에 대해 전용 에이전트를 사용 합니다.

### <a name="1.5.3"/>1.5.3</a>
* 문제 해결 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 미디어 ID에서 대시를 올바르게 처리합니다.

### <a name="1.5.2"/>1.5.2</a>
* 문제 해결 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 수신기 누수 경고

### <a name="1.5.1"/>1.5.1</a>
* 문제 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) 해결 - 대/소문자 구분 시스템으로 인해 Hash 폴더의 이름을 hash로 바꿉니다.

### <a name="1.5.0"/>1.5.0</a>
* 해시 및 범위 파티션 해결 프로그램을 추가하여 분할 지원 구현

### <a name="1.4.0"/>1.4.0</a>
* Upsert를 구현합니다. DocumentClient의 새로운 upsertXXX 메서드입니다.

### <a name="1.3.0"/>1.3.0</a>
* 다른 SDK와 정렬된 버전 번호를 가져오기 위해 건너뛰었습니다.

### <a name="1.2.2"/>1.2.2</a>
* 프라미스 래퍼를 새 리포지토리로 분할합니다.
* npm 레지스트리에 대한 패키지 파일로 업데이트합니다.

### <a name="1.2.1"/>1.2.1</a>
* ID 기반 라우팅을 구현합니다.
* 문제 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) 수정 - 현재 속성이 메서드 current()와 충돌합니다.

### <a name="1.2.0"/>1.2.0</a>
* 지리 공간 인덱스에 대한 지원이 추가되었습니다.
* 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스의 ID는 ?, /, #, &#47;&#47; 문자를 포함할 수 없으며 공백으로 끝날 수도 없습니다.
* 새 헤더 "인덱스 변환 진행률"을 ResourceResponse에 추가합니다.

### <a name="1.1.0"/>1.1.0</a>
* V2 인덱싱 정책을 구현합니다.

### <a name="1.0.3"/>1.0.3</a>
* 문제 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 핵심 및 프라미스 SDK에서 eslint 및 grunt 구성을 구현했습니다.

### <a name="1.0.2"/>1.0.2</a>
* 문제 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - 프라미스 래퍼에 오류가 있는 헤더가 포함되지 않습니다.

### <a name="1.0.1"/>1.0.1</a>
* readConflicts, readConflictAsync 및 queryConflicts를 추가하여 충돌을 쿼리하는 기능을 구현했습니다.
* API 설명서가 업데이트되었습니다.
* 문제 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 오류

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.14.4](#1.14.4) |2018년 5월 3일 |--- |
| [1.14.3](#1.14.3) |2018년 5월 3일 |--- |
| [1.14.2](#1.14.2) |2017년 12월 21일 |--- |
| [1.14.1](#1.14.1) |2017년 11월 10일 |--- |
| [1.14.0](#1.14.0) |2017년 11월 9일 |--- |
| [1.13.0](#1.13.0) |2017년 10월 11일 |--- |
| [1.12.2](#1.12.2) |2017년 8월 10일 |--- |
| [1.12.1](#1.12.1) |2017년 8월 10일 |--- |
| [1.12.0](#1.12.0) |2017년 5월 10일 |--- |
| [1.11.0](#1.11.0) |2017년 3월 16일 |--- |
| [1.10.2](#1.10.2) |2017년 1월 27일 |--- |
| [1.10.1](#1.10.1) |2016년 12월 22일 |--- |
| [1.10.0](#1.10.0) |2016년 10월 3일 |--- |
| [1.9.0](#1.9.0) |2016년 7월 7일 |--- |
| [1.8.0](#1.8.0) |2016년 6월 14일 |--- |
| [1.7.0](#1.7.0) |2016년 4월 26일 |--- |
| [1.6.0](#1.6.0) |2016년 3월 29일 |--- |
| [1.5.6](#1.5.6) |2016년 3월 8일 |--- |
| [1.5.5](#1.5.5) |2016년 2월 2일 |--- |
| [1.5.4](#1.5.4) |2016년 2월 1일 |--- |
| [1.5.2](#1.5.2) |2016년 1월 26일 |--- |
| [1.5.2](#1.5.2) |2016년 1월 22일 |--- |
| [1.5.1](#1.5.1) |2016년 1월 4일 |--- |
| [1.5.0](#1.5.0) |2015년 12월 31일 |--- |
| [1.4.0](#1.4.0) |2015년 10월 6일 |--- |
| [1.3.0](#1.3.0) |2015년 10월 6일 |--- |
| [1.2.2](#1.2.2) |2015년 9월 10일 |--- |
| [1.2.1](#1.2.1) |2015년 8월 15일 |--- |
| [1.2.0](#1.2.0) |2015년 8월 5일 |--- |
| [1.1.0](#1.1.0) |2015년 7월 9일 |--- |
| [1.0.3](#1.0.3) |2015년 6월 4일 |--- |
| [1.0.2](#1.0.2) |2015년 5월 23일 |--- |
| [1.0.1](#1.0.1) |2015년 5월 15일 |--- |
| [1.0.0](#1.0.0) |2015년 4월 8일 |--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

