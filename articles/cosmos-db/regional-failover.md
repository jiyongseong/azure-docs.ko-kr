---
title: Azure Cosmos DB의 지역별 장애 조치(failover) | Microsoft Docs
description: Azure Cosmos DB에서 수동 및 자동 장애 조치(failover)가 작동하는 방식에 대해 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977027de0627a6eca5f9eb5d1ab83dea5347c6d4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>비즈니스 연속성을 위한 Azure Cosmos DB의 자동 지역별 장애 조치(failover)
Azure Cosmos DB는 일관성, 가용성, 성능을 적절히 보증하면서 서로 간에 명확히 절충하는 완전 관리형 [다중 지역 데이터베이스 계정](distribute-data-globally.md)을 제공하여 글로벌 데이터 배포를 단순화합니다. Cosmos DB 계정은 고가용성, 짧은 대기 시간(한 자릿수 ms), [잘 정의된 일관성 수준](consistency-levels.md), multi-homing API를 사용한 투명한 지역별 장애 조치(failover) 및 전 세계적으로 처리량과 저장소를 탄력적으로 확장할 수 있는 기능을 제공합니다. 

Cosmos DB는 명시적 장애 조치(failover)와 정책 기반 장애 조치를 둘 다 지원하므로 장애 발생 시 종단 간 시스템 동작을 제어할 수 있습니다. 이 문서에서 다음을 살펴봅니다.

* 수동 장애 조치(failover)가 Cosmos DB에서 작동하는 방식
* Cosmos DB에서 자동 장애 조치(failover)가 작동하는 방식 및 데이터 센터가 다운될 때 발생하는 결과
* 응용 프로그램 아키텍처에서 수동 장애 조치를 사용하는 방법

지역 장애 조치를 비롯한 전역 배포 기능을 보여주는 Azure Cosmos DB 프로그램 관리자인 Andrew Liu에 의해 작성된 이 비디오에서 지역 장애 조치에 대해 알아볼 수 있습니다.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>다중 지역 응용 프로그램 구성
장애 조치 모드로 들어가기 전에 지역별 장애 조치에 직면하여 다중 지역 가용성을 활용하고 탄력적으로 대처할 수 있도록 응용 프로그램을 구성하는 방법을 살펴봅니다.

* 먼저 여러 지역에 응용 프로그램을 배포합니다.
* 응용 프로그램이 배포된 모든 지역에서 짧은 대기 시간 액세스를 보장하려면 지원되는 SDK 중 하나를 통해 각 지역에 해당하는 [기본 지역 목록](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations)을 구성합니다.

다음 코드 조각에서는 다중 지역 응용 프로그램을 초기화하는 방법을 보여 줍니다. 여기서는 Azure Cosmos DB 계정 `contoso.documents.azure.com`이 미국 서부와 북유럽의 두 지역으로 구성됩니다. 

* 응용 프로그램은 미국 서부 지역에 배포됩니다(예: Azure App Services 사용). 
* `West US`가 짧은 대기 시간 읽기를 위해 첫 번째 기본 지역으로 구성됩니다.
* `North Europe`이 지역 장애 시의 고가용성을 위해 두 번째 기본 지역으로 구성됩니다.

SQL API에서 이 구성은 다음 코드 조각과 같습니다.

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "<Fill your Cosmos DB account's AuthorizationKey>",
    usConnectionPolicy);
