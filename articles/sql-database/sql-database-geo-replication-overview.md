---
title: 장애 조치 그룹 및 활성 지역 복제 - Azure SQL Database | Microsoft Docs
description: 활성 지역 복제와 함께 자동 장애 조치(Failover)를 사용하고 가동 중단 시 자동 장애 조치(Failover)를 사용하도록 설정합니다.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 39c61fd3b2f03c99d74fded22927f319cc7f4cdd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160940"
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>개요: 장애 조치 그룹 및 활성 지역 복제
활성 지역 복제를 사용하면 동일하거나 다른 데이터 센터 위치(하위 지역)에 최대 4개의 읽기 기능한 보조 데이터베이스를 구성할 수 있습니다. 데이터 센터 정전이 발생하거나 주 데이터베이스에 연결하지 못하는 경우 쿼리 및 장애 조치(failover)에 보조 데이터베이스를 사용할 수 있습니다. 장애 조치는 사용자의 응용 프로그램에서 수동으로 시작되어야 합니다. 장애 조치 후 새 주 데이터베이스에는 다른 연결 엔드포인트가 있습니다. 

> [!NOTE]
> 활성 지역 복제는 모든 지역의 현재 모든 서비스 계층에 있는 모든 데이터베이스에 대해 제공됩니다.
>  

Azure SQL Database 자동 장애 조치 그룹(미리 보기 상태)은 규모에 맞게 지역에서 복제 관계, 연결 및 장애 조치를 자동으로 관리하도록 설계된 SQL Database 기능입니다. 이 기능을 사용하면 고객은 주 지역에서 SQL Database 서비스의 가용성을 완전히 또는 부분적으로 상실하게 하는 치명적인 지역적 장애 또는 계획되지 않은 다른 이벤트가 발생한 후에 보조 지역의 여러 관련 데이터베이스를 자동으로 복구할 수 있습니다. 또한 읽을 수 있는 보조 데이터베이스를 사용하여 읽기 전용 워크로드를 오프로드할 수 있습니다.  자동 장애 조치 그룹에는 여러 데이터베이스가 포함되므로 주 서버에서 이 그룹을 구성해야 합니다. 기본 서버와 보조 서버는 모두 동일한 구독에 있어야 합니다. 자동 장애 조치 그룹은 그룹의 모든 데이터베이스를 다른 지역에 있는 하나의 보조 서버로만 복제할 수 있도록 지원합니다. 자동 장애 조치 그룹 없는 활성 지역 복제는 모든 지역에서 최대 4개의 보조 서버를 허용합니다.

활성 지역 복제를 사용 중이고 어떤 이유로든 주 데이터베이스가 실패하거나 단순히 오프라인으로 전환해야 하는 경우 장애 조치를 보조 데이터베이스 중 하나로 시작할 수 있습니다. 장애 조치가 보조 데이터베이스 중 하나로 활성화된 경우 모든 다른 보조가 새 보조로 자동으로 연결됩니다. 자동 장애 조치 그룹(미리 보기 상태)을 사용하여 데이터베이스 복구를 관리하고 그룹의 데이터베이스 중 하나 이상에 영향을 미치는 가동 중단이 발생하면 자동 장애 조치를 수행합니다. 응용 프로그램 요구 사항에 가장 부합하는 자동 장애 조치 정책을 구성하거나 수동 활성화를 옵트아웃하고 사용할 수 있습니다. 또한 자동 장애 조치 그룹(미리 보기 상태)은 장애 조치하는 동안 변경되지 않는 읽기-쓰기 및 읽기 전용 수신기 끝점을 제공합니다. 수동 또는 자동 장애 조치 활성화를 사용하는지 여부에 관계 없이 장애 조치는 그룹의 모든 보조 데이터베이스를 주 데이터베이스로 전환합니다. 데이터베이스 장애 조치가 완료되면 끝점을 새 지역으로 리디렉션하도록 DNS 레코드를 자동으로 업데이트합니다.

