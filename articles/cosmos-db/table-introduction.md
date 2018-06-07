---
title: Azure Cosmos DB 테이블 API 소개 | Microsoft Docs
description: 인기 있는 OSS MongoDB API를 사용하여 대기 시간이 짧은 엄청난 양의 키-값 데이터를 저장하고 쿼리하는 데 Azure Cosmos DB를 사용하는 방법을 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/20/2017
ms.author: sngun
ms.openlocfilehash: 791529dc054c8007647ba64c4bceb3e3fdb1a194
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB: 테이블 API 소개

[Azure Cosmos DB](introduction.md)는 Azure Table Storage용으로 작성되었으며 다음과 같은 프리미엄 기능이 필요한 응용 프로그램에 대한 테이블 API를 제공합니다.

* [턴키 전역 배포](distribute-data-globally.md)
* 전 세계적인 [전용 처리량](partition-data.md)
* 99번째 백분위수에서 1자리 밀리초 대기 시간
* 보장된 고가용성
* [자동 보조 인덱싱(영문)](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)

Azure Table Storage에 대해 작성된 응용 프로그램은 코드를 변경하지 않고 테이블 API를 사용하여 Azure Cosmos DB로 마이그레이션할 수 있으며 프리미엄 기능을 활용할 수 있습니다. 테이블 API에는 .NET, Java, Python 및 Node.js에 사용할 수 있는 클라이언트 SDK가 있습니다.

Aravind Krishna가 Azure Cosmos DB 테이블 API를 시작하는 방법을 설명하는 다음 비디오를 시청하는 것이 좋습니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="table-offerings"></a>Table 제품
현재 Azure Table Storage를 사용하는 경우 Azure Cosmos DB 테이블 API로 이동하면 다음과 같은 이점이 있습니다.

| | Azure 테이블 저장소 | Azure Cosmos DB 테이블 API |
| --- | --- | --- |
| 대기 시간 | 빠르지만 대기 시간에 대한 상한이 없습니다. | 읽기/쓰기에 대한 1자리 밀리초 대기 시간이 지원됩니다(전 세계의 모든 규모에 대해 99번째 백분위수에서 10ms 미만의 읽기 및 15ms 미만의 쓰기 대기 시간 지원). |
| 처리량 | 가변 처리량 모델입니다. 테이블의 확장 제한은 20,000개 작업/초입니다. | SLA를 통해 지원하는 [테이블당 예약된 전용 처리량](request-units.md)으로 확장성이 뛰어납니다. 계정에는 처리량에 대한 상한이 없으며, 테이블당 1,000만 개 이상 작업/초를 지원합니다. |
| 글로벌 분포 | 고가용성을 위해 선택적인 읽기 가능한 보조 읽기 하위 지역 하나가 있는 단일 지역입니다. 장애 조치를 시작할 수 없습니다. | 하나에서 30개가 넘는 지역까지 [턴키 전역 배포](distribute-data-globally.md)를 수행할 수 있습니다. 전 세계 어디에서나 [자동 및 수동 장애 조치](regional-failover.md)를 지원합니다. |
| 인덱싱 | PartitionKey 및 RowKey에 대한 기본 인덱스만 제공. 보조 인덱스가 없습니다. | 모든 속성에 대해 자동 및 전체 인덱싱을 수행할 수 있으며, 인덱스 관리가 없습니다. |
| 쿼리 | 쿼리 실행 시 기본 키에 대한 인덱스를 사용하고 그렇지 않은 경우 검색합니다. | 쿼리는 빠른 쿼리 시간을 위해 속성에 대해 자동 인덱싱을 활용할 수 있습니다. |
| 일관성 | 주 지역 내에서 강력하게 유지되며, 최종적으로는 보조 지역 내에서 유지됩니다. | 응용 프로그램 요구 사항에 따라 가용성, 대기 시간, 처리량 및 일관성을 절충할 수 있는 [잘 정의된 5가지 일관성 수준](consistency-levels.md)이 적용됩니다. |
| 가격 | 저장소 최적화 | 처리량 최적화 |
| SLA | 99.99% 가용성입니다. | 모든 단일 지역 계정 및 모든 다중 지역 계정에는 99.99% 가용성 SLA와 완화된 일관성이 제공되고, 일반 공급 시에는 모든 다중 지역 데이터베이스 계정에 [업계 최고 수준의 포괄적인 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)와 99.999% 읽기 가용성이 제공됩니다. |

## <a name="get-started"></a>시작하기

[Azure Portal](https://portal.azure.com)에서 새 Azure Cosmos DB 계정을 만듭니다. 그런 다음 [.NET을 사용하여 테이블 API에 대한 빠른 시작](create-table-dotnet.md)을 시작합니다. 

> [!IMPORTANT]
> 미리 보기 도중에 Table API 계정을 만든 경우 일반 공급되는 Table API SDK와 작동하는 [새 Table API 계정](create-table-dotnet.md#create-a-database-account)을 만드세요.
>

## <a name="next-steps"></a>다음 단계

다음은 시작하기 위한 몇 가지 지침입니다.
* [테이블 API를 사용하여 .NET 응용 프로그램 빌드](create-table-dotnet.md)
* [.NET의 테이블 API(미리 보기)를 사용하여 개발](tutorial-develop-table-dotnet.md)
* [테이블 API를 사용하여 테이블 데이터 쿼리](tutorial-query-table.md)
* [테이블 API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법](tutorial-global-distribution-table.md)
* [Azure Cosmos DB 테이블 .NET API](table-sdk-dotnet.md)
* [Azure Cosmos DB 테이블 Java API](table-sdk-java.md)
* [Azure Cosmos DB 테이블 Node.js API](table-sdk-nodejs.md)
* [Python용 Azure Cosmos DB 테이블 SDK](table-sdk-python.md)

