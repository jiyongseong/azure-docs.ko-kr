---
title: Azure Cosmos DB BulkExecutor 라이브러리 개요 | Microsoft Docs
description: Azure Cosmos DB BulkExecutor 라이브러리, 라이브러리 및 해당 아키텍처를 사용할 때의 이점에 대해 알아봅니다.
keywords: Java Bulk Executor
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: d395376ad6cf191f8f355f6308f27e525da2911f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Azure Cosmos DB BulkExecutor 라이브러리 개요
 
Azure Cosmos DB는 다음을 지원하기 위해 탄력적으로 스케일 아웃하도록 설계된 빠르고 유연하며 전 세계적으로 분산된 데이터베이스 서비스입니다. 

* 대규모 읽기 및 쓰기 처리량(초당 수백만 작업).  
* 예측 가능한 밀리초 대기 시간으로 많은 양(수백 테라바이트 이상)의 트랜잭션 및 작업 데이터를 저장합니다.  

BulkExecutor 라이브러리는 이 대규모 처리량 및 저장소를 활용하는 데 유용합니다. BulkExecutor 라이브러리를 사용하면 API 대량 가져오기 및 대량 업데이트를 통해 Azure Cosmos DB에서 대량 작업을 수행할 수 있습니다. 다음 섹션에서 BulkExecutor 라이브러리의 기능에 대한 자세한 내용을 확인할 수 있습니다. 

> [!NOTE] 
> 현재, BulkExcutor 라이브러리는 가져오기 및 업데이트 작업을 지원하며, 이 라이브러리는 Azure Cosmos DB SQL API 계정에서만 지원됩니다. 라이브러리에 대한 업데이트 정보는 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 및 [Java](sql-api-sdk-bulk-executor-java.md) 릴리스 정보를 참조하세요.
 
## <a name="key-features-of-the-bulkexecutor-library"></a>BulkExecutor 라이브러리의 주요 기능  
 
* 컨테이너에 할당된 처리량을 사용하는 데 필요한 클라이언트 쪽 계산 리소스를 크게 줄여 줍니다. 대량 가져오기 API를 사용하여 데이터를 작성하는 단일 스레드 응용 프로그램은 클라이언트 컴퓨터의 CPU를 사용하면서 동시에 데이터를 작성하는 멀티 스레드 응용 프로그램에 비해 10회 이상의 쓰기 처리량을 달성합니다.  

* 응용 프로그램 논리 작성의 지루한 작업을 추상화하여 요청 제한, 요청 시간 제한 및 다른 일시적 예외를 라이브러리 내에서 효과적으로 처리합니다.  

* 응용 프로그램이 스케일 아웃할 대량 작업을 수행할 수 있도록 간소화된 메커니즘을 제공합니다. Azure VM에서 실행되는 단일 BulkExecutor 인스턴스는 500K RU/s 이상을 사용할 수 있으며, 사용자는 개별 클라이언트 VM에서 추가 인스턴스를 추가하여 더 높은 처리량 속도를 얻을 수 있습니다.  
 
* 스케일 아웃 아키텍처를 사용하면 한 시간에 테라바이트 이상의 데이터를 대량으로 가져올 수 있습니다.  

* Azure Cosmos DB 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Bulk Executor의 작동 방식 

문서를 가져오기 또는 업데이트하는 대량 작업이 엔터티의 일괄 처리로 트리거되는 경우 처음에 Azure Cosmos DB 파티션 키 범위에 해당하는 버킷에 섞입니다. 파티션 키 범위에 해당하는 각 버킷 내에서 미니 일괄 처리로 나뉘고 각 미니 일괄 처리는 서버 쪽에서 커밋되는 페이로드로 작동합니다. BulkExecutor 라이브러리는 파티션 키 범위 내 및 전반 모두에서 이러한 미니 일괄 처리를 동시에 실행할 수 있도록 최적화되어 빌드되었습니다. 다음 그림에서는 BulkExecutory가 다른 파티션 키로 데이터를 일괄 처리하는 방법을 보여 줍니다.  

![Bulk Executor 아키텍처](./media/bulk-executor-overview/bulk-executor-architecture.png)

Bulk Executor 라이브러리는 컬렉션에 할당된 처리량을 최대로 활용합니다. 각 Azure Cosmos DB 파티션 키 범위에 대해  [AIMD 스타일 정체 제어 메커니즘](https://tools.ietf.org/html/rfc5681)을 사용하여 제한 및 제한 시간을 효율적으로 처리합니다. 

## <a name="next-steps"></a>다음 단계 
  
* [.NET](bulk-executor-dot-net.md) 및 [Java](bulk-executor-java.md)에서 Bulk Executor 라이브러리를 사용하는 샘플 응용 프로그램을 사용해 보며 자세히 알아봅니다.  
* [.NET](sql-api-sdk-bulk-executor-dot-net.md) 및 [Java](sql-api-sdk-bulk-executor-java.md)의 BulkExecutor SDK 정보 및 릴리스 정보를 확인하세요.
* Bulk Executor 라이브러리는 Cosmos DB Spark 커넥터에 통합됩니다. 자세한 내용은 [Azure Cosmos DB Spark 커넥터](spark-connector.md) 문서를 참조하세요.  
* 또한 BulkExecutor 라이브러리는 Azure Data Factory가 데이터를 복사할 수 있도록 [Azure Cosmos DB 커넥터](https://aka.ms/bulkexecutor-adf-v2)의 새 버전에 통합됩니다.
