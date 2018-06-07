# [Data Lake Store 설명서](index.md)

# 개요
## [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
## [Azure Storage와 Azure Data Lake Store 비교](data-lake-store-comparison-with-blob-storage.md)
## [빅 데이터 처리를 위한 Azure Data Lake Store](data-lake-store-data-scenarios.md)
## [Azure Data Lake Store에서 작동하는 오픈 소스 응용 프로그램](data-lake-store-compatible-oss-other-applications.md)
## [Data Lake Store를 사용하는 모범 사례](data-lake-store-best-practices.md)

# 시작하기
## [포털 사용](data-lake-store-get-started-portal.md)
## [PowerShell 사용](data-lake-store-get-started-powershell.md)
## [Azure CLI 2.0 사용](data-lake-store-get-started-cli-2.0.md)


# 방법
## 데이터 로드 및 이동
### [Azure Data Factory 사용](../data-factory/load-azure-data-lake-store.md)
### [저장소 탐색기 사용](data-lake-store-in-storage-explorer.md)
### [AdlCopy 사용](data-lake-store-copy-data-azure-storage-blob.md)
### [DistCp 사용](data-lake-store-copy-data-wasb-distcp.md)
### [Sqoop 사용](data-lake-store-data-transfer-sql-sqoop.md)
### [오프라인 소스에서 데이터 업로드](data-lake-store-offline-bulk-data-upload.md)
### [지역 간 Azure Data Lake Store 마이그레이션](data-lake-store-migration-cross-region.md)

## 데이터 보호
### [보안 개요](data-lake-store-security-overview.md)
### [Data Lake Store에서 액세스 제어](data-lake-store-access-control.md)
### [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
### [암호화](data-lake-store-encryption.md)

## Data Lake Store를 사용하여 인증
### [인증 옵션](data-lakes-store-authentication-using-azure-active-directory.md)
### [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Java 사용](data-lake-store-end-user-authenticate-java-sdk.md)
#### [.NET SDK 사용](data-lake-store-end-user-authenticate-net-sdk.md)
#### [REST API 사용](data-lake-store-end-user-authenticate-rest-api.md)
#### [Python 사용](data-lake-store-end-user-authenticate-python.md)
### [서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Java 사용](data-lake-store-service-to-service-authenticate-java.md)
#### [.NET SDK 사용](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [REST API 사용](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Python 사용](data-lake-store-service-to-service-authenticate-python.md)

## Data Lake Store 사용
### 계정 관리 작업
#### [.NET SDK 사용](data-lake-store-get-started-net-sdk.md)
#### [REST API 사용](data-lake-store-get-started-rest-api.md)
#### [Python 사용](data-lake-store-get-started-python.md)
### 파일 시스템 작업
#### [.NET SDK 사용](data-lake-store-data-operations-net-sdk.md)
#### [Java SDK 사용](data-lake-store-get-started-java-sdk.md)
#### [REST API 사용](data-lake-store-data-operations-rest-api.md)
#### [Python 사용](data-lake-store-data-operations-python.md)

## 성능
### [Azure Data Lake Store에 대한 성능 조정 지침](data-lake-store-performance-tuning-guidance.md)
### [Azure Data Lake Store에서 PowerShell을 사용하기 위한 성능 조정 지침](data-lake-store-performance-tuning-powershell.md)
### [HDInsight의 Spark 및 Azure Data Lake Store에 대한 성능 조정 지침](data-lake-store-performance-tuning-spark.md)
### [HDInsight의 Hive 및 Azure Data Lake Store에 대한 성능 조정 지침](data-lake-store-performance-tuning-hive.md)
### [HDInsight의 MapReduce 및 Azure Data Lake Store에 대한 성능 조정 지침](data-lake-store-performance-tuning-mapreduce.md)
### [HDInsight의 Storm 및 Azure Data Lake Store에 대한 성능 조정 지침](data-lake-store-performance-tuning-storm.md)

## Azure 서비스와의 통합
### HDInsight 사용
#### [Azure Portal 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Azure PowerShell 사용(기본 저장소)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Azure PowerShell 사용(추가 저장소)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Azure 템플릿 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Azure VNET의 Vm에서 액세스](data-lake-store-connectivity-from-vnets.md)
### [Data Lake Analytics와 함께 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Azure Event Hubs와 함께 사용](data-lake-store-archive-eventhub-capture.md)
### [데이터 팩터리와 함께 사용](../data-factory/load-azure-data-lake-store.md)
### [Stream Analytics와 함께 사용](data-lake-store-stream-analytics.md)
### [Power BI와 함께 사용](data-lake-store-power-bi.md)
### [데이터 카탈로그와 함께 사용](data-lake-store-with-data-catalog.md)
### [SQL Data Warehouse의 PolyBase와 함께 사용](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [SQL Server Integration Services와 함께 사용](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [More Azure 통합 옵션](data-lake-store-integrate-with-other-services.md)

## 관리
### [진단 로그 액세스](data-lake-store-diagnostic-logs.md)
### [고가용성을 위한 설계](data-lake-store-disaster-recovery-guidance.md)

# 참고 자료
## [코드 샘플](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python(계정 관리)](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)
## [Python(파일 시스템 관리)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Azure CLI](https://docs.microsoft.com/cli/azure/dls)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/)
## [Data Lake Store 블로그](https://blogs.msdn.microsoft.com/azuredatalake/)
## [UserVoice에서 사용자 의견 제공](https://feedback.azure.com/forums/327234-data-lake)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [가격](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=data-lake-store)
## [스택 오버플로 포럼](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
