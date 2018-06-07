---
title: Azure SQL Data Warehouse용 연결 문자열 | Microsoft Docs
description: SQL Data Warehouse용 연결 문자열
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 8fce3ba099c871059ffb71a589e4d8cd33d5bcae
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse용 연결 문자열
[ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] 및 [JDBC][JDBC]와 같은 여러 가지 다양한 응용 프로그램 프로토콜을 사용하여 SQL Data Warehouse에 연결할 수 있습니다. 다음은 각 프로토콜에 대한 연결 문제열의 몇 가지 예입니다.  또한 Azure 포털을 사용하여 연결 문자열을 빌드할 수도 있습니다.  Azure Portal을 사용하여 연결 문자열을 빌드하려면 데이터베이스 블레이드로 이동한 후 *Essentials* 링크에서 *데이터베이스 연결 문자열 표시*를 클릭합니다.

## <a name="sample-adonet-connection-string"></a>샘플 ADO.NET 연결 문자열
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>샘플 ODBC 연결 문자열
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>샘플 PHP 연결 문자열
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>샘플 JDBC 연결 문자열
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> 짧은 시간 동안 사용 불가 이후에 연결이 계속 유지될 수 있도록 하려면 연결 제한 시간을 300초로 설정하는 것이 좋습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
Visual Studio 및 다른 응용 프로그램으로 데이터 웨어하우스 쿼리를 시작하려면 [Visual Studio를 사용하여 쿼리][Query with Visual Studio]를 참조하세요.

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
