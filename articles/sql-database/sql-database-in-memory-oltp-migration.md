---
title: 메모리 내 OLTP이 SQL txn 성능 개선 | Microsoft Docs
description: 메모리 내 OLTP를 채택하여 기존 SQL 데이터베이스의 트랜잭션 성능을 향상합니다.
services: sql-database
author: jodebrui
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 04/01/2018
ms.author: jodebrui
ms.openlocfilehash: 19ddcdf9a7d27535172c1e9ccfa89b3836589c84
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>메모리 내 OLTP를 사용하여 SQL Database에서 응용 프로그램의 성능 향상
[메모리 내 OLTP](sql-database-in-memory.md)를 사용하면 가격대를 높이지 않고도 [프리미엄 및 중요 비즈니스용 계층](sql-database-service-tiers-vcore.md) 데이터베이스에서 트랜잭션 처리, 데이터 수집 및 일시적인 데이터 시나리오의 성능을 개선할 수 있습니다. 

> [!NOTE] 
> [쿼럼이 SQL Database를 사용하여 DTU를 70% 줄이는 동시에 키 데이터베이스의 워크로드를 두 배로 증가시키는 방법](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)에 대해 알아보기


기존 데이터베이스에서 메모리 내 OLTP를 채택하려면 다음 단계를 따르세요.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>1단계: 프리미엄 및 중요 비즈니스용 계층 데이터베이스를 사용하고 있는지 확인합니다.
메모리 내 OLTP는 프리미엄 및 중요 비즈니스용 계층 데이터베이스에서만 지원됩니다. 반환된 결과가 1인 경우(0이 아님) 메모리 내가 지원됩니다.

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP*는 *고도의 트랜잭션 처리*의 약어입니다.



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>2단계: 개체를 식별하여 메모리 내 OLTP로 마이그레이션
SSMS는 활성 워크로드를 사용하여 데이터베이스에 대해 실행할 수 있는 **트랜잭션 성능 분석 개요** 보고서를 포함합니다. 보고서는 메모리 내 OLTP로 마이그레이션하기 위한 후보인 테이블 및 저장된 프로시저를 식별합니다.

SSMS에서 보고서를 생성합니다.

* **개체 탐색기**에서 데이터베이스 노드를 마우스 오른쪽 단추로 클릭합니다.
* **보고서** > **표준 보고서** > **트랜잭션 성능 분석 개요**를 클릭합니다.

