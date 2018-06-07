---
title: SQL Database용 연결 라이브러리 | Microsoft Docs
description: 다양한 클라이언트 프로그래밍 언어에서 SQL Server 및 SQL Database에 연결할 수 있도록 하는 모듈 다운로드를 위한 링크를 제공합니다.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: a1e74e9fd2f1ed5eec27d6ede9ba51a7ba93263d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>SQL Server의 연결 라이브러리 및 프레임워크

C#, Java, Node.js, PHP 및 Python과 같은 프로그래밍 언어를 빠르게 시작하려면 [자습서 시작](http://aka.ms/sqldev)을 확인하세요. 그런 다음 Linux 또는 Windows 또는 macOS의 Docker에서 SQL Server를 사용하여 앱을 빌드합니다.

다음 표는 클라이언트 응용 프로그램이 다양한 언어를 사용하여 온-프레미스 또는 클라우드에서 실행 중인 SQL Server에 연결하고 사용할 수 있는 연결 라이브러리 또는 *드라이버*를 나열합니다. Linux, Windows 또는 Docker에서 사용할 수 있으며 이를 사용하여 Azure SQL Database 및 Azure SQL Data Warehouse에 연결할 수 있습니다. 

| 언어 | 플랫폼 | 추가 리소스 | 다운로드 | 시작하기 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [SQL Server용 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [다운로드](https://www.microsoft.com/net/download/) | [시작](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| 자바 | Windows, Linux, macOS | [SQL Server용 Microsoft JDBC 드라이버](http://msdn.microsoft.com/library/mt484311.aspx) | [다운로드](https://go.microsoft.com/fwlink/?linkid=852460) |  [시작](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [SQL Server용 PHP SQL 드라이버](http://msdn.microsoft.com/library/dn865013.aspx) | 운영 체제: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [시작](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [SQL Server용 Node.js 드라이버](http://msdn.microsoft.com/library/mt652093.aspx) | [설치](https://msdn.microsoft.com/library/mt652094.aspx) |  [시작](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| 파이썬 | Windows, Linux, macOS | [Python SQL 드라이버](http://msdn.microsoft.com/library/mt652092.aspx) | 다음 선택 항목을 설치합니다. <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [시작](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [SQL Server용 Ruby 드라이버](http://msdn.microsoft.com/library/mt691981.aspx) | [설치](https://msdn.microsoft.com/library/mt711041.aspx) | [시작](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [SQL Server용 Microsoft ODBC 드라이버](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [다운로드](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

다음 표는 클라이언트 응용 프로그램이 온-프레미스 또는 클라우드에서 실행 중인 SQL Server를 사용할 수 있는 ORM(개체-관계형 매핑) 프레임워크 및 웹 프레임워크의 예를 나열합니다. Linux, Windows 또는 Docker에서 프레임워크를 사용할 수 있으며 이를 사용하여 SQL Database 및 SQL Data Warehouse에 연결할 수 있습니다. 

| 언어 | 플랫폼 | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| 자바 | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel(Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| 파이썬 | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>관련 링크
- 클라이언트 응용 프로그램에서 연결하는 데 사용되는 [SQL Server 드라이버](http://msdn.microsoft.com/library/mt654049.aspx)
- SQL Database에 연결:
    - [.NET(C#)을 사용하여 SQL Database에 연결](sql-database-connect-query-dotnet.md)
    - [PHP를 사용하여 SQL Database에 연결](sql-database-connect-query-php.md)
    - [Node.js를 사용하여 SQL Database에 연결](sql-database-connect-query-nodejs.md)
    - [Java를 사용하여 SQL Database에 연결](sql-database-connect-query-java.md)
    - [Python을 사용하여 SQL Database에 연결](sql-database-connect-query-python.md)
    - [Ruby를 사용하여 SQL Database에 연결](sql-database-connect-query-ruby.md)
- 다시 시도 논리 코드 예제:
    - [ADO.NET으로 SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [PHP로 SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

