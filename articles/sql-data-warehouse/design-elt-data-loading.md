---
title: ETL 대신 Azure SQL Data Warehouse용 ELT 설계 | Microsoft Docs
description: ETL 대신 Azure SQL 데이터 웨어하우스 또는 데이터를 로드하기 위한 추출, 로드 및 변환(ELT) 프로세스를 설계합니다.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5ceb8cfd8efea66dbf17b8c522316b9a010e437d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대한 ELT(추출, 로드 및 변환) 설계

추출, 변환 및 로드(ETL) 대신 Azure SQL Data Warehouse로 데이터를 로드하기 위한 추출, 로드 및 변환(ELT) 프로세스를 설계합니다. 이 문서에서는 Azure 데이터 웨어하우스로 데이터를 이동하는 ELT 프로세스를 설계하는 방법을 소개합니다.

## <a name="what-is-elt"></a>ELT란 무엇인가요?

ELT(추출, 로드 및 변환)는 원본 시스템에서 대상 데이터 웨어하우스로 데이터가 이동하는 프로세스입니다. 이 프로세스는 새로 생성된 데이터를 데이터 웨어하우스로 가져오기 위해 정기적으로 수행됩니다. 원본에서 데이터 웨어하우스로 데이터를 가져오는 이상적인 방법은 PolyBase를 사용하여 데이터를 SQL Data Warehouse로 로드하는 ELT 프로세스를 개발하는 것입니다.

ELT는 먼저 로드한 다음 변환하는 반면, ETL(추출, 변환 및 로드)은 데이터를 로드하기 전에 변환합니다. ETL 대신 ELT를 수행하면 로드되기 전에 데이터를 변환하기 위한 자체 리소스를 제공하는 비용을 절약할 수 있습니다. SQL Data Warehouse를 사용할 경우 ELT는 MPP 시스템을 사용하여 변환을 수행합니다.

SQL Data Warehouse용 ELT를 구현하기 위한 여러 가지 방법이 있지만 기본적인 단계는 다음과 같습니다.  

1. 원본 데이터를 텍스트 파일로 추출합니다.
2. Azure Blob 저장소 또는 Azure Data Lake Store에 데이터를 둡니다.
3. 로드할 데이터를 준비합니다.
2. PolyBase를 사용하여 데이터를 SQL Data Warehouse 준비 테이블로 로드합니다.
3. 데이터를 변환합니다.
4. 프로덕션 테이블에 데이터를 삽입합니다.


로드 자습서는 [PolyBase를 사용하여 Azure Blob Storage에서 Azure SQL Data Warehouse로 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)를 참조하세요.

자세한 정보는 [로드 패턴 블로그](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/)를 참조하세요. 

## <a name="options-for-loading-with-polybase"></a>PolyBase로 로드하기 위한 옵션

PolyBase는 T-SQL 언어를 통해 데이터베이스 외부의 데이터에 액세스하는 기술로, 데이터를 SQL Data Warehouse로 로드하기 위한 가장 좋은 방법입니다. PolyBase를 사용하면 데이터가 데이터 원본에서 직접 컴퓨팅 노드로 병렬로 로드됩니다. 

PolyBase로 데이터를 로드하려면 다음 로드 옵션 중 하나를 사용할 수 있습니다.

- [T-SQL을 이용한 PolyBase](load-data-from-azure-blob-storage-using-polybase.md): Azure Blob 저장소 또는 Azure Data Lake Store에 데이터가 있을 경우 효과적입니다. 로드 프로세스를 가장 잘 제어할 수 있지만, 외부 데이터 개체를 정의해야 합니다. 다른 방법에서는 원본 테이블을 대상 테이블에 매핑할 때 배후에서 이러한 개체를 정의합니다.  T-SQL 로드를 조정하려면 Azure Data Factory, SSIS 또는 Azure 함수를 사용할 수 있습니다. 
- [SSIS를 이용한 PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse): 원본 데이터가 SQL Server(SQL Server 온-프레미스 또는 클라우드)에 있을 경우 효과적입니다. SSIS는 대상 테이블 매핑에 대해 원본을 정의하고 로드를 조정합니다. 이미 SSIS 패키지가 있는 경우 새 데이터 웨어하우스 대상으로 작업하도록 패키지를 수정할 수 있습니다. 
- [ADF(Azure Data Factory)를 이용한 PolyBase](sql-data-warehouse-load-with-data-factory.md): 또 다른 오케스트레이션 도구입니다.  파이프라인을 정의하고 작업을 예약합니다. 
- [PolyBase와 Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md)는 SQL Data Warehouse 테이블에서 Databricks 데이터 프레임으로 데이터를 전송하고/전송하거나 Databricks 데이터 프레임에서 SQL Data Warehouse 테이블로 데이터를 씁니다.

