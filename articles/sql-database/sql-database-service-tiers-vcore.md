---
title: Azure SQL Database 서비스 - vCore | Microsoft Docs
description: 성능 수준 및 저장소 크기를 제공하기 위한 단일 및 풀 데이터베이스에 대한 서비스 계층에 대해 알아보세요.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/14/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 9abe7743906064d182453fea403ff94a097c3558
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212384"
---
# <a name="vcore-based-purchasing-model-for-azure-sql-database-preview"></a>Azure SQL Database에 대한 새 vCore 기반 구매 모델(미리 보기)

[Azure SQL Database](sql-database-technical-overview.md)는 계산, 저장소 및 IO 리소스에 대한 두 가지 구매 모델, 즉 DTU 기반 구매 모델과 vCore 기반 구매 모델(미리 보기)을 제공합니다. 다음 표와 차트에서는 이 두 가지 구매 모델을 비교하고 대조합니다.

> [!IMPORTANT]
> DTU 기반 구매 모델은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 참조하세요.


|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반 모델|계산, 저장소 및 IO 리소스를 번들로 묶은 측정값을 기반으로 합니다. 성능 수준은 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-what-is-a-dtu.md)을 참조하세요.|간단하고 미리 구성된 리소스 옵션을 원하는 고객에게 가장 적합합니다.| 
|vCore 기반 모델|이 모델을 사용하면 계산 및 저장소 리소스를 독립적으로 크기 조정할 수 있습니다(최대 80개 vCore, 4TB 데이터 저장소 및 200000 IOPS). SQL Server에 대한 Azure 하이브리드 혜택을 사용하여 비용을 절약할 수 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객에게 가장 적합합니다.|
||||  

