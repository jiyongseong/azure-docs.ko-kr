---
title: Azure SQL Database 관리되는 인스턴스 개요 | Microsoft Azure
description: 이 항목에서는 Azure SQL Database 관리되는 인스턴스에 대해 설명하고 작동 원리 및 Azure SQL Database의 단일 데이터베이스와 다른 점을 설명합니다.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: 85a0157751a1c26fb7f37152d7c12f56b1c423d1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32193287"
---
# <a name="what-is-a-managed-instance-preview"></a>관리되는 인스턴스(미리 보기)란?

Azure SQL Database 관리되는 인스턴스(미리 보기)는 Azure SQL Database의 새 기능으로 SQL Server 온-프레미스(Enterprise Edition)와 거의 100% 호환되며, 일반적인 보안 문제를 해결하는 기본 [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 구현과 온-프레미스 SQL Server 고객이 편리하게 사용할 수 있는 [비즈니스 모델](https://azure.microsoft.com/pricing/details/sql-database/)을 제공합니다. 관리되는 인스턴스를 사용하면 기존 SQL Server 고객은 응용 프로그램 및 데이터베이스 변경을 최소화하고 온-프레미스 응용 프로그램을 클라우드로 이동할 수 있습니다. 뿐만 아니라 관리되는 인스턴스는 관리 오버헤드와 TCO를 대폭 줄이는 모든 PaaS 기능(자동 패치 및 버전 업데이트, 백업, 고가용성)을 유지합니다.

> [!IMPORTANT]
> 관리되는 인스턴스를 현재 사용할 수 있는 지역 목록은 [Azure SQL Database 관리되는 인스턴스를 사용해 완벽히 관리되는 서비스로 데이터베이스 마이그레이션](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/)을 참조하세요.
 
다음 다이어그램은 관리되는 인스턴스의 주요 기능을 설명합니다.

![주요 기능](./media/sql-database-managed-instance/key-features.png)

관리되는 인스턴스는 다음과 같은 시나리오에 권장하는 플랫폼입니다. 

- 최소한의 설계 변경을 통해 응용 프로그램을 완전히 관리되는 서비스로 마이그레이션하려는 SQL Server 온-프레미스/IaaS 고객.
- SQL 데이터베이스를 사용 중이고, 고객이 클라우드로 마이그레이션할 수 있도록 지원하여 확고한 경쟁 우위를 확보하거나 글로벌 시장을 넓히려는 ISV. 

관리되는 인스턴스의 목표는 일반 공급이 시작될 때까지 단계별 릴리스 계획을 통해 최신 온-프레미스 SQL Server 버전과 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다. 

다음 표에는 SQL IaaS, Azure SQL Database 및 SQL Database 관리되는 인스턴스의 주요 차이점과 사용 시나리오가 설명되어 있습니다.

| | 사용 시나리오 | 
| --- | --- | 
|SQL Database 관리되는 인스턴스 |온-프레미스나 IaaS, 자체 제작 또는 ISV에서 제공하는 대량의 앱을 최소한의 마이그레이션 작업을 통해 마이그레이션하려는 고객에게는 관리되는 인스턴스를 제안합니다. Azure에서 완전 자동화 [DMS(Database Migration Service)](/sql/dma/dma-overview)를 사용하는 고객은 기본 VNET 지원을 통해 온-프레미스 SQL Server를 SQL Server 온-프레미스와 호환되고 고객의 인스턴스를 완벽하게 격리하는 관리되는 인스턴스로 이동할 수 있습니다.  Software Assurance와 함께 사용하면 Azure SQL Database 관리되는 인스턴스에서 [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 사용하여 기존 라이선스를 할인된 가격에 교환할 수 있습니다.  SQL Database 관리되는 인스턴스는 클라우드에서 높은 보안과 풍부한 프로그래밍 기능 영역이 필요한 SQL Server 인스턴스를 위한 최상의 마이그레이션 대상입니다. |
|Azure SQL Database(단일 또는 풀) |**탄력적 풀**: 새 SaaS 다중 테넌트 응용 프로그램을 개발 중이거나 기존 온-프레미스 앱을 의도적으로 SaaS 다중 테넌트 앱으로 전환하려는 고객에게는 탄력적 풀을 제안합니다. 이 모델의 장점은 다음과 같습니다. <br><ul><li>비즈니스 모델을 라이선스 판매에서 서비스 구독 판매로 전환(ISV의 경우)</li></ul><ul><li>간편하고 완벽한 테넌트 격리</li></ul><ul><li>간단한 데이터베이스 중심 프로그래밍 모델</li></ul><ul><li>하드 한도에 도달하지 않고 규모 확장할 수 있는 가능성</li></ul>**단일 데이터베이스**: SaaS 다중 테넌트 이외의 새 앱을 개발 중이고 워크로드가 안정적이고 예측 가능한 고객에게는 단일 데이터베이스를 제안합니다. 이 모델의 장점은 다음과 같습니다.<ul><li>간단한 데이터베이스 중심 프로그래밍 모델</li></ul>  <ul><li>각 데이터베이스의 예측 가능한 성능</li></ul>|
|SQL IaaS 가상 머신|운영 체제 또는 데이터베이스 서버를 사용자 지정해야 하는 고객 그리고 타사 앱을 SQL Server와 함께(같은 VM에서) 실행해야 하는 고객에게는 최적의 솔루션으로 SQL VM/IaaS를 제안합니다.|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>관리되는 인스턴스를 프로그래밍 방식으로 식별하는 방법

다음 표는 Transact SQL을 통해 액세스할 수 있으며 응용 프로그램이 관리되는 인스턴스와 함께 작동 중인지 감지하고 중요한 속성을 검색하는 데 사용할 수 있는 여러 속성을 보여줍니다.

|자산|값|주석|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure(RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|이 값은 SQL Database와 같습니다.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|이 값은 SQL Database와 같습니다.|
|`SERVERPROPERTY('EngineEdition')`|8|이 값은 관리되는 인스턴스를 고유하게 식별합니다.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|<instanceName>.<dnsPrefix>.database.windows.net 형식의 전체 인스턴스 DNS 이름. 여기서 <instanceName>은 고객이 제공한 이름이고, <dnsPrefix>는 전역 DNS 이름의 고유성을 보장하기 위해 이름에서 자동으로 생성되는 부분(예: "wcus17662feb9ce98")입니다.|예: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>관리되는 인스턴스의 주요 특징 및 기능 

> [!IMPORTANT]
> 관리되는 인스턴스에는 온라인 작업, 자동 계획 수정 및 기타 엔터프라이즈 성능 향상을 비롯한 SQL Server 최신 버전의 모든 기능이 실행됩니다. 

| **PaaS의 이점** | **비즈니스 연속성** |
| --- | --- |
|하드웨어를 구입하고 관리할 필요가 없음 <br>기본 인프라 관리를 위한 오버헤드가 없음 <br>신속한 프로비전 및 서비스 크기 조정 <br>자동 패치 적용 및 버전 업그레이드 <br>다른 PaaS 데이터 서비스와 통합 |99.99% 작동 시간 SLA  <br>기본적으로 고가용성 제공 <br>자동 백업으로 데이터 보호 <br>고객이 구성 가능한 백업 보존 기간(공개 미리 보기에서는 7일로 고정) <br>사용자가 시작하는 백업 <br>지정 시간 데이터베이스 복원 기능 |
|**보안 및 규정 준수** | **관리**|
|격리된 환경(VNet 통합, 단일 테넌트 서비스, 전용 계산 및 저장소) <br>전송 중 데이터 암호화 <br>Azure AD 인증, Single Sign-On 지원 <br>Azure SQL 데이터베이스와 동일한 표준 준수 <br>SQL 감사 <br>위협 감지 |서비스 프로비전 및 크기 조정을 자동화하는 Azure Resource Manager API <br>수동 서비스 프로비전 및 크기 조정을 위한 Azure Portal 기능 <br>데이터 마이그레이션 서비스 

![Single Sign-On](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model-preview"></a>vCore 기반 구매 모델(미리 보기)

vCore 기반 구매 모델(미리 보기)은 유연성, 제어, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 통해 워크로드 요구 사항에 따라 계산, 메모리 및 저장소의 크기를 조정할 수 있습니다. 또한 vCore 모델은 [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 통해 비용을 최대 30%까지 절약할 수 있습니다.

가상 코어는 하드웨어 세대 중에서 선택할 수 있는 옵션과 함께 제공되는 논리 CPU를 나타냅니다.
- 4세대 논리 CPU는 Intel E5-2673 v3(Haswell) 2.4GHz 프로세서를 기반으로 하며,
- 5세대 논리 CPU는 Intel E5-2673 v4(Broadwell) 2.3GHz 프로세서를 기반으로 합니다.

다음 표는 계산, 메모리, 저장소 및 I/O 리소스의 최적 구성을 선택하는 방법을 이해하는 데 도움이 됩니다.

||4세대|5세대|
|----|------|-----|
|하드웨어|Intel E5-2673 v3(Haswell) 2.4 GHz 프로세서, 연결형 SSD, vCore = 1PP(물리적 코어)|Intel E5-2673 v4(Broadwell) 2.3 GHz 프로세서, 고속 eNVM SSD, vCore = 1LP(하이퍼스레드)|
|성능 수준|8, 16, 24개 vCore|8, 16, 24, 32, 40개 vCore|
|메모리|vCore당 7GB|vCore당 5.5GB|
||||

## <a name="managed-instance-service-tier"></a>관리되는 인스턴스 서비스 계층

관리되는 인스턴스는 처음에는 일반적인 가용성 및 공통 IO 대기 시간 요구 사항을 가진 응용 프로그램을 위해 설계된 단일 서비스 계층(범용)에서 사용할 수 있습니다.

다음 목록에서는 범용 서비스 계층의 주요 특징을 설명합니다. 

- 일반적인 성능과 HA 요구 사항을 가진 대부분의 비즈니스 응용 프로그램에 적합한 디자인 
- 고성능 Azure Premium 저장소(8TB) 
- 100개 데이터베이스/인스턴스 

이 계층에서는 저장소 및 계산 용량을 독립적으로 선택할 수 있습니다. 

다음 다이어그램은 이 서비스 계층의 활성 계산 및 중복 노드를 보여줍니다.
 
![범용 서비스 계층](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

다음 표에서는 범용 서비스 계층의 주요 특징을 설명합니다.

|기능 | 설명|
|---|---|
| vCore 수* | 8, 16, 24개(4세대)<br>8, 16, 24, 32, 40개(5세대)|
| SQL Server 버전/빌드 | SQL Server(최신 버전 사용 가능) |
| 최소 저장소 크기 | 32GB |
| 최대 저장소 크기 | 8 TB |
| 데이터베이스당 최대 저장소 | 8 TB |
| 예상 저장소 IOPS | 데이터 파일당 500-7500 IOPS(데이터 파일에 따라 다름). [Premium Storage](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) 보기 |
| 데이터베이스당 데이터 파일(행) 수 | 여러 접두사 | 
| 데이터베이스당 로그 파일(로그) 수 | 1 | 
| 관리되는 자동 백업 | 예 |
| HA | 원격 저장소 및 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) 기반 |
| 기본 제공 인스턴스/데이터베이스 모니터링 및 메트릭 | 예 |
| 자동 소프트웨어 패치 | 예 |
| VNet - Azure Resource Manager 배포 | 예 |
| VNet - 클래식 배포 모델 | 아니오 |
| 포털 지원 | 예|
|||

\* 가상 코어는 하드웨어 세대를 선택할 수 있는 옵션이 함께 제공되는 논리 CPU를 나타냅니다. Gen 4 논리 CPU는 Intel E5-2673 v3(Haswell) 2.4GHz 프로세서를 기반으로 하며, Gen 5 논리 CPU는 Intel E5-2673 v4(Broadwell) 2.3GHz 프로세서를 기반으로 합니다. 

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수 

### <a name="managed-instance-security-isolation"></a>관리되는 인스턴스 보안 격리 

관리되는 인스턴스는 Azure 클라우드의 다른 테넌트와 추가로 격리되는 보안을 제공합니다. 보안 격리에는 다음이 포함됩니다. 

- Azure ExpressRoute 또는 VPN Gateway를 사용하여 [네이티브 가상 네트워크 구현](sql-database-managed-instance-vnet-configuration.md) 및 온-프레미스 환경에 연결 
- SQL 엔드포인트가 개인 IP 주소를 통해서만 노출되므로 개인 Azure 또는 하이브리드 네트워크에서 안전하게 연결
- 전용 기본 인프라(계산, 저장소)를 제공하는 단일 테넌트

다음 다이어그램은 격리 설계를 설명합니다. 

![고가용성](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>규정 준수 및 보안에 대한 감사 

[관리되는 인스턴스 감사](sql-database-managed-instance-auditing.md)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록합니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다. 

### <a name="data-encryption-in-motion"></a>진행 중인 데이터 암호화 

관리되는 인스턴스는 전송 계층 보안을 사용하여 이동 중인 데이터를 암호화함으로써 데이터를 보호합니다.

전송 계층 보안 외에도, SQL Database 관리되는 인스턴스는 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용하여 전송 중인 데이터, 미사용 데이터, 쿼리를 처리 중인 데이터를 보호하는 기능을 제공합니다. Always Encrypted는 중요한 데이터의 도용을 비롯한 위반에 대해 최상의 데이터 보안을 제공하는 업계 최고의 기능입니다. 예를 들어 Always Encrypted를 사용하여 쿼리를 처리하는 동안에도 신용 카드 번호가 데이터베이스에 암호화되어 저장됩니다. 또한 해당 데이터를 처리해야 하는 권한이 부여된 직원 또는 응용 프로그램에 의해 사용 시점에 암호를 해독할 수 있습니다. 

### <a name="dynamic-data-masking"></a>동적 데이터 마스킹 

SQL Database [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)에서는 권한이 없는 사용자에 대해 중요한 데이터를 마스킹해 표시함으로써 데이터 노출을 제한합니다. 동적 데이터 마스킹을 수행하면 응용 프로그램 계층에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 동적 데이터 마스킹은 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다. 이때 데이터베이스의 데이터 자체는 변경되지 않습니다. 

### <a name="row-level-security"></a>행 수준 보안 

[행 수준 보안](/sql/relational-databases/security/row-level-security)을 통해 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. RLS(행 수준 보안)는 응용 프로그램의 보안 설계 및 코딩을 간소화합니다. RLS를 사용하면 데이터 행 액세스에 대한 제한을 구현할 수 있습니다. 예를 들어 작업자가 소속 부서와 관련된 데이터 행에만 액세스하게 하거나 데이터 액세스를 관련 데이터로 제한할 수 있습니다. 

### <a name="threat-detection"></a>위협 감지 

[관리되는 인스턴스 위협 검색](sql-database-managed-instance-threat-detection.md)은 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 감지하는 서비스에 내장된 추가적인 보안 인텔리전스 계층을 제공하여 [관리되는 인스턴스 감사](sql-database-managed-instance-auditing.md) 기능을 보완합니다. 의심스러운 활동, 잠재적 취약성 및 SQL 삽입 공격은 물론 비정상적인 데이터베이스 액세스 패턴에 대해 경고합니다. 위협 감지 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에서 볼 수 있으며 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 Multi-Factor Authentication 

SQL Database를 사용하면 [Azure Active Directory 통합](sql-database-aad-authentication.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 MFA([Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md))을 제공하여 SSO(Single Sign-On) 프로세스를 지원하는 동시에 데이터 및 응용 프로그램 보안을 향상시킵니다. 

### <a name="authentication"></a>인증 
SQL 데이터베이스 인증은 사용자가 데이터베이스에 연결할 때 자신의 ID를 증명하는 방법을 나타냅니다. SQL Database는 두 가지 인증 유형을 지원합니다.  

- SQL 인증은 사용자 이름과 암호를 사용합니다.
- Azure Active Directory 인증은 Azure Active Directory에서 관리하는 ID를 사용하고 관리되고 통합된 도메인을 지원합니다. 

### <a name="authorization"></a>권한 부여

권한 부여는 사용자가 Azure SQL Database에서 수행할 수 있는 작업을 나타내며, 사용자 계정의 데이터베이스 역할 멤버 자격과 개체 수준 권한으로 제어합니다. 관리되는 인스턴스는 SQL Server 2017과 동일한 권한 부여 기능을 갖고 있습니다. 

## <a name="database-migration"></a>데이터베이스 마이그레이션 

관리되는 인스턴스는 온-프레미스 또는 IaaS 데이터베이스 구현에서 대량의 데이터베이스를 마이그레이션하는 사용자 시나리오를 대상으로 합니다. 관리되는 인스턴스는 여러 데이터베이스 마이그레이션 옵션을 지원합니다. 

### <a name="data-migration-service"></a>데이터 마이그레이션 서비스

Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure로 이동하는 데 필요한 작업을 간소화합니다. 공개 미리 보기의 배포 옵션으로는 Azure VM의 Azure SQL Database, 관리되는 인스턴스, SQL Server가 있습니다. [DMS를 사용하여 온-프레미스 데이터베이스를 관리되는 인스턴스로 마이그레이션하는 방법](https://aka.ms/migratetoMIusingDMS)을 참조하세요. 

### <a name="backup-and-restore"></a>Backup 및 복원  

마이그레이션 방식에서는 Azure blob 저장소에 SQL을 백업합니다. Azure 저장소 blob에 저장된 백업을 관리되는 인스턴스에 바로 복원할 수 있습니다. 기존 SQL 데이터베이스를 관리되는 인스턴스로 복원하려면 다음을 수행하면 됩니다.

- [DMS(데이터 마이그레이션 서비스)](/sql/dma/dma-overview)를 사용합니다. 데이터베이스 백업 파일에서 복원하는 방법을 보여 주는 자습서는 [DMS(Azure Database Migration Service)를 사용하여 관리되는 인스턴스로 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
- [T-SQL RESTORE 명령](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)을 사용합니다. 
  - Wide World Importers 표준 데이터베이스 백업 파일을 복원하는 방법을 보여 주는 자습서는 [관리되는 인스턴스에 백업 파일 복원](sql-database-managed-instance-restore-from-backup-tutorial.md)을 참조하세요. 이 자습서에서는 백업 파일을 Azure Blob 저장소에 업로드하고 SAS(보안 공유 액세스 서명) 키를 사용하여 보호해야 한다는 것을 보여 줍니다.
  - URL에서 복원하는 방법에 대한 자세한 내용은 [URL에서 네이티브 복원](sql-database-managed-instance-migrate.md#native-restore-from-url)을 참조하세요.
- [BACPAC 파일에서 가져오기](sql-database-import.md)

## <a name="sql-features-supported"></a>지원되는 SQL 기능 

관리되는 인스턴스의 목표는 서비스의 일반 공급이 시작될 때까지 단계별로 제공되는 온-프레미스 SQL Server와 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다. 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
 
관리되는 인스턴스는 SQL 2008 데이터베이스와 호환됩니다. SQL 2005 데이터베이스 서버에서 직접 마이그레이션할 수 있으며, 마이그레이션된 SQL 2005 데이터베이스의 호환성 수준은 SQL 2008로 업데이트됩니다. 
 
다음 다이어그램은 관리되는 인스턴스의 노출 영역 호환성을 설명합니다.  

![마이그레이션](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>SQL Server 온-프레미스와 관리되는 인스턴스의 주요 차이점 

관리되는 인스턴스는 클라우드에서 항상 최신 상태로 유지되는 이점이 있습니다. 즉, 온-프레미스 SQL Server의 일부 기능이 사용되지 않거나 사용 중지되거나 대체될 수 있습니다. 특정 기능이 약간 다른 방식으로 작동하거나 사용자에게 전체 제어 권한이 없는 환경에서 서비스가 실행되면 이 사실을 도구에서 인식해야 하는 경우가 있습니다. 

- 고가용성은 기본적으로 제공되며 미리 구성되어 있습니다. Always On 고가용성 기능은 SQL IaaS 구현과 동일한 방식으로 노출되지 않습니다. 
- 자동 백업 및 특정 시점 복원. 고객은 자동 백업 체인을 방해하지 않는 `copy-only` 백업을 시작할 수 있습니다. 
- 관리되는 인스턴스는 전체 실제 경로 지정을 허용하지 않으므로 해당하는 모든 시나리오를 약간씩 다르게 지원해야 합니다. RESTORE DB는 WITH MOVE를 지원하지 않고, CREATE DB는 실제 경로를 허용하지 않고, BULK INSERT는 Azure Blob에서만 작동합니다. 
- 관리되는 인스턴스는 Windows 인증의 클라우드 대안으로 [Azure AD 인증](sql-database-aad-authentication.md)을 지원합니다. 
- 관리되는 인스턴스는 메모리 내 OLTP 개체가 포함된 데이터베이스의 XTP 파일 그룹 및 파일을 자동으로 관리합니다.
 
### <a name="managed-instance-administration-features"></a>관리되는 인스턴스 관리 기능  

관리되는 인스턴스는 시스템 관리자가 비즈니스에 가장 중요한 문제에만 집중할 수 있게 해줍니다. 많은 시스템 관리자/DBA 활동을 수행할 필요가 없거나 아주 간단합니다. OS/RDBMS 설치 및 패치, 동적 인스턴스 크기 조정 및 구성, 백업, 데이터베이스 복제(시스템 데이터베이스 포함), 고가용성 구성, 상태 및 성능 모니터링 데이터 스트림 구성 등을 예로 들 수 있습니다. 

> [!IMPORTANT]
> 지원되는 기능, 부분적으로 지원되는 기능 및 지원되지 않는 기능 목록은 [SQL Database 기능](sql-database-features.md)을 참조하세요. 관리되는 인스턴스와 SQL Server의 T-SQL 차이점 목록은 SQL Server의 [관리되는 인스턴스 T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.
 
## <a name="next-steps"></a>다음 단계

- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [관리되는 인스턴스 VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.
- 백업 파일에서 관리되는 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 자습서는 [관리되는 인스턴스 만들기](sql-database-managed-instance-create-tutorial-portal.md)를 참조하세요.
- Azure DMS(Database Migration Service)를 사용하여 마이그레이션하는 방법에 대한 자습서는 [DMS를 사용하여 관리되는 인스턴스 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
