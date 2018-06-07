---
title: 'Azure Cosmos DB: Spark 및 Apache TinkerPop Gremlin을 사용하여 그래프 분석 수행 | Microsoft Docs'
description: 이 문서에서는 Spark와 TinkerPop SparkGraphComputer를 사용하여 Azure Cosmos DB에서 그래프 분석 및 병렬 계산을 설정하고 실행하는 지침을 제공합니다.
services: cosmosdb
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: sngun
ms.openlocfilehash: f44c7d432ac9c07daf9b99dd922f1dcd5de5d58e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: Spark 및 Apache TinkerPop Gremlin을 사용하여 그래프 분석 수행

[Azure Cosmos DB](introduction.md)는 Microsoft에서 제공하는 전 세계에 배포된 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 코어인 전역 배포 및 수평적 규모 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 만들고 쿼리할 수 있습니다. Azure Cosmos DB는 [Apache TinkerPop Gremlin](graph-introduction.md)을 사용하는 OLTP(온라인 트랜잭션 처리) 그래프 워크로드를 지원합니다.

[Spark](http://spark.apache.org/)는 범용 OLAP(온라인 분석 처리) 데이터 처리에 중점을 둔 Apache Software Foundation 프로젝트입니다. Spark는 Hadoop MapReduce 모델과 유사한 하이브리드 메모리 내/디스크 기반 분산형 컴퓨팅 모델을 제공합니다. Apache Spark는 클라우드에서 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)를 사용하여 배포할 수 있습니다.

Azure Cosmos DB와 Spark를 결합하여 Gremlin을 사용할 때 OLTP와 OLAP 워크로드를 모두 수행할 수 있습니다. 이 빠른 시작 문서는 Azure HDInsight Spark 클러스터에서 Azure Cosmos DB에 Gremlin 쿼리를 실행하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음 필수 조건이 있어야 합니다.
* Azure HDInsight Spark 클러스터 2.0
* JDK 1.8+(JDK가 없는 경우 `apt-get install default-jdk` 실행)
* Maven(Maven이 없는 경우 `apt-get install maven` 실행)
* Azure 구독([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Azure HDInsight Spark 클러스터를 설정하는 방법에 대한 자세한 내용은 [HDInsight 클러스터 프로비전](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

## <a name="create-an-azure-cosmos-db-database-account"></a>Azure Cosmos DB 데이터베이스 계정 만들기

먼저 다음을 수행하여 Graph API로 데이터베이스 계정을 만듭니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>컬렉션 추가

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Apache TinkerPop 가져오기

다음을 수행하여 Apache TinkerPop을 가져옵니다.

1. HDInsight 클러스터 `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`의 마스터 노드에 원격 연결

2. TinkerPop3 소스 코드 복제, 로컬에서 빌드 및 Maven 캐시에 설치

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Spark-Gremlin 플러그 인 설치 

    a. 플러그 인 설치는 Grape에서 처리됩니다. 플러그인과 해당 종속성을 다운로드 할 수 있도록 Grape에 대한 리포지토리 정보를 입력합니다. 

      `~/.groovy/grapeConfig.xml`에 grape 구성 파일이 없으면 해당 파일을 만듭니다. 다음 설정을 사용합니다.

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    나. Gremlin 콘솔 `bin/gremlin.sh`을 시작합니다.
        
    다. 이전 단계에서 빌드한 버전 3.3.0-SNAPSHOT으로 Spark-Gremlin 플러그인을 설치합니다.

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. `Hadoop-Gremlin`이 `:plugin list`를 사용하여 활성화되었는지 확인합니다. Spark Gremlin 플러그 인 `:plugin unuse tinkerpop.hadoop`을 방해할 수 있기 때문에 이 플러그 인을 사용하지 않습니다.

## <a name="prepare-tinkerpop3-dependencies"></a>TinkerPop3 종속성 준비

이전 단계에서 TinkerPop3을 빌드할 때 프로세스는 대상 디렉터리에 있는 Spark 및 Hadoop의 모든 jar 종속성도 가져왔습니다. HDI로 미리 설치된 jar 파일을 사용하고 필요한 대로 추가 종속성만 가져옵니다.

1. `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`에서 Gremlin 콘솔 대상 디렉터리로 이동합니다. 

2. `ext/`에서 모든 jar를 `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`으로 이동시킵니다.

3. 아래 목록에 없는 `lib/`에서 모든 jar 라이브러리를 제거합니다.

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Azure Cosmos DB Spark 커넥터 가져오기

1. Azure Cosmos DB Spark 커넥터 `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` 및 Cosmos DB Java SDK`azure-documentdb-1.12.0.jar`를 [GitHub의 Azure Cosmos DB Spark 커넥터](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11)에서 가져옵니다.

2. 또는 로컬에서 빌드할 수 있습니다. 최신 버전의 Spark-Gremlin이 Spark 1.6.1로 빌드되었고 Azure Cosmos DB Spark 커넥터에서 현재 사용되는 Spark 2.0.2와 호환되지 않으므로 수동으로 최신 TinkerPop3 코드를 빌드하고 jar를 설치할 수 있습니다. 다음을 수행합니다.

    a. Azure Cosmos DB Spark 커넥터를 복제합니다.

    나. TinkerPop3 빌드(이전 단계에서 이미 수행됨). 모든 TinkerPop 3.3.0-SNAPSHOT jar를 로컬로 설치합니다.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    다. `tinkerpop.version` `azure-documentdb-spark/pom.xml`를 `3.3.0-SNAPSHOT`으로 업데이트합니다.
    
    d. Maven으로 빌드. 필요한 jar가 `target` 및 `target/alternateLocation`에 배치됩니다.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. 이전에 언급한 jar를 로컬 디렉터리 ~/azure-documentdb-spark에 복사

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>종속성을 Spark 작업자 노드에 배포 

1. 그래프 데이터의 변환이 TinkerPop3에 달려 있기 때문에 관련 종속성을 모든 Spark 작업자 노드에 배포해야 합니다.

2. 이전에 언급한 Gremlin 종속성과 CosmosDB Spark 커넥터 jar 및 CosmosDB Java SDK를 다음과 같이 작업자 노드에 복사합니다.

    a. 모든 jar를 `~/azure-documentdb-spark`에 복사합니다.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    나. Ambari 대시보드에 있는 `Spark2` 섹션의 `Spark2 Clients` 리스트에서 모든 Spark 작업자 노드의 목록을 가져옵니다.

    다. 해당 디렉터리를 각 노드에 복사합니다.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>환경 변수 설정

1. Spark 클러스터의 HDP 버전을 확인합니다. `/usr/hdp/`에서 디렉터리 이름입니다(예: 2.5.4.2-7).

2. 모든 노드에 hdp.version을 설정합니다. Ambari 대시보드에서 **YARN 섹션** > **구성** > **고급**으로 이동하고 다음을 수행합니다. 
 
    a. `Custom yarn-site`에서 마스터 노드의 HDP 버전 값으로 새 속성 `hdp.version`을 추가합니다. 
     
    나. 구성을 저장합니다. 발생한 경고를 무시할 수 있습니다. 
     
    다. 알림 아이콘이 나타내는 대로 YARN 및 Oozie 서비스를 다시 시작합니다.

3. 마스터 노드에서 다음 환경 변수를 설정(적절하게 값을 대체)합니다.

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>그래프 구성 준비

1. Azure Cosmos DB 연결 매개 변수와 Spark 설정으로 구성 파일을 생성하고 `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`에 넣습니다.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. 이 경우에 이전 단계에서 배포한 jar 디렉터리를 포함하도록 `spark.driver.extraClassPath` 및 `spark.executor.extraClassPath`를 `/home/sshuser/azure-documentdb-spark/*`으로 업데이트합니다.

3. Azure Cosmos DB에 다음 세부 정보를 입력합니다.

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>TinkerPop 그래프를 로드하고 Azure Cosmos DB에 저장합니다.
Azure Cosmos DB에 그래프를 유지할 수 있는 방법을 보여주려면 이 예제에서는 TinkerPop 미리 정의된 TinkerPop 최신 그래프를 사용합니다. 그래프는 Kryo 형식으로 저장되었으며 TinkerPop 리포지토리에 제공됩니다.

1. Gremlin을 YARN 모드로 실행하기 때문에 Hadoop 파일 시스템에서 그래프 데이터를 사용할 수 있도록 해야 합니다. 다음 명령을 사용하여 디렉터리를 만들고 그 안에 로컬 그래프 파일을 복사합니다. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. 그래프를 읽기 위해 `GryoInputFormat`을 사용하도록 `gremlin-spark.properties` 파일을 임시로 업데이트합니다. 또한 `inputLocation`을 다음과 같이 만든 디렉터리로 표시합니다.

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Gremlin 콘솔을 시작하고 다음 계산 단계를 만들어서 구성된 Azure Cosmos DB 컬렉션에 데이터를 저장합니다.  

    a. `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")` 그래프를 만듭니다.

    나. `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`을 작성하기 위해 SparkGraphComputer를 사용합니다.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. 데이터 탐색기에서 Azure Cosmos DB에 데이터가 유지되어 있는지 확인할 수 있습니다.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Azure Cosmos DB에서 그래프를 로드하고 Gremlin 쿼리를 실행합니다.

1. 그래프를 로드하려면 `graphReader`를 `DocumentDBInputRDD`로 설정하도록 `gremlin-spark.properties`를 편집합니다.

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. 다음을 수행하여 그래프를 로드하고 데이터를 순회한 다음 Gremlin 쿼리를 실행합니다.

    a. Gremlin 콘솔 `bin/gremlin.sh`을 시작합니다.

    나. `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')` 구성으로 그래프를 만듭니다.

    다. SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`로 그래프 순회를 만듭니다.

    d. 다음 Gremlin 그래프 쿼리를 실행합니다.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> 자세한 로그를 보려면 `conf/log4j-console.properties`의 로그 수준을 보다 자세한 수준으로 설정합니다.
>

## <a name="next-steps"></a>다음 단계

이 빠른 시작 문서에서는 Azure Cosmos DB와 Spark를 결합하여 그래프를 사용하는 방법을 알아 보았습니다.

> [!div class="nextstepaction"]