### <a name="polybase-external-file-formats"></a>PolyBase 외부 파일 형식

PolyBase는 UTF-8 및 UTF-16으로 인코딩된 구분된 텍스트 파일에서 데이터를 로드합니다. 구분된 텍스트 파일 외에도 Hadoop 파일 형식 RC 파일, ORC 및 Parquet에서 로드됩니다. PolyBase는 Gzip 및 Snappy 압축 파일에서 데이터를 로드할 수 있습니다. PolyBase는 현재 확장 ASCII, 고정 너비 형식 및 중첩된 형식(예: WinZip, JSON 및 XML)을 지원하지 않습니다.

### <a name="non-polybase-loading-options"></a>PolyBase 외 로드 옵션
데이터가 PolyBase와 호환되지 않으면 [bcp](/sql/tools/bcp-utility) 또는 [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)를 사용할 수 있습니다. bcp는 Azure Blob 저장소를 거치지 않고도 SQL Data Warehouse에 직접 로드되며, 소규모 로드에만 사용됩니다. 이러한 옵션의 로드 성능은 PolyBase보다 훨씬 느립니다. 


## <a name="extract-source-data"></a>원본 데이터 추출

원본 시스템에서 데이터를 가져오는 것은 원본에 따라 다릅니다.  목표는 데이터를 구분된 텍스트 파일로 이동하는 것입니다. SQL Server를 사용 중인 경우 [bcp 명령줄 도구](/sql/tools/bcp-utility)를 사용하여 데이터를 내보낼 수 있습니다.  

## <a name="land-data-to-azure-storage"></a>Azure 저장소에 데이터 두기

Azure 저장소에 데이터를 두려면 [Azure Blob 저장소](../storage/blobs/storage-blobs-introduction.md) 또는 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)로 이동할 수 있습니다. 어느 위치에서든 데이터를 텍스트 파일에 저장해야 합니다. Polybase는 둘 중 하나에서 로드할 수 있습니다.

이들은 데이터를 Azure Storage로 이동하는 데 사용할 수 있는 도구 및 서비스입니다.

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 서비스는 네트워크 처리량, 성능 및 예측 가능성을 개선합니다. ExpressRoute는 Azure에 대한 전용 비공개 연결을 통해 데이터를 라우팅하는 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 데이터를 라우팅하지 않습니다. 이 연결은 공용 인터넷을 통한 일반적인 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.
- [AZCopy 유틸리티](../storage/common/storage-moving-data.md)는 공용 인터넷을 통해 Azure Storage로 데이터를 이동합니다. 이는 데이터 크기가 10TB 미만인 경우에 작동합니다. AZCopy를 사용하여 정기적으로 로드를 수행하려면 네트워크 속도를 테스트하여 가능한지 확인하세요. 
- [ADF(Azure Data Factory)](../data-factory/introduction.md)에는 로컬 서버에 설치할 수 있는 게이트웨이가 있습니다. 그런 다음 파이프라인을 만들어 로컬 서버에서 Azure Storage로 데이터를 이동할 수 있습니다. SQL Data Warehouse에 데이터 팩터리를 사용하려면 [SQL Data Warehouse로 데이터 로드](/azure/data-factory/load-azure-sql-data-warehouse)를 참조하세요.

## <a name="prepare-data"></a>데이터 준비

데이터를 SQL Data Warehouse에 로드하기 전에 저장소 계정에서 데이터를 준비하고 정리해야 할 수 있습니다. 데이터가 원본에 있는 동안, 데이터를 텍스트 파일로 내보낼 때 또는 Azure Storage로 이동한 후 데이터 준비를 수행할 수 있습니다.  가능한 경우 프로세스의 초기에 데이터로 작업하는 것이 가장 쉽습니다.  

