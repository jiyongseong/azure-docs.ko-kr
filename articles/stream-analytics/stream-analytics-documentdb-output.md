---
title: Cosmos DB에 Azure Stream Analytics 출력
description: 이 문서에서는 비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 위해 Azure Stream Analytics를 사용하여 JSON 출력에 대한 Azure Cosmos DB에 출력을 저장하는 방법을 알아봅니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: f7115f7d19cd44ae7d0812d3aa6c48d8dd58c20d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB에 Azure Stream Analytics 출력  
비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 사용하기 위해 Stream Analytics에서 JSON 출력의 대상을 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)로 지정할 수 있습니다. 이 문서에서는 이 구성을 구현하기 위한 몇 가지 모범 사례를 설명합니다.

Cosmos DB에 대해 잘 모를 경우 먼저 [Azure Cosmos DB의 학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)를 살펴보세요. 

> [!Note]
> 현재 Azure Stream Analytics는 **SQL API**를 사용한 CosmosDB 연결만을 지원합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. Azure Stream Analytics를 다른 API로 만든 Azure Cosmos DB 계정에 지정한 경우 데이터는 올바르게 저장되지 않을 수도 있습니다. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>출력 대상으로서 Cosmos DB의 기본 사항
Stream Analytics의 Azure Cosmos DB 출력을 사용하면 스트림 처리 결과를 Cosmos DB 컬렉션에 JSON 출력으로 쓸 수 있습니다. Stream Analytics에서는 데이터베이스에 컬렉션을 만들지 않습니다. 대신 사용자가 사전에 만들어야 합니다. 이는 Cosmos DB 컬렉션에 대한 청구 비용을 사용자에게 투명하게 하고, 사용자가 [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 컬렉션의 성능, 일관성 및 용량을 직접 조정할 수 있도록 하기 위한 것입니다. 스트리밍 작업당 하나의 Cosmos DB 데이터베이스를 사용하여 스트리밍 작업을 위한 컬렉션을 논리적으로 분리하는 것이 좋습니다.

아래에서는 Cosmos DB 컬렉션 옵션 중 일부를 자세히 설명합니다.

## <a name="tune-consistency-availability-and-latency"></a>일관성, 가용성 및 대기 시간 조정
Cosmos DB를 사용하면 응용 프로그램 요구 사항에 맞게 데이터베이스 및 컬렉션을 미세 조정하고, 일관성, 가용성 및 대기 시간 간의 균형을 유지할 수 있습니다. 시나리오에서 읽기 및 쓰기 대기 시간에 대해 필요로 하는 읽기 일관성 수준에 따라 데이터베이스 계정에 대한 일관성 수준을 선택할 수 있습니다. 또한 기본적으로 Cosmos DB는 컬렉션에 대한 각 CRUD 작업에서 동기 인덱싱을 지원합니다. 이는 Cosmos DB에서 쓰기/읽기 성능을 제어하는 또 다른 유용한 옵션입니다. 자세한 내용은 [데이터베이스 및 쿼리 일관성 수준 변경](../cosmos-db/consistency-levels.md) 문서를 검토하세요.

## <a name="upserts-from-stream-analytics"></a>Stream Analytics에서 Upsert
Stream Analytics를 Cosmos DB와 통합하면 지정된 문서 ID 열에 따라 Cosmos DB 컬렉션에 레코드를 삽입하거나 업데이트할 수 있습니다. 이를 *Upsert*라고도 합니다.

Stream Analytics에서는 문서 ID 충돌로 인해 삽입에 실패한 경우에만 업데이트가 수행되는 낙관적 Upsert 방식을 활용합니다. 이 업데이트는 Stream Analytics에서 패치로 수행되므로 문서의 부분 업데이트가 가능합니다. 즉, 새 속성 추가 또는 기존 속성 바꾸기가 증분식으로 수행됩니다. JSON 문서에서 배열 속성 값을 변경하면 전체 배열을 덮어씁니다. 즉, 배열이 병합되지 않습니다.

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB의 데이터 분할
데이터 분할에 권장되는 방법은 Cosmos DB [분할 컬렉션](../cosmos-db/partition-data.md)입니다. 

Stream Analytics를 사용하면 단일 Cosmos DB 컬렉션의 경우에도 응용 프로그램의 쿼리 패턴 및 성능 요구를 기준으로 데이터를 분할할 수 있습니다. 각 컬렉션은 최대 10GB의 데이터(최대값)를 포함할 수 있으며, 현재 컬렉션을 강화(또는 오버플로)할 방법은 없습니다. 규모 확장의 경우 Stream Analytics에서는 주어진 접두사를 사용하여 여러 컬렉션에 쓸 수 있도록 허용합니다(아래 사용법 정보 참조). Stream Analytics에서는 사용자가 제공한 PartitionKey 열에 따라 [해시 파티션 확인자](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 전략을 사용하여 해당 출력 레코드를 분할합니다. 스트리밍 작업 시작 시 지정된 접두사를 가진 컬렉션 수가 작업에서 병렬로 기록할 출력 파티션 수로 사용됩니다(Cosmos DB 컬렉션 = 출력 파티션). 삽입만 수행하는 지연 인덱싱을 사용하는 단일 컬렉션의 경우 약 0.4MB/초의 쓰기 처리량을 예상할 수 있습니다. 여러 컬렉션을 사용하여 처리량 및 용량을 늘릴 수 있습니다.

나중에 파티션 수를 늘리려는 경우 작업을 중지하고 기존 컬렉션의 데이터를 새 컬렉션으로 다시 분할한 다음 Stream Analytics 작업을 다시 시작해야 할 수도 있습니다. PartitionResolver를 사용하고 샘플 코드와 함께 다시 분할하는 방법에 대한 자세한 내용은 후속 게시물에서 다룰 예정입니다. [Cosmos DB에서 분할 및 크기 조정](../cosmos-db/sql-api-partition-data.md) 문서에서도 이에 대한 세부 정보를 제공합니다.

## <a name="cosmos-db-settings-for-json-output"></a>JSON 출력에 대한 Cosmos DB 설정
Cosmos DB를 Stream Analytics의 출력으로 만들면 아래와 같은 정보를 묻는 메시지가 생성됩니다. 이 섹션에서는 속성 정의에 대해 설명합니다.

분할된 컬렉션 | 여러 “단일 파티션” 컬렉션
---|---
![documentdb Stream Analytics 출력 화면](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb Stream Analytics 출력 화면](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> **여러 "단일 파티션" 컬렉션** 시나리오는 파티션 키를 필요로 하며 지원되는 구성입니다. 

* **출력 별칭** – ASA 쿼리에서 이 출력을 참조할 별칭입니다.  
* **계정 이름** – Cosmos DB 계정의 이름 또는 끝점 URI입니다.  
* **계정 키** – Cosmos DB 계정에 대한 공유 액세스 키입니다.  
* **데이터베이스** – Cosmos DB 데이터베이스 이름입니다.  
* **컬렉션 이름 패턴** – 사용될 컬렉션에 대한 컬렉션 이름이나 패턴입니다. 컬렉션 이름 형식은 선택적 {partition} 토큰을 사용하여 구성할 수 있으며 파티션은 0부터 시작합니다. 다음은 유효한 입력 샘플입니다.  
  1\) MyCollection – "MyCollection"이라는 이름의 컬렉션이 있어야 합니다.  
  2\) MyCollection{partition} – "MyCollection0”, “MyCollection1”, “MyCollection2” 등의 컬렉션이 있어야 합니다.  
* **파티션 키** – 선택 사항. 컬렉션 이름 패턴에 {parition} 토큰을 사용하는 경우에만 필요합니다. 컬렉션에서 출력 분할을 위한 키를 지정하는 데 사용되는 출력 이벤트의 필드 이름입니다. 단일 컬렉션 출력의 경우 임의의 출력 열(예: PartitionId)이 사용될 수 있습니다.  
* **문서 ID** – 선택 사항입니다. 삽입 또는 업데이트 작업이 기반으로 하는 기본 키를 지정하는 데 사용되는 출력 이벤트의 필드 이름입니다.  
