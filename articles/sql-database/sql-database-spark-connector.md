---
title: Azure SQL Database 및 SQL Server를 사용하는 Spark 커넥터 | Microsoft Docs
description: Azure SQL Database 및 SQL Server용 Spark 커넥터를 사용하는 방법을 알아봅니다.
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: article
ms.date: 04/23/2018
ms.author: xiwu
ms.openlocfilehash: 393af463c4145e1d865c14f2ace7d5123ab12cfa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187388"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Azure SQL Database 및 SQL Server용 Spark 커넥터를 통해 실시간 빅 데이터 분석 가속화

Azure SQL Database 및 SQL Server용 Spark 커넥터를 사용하면 Azure SQL Database 및 SQL Server를 비롯한 SQL 데이터베이스를 Spark 작업의 입력 데이터 원본 또는 출력 데이터 싱크로 사용할 수 있습니다. 이를 통해 빅 데이터 분석에서 실시간 트랜잭션 데이터를 활용하고 임시 쿼리 또는 보고에 대한 결과를 유지할 수 있습니다. 기본 제공 JDBC 커넥터에 비해, 이 커넥터는 SQL 데이터베이스에 대량으로 데이터를 삽입할 수 있는 기능을 제공합니다. 10배 ~ 20배 빠른 성능으로 행 단위 삽입 시 뛰어난 성능을 제공할 수 있습니다. Azure SQL Database 및 SQL Server용 Spark 커넥터 역시 AAD 인증을 지원합니다. AAD 계정을 사용하여 Azure Databricks에서 Azure SQL Database에 안전하게 연결할 수 있습니다. 기본 제공 JDBC 커넥터와 유사한 인터페이스를 제공합니다. 기존의 Spark 작업을 이 새로운 커넥터를 사용하도록 쉽게 마이그레이션할 수 있습니다.

## <a name="download"></a>다운로드
시작하려면 GitHub의 [azure-sqldb-spark 리포지토리](https://github.com/Azure/azure-sqldb-spark)에서 SQL DB 커넥터로 Spark를 다운로드합니다.

## <a name="official-supported-versions"></a>공식 지원되는 버전

| 구성 요소                            |버전                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 이상           |
| 스칼라                                |2.10 이상            |
| SQL Server용 Microsoft JDBC Driver |6.2 이상             |
| Microsoft SQL Server에 대한 연결 문자열                 |SQL Server 2008 이상 |
| Azure SQL Database                   |지원됨                |

Azure SQL Database 및 SQL Server용 Spark 커넥터는 SQL Server 용 Microsoft JDBC Driver를 사용하여 Spark 작업자 노드와 SQL 데이터베이스 간에 데이터를 이동합니다.
 
데이터 흐름은 다음과 같습니다.
1. Spark 마스터 노드는 SQL Server 또는 Azure SQL Database에 연결되고 특정 테이블 또는 특정 SQL 쿼리를 사용하여 데이터를 로드합니다.
2. Spark 마스터 노드는 변환을 위해 데이터를 작업자 노드에 배포합니다. 
3. 작업자 노드는 SQL Server 또는 Azure SQL Database에 연결되고 데이터를 데이터베이스에 기록합니다. 사용자는 행 단위 삽입 또는 대량 삽입을 사용할지 선택할 수 있습니다.

아래 다이어그램은 데이터 흐름을 보여 줍니다.

   ![아키텍처](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Spark - SQL DB 커넥터 빌드
현재 커넥터 프로젝트는 maven을 사용합니다. 종속성 없이 커넥터를 빌드하려면 다음을 실행할 수 있습니다.

- mvn clean package
- 릴리스 폴더에서 최신 버전의 JAR 다운로드
- SQL DB Spark JAR 포함

## <a name="connect-spark-to-sql-db-using-the-connector"></a>커넥터를 사용하여 Spark를 SQL DB에 연결
Spark 작업에서 Azure SQL Database 또는 SQL Server에 연결하고 데이터를 읽거나 쓸 수 있습니다. 또한 Azure SQL Database 또는 SQL Server 데이터베이스에서 DML 또는 DDL 쿼리를 실행할 수도 있습니다.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Azure SQL Database 또는 SQL Server에서 데이터 읽기

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>지정된 SQL 쿼리를 사용하여 Azure SQL Database 또는 SQL Server에서 데이터 읽기
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Azure SQL Database 또는 SQL Server에 데이터 기록
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Azure SQL Database 또는 SQL Server에서 DML 또는 DDL 쿼리 실행
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>AAD 인증을 사용하여 Azure SQL Database에 Spark 연결
AAD(Azure Active Directory) 인증을 사용하여 Azure SQL Database에 연결할 수 있습니다. AAD 인증을 사용하여 데이터베이스 사용자의 ID를 중앙에서 관리하고 SQL Server 인증 대신 사용할 수 있습니다.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>ActiveDirectoryPassword 인증 모드를 사용하여 연결
#### <a name="setup-requirement"></a>설치 요구 사항
ActiveDirectoryPassword 인증 모드를 사용하는 경우 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 및 해당 종속성을 다운로드하여 Java 빌드 경로에 포함시켜야 합니다.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>액세스 토큰을 사용하여 연결
#### <a name="setup-requirement"></a>설치 요구 사항
액세스 토큰 기반 인증 모드를 사용하는 경우 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 및 해당 종속성을 다운로드하여 Java 빌드 경로에 포함시켜야 합니다.

Azure SQL Database에 대한 액세스 토큰을 가져오는 방법에 대해 알아보려면 [SQL Database에서 인증을 위해 Azure Active Directory 인증 사용](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)을 참조하세요.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>대량 삽입을 사용하여 Azure SQL Database 또는 SQL Server에 데이터 기록
기존의 jdbc 커넥터는 행 단위 삽입을 사용하여 Azure SQL Database 또는 SQL Server에 데이터를 기록합니다. Spark - SQL DB 커넥터를 사용하고 대량 삽입을 사용하여 SQL Database에 데이터를 기록할 수 있습니다. 대량의 데이터 집합을 로드하거나 열 저장소 인덱스가 사용되는 테이블에 데이터를 로드할 때 쓰기 성능이 크게 향상됩니다.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>다음 단계
아직 다운로드하지 않았으면 [azure-sqldb-spark GitHub 리포지토리](https://github.com/Azure/azure-sqldb-spark)에서 Azure SQL Database 및 SQL Server용 Spark 커넥터를 다운로드하고 리포지토리에서 추가 리소스를 탐색합니다.

-   [Azure Databricks Notebooks 샘플](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [샘플 스크립트(Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

또한 [Apache Spark SQL, DataFrames 및 Datasets 가이드](http://spark.apache.org/docs/latest/sql-programming-guide.html) 및 [Azure Databricks 설명서](https://docs.microsoft.com/azure/azure-databricks/)를 검토할 수도 있습니다.

