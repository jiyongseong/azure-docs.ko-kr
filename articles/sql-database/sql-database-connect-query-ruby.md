---
title: Ruby를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: 이 항목에서는 Ruby를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: ruby
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: f86c53465636f82cf36d5099699fc9e6efeb55a5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Ruby를 사용하여 Azure SQL Database 쿼리

이 빠른 시작에서는 [Ruby](https://www.ruby-lang.org)를 사용하여 Azure SQL Database에 연결하고, TRANSACT-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 이 빠른 시작에서 사용하는 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)

- 운영 체제에 맞게 설치된 Ruby 및 관련 소프트웨어
    - **MacOS**: Homebrew를 설치하고, rbenv와 ruby-build를 설치하고, Ruby를 설치한 다음, FreeTDS를 설치합니다. [1.2, 1.3, 1.4 및 1.5 단계](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/) 참조
    - **Ubuntu**: Ruby용 필수 구성 요소를 설치하고, rbenv와 ruby-build를 설치하고, Ruby를 설치한 다음, FreeTDS를 설치합니다. [1.2, 1.3, 1.4 및 1.5 단계](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/) 참조

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> 이 자습서를 수행하는 컴퓨터의 공용 IP 주소에 대한 방화벽 규칙이 있어야 합니다. 다른 컴퓨터에 있거나 다른 공용 IP 주소가 있으면 [Azure Portal을 사용하여 서버 수준 방화벽 규칙을 만듭니다](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="insert-code-to-query-sql-database"></a>SQL 데이터베이스 쿼리 코드 삽입

1. 원하는 텍스트 편집기에서 **sqltest.rb** 파일을 새로 만듭니다.

2. 내용을 다음 코드로 바꾸고, 서버, 데이터베이스, 사용자 및 암호에 대해 적절한 값을 추가합니다.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```bash
   ruby sqltest.rb
   ```

2. 상위 20개 행이 반환되는지 확인한 다음 응용 프로그램 창을 닫습니다.


## <a name="next-steps"></a>다음 단계
- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [TinyTDS에 대한 GitHub 리포지토리](https://github.com/rails-sqlserver/tiny_tds)
- [TinyTDS에 관한 문제 보고 또는 질문](https://github.com/rails-sqlserver/tiny_tds/issues)
- [SQL Server용 Ruby 드라이버](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
