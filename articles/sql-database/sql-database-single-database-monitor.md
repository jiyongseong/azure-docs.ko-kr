---
title: Azure SQL Database에서 데이터베이스 성능 모니터링 | Microsoft Docs
description: Azure 도구 및 동적 관리 뷰를 사용하여 데이터베이스를 모니터링하기 위한 옵션에 대해 알아봅니다.
keywords: 데이터베이스 모니터링, 클라우드 데이터베이스 성능
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e56a40e1afef9c622d14804cb7f845b7b6edae6a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192624"
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Azure SQL Database에서 데이터베이스 성능 모니터링
Azure에서 SQL 데이터베이스의 성능 모니터링은 데이터베이스에 대해 선택한 데이터베이스 성능 수준을 기준으로 리소스 사용률을 모니터링하는 것으로 시작합니다. 모니터링을 통해 데이터베이스에 과도한 용량이 있는지, 리소스가 최대값에 도달하는 문제가 있는지 확인한 다음, 데이터베이스의 성능 수준 및 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 또는 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)의 서비스 계층을 조정할 시기인지 여부를 결정할 수 있습니다. [Azure 포털](https://portal.azure.com)에서 그래픽 도구를 사용하거나 SQL [동적 관리 뷰](https://msdn.microsoft.com/library/ms188754.aspx)를 사용하여 데이터베이스를 모니터링할 수 있습니다.

> [!TIP]
> 데이터베이스 성능 자동 모니터링에 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)를 사용합니다. 성능 문제가 검색되면 문제의 세부 정보와 RCA(근본 원인 분석)가 포함된 진단 로그가 생성됩니다. 성능 개선 권장 향상이 제공됩니다(가능한 경우).
>

## <a name="monitor-databases-using-the-azure-portal"></a>Azure 포털을 사용하여 데이터베이스 모니터링
[Azure 포털](https://portal.azure.com/)에서 데이터베이스를 선택하고 **모니터링** 차트를 클릭하여 단일 데이터베이스의 사용률을 모니터링할 수 있습니다. 그러면 **메트릭** 창이 나타나며 **차트 편집** 단추를 클릭하면 이 창을 변경할 수 있습니다. 다음 메트릭을 추가합니다.

* CPU 비율
* DTU 비율
* 데이터 IO 비율
* 데이터베이스 크기 비율

이러한 메트릭을 추가한 후 **메트릭** 창의 세부 정보가 포함된 **모니터링** 차트에서 메트릭을 계속 볼 수 있습니다. 네 가지 메트릭 모두 데이터베이스의 **DTU** 를 기준으로 평균 사용률 비율을 표시합니다. 서비스 계층에 대한 자세한 내용은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md) 문서를 참조하세요.  

