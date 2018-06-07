---
title: 규모에 맞게 ETL(추출, 변환 및 로드) - Azure HDInsight | Microsoft Docs
description: Hadoop과 함께 HDInsight에서 ETL을 사용하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 2f34233a68bec506d9f69426d779aee8e493d759
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="extract-transform-and-load-etl-at-scale"></a>규모에 맞게 ETL(추출, 변환 및 로드)

ETL(추출, 변환 및 로드)은 다양한 원본에서 데이터를 가져오고, 표준 위치에 수집하고, 정리 및 처리한 다음, 최종적으로 쿼리할 수 있는 데이터 저장소에 로드하는 프로세스입니다. 레거시 ETL 프로세스는 데이터를 가져오고, 제 위치에서 정리한 다음, 관계형 데이터 엔진에 저장합니다. HDInsight를 사용하면 다양한 Hadoop 에코시스템 구성 요소에서 규모에 맞게 ETL을 수행할 수 있습니다. 

ETL 프로세스에서 HDInsight를 사용하는 방법은 다음과 같은 파이프라인으로 요약할 수 있습니다.

![HDInsight ETL 개요](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

다음 섹션에서는 ETL 단계 및 관련 구성 요소 각각에 대해 살펴봅니다.

## <a name="orchestration"></a>오케스트레이션

오케스트레이션은 ETL 파이프라인의 모든 단계에 걸쳐 있습니다. HDInsight의 ETL 작업은 몇 가지 다른 제품과 함께 상호 작동하는 경우가 많습니다.  Pig에서 다른 부분을 정리하는 동안 Hive를 사용하여 데이터의 일부를 정리할 수 있습니다.  Azure Data Factory를 사용하여 Azure Data Lake Store에서 Azure SQL Database로 데이터를 로드할 수 있습니다.

적절한 시간에 적절한 작업을 실행하려면 오케스트레이션이 필요합니다.

### <a name="oozie"></a>Oozie

Apache Oozie는 Hadoop 작업을 관리하는 워크플로 조정 시스템입니다. Oozie는 HDInsight 클러스터 내에서 실행되며 Hadoop 스택과 통합됩니다. Oozie는 Apache MapReduce, Apache Pig, Apache Hive 및 Apache Sqoop에 대한 Hadoop 작업을 지원합니다. 또한 Oozie를 사용하여 Java 프로그램 또는 셸 스크립트와 같이 시스템 특정의 작업을 예약할 수 있습니다.

자세한 내용은 [Hadoop과 함께 Oozie를 사용하여 HDInsight에서 워크플로 정의 및 실행](../hdinsight-use-oozie-linux-mac.md)을 참조하세요. Oozie를 사용하여 종단 간 파이프라인을 구동하는 방법에 대한 자세한 내용은 [데이터 파이프라인 운용](../hdinsight-operationalize-data-pipeline.md)을 참조하세요. 

### <a name="azure-data-factory"></a>Azure 데이터 팩터리

Azure Data Factory는 오케스트레이션 기능을 PaaS(Platform as a Service ) 형태로 제공합니다. 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. 

Azure Data Factory를 사용하면 다음을 수행할 수 있습니다.

1. 서로 다른 데이터 저장소의 데이터를 수집하는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 예약합니다.
2. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리하고 변환합니다.
3. 사용할 BI(비즈니스 인텔리전스) 응용 프로그램용 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시합니다.

Azure Data Factory에 대한 자세한 내용은 [이 설명서](../../data-factory/introduction.md)를 참조하세요.

## <a name="ingest-file-storage-and-result-storage"></a>파일 저장소 및 결과 저장소 수집

원본 데이터 파일은 일반적으로 Azure Storage 또는 Azure Data Lake Store의 한 위치에 로드됩니다. 파일은 어떤 형식이든 될 수 있지만 일반적으로 CSV와 같은 플랫 파일입니다. 

### <a name="azure-storage"></a>Azure Storage 

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/)에는 [특정 확장성 목표](../../storage/common/storage-scalability-targets.md)가 있습니다.  대부분의 분석 노드의 경우 Azure Storage에서 더 작은 파일을 많이 처리할 때 그 크기가 가장 효율적으로 조정됩니다.  Azure Storage는 파일 수 또는 파일 크기에 관계 없이(한도 내에서) 동일한 성능을 보장합니다.  즉, 일부 데이터만 사용하든 모든 데이터를 사용하든 관계 없이 테라바이트 단위의 데이터를 저장할 수 있고 일관된 성능을 유지할 수 있습니다.

Azure Storage에는 여러 가지 유형의 Blob이 있습니다.  *추가 Blob*은 웹 로그 또는 센서 데이터를 저장하는 데 유용한 옵션입니다.  

