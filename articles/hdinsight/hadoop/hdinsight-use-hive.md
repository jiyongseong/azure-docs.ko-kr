---
title: Apache Hive 및 HiveQL이란? - Azure HDInsight | Microsoft Docs
description: Apache Hive는 Hadoop용 데이터 웨어하우스 시스템입니다. Transact-SQL과 유사하게 HiveQL을 사용하여 Hive에 저장된 데이터를 쿼리할 수 있습니다. 이 문서에서는 Azure HDInsight와 함께 Hive 및 HiveQL을 사용하는 방법에 알아봅니다.
keywords: hiveql,what is hive,hadoop hiveql,how to use hive,learn hive,what is hive
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: e418411cc6b681e304cc1ba66f0c815ad0d4db64
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34069712"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Azure HDInsight의 Apache Hive 및 HiveQL이란?

[Apache Hive](http://hive.apache.org/)는 Hadoop용 데이터 웨어하우스 시스템입니다. Hive를 사용하면 데이터의 요약, 쿼리 및 분석을 수행할 수 있습니다. Hive 쿼리는 SQL과 유사한 쿼리 언어인 HiveQL로 작성합니다.

Hive를 사용하면 크게 구조가 없는 데이터에 구조를 투영할 수 있습니다. 구조를 정의한 후에 Java 또는 MapReduce 지식 없이 해당 데이터를 쿼리할 때에 HiveQL을 사용할 수 있습니다.

HDInsight는 특정 워크로드에 맞게 조정되는 여러 클러스터 형식을 제공합니다. 다음과 같은 클러스터 형식이 Hive 쿼리에 가장 자주 사용됩니다.

* __Interactive Query__: [LLAP(낮은 대기 시간 분석 처리)](https://cwiki.apache.org/confluence/display/Hive/LLAP) 기능을 제공하여 대화형 쿼리에 대한 응답 시간을 개선하는 Hadoop 클러스터입니다. 자세한 내용은 [HDInsight의 Interactive Query로 시작](../interactive-query/apache-interactive-query-get-started.md) 문서를 참조하세요.

* __Hadoop__: 배치 프로세싱 워크로드에 대해 조정된 Hadoop 클러스터입니다. 자세한 내용은 [HDInsight의 Hadoop으로 시작](../hadoop/apache-hadoop-linux-tutorial-get-started.md) 문서를 참조하세요.

* __Spark__: Apache Spark는 Hive로 작업하기 위한 기본 제공 기능입니다. 자세한 내용은 [HDInsight의 Spark로 시작](../spark/apache-spark-jupyter-spark-sql.md) 문서를 참조하세요.

* __HBase__: HiveQL은 HBase에 저장된 데이터를 쿼리하는 데 사용할 수 있습니다. 자세한 내용은 [HDInsight의 HBase로 시작](../hbase/apache-hbase-tutorial-get-started-linux.md) 문서를 참조하세요.

## <a name="how-to-use-hive"></a>Hive 사용 방법

HDInsight에서 Hive를 사용하는 여러 가지 방법을 알아보려면 다음 표를 참조하세요.

| 다음을 원하는 경우 **이 메서드를 사용**... | ...**대화형** 쿼리 | ...**배치** 처리 | ... **클러스터 운영 체제** | ... **클라이언트 운영 체제** |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight tools for Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X, 또는 Windows |
| [Visual Studio용 HDInsight 도구](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Linux 또는 Windows* |Windows |
| [Hive 보기](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |모두(브라우저 기반) |
| [Beeline 클라이언트](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X, 또는 Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux 또는 Windows* |Linux, Unix, Mac OS X, 또는 Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux 또는 Windows* |Windows |

> [!IMPORTANT]
> \* Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="hiveql-language-reference"></a>HiveQL 언어 참조

HiveQL 언어 참조는 [언어 설명서(https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)에서 사용할 수 있습니다.

## <a name="hive-and-data-structure"></a>Hive 및 데이터 구조

Hive는 구조화되거나 반구조화된 데이터로 작업하는 방법을 이해합니다. 예를 들어 필드가 특정 문자로 구분된 텍스트 파일이 있습니다. 다음 HiveQL 문은 공백으로 구분된 데이터에 대해 테이블을 만듭니다.

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

또한 Hive는 복잡하거나 불규칙하게 구조화된 데이터에 대한 사용자 지정을 **serializer/deserializers(SerDe)** 지원합니다. 자세한 내용은 [HDInsight와 함께 사용자 지정 JSON SerDe를 사용하는 방법](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) 문서를 참조하세요.

하이브에서 지원하는 파일 형식에 대한 자세한 내용은 [언어 설명서(https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)를 참조하세요.

### <a name="hive-internal-tables-vs-external-tables"></a>Hive 내부 테이블과 외부 테이블 비교

Hive로 다음과 같은 두 가지 형식의 테이블을 만들 수 있습니다.

* __내부__: 데이터가 Hive 데이터 웨어하우스에 저장됩니다. 데이터 웨어하우스는 클러스터의 기본 저장소인 `/hive/warehouse/`에 있습니다.

    다음 조건 중 하나가 적용되는 경우 내부 테이블을 사용합니다.

    * 데이터가 일시적입니다.
    * Hive로 테이블 및 데이터의 수명 주기를 관리하고자 합니다.

* __외부__: 데이터가 데이터 웨어하우스 외부에 저장됩니다. 클러스터에서 액세스할 수 있는 저장소에 데이터를 저장할 수 있습니다.

    다음 조건 중 하나가 적용되는 경우 외부 테이블을 사용합니다.

    * 데이터를 Hive 외부에서도 사용합니다. 예를 들어, 데이터 파일이 다른 프로세스에 의해 업데이트됩니다(파일을 잠그지 않음).
    * 테이블을 삭제한 후에도 데이터는 기본 위치에 남아 있어야 합니다.
    * 기본이 아닌 저장소 계정과 같은 사용자 지정 위치가 필요합니다.
    * Hive가 아닌 프로그램이 데이터 형식, 위치 등을 관리합니다.

자세한 내용은 [Hive 내부 및 외부 테이블 소개][cindygross-hive-tables] 블로그 게시물을 참조하세요.

## <a name="user-defined-functions-udf"></a>UDF(사용자 정의 함수)

Hive는 **사용자 정의 함수(UDF)** 를 통해 확장 될 수도 있습니다. UDF를 사용하면 HiveQL에서 쉽게 모델링할 수 있는 기능 또는 논리를 구현할 수 있습니다. Hive와 함께 UDF를 사용하는 방법의 예는 다음 문서를 참조하세요.

* [Hive와 함께 Java 사용자 정의 함수 사용](../hadoop/apache-hadoop-hive-java-udf.md)

* [Hive에서 Python 사용자 정의 함수 사용](../hadoop/python-udf-hdinsight.md)

* [Hive에서 C# 사용자 정의 함수 사용](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight에 사용자 지정 Hive 사용자 정의 함수를 추가하는 방법](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [날짜/시간 형식을 Hive 타임스탬프로 변환하는 Hive 사용자 지정 함수 예제](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>예제 데이터

HDInsight에서 Hive는 `hivesampletable`이라는 내부 테이블로 미리 로드됩니다. 또한 HDInsight는 Hive와 함께 사용할 수 있는 예제 데이터 집합을 제공합니다. 이러한 데이터 집합은 `/example/data` 및 `/HdiSamples` 디렉터리에 저장됩니다. 이러한 디렉터리는 클러스터의 기본 저장소에 있습니다.

## <a id="job"></a>예제 Hive 쿼리

다음 HiveQL 문은 열을 `/example/data/sample.log` 파일에 저장합니다.

```hiveql
set hive.execution.engine=tez;
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
    GROUP BY t4;
```

이전 예제에서 HiveQL 문은 다음 작업을 수행합니다.

* `set hive.execution.engine=tez;`: Tez를 사용하도록 실행 엔진을 설정합니다. Tez를 사용하면 쿼리 성능 향상을 제공할 수 있습니다. Tez에 대한 자세한 내용은 [향상된 성능을 위해 Apache Tez 사용](#usetez)을 참조하세요.

    > [!NOTE]
    > 이 문은 Windows 기반 HDInsight 클러스터를 사용할 경우에만 필요합니다. Tez는 Linux 기반 HDInsight의 기본 실행 엔진입니다.

* `DROP TABLE`: 이미 테이블이 있는 경우 삭제합니다.

* `CREATE EXTERNAL TABLE`: Hive에서 새 **외부** 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 원래 형식으로 남아 있습니다.

* `ROW FORMAT`: 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

* `STORED AS TEXTFILE LOCATION`: 데이터가 저장된 위치(`example/data` 디렉터리) 및 텍스트로 저장되었음을 Hive에 알립니다. 데이터는 디렉터리 내에서 하나의 파일 또는 여러 파일에 걸쳐 분산될 수 있습니다.

* `SELECT`: **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 값을 포함하는 행이 3개 있으므로 이 문은 **3** 값을 반환합니다.

* `INPUT__FILE__NAME LIKE '%.log'` - Hive는 디렉터리의 모든 파일에 스키마를 적용하려고 합니다. 이 경우 디렉터리에 스키마와 일치하지 않는 파일이 포함되어 있습니다. 결과에 가비지 데이터가 나타나는 것을 방지하기 위해 이 문은 .log로 끝나는 파일의 데이터만 반환해야 함을 Hive에게 알립니다.

> [!NOTE]
> 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예를 들어 자동화된 데이터 업로드 프로세스 또는 MapReduce 작업이 있습니다.
>
> 외부 테이블을 삭제하면 데이터가 삭제되지 **않고** 테이블 정의만 삭제됩니다.

외부 테이블 대신 **내부** 테이블을 만들려면 다음 HiveQL을 사용합니다.

```hiveql
set hive.execution.engine=tez;
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

이러한 문은 다음 작업을 수행합니다.

* `CREATE TABLE IF NOT EXISTS`: 테이블이 없는 경우 만듭니다. **EXTERNAL** 키워드가 사용되지 않으므로 이 문은 내부 테이블을 만듭니다. 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에서 전적으로 관리됩니다.

* `STORED AS ORC`: 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC는 Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.

* `INSERT OVERWRITE ... SELECT`: **[ERROR]** 가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.

> [!NOTE]
> 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

## <a name="improve-hive-query-performance"></a>Hive 쿼리 성능 향상

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) 는 Hive와 같이 데이터를 많이 사용하는 응용 프로그램을 큰 규모에서도 훨씬 더 효율적으로 실행할 수 있는 프레임워크입니다. Tez는 Linux 기반 HDInsight 클러스터에 대해 기본값으로 사용할 수 있습니다.

> [!NOTE]
> Tez는 현재 Windows 기반 HDInsight 클러스터에 대해 기본적으로 꺼져 있으며 사용하도록 설정해야 합니다. Tez를 사용하려면 Hive 쿼리에 대해 다음 값을 설정해야 합니다.
>
> `set hive.execution.engine=tez;`
>
> Tez는 Linux 기반 HDInsight 클러스터의 기본 엔진입니다.

[Tez의 Hive 디자인 문서](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)에는 선택 가능한 구현 및 튜닝 구성과 관련된 세부 정보가 포함되어 있습니다.

Tez를 사용하여 실행된 작업을 디버깅하도록 보조하려면 HDInsight는 Tez 작업의 세부 정보를 볼 수 있도록 다음 웹 UI를 제공합니다.

* [Linux 기반 HDInsight에서 Ambari Tez 보기 사용](../hdinsight-debug-ambari-tez-view.md)

* [Windows 기반 HDInsight 클러스터에서 Tez UI 사용](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>LLAP(짧은 대기 시간 분석 처리)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP)(Live Long and Process라고도 함)는 쿼리의 메모리 내 캐싱을 수행할 수 있는 Hive 2.0의 새로운 기능입니다. LLAP 덕분에 Hive 쿼리를 훨씬 빠르게, [일부 경우에는 Hive 1.x보다 26배 더 빠르게](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/) 수행합니다.

HDInsight는 Interactive Query 클러스터 형식으로 LLAP를 제공합니다. 자세한 내용은 [Interactive Query로 시작](../interactive-query/apache-interactive-query-get-started.md) 문서를 참조하세요.

## <a name="scheduling-hive-queries"></a>Hive 쿼리 예약

예약된 워크플로 또는 주문형 워크플로의 일부로 Hive 쿼리를 실행하는 데 사용할 수 있는 여러 서비스가 있습니다.

### <a name="azure-data-factory"></a>Azure 데이터 팩터리

Azure Data Factory를 사용하면 데이터 팩터리 파이프라인의 일부로 HDInsight를 사용할 수 있습니다. 파이프라인에서 Hive를 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory에서 Hive 활동을 사용하여 데이터 변환](/data-factory/transform-data-using-hadoop-hive.md) 문서를 참조하세요.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive 작업 및 SQL Server Integration Services

SSIS(SQL Server Integration Services)를 사용하여 Hive 작업을 실행할 수 있습니다. Azure Feature Pack for SSIS는 HDInsight에서 Hive 작업을 하는 다음 구성 요소를 제공합니다.

* [Azure HDInsight Hive 작업][hivetask]

* [Azure 구독 연결 관리자][connectionmanager]

자세한 내용은 [Azure 기능 팩][ ssispack] 설명서를 참조하세요.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie는 Hadoop 작업을 관리하는 워크플로 및 코디네이션 시스템입니다. Hive에서 Oozie를 사용하는 방법에 대한 자세한 내용은 [Oozie를 사용하여 워크플로 정의 및 실행](../hdinsight-use-oozie-linux-mac.md) 문서를 참조하세요.

## <a id="nextsteps"></a>다음 단계

이제 Hive의 정의 및 HDInsight에서 Hadoop와 Hive를 사용하는 방법을 살펴보았으므로 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 MapReduce 작업 사용][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
