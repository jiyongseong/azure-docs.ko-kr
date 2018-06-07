---
title: Azure HDInsight에서 Apache Spark란 | Microsoft Docs
description: 이 문서에서는 HDInsight의 Spark를 소개하고 HDInsight에서 Spark 클러스터를 사용할 수 있는 다양한 시나리오를 제공합니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: overview
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 7e01915a63e0e260dc85893bdf65cf7b86000cef
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark란

*Apache Spark*: 메모리 내 처리를 지원하여 빅 데이터 분석 응용 프로그램의 성능을 향상시키는 병렬 처리 프레임워크입니다. Azure HDInsight의 Apache Spark는 클라우드에서 Apache Hadoop의 Microsoft의 구현입니다. HDInsight를 사용하면 Azure에서 Spark 클러스터를 더 쉽게 만들고 구성할 수 있습니다. HDInsight의 Spark 클러스터는 Azure Storage 및 Azure Data Lake Store와 호환됩니다. 따라서 HDInsight Spark 클러스터를 사용하여 Azure에 저장된 데이터를 처리할 수 있습니다. 구성 요소 및 버전 관리 정보를 보려면 [Azure HDInsight의 Hadoop 구성 요소 및 버전](../hdinsight-component-versioning.md)을 참조하세요.

![Spark: 통합된 프레임워크](./media/apache-spark-overview/hdinsight-spark-overview.png)


## <a name="what-is-spark"></a>Spark란?

Spark는 메모리 내 클러스터 컴퓨팅을 위한 기본 형식을 제공합니다. Spark 작업은 메모리로 데이터를 로드하고 캐시하여 반복적으로 쿼리할 수 있습니다. 메모리 내 컴퓨팅은 HDFS를 통해 데이터를 공유하는 Hadoop 같은 디스크 기반 응용 프로그램보다 훨씬 빠릅니다. 또한 Spark는 지역 컬렉션과 같이 분산 데이터 집합을 조작할 수 있도록 Scala 프로그래밍 언어로 통합합니다. 매핑 및 reduce 작업으로 모든 것을 구조화하지 않아도 됩니다.

