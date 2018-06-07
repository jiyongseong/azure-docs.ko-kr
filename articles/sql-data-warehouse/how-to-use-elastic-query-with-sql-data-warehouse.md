---
title: 탄력적 쿼리 - Azure SQL Database에서 Azure SQL Data Warehouse의 데이터 액세스 | Microsoft Docs
description: 탄력적 쿼리를 사용하여 Azure SQL Database에서 Azure SQL Data Warehouse의 데이터에 액세스하기 위한 모범 사례에 대해 알아봅니다.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: jrj
ms.openlocfilehash: 909271792b73b5fdc517847db7cfd6c8cf2092bc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Azure SQL Database에서 탄력적 쿼리를 사용하여 Azure SQL Data Warehouse의 데이터에 액세스하기 위한 모범 사례
탄력적 쿼리를 사용하여 Azure SQL Database에서 Azure SQL Data Warehouse의 데이터에 액세스하기 위한 모범 사례에 대해 알아봅니다. 

## <a name="what-is-an-elastic-query"></a>탄력적 쿼리란?
탄력적 쿼리를 사용하면 T-SQL 및 외부 테이블을 사용하여 Azure SQL Data Warehouse에 원격으로 전송되는 쿼리를 Azure SQL Database에서 작성할 수 있습니다. 시나리오에 따라 이 기능을 사용하여 비용을 절감하고 더 성능 높은 아키텍처를 구현할 수 있습니다.

이 기능을 통해 두 가지 기본 시나리오가 가능합니다.

1. 도메인 격리
2. 원격 쿼리 실행

### <a name="domain-isolation"></a>도메인 격리

도메인 격리란 클래식 데이터 마트 시나리오를 말합니다. 이런 시나리오에서는 다음을 망라한 여러 가지 이유로 나머지 데이터 웨어하우스와 격리된 다운스트림 사용자에게 데이터의 논리적 도메인을 제공하고자 할 수 있습니다.

1. 리소스 격리 - SQL Database는 데이터 웨어하우스가 예약된 대상인 대규모 분석 쿼리보다 약간씩 다른 워크로드를 서비스하는 대규모 동시 사용자 베이스를 서비스하도록 최적화되었습니다. 격리를 통해 올바른 도구에서 올바른 워크로드를 서비스하게 할 수 있습니다.
2. 보안 격리 - 특정 스키마를 통해 선별적으로 권한 있는 데이터 하위 집합을 분리합니다.
3. 샌드박스 - 프러덕션 쿼리 모색 등의 목적으로 샘플 데이터 집합을 "놀이터"로 제공합니다.

탄력적 쿼리는 SQL Data Warehouse의 하위 집합을 간편하게 선택하여 SQL Database 인스턴스로 이동하는 기능을 제공할 수 있습니다. 나아가 이러한 격리는 더 흥미로운 "캐시" 시나리오가 가능하도록 역시 원격 쿼리 실행을 구현하는 기능을 방해하지 않습니다.

### <a name="remote-query-execution"></a>원격 쿼리 실행

탄력적 쿼리는 SQL 데이터 웨어하우스 인스턴스에서의 원격 쿼리 실행을 제공합니다. 두 데이터베이스 간에 핫 데이터와 콜드 데이터를 구분하여 SQL Database와 SQL Data Warehouse를 모두 최대로 활용할 수 있습니다. 사용자는 SQL Database 안에 더 최신인 데이터를 유지하여 보고서와 대규모 평균 비즈니스 사용자를 서비스할 수 있습니다. 그러나 더 많은 데이터나 계산이 필요할 경우, 대규모 집계를 더 신속하고 효율적으로 처리하는 SQL 데이터 웨어하우스 인스턴스로 쿼리의 일부를 옮길 수 있습니다.

## <a name="elastic-query-process"></a>탄력적 쿼리 프로세스
탄력적 쿼리를 사용하여 한 SQL 데이터 웨어하우스에 있는 데이터를 다른 SQL 데이터베이스 인스턴스에 제공할 수 있습니다. 탄력적 쿼리를 사용하여 SQL Database의 쿼리가 원격 SQL Data Warehouse 인스턴스의 테이블을 참조하게 할 수 있습니다. 

첫 단계는 SQL 데이터 웨어하우스 안에서 기존 사용자 자격 증명을 사용하는 SQL 데이터 웨어하우스 인스턴스를 참조하는 외부 데이터 원본을 만드는 것입니다. 원격 SQL 데이터 웨어하우스 인스턴스에서는 변경이 필요하지 않습니다. 

