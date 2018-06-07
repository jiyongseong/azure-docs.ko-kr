---
title: Azure SQL Database 자동, 지역 중복 백업 | Microsoft Docs
description: SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 저장소를 사용하여 지리적 중복을 제공합니다.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189071"
---
# <a name="learn-about-automatic-sql-database-backups"></a>자동 SQL Database 백업에 대한 자세한 정보

SQL Database는 데이터베이스 백업을 자동으로 만들고 Azure RA-GRS(읽기 액세스 지역 중복 저장소)를 사용하여 지리적 중복을 제공합니다. 이러한 백업은 추가 비용 없이 자동으로 만들어집니다. 사용자는 아무 작업도 할 필요가 없습니다. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 고유한 저장소 컨테이너에서 백업을 유지하려면 장기 백업 보존 정책을 구성할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database는 SQL Server 기술을 사용하여 PITR(지정 시간 복원)의 목적으로 [전체](https://msdn.microsoft.com/library/ms186289.aspx), [차등](https://msdn.microsoft.com/library/ms175526.aspx) 및 [트랜잭션 로그](https://msdn.microsoft.com/library/ms191429.aspx) 백업을 만듭니다. 일반적으로 트랜잭션 로그 백업은 데이터베이스 활동의 성능 수준 및 양에 따른 빈도로 5 - 10분마다 발생합니다. 전체 및 차등 백업을 사용하는 트랜잭션 로그 백업을 통해 데이터베이스를 호스트하는 동일한 서버로 특정 시점에 대해 데이터베이스를 복원할 수 있습니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.


이러한 백업을 사용하여 다음을 수행할 수 있습니다.

* 보존 기간 내 지정 시간으로 데이터베이스를 복원합니다. 이 작업으로 원본 데이터베이스와 같은 서버에 새 데이터베이스가 만들어집니다.
* 삭제된 시간 또는 보존 기간 내 임의 시간으로 삭제된 데이터베이스를 복원합니다. 삭제된 데이터베이스는 원래 데이터베이스가 생성되었던 동일한 서버에만 복원할 수 있습니다.
* 데이터베이스를 다른 지리적 지역으로 복원합니다. 이렇게 하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다. 
* 장기 보존 정책을 사용하여 데이터베이스를 구성한 경우 특정 장기 백업에서 데이터베이스를 복원합니다. 그러면 이전 버전의 데이터베이스를 복원하여 규정 준수 요청을 충족하고 이전 버전의 응용 프로그램을 실행할 수 있습니다. [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
* 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 *데이터베이스 복제* 는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다. 
> 

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?
매주 전체 데이터베이스 백업이 수행되고, 일반적으로 몇 시간마다 차등 데이터베이스 백업이 수행되며, 일반적으로 5 - 10분마다 트랜잭션 로그 백업이 수행됩니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다. 

백업 저장소 지역에서 복제는 Azure Storage 복제 일정에 따라 발생합니다.

## <a name="how-long-do-you-keep-my-backups"></a>백업 보존 기간
각 SQL Database 백업에는 데이터베이스의 서비스 계층을 기반으로 하며 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md) 간 차이점이 있는 보존 기간이 있습니다. 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>DTU 기반 구매 모델에 대한 데이터베이스 보존
DTU 기반 구매 모델의 데이터베이스에 대한 보존 기간은 서비스 계층에 따라 달라집니다. 데이터베이스의 보존 기간은 다음과 같습니다.

* 기본 서비스 계층은 7일입니다.
* 표준 서비스 계층은 35일입니다.
* 프리미엄 서비스 계층은 35일입니다.
* 범용 계층은 최대 35일로 구성할 수 있음(기본적으로 7일)*
* 중요 비즈니스용 계층(미리 보기)은 최대 35일로 구성할 수 있음(기본적으로 7일)*

\* 미리 보기 동안에는 백업 보존 기간을 구성할 수 없으며 7일로 고정됩니다.

더 긴 백업 보존이 있는 데이터베이스를 더 짧은 보존이 있는 데이터베이스로 변환하는 경우 대상 계층의 보존 기간보다 오래된 기존의 모든 백업을 더 이상 사용할 수 없습니다.

더 짧은 보존 기간이 있는 데이터베이스를 더 긴 기간이 있는 데이터베이스로 업그레이드하는 경우 SQL Database는 더 긴 보존 기간에 도달할 때까지 기존 백업을 유지합니다. 

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 보관합니다. 예를 들어 보존 기간이 7일인 기본 데이터베이스를 삭제한다고 가정해 봅시다. 4일 된 백업은 앞으로 3일 동안 더 보관됩니다.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다.

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>VCore 기반 구매 모델(미리 보기)에 대한 데이터베이스 보존

데이터베이스 백업 저장소는 SQL Database의 PITR(특정 시점 복원) 및 LTR(장기 보존) 기능을 지원하기 위해 할당됩니다. 이 저장소는 각 데이터베이스에 대해 개별적으로 할당되며 데이터베이스당 별도의 두 가지 요금으로 청구됩니다. 

- **PITR**: 개별 데이터베이스 백업은 RA-GRS 저장소에 자동으로 복사됩니다. 새 백업이 만들어지면 저장소 크기가 동적으로 증가합니다.  저장소는 주별 전체 백업, 일별 차등 백업 및 5분마다 복사되는 트랜잭션 로그 백업에 사용됩니다. 저장소 사용량은 데이터베이스 변경률과 보존 기간에 따라 다릅니다. 각 데이터베이스에 대한 개별적인 보존 기간은 7-35일 사이에서 구성할 수 있습니다. 데이터 크기의 1배에 해당하는 최소 저장소 크기는 추가 비용 없이 제공됩니다. 대부분의 데이터베이스에서 이 크기는 7일간의 백업을 저장하기에 충분합니다. 자세한 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.
- **LTR**: SQL Database는 전체 백업의 장기 보존을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하도록 설정하면 이러한 백업이 RA-GRS 저장소에 자동으로 저장되지만 백업이 복사되는 빈도를 제어할 수 있습니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 이 구성은 LTR 백업에 사용되는 저장소의 크기를 정의합니다. LTR 가격 계산기를 사용하여 LTR 저장소 비용을 추정할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="how-to-extend-the-backup-retention-period"></a>백업 보존 기간을 확장하는 방법

응용 프로그램에 최대 PITR 백업 보존 기간보다 더 긴 기간 동안 백업을 사용할 수 있어야 하는 경우 개별 데이터베이스(LTR 정책)에 대해 장기 백업 보존 정책을 구성할 수 있습니다. 기본 제공 보존 기간은 최대 35일에서 최대 10년까지 연장할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

Azure Portal 또는 API를 사용하여 LTR 정책을 데이터베이스에 추가하면 매주 전체 데이터베이스 백업이 장기 보존(LTR 저장소)에 대한 별도의 RA-GRS 저장소 컨테이너로 자동으로 복사됩니다. TDE를 사용하여 암호화된 데이터베이스는 미사용 시 자동으로 암호화됩니다. SQL Database는 해당 타임스탬프 및 LTR 정책에 따라 만료된 백업을 자동으로 삭제합니다. 정책을 설정한 후 백업 일정을 관리할 필요가 없으며 이전 파일의 정리를 걱정할 필요가 없습니다. Azure Portal 또는 PowerShell을 사용하여 이러한 백업을 보거나 복원하거나 삭제할 수 있습니다.

## <a name="are-backups-encrypted"></a>백업이 암호화되나요?

Azure SQL Database에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL Database는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>자동 백업은 GDPR과 호환되나요?
백업이 GDPR(일반 데이터 보호 규정)이 적용되는 개인 데이터를 포함하는 경우 무단 액세스로부터 데이터를 보호하기 위해 향상된 보안 조치를 적용해야 합니다. GDPR을 준수하기 위해 백업에 액세스하지 않고 데이터 소유자의 데이터 요청을 관리하는 방법이 필요합니다.  단기 백업의 경우 한 가지 솔루션은 데이터 액세스 요청을 완료하도록 허용된 시간인 30일 아래로 백업 창을 줄이는 것이 될 수 있습니다.  장기 백업이 필요한 경우 백업에 "pseudonymized" 데이터만 저장하는 것이 좋습니다. 예를 들어 사용자에 대한 데이터를 삭제하거나 업데이트해야 하는 경우 기존 백업을 삭제하거나 업데이트할 필요가 없습니다. [GDPR 규정 준수에 대한 데이터 거버넌스](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html)에서 GDPR 모범 사례에 대한 자세한 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure Portal을 사용하여 지정 시간으로 복원하려면 [Azure Portal을 사용하여 지정 시간으로 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- PowerShell을 사용하여 지정 시간으로 복원하려면 [PowerShell을 사용하여 지정 시간으로 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.
- Azure Portal을 사용하여 Azure Recovery Services에서 자동화된 백업을 장기 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용하여 Azure Recovery Services에서 자동화된 백업을 장기 보존에서 구성, 관리 및 복원하려면 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
