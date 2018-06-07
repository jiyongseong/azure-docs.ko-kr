---
title: Entity Framework와 함께 탄력적 데이터베이스 클라이언트 라이브러리 사용 | Microsoft Docs
description: 데이터베이스 코딩을 위해 Elastic Database 클라이언트 라이브러리 및 Entity Framework 사용
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: fba872b01d4ddf0bb4e6aa8d0217042617688b8a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="elastic-database-client-library-with-entity-framework"></a>엔터티 프레임 작업과 함께 Elastic Database 클라이언트 라이브러리
이 문서에서는 [Elastic Database 도구](sql-database-elastic-scale-introduction.md)의 기능을 통합하는 데 필요한 Entity Framework 응용 프로그램의 변경 내용을 보여줍니다. 여기서는 Entity Framework **Code First** 접근 방식으로 [공유된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md) 및 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 작성하는 데 집중합니다. 이 문서 전체에서는 EF용 [Code First – New Database](http://msdn.microsoft.com/data/jj193542.aspx) 자습서를 실행 예제로 사용합니다. 이 문서와 함께 제공되는 샘플 코드는 Visual Studio 코드 샘플에 포함된 탄력적 데이터베이스 도구의 샘플 세트 일부입니다.

## <a name="downloading-and-running-the-sample-code"></a>샘플 코드 다운로드 및 실행
이 기사의 코드를 다운로드하려면:

* Visual Studio 2012 이상이 필요합니다. 
* MSDN에서 [Elastic DB Tools for Azure SQL - Entity Framework Integration sample](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba)(Azure SQL의 탄력적 DB 도구 - Entity Framework 통합 샘플)을 다운로드합니다. 선택한 위치에 샘플의 압축을 풉니다.
* Visual Studio를 시작합니다. 
* Visual Studio에서 [파일] -> [프로젝트/솔루션 열기]를 선택합니다. 
* **프로젝트 열기** 대화 상자에서 다운로드한 샘플로 이동하고 **EntityFrameworkCodeFirst.sln**을 선택하여 샘플을 엽니다. 

샘플을 실행하려면 Azure SQL Database에서 3개의 빈 데이터베이스를 만들어야 합니다.

* 분할된 데이터베이스 맵 관리자 데이터베이스
* 분할 1 데이터베이스
* 분할 2 데이터베이스

이러한 데이터베이스를 만든 후 Azure SQL DB 서버 이름, 데이터베이스 이름 및 데이터베이스에 연결하는 자격 증명을 **Program.cs**의 자리 표시자에 입력합니다. Visual Studio에서 솔루션을 빌드합니다. Visual Studio는 탄력적 데이터베이스 클라이언트 라이브러리, Entity Framework 및 일시적인 오류 처리를 위해 필요한 NuGet 패키지를 빌드 프로세스 중에 다운로드합니다. 사용 중인 솔루션에 대해 NuGet 패키지를 복원할 수 있는지 확인합니다. Visual Studio 솔루션 탐색기에서 솔루션 파일을 마우스 오른쪽 단추로 클릭하여 이 설정을 사용할 수 있습니다. 

## <a name="entity-framework-workflows"></a>Entity Framework 워크플로
Entity Framework 개발자는 다음 4개의 워크플로 중 하나를 사용하여 응용 프로그램을 구축하고 응용 프로그램 개체의 지속성을 확인합니다. 

* **Code First (New Database)**: EF 개발자가 응용 프로그램 코드에서 모델을 만들면 EF가 해당 모델에서 데이터베이스를 생성합니다. 
* **Code First (Existing Database)**: EF가 기존 데이터베이스에서 모델에 대한 응용 프로그램 코드를 생성하도록 지정합니다.
* **Model First**: 개발자가 EF 디자이너에서 모델을 만들면 EF가 해당 모델에서 데이터베이스를 만듭니다.
* **Database First**: 개발자가 EF 도구를 사용하여 기존 데이터베이스에서 모델을 유추합니다. 

이러한 모든 접근 방법은 DbContext 클래스를 사용하여 응용 프로그램용 데이터베이스 연결 및 데이터베이스 스키마를 쉽게 이해할 수 있게 관리합니다. DbContext 기본 클래스의 여러 생성자는 연결 생성, 데이터베이스 부트스트래핑 및 스키마 생성에 대해 다양한 수준의 제어를 허용합니다. EF에서 제공하는 데이터베이스 연결 관리가 탄력적 데이터베이스 클라이언트 라이브러리에서 제공하는 데이터 종속 라우팅 인터페이스의 연결 관리 기능과 교차한다는 사실에서 주로 문제가 발생합니다. 

## <a name="elastic-database-tools-assumptions"></a>탄력적 데이터베이스 도구 가정
용어 정의는 [Elastic Database 도구 용어집](sql-database-elastic-scale-glossary.md)을 참조하세요.

탄력적 데이터베이스 클라이언트 라이브러리를 사용하여 shardlet이라고 하는 응용 프로그램 데이터의 파티션을 정의합니다. Shardlet은 분할 키로 식별되며 특정 데이터베이스에 매핑됩니다. 응용 프로그램은 필요한 만큼 많은 데이터베이스를 포함하고 shardlet을 배포하여 현재 비즈니스 요구 사항에 따른 용량이나 성능을 충분히 제공할 수 있습니다. 탄력적 데이터베이스 클라이언트 API에서 제공하는 분할된 데이터베이스 맵을 통해 데이터베이스에 대한 분할 키 값의 매핑이 저장됩니다. 이 기능을 **분할된 데이터베이스 맵 관리**또는 줄여서 SMM이라고 합니다. 분할된 데이터베이스 맵은 분할 키를 전송하는 요청에 대한 데이터베이스 연결의 브로커 역할도 합니다. 이 기능은 **데이터 종속 라우팅**이라고 알려져 있습니다. 

분할된 데이터베이스 맵 관리자는 동시 shardlet 관리 작업(예: 분할된 데이터베이스 간에 데이터 재배치)이 수행될 때 발생할 수 있는 shardlet 데이터에 대한 불일치한 뷰가 생성되지 않도록 합니다. 이를 위해, 분할된 데이터베이스 맵은 클라이언트 라이브러리에 의해 관리되고 기존 응용 프로그램용 데이터베이스 연결을 조정합니다. 따라서 분할된 데이터베이스 관리 작업에서 연결이 생성된 shardlet 영향을 줄 수 있는 경우 분할된 데이터베이스 맵 기능이 자동으로 데이터베이스 연결을 중지합니다. 이 접근 방식은 EF의 기능 중 일부(예: 기존 연결에서 새 연결을 생성하여 데이터베이스 존재 여부 확인)와 통합해야 합니다. 일반적으로 표준 DbContext 생성자는 EF 작업을 위해 안전하게 복제할 수 있는 닫힌 데이터베이스 연결에 대해서만 안정적으로 작동합니다. 탄력적 데이터베이스의 설계 원칙에서만 열린 연결을 조정합니다. 클라이언트 라이브러리를 통해 조정된 연결을 EF DbContext로 전달하기 전에 닫으면 이 문제를 해결할 수 있습니다. 그러나 연결을 닫고 EF를 사용하여 다시 열면 라이브러리를 통해 수행된 유효성 검사 및 일관성 검사가 사라집니다. 그러나 EF의 마이그레이션 기능은 이러한 연결을 사용하여 응용 프로그램 작동에 영향을 주지 않는 방식으로 기본 데이터베이스 스키마를 관리합니다. 동일한 응용 프로그램에서 탄력적 데이터베이스 클라이언트 라이브러리 및 EF의 이러한 기능을 모두 유지하고 결합합니다. 다음 섹션에서는 이러한 속성 및 요구 사항을 자세히 설명합니다. 

## <a name="requirements"></a>요구 사항
탄력적 데이터베이스 클라이언트 라이브러리와 엔터티 프레임 워크 API를 모두 사용할 때는 다음 속성을 유지해야 합니다. 

* **Scale-out**:응용 프로그램의 용량 요구 사항에 따라 분할된 응용 프로그램의 데이터 계층에서 데이터베이스를 필요한 만큼 추가하거나 제거합니다. 즉, 데이터베이스 작성 및 삭제를 제어하는 동시에 탄력적 데이터베이스가 분할된 데이터베이스 맵 관리자 API를 사용하여 데이터베이스와 shardlet 매핑을 관리합니다. 
* **Consistency**: 응용 프로그램은 분할을 사용하며 탄력적인 확장의 데이터 종속 라우팅 기능을 사용합니다. 손상되었거나 잘못된 쿼리 결과를 방지하기 위해 분할된 데이터베이스 맵 관리자를 통해 연결이 조정됩니다. 또한 유효성 검사 및 일관성도 유지됩니다.
* **Code First**: EF의 Code First 패러다임의 편의성을 유지합니다. Code First에서 응용 프로그램의 클래스는 기본 데이터베이스 구조에 매핑됩니다. 응용 프로그램 코드는 기본 데이터베이스 처리와 관련된 대부분의 측면을 마스킹하는 DbSet와 상호 작용합니다.
* **Schema**: Entity Framework는 초기 데이터베이스 스키마 생성과 마이그레이션을 통한 후속 스키마 전개를 처리합니다. 이러한 기능을 유지하면 데이터가 전개될 때 그에 맞춰 응용 프로그램을 쉽게 조정할 수 있습니다. 

다음 지침에서는 탄력적 데이터베이스 도구를 사용하여 Code First 응용 프로그램에 대한 이러한 요구 사항을 충족하는 방법을 제공합니다. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>EF DbContext를 사용하는 데이터 종속 라우팅
Entity Framework를 사용하는 데이터베이스 연결은 대개 **DbContext**의 서브클래스를 통해 관리합니다. 이러한 서브클래스는 **DbContext**에서 파생시키는 방식으로 만듭니다. 여기서 응용 프로그램용 CLR 개체의 데이터베이스 기반 컬렉션을 구현하는 **DbSets** 를 정의합니다. 데이터 종속 라우팅의 컨텍스트에서 다른 EF Code First 응용 프로그램 시나리오의 경우에는 유지하지 않아도 되는 몇 가지 유용한 속성을 확인할 수 있습니다. 

* 데이터베이스가 이미 있으며 탄력적 데이터베이스의 분할된 데이터베이스 맵에 등록되어 있습니다. 
* 응용 프로그램의 스키마가 아래에 설명된 데이터베이스에 이미 배포되었습니다. 
* 데이터베이스에 대한 데이터 종속 라우팅 연결이 분할된 데이터베이스 맵을 통해 조정됩니다. 

규모 확장을 위해 **DbContexts** 를 데이터 종속 라우팅과 통합하려면

1. 분할된 데이터베이스 맵 관리자의 탄력적 데이터베이스 클라이언트 인터페이스를 통해 실제 데이터베이스 연결을 만듭니다. 
2. 연결을 **DbContext** 서브클래스로 래핑합니다.
3. 연결을 **DbContext** 기본 클래스로 전달하여 EF 쪽의 모든 처리가 제대로 수행되는지 확인합니다. 

다음 코드 예제에서 이 접근 방식을 보여 줍니다. 이 코드는 함께 제공되는 Visual Studio 프로젝트에도 있습니다.

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data-dependent routing. This call opens a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call fails for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>요점
* DbContext 서브클래스의 기본 생성자 대신 새로운 생성자가 제공됩니다. 
* 새로운 생성자는 탄력적 데이터베이스 클라이언트 라이브러리를 통해 데이터 종속 라우팅에 필요한 다음과 같은 인수를 가져옵니다.
  
  * 데이터 종속 라우팅 인터페이스에 액세스하는 분할된 데이터베이스 맵
  * shardlet을 식별하는 분할 키
  * 분할된 데이터베이스에 대한 데이터 종속 라우팅 연결에 필요한 자격 증명이 포함된 연결 문자열 
* 기본 클래스 생성자에 대한 호출은 데이터 종속 라우팅에 필요한 모든 단계를 수행하는 정적 메서드로 우회합니다. 
  
  * 즉, 분할된 데이터베이스 맵에서 탄력적 데이터베이스 클라이언트 인터페이스의 OpenConnectionForKey 호출을 사용하여 열린 연결을 설정합니다.
  * 분할된 데이터베이스 맵은 지정된 분할 키용 shardlet을 유지하는 분할된 데이터베이스에 대해 열린 연결을 만듭니다.
  * 이 열린 연결은 DbContext의 기본 클래스 생성자로 다시 전달되어 EF를 통해 새 연결을 자동으로 만드는 대신 이 연결이 EF에서 사용되도록 지정합니다. 이러한 방식으로 분할된 데이터베이스 맵 관리 작업에서 일관성이 보장되도록 탄력적 데이터베이스 클라이언트 API에서 연결 태그를 지정합니다.

코드에서 기본 생성자 대신 DbContext 서브클래스의 새로운 생성자를 사용합니다. 다음은 예제입니다. 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

새로운 생성자는 **tenantid1**의 값으로 식별되는 shardlet용 데이터가 저장되는 분할된 데이터베이스에 대한 연결을 엽니다. **using** 블록의 코드는 **tenantid1**용 분할된 데이터베이스에서 EF를 사용하는 블로그의 **DbSet**에 변경되지 않고 계속 액세스합니다. 그에 따라 모든 데이터베이스 작업의 범위가 이제 **tenantid1** 이 저장되는 분할된 데이터베이스 하나로 지정되도록 using 블록의 코드 의미 체계가 변경됩니다. 예를 들어 블로그 **DbSet** 에 대한 LINQ 쿼리는 현재 분할된 데이터베이스에 저장되어 있는 블로그만 반환하며 다른 분할된 데이터베이스에 저장된 블로그는 반환하지 않습니다.  

#### <a name="transient-faults-handling"></a>일시적인 오류 처리
Microsoft Patterns & Practices 팀은 [일시적인 오류 처리 응용 프로그램 블록](https://msdn.microsoft.com/library/dn440719.aspx)을 게시했습니다. 이 라이브러리는 EF와 함께 탄력적인 확장 클라이언트 라이브러리에 사용됩니다. 그러나 모든 일시적인 예외가 특정 위치 즉, 일시적인 오류 후 조정된 생성자를 사용하여 연결이 시도되도록 새 생성자가 사용되는지 확인할 수 있는 위치로 반환되어야 합니다. 그렇지 않으면 올바른 분할된 데이터베이스에 대한 연결을 보장되지 않으며, 분할된 데이터베이스 맵이 변경될 때 연결이 유지되지 않을 수 있습니다. 

다음 코드 샘플에서는 새 **DbContext** 서브클래스 생성자에 대해 SQL 다시 시도 정책을 사용할 수 있는 방법을 보여 줍니다. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

위 코드의 **SqlDatabaseUtils.SqlRetryPolicy**는 다시 시도 횟수가 10이고 다시 시도 간의 대기 시간이 5초인 **SqlDatabaseTransientErrorDetectionStrategy**로 정의됩니다. 이 방식은 EF 및 사용자가 시작한 트랜잭션에 대한 지침과 비슷합니다. 자세한 내용은 [다시 시도 실행 전략 제한 사항(EF6 이상)](http://msdn.microsoft.com/data/dn307226)을 참조하세요. 두 가지 경우 모두 응용 프로그램은 일시적인 예외 발생 시 다시 돌아갈 범위를 제어해야 합니다. 즉, 트랜잭션을 다시 열거나 위에 나와 있는 대로 탄력적 데이터베이스 클라이언트 라이브러리를 사용하는 적합한 생성자에서 컨텍스트를 다시 만들어야 합니다.

이처럼 일시적인 예외 발생 시 돌아갈 범위를 제어해야 하므로 EF에 포함된 기본 제공 **SqlAzureExecutionStrategy**를 사용할 필요가 없습니다. **SqlAzureExecutionStrategy**는 연결을 다시 열지만 **OpenConnectionForKey**는 사용하지 않으므로 **OpenConnectionForKey** 호출의 일부분으로 수행되는 모든 유효성 검사를 무시합니다. 대신 코드 샘플에서는 역시 EF에 포함된 기본 제공 **DefaultExecutionStrategy**를 사용합니다. **SqlAzureExecutionStrategy**와는 달리 DefaultExecutionStrategy는 일시적인 오류 처리의 다시 시도 정책과 정상적으로 연동됩니다. 실행 정책은 **ElasticScaleDbConfiguration** 클래스에서 설정됩니다. 여기서는 일시적인 예외 발생 시 **SqlAzureExecutionStrategy**를 사용하게 되므로 앞에서 설명한 것처럼 잘못된 동작이 수행됩니다. 따라서 **DefaultSqlExecutionStrategy**는 사용하지 않습니다. 각 다시 시도 정책 및 EF에 대한 자세한 내용은 [EF의 연결 복원력](http://msdn.microsoft.com/data/dn456835.aspx)을 참조하세요.     

#### <a name="constructor-rewrites"></a>생성자 다시 작성
위의 코드 예제는 응용 프로그램에서 Entity Framework와 함께 데이터 종속 라우팅을 사용하는 데 필요한 기본 생성자 다시 작성 방법을 보여 줍니다. 다음 표에서는 다른 생성자에 대한 이 접근 방식을 일반화합니다. 

| 현재 생성자 | 데이터에 맞게 다시 작성된 생성자 | 기본 생성자 | 메모 |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |연결은 분할된 데이터베이스 맵 및 데이터 종속 라우팅 키의 한 기능이어야 합니다. EF를 통한 자동 연결 생성을 무시하고 분할된 데이터베이스 맵을 사용하여 연결을 조정해야 합니다. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |연결은 분할된 데이터베이스 맵 및 데이터 종속 라우팅 키의 한 기능입니다. 고정 데이터베이스 이름 또는 연결 문자열은 분할된 데이터베이스 맵에 의한 유효성 검사를 무시하므로 작동하지 않습니다. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |연결은 제공된 모델을 사용하여 지정된 분할된 데이터베이스 맵 및 분할 키에 대해 생성됩니다. 컴파일된 모델이 기본 c'tor로 전달됩니다. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |연결은 분할된 데이터베이스 맵 및 키에서 유추해야 합니다. 연결은 입력으로 제공할 수 없습니다(해당 입력이 이미 분할된 데이터베이스 맵 및 키를 사용하고 있지 않다면). 부울이 전달됩니다. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |연결은 분할된 데이터베이스 맵 및 키에서 유추해야 합니다. 연결은 입력으로 제공할 수 없습니다(해당 입력이 분할된 데이터베이스 맵 및 키를 사용하고 있지 않다면). 컴파일된 모델이 전달됩니다. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |새로운 생성자가 입력으로 전달된 ObjectContext의 모든 연결이 탄력적인 확장을 통해 관리되는 연결로 다시 라우팅되는지 확인해야 합니다. 이 문서에서는 ObjectContexts에 대해 자세히 설명하지 않습니다. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |연결은 분할된 데이터베이스 맵 및 키에서 유추해야 합니다. 연결은 입력으로 제공할 수 없습니다(해당 입력이 이미 분할된 데이터베이스 맵 및 키를 사용하고 있지 않다면). 모델 및 Boolean이 기본 클래스 생성자에 전달됩니다. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>EF 마이그레이션을 통해 분할된 데이터베이스 스키마 배포
자동 스키마 관리는 Entity Framework에서 제공하는 편리한 기능입니다. 탄력적 데이터베이스 도구를 사용한 응용 프로그램의 컨텍스트에서, 데이터베이스가 분할된 응용 프로그램에 추가될 때 새로 생성된 분할된 데이터베이스에 스키마를 자동으로 프로 비전하는 이 기능을 유지하려고 합니다. 기본 사용 사례는 EF를 사용하여 분할된 응용 프로그램에 대한 데이터 계층의 용량을 늘리는 것입니다. 스키마 관리에 EF의 기능을 사용하면 EF를 기반으로 하는 분할된 응용 프로그램에 대한 데이터베이스 관리 작업이 줄어듭니다. 

EF 마이그레이션을 통한 스키마 배포는 **열려 있지 않은 연결**에서 가장 효율적으로 작동합니다. 이 시나리오는 탄력적 데이터베이스 클라이언트 API에서 제공하는 열린 연결을 사용하는 데이터 종속 라우팅 시나리오와 대조적입니다. 또 다른 차이점은 일관성 요구사항입니다: 동시 분할된 데이터베이스 맵 조작을 방지하기 위해 모든 데이터 종속 라우팅 연결에 대해 일관성을 유지하는 것이 바람직하긴 하지만 아직 분할된 데이터베이스 맵에 등록되지 않았고 shardlet을 유지하도록 아직 할당되지 않은 새 데이터베이스에 초기 스키마를 배포할 경우에는 문제가 없습니다. 따라서 데이터 종속 라우팅과 달리 이 시나리오에 대한 일반 데이터베이스 연결을 사용할 수 있습니다.  

그 결과, 이 접근 방식에서는 EF 마이그레이션을 통한 스키마 배포는 새 데이터베이스를 응용 프로그램의 분할된 데이터베이스 맵에 분할된 데이터베이스로 등록하는 과정과 밀접하게 결합됩니다. 다음 필수 구성 요소가 충족되어야 합니다. 

* 데이터베이스가 이미 생성되었습니다. 
* 데이터베이스가 비어 있습니다. 즉, 사용자 스키마와 사용자 데이터가 없습니다.
* 데이터 종속 라우팅을 위한 탄력적 데이터베이스 클라이언트 API를 통해 데이터베이스에 아직 액세스할 수 없습니다. 

이러한 필수 구성 요소가 충족되면 열려 있지 않은 일반 **SqlConnection**을 만들어 스키마 배포에 대한 EF 마이그레이션을 시작할 수 있습니다. 다음 코드 샘플에서 이 접근 방법을 보여 줍니다. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 


이 샘플에서는 **RegisterNewShard** 메서드를 보여 줍니다. 이 메서드는 탄력적인 확장의 분할된 데이터베이스 맵에 분할된 데이터베이스를 등록하고, EF 마이그레이션을 통해 스키마를 배포하고, 분할된 데이터베이스에 대한 분할 키 매핑을 저장합니다. 이 샘플은 SQL 연결 문자열을 입력으로 사용하는 **DbContext** 서브클래스(샘플에서는 **ElasticScaleContext**)의 생성자를 사용합니다. 이 생성자의 코드는 다음 예제와 같이 간단합니다. 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // You want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

기본 클래스에서 상속된 생성자의 버전을 사용할 수도 있습니다. 그러나 이 코드는 연결할 때 EF의 기본 이니셜라이저가 사용되는지 확인해야 합니다. 따라서 연결 문자열을 사용하여 기본 클래스 생성자를 호출하기 전에 정적 메서드로 짧게 우회합니다. EF에 대한 이니셜라이저 설정이 충돌하지 않도록 분할된 데이터베이스의 등록은 다른 앱 도메인 또는 프로세스에서 실행해야 합니다. 

## <a name="limitations"></a>제한 사항
이 문서에서 설명하는 접근 방식에는 몇 가지 제한 사항이 따릅니다. 

* 탄력적 데이터베이스 클라이언트 라이브러리를 사용하기 전에 먼저 **LocalDb** 를 사용하는 EF 응용 프로그램을 일반 SQL Server 데이터베이스로 마이그레이션해야 합니다. **LocalDb**의 경우 탄력적인 확장을 사용하는 분할된 데이터베이스를 통해 응용 프로그램의 규모를 확장할 수 없습니다. 개발에서는 **LocalDb**를 계속 사용할 수 있습니다. 
* 데이터베이스 스키마를 변경하는 모든 응용 프로그램에 변경을 수행할 때는 모든 분할된 데이터베이스에서 EF 마이그레이션을 진행해야 합니다. 이 문서의 샘플 코드에서는 이 작업을 수행하는 방법을 보여 주지 않습니다. ConnectionString 매개 변수와 함께 Update-Database를 사용하여 모든 분할된 데이터베이스에 대해 반복하거나, –Script 옵션과 함께 Update-Database를 사용하여 보류 중인 마이그레이션에 대한 T-SQL 스크립트를 추출하여 사용 중인 분할된 데이터베이스에 적용하는 것이 좋습니다.  
* 요청이 있는 경우 모든 데이터베이스 처리가 요청에서 제공된 분할 키로 식별되는 단일 분할된 데이터베이스에 포함된다고 가정합니다. 그러나 이 가정이 항상 참인 것은 아닙니다. 예를 들어 분할 키를 사용 가능하도록 설정하지 못할 수 있습니다. 이 문제를 해결하기 위해 클라이언트 라이브러리는 **MultiShardQuery** 클래스를 제공합니다. 이 클래스는 여러 분할된 데이터베이스를 쿼리할 수 있도록 연결 추상화를 구현합니다. 이 문서에서는 EF와 함께 **MultiShardQuery**를 사용하는 방법을 설명하지 않습니다.

## <a name="conclusion"></a>결론
이 문서에 설명된 단계를 통해 EF 응용 프로그램은 EF 응용 프로그램에 사용된 **DbContext** 서브클래스의 생성자를 리팩터링하여 데이터 종속 라우팅을 위한 탄력적 데이터베이스 클라이언트 라이브러리 기능을 사용할 수 있습니다. 이 경우 **DbContext** 클래스가 이미 있는 위치에서만 응용 프로그램을 변경해야 합니다. 또한 EF 응용 프로그램은 분할된 데이터베이스 맵에 새 분할된 데이터베이스 및 매핑을 등록하는 단계와 필요한 EF 마이그레이션을 호출하는 단계를 결합하여 자동 스키마 배포의 이점을 계속 활용할 수 있습니다. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
