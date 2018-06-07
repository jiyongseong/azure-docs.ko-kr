---
title: Azure SQL Database 성능 튜닝 지침 | Microsoft Docs
description: Azure SQL Database 쿼리 성능을 높이기 위한 권장 사항 사용 방법에 대해 알아봅니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: c84104ac9094980d0e6d16b535dcf13c462a645a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195450"
---
# <a name="tuning-performance-in-azure-sql-database"></a>Azure SQL Database에서 성능 튜닝

Azure SQL Database는 데이터베이스의 성능을 개선하는 데 사용할 수 있는 [권장 사항](sql-database-advisor.md)을 제공하며 사용자는 Azure SQL Database를 통해 워크로드 성능을 개선하도록 [응용 프로그램에 대해 자동으로 조정](sql-database-automatic-tuning.md)하고 변경 사항을 적용할 수 있습니다.

권장 사항을 적용할 수 없고 성능 문제가 있는 경우 다음 방법으로 성능을 개선할 수 있습니다.
- [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 또는 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)에서 서비스 계층을 높이면 사용자의 데이터베이스에 더 많은 리소스가 제공됩니다.
- 응용 프로그램을 튜닝하고 성능을 향상시킬 수 있는 몇 가지 모범 사례를 적용합니다. 
- 데이터에 대해 보다 효율적으로 작동하도록 인덱스 및 쿼리를 변경하여 데이터베이스를 튜닝합니다.

[DTU 기반 모델 리소스 제한](sql-database-dtu-resource-limits.md) 및 [vCore 기반 모델 리소스 제한(미리 보기)](sql-database-vcore-resource-limits.md)이 사용자의 요구를 충족하는지를 사용자가 결정해야 하므로 수동적인 방법을 사용합니다. 그렇지 않으면 응용 프로그램 또는 데이터베이스 코드를 다시 작성하고 변경 내용을 배포해야 합니다.

## <a name="increasing-performance-tier-of-your-database"></a>데이터베이스의 성능 계층 늘리기

Azure SQL Database는 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md) 중에서 사용자가 선택할 수 있는 두 가지 구매 모델을 제공합니다. 각 서비스 계층에서는 사용자의 SQL Database가 사용할 수 있는 리소스를 엄격하게 분리하며 해당 서비스 수준의 예측 가능한 성능을 보장합니다. 이 문서에서는 응용 프로그램에 대한 서비스 계층을 선택하는 데 도움이 되는 지침을 제공합니다. 또한 Azure SQL Database를 활용하도록 응용 프로그램을 튜닝할 수 있는 방법도 설명합니다.