```

또한 응용 프로그램이 기본 지역의 순서를 바꿔 북유럽 지역에도 배포됩니다. 즉 북유럽 지역이 짧은 대기 시간 읽기에 대해 첫 번째로 지정됩니다. 그런 다음 미국 서부 지역이 지역 장애 시 고가용성을 위해 두 번째 기본 지역으로 지정됩니다.

다음 아키텍처 다이어그램에서는 Cosmos DB 및 응용 프로그램이 4개의 Azure 지리적 하위 지역에서 사용할 수 있도록 구성된 다중 지역 응용 프로그램 배포를 보여 줍니다.  

![Azure Cosmos DB를 통해 전역으로 분산된 응용 프로그램 배포](./media/regional-failover/app-deployment.png)

이제 Cosmos DB 서비스에서 자동 장애 조치(failover)를 통해 지역 장애를 처리하는 방법을 살펴보겠습니다. 

## <a id="AutomaticFailovers"></a>자동 장애 조치
드물게 발생하는 Azure 지역 가동 중단 또는 데이터 센터 가동 중단의 경우 Cosmos DB는 영향을 받은 지역에 있는 모든 Cosmos DB 계정의 장애 조치를 자동으로 트리거합니다. 

**읽기 지역에 가동 중단이 발생하면 어떻게 됩니까?**

영향을 받은 지역 중 하나에 읽기 지역이 있는 Cosmos DB 계정은 자동으로 쓰기 지역과의 연결이 끊어지고 오프라인으로 표시됩니다. Cosmos DB SDK에서 지역 검색 프로토콜을 구현하여 지역을 사용할 수 있는 시기를 자동으로 감지하고 기본 지역 목록에서 사용 가능한 다음 지역으로 읽기 호출을 리디렉션합니다. 기본 지역 목록의 어느 지역도 사용할 수 없는 경우 호출은 현재 쓰기 지역으로 자동으로 대체됩니다. 지역별 장애 조치를 처리하기 위해 응용 프로그램 코드를 변경할 필요가 없습니다. 이 프로세스 전체에서 Cosmos DB를 통해 일관성이 계속 보장되기 때문입니다.

![Azure Cosmos DB의 읽기 지역 장애](./media/regional-failover/read-region-failures.png)

영향을 받은 지역이 가동 중단에서 복구되면 해당 지역에서 영향을 받은 모든 Cosmos DB 계정이 서비스에서 자동으로 복구됩니다. 영향을 받은 지역에 읽기 지역이 있는 Cosmos DB 계정은 자동으로 현재 쓰기 지역과 동기화되고 온라인 상태가 됩니다. Cosmos DB SDK에서 새로운 지역의 가용성을 검색하고 응용 프로그램이 구성한 기본 지역 목록을 기반으로 하여 해당 지역을 현재 읽기 지역으로 선택해야 하는지 평가합니다. 후속 읽기는 응용 프로그램 코드를 변경하지 않고도 복구된 지역으로 리디렉션됩니다.

**쓰기 지역에 가동 중단이 발생하면 어떻게 됩니까?**

영향을 받은 지역이 현재 쓰기 지역이고 Azure Cosmos DB 계정에 대해 자동 장애 조치(Failover)가 사용되도록 설정되면 해당 지역은 자동으로 오프라인으로 표시됩니다. 그런 다음 대체 지역이 영향을 받은 Azure Cosmos DB 계정의 쓰기 지역으로 승격됩니다. Azure Portal을 통해 또는 [프로그래밍 방식](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange)으로 Azure Cosmos DB 계정에 대해 자동 장애 조치(Failover)를 사용하도록 설정하고 지역 선택 순서를 완전히 제어할 수 있습니다. 

![Azure Cosmos DB의 장애 조치(failover) 우선 순위](./media/regional-failover/failover-priorities.png)

자동 장애 조치(Failover) 중에는 Azure Cosmos DB가 지정된 우선 순위에 따라 지정된 Azure Cosmos DB 계정의 다음 쓰기 지역을 자동으로 선택합니다. 응용 프로그램은 DocumentClient 클래스의 WriteEndpoint 속성을 사용하여 쓰기 지역의 변경 내용을 검색할 수 있습니다.

![Azure Cosmos DB의 쓰기 지역 장애](./media/regional-failover/write-region-failures.png)

영향을 받은 지역이 가동 중단에서 복구되면 해당 지역에서 영향을 받은 모든 Cosmos DB 계정이 서비스에서 자동으로 복구됩니다. 

* 작동 중단 동안 읽기 지역에 복제되지 않은 이전 쓰기 지역의 데이터는 충돌 피드로 게시됩니다. 응용 프로그램은 충돌 피드를 읽고, 응용 프로그램별 논리에 따라 충돌을 해결하고, 업데이트된 데이터를 Azure Cosmos DB 계정에 적절히 다시 쓸 수 있습니다. 
* 이전 쓰기 지역이 읽기 지역으로 다시 만들어진 후 자동으로 다시 온라인 상태로 전환됩니다. 
* Azure Portal을 통해 또는 [프로그래밍 방식](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)으로 수동 장애 조치(Failover)를 수행하여 자동으로 온라인으로 다시 전환된 읽기 지역을 쓰기 지역으로 다시 구성할 수 있습니다.

다음 코드 조각에서는 영향을 받는 지역이 가동 중단에서 복구된 후 충돌을 처리하는 방법을 보여 줍니다.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>수동 장애 조치

자동 장애(failover) 조치 외에도 지정된 Cosmos DB 계정의 현재 쓰기 지역을 기존 읽기 지역 중 하나로 직접 동적으로 변경할 수 있습니다. 수동 장애 조치는 Azure Portal을 통하거나 [프로그래밍 방식](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)으로 시작할 수 있습니다. 

수동 장애 조치(failover)는 **데이터 무손실** 및 **가용성 무손실**을 보장하고 이전 쓰기 지역의 쓰기 상태를 지정된 Cosmos DB 계정의 새 쓰기 지역으로 정상적으로 전송합니다. 자동 장애 조치(failover)와 마찬가지로 Cosmos DB SDK는 수동 장애 조치 중에 쓰기 지역 변경을 자동으로 처리하고 호출이 자동으로 새 쓰기 지역으로 리디렉션되도록 합니다. 장애 조치를 관리하기 위해 응용 프로그램 코드 또는 구성을 변경할 필요가 없습니다. 

![Azure Cosmos DB에서의 수동 장애 조치(failover)](./media/regional-failover/manual-failovers.png)

수동 장애 조치가 유용할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.

**시계 모델 준수** - 응용 프로그램에 하루 중 시간을 기준으로 예측 가능한 트래픽 패턴이 있는 경우 이 기준에 따라 쓰기 상태를 가장 활동적인 지역으로 주기적으로 변경할 수 있습니다.

**서비스 업데이트** - 전 세계에 분산된 응용 프로그램 배포에는 예정된 서비스 업데이트 중에 트래픽 관리자를 통해 다른 지역으로 트래픽을 다시 라우팅하는 작업이 포함될 수 있습니다. 이러한 응용 프로그램 배포는 이제 수동 장애 조치를 사용하여 서비스 업데이트 기간 동안 활성 트래픽이 될 지역에 대한 쓰기 상태를 유지할 수 있습니다.

**BCDR(비즈니스 연속성 및 재해 복구) 및 HADR(고가용성 및 재해 복구) 절차** - 대부분의 엔터프라이즈 응용 프로그램에는 개발 및 릴리스 프로세스의 일부로 비즈니스 연속성 테스트가 포함됩니다. BCDR 및 HADR 테스트는 종종 지역 가동 중단 위반의 경우 규정 준수 인증 및 서비스 가용성 보장의 중요한 단계입니다. Cosmos DB 계정의 수동 장애 조치(failover)를 트리거하거나 지역을 동적으로 추가 및 제거하여 저장소에 Cosmos DB를 사용하는 응용 프로그램의 BCDR 준비 상태를 테스트할 수 있습니다.

이 문서에서는 Cosmos DB에서 수동 및 자동 장애 조치(failover)가 작동하는 방식 및 전역으로 사용할 수 있도록 Cosmos DB 계정과 응용 프로그램을 구성하는 방법을 살펴보았습니다. Cosmos DB의 글로벌 복제 지원을 사용하면 종단 간 대기 시간을 향상시키고 지역 장애 발생 시에도 가용성을 높일 수 있습니다. 

## <a id="NextSteps"></a>다음 단계
* Cosmos DB에서 [글로벌 배포](distribute-data-globally.md)를 지원하는 방법에 대한 자세한 정보
* [Azure Cosmos DB를 통한 전역 일관성](consistency-levels.md)에 대한 자세한 정보
* Azure Cosmos DB의 [SQL API](tutorial-global-distribution-sql-api.md)를 사용하여 여러 지역으로 개발
* Azure Cosmos DB로 [다중 지역 기록기 아키텍처](multi-region-writers.md)를 작성하는 방법에 대해 알아봅니다.