> [!IMPORTANT] 
> 
> 사용자는 모든 외부 데이터 원본 ALTER 권한이 있어야 합니다. 이 사용 권한은 ALTER DATABASE 권한에 포함됩니다. 원격 데이터 원본을 참조하기 위해 ALTER ANY EXTERNAL DATA SOURCE 권한이 필요합니다.

다음으로 SQL Data Warehouse의 원격 테이블을 가리키는 SQL Database 인스턴스의 원격 외부 테이블 정의를 만듭니다. 쿼리에서 외부 테이블을 사용할 때는 외부 테이블을 참조하는 쿼리 부분이 처리를 위해 SQL 데이터 웨어하우스 인스턴스로 보내집니다. 쿼리가 완료되면 결과 집합이 다시 호출하는 SQL Database 인스턴스로 보내집니다. SQL Database와 SQL Data Warehouse 간의 탄력적 쿼리를 설정하는 간략한 자습서는 [SQL Data Warehouse에서 탄력적 쿼리 구성][Configure Elastic Query with SQL Data Warehouse]을 참조하세요.

SQL Database에서의 탄력적 쿼리에 자세한 내용은 [Azure SQL Databased 탄력적 쿼리 개요][Azure SQL Database elastic query overview]를 참조하세요.

## <a name="best-practices"></a>모범 사례
다음 모범 사례를 사용하여 탄력적 쿼리를 효과적으로 사용하세요.

### <a name="general"></a>일반

- 원격 쿼리 실행을 사용할 때는 필요한 열만 선택하고 올바른 필터를 적용하도록 해야 합니다. 이렇게 하면 계산 필요만 커지는 것이 아니라 결과 집합의 크기도 증가하기 때문에 두 인스턴스 간에 이동이 필요한 데이터 크기도 늘어나게 됩니다.
- 분석 성능을 위해 클러스터형 Columnstore의 SQL Data Warehouse와 SQL Database 모두에서 분석을 위한 데이터를 유지합니다.
- 쿼리 및 데이터 이동을 위해 원본 테이블이 분할되어야 합니다.
- 캐시로 사용되는 SQL Database 인스턴스는 더 세분화된 업데이트와 간편한 관리를 위해 분할됩니다. 
- 이상적으로 PremiumRS 데이터베이스를 사용합니다. 이 데이터베이스는 Premium 데이터베이스보다 저렴하게 IO 집약적 워크로드에 초점을 맞추어 클러스터형 Columnstore 인덱싱의 장점을 제공하기 때문입니다. 
- 로드 후에는 로드 또는 유효 날짜 확인 열을 SQL Database 인스턴스의 업서트에 사용하여 캐시 원본 무결성을 유지합니다. 
- 외부 데이터 원본에 정의된 SQL Database 원격 로그인 자격 증명에 대해 SQL Data Warehouse 인스턴스에서 별도 로그인 및 사용자를 만듭니다. 

### <a name="elastic-querying"></a>탄력적 쿼리

- 대부분의 경우 성능 향상을 테이블의 일부를 SQL Database 내에 캐시된 데이터로 저장하고 나머지 데이터는 SQL Data Warehouse에 저장하는 확대 테이블 형식을 관리할 수 있습니다. 이 경우 SQL Database에는 두 개의 개체 즉, SQL Data Warehouse의 기본 테이블을 참조하는 SQL Database 내에 있는 외부 테이블과 SQL Database 내에 있는 테이블의 “캐시된” 부분이 있어야 합니다. 테이블의 캐시된 부분과 외부 테이블 위에 뷰를 만들어 두 테이블을 통합하고 SQL Database 내에 구체화된 데이터와 외부 테이블을 통해 노출된 SQL Data Warehouse 데이터를 구분하는 필터를 적용하는 것이 좋습니다.

  SQL Database 인스턴스에 가장 최근 연도의 데이터를 유지하려 한다고 가정합니다. **ext.Orders** 테이블은 데이터 웨어하우스 주문 테이블을 참조합니다. **dbo.Orders**는 SQL Ddatabase 인스턴스 내의 최근 수년 간 데이터를 나타냅니다. 사용자가 어떤 테이블을 쿼리할지 결정하게 하는 대신 가장 최근 연도의 파티션 지점에 두 쿼리의 맨 위에 대한 보기를 만듭니다.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  이렇게 생성된 보기에서는 쿼리 컴파일러가 데이터 웨어하우스 인스턴스를 사용하여 사용자 쿼리에 답해야 할지 여부를 결정합니다. 

  데이터 웨어하우스 인스턴스에 대해 각각의 탄력적 쿼리와 관련한 데이터의 제출, 컴파일, 실행 및 이동에 오버헤드가 있습니다.  각 탄력적 쿼리는 동시 슬롯에 대해 계산하고 리소스를 사용한다는 점을 인식합니다.  