> [!NOTE]
> 이 문서는 Azure SQL Database의 단일 데이터베이스에 대한 성능 지침을 중심으로 살펴봅니다. 탄력적 풀과 관련된 성능 지침을 보려면 [탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요. 단, 이 문서의 많은 튜닝 권장 사항을 탄력적 풀의 데이터베이스에 적용하고 유사한 성능 이점을 얻을 수는 있습니다.
> 

* **기본**: 기본 서비스 계층은 각 데이터베이스에 시간 단위의 적절한 성능 예측 가능성을 제공합니다. Basic 데이터베이스에서는 여러 개의 동시 요청이 없는 작은 데이터베이스에 충분한 우수한 성능을 지원합니다. 다음은 기본 서비스 계층을 사용할 때 일반적인 사용 사례입니다.
  * **Azure SQL Database를 방금 시작한 경우**. 개발 중인 응용 프로그램은 높은 성능 수준이 필요하지 않은 경우가 많습니다. 기본 데이터베이스는 낮은 가격대의 이상적인 데이터베이스 개발 또는 테스트 환경입니다.
  * **사용자가 한 명인 데이터베이스를 가지고 있는 경우**. 사용자 한 명을 데이터베이스와 연결하는 응용 프로그램은 일반적으로 동시성과 성능 요구 사항이 높지 않습니다. 이러한 응용 프로그램에는 Basic 서비스 계층이 적합합니다.
* **표준**: 표준 서비스 계층은 향상된 성능 예측 가능성을 제공하며 작업 그룹 및 웹 응용 프로그램과 같이 여러 개의 동시 요청을 가진 데이터베이스에서 뛰어난 성능을 제공합니다. Standard 서비스 계층 데이터베이스를 선택하면 분 단위의 예측 가능한 성능을 기준으로 데이터베이스 응용 프로그램의 규모를 조정할 수 있습니다.
  * **데이터베이스에 여러 개의 동시 요청이 있는 경우**. 한 번에 사용자 두 명 이상을 서비스하는 응용 프로그램에는 일반적으로 더 높은 성능 수준이 필요합니다. 예를 들어, 여러 동시 쿼리를 지원하는 중소 규모의 IO 트래픽 요구 사항을 포함하는 작업 그룹 또는 웹 응용 프로그램은 표준 서비스 계층에 적합한 후보입니다.
* **프리미엄**: 프리미엄 서비스 계층은 각 프리미엄 또는 중요 비즈니스용(미리 보기) 데이터베이스에 대한 예측 가능한 성능을 초 단위로 제공합니다. 프리미엄 서비스 계층을 선택하면 해당 데이터베이스에 대한 최대 부하를 기준으로 데이터베이스 응용 프로그램의 규모를 조정할 수 있습니다. 계획은 대기 시간이 중요한 작업에서 작은 쿼리가 예상하는 것보다 더 오래 걸릴 수 있는 경우를 제거합니다. 이 모델은 최고 리소스 요구 사항, 성능 차이 또는 쿼리 대기 시간에 관한 강력한 문을 만들어야 하는 응용 프로그램에 대한 개발 및 제품 유효성 검사 주기를 크게 간소화할 수 있습니다. 대부분의 프리미엄 서비스 계층 사용 사례는 이러한 특성을 하나 이상 가지고 있습니다.
  * **높은 최고 부하**. 작업을 완료하기 위해 CPU, 메모리 또는 입력/출력(IO)을 많이 요구하는 응용 프로그램에는 높은 수준의 전용 성능이 필요합니다. 예를 들어 데이터베이스 작업이 장시간 많은 CPU 코어를 사용해야 하는 것으로 확인된 경우 Premium 서비스 계층이 적합합니다.
  * **동시 요청이 많은 경우**. 일부 데이터베이스 응용 프로그램은 트래픽 양이 많은 웹 사이트와 같이 많은 동시 요청을 지원합니다. Basic 및 Standard 서비스 계층은 데이터베이스당 동시 요청 수가 제한적입니다. 추가 연결이 필요한 응용 프로그램은 적절한 예약 크기를 선택하여 필요한 요청의 최대 수를 처리해야 합니다.
  * **낮은 대기 시간**. 일부 응용 프로그램은 데이터베이스에서 최소 시간의 응답을 보장해야 합니다. 광범위한 고객 작업의 일부로 특정 저장된 프로시저가 호출될 경우 시간의 99%인 20밀리초 이내에 해당 호출에서 반환해야 하는 요구 사항이 있을 수 있습니다. 이러한 유형의 응용 프로그램은 Premium 서비스 계층을 활용하여 필요한 컴퓨팅 성능의 가용성을 보장할 수 있습니다.

SQL Database에 필요한 서비스 수준은 각 리소스 규격의 최고 부하 요구 사항에 따라 다릅니다. 일부 응용 프로그램은 단일 리소스를 매우 적게 사용하는 반면 다른 리소스에 대한 요구 사항은 높습니다.

### <a name="service-tier-capabilities-and-limits"></a>서비스 계층 기능 및 한도

각 서비스 계층에서 필요한 용량에 대해서만 요금을 지불하는 유연성을 갖도록 성능 수준을 설정합니다. 워크로드가 변함에 따라 [용량을 높거나 낮게 조정](sql-database-service-tiers-dtu.md)할 수 있습니다. 예를 들어, 개학 전 쇼핑 시즌에 데이터베이스 워크로드가 많아질 경우 7월부터 9월까지 설정된 기간 동안 데이터베이스에 대한 성능 수준을 증가시킬 수 있습니다. 최대 시즌이 끝나면 성능 수준을 줄일 수 있습니다. 비즈니스의 계절성에 따라 클라우드 환경을 최적화하여 지불하는 비용을 최소화할 수 있습니다. 이 모델은 소프트웨어 개발 출시 주기에도 적합합니다. 테스트 팀은 테스트 실행 중 용량을 할당하고 테스트가 완료되면 용량을 해제할 수 있습니다. 용량 요청 방식에서는 필요할 때마다 용량에 대해 지불하며 거의 사용하지 않는 전용 리소스에 대한 비용 지출을 방지합니다.

### <a name="why-service-tiers"></a>왜 서비스 계층인가?
각 데이터베이스 워크로드는 다를 수 있지만 서비스 계층의 목적은 다양한 성능 수준에서 성능 예측 가능성을 제공하는 것입니다. 데이터베이스 리소스 요구사항이 큰 고객은 더 많은 전용 컴퓨팅 환경에서 작업할 수 있습니다.

## <a name="tune-your-application"></a>응용 프로그램 튜닝
기존 온-프레미스 SQL Server에서 초기 용량 계획 프로세스는 프로덕션 응용 프로그램의 실행 프로세스에서 분리된 경우가 많았습니다. 하드웨어 및 제품 라이선스를 먼저 구입하고 성능 튜닝을 나중에 수행합니다. Azure SQL Database를 사용하는 경우 응용 프로그램을 실행하고 튜닝하는 과정을 함께 사용하는 것이 좋습니다. 주문형 용량 지불 모델에서는 응용 프로그램에 대해 어림짐작한 미래 성장 계획(정확하지 않은 경우가 많음)을 기준으로 과도한 프로비전을 하지 않고, 응용 프로그램을 튜닝하여 현재 필요한 최소 리소스를 사용할 수 있습니다. 일부 고객은 응용 프로그램을 튜닝하지 않고 하드웨어 리소스의 과도한 프로비전을 선택할 수 있습니다. 사용량이 많은 기간 중에 주요 응용 프로그램을 변경하지 않으려면 이 방법이 좋은 생각일 수 있습니다. 하지만 응용 프로그램을 튜닝하면 Azure SQL Database에서 서비스 계층을 사용할 때 리소스 요구 사항을 최소화하고 월 청구 금액을 낮출 수 있습니다.

### <a name="application-characteristics"></a>응용 프로그램의 특성
Azure SQL Database 서비스 계층이 응용 프로그램의 성능 안정성과 예측 가능성을 향상하도록 설계되었지만 몇 가지 모범 사례에 따라 응용 프로그램을 튜닝하면 성능 수준에서 리소스를 더욱 효율적으로 활용할 수 있습니다. 대부분의 응용 프로그램이 단순히 높은 성능 수준 또는 서비스 계층으로 전환하여 성능을 크게 향상할 수 있는 반면, 일부 응용 프로그램은 더 높은 서비스 수준에서 이점을 얻으려면 추가 튜닝이 필요합니다. 성능 향상을 위해 이러한 특성을 가진 응용 프로그램에 대한 추가 응용 프로그램 튜닝을 고려하십시오.

* **"번잡한" 동작으로 인해 성능이 느려지는 응용 프로그램**. 번잡한 응용 프로그램은 네트워크 대기 시간에 민감한 과도한 데이터 액세스 작업을 만듭니다. 이러한 종류의 응용 프로그램은 SQL Database에 대한 데이터 액세스 작업 수를 줄이기 위한 수정이 필요할 수 있습니다. 예를 들어 임시 쿼리를 일괄 처리로 처리하거나 쿼리를 저장된 프로시저로 이동하는 등의 기법을 사용하여 응용 프로그램 성능을 개선할 수 있습니다. 자세한 내용은 [쿼리 일괄 처리](#batch-queries)를 참조하세요.
* **전체 단일 시스템에서 지원할 수 없는 집중적인 워크로드를 가진 데이터베이스**. 가장 높은 Premium 성능 수준의 리소스를 초과하는 데이터베이스는 워크로드를 확장하여 이점을 얻을 수 있습니다. 자세한 내용은 아래의 [교차-데이터베이스 분할](#cross-database-sharding) 및 [기능 분할](#functional-partitioning) 섹션을 참조하세요.
* **최적이 아닌 쿼리를 포함하고 있는 응용 프로그램**. 응용 프로그램, 특히 쿼리가 적합하게 튜닝되지 않은 데이터 액세스 계층의 응용 프로그램은 더 높은 성능 수준에서 이점을 얻을 수 없습니다. 예를 들어 WHERE 절이 없거나 인덱스가 누락되거나 통계가 오래된 쿼리가 있습니다. 이러한 응용 프로그램은 표준 쿼리 성능 튜닝 기술을 사용하는 것이 도움이 됩니다. 자세한 내용은 아래의 [인덱스 누락](#identifying-and-adding-missing-indexes) 및 [쿼리 튜닝 및 힌팅](#query-tuning-and-hinting) 섹션을 참조하세요.
* **최적이 아닌 데이터 액세스 설계를 포함하고 있는 응용 프로그램**. 교착 상태와 같이 본질적인 데이터 액세스 동시성 문제가 있는 응용 프로그램은 더 높은 성능 수준에서 이점을 얻을 수 없습니다. Azure 캐싱 서비스 또는 다른 캐싱 기술로 클라이언트 쪽에서 데이터를 캐싱하여 Azure SQL Database에 대한 왕복을 줄이는 것을 고려하십시오. [응용 프로그램 계층 캐싱](#application-tier-caching)을 참조하세요.

## <a name="tune-your-database"></a>데이터베이스 튜닝
이 섹션에서는 Azure SQL Database를 튜닝하여 응용 프로그램에서 최고의 성능을 달성하고 최저 성능 수준에서도 실행할 수 있는 몇 가지 기법에 대해 설명합니다. 이러한 기법 중 일부는 기존 SQL Server 튜닝의 모범 사례와 동일하지만 일부 기법은 Azure SQL Database에만 해당합니다. 경우에 따라 데이터베이스에 사용된 리소스를 조사하고 추가 튜닝 영역을 찾으면 기존 SQL Server 기법을 확장하여 Azure SQL Database에서도 사용할 수 있습니다.

### <a name="identify-performance-issues-using-azure-portal"></a>Azure Portal을 사용하여 성능 문제 식별
Azure Portal에서 제공되는 다음 도구는 SQL Database를 사용하여 성능 문제를 분석하고 해결하는 데 도움이 될 수 있습니다.

* [쿼리 성능 Insight](sql-database-query-performance.md)
* [SQL Database 관리자](sql-database-advisor.md)

Azure Portal에 이러한 도구와 사용 방법에 대한 자세한 정보가 있습니다. 먼저 Azure Portal의 도구를 사용하여 보다 효율적으로 문제를 진단 및 해결하려고 시도하는 것이 좋습니다. 특별한 경우 인덱스 누락 및 쿼리 튜닝을 위해 다음에 설명하는 수동 튜닝 방법을 사용할 것을 권장합니다.

[성능 모니터링](sql-database-single-database-monitor.md) 문서에서 Azure SQL Database의 문제를 식별하는 방법에 대해 자세히 알아보세요.

### <a name="identifying-and-adding-missing-indexes"></a>누락된 인덱스 식별 및 추가
OLTP 데이터베이스 성능의 일반적인 문제는 물리적 데이터베이스 설계와 관련되어 있습니다. 데이터베이스 스키마를 (부하 또는 데이터 볼륨에서) 대규모로 테스트하지 않고 설계 및 배송하는 경우가 많습니다. 하지만 쿼리 계획의 성능이 소규모에서는 만족할 만한 수준인 경우에도 프로덕션 수준의 데이터 볼륨을 처리할 경우 크게 저하될 수 있습니다. 이 문제의 가장 일반적인 원인은 필터 또는 쿼리의 다른 한도를 충족할 수 있는 적절한 인덱스가 없기 때문입니다. 인덱스 누락으로 인해 인덱스 검색으로도 충분한 상황에서도 테이블 검색을 수행하는 경우가 많습니다.

이 예제에서는 검색이 충분한 경우 선택한 쿼리 계획에 스캔을 사용합니다.

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![인덱스가 누락된 쿼리 계획](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database는 일반적인 인덱스 누락 조건을 찾아서 해결하는 데 도움이 될 수 있습니다. Azure SQL Database에 내장된 DMV는 인덱스로 쿼리 실행의 예상 비용을 크게 줄일 수 있는 쿼리 컴파일을 확인합니다. 쿼리 실행 중에는 SQL Database가 각 쿼리 계획이 실행된 빈도를 추적하며, 쿼리 계획 실행과 인덱스가 있을 경우 예상되는 쿼리 실행 간 예상 차이를 추적합니다. 이러한 DMV를 사용하여 물리적 데이터베이스 설계를 어떻게 변경해야 데이터베이스와 실제 워크로드의 전반적 워크로드 비용을 개선할 수 있을지 빠르게 추정할 수 있습니다.

이 쿼리를 사용하여 잠재적 인덱스 누락을 평가할 수 있습니다.

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

이 예제의 쿼리를 통해 얻은 권장 사항:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

쿼리를 만든 후 해당 동일한 SELECT 문에서 다른 계획(스캔 대신 검색을 사용)을 선택한 다음 계획을 더 효율적으로 실행합니다.

![수정된 인덱스가 있는 쿼리 계획](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

중요한 통찰력은 공유된 상용 시스템의 IO 용량은 전용 서버 시스템보다 제한적이라는 것입니다. 불필요한 IO를 최소화하여 Azure SQL Database 서비스 계층의 각 성능 수준의 DTU 내에서 시스템을 최대한 활용하는 것이 유리합니다. 물리적 데이터베이스 설계를 적절히 선택할 경우 개별 쿼리의 대기 시간 및 규모 단위당 처리할 수 있는 동시 요청의 처리량을 크게 개선하고 쿼리를 충족하는 데 필요한 비용을 최소화할 수 있습니다. 누락된 인덱스 DMV에 대한 자세한 내용은 [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)를 참조하세요.

### <a name="query-tuning-and-hinting"></a>쿼리 튜닝 및 힌팅
Azure SQL Database 내 쿼리 최적화 프로그램은 기존 SQL Server 쿼리 최적화 프로그램과 유사합니다. 쿼리 튜닝 및 쿼리 최적화 프로그램의 추론 모델 제한 이해에 대한 모범 사례의 대부분은 Azure SQL Database에도 적용됩니다. Azure SQL Database에서 쿼리를 튜닝하는 경우 집계 리소스 요구를 줄이는 추가적인 이점이 있습니다. 응용 프로그램이 낮은 성능 수준에서 실행될 수 있기 때문에 튜닝하지 않은 경우보다 더 낮은 비용으로 실행할 수 있습니다.

SQL Server에서 공통적으로 적용되고 Azure SQL Database에도 적용되는 예는 쿼리 최적화 프로그램이 매개 변수를 "확인"하는 방법입니다. 컴파일하는 동안 쿼리 최적화 프로그램이 매개 변수의 현재 값을 평가하여 더 최적인 쿼리 계획을 생성할 수 있는지 여부를 결정합니다. 이 전략으로 쿼리가 알려진 매개 변수 값이 없이 컴파일된 계획보다 훨씬 더 빨라질 수 있지만, 현재 이 전략은 SQL Server와 Azure SQL Database에서 모두 불완전하게 작동합니다. 매개 변수가 확인되지 않는 경우도 있고, 매개 변수가 확인되지만 생성된 계획이 워크로드의 전체 매개 변수 값 집합에 대해 최적이 아닌 경우도 있습니다. Microsoft는 더욱 의도적으로 지정하고 매개 변수 스니프의 기본 동작을 재정의할 수 있도록 쿼리 힌트(지침)를 포함합니다. 힌트를 사용하면 기본 SQL Server 또는 Azure SQL Database 동작이 특정 고객 워크로드에서 완벽하지 않은 사례를 해결할 수 있는 경우도 많이 있습니다.

다음 예제에서는 쿼리 프로세서가 성능 및 리소스 요구 사항에 대해 모두 최적이 아닌 계획을 생성하는 방법을 보여 줍니다. 또한 이 예에서는 쿼리 힌트를 사용하는 경우 SQL Database에 대한 쿼리 실행 시간 및 리소스 요구 사항을 줄일 수 있음을 보여 줍니다.

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

설정 코드는 기울어진 데이터 분포가 포함된 테이블을 만듭니다. 최적 쿼리 계획은 선택한 매개 변수에 따라 달라집니다. 아쉽게도 계획 캐싱 동작이 가장 일반적인 매개 변수 값을 기준으로 쿼리를 다시 컴파일하지 않는 경우도 있습니다. 따라서 다른 계획을 선택하는 것이 평균적으로 더 나을 수 있는 경우에도 최적이 아닌 계획이 많은 값에 대해 캐싱 및 사용될 수 있습니다. 이러한 경우 쿼리 계획은 하나의 특별 쿼리 힌트가 포함된 프로시저를 제외하고 두 개의 동일한 저장된 프로시저를 만듭니다.

**예(1부)**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**예(2부)**

(결과 원격 분석 데이터에서 결과가 구분되도록 예의 2부를 시작하기 전에 최소 10분 기다리는 것이 좋습니다.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

이 예의 각 부분은 (테스트 데이터 집합으로 사용하기에 충분한 부하를 생성하기 위해) 매개 변수가 있는 insert 문의 실행을 1000회 시도합니다. 저장된 프로시저를 실행할 경우 쿼리 프로세서는 첫 번째 컴파일 중 프로시저로 전달된 매개 변수 값을 검사합니다(매개 변수 "스니프"라고 함). 프로세서는 매개 변수 값이 다른 경우에도 결과 계획을 캐싱하고 나중의 호출에 사용합니다. 최적의 계획이 사용되지 않는 경우가 있을 수 있습니다. 쿼리가 최초로 컴파일되었을 때 구체적인 케이스보다 평균적인 케이스에 더 적합한 계획을 선택하도록 경우에 따라 최적화 프로그램을 조정해야 합니다. 이 예에서 초기 계획은 모든 행을 읽어서 매개 변수와 일치하는 각 값을 찾는 "스캔" 계획을 생성합니다.

![스캔 계획을 사용하여 쿼리 튜닝](./media/sql-database-performance-guidance/query_tuning_1.png)

여기서는 값 1을 사용하여 프로시저를 실행했기 때문에 결과 계획은 값 1에 대해 최적이지만 테이블의 나머지 값에 대해서는 최적이 아닙니다. 그 결과 계획이 더 느리게 수행되고 더 많은 리소스를 사용하기 때문에 각 계획을 무작위로 선택한 경우 원하던 것과 다를 수 있습니다.

`SET STATISTICS IO`을 `ON`로 설정하고 테스트를 실행하면 이 예의 논리적 스캔 작업은 백그라운드에서 수행됩니다. 계획에 의해 1,148개의 읽기가 수행된 것을 알 수 있습니다(평균 케이스가 단 한 개의 행만 반환하려는 경우 부족함).

![논리적 스캔을 사용하여 쿼리 튜닝](./media/sql-database-performance-guidance/query_tuning_2.png)

예제의 두 번째 부분은 쿼리 힌트를 사용하여 최적화 프로그램이 컴파일 프로세스 중 특정 값을 사용하도록 합니다. 이 경우 강제로 쿼리 프로세서가 매개 변수로 전달된 값을 무시하는 대신 `UNKNOWN`을 가정하게 합니다. 이는 테이블에서 평균적인 빈도가 포함된 값을 나타냅니다(기울이기 무시). 결과 계획은 더욱 빠른 검색 기반 계획으로, 예의 1부 계획보다 평균적으로 더 적은 리소스를 사용합니다.

![쿼리 힌트를 사용하여 쿼리 튜닝](./media/sql-database-performance-guidance/query_tuning_3.png)

**sys.resource_stats** 테이블의 영향(테스트를 실행하는 시간부터 데이터가 테이블을 채울 때까지 지연이 발생함)을 확인할 수 있습니다. 이 예제에서 1부는 22:25:00 기간 중 실행되었으며 2부는 22:35:00에 실행되었습니다. 이전 기간에는 같은 시간에 (계획 효율성 개선으로 인해) 이후 기간보다 더 많은 리소스를 사용했습니다.

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![쿼리 튜닝 예의 결과](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> 이 예제의 볼륨은 의도적으로 작게 만들었지만 최적이 아닌 매개 변수의 영향은 특히 큰 데이터베이스에서 크게 나타날 수 있습니다. 극한의 경우 그 차이는 빠른 케이스에서 몇 초, 느린 케이스에서 몇 시간이 될 수 있습니다.
> 
> 

**sys.resource_stats**를 검사하여 특정 테스트의 리소스가 다른 테스트보다 리소스를 더 많이 또는 더 적게 사용했는지 확인할 수 있습니다. 데이터를 비교할 때에는 **sys.resource_stats** 뷰에서 두 테스트가 동일한 5분 기간에 겹치지 않도록 테스트 시간을 구분합니다. 이 연습의 목표는 최대 리소스를 최소화하는 것이 아니라 사용된 총 리소스 양을 최소화하는 것입니다. 일반적으로 대기 시간의 코드를 최적화할 경우 리소스 소비가 감소합니다. 응용 프로그램을 변경해야 하는지, 그리고 변경 내용이 응용 프로그램에서 쿼리 힌트를 사용 중인 고객 경험에 부정적 영향을 미치지 않는지 확인하십시오.

워크로드에 반복되는 쿼리 집합이 포함된 경우 데이터베이스를 호스트하는 데 필요한 최소 리소스 크기 단위가 결정되므로 선택한 계획의 최적성을 파악하고 확인하는 것이 좋은 경우가 많습니다. 유효성을 검사한 후 가끔 계획을 다시 검사하면 성능이 저하되지 않도록 하는 데 도움이 됩니다. [쿼리 힌트(Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)에 대해 더 자세히 알아볼 수 있습니다.

### <a name="cross-database-sharding"></a>교차-데이터베이스 분할
Azure SQL Database는 상용 하드웨어에서 실행되므로 기존 온-프레미스 SQL Server 설치보다 단일 데이터베이스에 대한 용량 한도가 낮습니다. 일부 고객은 분할 기법을 사용하여 데이터베이스 작업이 Azure SQL Database의 단일 데이터베이스 한도를 초과할 경우 여러 데이터베이스에서 해당 작업을 분담하게 합니다. 현재 Azure SQL Database에 분할 기법을 사용하는 대부분의 고객은 여러 데이터베이스에서 단일 규격으로 데이터를 분할합니다. 이 방식에서는 OLTP 응용 프로그램이 스키마 내에서 하나의 행 또는 작은 행 그룹에만 적용되는 트랜잭션을 수행하는 경우가 많다는 점을 이해해야 합니다.

> [!NOTE]
> 이제 SQL Database는 분할을 지원하기 위한 라이브러리를 제공합니다. 자세한 내용은 [Elastic Database 클라이언트 라이브러리 개요](sql-database-elastic-database-client-library.md)를 참조하세요.
> 
> 

예를 들어 데이터베이스에 고객 이름, 주문, 주문 정보가 포함된 경우(SQL Server에 기본 제공된 기존 예제 Northwind 데이터베이스와 같이) 관련 주문 및 주문 정보가 있는 고객을 그룹화하여 이 데이터를 여러 데이터베이스로 분할할 수 있습니다. 그러면 고객의 데이터가 단일 데이터베이스에 유지된다는 것을 보장할 수 있습니다. 응용 프로그램은 다양한 고객을 데이터베이스로 분할하여 부하를 여러 데이터베이스로 효과적으로 나눕니다. 분할을 통해 고객은 최대 데이터베이스 크기 한도에 도달하지 않을 뿐만 아니라, 개별 데이터베이스가 해당 DTU에 적합한 이상 Azure SQL Database가 다양한 성능 수준의 한도보다 훨씬 큰 워크로드를 처리할 수도 있게 됩니다.

데이터베이스 분할을 사용해도 솔루션에 대한 집계 리소스 용량이 줄지 않지만, 여러 데이터베이스에 분담되는 매우 큰 솔루션을 지원할 때 매우 효과적입니다. 각 데이터베이스는 리소스 요구사항이 큰 매우 크고 "효과적인" 데이터베이스를 지원하기 위해 서로 다른 성능 수준에서 실행할 수 있습니다.

### <a name="functional-partitioning"></a>기능 분할
SQL Server 사용자는 단일 데이터베이스 내에 여러 기능을 결합하는 경우가 많습니다. 예를 들어 응용 프로그램에 매장 재고를 관리하는 논리가 포함되어 있을 경우 해당 데이터베이스에는 재고와 관련된 논리, 구매 주문서 추적, 저장된 프로시저, 그리고 월말 보고를 관리하는 인덱스가 되어 있거나 구체화된 뷰가 포함되어 있을 수 있습니다. 이 기법을 사용하면 데이터베이스에서 백업과 같은 작업을 쉽게 관리할 수 있지만 응용 프로그램의 모든 기능에서 최고 부하를 처리할 수 있도록 하드웨어 규모를 늘려야 합니다.

Azure SQL Database 내에서 확장형 아키텍처를 사용하는 경우 응용 프로그램의 다양한 기능을 여러 데이터베이스로 분할하는 것이 유리합니다. 이 기법을 사용하면 각 응용 프로그램은 독립적으로 확장됩니다. 응용 프로그램 사용량이 많아지면(데이터베이스의 부하 증가) 관리자가 응용 프로그램 내의 각 기능에 대해 독립적인 성능 수준을 선택할 수 있습니다. 이 아키텍처에서 한도에 도달할 경우 여러 시스템으로 부하를 분산하여 단일 상용 시스템이 처리할 수 있는 것보다 크게 응용 프로그램을 확장할 수 있습니다.

### <a name="batch-queries"></a>쿼리 일괄 처리
임시 쿼리를 대량으로 빈번하게 사용하여 데이터에 액세스하는 응용 프로그램의 경우 응용 프로그램 계층과 Azure SQL Database 계층 간 네트워크 통신에서 많은 응답 시간이 사용됩니다. 응용 프로그램과 Azure SQL Database가 동일한 데이터 센터 내에 있는 경우에도 데이터 액세스 작업 수가 많으면 그 사이의 네트워크 대기 시간이 커질 수 있습니다. 데이터 액세스 작업의 네트워크 왕복을 줄이기 위해 임시 쿼리를 일괄 처리하거나 저장된 프로시저로 컴파일하는 옵션을 사용하는 것을 고려하십시오. 임시 쿼리를 일괄 처리할 경우 복수 쿼리를 Azure SQL Database로 하나의 큰 일괄 처리로 한 번에 보낼 수 있습니다. 임시 쿼리를 저장된 프로시저로 컴파일하면 일괄 처리와 동일한 결과를 얻을 수 있습니다. 저장된 프로시저를 사용하면 Azure SQL Database에서 쿼리 계획을 캐싱하여 저장된 프로시저를 다시 사용할 수 있도록 하는 기회를 늘린다는 이점이 있습니다.

일부 응용 프로그램은 쓰기를 많이 사용합니다. 쓰기를 일괄 처리하는 방법을 고려하여 데이터베이스에서 총 IO 부하를 줄일 수 있는 경우도 있습니다. 흔히 이렇게 하려면 저장된 프로시저 및 임시 배치 내에서 트랜잭션을 자동 커밋하는 대신 명시적 트랜잭션을 사용하기만 하면 됩니다. 사용 가능한 다양한 기법을 평가하려면 [Azure에서 SQL Database 응용 프로그램의 일괄 처리 기법](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)을 참조하세요. 자신의 고유한 워크로드를 가지고 실험하여 일괄 처리에 적합한 모델을 찾으세요. 모델의 트랜잭션 일관성 보증이 조금 다를 수 있다는 것을 이해해야 합니다. 리소스 사용을 최소화하는 올바른 워크로드를 찾으려면 일관성과 성능 사이의 올바른 조합을 찾아야 합니다.

### <a name="application-tier-caching"></a>응용 프로그램 계층 캐싱
일부 데이터베이스 응용 프로그램에는 읽기 작업이 많은 워크로드가 포함되어 잇습니다. 캐싱 계층을 통해 데이터베이스의 부하를 줄이고 Azure SQL Database를 사용하여 데이터베이스를 지원하는 데 필요한 성능 수준을 줄일 수 있습니다. [Azure Redis Cache](https://azure.microsoft.com/services/cache/)를 사용하면 읽기 작업이 많은 워크로드에서 데이터를 한 번만(또는 구성 방식에 따라 응용 프로그램 계층 시스템당 한 번만) 읽고 해당 데이터를 Azure SQL Database 밖에 저장할 수 있습니다. 그러면 데이터베이스 부하(CPU 및 읽기 IO)가 감소하지만, 캐시에서 읽는 데이터가 데이터베이스에 있는 데이터와 동기화되지 않을 수 있어 트랜잭션 일관성에 영향을 미칠 수 있습니다. 많은 응용 프로그램에서 비일관성이 어느 정도 허용되지만 모든 워크로드에 대해 허용되는 것은 아닙니다. 응용 프로그램 계층 캐싱 전략을 구현할 경우 응용 프로그램 요구 사항을 완전히 이해해야 합니다.

## <a name="next-steps"></a>다음 단계
* DTU 기반 서비스 계층에 대한 자세한 내용은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [DTU 기반 모델 리소스 제한](sql-database-dtu-resource-limits.md)을 참조하세요.
* vCore 기반 서비스 계층에 대한 자세한 내용은 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md) 및 [vCore 기반 모델 리소스 제한(미리 보기)](sql-database-vcore-resource-limits.md)을 참조하세요.
* 탄력적 풀에 대한 자세한 내용은 [Azure 탄력적 풀이란?](sql-database-elastic-pool.md)을 참조하세요.
* 성능 및 탄력적 풀에 대한 자세한 내용은 [탄력적 풀을 고려 하는 경우](sql-database-elastic-pool-guidance.md)

