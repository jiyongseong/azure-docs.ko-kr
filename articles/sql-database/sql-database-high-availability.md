---
title: 고가용성 - Azure SQL Database 서비스 | Microsoft Docs
description: Azure SQL Database 서비스 고가용성 기능에 대해 알아보세요.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 04/24/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: e541513890d357587e5c1e792165123c2beb5d96
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777022"
---
# <a name="high-availability-and-azure-sql-database"></a>고가용성 및 Azure SQL Database
Azure SQL Database PaaS 제품을 출시한 이후, Microsoft는 HA(고가용성)를 서비스에 기본적으로 제공하고, 고객이 HA를 운영하거나 HA에 특별한 논리를 추가하거나 HA 관련 의사 결정을 내릴 필요가 없도록 하겠다고 고객에게 약속한 바 있습니다. Microsoft는 고객에게 SLA를 제공하여 HA 시스템 구성 및 운영에 대한 완전한 제어를 유지 관리합니다. HA SLA는 지역에 있는 SQL 데이터베이스에 적용되며, Microsoft의 합리적인 제어 이외의 요인으로 인해 전체 지역에 장애가 발생하는 경우 보호를 제공하지 않습니다. 이러한 요인으로, 자연 재해, 전쟁, 테러 행위, 폭동, 정부 조치 또는 Microsoft의 데이터 센터 외부(고객 사이트 또는 고객 사이트와 Microsoft 데이터 센터 간 포함)의 네트워크 또는 장치 오류가 있습니다.

HA의 문제 영역을 단순화하기 위해 Microsoft는 다음 가정을 사용합니다.
1.  하드웨어 및 소프트웨어 장애가 불가피함
2.  운영 직원의 실수로 장애가 발생함
3.  계획된 서비스 운영으로 인해 정전 발생 

이러한 개별 이벤트는 자주 일어나지 않지만 클라우드 규모에서는 거의 매주 발생합니다. 

## <a name="fault-tolerant-sql-databases"></a>내결함성 SQL 데이터베이스
고객은 자신의 데이터베이스 복원력에 가장 관심이 있으며 전체적인 SQL Database 서비스의 복원력에는 관심이 덜합니다. “내 데이터베이스”가 다운된 데이터베이스의 0.01%에 속하는 경우 서비스 가동 시간 99.99%는 의미가 없습니다. 모든 데이터베이스에 내결함성이 있어야 하며 결함 완화가 커밋된 트랜잭션의 손실로 이어지지 않아야 합니다. 

데이터의 경우 SQL Database는 직접 연결된 디스크/VHD 기반의 LS(로컬 저장소)와 Azure Premium Storage 페이지 Blob 기반의 RS(원격 저장소)를 모두 사용합니다. 
- 로컬 저장소는 IOPS 요구 사항이 높은 중요 업무용 OLTP 응용 프로그램용으로 설계된 프리미엄 또는 중요 비즈니스용(미리 보기) 데이터베이스 및 탄력적 풀에서 사용됩니다. 
- 원격 저장소는 기본, 표준 및 범용 서비스 계층에 사용되며, 저장소와 컴퓨팅 성능이 독립적으로 확장되어야 하는 예산 지향 비즈니스 작업을 위해 설계되었습니다. 원격 저장소는 데이터베이스 및 로그 파일용 단일 페이지 BLOB을 사용하며, 기본 제공된 저장소 복제 및 장애 조치(failover) 메커니즘을 사용합니다.

두 경우 모두 SQL Database의 복제, 장애 감지 및 장애 조치(failover) 메커니즘이 완전히 자동화되어 사용자의 개입 없이 작동합니다. 이 아키텍처는 커밋된 데이터가 손실되지 않으며 데이터 내구성이 최우선시되도록 설계되었습니다.

