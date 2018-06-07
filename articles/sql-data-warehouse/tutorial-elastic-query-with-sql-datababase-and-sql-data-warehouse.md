---
title: '자습서: Azure SQL Data Warehouse의 탄력적 쿼리 | Microsoft Docs'
description: 이 자습서에서는 탄력적 쿼리 기능을 사용하여 Azure SQL 데이터베이스에서 Azure SQL Data Warehouse로 쿼리합니다.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: a31f035b5ec086a046028956c4a9c0de0d6a313d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>자습서: 탄력적 쿼리를 사용하여 Azure SQL Database에서 Azure SQL Data Warehouse의 데이터에 액세스

이 자습서에서는 탄력적 쿼리 기능을 사용하여 Azure SQL 데이터베이스에서 Azure SQL Data Warehouse로 쿼리합니다. 

## <a name="prerequisites-for-the-tutorial"></a>자습서의 필수 조건

이 자습서를 시작하기 전에 다음 요건이 충족되어야 합니다.

1. SSMS(SQL Server Management Studio)를 설치합니다.
2. 이 서버 내에서 데이터베이스 및 데이터 웨어하우스를 사용하여 Azure SQL Server를 만듭니다.
3. Azure SQL Server에 액세스하기 위한 방화벽 규칙을 설정합니다.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>SQL Data Warehouse 및 SQL Database 인스턴스 간에 연결을 설정합니다. 

1. SSMS 또는 다른 쿼리 클라이언트를 사용하여 논리 서버에서 **master** 데이터베이스에 대한 새 쿼리를 엽니다.

2. SQL Database에서 Data Warehouse로의 연결을 나타내는 로그인 및 사용자를 만듭니다.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. SSMS 또는 다른 쿼리 클라이언트를 사용하여 논리 서버에서 **SQL Data Warehouse 인스턴스**에 대한 새 쿼리를 엽니다.

4. 2단계에서 만든 로그인으로 데이터 웨어하우스 인스턴스에 사용자를 만듭니다.

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. SQL Database를 실행하게 하려는 4단계의 사용자에게 사용 권한을 부여합니다. 이 예제에서는 특정 스키마의 SELECT에 대해서만 권한을 부여하여 SQL Database의 쿼리를 특정 도메인으로 제한하는 방법을 보여 줍니다. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. SSMS 또는 다른 쿼리 클라이언트를 사용하여 논리 서버에서 **SQL Database 인스턴스**에 대한 새 쿼리를 엽니다.

7. 마스터 키가 아직 없는 경우 만듭니다. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. 2단계에서 만든 자격 증명을 사용하여 데이터베이스 범위 자격 증명을 만듭니다.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. 데이터 웨어하우스 인스턴스를 가리키는 외부 데이터 원본을 만듭니다.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. 이제 이 외부 데이터 원본을 참조하는 외부 테이블을 만들 수 있습니다. 이러한 테이블을 사용하는 쿼리는 처리를 위해 데이터 웨어하우스 인스턴스로 전송된 후 다시 데이터베이스 인스턴스로 전송됩니다.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>SQL Database에서 SQL Data Warehouse로의 탄력적 쿼리

다음 일부 단계에서는 몇 개의 값을 사용해서 데이터 웨어하우스 인스턴스에 테이블을 만듭니다. 그런 후 데이터베이스 인스턴스에서 데이터 웨어하우스 인스턴스를 쿼리하도록 외부 테이블을 설정하는 방법을 보여 줍니다.

1. SSMS 또는 다른 쿼리 클라이언트를 사용하여 논리 서버에서 **SQL Data Warehouse**에 대한 새 쿼리를 엽니다.

2. 다음 쿼리를 제출하여 데이터 웨어하우스 인스턴스에서 **OrdersInformation** 테이블을 만듭니다.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. SSMS 또는 다른 쿼리 클라이언트를 사용하여 논리 서버에서 **SQL Database**에 대한 새 쿼리를 엽니다.

4. 다음 쿼리를 제출하여 데이터 웨어하우스 인스턴스의 **OrdersInformation** 테이블을 가리키는 외부 테이블 정의를 만듭니다.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. 이제 **SQL Database 인스턴스**에 외부 테이블 정의가 있는 것을 볼 수 있습니다.

   ![탄력적 쿼리 외부 테이블 정의](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. 데이터 웨어하우스 인스턴스를 쿼리하는 다음 쿼리를 제출합니다. 2단계에서 삽입한 5개 값을 받게 됩니다. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> 값이 거의 없지만 이 쿼리는 반환하는 데 상당히 많은 시간이 걸립니다. 데이터 웨어하우스에 탄력적 쿼리를 사용할 경우 쿼리 처리 및 전송에 따른 오버헤드 비용을 고려해야 합니다. 대기 시간보다 계산 성능이 중요할 때는 탄력적 쿼리 원격 실행을 사용합니다.

이제 매우 기본적인 탄력적 쿼리가 설정되었습니다. 

## <a name="next-steps"></a>다음 단계
권장 사항은 [Azure SQL Data Warehouse에서 탄력적 쿼리를 사용하는 모범 사례](how-to-use-elastic-query-with-sql-data-warehouse.md)를 참조하세요.