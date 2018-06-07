---
title: SQL Database 재해 복구 | Microsoft Docs
description: Azure SQL Database 활성 지역 복제 및 지역 복원 기능을 사용하여 하위 지역 데이터 센터 중단 또는 오류로부터 데이터베이스를 복구하는 방법에 대해 알아봅니다.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 6ac4b26e1b014da792791ca657c9f51230a135b5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Azure SQL Database 복원 또는 보조 데이터베이스에 대한 장애 조치
Azure SQL Database는 중단에서의 복구를 위해 다음 기능을 제공합니다.

* [활성 지역 복제 및 장애 조치(Failover) 그룹](sql-database-geo-replication-overview.md)
* [지역 복원](sql-database-recovery-using-backups.md#point-in-time-restore)
* [영역 중복 데이터베이스](sql-database-high-availability.md)

비즈니스 연속성 시나리오와 이러한 시나리오를 지원하는 기능에 대해 알아보려면 [비즈니스 연속성](sql-database-business-continuity.md)을 참조하세요.

> [!NOTE]
> 영역 중복 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀(미리 보기)을 사용하는 경우 복구 프로세스가 자동화되고 이 자료의 나머지 부분이 적용되지 않습니다. 

### <a name="prepare-for-the-event-of-an-outage"></a>가동 중단 이벤트에 대비
장애 조치(Failover) 그룹 또는 지역 중복 백업을 사용하여 다른 데이터 영역으로 성공적으로 복구하려면 이러한 요구가 발생하고 원활한 복구를 보장하기 위해 잘 정의된 단계가 문서화되고 테스트된 경우 다른 데이터 센터 가동 중단에 대비해서 서버를 새로운 주 서버로 준비해야 합니다. 이러한 준비 단계는 다음과 같습니다.

* 새로운 주 서버가 될 수 있는 다른 지역의 논리 서버를 식별합니다. 지역 복원의 경우 일반적으로 데이터베이스가 있는 지역과 [쌍을 이루는 지역](../best-practices-availability-paired-regions.md)에 있는 서버입니다. 이렇게 하면 지역 복원 작업 중에 추가 트래픽 비용이 제거됩니다.
* 사용자가 새로운 주 데이터베이스에 액세스하는 데 필요한 서버 수준 방화벽 규칙을 식별하고 경우에 따라 정의합니다.
* 연결 문자열을 변경하거나 DNS 항목을 변경하는 것과 같이 사용자를 새로운 주 서버로 리디렉션하는 방법을 결정합니다.
* 새로운 주 서버의 master 데이터베이스에 있어야 하는 로그인을 식별하고 필요에 따라 만든 후, 이러한 로그인(있는 경우)이 master 데이터베이스에서 적절한 권한이 있는지 확인합니다. 자세한 내용은 [재해 복구 후 Azure SQL Database 보안](sql-database-geo-replication-security-config.md)
* 새로운 주 데이터베이스에 매핑하기 위해 업데이트해야 하는 경고 규칙을 식별합니다.
* 현재 주 데이터베이스에 대한 감사 구성을 문서화합니다.
* [재해 복구 훈련](sql-database-disaster-recovery-drills.md)을 수행합니다. 지역 복원 기능에 대해 가동 중단을 시뮬레이션하려면 원본 데이터베이스를 삭제하거나 이름을 바꿔 응용 프로그램 연결 오류를 발생시킬 수 있습니다. 장애 조치(Failover) 그룹을 사용한 가동 중단을 시뮬레이트하려면 데이터베이스에 연결된 웹 응용 프로그램 또는 가상 머신을 사용하지 않도록 설정하거나 데이터베이스를 장애 조치하여 응용 프로그램 연결 오류를 발생시킬 수 있습니다.

## <a name="when-to-initiate-recovery"></a>복구를 시작해야 하는 시기
복구 작업은 응용 프로그램에 영향을 줍니다. DNS를 사용하여 SQL 연결 문자열 또는 리디렉션을 변경해야 하며 데이터가 영구적으로 손상될 수 있습니다. 따라서 중단이 응용 프로그램의 복구 시간 목표보다 오래 지속될 가능성이 있을 때만 복구를 수행해야 합니다. 응용 프로그램이 프로덕션에 배포되면 응용 프로그램 상태를 정기적으로 모니터링하고 다음 데이터 요소를 사용하여 복구가 보장되는지 확인해야 합니다.

1. 응용 프로그램 계층에서 데이터베이스로 영구적인 연결 실패
2. Azure 포털에 광범위한 영향이 있는 지역 내 인시던트에 대한 경고가 표시됩니다.

> [!NOTE]
> 장애 조치(failover) 그룹을 사용 중이며 자동 장애 조치(failover)를 선택한 경우 복구 프로세스가 자동화되고 응용 프로그램에 대해 투명해집니다. 

가동 중지 시간에 대한 응용 프로그램 허용 범위 및 가능한 비즈니스 책임에 따라 다음과 같은 복구 옵션을 고려할 수 있습니다.

[Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*)를 사용하여 가장 최근의 지역에서 복제된 복원 지점을 가져옵니다.

## <a name="wait-for-service-recovery"></a>서비스 복구 대기
Azure 팀은 가능한 한 신속하게 서비스 가용성을 복원하기 위해 열심히 작업하지만 루트에 따라 몇 시간 또는 며칠씩 걸릴 수 있습니다.  응용 프로그램이 가동 중지 시간을 상당히 허용할 수 있는 경우 복구가 완료되기를 기다릴 수밖에 없습니다. 이 경우에 사용자의 조치가 필요하지 않습니다. 서비스의 현재 상태를 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)에서 확인할 수 있습니다. 지역 복구 후에 응용 프로그램의 가용성이 복원됩니다.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>장애 조치(failover) 그룹에 있는 지역에서 복제된 보조 서버로 장애 조치합니다.
응용 프로그램의 가동 중지 시간으로 인해 비즈니스 책임이 발생할 경우 장애 조치(Failover) 그룹을 사용해야 합니다. 가동 중지 시에도 응용 프로그램이 다른 지역의 가용성을 신속하게 복원할 수 있습니다. [장애 조치(failover) 그룹을 구성](sql-database-geo-replication-portal.md)하는 방법에 대해 알아봅니다.

