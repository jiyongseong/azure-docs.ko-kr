---
title: 최대 10년 동안 Azure SQL Database 백업 저장 | Microsoft Docs
description: Azure SQL Database에서 최대 10년 동안 전체 데이터베이스 백업을 저장하도록 지원하는 방법에 대해 알아봅니다.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 05/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ce78201e3f87b9687ced181f90d352d73aa29431
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366065"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>최대 10년 동안 Azure SQL Database 백업 저장

여러 응용 프로그램에서는 규정, 규정 준수 또는 기타 비즈니스를 목적으로 Azure SQL Database의 [자동 백업](sql-database-automated-backups.md)에서 제공하는 데이터베이스 백업을 7-35일 넘게 보존하도록 요구합니다. LTR(장기 보존) 기능을 사용하여 [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) Blob 저장소에 최대 10년 동안 지정된 SQL 데이터베이스 전체 백업을 저장할 수 있습니다. 그런 다음, 새 데이터베이스로 모든 백업을 복원할 수 있습니다.

> [!IMPORTANT]
> 장기 보존은 현재 미리 보기 상태입니다. 이 기능은 이전 미리 보기의 일부분으로 Azure Services Recovery Service 자격 증명 모음에 저장된 기존 백업이 SQL Azure 저장소로 마이그레이션됩니다.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>SQL Database의 장기 보존 작동 방법

장기 백업 보존은 PITR(지정 시간 복원)을 사용하여 만든 [자동 SQL Database 백업](sql-database-automated-backups.md)을 활용합니다. 각 SQL 데이터베이스에 대해 장기 보존 정책을 구성하고 장기 저장소에 백업을 복사해야 하는 빈도를 지정할 수 있습니다. 해당 유연성을 활성화하기 위해 네 개의 매개 변수(주간 백업 보존(W), 월간 백업 보존(M), 연간 백업 보존(Y), 연간 주(WeekOfYear)) 조합을 사용하여 정책을 정의할 수 있습니다. W를 지정하는 경우 매주 하나의 백업이 장기 저장소에 복사됩니다. M을 지정하는 경우 각 월의 첫 주 동안 하나의 백업이 장기 저장소에 복사됩니다. Y를 지정하는 경우 WeekOfYear로 지정된 주 동안 하나의 백업이 장기 저장소에 복사됩니다. 각 백업은 이러한 매개 변수에 의해 지정된 기간 동안 장기 저장소에 유지됩니다. 

예제:

-  W=0, M=0, Y=5, WeekOfYear=3

   매년 세 번째 전체 백업이 5년 동안 유지됩니다.

- W=0, M=3, Y=0

   매월 첫 번째 전체 백업이 3개월 동안 유지됩니다.

- W=12, M=0, Y=0

   각각의 매주 전체 백업이 12주 동안 유지됩니다.

- W=6, M=12, Y=10, WeekOfYear=16

   각각의 매주 전체 백업이 6주 동안 유지됩니다. 매월 첫 번째 전체 백업을 제외하고 12개월 동안 유지됩니다. 연간 16번째 주에서 수행된 전체 백업을 제외하고 10년 동안 유지됩니다. 

다음 표는 다음 정책에 대한 장기 백업의 주기 및 만료를 나타냅니다.

W=12주(84일), M=12개월(365일), Y=10년(3650일), WeekOfYear=15(4월 15일 이후 주)

   ![ltr 예제](./media/sql-database-long-term-retention/ltr-example.png)


 
위의 정책을 수정하고 W=0(주별 백업 없음)으로 설정하는 경우 백업 복사본의 주기는 강조 표시된 날짜까지 위의 표에 나와 있는 것처럼 변경됩니다. 이러한 백업을 유지하는 데 필요한 저장소 크기는 적절하게 줄어듭니다. 

> [!NOTE]
1. LTR 복사본은 Azure 저장소 서비스에서 생성되므로 복사 프로세스는 기존 데이터베이스에 성능 영향을 주지 않습니다.
2. 이 정책은 향후 백업에 적용됩니다. 예: 지정된 WeekOfYear가 정책이 구성된 지난 해인 경우 첫 번째 LTR 백업은 다음 해에 만들어집니다. 
3. LTR 저장소에서 데이터베이스를 복원하기 위해 해당 타임스탬프에 따라 특정 백업을 선택할 수 있습니다.   데이터베이스는 원본 데이터베이스와 동일한 구독 아래의 기존 서버로 복원될 수 있습니다. 
> 

## <a name="configure-long-term-backup-retention"></a>장기 백업 보존 구성

Azure Portal 또는 PowerShell을 사용하여 장기 보존을 구성하는 방법을 알아보려면 [장기 백업 보존 구성](sql-database-long-term-backup-retention-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략에서 필수입니다. 다른 SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