### <a name="define-external-tables"></a>외부 테이블 정의
데이터를 로드하려면 먼저 데이터 웨어하우스에서 외부 테이블을 정의해야 합니다. PolyBase는 외부 테이블을 사용하여 Azure Storage의 데이터를 정의하고 액세스합니다. 외부 테이블은 일반 테이블과 유사합니다. 기본 차이점은 데이터 웨어하우스 외부에 저장된 데이터에 대한 외부 테이블 지점입니다. 

외부 테이블 정의에는 데이터 원본, 텍스트 파일 형식 및 테이블 정의를 지정하는 것이 포함됩니다. 다음은 필요한 T-SQL 구문 항목입니다.
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

외부 개체를 만드는 예는 로드 자습서의 [외부 테이블 만들기](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) 단계를 참조하세요.

### <a name="format-text-files"></a>텍스트 파일 형식 지정

외부 개체가 정의되면 텍스트 파일의 행을 외부 테이블 및 파일 형식 정의와 정렬해야 합니다. 텍스트 파일의 각 행에 있는 데이터는 테이블 정의와 정렬되어야 합니다.

텍스트 파일의 형식을 지정하려면:

- 데이터가 비관계형 원본에서 오는 경우 행과 열로 변환해야 합니다. 데이터가 관계형 원본에서 오건 비관계형 원본에서 오건, 데이터를 로드하려고 하는 테이블에 대한 열 정의와 정렬되도록 데이터를 변환해야 합니다. 
- SQL Data Warehouse 대상 테이블의 열 및 데이터 형식과 정렬되도록 텍스트 파일에서 데이터의 형식을 지정합니다. 외부 텍스트 파일의 데이터 형식과 데이터 웨어하우스 테이블의 데이터 형식이 정렬되지 않으면 로드 중에 행이 거부됩니다.
- 종결자로 텍스트 파일의 필드를 구분합니다.  원본 데이터에서 찾을 수 없는 문자 또는 문자 시퀀스를 사용해야 합니다. [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)으로 지정한 종결자를 사용하세요.

## <a name="load-to-a-staging-table"></a>준비 테이블에 로드
데이터 웨어하우스에 데이터를 가져오려면 먼저 데이터를 준비 테이블에 로드하는 것이 좋습니다. 준비 테이블을 사용하면 프로덕션 테이블을 방해하지 않고 오류를 처리할 수 있으며 프로덕션 테이블에서 롤백 작업을 실행하지 않을 수 있습니다. 또한 준비 테이블에서는 데이터를 프로덕션 테이블에 삽입하기 전에 SQL Data Warehouse를 사용하여 변환을 실행할 수 있는 기회도 제공합니다.

T-SQL을 사용하여 로드하려면 [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) T-SQL 문을 실행하세요. 이 명령은 select 문의 결과를 새 테이블에 삽입합니다. 이 문은 외부 테이블에서 선택할 경우 외부 데이터를 가져옵니다. 

다음 예제에서 ext.Date는 외부 테이블입니다. 모든 행을 dbo.Date라는 새 테이블로 가져옵니다.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>데이터 변환
데이터가 준비 테이블에 있는 동안, 워크로드에 필요한 변환을 수행하세요. 그런 다음 데이터를 프로덕션 테이블로 이동하세요.

## <a name="insert-data-into-production-table"></a>프로덕션 테이블에 데이터 삽입

INSERT INTO ... SELECT 문은 스테이징 테이블에서 영구 테이블로 데이터를 이동합니다. 

ETL 프로세스를 설계할 때 작은 테스트 샘플에서 프로세스를 실행해 보세요. 테이블에서 파일로 1000개 행을 추출하여 Azure로 이동한 다음 준비 테이블에 로드해 보세요. 

## <a name="partner-loading-solutions"></a>파트너 로드 솔루션
상당수의 파트너에게는 로드 솔루션이 있습니다. 자세한 내용을 알아보려면 [솔루션 파트너](sql-data-warehouse-partner-business-intelligence.md) 목록을 참조하세요. 

## <a name="next-steps"></a>다음 단계
설명서를 로드하려면 [데이터를 로드하기 위한 지침](guidance-for-loading-data.md)을 참조하세요.