활성 지역 복제를 사용하여 서버 또는 탄력적 풀에 있는 개별 데이터베이스 또는 데이터베이스 집합의 복제 및 장애 조치를 관리할 수 있습니다. 다음을 사용하여 수행할 수 있습니다. 

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: 단일 데이터베이스](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: 탄력적 풀](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: 그룹 장애 조치](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: 단일 데이터베이스 또는 탄력적 풀](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: 단일 데이터베이스](/rest/api/sql/replicationlinks/failover)
- [REST API: 장애 조치(failover) 그룹](/rest/api/sql/failovergroups/failover) 
 
장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요. 이는 활성 지역 복제 및 자동 장애 조치 그룹(미리 보기 상태) 모두에 적용됩니다.

활성 지역 복제는 SQL Server의 [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 기술을 활용하여 RCSI(읽기 커밋된 스냅숏 격리)를 통해 주 데이터베이스의 커밋된 트랜잭션을 보조 데이터베이스로 비동기적으로 복제합니다. 자동 장애 조치 그룹은 활성 지역 복제를 기반으로 하는 그룹 의미 체계를 제공하지만 동일한 비동기 복제 메커니즘이 사용됩니다. 지정된 지점에서 보조 데이터베이스는 주 데이터베이스보다 약간 뒤에 있을 수 있는 반면 보조 데이터는 절대 부분 트랜잭션을 갖지 않습니다. 지역 간 중복을 사용하면 자연 재해, 인간의 치명적인 실수 또는 악의적 행위로 인해 데이터 센터의 일부 또는 전체가 영구적으로 손실되더라도 응용 프로그램을 빠르게 복구할 수 있습니다. 특정 RPO 데이터를 [비즈니스 연속성 개요](sql-database-business-continuity.md)에서 찾을 수 있습니다.

다음 그림은 미국 중북부 지역에 주 데이터베이스, 미국 중남부 지역에 보조 데이터베이스가 구성된 활성 지역 복제의 예입니다.

![지역에서 복제 관계](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

보조 데이터베이스를 읽을 수 있기 때문에 보고 작업과 같은 읽기 전용 워크로드를 오프로드하는 데 사용할 수 있습니다. 활성 지역 복제를 사용하는 경우 주 데이터베이스와 동일한 지역에 보조 데이터베이스를 만들 수 있지만 치명적인 장애에 대한 응용 프로그램의 복원성을 높이지는 않습니다. 자동 장애 조치 그룹(미리 보기 상태)을 사용하는 경우 보조 데이터베이스는 항상 다른 지역에 만들어집니다.

활성 지역 복제는 재해 복구 외에도 다음과 같은 시나리오에서 사용할 수 있습니다.

* **데이터베이스 마이그레이션**: 활성 지역 복제를 사용하여 최소 가동 중지 시간으로 하나의 서버에서 다른 온라인으로 데이터베이스를 마이그레이션할 수 있습니다.
* **응용 프로그램 업그레이드**: 응용 프로그램을 업그레이드하는 동안 추가 보조 데이터베이스를 장애 복구 복사본으로 만들 수 있습니다.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 응용 프로그램(서비스) 종단 간 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 저장소 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 응용 프로그램의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## <a name="active-geo-replication-capabilities"></a>활성 지역 복제 기능
활성 지역 복제 기능은 다음과 같은 필수 기능을 제공합니다.
* **자동 비동기 복제**: 보조 데이터베이스는 기존 데이터베이스에 추가하는 방법으로만 만들 수 있습니다. 보조 데이터베이스는 모든 Azure SQL Database 서버에 만들 수 있습니다. 한번 만들면 보조 데이터베이스는 주 데이터베이스에서 복사된 데이터로 채워집니다. 이 프로세스를 시드라고 합니다. 보조 데이터베이스가 생성 및 시드되면 주 데이터베이스에 대한 업데이트가 보조 데이터베이스에 자동으로 비동기적으로 복사됩니다. 비동기 복제에서는 트랜잭션이 보조 데이터베이스에 복제되기 전에 주 데이터베이스에 커밋됩니다. 
* **읽을 수 있는 보조 데이터베이스**: 응용 프로그램은 주 데이터베이스에 액세스하는 데 사용된 동일한 보안 주체를 사용하여 읽기 전용 작업에 대한 보조 데이터베이스에 액세스할 수 있습니다. 보조 데이터베이스에서 실행되는 쿼리 때문에 주 데이터베이스(로그 재생)의 업데이트 복제가 지연되지 않도록 보장하기 위해 보조 데이터베이스는 스냅숏 격리 모드에서 작동합니다.

   > [!NOTE]
   > 주 데이터베이스에 스키마 업데이트가 있는 경우 보조 데이터베이스에서 로그 재생이 지연됩니다. 후자의 경우 보조 데이터베이스에 대한 스키마 잠금이 필요합니다. 
   > 

* **여러 번 읽을 수 있는 보조 데이터베이스**: 둘 이상의 보조 데이터베이스는 주 데이터베이스와 응용 프로그램의 중복성 및 보호 수준을 높입니다. 보조 데이터베이스가 여러 개 있는 경우 보조 데이터베이스 중 하나가 실패하더라도 응용 프로그램이 계속해서 보호됩니다. 보조 데이터베이스가 하나밖에 없는 경우 새 보조 데이터베이스를 만들 때까지 응용 프로그램이 더 높은 위험에 노출됩니다.

   > [!NOTE]
   > 활성 지역 복제를 사용하여 전역으로 분산된 응용 프로그램을 빌드하고 5개 이상의 지역에 있는 데이터에 대한 읽기 전용 액세스를 제공해야 하는 경우 보조 데이터베이스의 보조 데이터베이스를 만들 수 있습니다(체이닝으로 알려진 프로세스). 이렇게 하면 거의 제한이 없는 규모의 데이터베이스 복제를 수행할 수 있습니다. 또한 체이닝을 사용하면 주 데이터베이스의 복제 오버헤드가 줄어듭니다. 단점은 리프가 가장 많은 보조 데이터베이스의 복제 지연 시간이 증가하는 것입니다. 
   >

* **탄력적 풀 데이터베이스 지원**: 각 복제본은 탄력적 풀에 개별적으로 참여하거나 탄력적 풀에 전혀 존재할 수 없습니다. 각 복제본에 대한 풀 선택은 별개이며 다른 복제본(기본 또는 보조)의 구성에 종속되지 않습니다. 각 탄력적 풀은 단일 지역 내에 포함되므로 동일한 토폴로지의 여러 복제본에서 탄력적 풀을 공유할 수 없습니다.
* **보조 데이터베이스의 구성 가능한 성능 수준**: 기본 및 보조 데이터베이스에는 동일한 서비스 계층이 있어야 합니다. 보조 데이터베이스는 기본에 비해 낮은 성능 수준(DTU)으로 만들 수 있습니다. 데이터베이스 쓰기 활동이 많은 응용 프로그램에는 이 방법을 권장하지 않습니다. 복제 지연이 증가하고, 그로 인해 장애 조치(failover) 후 후속 데이터 손실의 위험이 높기 때문입니다. 뿐만 아니라 장애 조치(failover) 후 새로운 주 데이터베이스가 더 높은 성능 수준으로 업그레이드될 때까지 응용 프로그램 성능이 영향을 받습니다. Azure Portal의 로그 IO 백분율 차트는 복제 부하를 유지하는 데 필요한 보조 데이터베이스의 최소 성능 수준을 예상하는 데 유용하게 사용할 수 있습니다. 예를 들어 주 데이터베이스가 P6(1000 DTU)이면 해당 로그 IO 백분율은 50%이고 보조 데이터베이스는 최소한 P4(500 DTU) 이상이어야 합니다. [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 또는 [ys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 데이터베이스 뷰를 사용하여 로그 IO 데이터를 검색할 수도 있습니다.  SQL Database 성능 수준에 대한 자세한 내용은 [SQL Database 서비스 계층이란](sql-database-service-tiers.md)을 참조하세요. 
* **사용자 제어 장애 조치(failover) 및 장애 복구**: 보조 데이터베이스는 응용 프로그램 또는 사용자에 의해 언제든지 주 데이터베이스 역할로 전환될 수 있습니다. 실제로 가동이 중단되면 보조 데이터베이스를 즉시 주 데이터베이스로 승격하는 "계획되지 않음" 옵션을 사용해야 합니다. 중지된 주 데이터베이스가 복구되어 작동 가능한 상태가 되면 시스템에서는 복구된 주 데이터베이스를 자동으로 보조 데이터베이스로 표시하고 새로운 주 데이터베이스와 함께 최신 상태로 전환합니다. 주 데이터베이스가 가장 최근의 변경 사항을 보조 데이터베이스로 복제하기 전에 중단될 경우 비동기 복제의 특성상 계획되지 않은 장애 조치(failover)가 진행되는 동안 소량의 데이터가 손실될 수 있습니다. 보조 데이터베이스가 여러 개 있는 주 데이터베이스가 중단되면 시스템에서 사용자의 개입 없이 자동으로 복제 관계를 다시 구성하고 남아 있는 보조 데이터베이스를 새로 승격되는 주 데이터베이스에 연결합니다. 장애 조치(failover)를 일으킨 작동 중단 상황이 완화된 후에는 응용 프로그램을 주 지역으로 반환하는 것이 바람직할 수 있습니다. 장애 조치(failover)를 수행하려면 “계획됨” 옵션을 사용하여 명령을 호출해야 합니다. 
* **자격 증명과 방화벽 규칙의 동기화 유지**: 지역에서 복제된 데이터베이스에 [데이터베이스 방화벽 규칙](sql-database-firewall-configure.md)을 사용할 것을 권장합니다. 데이터베이스 방화벽 규칙을 데이터베이스와 함께 복제하면 모든 보조 데이터베이스가 주 데이터베이스와 동일한 방화벽 규칙을 갖기 때문입니다. 이렇게 하면 고객이 주 데이터베이스 및 보조 데이터베이스를 호스팅하는 서버에서 수동으로 방화벽 규칙을 구성하고 유지 관리할 필요가 없습니다. 마찬가지로, 데이터 액세스에 [포함된 데이터베이스 사용자](sql-database-manage-logins.md)를 사용하면 주 데이터베이스와 보조 데이터베이스의 사용자 자격 증명이 항상 똑같기 때문에 장애 조치(failover) 시에 로그인과 암호가 불일치하여 중단되는 일이 없습니다. [Azure Active Directory](../active-directory/active-directory-whatis.md)가 추가되면서 고객은 주 데이터베이스 및 보조 데이터베이스에 대한 사용자 액세스를 관리할 수 있으므로 데이터베이스의 자격 증명을 모두 관리할 필요가 없습니다.

## <a name="auto-failover-group-capabilities"></a>자동 장애 조치 그룹 기능

자동 장애 조치 그룹 기능은 그룹 수준 복제 및 자동 장애 조치를 지원하여 활성 지역 복제의 강력한 추상화를 제공합니다. 또한 추가 수신기 끝점을 제공하여 장애 조치 후 SQL 연결 문자열을 변경할 필요가 없습니다. 

* **장애 조치 그룹**: 다른 지역의 두 서버 (기본 및 보조 서버) 간에 하나 이상의 장애 조치 그룹을 만들 수 있습니다. 주 지역의 가동 중단으로 인해 주 데이터베이스 전체 또는 일부를 사용할 수 없게 되는 경우를 대비하여 각 그룹에 한 단위로 복구되는 하나 또는 여러 개의 데이터베이스가 포함될 수 있습니다.  
* **주 서버**: 장애 조치 그룹에서 주 데이터베이스를 호스팅하는 서버입니다.
* **보조 서버**: 장애 조치 그룹에서 보조 데이터베이스를 호스팅하는 서버입니다. 보조 서버는 주 서버와 동일한 지역에 있을 수 없습니다.
* **장애 조치 그룹에 데이터베이스 추가**: 서버 내 또는 탄력적 풀 내의 여러 데이터베이스를 동일한 장애 조치 그룹에 배치할 수 있습니다. 그룹에 독립 실행형 데이터베이스를 추가하면 동일한 버전과 성능 수준을 사용하는 보조 데이터베이스가 자동으로 만들어집니다. 탄력적 풀에 주 데이터베이스가 있으면 보조 서버가 동일한 이름을 가진 탄력적 풀에 자동으로 만들어집니다. 보조 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하면 해당 지역 복제가 그룹에 상속됩니다.

   > [!NOTE]
   > 장애 조치 그룹에 속하지 않은 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하면 새 보조 데이터베이스가 보조 서버에 만들어집니다. 
   >

* **장애 조치(failover) 그룹 읽기-쓰기 수신기**: 현재 주 서버 URL을 가리키는 **&lt;failover-group-name&gt;.database.windows.net** 형식의 DNS CNAME 레코드입니다. 장애 조치 후 주 데이터베이스가 변경되면 읽기-쓰기 SQL 응용 프로그램이 해당 주 데이터베이스에 투명하게 다시 연결할 수 있습니다. 
* **장애 조치(failover) 그룹 읽기 전용 수신기**: 현재 보조 서버 URL을 가리키는 **&lt;failover-group-name&gt;.secondary.database.windows.net** 형식의 DNS CNAME 레코드입니다. 읽기 전용 SQL 응용 프로그램은 지정된 부하 분산 규칙을 사용하여 보조 데이터베이스에 투명하게 연결할 수 있습니다. 필요에 따라 보조 서버를 사용할 수 없을 때 읽기 전용 트래픽을 주 서버로 자동으로 리디렉션할지 여부를 지정할 수 있습니다.
* **자동 장애 조치 정책**: 기본적으로 장애 조치 그룹은 자동 장애 조치 정책으로 구성됩니다. 장애가 검색되는 즉시 시스템에서 장애 조치를 트리거합니다. 응용 프로그램에서 장애 조치 워크플로를 제어하려는 경우 자동 장애 조치를 해제할 수 있습니다. 
* **수동 장애 조치**: 자동 장애 조치 구성에 관계 없이 언제든지 장애 조치를 수동으로 시작할 수 있습니다. 자동 장애 조치(failover) 정책이 구성되지 않은 경우 장애 조치 그룹의 데이터베이스를 복구하려면 수동 장애 조치(failover)를 수행해야 합니다. 강제 장애 조치 또는 친숙한 장애 조치(전체 데이터 동기화 사용)를 시작할 수 있습니다. 후자는 활성 서버를 주 지역으로 다시 배치하는 데 사용될 수 있습니다. 장애 조치(failover)가 완료되면 올바른 서버에 대한 연결을 보장하도록 DNS 레코드가 자동으로 업데이트됩니다.
* **데이터 손실이 있는 유예 기간**: 주 및 보조 데이터베이스가 비동기 복제를 사용하여 동기화되기 때문에 장애 조치(failover)로 인해 데이터가 손실될 수 있습니다. 응용 프로그램의 데이터 손실 허용 오차를 반영하도록 자동 장애 조치 정책을 사용자 지정할 수 있습니다. **GracePeriodWithDataLossHours**를 구성하여 데이터 손실이 발생할 수 있는 장애 조치를 시작하기 전에 시스템에서 대기하는 시간을 제어할 수 있습니다. 

   > [!NOTE]
   > 시스템에서 그룹의 데이터베이스가 아직 온라인 상태에 있음(예: 가동 중단이 서비스 제어 평면에만 영향을 미침)을 검색하면 **GracePeriodWithDataLossHours**에서 설정한 값에 관계 없이 전체 데이터 동기화(친숙한 장애 조치)를 통해 장애 조치를 즉시 활성화합니다. 이 동작은 복구 중 데이터가 손실되지 않도록 합니다. 유예 기간은 친숙한 장애 조치를 수행할 수 없는 경우에만 적용됩니다. 유예 기간이 만료되기 전에 가동 중단이 완화되면 장애 조치가 활성화되지 않습니다.
   >

* **여러 장애 조치 그룹**: 동일한 서버 쌍에 대해 여러 장애 조치 그룹을 구성하여 장애 조치의 크기를 제어할 수 있습니다. 각 그룹은 독립적으로 장애 조치됩니다. 다중 테넌트 응용 프로그램에서 탄력적 풀을 사용하는 경우 이 기능을 사용하여 각 풀에 주 및 보조 데이터베이스를 혼합할 수 있습니다. 이렇게 하면 테넌트의 절반에 대해서만 가동 중단에 따른 영향을 줄일 수 있습니다.

## <a name="best-practices-of-building-highly-available-service"></a>항상 사용 가능한 서비스를 빌드하는 모범 사례

Azure SQL Database를 사용하는 항상 사용 가능한 서비스를 빌드하려면 사용자는 다음 지침을 따라야 합니다.

- **장애 조치(failover) 그룹 사용**: 다른 지역의 두 서버(기본 및 보조 서버) 간에 하나 이상의 장애 조치(failover) 그룹을 만들 수 있습니다. 주 지역의 가동 중단으로 인해 주 데이터베이스 전체 또는 일부를 사용할 수 없게 되는 경우를 대비하여 각 그룹에 한 단위로 복구되는 하나 또는 여러 개의 데이터베이스가 포함될 수 있습니다. 장애 조치(failover) 그룹을 사용하는 경우 주 데이터베이스와 서비스 목표가 동일한 지역 보조 데이터베이스가 작성됩니다. 장애 조치 그룹에 기존 지역에서 복제 관계를 추가하는 경우 보조 데이터베이스가 주 데이터베이스와 동일한 서비스 수준 목표를 갖도록 구성했는지 확인합니다.
- **OLTP 작업용 읽기-쓰기 수신기 사용**: OLTP 작업을 수행할 때 **&lt;failover-group-name&gt;.database.windows.net**을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새 주 데이터베이스로 리디렉션됩니다.
- **읽기 전용 작업용 읽기 전용 수신기 사용**: 특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 응용 프로그램에서 보조 데이터베이스를 사용할 수 있습니다. 읽기 전용 세션의 경우 **&lt;failover-group-name&gt;.secondary.database.windows.net**을 서버 URL로 사용하면 자동으로 보조 데이터베이스에 연결됩니다. 또한 **ApplicationIntent=ReadOnly**를 사용하여 연결 문자열 읽기 의도를 표시하는 것이 좋습니다. 
- **성능 저하에 대비**: SQL 장애 조치 결정은 응용 프로그램의 나머지 부분 또는 사용되는 다른 서비스에서 독립적입니다. 응용 프로그램은 한 지역의 일부 구성 요소 및 나머지 지역의 일부 구성 요소와 "혼합"될 수 있습니다. 성능 저하를 방지하려면 DR 지역에서 중복 응용 프로그램 배포를 확인하고 이 문서의 지침을 따릅니다. DR 지역의 응용 프로그램이 다른 연결 문자열을 사용하지 않아도 됩니다.  
- **데이터 손실에 대비**: 가동 중지가 검색되는 경우 SQL은 중요한 지식에 대한 데이터 손실이 없으면 읽기-쓰기 장애 조치(failover)를 자동으로 트리거합니다. 그렇지 않으면 **GracePeriodWithDataLossHours**에서 지정한 기간 동안 대기합니다. **GracePeriodWithDataLossHours**를 지정한 경우 데이터 손실에 대비합니다. 일반적으로 중단 시에 Azure에서는 가용성을 우선으로 합니다. 데이터 손실을 방지하려는 경우 **GracePeriodWithDataLossHours**를 24시간과 같은 충분히 큰 숫자로 설정해야 합니다. 

> [!IMPORTANT]
> 지역에서 복제를 사용하는 800개 이하의 DTU 및 250개 이상의 데이터베이스가 있는 탄력적 풀에는 더 길게 계획된 장애 조치(failover) 및 저하된 성능을 포함한 문제가 발생할 수 있습니다.  이러한 문제는 지역에서 복제 끝점이 지리적으로 광범위하게 분리되어 있거나 여러 보조 끝점이 각 데이터베이스에 대해 사용되는 경우 쓰기 집약적 작업에 발생할 가능성이 높습니다.  이러한 문제의 증상은 지역에서 복제 지연이 시간에 따라 증가하는 경우에 표시됩니다.  이러한 지연은 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 사용하여 모니터링할 수 있습니다.  이러한 문제가 발생하는 경우 완화 방안에는 풀 DTU의 수를 늘리거나 동일한 풀에 지역에서 복제된 데이터베이스 수를 줄이는 방법이 있습니다.

## <a name="upgrading-or-downgrading-a-primary-database"></a>주 데이터베이스 업그레이드 또는 다운그레이드
보조 데이터베이스와의 연결을 해제하지 않고 주 데이터베이스를 다른 성능 수준(동일한 서비스 계층 내)으로 업그레이드 또는 다운그레이드할 수 있습니다. 업그레이드하는 경우에는 보조 데이터베이스를 먼저 업그레이드한 다음에 주 데이터베이스를 업그레이드하는 것이 좋습니다. 다운그레이드하는 경우에는 반대 순서로 주 데이터베이스를 먼저 다운그레이드하고 보조 데이터베이스를 다운그레이드합니다. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다. 

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다. 
>

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지
광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 응용 프로그램은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 응용 프로그램 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. **sp_wait_for_database_copy_sync** 호출은 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 전송될 때까지 호출 스레드를 차단합니다. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. **sp_wait_for_database_copy_sync**는 그 범위가 특정 연속 복사 링크로 한정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> **sp_wait_for_database_copy_sync**는 장애 조치 후 데이터 손실을 방지하지만 읽기 액세스를 위한 전체 동기화를 보장하지 않습니다. **sp_wait_for_database_copy_sync** 프로시저 호출로 인한 지연은 심각할 수 있으며 호출 시 트랜잭션 로그의 크기에 따라 달라집니다. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>장애 조치(failover) 그룹 및 활성 지역 복제를 프로그래밍 방식으로 관리
앞에서 설명한 대로 자동 장애 조치 그룹(미리 보기 상태)과 활성 지역 복제는 Azure PowerShell 및 REST API를 사용하여 프로그래밍 방식으로 관리할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다.

**Azure Resource Manager API 및 역할 기반 보안**: 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 및 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 비롯한 Azure Resource Manager API 집합을 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현하는 방법에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/overview.md)을 참조하세요.

## <a name="manage-sql-database-failover-using-transact-sql"></a>Transact-SQL을 사용하여 SQL Database 장애 조치(failover) 관리

| 명령 | 설명 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작하려면 ADD SECONDARY ON SERVER 인수를 사용합니다. |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |장애 조치를 시작하기 위해 보조 데이터베이스를 기본 데이터베이스로 전환하려면 FAILOVER 또는 FORCE_FAILOVER_ALLOW_DATA_LOSS를 사용합니다. |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료하려면 REMOVE SECONDARY ON SERVER를 사용합니다. |
| [sys.geo_replication_links(Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Azure SQL Database 논리 서버의 각 데이터베이스에 대한 모든 기존 복제 링크에 대한 정보를 반환합니다. |
| [sys.dm_geo_replication_link_status(Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |지정된 SQL Database의 복제 링크에 대한 마지막 복제 시간, 마지막 복제 지연 및 기타 정보를 가져옵니다. |
| [sys.dm_operation_status(Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |복제 링크의 상태를 비롯한 모든 데이터베이스 작업에 대한 상태를 표시합니다. |
| [sp_wait_for_database_copy_sync(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |커밋된 모든 트랜잭션이 복제되고 활성 보조 데이터베이스에서 승인될 때까지 응용 프로그램이 대기하도록 합니다. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>PowerShell을 사용하여 SQL Database 장애 조치(failover) 관리

| Cmdlet | 설명 |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |하나 이상의 데이터베이스를 가져옵니다. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작합니다. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |장애 조치를 시작하기 위해 보조 데이터베이스로 전환합니다. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료합니다. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Azure SQL Database와 리소스 그룹 또는 SQL Server 간의 지역에서 복제 링크를 가져옵니다. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | 서버에서 장애 조치 그룹을 제거하고 그룹에 포함된 모든 보조 데이터베이스를 삭제합니다. |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | 장애 조치 그룹 구성을 검색합니다. |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   장애 조치 그룹의 구성을 수정합니다. |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | 장애 조치 그룹의 장애 조치를 보조 서버로 트리거합니다. |
|  | |

> [!IMPORTANT]
> 샘플 스크립트에 대해서는 [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md) 및 [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)(미리 보기)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)를 참조하세요.
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>REST API를 사용하여 SQL Database 장애 조치(failover) 관리
| API | 설명 |
| --- | --- |
| [데이터베이스 생성 또는 업데이트(createMode=Restore)](/rest/api/sql/Databases/CreateOrUpdate) |주 보조 데이터베이스 또는 보조 데이터베이스를 만들거나, 업데이트하거나, 복원합니다. |
| [데이터베이스 만들기 또는 업데이트 상태 가져오기](/rest/api/sql/Databases/CreateOrUpdate) |만들기 작업 동안 상태를 반환합니다. |
| [보조 데이터베이스를 주 데이터베이스로 설정(계획된 장애 조치(failover))](/rest/api/sql/replicationlinks/failover) |현재 주 복제본 데이터베이스에서 장애 조치(failover)를 통해 주 복제본 데이터베이스로 사용할 데이터베이스를 설정합니다. |
| [보조 데이터베이스를 주 데이터베이스로 설정(계획되지 않은 장애 조치(failover))](/rest/api/sql/replicationlinks/failoverallowdataloss) |현재 주 복제본 데이터베이스에서 장애 조치(failover)를 통해 주 복제본 데이터베이스로 사용할 데이터베이스를 설정합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [복제 링크 가져오기](/rest/api/sql/replicationlinks/get) |지역에서 복제 파트너 관계의 지정된 SQL 데이터베이스에 대한 특정 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. |
| [복제 링크 - 데이터베이스 기준 목록](/rest/api/sql/replicationlinks/listbydatabase) | 지역에서 복제 파트너 관계의 지정된 SQL 데이터베이스에 대한 모든 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. |
| [복제 링크 삭제](/rest/api/sql/databases/delete) | 데이터베이스 복제 링크를 삭제합니다. 장애 조치(failover) 중에 수행할 수 없습니다. |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](/rest/api/sql/failovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](/rest/api/sql/failovergroups/delete) | 서버에서 장애 조치 그룹을 제거합니다. |
| [장애 조치(failover)(계획됨)](/rest/api/sql/failovergroups/failover) | 현재 주 서버에서 이 서버로 장애 조치(failover)합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |현재 주 서버에서 이 서버로 장애 조치(failover)합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](/rest/api/sql/failovergroups/get) | 장애 조치(failover) 그룹을 가져옵니다. |
| [서버별 장애 조치(failover) 그룹 나열](/rest/api/sql/failovergroups/listbyserver) | 서버에 있는 장애 조치(failover) 그룹을 나열합니다. |
| [장애 조치(failover) 그룹 업데이트](/rest/api/sql/failovergroups/update) | 장애 조치(failover) 그룹을 업데이트합니다. |
|  | |

## <a name="next-steps"></a>다음 단계
* 샘플 스크립트에 대해서는 다음을 참조하세요.
   - [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [활성 지역 복제를 사용하여 풀된 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)(미리 보기)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
* Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
* 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.