![기존 MapReduce와 Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

HDInsight에서 Spark 클러스터는 완벽하게 관리되는 Spark 서비스를 제공합니다. HDInsight에서 Spark 클러스터를 만드는 이점은 다음과 같습니다.

| 기능 | 설명 |
| --- | --- |
| 쉽게 만들기 |Azure Portal, Azure PowerShell 또는 HDInsight .NET SDK를 사용하여 몇 분 만에 HDInsight에서 새 Spark 클러스터를 만들 수 있습니다. [HDInsight에서 Spark 클러스터 시작](apache-spark-jupyter-spark-sql.md) |
| 사용 편의성 |HDInsight의 Spark 클러스터에는 Jupyter 및 Zeppelin 노트북이 포함되어 있습니다. 이러한 노트북을 대화형 데이터 처리 및 시각화에 사용할 수 있습니다.|
| REST API |HDInsight의 Spark 클러스터에는 원격으로 작업을 제출하고 모니터링하는 REST API 기반 Spark 작업 서버인 [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)가 포함되어 있습니다. |
| Azure Data Lake 저장소에 대한 지원 | HDInsight의 Spark 클러스터는 Azure Data Lake Store를 기본 저장소 또는 추가 저장소로 모두 사용할 수 있습니다. Data Lake 저장소에 대한 자세한 내용은 [Azure Data Lake 저장소 개요](../../data-lake-store/data-lake-store-overview.md)를 참조하세요. |
| Azure 서비스와의 통합 |HDInsight에서 Spark 클러스터는 Azure Event Hubs에 대한 커넥터와 함께 제공됩니다. [Kafka](http://kafka.apache.org/)외에, 이미 Spark에서 제공하는 Event Hubs를 사용하여 스트리밍 응용 프로그램을 빌드할 수 있습니다. |
| R 서버에 대한 지원 | Spark 클러스터와 약속된 속도로 분산된 R 계산을 실행하도록 HDInsight Spark 클러스터에서 R 서버를 설정할 수 있습니다. 자세한 내용은 [HDInsight에서 R 서버 시작](../r-server/r-server-get-started.md)을 참조하세요. |
| 타사 IDE와의 통합 | HDInsight는 유용한 여러 IDE 플러그인을 제공하여 응용 프로그램을 만들고 HDInsight Spark 클러스터에 제출합니다. 자세한 내용은 [IntelliJ IDEA용 Azure 도구 키트 사용](apache-spark-intellij-tool-plugin.md) 및 [Eclipse용 Azure 도구 키트 사용](apache-spark-eclipse-tool-plugin.md)을 참조하세요.|
| 동시 쿼리 |HDInsight의 Spark 클러스터는 동시 쿼리를 지원합니다. 이 기능을 통해 한 사용자의 여러 개 쿼리 또는 여러 사용자 및 응용 프로그램의 여러 개 쿼리에서 동일한 클러스터 리소스를 공유할 수 있습니다. |
| SSD에서 캐시 |클러스터 노드에 연결된 메모리 또는 SSD에서 데이터를 캐시하도록 선택할 수 있습니다. 메모리에서 캐시하면 최고의 쿼리 성능을 제공하지만 리소스가 많이 들 수 있습니다. SSD에서 캐시하면 메모리에서 전체 데이터 집합에 맞게 필요한 크기의 클러스터를 만들 필요 없이 쿼리 성능 향상을 위한 훌륭한 옵션을 제공합니다. |
| BI 도구와의 통합 |HDInsight에서 Spark 클러스터는 데이터 분석을 위해 [Power BI](http://www.powerbi.com/)와 같은 BI 도구용 커넥터를 제공합니다. |
| 미리 로드된 Anaconda 라이브러리 |HDInsight에서 Spark 클러스터는 미리 설치된 Anaconda 라이브러리와 함께 제공됩니다. [Anaconda](http://docs.continuum.io/anaconda/) 는 기계 학습, 데이터 분석, 시각화 등을 위해 약 200개의 라이브러리를 제공합니다. |
| 확장성 | HDInsight를 사용하면 클러스터 노드 수를 변경할 수 있습니다. 또한 모든 데이터가 Azure Storage 또는 Data Lake Store에 저장되므로 데이터 손실 없이 Spark 클러스터를 삭제할 수 있습니다. |
| SLA |HDInsight의 Spark 클러스터는 24/7 지원 및 99.9% 가동 시간 SLA와 함께 제공됩니다. |

HDInsight의 Spark 클러스터에는 기본적으로 클러스터에서 사용할 수 있는 다음 구성 요소가 포함되어 있습니다.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Spark Core, Spark SQL, Spark 스트리밍 API, GraphX 및 MLlib가 포함됩니다.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Zeppelin Notebook](http://zeppelin-project.org/)

또한 HDInsight의 Spark 클러스터는 Microsoft Power BI와 같은 BI 도구에서 HDInsight의 Spark 클러스터에 연결하기 위한 [ODBC 드라이버](http://go.microsoft.com/fwlink/?LinkId=616229)도 제공합니다.

## <a name="spark-cluster-architecture"></a>Spark 클러스터 아키텍처

![HDInsight Spark의 아키텍처](./media/apache-spark-overview/spark-architecture.png)

Spark가 HDInsight 클러스터에서 실행되는 방식을 이해하면 Spark의 구성 요소를 이해하는 것이 쉽습니다.

Spark 응용 프로그램은 클러스터의 독립 프로세스 집합으로 실행되며 기본 프로그램(드라이버 프로그램이라고 함)에서 SparkContext 개체에 의해 조정됩니다.

SparkContext는 응용 프로그램 간에 리소스를 할당하는 여러 유형의 클러스터 관리자에 연결합니다. 이러한 클러스터 관리자에는 Apache Mesos, Apache YARN 또는 Spark 클러스터 관리자가 포함됩니다. HDInsight에서 Spark는 YARN 클러스터 관리자를 사용하여 실행합니다. 일단 연결되면 Spark는 클러스터의 작업자 노드에서 실행기를 얻습니다. 이 실행기는 계산을 실행하고 응용 프로그램의 데이터를 저장하는 프로세스입니다. 그런 다음 응용 프로그램 코드(SparkContext에 전달된 JAR 또는 Python 파일에 의해 정의됨)를 실행기에 보냅니다. 마지막으로, SparkContext는 실행할 실행기로 작업을 전송합니다.

SparkContext는 사용자의 주 함수를 실행하고 작업자 노드에서 다양한 병렬 작업을 실행합니다. 그런 다음 SparkContext는 작업 결과를 수집합니다. 작업자 노드는 HDFS(Hadoop 분산 파일 시스템) 간에 데이터를 읽고 씁니다. 또한 작업자 노드는 RDD(Resilient Distributed Datasets)로 메모리 내 변환된 데이터를 캐시합니다.

SparkContext는 Spark 마스터에 연결하고 응용 프로그램을 작업자 노드의 실행기 프로세스 내에서 실행하는 개별 작업의 방향성 그래프(DAG)로 변환할 책임이 있습니다. 각 응용 프로그램은 전체 응용 프로그램의 기간에 대해 유지하고 여러 스레드에서 작업을 실행하는 자체 실행기 프로세스를 가져옵니다.

## <a name="spark-in-hdinsight-use-cases"></a>HDInsight의 Spark 사용 사례

HDInsight의 Spark 클러스터는 다음과 같은 주요 시나리오를 사용합니다.

- 대화형 데이터 분석 및 BI

    HDInsight의 Apache Spark는 Azure Storage 또는 Azure Data Lake Store에 데이터를 저장합니다. 비즈니스 전문가 및 주요 의사 결정권자는 해당 데이터에 대한 보고서를 분석 및 작성하고 Microsoft Power BI를 사용하여 분석된 데이터에서 대화형 보고서를 작성합니다. 분석자는 클러스터 저장소의 비구조적/반구조적 데이터부터 시작하여 노트북으로 데이터에 대한 스키마를 정의한 다음 Microsoft Power BI를 사용하여 데이터 모델을 작성할 수 있습니다. HDInsight에서 Spark 클러스터는 Tableau와 같은 많은 타사 BI 도구를 지원하므로 데이터 분석가, 비즈니스 전문가 및 핵심 의사 결정자가 쉽게 사용할 수 있습니다.

    [자습서: Power BI를 사용하여 Spark 데이터 시각화](apache-spark-use-bi-tools.md)
- Spark Machine Learning

    Apache Spark는 [MLlib](http://spark.apache.org/mllib/), 즉 Spark를 기반으로 하여 빌드되어 HDInsight의 Spark 클러스터에서 사용할 수 있는 기계 학습 라이브러리와 함께 제공됩니다. HDInsight의 Spark 클러스터에는 기계 학습을 위한 다양한 패키지가 포함된 Python 배포인 Anaconda도 있습니다. Jupyter 및 Zeppelin 노트북이 기본 제공되는 지원 기능과 결합하고, 기계 학습 응용 프로그램을 만들기 위한 환경을 제공합니다.

    [자습서: HVAC 데이터를 사용하여 건물 온도 예측](apache-spark-ipython-notebook-machine-learning.md) [자습서: 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)    
- Spark 스트리밍 및 실시간 데이터 분석

    HDInsight의 Spark 클러스터는 실시간 분석 솔루션을 빌드하기 위한 풍부한 지원을 제공합니다. Spark가 이미 커넥터를 가지고 Kafka, Flume, Twitter, ZeroMQ 또는 TCP 소켓 같은 여러 소스에서 데이터를 수집하는 동안 HDInsight에서 Spark는 Azure Event Hubs에서 데이터 수집을 위한 최상의 지원을 추가합니다. Event Hubs는 Azure에서 가장 널리 사용되는 큐 서비스입니다. Event Hubs에 대한 즉각적인 지원을 통해 HDInsight의 Spark 클러스터는 실시간 분석 파이프라인을 빌드하기 위한 이상적인 플랫폼이 됩니다.
    
## <a name="where-do-i-start"></a>시작 단계

다음 문서를 사용하여 HDInsight의 Spark에 대해 자세히 알아볼 수 있습니다.

- [빠른 시작: HDInsight에서 Spark 클러스터 만들기 및 Jupyter를 사용하여 대화형 쿼리 실행](./apache-spark-jupyter-spark-sql.md)
- [자습서: Jupyter를 사용하여 Spark 작업 실행](./apache-spark-load-data-run-query.md)
- [자습서: BI 도구를 사용하여 데이터 분석](./apache-spark-use-bi-tools.md)
- [자습서: Spark를 사용하여 기계 학습](./apache-spark-ipython-notebook-machine-learning.md)
- [자습서: IntelliJ를 사용하여 Scala Maven 응용 프로그램 만들기](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>다음 단계

이 개요를 통해 Azure HDInsight의 Apache Spark에 대한 몇 가지 기본 사항을 이해할 수 있습니다. 다음 문서에서는 HDInsight Spark 클러스터를 만들고 일부 Spark SQL 쿼리를 실행하는 방법을 알아봅니다.

- [HDInsight에서 Spark 클러스터 만들기](./apache-spark-jupyter-spark-sql.md)