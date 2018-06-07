---
title: SQL Data Warehouse에 SQL 코드 마이그레이션| Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse로 SQL 코드를 마이그레이션하기 위한 팁
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: b17e8e306c01bef4c58658b35f3a67d0e721633c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>SQL Data Warehouse에 SQL 코드 마이그레이션
이 문서는 다른 데이터베이스에서 SQL Data Warehouse로 코드를 마이그레이션하는 경우 수행해야 하는 코드 변경 사항을 설명합니다. 일부 SQL Data Warehouse 기능은 원래 분산 방식으로 작동하도록 디자인되었기 때문에 크게 성능을 향상시킬 수 있습니다. 그러나 성능 및 확장을 유지하려면 일부 기능은 사용할 수 없습니다.

## <a name="common-t-sql-limitations"></a>일반적인 T-SQL 제한 사항
다음 목록에서는 SQL Data Warehouse에서 지원하지 않는 가장 일반적인 기능을 간략하게 설명합니다. 링크를 따라 이동하면 지원되지 않는 기능에 대한 대안을 확인할 수 있습니다.

* [업데이트 시 ANSI 조인][ANSI joins on updates]
* [삭제 시 ANSI 조인][ANSI joins on deletes]
* [병합 문][merge statement]
* 데이터베이스 간 조인
* [커서][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* output 절
* 인라인 사용자 정의 함수
* 다중 문 함수
* [공통 테이블 식](#Common-table-expressions)
* [재귀 공통 테이블 식(CTE)](#Recursive-common-table-expressions-(CTE)
* CLR 함수 및 프로시저
* $partition 함수
* 테이블 변수
* 테이블 값 매개 변수
* 분산된 트랜잭션
* 커밋 / 롤백 작업
* 트랜잭션 저장
* 실행 컨텍스트 (EXECUTE AS)
* [롤업/큐브/그룹화 집합 옵션을 사용하는 GROUP BY 절][group by clause with rollup / cube / grouping sets options]
* [8을 초과한 중첩 수준][nesting levels beyond 8]
* [뷰를 통한 업데이트][updating through views]
* [변수 할당에 대한 select 사용][use of select for variable assignment]
* [동적 SQL 문자열에 대한 MAX 데이터 형식 없음][no MAX data type for dynamic SQL strings]

다행히 대부분의 이러한 제한을 해결할 수 있습니다. 위에서 언급한 개발 관련 문서에 대한 설명이 제공됩니다.

## <a name="supported-cte-features"></a>지원되는 CTE 기능
CTE(공용 테이블 식)는 SQL Data Warehouse에서 부분적으로 지원됩니다.  현재 지원되는 CTE 기능은 다음과 같습니다.

* SELECT 문에서 CTE를 지정할 수 있습니다.
* CREATE VIEW 문에서 CTE를 지정할 수 있습니다.
* (CTAS)CREATE TABLE AS SELECT 문에서 CTE를 지정할 수 있습니다.
* (CRTAS)CREATE REMOTE TABLE AS SELECT 문에서 CTE를 지정할 수 있습니다.
* (CETAS)CREATE EXTERNAL TABLE AS SELECT 문에서 CTE를 지정할 수 있습니다.
* CTE로부터 원격 테이블을 참조할 수 있습니다.
* CTE로부터 외부 테이블을 참조할 수 있습니다.
* CTE에 여러 개의 CTE 쿼리 정의를 정의할 수 있습니다.

## <a name="cte-limitations"></a>CTE 제한 사항
공용 테이블 식은 SQL Data Warehouse에서 다음을 포함하여 몇 가지 제한 사항이 있습니다.

* CTE는 단일 SELECT 문 뒤에 와야 합니다. INSERT, UPDATE, DELETE 및 MERGE 문은 지원되지 않습니다.
* 자체에 대한 참조를 포함하는 공통 테이블 식(재귀 공통 테이블 식)은 지원되지 않습니다(아래 섹션 참조).
* CTE에 둘 이상의 WITH 절을 지정할 수 없습니다. 예를 들어 CTE_query_definition에 하위 쿼리가 포함된 경우 해당 하위 쿼리에는 다른 CTE를 정의하는 중첩된 WITH 절을 포함할 수 없습니다.
* TOP 절이 지정된 경우를 제외하고 ORDER BY 절은 CTE_query_definition에서 사용할 수 없습니다.
* 일괄 처리의 일부인 문에서 CTE가 사용되는 경우 그 전의 문 뒤에 세미콜론이 와야 합니다.
* Sp_prepare에 의해 준비된 문에서 사용할 경우 CTE는 PDW에서 다른 SELECT 문과 동일한 방식으로 작동합니다. 그러나 sp_prepare를 통해 준비된 CETAS의 일부로 CTE를 사용할 경우 sp_prepare에 대해 구현되는 바인딩 방식이 다르기 때문에 동작이 SQL Server 및 다른 PDW와 다를 수 있습니다. CTE를 참조하는 SELECT 문이 CTE에 없는 잘못된 열을 사용할 경우 오류를 감지하지 않고 sp_prepare를 통과합니다. 대신 sp_execute 동안 오류가 throw됩니다.

## <a name="recursive-ctes"></a>재귀 CTE
재귀 CTE는 SQL Data Warehouse에서 지원되지 않습니다.  재귀 CTE의 마이그레이션은 복잡할 수 있지만 가장 좋은 프로세스는 여러 단계로 분할하는 것입니다. 재귀 중간 쿼리를 반복할 때 일반적으로 루프를 사용하여 임시 테이블을 채울 수 있습니다. 임시 테이블을 채우고 나면 데이터를 단일 결과 집합으로 반환할 수 있습니다. [롤업/큐브/그룹화 집합 옵션을 사용하는 GROUP BY 절][group by clause with rollup / cube / grouping sets options] 문서에서 `GROUP BY WITH CUBE`를 해결하는 데 사용한 것과 비슷한 방법입니다.

## <a name="unsupported-system-functions"></a>지원되지 않는 시스템 함수
지원하지 않는 일부 시스템 함수도 있습니다. 일반적으로 데이터 웨어하우징에서 사용될 수 있는 일부 기본 함수는 다음과 같습니다.

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

이러한 문제 중 일부는 해결될 수 있습니다.

## <a name="rowcount-workaround"></a>@@ROWCOUNT 해결 방법
@@ROWCOUNT에 대한 지원 부족 문제를 해결하려면 sys.dm_pdw_request_steps에서 마지막 행 수를 검색한 후 DML 문 다음에 `EXEC LastRowCount`를 실행하는 저장 절차를 만듭니다.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>다음 단계
지원되는 모든 T-SQL 문의 전체 목록은 [Transact-SQL 항목][Transact-SQL topics]을 참조하세요.

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
