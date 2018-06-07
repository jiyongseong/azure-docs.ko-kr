---
title: 데이터 형식 정의 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse에서 테이블 데이터 형식을 정의하기 위한 권장 사항입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4d8a222a6d4cfa4138fe833fb4e9cc895dbc5f65
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse의 테이블 데이터 형식
Azure SQL Data Warehouse에서 테이블 데이터 형식을 정의하기 위한 권장 사항입니다. 

## <a name="what-are-the-data-types"></a>데이터 형식은 무엇인가요?

SQL Data Warehouse는 가장 일반적으로 사용되는 데이터 형식을 지원합니다. 지원되는 데이터 형식의 목록은 CREATE TABLE 문에서 [데이터 형식](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes)을 참조하세요. 

## <a name="minimize-row-length"></a>행 길이 최소화
데이터 형식의 크기를 최소화하면 쿼리 성능을 향상시키는 행 길이를 줄입니다. 데이터에 대해 작동하는 가장 작은 데이터 형식을 사용합니다. 

- 문자 열을 큰 기본 길이로 정의하지 마세요. 예를 들어 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다. 
- VARCHAR만 필요한 경우 [NVARCHAR][NVARCHAR]를 사용하지 마세요.
- 가능한 경우 NVARCHAR(MAX) 또는 VARCHAR(MAX) 대신 NVARCHAR(4000) 또는 VARCHAR(8000)를 사용합니다.

PolyBase 외부 테이블을 사용하여 테이블을 로드하는 경우 정의된 테이블 행의 길이는 1MB를 초과할 수 없습니다. 가변 길이 데이터가 있는 행이 1MB를 초과하는 경우 행을 PolyBase가 아닌 BCP로 로드할 수 있습니다.

## <a name="identify-unsupported-data-types"></a>지원되지 않는 데이터 형식 식별
다른 SQL 데이터베이스에서 데이터베이스를 마이그레이션하려는 경우 SQL Data Warehouse에서 지원되지 않는 데이터 형식이 나타날 수 있습니다. 이 쿼리를 사용하여 기존 SQL 스키마에서 지원되지 않는 데이터 형식을 검색합니다.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>지원되지 않는 데이터 형식에 대한 해결 방법

다음 목록은 SQL Data Warehouse에서 지원하지 않는 데이터 형식을 보여 주고 지원되지 않는 데이터 형식 대신 사용할 수 있는 대안을 제공합니다.

| 지원되지 않는 데이터 형식 | 해결 방법 |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |열을 강력한 형식의 열로 분할합니다. |
| [테이블](/sql/t-sql/data-types/table-transact-sql) |임시 테이블로 변환합니다. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |[datetime2](/sql/t-sql/data-types/datetime2-transact-sql) 및 [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) 함수를 사용하도록 코드 재작업을 수행합니다. 상수만 기본값으로 지원됩니다. 따라서 current_timestamp는 기본 제약 조건으로 정의할 수 없습니다. 행 버전 값을 타임스탬프 형식의 열에서 마이그레이션해야 하는 경우, NOT NULL 또는 NULL 행 버전 값으로 [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) 또는 [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) 를 사용합니다. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [사용자 정의 형식](/sql/relational-databases/native-client/features/using-user-defined-types) |가능하면 네이티브 데이터 형식으로 다시 변환합니다. |
| 기본값 | 기본값은 리터럴 및 상수만 지원합니다. |


## <a name="next-steps"></a>다음 단계
테이블 개발에 대한 자세한 내용은 [테이블 개요](sql-data-warehouse-tables-overview.md)를 참조하세요.
