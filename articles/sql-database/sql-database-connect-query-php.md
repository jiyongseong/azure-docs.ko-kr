---
title: PHP를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: 이 항목에서는 PHP를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: php
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 8fe343587336ff22f82ed0d1ef700fc56c86f577
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363763"
---
# <a name="use-php-to-query-an-azure-sql-database"></a>PHP를 사용하여 Azure SQL Database 쿼리

이 빠른 시작에서는 [PHP](http://php.net/manual/en/intro-whatis.php)를 사용하여 Azure SQL Database에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 이 빠른 시작에서 사용하는 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)

- 운영 체제에 맞게 설치된 PHP 및 관련 소프트웨어

    - **MacOS**: Homebrew와 PHP를 설치하고, ODBC 드라이버와 SQLCMD를 설치한 다음, SQL Server용 PHP Driver를 설치합니다. [1.2, 1.3 및 2.1단계](https://www.microsoft.com/sql-server/developer-get-started/php/mac/)를 참조하세요.
    - **Ubuntu**: PHP 및 기타 필요한 패키지를 설치한 다음, SQL Server용 PHP Driver를 설치합니다. [1.2 및 2.1단계](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)를 참조하세요.
    - **Windows**: IIS Express용 PHP 최신 버전, IIS Express의 SQL Server용 Microsoft 드라이버 최신 버전, Chocolatey, ODBC 드라이버 및 SQLCMD를 설치합니다. [1.2 및 1.3단계](https://www.microsoft.com/sql-server/developer-get-started/php/windows/)를 참조하세요.    

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>SQL 데이터베이스 쿼리 코드 삽입

1. 원하는 텍스트 편집기에서 **sqltest.php** 파일을 새로 만듭니다.  

2. 내용을 다음 코드로 바꾸고, 서버, 데이터베이스, 사용자 및 암호에 대해 적절한 값을 추가합니다.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
   );
   //Establishes the connection
   $conn = sqlsrv_connect($serverName, $connectionOptions);
   $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";
   $getResults= sqlsrv_query($conn, $tsql);
   echo ("Reading data from table" . PHP_EOL);
   if ($getResults == FALSE)
       echo (sqlsrv_errors());
   while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
   }
   sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```php
   php sqltest.php
   ```

2. 상위 20개 행이 반환되는지 확인한 다음 응용 프로그램 창을 닫습니다.

## <a name="next-steps"></a>다음 단계
- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [SQL Server용 Microsoft PHP 드라이버](https://github.com/Microsoft/msphpsql/)
- [문제 보고 또는 질문(영문)](https://github.com/Microsoft/msphpsql/issues)
- [다시 시도 논리 예제: PHP로 SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