- 데이터 웨어하우스 인스턴스의 결과 집합으로 더 상세히 들어가려면 성능을 높이고 불필요한 리소스 사용을 방지하기 위해 SQL Database의 임시 테이블에 구체화하는 것이 좋습니다.

### <a name="moving-data"></a>데이터 이동 

- 가능한 경우 데이터 웨어하우스와 데이터베이스 인스턴스 사이에 업데이트를 간편하게 유지할 수 있게 추가 전용 원본 테이블을 통해 데이터 관리를 더 간편하게 유지합니다.
- 플러시 및 채우기 의미 체계가 있는 파티션 수준에서 데이터를 이동하여 데이터 웨어하우스 수준의 쿼리 비용과 이동하는 데이터 크기를 최소화하여 데이터베이스 인스턴스를 최신으로 유지합니다. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Azure Analysis Services 또는 SQL Database를 선택하는 경우

다음 경우에 Azure Analysis Services를 사용합니다.

- 많은 소규모 쿼리를 제출하는 BI 도구에서 캐시를 사용하려는 경우
- 1초 미만의 쿼리 대기 시간이 필요한 경우
- Analysis Services에 대한 모델 관리/개발 경험이 있는 경우 

다음 경우에 Azure SQL Database를 사용합니다.

- SQL에서 캐시 데이터를 쿼리하려는 경우
- 특정 쿼리에 대해 원격 실행이 필요한 경우
- 캐시 요구 사항이 더 큰 경우

## <a name="faq"></a>FAQ

Q: 탄력적 쿼리로 탄력적 풀 내에서 데이터베이스를 사용할 수 있나요?

A: 예. 탄력적 풀 내의 SQL Database는 탄력적 쿼리를 사용할 수 있습니다. 

Q: 탄력적 쿼리에 대해 사용할 수 있는 데이터베이스 수에 한도가 있나요?

A: 탄력적 쿼리에 사용할 수 있는 데이터베이스 수에는 제약이 없습니다. 그러나 각 탄력적 쿼리(SQL Data Warehouse를 적중하는 쿼리)는 일반 동시성 제한 수에 포함되어 계산됩니다.

Q: 탄력적 쿼리와 관련된 DTU 한도가 있나요?

A: 탄력적 쿼리에서 DTU 한도가 다르게 적용되지 않습니다. 표준 정책에 따라 논리 서버에는 고객의 우발적인 과도 사용을 방지하기 위한 DTU 한도가 있습니다. SQL Data Warehouse 인스턴스와 함께 탄력적 쿼리에 대해 여러 데이터베이스를 사용하면 예기치 않게 이 한도에 도달할 수 있습니다. 이 경우 요청을 제출하여 논리 서버의 DTU 한도를 증대합니다. [지원 티켓을 만들고](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) *할당량* 을 요청 형식으로 선택하여 할당량을 늘릴 수 있습니다.

Q: 탄력적 쿼리와 함께 행 수준 보안/동적 데이터 마스킹을 사용할 수 있나요?

A: SQL Database에 더 고급 보안 기능을 사용하려는 고객은 먼저 SQL Database에 데이터를 이동 및 저장하여 그렇게 할 수 있습니다. 현재는 외부 테이블을 통해 쿼리된 데이터에 행 수준 보안 또는 DDM을 적용할 수 없습니다. 

Q: SQL Database 인스턴스로부터 데이터 웨어하우스 인스턴스에 쓸 수 있나요? 

A: 이 기능은 현재 지원되지 않습니다. 향후 이 기능을 만나고 싶다면 [피드백 페이지][Feedback page]를 방문하여 이 기능을 만들거나 투표해 주세요. 

Q: 기하/지리 등의 공간 형식을 사용할 수 있나요?

A: SQL 데이터 웨어하우스에 공간 형식을 varbinary(max) 값으로 저장할 수 있습니다.  탄력적 쿼리를 사용하여 이 열을 쿼리할 때는 런타임에서 이 열을 적합한 형식으로 변환할 수 있습니다.

![공간 형식](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md


