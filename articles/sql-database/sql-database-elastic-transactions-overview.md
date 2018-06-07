---
title: 클라우드 데이터베이스의 분산 트랜잭션
description: Azure SQL Database와 Elastic Database 트랜잭션 개요
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.topic: article
ms.custom: scale out apps
ms.workload: On Demand
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 37de2a6918012707ae171415ac8f043d6f9cb1ae
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="distributed-transactions-across-cloud-databases"></a>클라우드 데이터베이스의 분산 트랜잭션
Azure SQL Database(SQL DB)용 Elastic Database를 사용하면 SQL DB의 여러 데이터베이스에 걸쳐 트랜잭션을 실행할 수 있습니다. SQL DB용 탄력적 데이터베이스 트랜잭션은 ADO .NET을 사용하여 .NET 응용 프로그램에서 사용할 수 있고 [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) 클래스를 사용하여 친숙한 프로그래밍 환경과 통합될 수 있습니다. 라이브러리를 가져오려면 [.NET Framework 4.6.1(웹 설치 관리자)](https://www.microsoft.com/download/details.aspx?id=49981)을 참조하세요.

온-프레미스에서 이러한 시나리오는 Microsoft Distributed Transaction Coordinator(MSDTC) 실행을 필요로 합니다. MSDTC는 Azure의 PaaS(Platform-as-a-Service) 응용 프로그램에서 사용할 수 없기 때문에 분산된 트랜잭션을 조정할 수 있는 기능이 SQL DB와 직접적으로 통합되었습니다. 응용 프로그램은 분산된 트랜잭션을 시작하도록 모든 SQL Database에 연결할 수 있으며, 다음 그림에 표시된 것처럼 데이터베이스 중 하나는 분산된 트랜잭션을 투명하게 조정합니다. 

  ![Elastic Database 트랜잭션을 사용한 Azure SQL Database와 분산 트랜잭션 ][1]

## <a name="common-scenarios"></a>일반적인 시나리오
SQL DB용 Elastic Database 트랜잭션은 여러 개의 다른 SQL Database에 저장된 데이터에 대한 응용 프로그램의 원자성 변경을 가능하게 합니다. 미리 보기는 C# 및 .NET의 클라이언트 쪽 개발 환경에 중점을 둡니다. T-SQL을 사용한 서버 쪽 환경은 차후에 계획되어 있습니다.  
탄력적 데이터베이스 트랜잭션은 다음과 같은 시나리오를 대상으로 합니다.

* Azure의 다중 데이터베이스 응용 프로그램: 이 시나리오의 경우 데이터가 SQL DB의 여러 데이터베이스에 걸쳐 수직으로 분할되어 있고 다른 종류의 데이터가 다른 데이터베이스에 상주합니다. 일부 작업은 둘 이상의 데이터베이스에 보관되는 데이터에 대한 변경을 필요로 합니다. 응용 프로그램은 여러 데이터베이스에 걸쳐 변경을 조정하고 원자성을 보장하기 위해 탄력적 데이터베이스 트랜잭션을 사용합니다.
* Azure의 분할된 데이터베이스 응용 프로그램: 이 시나리오의 경우, 데이터 계층은 [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md) 또는 자체 분할을 사용하여 SQL DB의 여러 데이터베이스에 걸쳐있는 데이터를 수평적으로 분할합니다. 한 가지 주요한 사용 사례는 여러 테넌트에 변경이 미칠 때 분할된 다중 테넌트 응용 프로그램에 원자성 변경을 수행해야 하는 경우입니다. 서로 다른 데이터베이스에 상주하는 하나의 테넌트에서 다른 테넌트로 전송하는 인스턴스를 생각해 볼 수 있습니다. 두 번째 사례는 대규모 테넌트에 필요한 용량을 공급하기 위한 세분화된 분할이며, 일반적으로 원자성 작업이 동일한 테넌트의 여러 데이터베이스에까지 결과적으로 영향을 미쳐야 하는 필요성을 수반합니다. 세 번째 사례는 여러 데이터베이스에 걸쳐 복제되는 참조 데이터에 대한 원자성 업데이트입니다. 이러한 방식의 트랜잭션 처리된, 원자성, 작업은 미리 보기를 사용하여 여러 데이터베이스에 걸쳐 조정될 수 있습니다.
  탄력적 데이터베이스 트랜잭션은 여러 데이터베이스에 걸쳐 트랜잭션 원자성을 보장하기 위해 2단계 커밋을 사용합니다. 이것은 단일 트랜잭션 내에 한 번에 100개 미만의 데이터베이스가 관여되는 트랜잭션에 적합합니다. 이 한도가 강제로 적용되지는 않지만, 한도를 초과하는 경우 탄력적 데이터베이스의 성능 및 성공률이 불리해질 수 있다는 것을 예상해야 합니다.

## <a name="installation-and-migration"></a>설치 및 마이그레이션
SQL DB의 탄력적 데이터베이스 트랜잭션 기능은 .NET 라이브러리 System.Data.dll 및 System.Transactions.dll 업데이트를 통해 제공됩니다. DLL은 원자성을 보장하기 위해 필요한 경우 2단계 커밋이 사용되도록 합니다. 탄력적 데이터베이스 트랜잭션을 사용하여 응용 프로그램 개발을 시작하려면 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 이상 버전을 설치합니다. 이전 버전의 .NET framework를 실행하면, 트랜잭션이 분산 트랜잭션으로 승격하지 못하고 예외가 발생합니다.

설치 후에는 SQL DB 연결과 함께 System.Transactions에 분산 트랜잭션 API를 사용할 수 있습니다. 기존 MSDTC 응용 프로그램에서 이러한 API를 사용하고 있는 경우에는 4.6.1 Framework를 설치한 후에 .NET 4.6에 대한 기존 응용 프로그램을 간단히 다시 빌드합니다. 프로젝트가 .NET 4.6을 대상으로 하는 경우에는, 새 Framework 버전에서 업데이트된 DLL이 자동으로 사용되고 분산 트랜잭션 API 호출이 SQL DB 연결과 함께 성공합니다.

탄력적 데이터베이스 트랜잭션은 MSDTC를 설치할 필요가 없습니다. 대신 탄력적 데이터베이스 트랜잭션은 SQL DB 내에서 SQL DB에 의해 직접적으로 관리됩니다. 분산된 트랜잭션을 SQL DB와 사용하기 위해 MSDTC 배포가 필요하지 않기 때문에 클라우드 시나리오를 상당히 간소화시킵니다. 섹션 4는 탄력적 데이터베이스 트랜잭션 배포 방법 및 필요한 .NET framework를 Azure에 대한 클라우드 응용 프로그램과 함께 보다 자세히 설명합니다.

## <a name="development-experience"></a>개발 환경
### <a name="multi-database-applications"></a>다중 데이터베이스 응용 프로그램
다음 샘플 코드는 .NET System.Transactions와 친숙한 프로그래밍 환경을 사용합니다. TransactionScope 클래스는 .NET에서 앰비언트 트랜잭션을 설정합니다. (“앰비언트 트랜잭션”이란 현재 스레드에 존재하는 트랜잭션입니다.) 모든 연결은 트랜잭션에 참여하는 TransactionScope 내에서 열려 있습니다. 다른 데이터베이스가 참여하면, 트랜잭션은 분산 트랜잭션으로 자동 승격됩니다. 트랜잭션의 결과는 커밋을 나타내기 위해 완료해야 하는 범위를 설정하여 제어됩니다.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>분할된 데이터베이스 응용 프로그램
SQL DB용 탄력적 데이터베이스 트랜잭션은 확장된 데이터 층에 대한 연결을 열기 위해 탄력적 데이터베이스 라이브러리의 OpenConnectionForKey 메서드를 사용할 수 있는 분산 트랜잭션 조정을 지원합니다. 여러 개의 다른 분할 키 값에 걸쳐 변경 사항에 대해 트랜잭션 일관성을 보장해야 하는 경우를 고려해 보겠습니다. 다른 분할 키 값을 호스팅하는 분할에 대한 연결은 OpenConnectionForKey를 사용하여 중개됩니다. 일반적인 상황에서 트랜잭션 보장이 분산 트랜잭션을 요구하도록 하기 위해서 다른 분할에 연결될 수 있습니다. 다음 코드 샘플에서 이 접근 방법을 보여 줍니다. shardmap이라는 매개 변수가 탄력적 데이터베이스 클라이언트 라이브러리의 분할된 데이터베이스 맵을 나타내기 위해 사용된다고 가정합니다.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Azure Cloud Services용 .NET 설치
Azure에서는 .NET 응용 프로그램을 호스트하는 여러 제품을 제공합니다. [Azure App Service, Cloud Services 및 Virtual Machines 비교](../app-service/choose-web-site-cloud-service-vm.md)에서 다양한 제품을 비교할 수 있습니다. 제품의 게스트 OS가 탄력적인 트랜잭션에 필요한 .NET 4.6.1보다 작은 경우 게스트 OS를 4.6.1로 업그레이드해야 합니다. 

Azure App Services의 경우에는 현재 게스트 OS 업그레이드가 지원되지 않습니다. Azure Virtual Machines의 경우 VM에 로그인하여 최신 .NET Framework용 설치 관리자를 실행하기만 하면 됩니다. Azure Cloud Services의 경우 최신 .NET 버전의 설치를 배포의 시작 작업에 포함해야 합니다. 개념 및 단계는 [클라우드 서비스 역할에 .NET 설치](../cloud-services/cloud-services-dotnet-install-dotnet.md)에 문서화되어 있습니다.  

.NET 4.6.1용 설치 관리자는 Azure 클라우드 서비스에서 프로세스를 부트스트랩하는 과정에서 .NET 4.6용 설치 관리자보다 더 많은 임시 저장 공간을 필요로 할 수 있습니다. 성공적인 설치를 위해 다음 예에서처럼 LocalResources 섹션의 ServiceDefinition.csdef 파일과 시작 작업의 환경 설정에서 Azure 클라우드 서비스의 임시 저장 공간을 늘려야 합니다.

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>

## <a name="transactions-across-multiple-servers"></a>여러 서버 간의 트랜잭션
Elastic Database 트랜잭션은 Azure SQL Database의 여러 논리 서버에 걸쳐 지원됩니다. 트랜잭션이 논리 서버 경계를 교차하는 경우 참여하는 서버는 먼저 상호 통신 관계가 설정되어야 합니다. 통신 관계가 설정된 후에는 두 서버 중 하나의 모든 데이터베이스가 다른 서버 데이터베이스와의 탄력적인 트랜잭션에 참여할 수 있습니다. 트랜잭션이 둘 이상의 논리 서버에 걸친 경우 모든 논리 서버 쌍에 대해 통신 관계가 있어야 합니다.

다음 PowerShell cmdlet을 사용하여 탄력적인 데이터베이스 트랜잭션에 대한 서버 간 통신 관계를 관리할 수 있습니다.

* **New-AzureRmSqlServerCommunicationLink**: Azure SQL DB에서 두 개의 논리 서버 간에 새로운 통신 관계를 만들려면 이 cmdlet을 사용합니다. 관계는 대칭적입니다. 즉, 두 서버 모두 상대 서버와의 트랜잭션을 시작할 수 있습니다.
* **Get-AzureRmSqlServerCommunicationLink**: 기존 통신 관계와 해당 속성을 검색하려면 이 cmdlet을 사용합니다.
* **Remove-AzureRmSqlServerCommunicationLink**: 기존 통신 관계와 해당 속성을 제거하려면 이 cmdlet을 사용합니다. 

## <a name="monitoring-transaction-status"></a>트랜잭션 상태 모니터링
SQL DB의 동적 관리 뷰(DMV)를 사용하여 진행 중인 탄력적 데이터베이스 트랜잭션의 상태와 진행률을 모니터링합니다. 트랜잭션과 관련된 모든 DMV는 SQL DB의 분산 트랜잭션과 관련이 있습니다. 해당 DMV 목록은 [트랜잭션 관련 동적 관리 뷰 및 함수(Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx)에서 찾을 수 있습니다.

다음 DMV는 특히 유용합니다.

* **sys.dm\_tran\_active\_transactions**: 현재 활성 트랜잭션과 그 상태를 나열합니다. UOW(작업 단위) 열은 동일한 분산 트랜잭션에 속하는 다른 자식 트랜잭션을 식별할 수 있습니다. 동일한 분산 트랜잭션 내의 모든 트랜잭션은 동일한 UOW 값을 갖습니다. 자세한 내용은 [DMV 설명서](https://msdn.microsoft.com/library/ms174302.aspx)를 참조하세요.
* **sys.dm\_tran\_database\_transactions**: 로그에 트랜잭션 배치 같은 트랜잭션에 대한 추가적인 정보를 제공합니다. 자세한 내용은 [DMV 설명서](https://msdn.microsoft.com/library/ms186957.aspx)를 참조하세요.
* **sys.dm\_tran\_locks**: 진행 중인 트랜잭션에 의해 현재 유지되는 잠금에 대한 정보를 제공합니다. 자세한 내용은 [DMV 설명서](https://msdn.microsoft.com/library/ms190345.aspx)를 참조하세요.

## <a name="limitations"></a>제한 사항
다음 제한 사항은 SQL DB의 탄력적 데이터베이스 트랜잭션에 현재 적용됩니다.

* SQL DB의 데이터베이스 간 트랜잭션만 지원됩니다. 다른 [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) 리소스 공급자 및 SQL DB 이외의 데이터베이스는 탄력적 데이터베이스 트랜잭션에 참여할 수 없습니다. Elastic Database 트랜잭션은 온-프레미스 SQL Server와 Azure SQL Database 사이에서 확장될 수 없습니다. 온-프레미스 분산 트랜잭션을 위해서는 MSDTC를 계속 사용하세요. 
* .NET 응용 프로그램의 클라이언트 조정 트랜잭션만 지원됩니다. BEGIN DISTRIBUTED TRANSACTION 같은 T-SQL에 대한 서버 쪽 지원이 계획되어 있지만 아직 제공되지 않습니다. 
* WCF 서비스에서 트랜잭션은 지원되지 않습니다. 예를 들어 트랜잭션을 실행하는 WCF 서비스 메서드가 있습니다. 트랜잭션 범위 내로 호출을 묶으면 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)으로 실패합니다.

## <a name="next-steps"></a>다음 단계
의문 사항이 있으면 [SQL Database 포럼](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)에 문의하고, 기능에 대한 요청이 있는 경우 해당 기능을 [SQL Database 사용자 의견 포럼](https://feedback.azure.com/forums/217321-sql-database/)에 추가하세요.

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