![데이터베이스 성능의 서비스 계층 모니터링.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

성능 메트릭에 대한 경고를 구성할 수도 있습니다. **메트릭** 창에서 **경고 추가** 단추를 클릭합니다. 마법사를 따라 경고를 구성합니다. 메트릭이 특정 임계값을 초과하거나 메트릭이 특정 임계값보다 낮은 경우 경고하는 옵션이 있습니다.

예를 들어 데이터베이스에 대한 워크로드가 확장될 것으로 예상되면 데이터베이스가 성능 메트릭의 80%에 도달할 때마다 이메일 경고를 보내도록 구성할 수 있습니다. 다음 높은 성능 수준으로 전환해야 할 수 있는 시기를 알 수 있도록 이 기능을 조기 경고로 사용할 수 있습니다.

성능 메트릭이 더 낮은 성능 수준으로 다운그레이드할 수 있는지 여부를 판단하는 데 도움이 될 수도 있습니다. 표준 S2 데이터베이스를 사용하고 있는데 모든 성능 메트릭에서 지정한 시기에 데이터베이스가 평균적으로 10% 이하를 사용하는 것으로 나타난다고 가정합니다. 데이터베이스가 표준 S1에서 잘 작동할 가능성이 있습니다. 그러나 더 낮은 성능 수준으로 이동하도록 결정하기 전에 갑자기 증가하거나 변동하는 워크로드에 주의해야 합니다.

## <a name="monitor-databases-using-dmvs"></a>DMV를 사용하여 데이터베이스 모니터링
포털에 노출된 것과 같은 메트릭을 서버의 논리 **마스터** 데이터베이스에 있는 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx), 사용자 데이터베이스의 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 등의 시스템 뷰를 통해 사용할 수도 있습니다. 더 오랜 개간 동안 덜 자세한 데이터를 모니터링해야 하는 경우 **sys.resource_stats**를 사용합니다. 더 작은 시간 범위의 자세한 데이터를 모니터링해야 하는 경우 **sys.dm_db_resource_stats**를 사용합니다. 자세한 내용은 [Azure SQL Database 성능 지침](sql-database-single-database-monitor.md#monitor-resource-use)을 참조하세요.

> [!NOTE]
> **sys.dm_db_resource_stats**는 Web 및 Business Edition 데이터베이스에서 사용할 때 빈 결과 집합을 반환합니다.
>
>

### <a name="monitor-resource-use"></a>리소스 사용 모니터링

[SQL Database Query Performance Insight](sql-database-query-performance.md) 및 [쿼리 저장소](https://msdn.microsoft.com/library/dn817826.aspx)를 사용하여 리소스 사용량을 모니터링할 수 있습니다.

또한 다음 두 가지 뷰를 사용하여 사용을 모니터링할 수도 있습니다.

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
모든 SQL Database에 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 뷰를 사용할 수 있습니다. **sys.dm_db_resource_stats** 뷰는 서비스 계층과 관련된 최근 리소스 사용 데이터를 보여 줍니다. CPU, 데이터 IO, 로그 쓰기 및 메모리의 평균 백분율을 15초마다 기록되고 1시간 동안 유지됩니다.

이 뷰는 리소스 사용률에 대한 훨씬 구체적인 정보를 제공하므로 현재 상태 분석 또는 문제 해결에 **sys.dm_db_resource_stats**를 먼저 사용해야 합니다. 예를 들어 이 쿼리는 지난 시간 동안 현재 데이터베이스의 평균 및 최대 리소스 사용률을 보여 줍니다.

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

다른 쿼리는 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)의 예를 참조하세요.

#### <a name="sysresourcestats"></a>sys.resource_stats
**마스터** 데이터베이스의 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 뷰에는 특정 서비스 계층 및 성능 수준에서 SQL Database의 성능 수준을 모니터링할 수 있는 추가 정보가 포함되어 있습니다. 데이터는 5분마다 수집되어 약 14일 동안 보관됩니다. 이 뷰는 SQL Database가 리소스를 사용하는 방법에 대한 장기적인 기록 분석에 유용합니다.

다음 그래프는 한 주 동안 각 시간당 P2 성능 수준의 Premium 데이터베이스에 대한 CPU 리소스 사용률을 보여 줍니다. 이 그래프는 월요일부터 5근무일과 응용 프로그램 사용량이 훨씬 적은 주말까지 표시되어 있습니다.

![SQL Database 리소스 사용](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

이 데이터베이스의 현재 최고 CPU 부하는 P2 성능 수준에 비해 약 50% 더 많습니다(화요일 낮). CPU가 응용 프로그램의 리소스 프로필에서 가장 지배적인 요인인 경우 P2가 항상 워크로드를 충족하는 데 적합한 성능 수준임을 확인할 수 있습니다. 응용 프로그램이 시간이 지남에 따라 성장할 것으로 예상되는 경우 응용 프로그램이 성능 수준 한도에 도달하지 않도록 추가 리소스 버퍼를 두는 것이 좋습니다. 성능 수준을 높이면 특히 대기 시간이 중요한 환경에서 데이터베이스가 요청을 효과적으로 처리하므로 충분한 능력을 갖고 있지 않은 경우 발생할 수 있는 고객에게 보이는 오류를 방지하는 데 도움이 될 수 있습니다. 예를 들어 데이터베이스 호출의 결과를 기반으로 웹 페이지를 표시하는 응용 프로그램을 지원하는 데이터베이스가 있습니다.

다른 응용 프로그램 유형은 동일한 그래프를 다르게 해석할 수 있습니다. 예를 들어 응용 프로그램에서 매일 급여 데이터를 처리하고 동일한 차트를 사용하는 경우와 같은 "일괄 처리 작업" 모델은 P1 성능 수준으로 충분할 수 있습니다. P2 성능 수준은 200 DTU이지만 P1 성능 수준은 100 DTU입니다. P1 성능 수준은 P2 성능 수준의 절반의 성능만 제공합니다. 따라서 P2에서 CPU 사용의 50%는 P1에서 100% CPU 사용과 같습니다. 응용 프로그램에 시간 제한이 없는 경우 작업이 오늘 완료되기만 한다면 2시간이 소요되든 또는 2.5시간이 소요되든 중요하지 않을 수 있습니다. 이 범주의 응용 프로그램은 아마 P1 성능 수준을 사용할 것입니다. 하루 중 리소스 사용량이 낮은 시간대가 있다는 사실을 활용할 수 있습니다. 즉, "최고" 시간대의 작업을 하루 중 사용량이 낮은 시간대 중 하나로 나눌 수 있습니다. 작업을 매일 정시에 완료할 수 있는 경우 이러한 종류의 응용 프로그램에는 P1 성능 수준이 적합하며 비용도 절감할 수 있습니다.

Azure SQL Database는 각 서버에 있는 **마스터** 데이터베이스의 **sys.resource_stats** 뷰로 각 활성 데이터베이스에 사용된 리소스 정보를 표시합니다. 표의 데이터는 5분 간격으로 집계되어 있습니다. Basic, Standard, Premium 서비스 계층에서 데이터가 테이블에 표시될 때까지 5분 이상이 소요될 수 있어 이 데이터는 거의 실시간에 가까운 분석보다 기록 분석에 더 적합합니다. **sys.resource_stats** 뷰에 대한 쿼리는 데이터베이스의 최근 기록을 보여주며 선택한 예약이 필요 시 원하는 성능을 제공했는지 여부를 검증합니다.

> [!NOTE]
> 다음 예제에서 **sys.resource_stats**를 쿼리하려면 논리 SQL Database 서버의 **마스터** 데이터베이스에 연결해야 합니다.
> 
> 

이 예제에서는 이 뷰의 데이터가 표시되는 방법을 보여줍니다.

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![sys.resource_stats 카탈로그 뷰](./media/sql-database-performance-guidance/sys_resource_stats.png)

다음 예제에서는 **sys.resource_stats** 카탈로그 뷰를 사용하여 SQL Database에서 리소스를 사용하는 방법에 대한 정보를 얻을 수 있는 다른 방법을 보여줍니다.

1. 데이터베이스 userdb1에서 지난 주의 리소스 사용을 확인하고자 할 때 이 쿼리를 실행할 수 있습니다.
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. 워크로드가 성능 수준에 얼마나 적합한지 평가하려면 리소스 메트릭의 각 측면(CPU, 읽기, 쓰기, 작업자 수, 세션 수)까지 집중 분석해야 합니다. 다음은 이러한 리소스 메트릭의 평균값 및 최대값에 대해 보고하기 위해 **sys.resource_stats**를 사용하여 수정한 쿼리입니다.
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. 각 리소스 메트릭의 평균값 및 최대값에 대한 이 정보를 사용하여 워크로드가 선택한 성능 수준에 얼마나 적합한지 평가할 수 있습니다. 일반적으로 **sys.resource_stats**의 평균값은 대상 크기에 맞게 사용하기에 적합한 기준선을 제공합니다. 기본 측정 기준이 되어야 합니다. 예를 들어 S2 성능 수준과 함께Standard 서비스 계층을 사용할 수 있습니다. CPU 및 IO 읽기와 쓰기에 대한 평균 사용 비율은 40% 미만, 평균 작업자 수는 50 미만, 평균 세션 수는 200 미만입니다. 사용자의 워크로드는 S1 성능 수준에 적합할 수 있습니다. 데이터베이스가 작업자 및 세션 한도 이내에서 적합한지 여부를 쉽게 확인할 수 있습니다. 데이터베이스가 CPU, 읽기, 쓰기 기준의 낮은 성능 수준에 적합한지 확인하려면 낮은 성능 수준의 DTU 수를 현재 성능 수준의 DTU 수로 나눈 다음 결과에 100을 곱합니다.
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    결과는 백분율로 표시한 두 성능 수준 간 상대적 성능 차이입니다. 리소스 사용이 이 금액을 초과하지 않는 경우 워크로드가 낮은 성능 수준에 적합할 수 있습니다. 하지만 모든 범위의 리소스 사용 값을 살펴보고 데이터베이스 워크로드가 낮은 성능 수준에 적합한 빈도를 백분율로 확인해야 합니다. 다음 쿼리는 이 예에서 계산된 40%의 임계값을 기준으로 리소스 규격당 적합률을 출력합니다.
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    데이터베이스의 SLO(서비스 수준 목표)를 기준으로 워크로드가 낮은 성능 수준에 적합한지 여부를 결정할 수 있습니다. 데이터베이스 워크로드 SLO가 99.9%이고 앞의 쿼리에서 세 가지 리소스 규격에 대해 99.9%보다 큰 값을 반환할 경우 워크로드가 낮은 성능 수준에 적합할 가능성이 높습니다.
   
    적합률을 살펴보면 SLO를 충족하기 위해 상위 성능 수준으로 이동해야 하는지 여부를 알 수 있습니다. 예를 들어 userdb1에서 지난 주에 대한 CPU 사용률은 다음과 같습니다.
   
   | 평균 CPU 사용률 | 최대 CPU 사용률 |
   | --- | --- |
   | 24.5 |100.00 |
   
    평균 CPU는 성능 수준 한도의 약 1/4이며 데이터베이스 성능 수준에 적합합니다. 하지만 최대값은 데이터베이스가 성능 수준 한도에 도달함을 보여줍니다. 다음 상위 성능 수준으로 이동해야 하나요? 워크로드가 100%에 도달하는 횟수를 살펴보고 데이터베이스 워크로드 SLO와 비교합니다.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    이 쿼리에서 세 가지 리소스 규격에 대해 99.9% 미만의 값을 반환할 경우 다음 상위 성능 수준으로 이동하거나 응용 프로그램 튜닝 기술을 사용하여 SQL Database에서 부하를 줄입니다.
4. 이 연습에서는 향후 예상되는 워크로드 증가도 고려합니다.

탄력적 풀의 경우 이 섹션에서 설명하는 기법을 사용하여 풀의 개별 데이터베이스를 모니터링할 수 있습니다. 하지만 전체적으로 풀을 모니터링할 수도 있습니다. 자세한 내용은 [탄력적 풀 모니터링 및 관리](sql-database-elastic-pool-manage-portal.md)를 참조하세요.


### <a name="maximum-concurrent-requests"></a>동시 요청이 최대인 경우
동시 요청 수를 보려면 SQL Database에서 이 Transact-SQL 쿼리를 실행하세요.

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

온-프레미스 SQL Server 데이터베이스의 워크로드를 분석하려면 이 쿼리를 수정하여 분석할 특정 데이터베이스에서 필터링해야 합니다. 예를 들어 MyDatabase라는 온-프레미스 데이터베이스가 있다면 이 Transact-SQL 쿼리는 해당 데이터베이스의 동시 요청 수를 반환합니다.

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

이는 단일 시점의 스냅숏일 뿐입니다. 워크로드 및 동시 요청 요구 사항을 더 깊이 이해하려면 시간 변화에 따라 여러 샘플을 수집해야 합니다.

### <a name="maximum-concurrent-logins"></a>최대 동시 로그인 수
사용자 및 응용 프로그램 패턴을 분석하여 로그인 빈도를 파악할 수 있습니다. 또한 테스트 환경에서 실제 부하를 실행하여 이 문서에서 설명한 이 한도 또는 기타 한도에 도달하지 않도록 보장할 수 있습니다. 동시 로그인 수 또는 기록을 표시할 수 있는 단일 쿼리 또는 동적 관리 뷰(DMV)는 없습니다.

여러 클라이언트에서 동일한 연결 문자열을 사용하는 경우 서비스에서는 각 로그인을 인증합니다. 10명의 사용자가 동일한 사용자 이름 및 암호를 사용하여 데이터베이스에 동시에 연결하는 경우 동시 로그인 수는 10개가 됩니다. 이 한도는 로그인 및 인증 기간에만 적용됩니다. 따라서 동일한 10명의 사용자가 특정 데이터베이스에 순차적으로 연결하는 경우 동시 로그인 수는 절대로 1보다 높을 수 없습니다.

> [!NOTE]
> 현재 이 한도는 탄력적 풀의 데이터베이스에는 적용되지 않습니다.
> 
> 

### <a name="maximum-sessions"></a>최대 세션 수
현재 활성 세션 수를 보려면 SQL Database에서 이 Transact-SQL 쿼리를 실행하세요.

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

온-프레미스 SQL Server 워크로드를 분석하려는 경우 특정 데이터베이스에 집중하도록 쿼리를 수정하세요. 이 쿼리는 데이터베이스를 Azure SQL Database로 이동을 고려할 때 가능한 세션 요구 사항을 확인하는 데 도움이 됩니다.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

역시 이러한 쿼리도 지정 시간 수를 반환합니다. 시간이 지남에 따라 여러 샘플을 수집하는 경우 자신의 세션 사용을 잘 이해해야 합니다.

SQL Database 분석의 경우 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 뷰를 쿼리하고 **active_session_count** 열을 검토하여 세션에 대한 통계 자료를 얻을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 자동 튜닝](sql-database-automatic-tuning.md)을 사용하여 데이터베이스 인덱스 및 쿼리 실행 계획을 자동으로 튜닝합니다.
- [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)를 사용하여 데이터베이스 성능을 자동으로 모니터링합니다. 이 기능은 성능 문제의 진단 정보 및 근본 원인 분석을 제공합니다.
