---
title: Azure Cosmos DB에서 변경 피드 지원 사용 | Microsoft Docs
description: Azure Cosmos DB의 변경 피드 지원을 사용하여 문서에서 변경 내용을 추적하고 트리거와 마찬가지로 이벤트 기반 처리를 수행하고 캐시 및 분석 시스템을 최신 상태로 유지합니다.
keywords: 변경 피드
services: cosmos-db
author: rafats
manager: kfile
documentationcenter: ''
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: be59f1a9dc19fffdb6a952c7db73756909036bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Azure Cosmos DB에서 변경 피드 지원 사용

[Azure Cosmos DB](../cosmos-db/introduction.md)는 IoT, 게이밍, 소매, 운영 로깅 응용 프로그램에 적합한 빠르고 유연한 전역 복제 데이터베이스입니다. 이러한 응용 프로그램에서 일반적인 설계 패턴은 데이터 변경 사항을 사용하여 추가 작업을 시작하는 것입니다. 이러한 추가 작업의 예를 들면 다음과 같습니다. 

* 문서가 삽입 또는 수정될 경우 알림 또는 API에 대한 호출 트리거링.
* IoT에 대한 스트림 처리 또는 분석 수행.
* 캐시, 검색 엔진 또는 데이터 웨어하우스와 동기화하거나 데이터를 콜드 스토리지로 아카이브하여 추가 데이터 이동.

Azure Cosmos DB의 **변경 피드 지원**을 사용하면 다음 그림과 같이 이러한 패턴 각각에 대해 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

