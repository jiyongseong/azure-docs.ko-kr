---
title: Azure Cosmos DB에 Apache Spark 연결 | Microsoft Docs
description: 이 자습서에서는 Azure Cosmos DB Spark 커넥터를 통해 Azure Cosmos DB에 Apache Spark를 연결하여 Microsoft에서 클라우드를 위해 설계한 다중 테넌트 전역 분산형 데이터베이스 시스템에서 분산 집계 및 데이터 과학을 수행할 수 있는 방법에 대해 알아봅니다.
keywords: Apache Spark
services: cosmos-db
documentationcenter: ''
author: dennyglee
manager: kfile
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: denlee
ms.openlocfilehash: e93148a8870a40dbe5d89a2364497c44096eef7c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Spark-Azure Cosmos DB 커넥터를 사용하여 실시간 빅 데이터 분석 가속화

Spark-Azure Cosmos DB 커넥터를 사용하면 Azure Cosmos DB가 Apache Spark 작업의 입력 소스 또는 출력 싱크로 작동할 수 있습니다. [Spark](http://spark.apache.org/)를 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 연결하면 빠르게 움직이는 데이터 과학 문제를 더욱 빠르게 해결할 수 있도록 하므로 Azure Cosmos DB를 사용하여 데이터를 신속하게 저장하고 쿼리할 수 있습니다. Spark-Azure Cosmos DB 커넥터는 네이티브 Azure Cosmos DB 관리 인덱스를 효율적으로 활용합니다. 인덱스는 IoT(사물 인터넷)에서 데이터 과학 및 분석 시나리오에 이르기까지 급변하는 전역 분산 데이터에 대한 분석 및 푸시다운 조건자 필터링을 수행할 때 업데이트할 수 있는 열을 사용할 수 있도록 합니다.

이 비디오에서 Azure Cosmos DB 수석 프로그램 관리자 Denny Lee와 함께 자세히 알아보세요. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

Spark GraphX 및 Azure Cosmos DB의 Gremlin Graph API로 작업하는 경우 [Spark 및 Apache TinkerPop Gremlin을 사용하여 그래프 분석 수행](spark-connector-graph.md)을 참조하세요.

## <a name="download"></a>다운로드

시작하려면 GitHub의 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) 리포지토리에서 Azure Cosmos DB 커넥터로 Spark를 다운로드합니다.

## <a name="connector-components"></a>커넥터 구성 요소

커넥터는 다음 구성 요소를 사용합니다.