여러 Blob을 많은 서버에 분산하여 액세스를 확장할 수 있지만, 단일 Blob은 단일 서버에서만 제공할 수 있습니다. Blob 컨테이너에서 blob를 논리적으로 그룹화하는 반면 해당 그룹화에는 파티션이 적용되지 않습니다.

또한 Azure Storage에는 Blob 저장소를 위한 WebHDFS API 계층도 있습니다.  HDInsight의 모든 서비스는 HDFS(Hadoop 분산 파일 시스템)를 사용하는 방식과 비슷하게 데이터 정리 및 데이터 처리를 위해 Azure Blob Storage의 파일에 액세스할 수 있습니다.

데이터는 일반적으로 PowerShell, Azure Storage SDK 또는 AZCopy를 사용하여 Azure Storage로 수집됩니다.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

ADLS(Azure Data Lake Store)는 HDFS와 호환되는 분석 데이터를 위한 관리되는 하이퍼스케일 리포지토리입니다.  ADLS는 HDFS와 비슷한 디자인 패러다임을 사용하며, 총 용량 및 개별 파일 크기 측면에서 무제한의 확장성을 제공합니다. ADLS는 큰 파일을 여러 노드에 걸쳐 저장할 수 있으므로 큰 파일로 작업할 때 매우 유용합니다.  ADLS에서 데이터를 분할하는 작업은 백그라운드에서 수행됩니다.  수백 테라바이트의 데이터를 효율적으로 읽고 쓰는 수천 개의 동시 실행기를 사용하여 분석 작업을 실행하는 대규모 처리량을 얻을 수 있습니다.

데이터는 일반적으로 Azure Data Factory, ADLS SDK, AdlCopy Service, Apache DistCp 또는 Apache Sqoop을 사용하여 ADLS로 수집됩니다.  이러한 서비스 중 주로 사용할 서비스는 데이터가 있는 위치에 따라 다릅니다.  데이터가 현재 기존 Hadoop 클러스터에 있는 경우 Apache DistCp, AdlCopy Service 또는 Azure Data Factory를 사용할 수 있습니다.  Azure Blob Storage에 있는 경우 Azure Data Lake Store .NET SDK, Azure PowerShell 또는 Azure Data Factory를 사용할 수 있습니다.

또한 ADLS는 Azure Event Hub 또는 Apache Storm을 사용하는 이벤트 수집에 최적화되어 있습니다.

#### <a name="considerations-for-both-storage-options"></a>두 저장소 옵션 모두에 대한 고려 사항

테라바이트 범위의 데이터 집합을 업로드하는 경우, 특히 데이터가 온-프레미스 위치에서 제공되면 네트워크 대기 시간이 중요한 문제가 될 수 있습니다.  이러한 경우 아래 옵션을 사용할 수 있습니다.

* Azure ExpressRoute: Azure ExpressRoute를 사용하면 Azure 데이터 센터와 온-프레미스 인프라 사이의 사설 연결을 만들 수 있습니다. 이러한 연결은 대량의 데이터를 전송할 때 신뢰할 수 있는 옵션을 제공합니다. 자세한 내용은 [Azure ExpressRoute 설명서](../../expressroute/expressroute-introduction.md)를 참조하세요.

* "오프라인" 데이터 업로드: [Azure Import/Export 서비스](../../storage/common/storage-import-export-service.md)를 사용하여 데이터가 포함된 하드 디스크를 Azure 데이터 센터로 보낼 수 있습니다. 데이터는 먼저 Azure Storage Blob에 업로드됩니다. 그런 다음 [Azure Data Factory](../../data-factory/v1/data-factory-azure-datalake-connector.md) 또는 [AdlCopy 도구](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)를 사용하여 Azure Storage Blob에서 Data Lake Store로 데이터를 복사할 수 있습니다.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW(SQL Data Warehouse)는 향후 분석을 위해 정리되고 준비된 결과를 저장하는 데 매우 적합합니다.  Azure HDInsight는 Azure SQL DW에 대해 이러한 서비스를 수행하는 데 사용할 수 있습니다.

Azure SQL DW는 분석 작업에 최적화된 관계형 데이터베이스 저장소입니다.  Azure SQL DW의 크기는 분할된 테이블에 따라 조정됩니다.  테이블은 여러 노드에 걸쳐 분할될 수 있습니다.  Azure SQL DW 노드는 테이블을 만들 때 선택됩니다.  사후에 크기를 조정할 수 있지만, 이 경우 데이터 이동이 필요할 수 있는 활성 프로세스입니다. 자세한 내용은 [SQL Data Warehouse - 계산 관리](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)를 참조하세요.

### <a name="hbase"></a>HBase