![Azure Cosmos DB 변경 피드를 사용하여 실시간 분석 및 이벤트 기반 컴퓨팅 시나리오 작동](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Azure Cosmos DB의 모든 데이터 모델 및 컨테이너에 대해 변경 피드 지원이 제공됩니다. 하지만 변경 피드는 SQL 클라이언트를 사용하여 읽고 항목을 JSON 형식으로 직렬화합니다. MongoDB 클라이언트는 JSON 형식으로 인해 BSON 형식 문서와 JSON 형식의 변경 피드 간 불일치가 나타납니다.

다음 비디오에서는 Azure Cosmos DB 프로그램 관리자인 Andrew Liu가 Azure Cosmos DB 변경 피드 작동 방법을 설명합니다.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>변경 피드의 작동 방식

Azure Cosmos DB의 변경 피드 지원은 모든 변경 사항에 대해 Azure Cosmos DB 컬렉션을 수신하여 작동합니다. 그런 다음 변경된 문서가 수정된 순서로 정렬된 목록이 출력됩니다. 변경 사항은 지속적이며, 비동기적 및 증분적으로 처리할 수 있고 출력을 하나 이상의 소비자 사이에 분산하여 병렬 처리가 가능합니다. 

변경 피드는 이 문서의 뒷부분에서 설명하는 대로 세 가지 방식으로 읽을 수 있습니다.

1.  [Azure Functions 사용](#azure-functions)
2.  [Azure Cosmos DB SDK 사용](#rest-apis)
3.  [Azure Cosmos DB 변경 피드 프로세서 라이브러리 사용](#change-feed-processor)

변경 피드는 문서 컬렉션 내에서 각 파티션 키 범위에 대해 사용할 수 있으며 따라서 다음 그림과 같이 한 명 이상의 소비자에게 배포하여 병렬 처리할 수 있습니다.

![Azure Cosmos DB 변경 피드의 분산 처리](./media/change-feed/changefeedvisual.png)

추가 정보:
* 변경 피드는 기본적으로 모든 계정에 사용됩니다.
* 쓰기 지역 또는 [읽기 지역](distribute-data-globally.md)에서 [프로비전된 처리량](request-units.md)을 사용하여 Azure Cosmos DB의 다른 작업과 마찬가지로 변경 피드에서 읽을 수 있습니다.
* 변경 피드는 컬렉션 내의 문서에 수행된 삽입 및 업데이트 작업을 포함합니다. 삭제 대신 문서 내에서 "soft-delete" 플래그를 설정하여 삭제를 캡처할 수 있습니다. 또는 [TTL 기능](time-to-live.md)을 통해 문서에 대한 제한 만료 기간을 설정할 수 있습니다. 예를 들어, 24시간으로 설정하고 해당 속성의 값을 사용하여 삭제를 캡처합니다. 이 솔루션을 사용하여 TTL 만료 기간보다 짧은 시간 간격 내에 변경 내용을 처리해야 합니다.
* 각 문서에 대한 변경 내용이 한 번 변경 피드에 즉시 표시되고 클라이언트는 해당 검사점 논리를 관리합니다. 변경 피드 프로세서 라이브러리는 자동 검사점 및 “최소 한 번” 의미 체계를 제공합니다.
* 지정된 문서에 대한 가장 최근의 변경 내용만이 변경 로그에 포함됩니다. 중간 변경 내용을 사용할 수 없습니다.
* 변경 피드는 각 파티션 키 값 내에서 수정된 순서로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다.
* 특정 시점에서 변경 내용을 동기화할 수 있습니다. 즉, 변경 내용을 사용할 수 있는 고정 데이터 보존 기간이 없습니다.
* 변경 내용은 파티션 키 범위에서 사용할 수 있습니다. 이 기능을 사용하면 대규모 컬렉션의 변경 내용을 여러 소비자/서버에 의해 병렬로 처리할 수 있습니다.
* 응용 프로그램은 동일한 컬렉션에서 동시에 여러 변경 피드를 요청할 수 있습니다.
* ChangeFeedOptions.StartTime을 사용하여 최초 시작 지점을 제공할 수 있습니다. 예를 들어 이렇게 주어진 클록 시간에 해당하는 연속 토큰을 찾을 수 있습니다. ContinuationToken을 지정할 경우 이것이 StartTime 및 StartFromBeginning 값보다 우선합니다. ChangeFeedOptions.StartTime의 정확도는 5초입니다. 

## <a name="use-cases-and-scenarios"></a>사용 사례 및 시나리오

변경 피드를 사용하면 많은 양의 쓰기가 포함된 큰 데이터 집합을 효율적으로 처리할 수 있고 변경된 내용을 식별하는 전체 데이터 집합을 쿼리하는 대안을 제공합니다. 

예를 들어, 변경 피드를 사용하여 다음 작업을 효율적으로 수행할 수 있습니다.

* Azure Cosmos DB에 저장된 데이터를 사용하여 캐시, 검색 인덱스 또는 데이터 웨어하우스를 업데이트합니다.
* 응용 프로그램 수준 데이터 계층 및 보관을 구현합니다. 즉, Azure Cosmos DB에 "핫 데이터"를 저장하고 "콜드 데이터"를 [Azure Blob Storage](../storage/common/storage-introduction.md) 또는 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)로 내보냅니다.
* 다른 파티션 구성표를 사용하여 다른 Azure Cosmos DB 계정에 대한 중단 시간이 없는 마이그레이션을 수행합니다.
* Azure Cosmos DB를 사용하여 [Azure의 람다 파이프라인](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)을 구현합니다. Azure Cosmos DB는 수집 및 쿼리를 모두 처리할 수 있는 확장성이 뛰어난 데이터베이스 솔루션을 제공하고 TCO가 낮은 람다 아키텍처를 구현합니다. 
* 장치, 센서, 인프라 및 응용 프로그램에서 이벤트 데이터를 수신하고 저장하며 [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md) 또는 [Apache Spark](../hdinsight/spark/apache-spark-overview.md)를 사용하여 이러한 이벤트를 실시간으로 처리합니다. 

다음 그림은 Azure Cosmos DB를 사용하여 수집과 쿼리를 모두 수행하는 lambda 파이프라인이 어떻게 변경 피드 지원을 사용할 수 있는가를 보여줍니다. 

![수집 및 쿼리를 위한 Azure Cosmos DB 기반 lambda 파이프라인](./media/change-feed/lambda.png)

또한 [서버를 사용하지 않는](http://azure.com/serverless) 웹 및 모바일 앱 내에서는 [Azure Functions](#azure-functions)를 사용하여 해당 장치에 푸시 알림을 보내는 등의 특정 작업을 트리거하기 위해 고객의 프로필, 기본 설정 또는 위치에 대한 변경 내용과 같은 이벤트를 추적할 수 있습니다. 예를 들어, Azure Cosmos DB를 사용하여 게임을 빌드하는 경우 변경 피드를 사용하여 완료된 게임의 점수에 따라 실시간 순위표를 구현할 수 있습니다.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Azure Functions 사용 

Azure Functions를 사용하는 경우 Azure Cosmos DB 변경 피드에 연결하는 가장 간단한 방법은 사용자의 Azure Functions 앱에 Azure Cosmos DB 트리거를 추가하는 것입니다. Azure Functions 앱에 Azure Cosmos DB 트리거를 만들 경우 연결할 Azure Cosmos DB 컬렉션을 선택합니다. 컬렉션이 변경될 때마다 함수가 트리거됩니다. 

트리거는 Azure Functions 포털에서, Azure Cosmos DB 포털에서 또는 프로그래밍 방식으로 만들 수 있습니다. 자세한 내용은 [Azure Cosmos DB: Azure Functions를 통한, 서버를 사용하지 않는 데이터베이스 컴퓨팅](serverless-computing-database.md)을 참조하세요.

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>SDK 사용

Azure Cosmos DB의 [SQL SDK](sql-api-sdk-dotnet.md)는 변경 피드를 읽고 관리할 수 있는 모든 기능을 제공합니다. 하지만 많은 기능에는 많은 책임도 따릅니다. 검사점을 관리하고 문서 시퀀스 번호를 처리하거나 파티션 키를 자세히 관리하려는 경우 SDK를 사용하는 것이 적절한 방식일 수 있습니다.

이 섹션은 SQL SDK를 사용하여 변경 피드를 사용하는 방법을 안내합니다.

1. 가장 먼저 appconfig의 다음 리소스를 읽습니다. 끝점 및 인증 키 검색에 대한 소개는 [연결 문자열 업데이트](create-sql-api-dotnet.md#update-your-connection-string)에서 확인할 수 있습니다.

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. 다음과 같은 방법으로 클라이언트를 계정을 만듭니다.

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. 파티션 키 범위를 가져옵니다.

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. 모든 파티션 키 범위에 대해 ExecuteNextAsync를 호출합니다.

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> `ChangeFeedOptions.PartitionKeyRangeId` 대신, `ChangeFeedOptions.PartitionKey`를 사용하여 변경 피드를 가져올 단일 파티션 키를 지정합니다. 예: `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`
> 
>

복수의 reader가 있는 경우 **ChangeFeedOptions**를 사용하여 읽기 로드를 다른 스레드 또는 다른 클라이언트로 분산할 수 있습니다.

다음과 같은 몇 개의 코드만 있으면 바로 변경 피드를 읽을 수 있습니다. 이 문서에 사용된 전체 코드는 [GitHub 리포지토리](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)에서 확인할 수 있습니다.

위 4단계의 코드에서 마지막 줄에 있는 **ResponseContinuation**에는 문서의 마지막 LSN(논리적 시퀀스 번호)이 있으며, 다음에 이 시퀀스 번호 이후 새 문서를 읽을 때 이 LSN을 사용합니다. **ChangeFeedOption**의 **StartTime**을 사용하면 네트워크를 넓혀 문서를 가져올 수 있습니다. 따라서 **ResponseContinuation**이 null이지만 **StartTime**이 과거로 돌아갈 경우 **StartTime** 이후 변경된 모든 문서를 얻습니다. 단, **ResponseContinuation**에 값이 있는 경우에는 시스템에서 해당 LSN 이후의 모든 문서를 제공합니다.

따라서 검사점 배열은 각 파티션에 대해 LSN을 유지하는 것입니다. 파티션, 검사점, LSN, 시작 시간 등을 처리하지 않으려는 경우에는 변경 피드 프로세서 라이브러리를 사용하는 것이 더 간단한 방법입니다.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 사용 

[Azure Cosmos DB 변경 피드 프로세서 라이브러리](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed)는 이벤트 처리를 여러 소비자 사이에 손쉽게 배포하는 데 유용합니다. 이 라이브러리는 파티션 및 병렬 작동하는 여러 스레드 사이에서 변경 사항 읽기를 간소화합니다.

변경 피드 프로세서 라이브러리의 주요 이점은 각 파티션 및 구성 토큰을 관리하지 않아도 되며 각 컬렉션을 수동으로 폴링하지 않아도 된다는 점입니다.

이 변경 피드 프로세서 라이브러리는 파티션 및 병렬 작동하는 여러 스레드 사이에서 변경 사항 읽기를 간소화합니다.  이 라이브러리는 임대 메커니즘을 사용하여 파티션 사이에서 변경 사항 읽기를 자동으로 관리합니다. 다음 그림과 같이 변경 피드 프로세서 라이브러리를 사용하는 두 클라이언트를 시작하는 경우 두 클라이언트가 둘 사이에서 작업을 분할합니다. 계속해서 클라이언트 수를 늘리면 계속해서 클라이언트 사이에서 작업을 분할합니다.

![Azure Cosmos DB 변경 피드의 분산 처리](./media/change-feed/change-feed-output.png)

왼쪽 클라이언트가 가장 먼저 시작되었고 모든 파티션의 모니터링을 시작한 다음 두 번째 클라이언트가 시작되었고 첫 번째 클라이언트는 두 번째 클라이언트에 일부 임대를 해제합니다. 그림에서 볼 수 있듯이, 다른 컴퓨터와 클라이언트 사이에 작업을 배포하는 적절한 방법입니다.

서버를 사용하지 않는 Azure 함수로 동일한 컬렉션을 모니터링하고 동일한 임대를 사용하는 경우 두 함수는 프로세스 라이브러리가 파티션 처리 방식을 어떻게 결정하는가에 따라 다른 문서를 얻을 수 있습니다.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 이해

변경 피드 프로세서를 구현하는 4개의 주요 구성 요소는 '모니터링되는 컬렉션, 임대 컬렉션, 프로세서 호스트, 소비자'입니다. 

> [!WARNING]
> 응용 프로그램이 Azure Cosmos DB와 통신하기 위해 처리량을 예약할 때 컬렉션을 만드는 것은 가격에 영향을 미칩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
> 
> 

**모니터링되는 컬렉션:** 모니터링되는 컬렉션은 변경 피드가 생성되는 데이터입니다. 모니터링되는 컬렉션에 대한 모든 삽입 및 변경 내용은 컬렉션의 변경 피드에 반영됩니다. 

**임대 컬렉션:** 임대 컬렉션은 여러 작업자 사이에서 변경 피드의 처리를 조정합니다. 임대는 개별 컬렉션을 사용하여 저장됩니다(각 파티션마다 하나의 임대). 이 임대 컬렉션은 변경 피드 프로세서를 실행하는 곳과 가장 근접한 쓰기 영역에서 다른 계정으로 저장하는 것이 유용합니다. 임대 개체에는 다음 특성이 포함됩니다. 
* Owner: 임대를 소유하는 호스트를 지정합니다.
* Continuation: 특정 파티션에 대한 변경 피드에서의 위치(연속 토큰)를 지정합니다.
* Timestamp: 마지막으로 임대가 업데이트된 시간입니다. 타임스탬프를 사용하여 임대가 만료된 것으로 간주되는지 여부를 확인할 수 있습니다. 

**프로세서 호스트:** 각 호스트는 활성 임대가 포함된 호스트의 다른 인스턴스 수에 따라, 처리할 파티션 수를 결정합니다. 
1.  호스트가 시작되면 임대를 획득하여 모든 호스트에서 워크로드를 분산합니다. 호스트는 임대가 활성 상태로 유지되도록 주기적으로 임대를 갱신합니다. 
2.  호스트는 읽기를 수행할 때마다 해당 임대에 대한 가장 최근 연속 토큰의 검사점을 설정합니다. 동시성 안전을 보장하기 위해 호스트는 각 임대 업데이트에 대한 ETag를 확인합니다. 다른 검사점 전략도 지원됩니다.  
3.  종료 시 호스트는 모든 임대를 해제하지만 나중에 저장된 검사점에서 읽기를 다시 시작할 수 있도록 연속 정보를 유지합니다. 

이 경우에 호스트 수는 파티션(임대) 수보다 클 수 없습니다.

**소비자:** 소비자나 작업자는 각 호스트에서 시작된 변경 피드 처리를 수행하는 스레드입니다. 각 프로세서 호스트에는 여러 소비자가 있을 수 있습니다. 각 소비자는 할당된 파티션에서 변경 피드를 읽고 호스트에게 변경 사항 및 만료된 임대를 알려줍니다.

변경 피드 프로세서의 이러한 4개의 요소가 상호 작용하는 방법을 이해하기 위해 다음 다이어그램의 예제를 살펴보겠습니다. 모니터링되는 컬렉션은 문서를 저장하고 파티션 키로 "city"를 사용합니다. 파란색 파티션에는 "A~E"의 "city" 필드가 있는 문서가 포함된 것을 확인할 수 있습니다. 두 호스트에는 동시에 4개의 파티션을 읽는 두 명의 소비자가 있습니다. 화살표는 소비자가 읽는 변경 피드의 특정 지점을 표시합니다. 첫 번째 파티션에서, 어두운 파란색은 변경 피드의 아직 읽지 않은 변경 내용을 나타내는 반면, 연한 파란색은 이미 읽은 변경 내용을 나타냅니다. 호스트는 임대 컬렉션을 사용하여 "continuation" 값을 저장하고, 이로써 각 소비자의 현재 읽기 위치를 추적할 수 있습니다. 

![Azure Cosmos DB 변경 피드 프로세서 호스트 사용](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 작업

변경 피드 프로세서 NuGet 패키지를 설치하기 전에 먼저 다음을 설치합니다. 

* Microsoft.Azure.DocumentDB, 버전 1.13.1 이상 
* Newtonsoft.Json, 버전 9.0.1 이상

그런 다음 [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)를 설치하고 참조로 포함합니다.

변경 피드 프로세서 라이브러리를 구현하려면 다음을 수행해야 합니다.

1. **IChangeFeedObserver**를 구현하는 **DocumentFeedObserver** 개체를 구현합니다.

2. **IChangeFeedObserverFactory**를 구현하는 **DocumentFeedObserverFactory**를 구현합니다.

3. **DocumentFeedObserverFacory**의 **CreateObserver** 메서드에서, 1단계에서 만든 **ChangeFeedObserver**를 인스턴스화하고 반환합니다.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. **DocumentObserverFactory**를 인스턴스화합니다.

5. **ChangeFeedEventHost**를 인스턴스화합니다.

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. 호스트에 **DocumentFeedObserverFactory**를 등록합니다.

4 ~ 6단계의 코드는 다음과 같습니다. 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

지금까지 위의 몇 단계를 마치면 **DocumentFeedObserver ProcessChangesAsync** 메서드로 문서가 공급되기 시작합니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB와 Azure Functions를 사용하는 방법에 대한 자세한 내용은 [Azure Cosmos DB: Azure Functions를 통한 서버를 사용하지 않는 데이터베이스 컴퓨팅](serverless-computing-database.md)을 참조하세요.

변경 피드 프로세서 라이브러리를 사용하는 방법을 보려면 다음 리소스를 사용하세요.

* [정보 페이지](sql-api-sdk-dotnet-changefeed.md) 
* [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [위 1 ~ 6단계를 보여주는 샘플 코드](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [GitHub의 추가 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

SDK를 통해 변경 피드를 사용하는 방법을 보려면 다음 리소스를 사용하세요.

* [SDK 정보 페이지](sql-api-sdk-dotnet.md)
