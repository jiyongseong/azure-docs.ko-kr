---
title: Azure SQL 데이터베이스 연결 및 쿼리 빠른 시작 | Microsoft Docs
description: Azure SQL 데이터베이스 빠른 시작에서는 Azure SQL 데이터베이스에 연결하고 쿼리하는 방법을 보여 줍니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc
ms.devlang: ''
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: carlrab
ms.openlocfilehash: ec39c5ad0771c2bc78655e52c58949db6e9b3353
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-sql-database-connect-and-query-quickstarts"></a>Azure SQL 데이터베이스 연결 및 쿼리 빠른 시작

다음 문서는 Azure SQL 데이터베이스에 연결하고 쿼리하는 방법을 보여주는 Azure 예제에 대한 링크를 포함합니다. 또한 전송 수준 보안에 대한 몇 가지 권장 사항을 제공합니다.

## <a name="quickstarts"></a>빠른 시작

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|이 빠른 시작은 SSMS를 사용하여 Azure SQL Database에 연결한 후 Transact-SQL 문을 사용하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.|
|[SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|이 빠른 시작에서는 SQL Operations Studio(미리 보기)를 사용하여 Azure SQL Database에 연결한 다음, T-SQL(Transact-SQL) 문을 사용하여 SQL Operations Studio(미리 보기) 자습서에 사용되는 TutorialDB를 만드는 방법을 보여 줍니다.|
|[Azure Portal](sql-database-connect-query-portal.md)|이 빠른 시작에서는 쿼리 편집기를 사용하여 SQL 데이터베이스에 연결한 다음, Transact-SQL 문을 사용하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|이 빠른 시작은 Visual Studio 코드를 사용하여 Azure SQL Database에 연결한 후 Transact-SQL 문을 사용하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.|
|[Visual Studio에서 .NET 사용](sql-database-connect-query-dotnet-visual-studio.md)|이 빠른 시작에서는 .NET Framework를 사용하여 Visual Studio에서 C# 프로그램을 만들어 Azure SQL Database에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|이 빠른 시작에서는 Windows/Linux/macOS에서 .NET Core를 통해 Azure SQL Database에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 C# 프로그램을 만드는 방법을 보여 줍니다.|
|[Go](sql-database-connect-query-go.md)|이 빠른 시작에서는 Go를 사용하여 Azure SQL에 연결하는 방법을 보여 줍니다. 데이터 쿼리 및 수정을 위한 Transact SQL 문도 보여 줍니다.|
|[Java](sql-database-connect-query-java.md)|이 빠른 시작에서는 Java를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.|
|[Node.js](sql-database-connect-query-nodejs.md)|이 빠른 시작에서는 Node.js를 사용하여 Azure SQL Database에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[PHP](sql-database-connect-query-php.md)|이 빠른 시작에서는 PHP를 사용하여 Azure SQL Database에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[Python](sql-database-connect-query-python.md)|이 빠른 시작에서는 Python을 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다. |
|[Ruby](sql-database-connect-query-ruby.md)|이 빠른 시작에서는 Ruby를 사용하여 Azure SQL Database에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>SQL Database 연결에 대한 TLS 고려 사항
TLS(전송 계층 보안)는 Microsoft가 Azure SQL Database에 대한 연결을 위해 제공하거나 지원하는 모든 드라이버에서 사용됩니다. 특별한 구성은 필요하지 않습니다. SQL Server 또는 Azure SQL Database에 대한 모든 연결의 경우 모든 응용 프로그램에서 다음 구성 또는 해당 사항을 설정하는 것이 좋습니다.

 - **암호화 = 켜짐**
 - **TrustServerCertificate = 꺼짐**

일부 시스템은 해당 구성 키워드에 대해 각기 다르지만 해당 키워드를 사용합니다. 이러한 구성은 클라이언트 드라이버에서 서버에서 받은 TLS 인증서의 ID를 확인하도록 합니다.

또한 PCI-DSS(Payment Card Industry - Data Security Standard)를 준수해야 하는 경우 클라이언트에서 TLS 1.1 및 1.0을 비활성화하는 것이 좋습니다.

타사 드라이버는 기본적으로 TLS를 사용할 수 있습니다. Azure SQL Database에 연결할 때 요인이 될 수 있습니다. 포함된 드라이버가 있는 응용 프로그램은 이러한 연결 설정을 제어하도록 허용할 수 없습니다. 중요한 데이터와 상호 작용하는 시스템에서 사용하기 전에 이러한 드라이버와 응용 프로그램의 보안을 검사하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

연결 아키텍처 정보는 [Azure SQL Database 연결 아키텍처](sql-database-connectivity-architecture.md)를 참조하세요.