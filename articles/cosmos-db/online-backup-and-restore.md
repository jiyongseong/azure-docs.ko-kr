---
title: Azure Cosmos DB로 온라인 백업 및 복원 | Microsoft Docs
description: Azure Cosmos DB 데이터베이스에서 자동 백업 및 복원을 수행하는 방법을 알아봅니다.
keywords: 백업 및 복원, 온라인 백업
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 5f8ddc9c57df878137ee1ff1b6431e40acfd5eb4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Azure Cosmos DB로 자동 온라인 백업 및 복원
Azure Cosmos DB는 자동으로 모든 데이터의 백업을 정기적으로 수행합니다. 자동 백업은 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. 모든 백업은 다른 저장소 서비스에 개별적으로 저장되고 이러한 백업은 지역 재해에 대한 복원을 위해 전역적으로 복제됩니다. 자동 백업은 Cosmos DB 컨테이너를 실수로 삭제했다가 나중에 데이터 복구 또는 재해 복구 솔루션이 필요한 시나리오를 위한 것입니다.  

이 문서는 Cosmos DB에서 데이터 중복성 및 가용성의 빠른 정리로 시작한 다음 백업을 설명합니다. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Cosmos DB를 사용한 고가용성 - 정리
Cosmos DB는 [전역으로 분산](distribute-data-globally.md)되도록 설계되었습니다. 이를 통해 정책 기반 장애 조치(Failover) 및 투명한 multi-homing API와 함께 여러 Azure 지역에서 처리량의 크기를 조정할 수 있습니다. Azure Cosmos DB는 모든 단일 지역 계정 및 평범한 일관성 수준의 모든 다중 지역 계정에 대한 [99.99% 가용성 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 및 모든 다중 지역 데이터베이스 계정에 대한 99.999% 읽기 가용성을 제공합니다. Azure Cosmos DB의 모든 쓰기는 클라이언트에 승인되기 전에 로컬 데이터 센터 내에서 복제본 쿼럼으로 로컬 디스크에 지속적으로 커밋됩니다. Cosmos DB의 고가용성은 로컬 저장소에 의존하고 외부 저장소 기술에 의존하지 않습니다. 또한 데이터베이스 계정이 둘 이상의 Azure 지역과 연결되어 있는 경우 사용자의 쓰기도 다른 지역에 걸쳐 복제됩니다. 낮은 대기 시간에 처리량을 확장하고 데이터에 액세스하기 위해 원하는 만큼 많이 데이터베이스 계정과 연결된 읽기 지역을 가질 수 있습니다. 각 읽기 지역에서 (복제된) 데이터는 복제본 세트에서 지속적으로 유지됩니다.  

다음 다이어그램과 같이 단일 Cosmos DB 컨테이너는 [수평 분할](partition-data.md)되어 있습니다. "파티션"은 다음 다이어그램에서 원으로 표시되고 각 파티션은 복제본 세트를 통해 고가용성으로 만들어집니다. 이는 단일 Azure 지역 내에서 로컬 배포입니다(X축에 의해 표시됨). 또한 각 파티션(해당 복제본 세트와 함께)은 데이터베이스 계정과 연결된 여러 지역으로 전역적으로 분산됩니다(예를 들어 이 그림에서 3개의 지역 - 미국 동부, 미국 서부 및 인도 중부). "파티션 집합"은 전역적으로 분산된 각 지역의 여러 데이터 복사본으로 구성된 엔터티입니다(Y축에 의해 표시됨). 데이터베이스 계정에 연결된 지역에 우선 순위를 지정할 수 있으며 Cosmos DB는 재해가 발생할 경우 다음 지역에 대해 투명하게 장애 조치합니다. 응용 프로그램의 종단 간 가용성 테스트를 위해 장애 조치를 수동으로 시뮬레이션할 수 있습니다.  

다음 그림은 높은 수준의 Cosmos DB와의 중복성을 보여 줍니다.

![높은 수준의 Cosmos DB와의 중복성](./media/online-backup-and-restore/redundancy.png)

![높은 수준의 Cosmos DB와의 중복성](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>전체, 자동, 온라인 백업
이런, 내 컨테이너 또는 데이터베이스를 삭제했습니다! Cosmos DB를 사용하면 데이터뿐만 아니라 데이터 백업이 지역 재해에 대한 높은 수준의 복원력 및 중복으로 만들어집니다. 이러한 자동화된 백업은 현재 약 4시간마다 수행되고 항상 최근 2개의 백업이 저장됩니다. 데이터가 실수로 삭제되거나 손상되면 8시간 이내에 [Azure 지원에 문의](https://azure.microsoft.com/support/options/)하세요. 

백업은 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. Cosmos DB는 프로비전된 RU를 사용하거나 성능에 영향을 주지 않을 뿐 아니라 데이터베이스 가용성에도 영향을 주지 않고 백그라운드에서 백업을 수행합니다. 

Cosmos DB 내에 저장된 데이터와 달리 자동 백업은 Azure Blob Storage 서비스에 저장됩니다. 낮은 대기 시간/효율적인 업로드를 보장하기 위해 백업 스냅숏이 Cosmos DB 데이터베이스 계정의 현재 쓰기 지역과 동일한 지역에 있는 Azure Blob Storage 인스턴스에 업로드됩니다. 지역적 재해에 대한 복구를 위해 Azure Blob Storage에 있는 백업 데이터의 각 스냅숏은 GRS(지역 중복 저장소)를 통해 다른 지역에 다시 복제됩니다. 다음 다이어그램은 전체 Cosmos DB 컨테이너(이 예제에서는 미국 서부의 기본 파티션 3개를 모두 포함)가 미국 서부의 원격 Azure Blob Storage 계정에 백업된 다음 미국 동부에 복제된 GRS에 백업되는 과정을 보여 줍니다. 

다음 그림은 GRS Azure Storage에 있는 모든 Cosmos DB 엔터티의 정기적인 전체 백업을 보여 줍니다.

![GRS Azure Storage에 있는 모든 Cosmos DB 엔터티의 정기적인 전체 백업](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Backup 보존 기간
위에서 설명한 대로 Azure Cosmos DB는 4시간마다 파티션 수준으로 데이터의 스냅숏을 생성합니다. 지정된 시간에는 마지막 두 스냅숏만 유지됩니다. 단, 컬렉션/데이터베이스가 삭제된 경우 지정된 컬렉션/데이터베이스 내 삭제된 모든 파티션의 기존 스냅숏은 30일 동안 유지합니다.

SQL API의 경우 사용자 고유의 스냅숏을 유지하려는 경우 Azure Cosmos DB [데이터 마이그레이션 도구](import-data.md#export-to-json-file)에서 JSON으로 내보내기 옵션을 사용하여 추가 백업을 예약할 수 있습니다.

> [!NOTE]
> "데이터베이스 수준에서 컨테이너의 집합에 대한 처리량을 프로비전"하는 경우 복원은 전체 데이터베이스 계정 수준에서 발생한다는 것을 기억하세요. 이 새 기능을 사용하는 경우 컨테이너 - 컬렉션/테이블/그래프를 실수로 삭제한 경우 8시간 이내에 지원 팀에 연락해야 합니다. 


## <a name="restoring-a-database-from-an-online-backup"></a>온라인 백업에서 데이터베이스 복원
데이터베이스 또는 컬렉션을 실수로 삭제한 경우 [지원 티켓](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출하거나 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의하여 마지막 자동 백업에서 데이터를 복원할 수 있습니다. 데이터 손상 문제로 인해 데이터베이스를 복원해야 하는 경우(컬렉션 내 문서가 삭제된 경우 포함) 손상된 데이터가 기존 백업에 덮어쓰이는 것을 방지하기 위해 추가 단계를 수행해야 할 때는 [데이터 손상 처리](#handling-data-corruption)를 참조하세요. 백업의 특정 스냅숏을 복원하려면 Cosmos DB에서 해당 스냅숏의 백업 주기 동안 데이터를 사용할 수 있어야 합니다.

## <a name="handling-data-corruption"></a>데이터 손상 처리
Azure Cosmos DB는 데이터베이스 계정에서 모든 파티션의 마지막 두 개 백업을 유지합니다. 이 모델은 마지막 버전 중 하나를 복원할 수 있으므로 컨테이너(문서, 그래프, 테이블의 컬렉션) 또는 데이터베이스가 실수로 삭제될 경우에 매우 잘 작동합니다. 그러나 사용자가 데이터 손상 문제를 일으킬 수 있는 경우 Azure Cosmos DB에서 데이터 손상을 인식하지 못할 수 있으며 손상이 기존 백업에 덮어쓰여질 가능성이 있습니다. 손상이 발견되는 즉시 사용자는 백업이 손상된 데이터로 덮어 쓰이지 않도록 보호하기 위해 손상된 컨테이너(컬렉션/그래프/테이블)를 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

여러 데이터 센터에 데이터베이스를 복제하려면 [Cosmos DB를 사용하여 데이터를 전역으로 배포](distribute-data-globally.md)를 참조하세요. 

Azure 지원에 문의하려면 [Azure 포털에서 티켓 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 참조하세요.

