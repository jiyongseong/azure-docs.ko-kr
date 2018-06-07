---
title: 'Azure Cosmos DB: .NET 변경 피드 프로세서 API, SDK 및 리소스 | Microsoft Docs'
description: 릴리스 날짜, 사용 중지 날짜 및 .NET 변경 피드 프로세서 SDK의 각 버전 간 변경 내용을 포함하여 변경 피드 프로세서 API 및 SDK에 대한 모든 것을 알아봅니다.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kfile
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/19/2018
ms.author: maquaran
ms.openlocfilehash: 7ed5772df4d8677fe878d7ced831dc15bbe8cac0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885139"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 변경 피드 프로세서 SDK: 다운로드 및 릴리스 정보
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

|   |   |
|---|---|
|**SDK 다운로드**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 설명서**|[피드 프로세서 라이브러리 API 참조 문서 변경](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**시작**|[변경 피드 프로세서 .NET SDK 시작](change-feed.md)|
|**현재 지원되는 프레임워크**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>릴리스 정보

### <a name="stable-builds"></a>안정적인 빌드

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* 보류 중인 작업 예측에서 해결합니다.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* 안정성 향상
  * 일부 파티션에서 관찰자를 중지할 수 있는 취소된 작업 문제의 처리를 수정합니다.
* 수동 검사점 설정 지원
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.21 이상과 호환

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* .NET Standard 2.0에 대한 지원을 추가합니다. 이제 패키지는 `netstandard2.0` 및 `net451` 프레임워크 모니커를 지원합니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.17.0 이상과 호환 가능합니다.
* [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 버전 1.5.1 이상과 호환 가능합니다.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 변경 피드가 비어 있거나 보류 중인 작업이 없을 때 예상되는 남은 작업을 계산할 때 발생하는 문제를 해결했습니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 변경 피드에서 처리될 나머지 작업의 추정치를 얻을 수 있는 메서드가 추가되었습니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.14.1 이하와 호환 가능합니다.

### <a name="pre-release-builds"></a>시험판 빌드

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2 시험판
* 부 버전 API 변경 내용:
  * 사용되지 않는 것으로 표시된 ChangeFeedProcessorOptions.IsAutoCheckpointEnabled를 제거했습니다.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-시험판
* 안정성 향상:
  * 임대 저장소 초기화를 더 효율적으로 처리합니다. 임대 저장소가 비어 있는 경우 프로세서의 한 인스턴스만 초기화할 수 있으며 다른 인스턴스는 대기합니다.
  * 더 안정적인/효율적인 임대 갱신/릴리스를 제공합니다. 하나의 파티션 임대의 갱신 및 해제는 다른 파티션의 갱신과 독립적입니다. v1에서는 모든 파티션에 대해 순차적으로 수행됩니다.
* 새 v2 API:
  * 프로세서의 유연한 구축을 위한 작성기 패턴: ChangeFeedProcessorBuilder 클래스입니다.
    * 모든 조합의 매개 변수를 사용할 수 있습니다.
    * 모니터링 및/또는 임대 컬렉션에 DocumentClient 인스턴스를 사용할 수 있습니다(v1에서 사용할 수 없음).
  * 이제 IChangeFeedObserver.ProcessChangesAsync는 CancellationToken을 사용합니다.
  * IRemainingWorkEstimator - 남은 작업 추정을 프로세서에서 별도로 사용할 수 있습니다.
  * 새 확장성 지점:
    * IParitionLoadBalancingStrategy - 프로세서의 인스턴스 간 파티션의 사용자 지정 부하 분산용
    * ILease, ILeaseManager - 사용자 지정 임대 관리용
    * IPartitionProcessor - 파티션에 대한 사용자 지정 처리 변경용
* 로깅 - [LibLog](https://github.com/damianh/LibLog) 라이브러리를 사용합니다.
* v1 API를 사용하여 100% 이전 버전과 호환 가능합니다.
* 새 코드 기준입니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.21.1 이상과 호환 가능합니다.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.3.2](#1.3.2) |2018년 4월 18일 |--- |
| [1.3.1](#1.3.1) |2018년 3월 13일 |--- |
| [1.2.0](#1.2.0) |2017년 10월 31일 |--- |
| [1.1.1](#1.1.1) |2017년 8월 29일 |--- |
| [1.1.0](#1.1.0) |2017년 8월 13일 |--- |
| [1.0.0](#1.0.0) |2017년 7월 7일 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 