자세한 내용은 [테이블 또는 저장 프로시저가 메모리 내 OLTP로 이식되어야 하는지 결정](http://msdn.microsoft.com/library/dn205133.aspx)을 참조하세요.

## <a name="step-3-create-a-comparable-test-database"></a>3단계: 비교할 수 있는 테스트 데이터베이스 만들기
보고서는 데이터베이스에 메모리 최적화 테이블로 변환하여 이점을 얻을 수 있는 테이블이 있음을 나타낸다고 가정합니다. 이를 확인하기 위해 먼저 테스트하는 것이 좋습니다.

프로덕션 데이터베이스의 테스트 복사본이 필요합니다. 테스트 데이터베이스는 프로덕션 데이터베이스와 동일한 서비스 계층 수준에 있어야 합니다.

쉽게 테스트하려면 테스트 데이터베이스를 다음과 같이 조정합니다.

1. SSMS를 사용하여 테스트 데이터베이스에 연결합니다.
2. 쿼리에서 WITH(SNAPSHOT) 옵션의 필요를 방지하려면 다음 T-SQL 문에서와 같이 데이터베이스 옵션을 설정합니다.
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>4단계: 테이블 마이그레이션
테스트하려는 테이블의 메모리 최적화 복사본을 만들고 채워야 합니다. 다음 중 하나를 사용하여 만들 수 있습니다.

* SSMS의 편리한 메모리 최적화 마법사.
* 수동 T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>SSMS의 메모리 최적화 마법사.
이 마이그레이션 옵션을 사용하려면.

1. SSMS를 사용하여 테스트 데이터베이스에 연결합니다.
2. **개체 탐색기**에서 테이블을 마우스 오른쪽 단추로 클릭한 다음 **메모리 최적화 관리자**를 클릭합니다.
   
   * **테이블 메모리 최적화 관리자** 마법사가 표시됩니다.
3. 마법사에서 **마이그레이션 유효성 검사**(또는 **다음** 단추)를 클릭하여 메모리 최적화 테이블에서 지원하지 않는 지원되지 않는 기능이 테이블에 있는지 확인합니다. 자세한 내용은 다음을 참조하세요.
   
   * *메모리 최적화 관리자* 의 [메모리 최적화 검사 목록](http://msdn.microsoft.com/library/dn284308.aspx).
   * [메모리 내 OLTP에서 지원되지 않는 TRANSACT-SQL 항목](http://msdn.microsoft.com/library/dn246937.aspx).
   * [메모리 내 OLTP로 마이그레이션](http://msdn.microsoft.com/library/dn247639.aspx).
4. 테이블에 지원되지 않는 기능이 없는 경우 관리자는 사용자에게 실제 스키마 및 데이터 마이그레이션을 수행할 수 있습니다.

#### <a name="manual-t-sql"></a>수동 T-SQL
이 마이그레이션 옵션을 사용하려면.

1. SSMS(또는 유사한 유틸리티)를 사용하여 테스트 데이터베이스에 연결합니다.
2. 테이블 및 해당 인덱스에 대한 완전한 T-SQL 스크립트를 가져옵니다.
   
   * SSMS에서 테이블 노드를 마우스 오른쪽 단추로 클릭합니다.
   * **테이블 스크립팅** > **CREATE** > **새 쿼리 창**을 클릭합니다.
3. 스크립트 창에서 테이블 만들기 문에 WITH (MEMORY_OPTIMIZED = ON)을 추가합니다.
4. 클러스터형 인덱스가 있을 경우 비클러스터형으로 변경합니다.
5. SP_RENAME을 사용하여 기존 테이블의 이름을 바꿉니다.
6. 편집된 만들기 테이블 스크립트를 실행하여 테이블의 새 메모리 최적화 복사본을 만듭니다.
7. 삽입을 사용하여 메모리 최적화 테이블에 데이터를 복사합니다... *를 다음에 선택합니다.

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>5단계(선택 사항): 저장 프로시저 마이그레이션
또한 메모리 내 기능은 향상된 성능을 위해 저장 프로시저를 수정할 수 있습니다.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>고유하게 컴파일된 저장 프로시저를 사용할 때 고려 사항
고유하게 컴파일된 저장 프로시저에는 절이 있는 해당 T-SQL에서 다음 옵션이 있어야 합니다.

* NATIVE_COMPILATION
* SCHEMABINDING: 저장 프로시저가 삭제되지 않는 한 스스로에 영향을 주는 방식으로 변경된 해당 열 정의를 가질 수 없는 테이블을 의미합니다.

네이티브 모듈은 트랜잭션 관리에 하나의 큰 [ATOMIC 블록](http://msdn.microsoft.com/library/dn452281.aspx) 을 사용해야 합니다. 명시적 BEGIN TRANSACTION 또는 ROLLBACK TRANSACTION에 대한 역할이 없습니다. 코드가 비즈니스 규칙 위반을 감지한 경우 [THROW](http://msdn.microsoft.com/library/ee677615.aspx) 문으로 ATOMIC 블록을 종료할 수 있습니다.

### <a name="typical-create-procedure-for-natively-compiled"></a>고유하게 컴파일된 일반적인 만들기 프로시저
고유하게 컴파일된 저장 프로시저를 만드는 T-SQL은 일반적으로 다음 템플릿과 유사합니다.

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* TRANSACTION_ISOLATION_LEVEL의 경우 스냅숏은 고유하게 컴파일된 저장 프로시저에 대한 가장 일반적인 값입니다. 그러나 다른 값의 하위 집합에서도 지원됩니다.
  
  * 반복 가능한 읽기
  * 직렬화 가능
* sys.languages 보기에 언어 값이 나탸나야 합니다.

### <a name="how-to-migrate-a-stored-procedure"></a>저장 프로시저를 마이그레이션하는 방법
마이그레이션 단계는 다음과 같습니다.

1. 일반적으로 해석된 저장 프로시저에 프로시저 만들기 스크립트를 가져옵니다.
2. 이전 템플릿에 맞게 해당 헤더를 다시 작성합니다.
3. 저장 프로시저 T-SQL 코드가 고유하게 컴파일된 저장 프로시저에 지원하지 않는 기능을 사용하는지를 확인합니다. 필요한 경우 해결 방법을 구현합니다.
   
   * 자세한 내용은 [고유하게 컴파일된 저장 프로시저에 대한 마이그레이션 문제](http://msdn.microsoft.com/library/dn296678.aspx)를 참조하세요.
4. SP_RENAME을 사용하여 이전의 저장 프로시저의 이름을 바꿉니다. 또는 단순히 삭제합니다.
5. 편집된 프로시저 T-SQL 만들기 스크립트를 실행합니다.

## <a name="step-6-run-your-workload-in-test"></a>6단계: 테스트에서 워크로드 실행
프로덕션 데이터베이스에서 실행되는 워크로드와 비슷한 테스트 데이터베이스에서 워크로드를 실행합니다. 이 옵션은 테이블 및 저장된 프로시저에 메모리 내 기능을 사용하여 얻는 성능 향상을 드러내야 합니다.

워크로드의 주요 특성은 다음과 같습니다.

* 동시 연결 수.
* 읽기/쓰기 비율.

테스트 워크로드를 조정하고 실행하려면 [여기](sql-database-in-memory.md)에 설명된 편리한 ostress.exe 도구를 사용하는 것이 좋습니다.

네트워크 대기 시간을 최소화하려면 데이터베이스가 있는 동일한 Azure 지리적 지역에 있는 테스트를 실행합니다.

## <a name="step-7-post-implementation-monitoring"></a>7단계: 사후 실현 모니터링
프로덕션에서 메모리 내 구현의 성능 효과를 모니터링하는 것이 좋습니다.

* [메모리 내 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md).
* [동적 관리 뷰를 사용하여 Azure SQL Database 모니터링](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>관련 링크
* [메모리 내 OLTP(메모리 내 최적화)](http://msdn.microsoft.com/library/dn133186.aspx)
* [고유하게 컴파일된 저장 프로시저의 소개](http://msdn.microsoft.com/library/dn133184.aspx)
* [메모리 최적화 관리자](http://msdn.microsoft.com/library/dn284308.aspx)

