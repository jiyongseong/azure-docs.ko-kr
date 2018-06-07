---
title: Azure HDInsight에서 Hadoop 구성 요소에 대한 릴리스 정보 | Microsoft Docs
description: Azure HDInsight용 Hadoop 구성 요소의 최신 릴리스 정보 및 버전입니다. Spark, R 서버, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
services: hdinsight
documentationcenter: ''
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: nitinme
ms.openlocfilehash: 17ba4af006587dd1d34f61a0f5f77cbcd768fcf8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Azure HDInsight에서 Hadoop 구성 요소에 대한 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전 관리 문서](hdinsight-component-versioning.md)를 참조하세요.

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>HDInsight 3.6에서 Spark 2.2의 2018/03/20 릴리스 정보

- Spark 2.2.0은 Spark Core, SQL, ML 간 안정성을 개선하고 GA 상태에 대한 구조적 스트리밍을 제공합니다. 이제 Spark 2.2.0은 HDInsight 3.6에 제공됩니다.


## <a name="notes-for-08012017-release-of-hdinsight"></a>HDInsight의 2017/08/01 릴리스 정보

| 제목 | 설명 | 영향을 받는 영역  | 클러스터 유형  | 
| --- | --- | --- | --- | --- |
| HDInsight의 Microsoft R Server 9.1 릴리스 |HDInsight는 이제 HDInsight에서 R Server 9.1 클러스터 프로비저닝을 지원합니다. Microsoft R Server 9.1 릴리스에 대한 자세한 내용은 [이 블로그](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/)를 참조하세요. |서비스 |R 서버 |
| HDInsight 3.6에는 이제 Hadoop 스택의 더 새로운 버전이 포함됨|<ul><li>업데이트된 버전의 세부적인 목록은 [HDInsight에서 사용할 수 있는 Hadoop 구성 요소 버전](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions)을 참조하세요.</li><li>Hadoop 스택 최신 버전의 버그 수정 목록은 [Apache 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html)를 참조하세요.</li><li>HDP 2.6.1(이제 HDInsight 3.6에서 제공됨) 간의 주요 변경 사항 목록은 [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html)을 참조하세요.</li><li>HDP 2.6.1의 알려진 문제 목록은 [알려진 문제](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html)를 참조하세요.</li></ul> |서비스 |모두 |해당 없음 |
| Interactive Hive(미리 보기) 클러스터 업데이트 |<ul><li><b>기능 개선.</b> 메타데이터를 캐시하여 백엔드 SQL의 부하를 줄이고 모든 메타데이터 작업의 성능을 개선하는 캐시된 metastore 구현.  이 개선은 현재 모든 Interactive Hive 클러스터의 기본값입니다. 자세한 내용은 [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520)을 참조하세요.</li><li><b>기능 개선.</b> 동적 파티션 로딩이 최적화됩니다. 자세한 내용은 [https://issues.apache.org/jira/browse/HIVE-14204](https://issues.apache.org/jira/browse/HIVE-14204))을 참조하세요.</li><li><b>기능 개선.</b> Linux의 HDInsight 구성 최적화.</li><li><b>버그 수정.</b> `CredentialProviderFactory$getProviders`은(는) 스레드로부터 안전하지 않습니다. 이제 이 문제는 해결되었습니다. 자세한 내용은 [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195)을 참조하세요.</li><li><b>버그 수정.</b> WASB 드라이버 `liststatus` API에서 높은 CPU 사용률과 그로 인한 ATS 성능 저하. 이제 이 문제는 해결되었습니다. 자세한 내용은 [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154)을 참조하세요.</li></ul> |서비스 |대화형 Hive(미리 보기) |
| Hadoop 클러스터 업데이트 |Templeton 작업 안정성이 개선되었습니다. 자세한 내용은 [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947)을 참조하세요. |서비스 |Hadoop은 |
| YARN 업데이트 | 이제 HDInsight는 250GB Ambari 데이터베이스(비용 증가 없음)를 만들며 그 결과 고객의 환경이 개선됩니다. 이 변경은 ATS가 꽉 차지 않도록 하며, 대부분의 경우 더 높은 성능을 제공합니다. |서비스 |모두 |
| Spark 업데이트 | Spark 2.1.1 릴리스 자세한 내용은 [Spark 릴리스 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html)을 참조하세요. | 서비스 | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>2017/04/06 - HDInsight 3.6 일반 공급

* 이 릴리스에서 Azure HDInsight는 HDP 2.6을 기준으로 하는 버전 3.6을 추가합니다. HDP 2.6 릴리스 정보는 [여기](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html)에서 확인할 수 있고 HDInsight 버전에 대한 자세한 정보는 [여기](hdinsight-component-versioning.md)에서 확인할 수 있습니다. HDInsight 3.6은 다음과 같은 작업에 사용할 수 있습니다.

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **하이브 보기 2.0에 대한 지원**. Interactive Hive의 사용자 환경이 개선됩니다. 자세한 내용은 [Hortonworks 설명서](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html)를 참조하세요.

* **Hive LLAP를 통한 성능 향상**. 자세한 내용은 [Hortonworks 설명서](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/)를 참조하세요.

* **Hive의 새로운 기능** [Hortonworks 설명서](https://hortonworks.com/apache/hive/#section_4)를 참조하세요.

* **Hive CLI 사용 중단**: Hive CLI는 더 이상 사용되지 않으며 고객은 대신 Beeline을 사용하는 것이 좋습니다. 자세한 내용은 [Apache 설명서](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline)를 참조하세요. HDInsight와 함께 Beeline을 사용하는 방법에 대한 지침은 [HDInsight Hadoop 클러스터에서 Beeline 사용](hadoop/apache-hadoop-use-hive-beeline.md)을 참조하세요.

* **Apache Phoenix 및 HBase의 새로운 기능**.
    * 저장소 할당량 지원: 다중 테넌트 환경에서 일반적으로 사용되며 테이블 및 네임스페이스 수준별로 저장소 공간을 제한할 수 있도록 합니다.
    * Phoenix 인덱싱 개선: 증분 인덱스 만들기 및 이전 오류에서 인덱싱 다시 작성/재개
    * Phoenix 데이터 무결성 도구: 스키마, 인덱스 및 기타 메타데이터의 유효성 검사를 지원합니다.


* **Hbase 문제**: CSV 대량 업로드 MapReduce 작업을 실행하는 동안 다음 구문으로 인해 오류가 발생할 수 있습니다.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    대신 다음 구문을 사용합니다.

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>2017/02/28 - HDInsight 3.6(Preview)의 Spark 2.1 릴리스
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html)에서는 이전 버전에서 발생했던 수많은 안정성 및 유용성 문제가 개선되었습니다. 또한 Spark Core, SQL, ML 및 스트리밍 등 모든 Spark 워크로드 간에 새로운 기능을 적용할 수 있습니다.
* 구조화된 스트리밍으로 이벤트 시간 워터마크 및 Kafka 0.10 커넥터 지원을 포함하며 확장성이 향상되었습니다.
* 이제 Spark SQL 분할이 새로운 확장성 있는 파티션 처리 메커니즘을 사용하여 처리됩니다. 업그레이드 방법에 대한 자세한 내용은 [여기](http://spark.apache.org/releases/spark-release-2-1-0.html)를 참조하세요.
* 현재 Azure HDInsight 3.6 Preview에서 Spark 2.1은 ODBC 드라이버를 사용한 BI 도구 연결을 지원하지 않습니다.
* Spark 2.1 클러스터에서 Azure Data Lake Store 액세스는 이 미리 보기에서 지원되지 않습니다.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>2016/11/18 - HDInsight 3.5의 Spark 2.0.1 릴리스
Spark 2.0.1은 Spark 클러스터(HDInsight 버전 3.5)에서 사용할 수 있습니다.

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>2016/11/16 - HDInsight 3.5(Spark 2.0)의 R Server 9.0 릴리스
*   R Server 클러스터에는 이제 HDI 3.5의 R Server 9.0(Spark 2.0)과 HDI 3.4의 R Server 8.0(Spark 1.6)의 두 버전에 대한 옵션이 포함되어 있습니다.
*   HDI 3.5(Spark 2.0)의 R Server 9.0은 R 3.3.2 버전을 기반으로 하며, Hive 및 Parquet의 데이터를 Spark DataFrames로 직접 로드하여 ScaleR로 분석할 수 있는 RxHiveData 및 RxParquetData라는 새로운 ScaleR 데이터 원본 함수를 포함하고 있습니다. 자세한 내용을 확인하려면 **?RxHiveData** 및 **?RxParquetData** 명령을 사용하여 R에서 이러한 함수에 대한 인라인 도움말을 참조하세요.
*   RStudio Server 커뮤니티 버전은 기본적으로 프로비전 흐름의 일부로 클러스터 구성 블레이드(옵트아웃 옵션 사용)에서 설치됩니다.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>2016/11/09 - HDInsight의 Spark 2.0 릴리스
* 이제 HDInsight 3.5에서 Spark 2.0 클러스터에서는 Livy 및 Jupyter 서비스를 지원합니다.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>2016/10/26 - HDInsight의 R Server 릴리스
* 에지 노드 액세스를 위한 URI가 **clustername**-ed-ssh.azurehdinsight.net으로 변경되었습니다.
* HDInsight의 R 서버 클러스터 프로비전이 간소화되었습니다.
* 이제 HDInsight의 R 서버를 일반 HDInsight "R 서버" 클러스터 유형으로 사용할 수 있으며 별도의 HDInsight 응용 프로그램으로는 더 이상 설치되지 않습니다. 이제 에지 노드 및 R 서버 이진 파일이 R 서버 클러스터 배포의 일부로 프로비전됩니다. 그러면 프로비전의 속도 및 안정성이 향상됩니다. R 서버에 대한 가격 책정 모델이 이에 따라 업데이트됩니다.
* R 서버 클러스터 유형 가격은 표준 계층 가격 및 R 서버 추가 요금 가격을 따릅니다. 이러한 변경은 R 서버의 실효 가격에 영향을 주지 않으며 청구서에 요금이 표시되는 방식만 변경합니다. 기존의 모든 R 서버 클러스터는 계속 작동하고 Resource Manager 템플릿은 사용 중단 공지가 있을 때까지 계속 작동합니다. **새로운 Resource Manager 템플릿을 사용하도록 스크립트 배포를 업데이트하는 것이 좋습니다.**






