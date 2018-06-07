---
title: Data Lake Store를 다른 Azure 서비스와 통합 | Microsoft 문서
description: 데이터 레이크 저장소가 다른 Azure 서비스와 통합하는 방법을 이해합니다.
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e11100118f599b4fee6362625aff05c50bb07c87
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>데이터 레이크 저장소와 다른 Azure 서비스 통합
Azure 데이터 레이크 저장소를 다른 Azure 서비스와 함께 사용하면 광범위한 범위의 시나리오를 사용할 수 있습니다. 다음 문서에는 데이터 레이크 저장소와 통합될 수 있는 서비스가 나열되어 있습니다.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Azure HDInsight에 데이터 레이크 저장소 사용
데이터 레이크 저장소를 사용하는 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 클러스터를 HDFS 규격 저장소로 프로비전할 수 있습니다. 이 릴리스의 Windows 및 Linux의 Storm 및 Hadoop 클러스터의 경우 데이터 레이크 저장소를 추가 저장소로만 사용할 수 있습니다. 이러한 클러스트는 여전히 Azure Storage(WASB)를 기본 저장소로 사용합니다. 그러나 Windows 및 Linux의 HBase 클러스터의 경우에는 데이터 레이크 저장소를 기본 저장소나 추가 저장소 또는 둘 다로 사용할 수 있습니다.

데이터 레이크 저장소를 사용하는 HDInsight 클러스터를 프로비전하는 방법에 대한 지침은 다음을 참조하세요.

* [Azure Portal을 사용하여 데이터 레이크 저장소로 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure PowerShell을 사용하여 Data Lake Store를 기본 저장소로 사용하는 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Azure PowerShell을 사용하여 Data Lake Store를 추가 저장소로 사용하는 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용
[Azure 데이터 레이크 분석](../data-lake-analytics/data-lake-analytics-overview.md) 을 사용하면 클라우드 규모에서 빅 데이터 작업을 수행할 수 있습니다. 이를 통해 동적으로 리소스를 프로비전할 뿐만 아니라 지원되는 많은 데이터 원본(예: 데이터 레이크 저장소)에 저장될 수 있는 테라바이트 또는 심지어 엑사바이트의 데이터를 분석할 수 있습니다. 데이터 레이크 분석은 Azure 데이터 레이크 저장소에서 작업할 때 특히 최적화되어 빅 데이터 작업에 대한 최상의 성능, 처리량 및 병렬화를 제공합니다.

데이터 레이크 분석에 데이터 레이크 저장소를 사용하는 방법에 대한 지침은 [데이터 레이크 저장소를 사용하여 데이터 레이크 분석 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.

## <a name="use-data-lake-store-with-azure-data-factory"></a>Azure Data Factory에 데이터 레이크 저장소 사용
[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 를 사용하여 Azure 테이블, Azure SQL Database, Azure SQL Data Warehouse, Azure Storage Blob 및 온-프레미스 데이터베이스에서 데이터를 수집할 수 있습니다. Azure 에코시스템의 첫 번째 클래스가 된 Azure Data Factory를 사용하면 이러한 원본에서 Azure 데이터 레이크 저장소로 데이터를 수집하는 작업을 오케스트레이션할 수 있습니다.

Azure Data Factory에 데이터 레이크 저장소를 사용하는 방법에 대한 지침은 [Data Factory를 사용하여 데이터 레이크 저장소에서 데이터 이동](../data-factory/connector-azure-data-lake-store.md)을 참조하세요.

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Azure Storage Blob에서 데이터 레이크 저장소로 데이터 복사
Azure 데이터 레이크 저장소에서는 Azure Blob Storage에서 데이터 레이크 Storage 계정에 데이터를 복사할 수 있는 명령줄 도구인 AdlCopy를 제공합니다. 자세한 내용은 [Azure Storage Blob에서 Data Lake 저장소에 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)를 참조하세요.

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Azure SQL Database와 Data Lake 저장소 간에 데이터 복사
Apache Sqoop를 사용하여 Azure SQL Database와 Data Lake 저장소 간에 데이터를 가져오고 내보낼 수 있습니다. 자세한 내용은 [Sqoop를 사용하여 Data Lake 저장소와 Azure SQL 데이터베이스 간에 데이터 복사](data-lake-store-data-transfer-sql-sqoop.md)를 참조하세요.

## <a name="use-data-lake-store-with-stream-analytics"></a>Stream Analytics에 Data Lake 저장소 사용
Azure Stream Analytics를 사용하여 스트리밍되는 데이터를 저장할 출력 중 하나로 Data Lake 저장소를 사용할 수 있습니다. 자세한 내용은 [Azure Stream Analytics를 사용하여 Azure Storage Blob에서 Data Lake 저장소에 데이터 스트리밍](data-lake-store-stream-analytics.md)을 참조하세요.

## <a name="use-data-lake-store-with-power-bi"></a>Power BI로 Data Lake 저장소 사용
Power BI를 사용하여 Data Lake 저장소 계정의 데이터를 가져와서 데이터를 분석하고 시각화할 수 있습니다. 자세한 내용은 [Power BI를 사용하여 Data Lake 저장소의 데이터 분석](data-lake-store-power-bi.md)을 참조하세요.

## <a name="use-data-lake-store-with-data-catalog"></a>Data Catalog로 Data Lake 저장소 사용
Data Lake 저장소의 데이터를 Azure Data Catalog에 등록하여 조직 전체에서 데이터를 검색할 수 있도록 만들 수 있습니다. 자세한 내용은 [Azure Data Catalog에 Data Lake 저장소의 데이터 등록](data-lake-store-with-data-catalog.md)을 참조하세요.

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>SSIS(SQL Server Integration Services)에서 Data Lake Store 사용
SSIS에서 Azure Data Lake Store 연결 관리자를 사용하여 SSIS 패키지를 Azure Data Lake Store에 연결할 수 있습니다. 자세한 내용은 [SSIS에서 Data Lake Store 사용](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)을 참조하세요.

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>SQL Data Warehouse에서 Data Lake Store 사용
PolyBase를 사용하여 Azure Data Lake Store에서 SQL Data Warehouse로 데이터를 로드할 수 있습니다. 자세한 내용은 [SQL Data Warehouse에서 Data Lake Store 사용](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)을 참조하세요.

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Azure Event Hubs에 Data Lake Store 사용
Azure Data Lake Store를 사용하여 Azure Event Hubs에서 받은 데이터를 보관하고 캡처할 수 있습니다. 자세한 내용은 [Azure Event Hubs에 Data Lake Store 사용](data-lake-store-archive-eventhub-capture.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
* [포털을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)
* [PowerShell을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-powershell.md)  