Apache HBase는 Azure HDInsight에서 사용할 수 있는 키-값 저장소입니다.  Apache HBase는 Hadoop을 기반으로 하고 Google BigTable 이후에 모델링된 오픈 소스 NoSQL 데이터베이스입니다. HBase는 열 패밀리로 구성되고 스키마가 없는 데이터베이스에서 대량의 비구조적/반구조적 데이터에 대해 고성능 임의 액세스 및 강력한 일관성을 제공합니다.

데이터는 테이블의 행에 저장되고 행 내의 데이터는 열 제품군으로 그룹화됩니다. HBase는 사용 전에 열과 열에 저장되는 데이터 형식을 정의할 필요가 없다는 점에서 스키마 없는 데이터베이스입니다. 오픈 소스 코드는 수천 대의 노드에 있는 페타바이트 크기의 데이터를 처리할 수 있을 정도로 선형으로 확장됩니다. HBase는 Hadoop 에코시스템의 분산 응용 프로그램에서 제공하는 데이터 중복, 일괄 처리 및 기타 기능을 사용할 수 있습니다.   

HBase는 향후 분석을 위한 센서 및 로그 데이터에 대한 뛰어난 대상입니다.

HBase 확장성은 HDInsight 클러스터의 노드 수에 따라 다릅니다.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database 및 Azure Database

Azure는 PAAS(Platform-as-a-Service)와 같은 별도의 세 가지 관계형 데이터베이스를 제공합니다.

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md)는 Microsoft SQL Server를 구현한 데이터베이스입니다. 성능에 대한 자세한 내용은 [Azure SQL Database에서 성능 튜닝](../../sql-database/sql-database-performance-guidance.md)을 참조하세요.
* [Azure Database for MySQL](../../mysql/overview.md)은 Oracle MySQL을 구현한 데이터베이스입니다.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md)은 PostgreSQL을 구현한 데이터베이스입니다.

이러한 제품은 강화되므로 CPU와 메모리를 추가하여 크기가 조정됩니다.  또한 I/O 성능을 향상시키기 위해 제품과 함께 프리미엄 디스크를 사용하도록 선택할 수도 있습니다.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

AAS(Azure Analysis Services)는 의사 결정 지원 및 비즈니스 분석에 사용되는 분석 데이터 엔진으로, Power BI, Excel, Reporting Services 보고서 및 다른 데이터 시각화 도구와 같은 비즈니스 보고서 및 클라이언트 응용 프로그램에 대한 분석 데이터를 제공합니다.

분석 큐브의 크기는 개별 큐브 각각에 대한 계층을 변경하여 조정할 수 있습니다.  자세한 내용은 [Azure Analysis Services 가격](https://azure.microsoft.com/pricing/details/analysis-services/)을 참조하세요.

## <a name="extract-and-load"></a>추출 및 로드

Azure에 데이터가 있으면 많은 서비스를 사용하여 이를 추출하여 다른 제품에 로드할 수 있습니다.  HDInsight는 Sqoop 및 Flume을 지원합니다. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop은 구조화, 반구조화 및 비구조화된 데이터 원본 간에 데이터를 효율적으로 전송하도록 설계된 도구입니다. 

Sqoop은 MapReduce를 사용하여 데이터를 가져오고 내보내고, 병렬 작업과 내결함성을 제공합니다.

### <a name="flume"></a>Flume

Apache Flume은 대량의 로그 데이터를 효율적으로 수집, 집계 및 이동하기 위해 안정적으로 사용할 수 있는 분산 서비스입니다. Flume은 스트리밍 데이터 흐름에 기반한 간단하고 유연한 아키텍처입니다. Flume은 튜닝할 수 있는 안정성 메커니즘과 다양한 장애 조치 및 복구 메커니즘을 통해 내결함성이 있는 강력한 도구입니다. Flume은 온라인 분석 응용 프로그램을 허용하는 확장 가능한 간단한 데이터 모델을 사용합니다.

Apache Flume은 Azure HDInsight와 함께 사용할 수 없습니다.  온-프레미스 Hadoop 설치에서는 Flume을 사용하여 Azure Storage Blob 또는 Azure Data Lake Store에 데이터를 보낼 수 있습니다.  자세한 내용은 [HDInsight와 함께 Apache Flume 사용](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)을 참조하세요.

## <a name="transform"></a>변환

선택한 위치에 데이터가 있으면 특정 사용 패턴에 맞게 데이터를 정리, 결합 또는 준비해야 합니다.  Hive, Pig 및 Spark SQL은 모두 이러한 종류의 작업에 적합합니다.  HDInsight에서는 이러한 도구를 모두 지원합니다. 

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [Apache Hive를 ETL 도구로 사용](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
