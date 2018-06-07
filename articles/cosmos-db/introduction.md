---
title: Azure Cosmos DB 소개 | Microsoft Docs
description: Azure Cosmos DB에 대해 알아봅니다. 이 전세계에 배포된 멀티모델 데이터베이스는 낮은 대기 시간, 탄력적 확장성 및 고가용성을 위해 구축되었습니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 3c7fc83e379bcd1ad084d20555d9a82697cb5069
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB 시작

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 멀티모델 데이터베이스입니다. Azure Cosmos DB에서는 단추를 클릭하는 간단한 방식으로 Azure의 지리적 위치 수에 관계없이 처리량 및 저장소를 탄력적 및 독립적으로 확장할 수 있습니다. 다른 데이터베이스 서비스에서는 제공하지 않는 포괄적 [Service Level Agreement(서비스 수준 약정)](https://aka.ms/acdbsla)(SLA)를 통해 처리량, 대기 시간, 가용성 및 일관성을 보장합니다. Azure 구독, 요금 및 약정 없이 [무료로 Azure Cosmos DB를 사용해 볼 수 있습니다](https://azure.microsoft.com/try/cosmosdb/).

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB는 탄력적 규모 확장, 낮은 대기 시간 보증, 일관성 모델 5개, 포괄적 보장 SLA를 갖춘 글로벌하게 배포된 데이터베이스 서비스입니다.](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>주요 기능
전 세계적으로 분산된 다중 모델 데이터베이스 서비스인 Azure Cosmos DB는 전 세계적 규모의 확장성과 응답성이 뛰어난 응용 프로그램을 쉽게 만들 수 있습니다.

* **턴키 글로벌 배포**
    * [단추를 클릭](tutorial-global-distribution-sql-api.md)하여 원하는 수의 [Azure 지역](https://azure.microsoft.com/regions/)에 [데이터를 배포](distribute-data-globally.md)할 수 있습니다. 이렇게 하면 사용자가 있는 위치에 데이터를 배치하여 고객에게 가장 짧을 대기 시간을 보장할 수 있습니다. 
    * 앱에서는 Azure Cosmos DB의 멀티 호밍 API를 사용하여 가장 가까운 지역을 알고 가장 가까운 데이터 센터에 요청을 보냅니다. 이 모든 작업은 구성을 변경하지 않고 수행할 수 있습니다. 쓰기 지역 및 읽기 지역을 원하는 만큼 설정하면 나머지도 처리됩니다.
    * Azure Cosmos DB 데이터베이스에 지역을 추가하거나 제거하는 경우 멀티 호밍(multi-homing) API 기능에 따라 응용 프로그램을 다시 배포할 필요가 없으며 높은 가용성을 계속 유지할 수 있습니다.

* **데이터 액세스 및 쿼리를 위한 다중 데이터 모델 및 주요 API**
    * Azure Cosmos DB가 빌드된 ARS(atom-record-sequence) 기반 데이터 모델은 기본적으로 문서, 그래프, 키-값, 테이블 및 열 계열 데이터 모델을 포함하지만 이를 제한하지 않는 여러 데이터 모델을 지원합니다.
    * 여러 언어에서 사용할 수 있는 SDK로 다음 데이터 모델의 API가 지원됩니다.
        * [SQL API](sql-api-introduction.md): 풍부한 SQL 쿼리 기능이 있지만 스키마가 없는 JSON 데이터베이스 엔진입니다.
        * [MongoDB API](mongodb-introduction.md): Azure Cosmos DB 플랫폼에서 구동하는 대규모로 확장 가능한 *MongoDB-as-a-Service*입니다. 기존 MongoDB 라이브러리, 드라이버, 도구 및 응용 프로그램과 호환됩니다.
        * [Cassandra API](cassandra-introduction.md): Azure Cosmos DB 플랫폼에서 구동하는 전 세계적으로 분산된 Cassandra-as-a-Service입니다. 기존 [Apache Cassandra](https://cassandra.apache.org/) 라이브러리, 드라이버, 도구 및 응용 프로그램과 호환됩니다.
        * [Gremlin API](graph-introduction.md): 개방형 Graph API([Apache TinkerPop 사양](http://tinkerpop.apache.org/), Apache Gremlin을 기반으로 함)를 지원하는 고도로 연결된 데이터 집합을 사용하는 응용 프로그램을 쉽게 빌드하고 실행할 수 있게 해주는 완전히 관리되고 수평적으로 확장 가능한 그래프 데이터베이스 서비스입니다.
        * [Table API](table-introduction.md): 앱을 변경하지 않고도 기존 Azure Table 저장소 응용 프로그램에 프리미엄 기능(예: 자동 인덱싱, 짧은 대기 시간 보장, 전역 배포)을 제공하도록 만들어진 키-값 데이터베이스 서비스입니다.
        * 추가 데이터 모델 및 API 제공 예정

* **주문형 및 전 세계적 규모의 처리량 및 저장소에 대한 탄력적이고 독립적인 크기 조정**
    * 데이터베이스 처리량을 [초](request-units.md) 단위로 간편하게 확장하고 원하면 언제든 변경할 수 있습니다. 
    * 크기 요구 사항에 맞게 지속적으로 저장소 크기를 [투명하게 자동으로](partition-data.md) 확장합니다.

* **응답성 높은 중요 업무용 응용 프로그램 빌드**
    * Azure Cosmos DB는 고객에게 백분위 99의 종단 간 대기 시간을 보장합니다. 
    * 일반적인 1KB 항목의 경우 Cosmos DB는 동일한 Azure 지역 내에서 99번째 백분위수로 10ms 미만의 종단 간 읽기 대기 시간, 15ms 미만의 인덱싱된 쓰기를 보장합니다. 중간 대기 시간이 크게 낮아집니다(5ms 미만).

* **"Always On" 가용성 보장**
    * 모든 단일 지역 데이터베이스 계정에 대해 99.99% 가용성 SLA 및 모든 다중 지역 데이터베이스 계정에 대해 99.999% 읽기 가용성을 보장합니다.
    * 더 높은 가용성과 더 나은 성능을 위해 원하는 수의 [Azure 지역](https://azure.microsoft.com/regions)에 배포합니다.
    * 지역에 대한 우선 순위를 동적으로 설정하고 데이터 손실 보장이 0인 하나 이상의 지역에서 [오류를 시뮬레이션](regional-failover.md)하여 전체 앱(데이터베이스 외)에 대한 종단 간 가용성을 테스트합니다. 

* **올바른 방법으로 전세계에 배포되는 응용 프로그램 작성**
    * 잘 정의되고 실용적이며 직관적인 5개 [일관성 모델](consistency-levels.md)은 중간 형태의 일관성을 포함하여 완화된 NoSQL 형태의 최종 일관성에서 강력한 SQL 형태의 일관성까지 모두 제공합니다. 
  
* **환불 보장**
    * 재정적으로 지원되는 업계 최고의 포괄적인 [서비스 수준 계약](https://aka.ms/acdbsla)(SLA)으로 중요한 데이터에 대한 가용성, 대기 시간, 처리량 및 일관성을 보장합니다. 

* **데이터베이스 스키마/인덱스 관리 없음**
    * 데이터베이스 스키마 및/또는 인덱스 관리에 대한 걱정 없이 응용 프로그램의 스키마를 신속하게 반복합니다.
    * Azure Cosmos DB의 데이터베이스 엔진은 스키마에서 완전히 자유로우며 스키마나 인덱스 없이 자신이 수집한 모든 데이터를 자동으로 인덱싱하며 매우 빠르게 쿼리를 제공합니다. 

* **낮은 소유 비용**
    * 관리되지 않는 솔루션 또는 온-프레미스 NoSQL 솔루션보다 5-10배 정도 [비용 효율적입니다](https://aka.ms/cosmos-db-tco-paper).
    * AWS DynamoDB 또는 Google Spanner보다 3배 저렴합니다.

## <a name="capability-comparison"></a>기능 비교

Azure Cosmos DB는 기존의 관계형 및 비관계형 데이터베이스의 최고 기능을 제공합니다.

| 기능 | 관계형 데이터베이스   | 비관계형(NoSQL) 데이터베이스 |    Azure Cosmos DB |
| --- | --- | --- | --- |
| 글로벌 분포 | 아니오 | 아니오 | 예, 멀티 호밍 API를 사용하여 30개 이상의 지역에서 턴키 배포|
| 수평적 확장 | 아니오 | 예 | 예, 독립적으로 저장소 및 처리량을 확장할 수 있습니다. | 
| 대기 시간 보장 | 아니오 | 예 | 예, <10ms인 읽기의 99% 및 <15 ms인 쓰기 | 
| 고가용성 | 아니오 | 예 | 예, Azure Cosmos DB는 항상 켜져 있고, PACELC 장단점이 잘 정의되어 있으며, 자동 및 수동 장애 조치 옵션을 제공합니다.|
| 데이터 모델 + API | 관계형 + SQL | 다중 모델 + OSS API | 다중 모델 + SQL + OSS API(추가 서비스 예정) |
| SLA | 예 | 아니오 | 예, 대기 시간, 처리량, 일관성, 가용성에 대한 포괄적 SLA |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Azure Cosmos DB를 활용하는 솔루션

다양한 데이터에 대한 거의 실제적인 응답 시간을 통해 [전역적으로](distribute-data-globally.md) 대량의 데이터, 읽기 및 쓰기를 처리해야 하는 모든 [웹, 모바일, 게임 및 IoT 응용 프로그램](use-cases.md)은 Azure Cosmos DB에서 [보장](https://azure.microsoft.com/support/legal/sla/cosmos-db/)하는 고가용성, 높은 처리량, 짧은 대기 시간 및 조정 가능한 일관성의 이점을 누릴 수 있습니다. Azure Cosmos DB를 [IoT 및 전자 통신 정보](use-cases.md#iot-and-telematics), [소매 및 마케팅](use-cases.md#retail-and-marketing), [게임](use-cases.md#gaming) 및 [웹 및 모바일 응용 프로그램](use-cases.md#web-and-mobile-applications)에 적용하는 방법에 대해 알아보세요.

## <a name="next-steps"></a>다음 단계
다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

* [Azure Cosmos DB SQL API 시작](create-sql-api-dotnet.md)
* [Azure Cosmos DB MongoDB API 시작](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 시작](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB Table API 시작](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)