![가격 책정 모델](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>vCore 기반 구매 모델(미리 보기)

가상 코어는 하드웨어 세대 중에서 선택할 수 있는 옵션과 함께 제공되는 논리 CPU를 나타냅니다. vCore 기반 구매 모델(미리 보기)은 개별 리소스 사용에 대한 유연성, 제어, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 통해 워크로드 요구 사항에 따라 계산, 메모리 및 저장소의 크기를 조정할 수 있습니다. vCore 기반 구매 모델(미리 보기)에서 고객은 [단일 데이터베이스](sql-database-single-database-resources.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 모두에 대해 범용 및 중요 비즈니스 서비스 계층(미리 보기) 중에서 선택할 수 있습니다. 

서비스 계층은 다양한 성능 수준, 고가용성 설계, 오류 격리, 저장소 유형 및 IO 범위로 구분됩니다. 고객은 백업에 필요한 저장소와 보존 기간을 개별적으로 구성해야 합니다. vCore 모델을 사용하는 경우 단일 데이터베이스와 탄력적 풀은 [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 통해 비용을 최대 30%까지 절약할 수 있습니다.

vCore 기반 구매 모델(미리 보기)에서 고객이 지불하는 비용은 다음과 같습니다.
- 계산(서비스 계층 + vCore 수 + 하드웨어 세대)*
- 데이터 저장소 및 로그 저장소의 유형 및 크기 
- IO 수**
- 백업 저장소(RA-GRS)** 

\* 최초 공개 미리 보기에서 4세대 논리 CPU는 Intel E5-2673 v3(Haswell) 2.4GHz 프로세서를 기반으로 합니다.

\*\* 미리 보기 동안 백업 및 IO는 7일간 무료입니다.

> [!IMPORTANT]
> 계산, IO, 데이터 저장소 및 로그 저장소는 데이터베이스 또는 탄력적 풀당 요금이 부과됩니다. 백업 저장소는 각 데이터베이스당 요금이 부과됩니다. 관리되는 인스턴스 요금에 대한 자세한 내용은 [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요.

> [!IMPORTANT]
> 지역 제한 사항: 
>
> vCore 기반 구매 모델(미리 보기)을 오스트레일리아 남동부에서는 아직 사용할 수 없습니다. 서유럽, 프랑스 중부, 영국 남부 및 영국 서부 지역에서는 미리 보기를 사용할 수 없습니다.
> 

## <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>서비스 계층, 계산, 메모리, 저장소 및 IO 리소스 선택

vCore 기반 구매 모델(미리 보기)로 변환하면 계산 및 저장소 리소스의 크기를 독립적으로 조정하고, 온-프레미스 성능에 맞추고, 가격을 최적화할 수 있습니다. 데이터베이스 또는 탄력적 풀에서 300을 초과하는 DTU를 vCore로 변환하면 비용을 절약할 수 있습니다. 선택한 API를 사용하거나 Azure Portal을 사용하여 가동 중지 시간 없이 변환할 수 있습니다. 그러나 변환이 필요하지 않습니다. DTU 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다. DTU 모델을 vCore 모델로 변환하려는 경우 경험 규칙을 사용하여 성능 수준을 선택해야 합니다. 이 경우 표준 계층의 각 100DTU에는 범용 계층에서 하나 이상의 vCore가 필요합니다. 프리미엄 계층의 각 125DTU에는 중요 비즈니스용 계층에서 하나 이상의 vCore가 필요합니다.

다음 표는 이러한 두 계층 간의 차이점을 이해하는 데 도움이 됩니다.

||**범용**|**중요 비즈니스**|
|---|---|---|
|적합한 대상|대부분의 비즈니스 워크로드. 예산 중심의 균형 잡히고 확장 가능한 계산 및 저장소 옵션을 제공합니다.|IO 요구 사항이 높은 비즈니스 응용 프로그램입니다. 여러 개의 격리된 복제본을 사용하여 실패에 대한 최고 수준의 복원력을 제공합니다.|
|컴퓨팅|1~80개 vCore, 4세대 및 5세대 |1~80개 vCore, 4세대 및 5세대|
|메모리|코어당 7GB |코어당 7GB |
|Storage|프리미엄 원격 저장소, 5GB-4TB|로컬 SSD 저장소, 5GB~4TB|
|IO 처리량(근사치)|vCore당 500 IOPS(최대 7,000 IOPS)|vCore당 5000 IOPS(최대 200,000 IOPS)|
|가용성|1개 복제본, 읽기 크기 조정 없음|3개 복제본, 1개 [읽기 크기 조정](sql-database-read-scale-out.md), 영역 중복 HA|
|Backup|RA-GRS, 7-35일(기본값: 7일)|RA-GRS, 7-35일(기본값: 7일)*|
|메모리 내|해당 없음|지원됨|
|||

\* 미리 보기 동안에는 백업 보존 기간을 구성할 수 없으며 7일로 고정됩니다.

> [!IMPORTANT]
> vCore의 계산 용량이 1개 미만인 경우 DTU 기반 구매 모델을 사용합니다.

단일 데이터베이스에 사용할 수 있는 특정 성능 수준 및 저장소 크기를 선택하는 방법에 대한 자세한 내용은 [단일 데이터베이스에 대한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels)을 참조하고, 탄력적 풀의 경우 [탄력적 풀에 대한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)을 참조하세요.

자주 묻는 질문에 대한 답변은 [SQL Database FAQ](sql-database-faq.md)를 참조하세요. 

## <a name="storage-considerations"></a>저장소 고려 사항

다음을 고려해 보세요.
- 할당된 저장소는 데이터 파일(MDF) 및 로그 파일(LDF) 파일에서 사용됩니다.
- 각 성능 수준은 최대 데이터베이스 크기를 지원하며 기본 최대 크기는 32GB입니다.
- 필요한 데이터베이스 크기(MDF 크기)를 구성하면, LDF를 지원하기 위해 추가 저장소의 30%가 자동으로 추가됩니다
- 10GB와 지원되는 최대 크기 중에서 데이터베이스 크기를 선택할 수 있습니다.
 - 표준 저장소의 경우 크기는 10GB 증분 단위로 늘리거나 줄입니다.
 - 프리미엄 저장소의 경우 크기는 250GB 증분 단위로 늘리거나 줄입니다.
- 범용 서비스 계층의 경우 `tempdb`에서 연결형 SSD를 사용하며, 이 저장소의 비용이 vCore 가격에 포함됩니다.
- 중요 비즈니스 서비스 계층의 경우 `tempdb`에서 MDF 및 LDF 파일이 포함된 연결형 SSD를 공유하며, tempDB 저장소의 비용이 vCore 가격에 포함됩니다.

> [!IMPORTANT]
> MDF 및 LDF에 할당된 총 저장소 비용이 부과됩니다.

MDF 및 LDF의 현재 총 크기를 모니터링하려면 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)를 사용합니다. 개별 MDF 및 LDF 파일의 현재 크기를 모니터링하려면 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)를 사용합니다.

## <a name="backups-and-storage"></a>백업 및 저장소

데이터베이스 백업 저장소는 SQL Database의 PITR(특정 시점 복원) 및 LTR(장기 보존) 기능을 지원하기 위해 할당됩니다. 이 저장소는 각 데이터베이스에 대해 개별적으로 할당되며 데이터베이스당 별도의 두 가지 요금으로 청구됩니다. 

- **PITR**: 개별 데이터베이스 백업은 RA-GRS 저장소에 자동으로 복사됩니다. 새 백업이 만들어지면 저장소 크기가 동적으로 증가합니다.  저장소는 주별 전체 백업, 일별 차등 백업 및 5분마다 복사되는 트랜잭션 로그 백업에 사용됩니다. 저장소 사용량은 데이터베이스 변경률과 보존 기간에 따라 다릅니다. 각 데이터베이스에 대한 개별적인 보존 기간은 7-35일 사이에서 구성할 수 있습니다. 데이터 크기의 1배에 해당하는 최소 저장소 크기는 추가 비용 없이 제공됩니다. 대부분의 데이터베이스에서 이 크기는 7일간의 백업을 저장하기에 충분합니다.
- **LTR**: SQL Database는 전체 백업의 장기 보존을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하도록 설정하면 이러한 백업이 RA-GRS 저장소에 자동으로 저장되지만 백업이 복사되는 빈도를 제어할 수 있습니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 이 구성은 LTR 백업에 사용되는 저장소의 크기를 정의합니다. LTR 가격 계산기를 사용하여 LTR 저장소 비용을 추정할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="azure-hybrid-use-benefit"></a>AHUB(Azure Hybrid Use Benefit)

vCore 기반 구매 모델(미리 보기)에서 [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 사용하여 기존 라이선스를 SQL Database의 할인된 가격으로 교환할 수 있습니다. 이 Azure 혜택에서는 온-프레미스 SQL Server 라이선스를 통해 Software Assurance가 있는 온-프레미스 SQL Server 라이선스를 사용하여 Azure SQL Database에서 최대 30%까지 절약할 수 있습니다.

![가격 책정](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>지역 복제 링크를 사용하여 단일 데이터베이스 마이그레이션

DTU 기반 모델에서 vCore 기반 모델로 마이그레이션하는 것은 표준 및 프리미엄 데이터베이스 간의 지역 복제 관계를 업그레이드하거나 다운그레이드하는 것과 비슷합니다. 지역 복제를 종료할 필요는 없지만 사용자가 순서 지정 규칙을 준수해야 합니다. 업그레이드하는 경우 보조 데이터베이스를 먼저 업그레이드한 다음, 주 데이터베이스를 업그레이드해야 합니다. 다운그레이드하는 경우 반대 순서로 주 데이터베이스를 먼저 다운그레이드한 다음, 보조 데이터베이스를 다운그레이드해야 합니다. 

두 개의 탄력적 풀 간에 지역 복제를 사용하는 경우, 한 풀을 기본 풀로 지정하고 다른 풀을 보조 풀로 지정하는 것이 좋습니다. 이 경우 마이그레이션하는 탄력적 풀에서 동일한 지침을 사용해야 합니다.  그러나 탄력적 풀에는 기술적으로 주 데이터베이스와 보조 데이터베이스가 모두 포함될 수 있습니다. 이 경우 올바르게 마이그레이션하려면 사용량이 많은 풀을 "기본 풀"로 처리하고 이에 따라 적절한 순서 지정 규칙을 적용해야 합니다.  

다음 표에서는 특정 마이그레이션 시나리오에 대한 지침을 제공합니다. 

|현재 서비스 계층|대상 서비스 계층|마이그레이션 유형|사용자 작업|
|---|---|---|---|
|Standard|범용 가상 컴퓨터|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|Premium|중요 비즈니스용|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|Standard|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|Standard|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|Premium|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|Premium|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
||||

\* 표준 계층의 각 100DTU 및 프리미엄 계층의 각 125DTU마다 각각 하나 이상의 vCore가 필요합니다.

## <a name="migration-of-failover-groups"></a>장애 조치 그룹의 마이그레이션 

여러 데이터베이스가 있는 장애 조치 그룹을 마이그레이션하려면 주 데이터베이스와 보조 데이터베이스를 개별적으로 마이그레이션해야 합니다. 이 과정에서 동일한 고려 사항과 순서 지정 규칙이 적용됩니다. 데이터베이스가 vCore 기반 모델로 변환된 후에는 장애 조치 그룹이 동일한 정책 설정으로 계속 적용됩니다. 

## <a name="creation-of-a-geo-replication-secondary"></a>지역 복제 보조 데이터베이스 만들기

주 데이터베이스와 동일한 서비스 계층을 사용하여 지역 복제 보조 데이터베이스만 만들 수 있습니다. 로그 생성 속도가 높은 데이터베이스의 경우, 보조 데이터베이스를 주 데이터베이스와 동일한 성능 수준으로 만드는 것이 좋습니다. 단일 주 데이터베이스에 대한 탄력적 풀에 지역 복제 보조 데이터베이스를 만드는 경우 주 데이터베이스 성능 수준과 일치하는 `maxVCore` 설정이 풀에 있는 것이 좋습니다. 다른 탄력적 풀에 있는 기본 탄력적 풀에 지역 보조 데이터베이스를 만드는 경우 풀의 설정은 `maxVCore`과 동일해야 합니다

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>데이터베이스 복사본을 사용하여 DTU 기반 데이터베이스를 vCore 기반 데이터베이스로 변환합니다.

대상 성능 수준이 원본 데이터베이스의 최대 데이터베이스 크기를 지원하는 한, 제한 또는 특별한 순서 지정 없이 DTU 기반 성능 수준의 데이터베이스를 vCore 기반 성능 수준의 데이터베이스에 복사할 수 있습니다. 이는 데이터베이스 복사본이 복사 작업의 시작 시간에 따라 데이터의 스냅숏을 만들고 원본과 대상 간에 데이터 동기화를 수행하지 않기 때문입니다. 

## <a name="next-steps"></a>다음 단계

- 사용할 수 있는 특정 성능 수준 및 저장소 크기를 선택하는 방법에 대한 자세한 내용은 [SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits.md) 및 [SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits.md)을 참조하세요.
- 자주 묻는 질문에 대한 답변은 [SQL Database FAQ](sql-database-faq.md)를 참조하세요.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에 대해 자세히 알아보세요.