주요 이점:
- 고객은 복잡한 하드웨어, 소프트웨어, 운영 체제 또는 가상화 환경을 구성하거나 유지하지 않고도 복제된 데이터베이스를 최대한 활용할 수 있습니다.
- 관계형 데이터베이스의 전체 ACID 속성은 시스템에서 유지 관리합니다.
- 장애 조치(failover)는 커밋된 데이터의 손실 없이 완전히 자동화됩니다.
- 주 복제본에 대한 연결 라우팅은 응용 프로그램 논리가 필요 없는 서비스에 의해 동적으로 관리됩니다.
- 자동화된 중복성의 상위 수준이 추가 요금 없이 제공됩니다.

> [!NOTE]
> 설명된 고가용성 아키텍처는 통지 없이 변경될 수 있습니다. 

## <a name="data-redundancy"></a>데이터 중복

SQL Database의 고가용성 솔루션은 SQL Server의 [Always ON 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 기술을 기반으로 하며, LS 및 RS 데이터베이스 모두에 대해 최소한의 차이로 작동합니다. Always ON 가용성 그룹 기술은 LS 구성에서 지속성에 사용되는 한편, RS에서는 가용성(활성 지역 복제에 의한 낮은 RTO)에 사용됩니다. 

## <a name="local-storage-configuration"></a>로컬 저장소 구성

이 구성에서 각 데이터베이스는 제어 링 내의 MS(관리 서비스)에서 온라인 상태가 됩니다. 하나의 주 복제본과 둘 이상의 보조 복제본(쿼럼 집합)은 같은 데이터 센터 내 세 개의 독립적인 물리적 하위 시스템에 걸쳐 있는 테넌트 링 내에 있습니다. 모든 읽기 및 쓰기는 GW(게이트웨이)에서 주 복제본으로 전송되고, 쓰기는 보조 복제본에 비동기로 복제됩니다. SQL Database는 트랜잭션이 커밋되기 전에 주 복제본 및 하나 이상의 보조 복제본에 데이터가 기록되는 쿼럼 기반 커밋 체계를 사용합니다.

[Service Fabric](../service-fabric/service-fabric-overview.md) 장애 조치(failover) 시스템은 노드 실패 시 자동으로 복제본을 다시 빌드하며, 노드가 시스템에서 떠날 때와 시스템에 추가될 때 쿼럼 집합 멤버 자격을 유지 관리합니다. 계획된 유지 관리는 쿼럼 집합이 최소 복제본 수(일반적으로 2) 미만이 되지 않도록 신중하게 조정됩니다. 이 모델은 프리미엄 및 중요 비즈니스용(미리 보기) 데이터베이스에 적합하지만, 컴퓨팅 및 저장소 구성 요소 모두의 중복성이 필요하므로 비용이 더 많이 듭니다.

## <a name="remote-storage-configuration"></a>원격 저장소 구성

원격 저장소 구성(기본, 표준 및 범용 계층)의 경우, 내구성, 중복성 및 bit-rot 감지에 대해 저장소 시스템 기능을 사용하여 원격 Blob 저장소에서 정확히 하나의 복사본이 유지 관리됩니다. 

고가용성 아키텍처는 다음 다이어그램에 설명되어 있습니다.
 
![고가용성 아키텍처](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>장애 검색 및 복구 
대규모 분산 시스템에는 장애를 안정적으로 빠르게, 그리고 고객에게 최대한 가깝게 감지할 수 있는 안정적인 장애 감지 시스템이 필요합니다. SQL Database의 경우 이 시스템은 Azure Service Fabric을 기반으로 합니다. 

주 복제본이 있는 경우 주 복제본에 장애가 발생하여 작업을 계속할 수 없으면 이것이 즉시 명백해집니다. 모든 읽기 및 쓰기가 주 복제본에서 먼저 발생하기 때문입니다. 보조 복제본을 주 복제본 상태로 승격하는 이 프로세스에서는 RTO(복구 시간 목표)가 30초이고 RPO(복구 지점 목표)가 0입니다. 30초 RTO의 영향을 줄이기 위한 모범 사례는 연결 실패 시도에 대해 더 짧은 대기 시간으로 여러 번 연결을 다시 시도하는 것입니다.

보조 복제본이 실패하면 데이터베이스는 예비 없이 최소 쿼럼 집합으로 줄어듭니다. Service Fabric은 주 복제본 장애 이후 발생하는 프로세스와 유사한 재구성 프로세스를 시작하므로, 오류가 영구적인지를 확인하기 위해 잠시 대기한 후 다른 보조 복제본이 만들어집니다. 운영 체제 장애 또는 업그레이드와 같은 일시적 서비스 상태가 발생하는 경우, 실패한 노드가 다시 시작되도록 허용하기 위해 새 복제본이 즉시 빌드되지는 않습니다. 

원격 저장소 구성의 경우 SQL Database는 Always On 기능을 사용하여 업그레이드 중에 데이터베이스를 장애 조치합니다. 이를 수행하기 위해 새 SQL 인스턴스는 계획된 업그레이드 이벤트의 일부로 미리 시작되지 않으며, 원격 저장소에서 데이터베이스 파일을 첨부하고 복구합니다. 프로세스 충돌 또는 기타 계획되지 않은 이벤트의 경우 Windows Fabric은 인스턴스 가용성을 관리하고 마지막 복구 단계로 원격 데이터베이스 파일을 연결합니다.

## <a name="zone-redundant-configuration-preview"></a>영역 중복 구성(미리 보기)

로컬 저장소 구성에 대한 쿼럼 집합 복제본은 기본적으로 동일한 데이터 센터에 만들어집니다. [Azure 가용성 영역](../availability-zones/az-overview.md)이 도입되면 쿼럼 집합의 여러 복제본을 동일한 지역의 서로 다른 가용성 영역에 배치할 수 있습니다. 단일 실패 지점을 제거하기 위해 제어 링은 세 개의 GW(게이트웨이 링)로 여러 영역에 걸쳐 복제됩니다. 특정 게이트웨이 링에 대한 라우팅은 [ATM(Azure Traffic Manager)](../traffic-manager/traffic-manager-overview.md)에서 제어됩니다. 영역 중복 구성을 통해 데이터베이스 중복성을 추가로 만들지 않으므로 프리미엄 또는 중요 비즈니스용(미리 보기) 서비스 계층에서 가용성 영역을 사용하는 경우 추가 비용 없이 사용할 수 있습니다. 영역 중복 데이터베이스를 선택하면 응용 프로그램 논리를 변경하지 않고도 치명적인 데이터 센터 중단을 포함하여 훨씬 더 큰 실패 집합에 탄력적인 프리미엄 또는 중요 비즈니스용(미리 보기) 데이터베이스를 만들 수 있습니다. 기존 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀(미리 보기)을 영역 중복 구성으로 변환할 수도 있습니다.

영역 중복 쿼럼 집합에서는 복제본이 서로 간에 약간 떨어져 있는 서로 다른 데이터 센터에 있기 때문에, 네트워크 대기 시간이 늘어나면 커밋 시간이 늘어나고, 이에 따라 일부 OLTP 작업의 성능에 영향을 줄 수 있습니다. 언제든지 영역 중복 설정을 사용하지 않도록 설정하여 단일 영역 구성으로 돌아갈 수 있습니다. 이 프로세스는 데이터 작업의 크기이며 일반 SLO(서비스 수준 목표) 업데이트와 비슷합니다. 프로세스가 완료되면 데이터베이스 또는 풀이 영역 중복 링에서 단일 영역 링으로 또는 그 반대로 마이그레이션됩니다.

> [!IMPORTANT]
> 영역 중복 데이터베이스 및 탄력적 풀은 프리미엄 및 중요 비즈니스용(미리 보기) 서비스 계층에서만 지원됩니다. 공개 미리 보기로 있는 동안 백업 및 감사 레코드는 RA-GRS 저장소에 저장되므로 전체 영역 중단 시 자동으로 사용되지 않을 수 있습니다. 

다음 다이어그램에서는 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.
 
![고가용성 아키텍처 영역 중복](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>읽기 확장
설명한 대로 프리미엄 및 중요 비즈니스용(미리 보기) 서비스 계층은 단일 영역 및 영역 중복 구성 모두에서 고가용성을 위해 쿼럼 집합 및 AlwaysON 기술을 활용합니다. AlwasyON의 이점 중 하나는 복제본이 항상 트랜잭션 측면에서 일관된 상태에 있다는 점입니다. 복제본이 주 데이터베이스와 동일한 성능 수준을 가지기 때문에 응용 프로그램은 추가 비용 없이 읽기 전용 작업을 제공하기 위해 해당 추가 용량을 활용할 수 있습니다(읽기 확장). 이러한 방식으로 읽기 전용 쿼리는 주 읽기-쓰기 작업에서 격리되고 해당 성능에 영향을 주지 않습니다. 읽기 확장 기능은 분석과 같은 논리적으로 구분된 읽기 전용 작업을 포함하는 응용 프로그램을 위한 것이므로 주 데이터베이스에 연결하지 않고 이 추가 용량을 활용할 수 있습니다. 

특정 데이터베이스에서 읽기 확장 기능을 사용하려면, 데이터베이스를 만들 때 또는 나중에 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet을 호출하는 PowerShell을 사용하거나 [데이터베이스 - 만들기 또는 업데이트](/rest/api/sql/databases/createorupdate) 방법을 사용하는 Azure Resource Manager REST API를 통해 해당 구성을 변경하여 명시적으로 사용하도록 설정해야 합니다.

데이터베이스에 대해 읽기 확장을 사용하도록 설정하면, 응용 프로그램의 연결 문자열에 구성된 `ApplicationIntent` 속성에 따라 해당 데이터베이스에 연결하는 응용 프로그램이 해당 데이터베이스의 읽기-쓰기 복제본 또는 읽기 전용 복제본으로 전달됩니다. `ApplicationIntent` 속성에 대한 자세한 내용은 [응용 프로그램 의도 지정](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)을 참조하세요. 

읽기 확장을 사용할 수 없거나 지원되지 않는 서비스 계층에서 ReadScale 속성을 설정한 경우 모든 연결은 `ApplicationIntent` 속성과 독립적으로 읽기/쓰기 복제본으로 이동됩니다.  

> [!NOTE]
> 표준 또는 범용 데이터베이스에서 읽기 확장을 활성화할 수는 있지만 이러한 데이터는 읽기 전용 세션을 별도의 복제본으로 라우팅하지 않습니다. 표준/범용 및 프리미엄/중요 비즈니스용 계층에서 수직 확장/축소되는 기존 응용 프로그램을 지원하기 위해서입니다.  

읽기 확장 기능은 세션 수준 일관성을 지원합니다. 복제본 비가용성으로 인해 연결 오류가 발생한 후 읽기 전용 세션이 다시 연결되면, 다른 복제본으로 리디렉션될 수 있습니다. 가능성이 낮지만 부실한 데이터 집합이 처리될 수 있습니다. 마찬가지로 응용 프로그램에서 읽기-쓰기 세션을 사용하여 데이터를 쓰고 읽기 전용 세션을 사용하여 즉시 읽는 경우 새 데이터가 즉시 표시되지 않을 수도 있습니다.

## <a name="conclusion"></a>결론
Azure SQL Database는 Azure 플랫폼과 긴밀하게 통합되어 있으며, Azure Storage Blob(데이터 보호용) 및 가용성 영역(높은 내결함성용)에 대한 장애 검색 및 복구를 위한 Service Fabric에 따라 크게 달라집니다. 동시에 Azure SQL Database는 복제 및 장애 조치를 위해 SQL Server 제품군의 Always On 기술을 최대한 활용합니다. 이러한 기술의 조합을 통해 응용 프로그램은 혼합 저장소 모델의 이점을 완전히 실현하고 가장 까다로운 SLA를 지원할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](../availability-zones/az-overview.md) 알아보기
- [Service Fabric](../service-fabric/service-fabric-overview.md) 알아보기
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 알아보기 