* [Azure Cosmos DB](http://documentdb.com)를 사용하면 고객은 여러 지리적 영역에서 처리량과 저장소를 프로비전하고 탄력적으로 확장할 수 있습니다. 서비스에서는 다음을 제공합니다.
   * 턴키 [전역 배포](distribute-data-globally.md) 및 수평적 확장
   * 백분위 99의 1자리 수 대기 시간 보증
   * [잘 정의된 다중 일관성 모델](consistency-levels.md)
   * multi-homing 기능을 통한 고가용성 보장
   * 모든 기능이 업계 최고 수준의 포괄적인 SLA([서비스 수준 약정](https://azure.microsoft.com/support/legal/sla/cosmos-db))에 의해 지원됩니다.

* [Apache Spark](http://spark.apache.org/)는 속도, 사용 편의성 및 정교한 분석을 기반으로 구축된 강력한 오픈 소스 처리 엔진입니다.

* [Azure HDInsight에서 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)를 사용하면 중요 업무용 배포를 위해 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)를 사용하여 클라우드에 Apache Spark를 배포할 수 있습니다.

공식적으로 지원되는 버전:

| 구성 요소 | 버전 |
|---------|-------|
|Apache Spark|2.0.2, 2.1.0, 2.2.0|
| 스칼라| 2.10, 2.11|
| Azure Cosmos DB SQL Java SDK | 1.14.0, 1.15.0 |

이 문서는 Python(pyDocumentDB를 통해)과 Scala 인터페이스를 사용하여 간단한 샘플을 실행하는 데 도움이 됩니다.

Apache Spark와 Azure Cosmos DB를 연결하는 방법에는 두 가지가 있습니다.
- [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python)를 통해 pyDocumentDB를 사용합니다.
- [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java)를 사용하여 Java 기반 Spark-Azure Cosmos DB 커넥터를 만듭니다.

## <a name="pydocumentdb-implementation"></a>pyDocumentDB 구현
현재 [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python)를 사용하면 다음 다이어그램과 같이 Spark를 Azure Cosmos DB에 연결할 수 있습니다.

![pyDocumentDB DB를 통한 Spark-Azure Cosmos DB 데이터 흐름](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>pyDocumentDB 구현의 데이터 흐름

데이터 흐름은 다음과 같습니다.

1. Spark 마스터 노드는 pyDocumentDB를 통해 Azure Cosmos DB 게이트웨이 노드에 연결합니다. 사용자는 Spark 및 Azure Cosmos DB 연결만 지정합니다. 해당 마스터 및 게이트웨이 노드에 대한 연결은 사용자에게 투명합니다.
2. 게이트웨이 노드를 통해 Azure Cosmos DB에 대해 쿼리가 수행되며, 이후에 여기서 데이터 노드의 컬렉션 파티션에 대해 쿼리가 실행됩니다. 이러한 쿼리에 대한 응답은 게이트웨이 노드로 다시 보내지고, 결과 집합은 Spark 마스터 노드로 반환됩니다.
3. 이후의 쿼리(예: Spark DataFrame)는 처리를 위해 Spark 작업자 노드로 보내집니다.

Spark와 Azure Cosmos DB 간의 통신은 Spark 마스터 노드와 Azure Cosmos DB 게이트웨이 노드로 제한됩니다.  쿼리는 이 두 노드 사이의 전송 계층만큼 빠릅니다.

### <a name="install-pydocumentdb"></a>pyDocumentDB 설치
**pip**를 사용하여 드라이버 노드에 pyDocumentDB를 설치할 수 있습니다. 예를 들면 다음과 같습니다.

```bash
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>pyDocumentDB를 통해 Azure Cosmos DB에 Spark 연결
통신 전송의 단순성으로 인해 pyDocumentDB를 사용하여 Spark에서 Azure Cosmos DB로 쿼리를 실행하는 것이 비교적 간단합니다.

다음 코드 조각에서는 Spark 컨텍스트에서 pyDocumentDB를 사용하는 방법을 보여 줍니다.

```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

코드 조각에서 설명했듯이

* Azure Cosmos DB Python SDK(`pyDocumentDB`)에는 모든 필수 연결 매개 변수가 포함되어 있습니다. 예를 들어 기본 위치 매개 변수는 읽기 복제본 및 우선 순위 순서를 선택합니다.
*  필요한 라이브러리를 가져와서 **masterKey** 및 **호스트**를 구성하여 Azure Cosmos DB *클라이언트*(**pydocumentdb.document_client**)를 만듭니다.


### <a name="execute-spark-queries-via-pydocumentdb"></a>pyDocumentDB를 통해 Spark 쿼리 실행
다음 예제에서는 지정된 읽기 전용 키를 사용하여 이전 코드 조각에서 만들어진 Azure Cosmos DB 인스턴스를 사용합니다. 다음 코드 조각은 앞에서 지정한 DoctorWho 계정의 **airports.codes** 컬렉션에 연결하여 워싱턴 주에 있는 공항 도시를 추출하는 쿼리를 실행합니다.

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

쿼리가 **query**를 통해 실행되면 결과는 Python 목록으로 변환된 **query_iterable.QueryIterable**입니다. Python 목록은 다음 코드를 사용하여 Spark DataFrame으로 쉽게 변환될 수 있습니다.

```python
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>pyDocumentDB를 사용하여 Azure Cosmos DB에 Spark를 연결하는 이유
pyDocumentDB를 사용하여 Azure Cosmos DB에 Spark를 연결하는 시나리오는 일반적으로 다음과 같습니다.

* Python을 사용하려고 합니다.
* 비교적 작은 결과 집합을 Azure Cosmos DB에서 Spark로 반환합니다. Azure Cosmos DB의 기본 데이터 집합은 상당히 클 수 있습니다. Azure Cosmos DB 소스에 대해 필터를 적용 즉, 조건자 필터를 실행합니다.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Spark-Azure Cosmos DB 커넥터

Spark-Azure Cosmos DB 커넥터는 [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java)를 사용하고 다음 다이어그램과 같이 Spark 작업자 노드와 Azure Cosmos DB 간에 데이터를 이동합니다.

![Spark-Azure Cosmos DB 커넥터의 데이터 흐름](./media/spark-connector/spark-connector.png)

데이터 흐름은 다음과 같습니다.

1. Spark 마스터 노드는 Azure Cosmos DB 게이트웨이 노드에 연결하여 파티션 맵을 가져옵니다. 사용자는 Spark 및 Azure Cosmos DB 연결만 지정합니다. 해당 마스터 및 게이트웨이 노드에 대한 연결은 사용자에게 투명합니다.
2. 이 정보는 Spark 마스터 노드에 다시 제공됩니다.  이 시점에서 쿼리를 구문 분석하여 액세스해야 하는 Azure Cosmos DB의 파티션 및 해당 위치를 확인할 수 있어야 합니다.
3. 이 정보는 Spark 작업자 노드로 전송됩니다.
4. Spark 작업자 노드는 Azure Cosmos DB 파티션에 직접 연결하여 데이터를 추출하고 Spark 작업자 노드의 Spark 파티션으로 데이터를 반환합니다.

Spark 작업자 노드와 Azure Cosmos DB 데이터 노드(파티션) 간에 데이터가 이동하므로 Spark와 Azure Cosmos DB 간의 통신이 훨씬 더 빠릅니다.

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Spark-Azure Cosmos DB 커넥터 빌드
현재 커넥터 프로젝트는 maven을 사용합니다. 종속성 없이 커넥터를 빌드하려면 다음을 실행할 수 있습니다.
```
mvn clean package
```
또한 [*releases* 폴더](https://github.com/Azure/azure-cosmosdb-spark/releases)에서 최신 버전의 JAR를 다운로드할 수 있습니다.

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Azure Cosmos DB Spark JAR 포함
코드를 실행하기 전에 먼저 Azure Cosmos DB Spark JAR를 포함해야 합니다.  **spark-shell**을 사용하는 경우 **--jars** 옵션을 사용하여 JAR를 포함할 수 있습니다.  

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0-uber.jar
```

종속성 없이 JAR를 실행하려면 다음 코드를 사용합니다.

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar,/$location/azure-documentdb-1.14.0.jar,/$location/azure-documentdb-rx-0.9.0-rc2.jar,/$location/json-20140107.jar,/$location/rxjava-1.3.0.jar,/$location/rxnetty-0.4.20.jar 
```

노트북 서비스(예: Azure HDInsight Jupyter 노트북 서비스)를 사용하는 경우 **spark 매직** 명령을 사용할 수 있습니다.

```
%%configure
{ "name":"Spark-to-Cosmos_DB_Connector", 
  "jars": ["wasb:///example/jars/1.0.0/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-1.14.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-rx-0.9.0-rc2.jar", "wasb:///example/jars/1.0.0/json-20140107.jar", "wasb:///example/jars/1.0.0/rxjava-1.3.0.jar", "wasb:///example/jars/1.0.0/rxnetty-0.4.20.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

**jars** 명령을 사용하면 **azure-cosmosdb-spark**(자체 및 Azure Cosmos DB SQL Java SDK)에 필요한 두 개의 JAR를 포함하고 **scala-reflect**를 제외할 수 있으므로 Livy 호출을 방해하지 않습니다(Jupyter 노트북 > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>커넥터를 사용하여 Spark를 Azure Cosmos DB에 연결
통신 전송이 좀 더 복잡하지만, 커넥터를 사용하여 Spark에서 Azure Cosmos DB로 쿼리를 실행하는 것이 훨씬 더 빠릅니다.

다음 코드 조각에서는 Spark 세션에서 커넥터를 사용하는 방법을 보여 줍니다. Python 샘플에 대해서는 `azure-cosmosdb-spark`[GitHub 리포지토리](https://github.com/Azure/azure-cosmosdb-spark)를 참조하세요.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val baseConfig = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(baseConfig)
coll.createOrReplaceTempView("c")
```

코드 조각에서 설명했듯이

- **azure-cosmosdb-spark**에는 기본 위치가 포함된 모든 필수 연결 매개 변수가 포함되어 있습니다. 예를 들어 읽기 복제본 및 우선 순위 순서를 선택할 수 있습니다.
- 필요한 라이브러리를 가져와서 masterKey 및 호스트를 구성하여 Azure Cosmos DB 클라이언트를 만듭니다.

### <a name="execute-spark-queries-via-the-connector"></a>커넥터를 통해 Spark 쿼리 실행

다음 예제에서는 지정된 읽기 전용 키를 사용하여 이전 코드 조각에서 만들어진 Azure Cosmos DB 인스턴스를 사용합니다. 다음 코드 조각은 DepartureDelays.flights_pcoll 컬렉션(앞에서 지정한 DoctorWho 계정)에 연결하여 시애틀에서 출발하는 항공편의 비행 지연 정보를 추출하는 쿼리를 실행합니다.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Spark-Azure Cosmos DB 커넥터 구현을 사용하는 이유

커넥터를 사용하여 Azure Cosmos DB에 Spark를 연결하는 시나리오는 일반적으로 다음과 같습니다.

* Python 및/또는 Scala를 사용하려고 합니다.
* Apache Spark와 Azure Cosmos DB 간에 많은 양의 데이터를 전송해야 합니다.

쿼리 성능 차이에 대한 자세한 내용은 [쿼리 테스트 실행 wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs)를 참조하세요.

## <a name="distributed-aggregation-example"></a>분산 집계 예제
이 섹션에서는 Apache Spark와 Azure Cosmos DB를 함께 사용하여 분산 집계 및 분석을 수행하는 방법의 몇 가지 예를 제공합니다. [Planet scale aggregates with Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)(Azure Cosmos DB를 사용한 전 세계적인 규모의 집계) 블로그에 설명된 대로 Azure Cosmos DB는 이미 집계를 지원하고 있습니다. 이러한 방법으로 Apache Spark를 사용하여 다음 단계로 넘어갈 수 있습니다.

이러한 집계는 [Spark-Azure Cosmos DB 커넥터 노트북](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb)을 참조합니다.

### <a name="connect-to-flights-sample-data"></a>비행 샘플 데이터에 연결
이러한 집계 예제에서는 **DoctorWho** Azure Cosmos DB 데이터베이스에 저장된 일부 비행 성능 데이터에 액세스합니다. 연결하려면 다음 코드 조각을 사용해야 합니다.

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

또한 이 코드 조각을 사용하여 필터링된 데이터 집합을 Azure Cosmos DB에서 Spark로 전송하는 기본 쿼리를 실행합니다. Spark는 분산 집계를 수행할 수 있습니다. 이 경우 시애틀(SEA)에서 출발하는 항공편을 요청합니다.

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

다음 결과는 Jupyter 노트북 서비스에서 쿼리를 실행하여 생성되었습니다.  모든 코드 조각은 일반적이며 어떤 서비스와도 관련이 없습니다.

### <a name="running-limit-and-count-queries"></a>LIMIT 및 COUNT 쿼리 실행
SQL/Spark SQL에서 사용했던 것처럼 **LIMIT** 쿼리로 시작해 보겠습니다.

![Spark LIMIT 쿼리](./media/spark-connector/spark-sql-query.png)

다음 쿼리는 간단하고 빠른 **COUNT** 쿼리입니다.

![Spark COUNT 쿼리](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY 쿼리
다음 집합에서 Azure Cosmos DB 데이터베이스에 대해 **GROUP BY** 쿼리를 쉽게 실행할 수 있습니다.

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Spark GROUP BY 쿼리 그래프](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINCT, ORDER BY 쿼리
다음은 **DISTINCT, ORDER BY** 쿼리입니다.

![Spark GROUP BY 쿼리 그래프](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>비행 데이터 분석 계속
다음 예제 쿼리를 사용하여 비행 데이터 분석을 계속할 수 있습니다.

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>시애틀에서 출발하는 상위 5개 지연 대상(도시)
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Spark 상위 지연 그래프](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>시애틀에서 출발하는 대상 도시별 지연 중앙값 계산
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Spark 지연 중앙값 지연 그래프](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>다음 단계

Cosmos DB와 Spark를 아직 연결하지 않았으면 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 리포지토리에서 Spark-Azure Cosmos DB 커넥터를 다운로드하고 다음 리포지토리에서 추가 리소스를 탐색합니다.

* [분산 집계 예제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)(영문)
* [샘플 스크립트 및 노트북](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)(영문)

또한 [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html)(Apache Spark SQL, DataFrame 및 데이터 집합 가이드) 및 [Azure HDInsight의 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 문서를 검토할 수도 있습니다.
