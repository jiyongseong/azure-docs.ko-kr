---
title: 데이터베이스 간 쿼리 시작(수직 분할) | Microsoft Docs
description: 수직 분할 데이터베이스에서 탄력적 데이터베이스 쿼리를 사용하는 방법
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c7bf6816b457f7e193f53336c48f5e205722067e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>데이터베이스 간 쿼리 시작(수직 분할)(미리 보기)
Azure SQL Database에 Elastic Database 쿼리 (미리 보기)를 사용 하면 단일 연결 지점을 사용하여 여러 데이터베이스에 걸쳐 있는 T-SQL 쿼리를 실행할 수 있습니다. 이 항목은 [데이터베이스 수직 분할](sql-database-elastic-query-vertical-partitioning.md)에 적용됩니다.  

완료되면 여러 관계형 데이터베이스에 걸쳐 있는 쿼리를 수행 하는 Azure SQL Database를 사용과 구성방법을 알아봅니다. 

탄력적 데이터베이스 쿼리 기능에 대한 자세한 내용은 [Azure SQL Database 탄력적 데이터베이스 쿼리 개요](sql-database-elastic-query-overview.md)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건

사용자는 모든 외부 데이터 원본 ALTER 권한이 있어야 합니다. 이 사용 권한은 ALTER DATABASE 권한에 포함됩니다. 기본 데이터 원본을 참조하기 위해 ALTER ANY EXTERNAL DATA SOURCE 권한이 필요합니다.

## <a name="create-the-sample-databases"></a>샘플 데이터베이스 만들기
먼저 동일하거나 다른 논리적 서버에 있는 이름이 **고객** 및 **주문**인 두 데이터베이스를 만들어야 합니다.   

**주문** 데이터베이스에서 다음 쿼리를 실행하여 **주문 정보** 테이블을 만들고 샘플 데이터를 입력합니다. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

이제 **고객** 데이터베이스에서 다음 쿼리를 실행하여 **고객 정보** 테이블을 만들고 샘플 데이터를 입력합니다. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>데이터베이스 개체 만들기
### <a name="database-scoped-master-key-and-credentials"></a>데이터베이스 범위 마스터 키 및 자격 증명
1. SQL Server Management Studio 또는 Visual Studio의 SQL Server Data Tools를 엽니다.
2. 주문 데이터베이스에 연결하고 T-SQL 명령을 실행 합니다.
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    “사용자 이름”과 “암호”는 고객 데이터베이스 로그인에 사용되는 사용자 이름과 암호여야 합니다.
    탄력적 쿼리를 통해 Azure Active Directory를 사용한 인증은 현재 지원되지 않습니다.

### <a name="external-data-sources"></a>외부 데이터 원본
외부 데이터 소스를 만들려면 주문 데이터베이스에서 다음 명령을 실행 합니다. 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>외부 테이블
주문 데이터베이스에서 고객 정보 테이블의 정의와 일치하는 외부 테이블을 만듭니다. 

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>탄력적 데이터베이스 T-SQL쿼리 샘플 실행
외부 데이터 원본 및 외부 테이블을 정의한 후에는 T-SQL을 사용하여 외부 테이블을 쿼리할 수 있습니다. 주문 데이터베이스에 다음 쿼리를 실행합니다. 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>비용
현재 Azure SQL Database 가격에는 Elastic Database 쿼리 기능이 포함되어 있습니다.  

가격 정보는 [SQL Database 가격 정보](https://azure.microsoft.com/pricing/details/sql-database)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

* 탄력적 쿼리의 개요는 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)를 참조하세요.
* 수직 분할된 데이터에 대한 구문 및 예제 쿼리는 [수직 분할된 데이터 쿼리하기](sql-database-elastic-query-vertical-partitioning.md)를 참조하세요.
* 행 분할(분할) 자습서는 [행 분할(분할)을 위한 탄력적 데이터베이스 쿼리 시작하기](sql-database-elastic-query-getting-started.md)를 참조하세요.
* 행 분할된 데이터에 대한 구문 및 예제 쿼리는 [행 분할된 데이터 쿼리하기](sql-database-elastic-query-horizontal-partitioning.md)를 참조하세요.
* 단일 원격 Azure SQL Database 또는 수평 분할 구성표의 분할을 제공하는 데이터베이스 집합에서 TRANSACT-SQL 문을 실행하는 저장된 프로시저는 [sp\_실행 \_원격](https://msdn.microsoft.com/library/mt703714)을 참조하세요.