데이터베이스의 가용성을 복원하려면 지원되는 방법 중 하나를 사용하여 보조 서버에 대한 장애 조치(failover)를 시작해야 합니다.

다음 가이드 중 하나를 사용하여 지역에서 복제된 보조 데이터베이스를 장애 조치합니다.

* [Azure Portal을 사용하여 지역에서 복제된 보조 서버로 장애 조치](sql-database-geo-replication-portal.md)
* [PowerShell을 사용하여 보조 서버로 장애 조치](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)

## <a name="recover-using-geo-restore"></a>지역 복원을 사용한 복구
응용 프로그램의 가동 중지 시간으로 인해 비즈니스 책임이 발생하지 않은 경우 응용 프로그램 데이터베이스를 복구하는 방법으로 [지역 복원](sql-database-recovery-using-backups.md)을 사용할 수 있습니다. 최신 지역 중복 백업에서 데이터베이스의 복사본을 만듭니다.

## <a name="configure-your-database-after-recovery"></a>복구 후 데이터베이스 구성
지리적 복원을 사용하여 가동 중단에서 복구하는 경우 일반적인 응용 프로그램 함수를 다시 시작할 수 있도록 새 데이터베이스에 대한 연결이 제대로 구성되었는지 확인해야 합니다. 복구된 데이터베이스 프로덕션을 준비하는 작업의 검사 목록입니다.

### <a name="update-connection-strings"></a>연결 문자열 업데이트
복구된 데이터베이스가 다른 서버에 상주하기 때문에 해당 서버를 가리키도록 응용 프로그램의 연결 문자열을 업데이트해야 합니다.

연결 문자열을 변경하는 방법에 대한 자세한 내용은 [연결 라이브러리](sql-database-libraries.md)에 대한 적절한 개발 언어를 참조하세요.

### <a name="configure-firewall-rules"></a>방화벽 규칙 구성
서버 및 데이터베이스에서 구성된 방화벽 규칙이 주 데이터베이스와 주 서버에서 구성된 방화벽 규칙과 일치하는지 확인해야 합니다. 자세한 내용은 [방법: 방화벽 설정 구성(Azure SQL Database)](sql-database-configure-firewall-settings.md)을 참조하세요.

### <a name="configure-logins-and-database-users"></a>로그인 및 데이터베이스 사용자 구성
응용 프로그램에서 사용하는 모든 로그인이 복구된 데이터베이스를 호스팅하는 서버에 존재하도록 해야 합니다. 자세한 내용은 [지역에서 복제를 위한 보안 구성](sql-database-geo-replication-security-config.md)을 참조하세요.

> [!NOTE]
> 재해 복구 훈련 동안 서버 방화벽 규칙 및 로그인(및 해당 사용 권한)을 구성하고 테스트해야 합니다. 이러한 서버 수준 개체 및 해당 구성을 중단 도중에는 사용하지 못할 수 있습니다.
> 
> 

### <a name="setup-telemetry-alerts"></a>원격 분석 경고 설정
기존 경고 규칙 설정을 업데이트하여 복구된 데이터베이스와 다른 서버에 매핑하도록 해야 합니다.

데이터베이스 경고 규칙에 대한 자세한 내용은 [경고 알림 수신](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) 및 [서비스 상태 추적](../monitoring-and-diagnostics/insights-service-health.md)을 참조하세요.

### <a name="enable-auditing"></a>감사 사용
데이터베이스에 액세스하기 위해 감사가 필요한 경우, 데이터베이스 복구 후에 감사 사용을 설정해야 합니다. 자세한 내용은 [Database 감사](sql-database-auditing.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)
* 비즈니스 연속성 설계 및 복구 시나리오에 대해 알아보려면 [연속성 시나리오](sql-database-business-continuity.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)

