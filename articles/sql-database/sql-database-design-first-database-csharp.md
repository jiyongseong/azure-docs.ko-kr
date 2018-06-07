---
title: 첫 번째 Azure SQL Database 설계 - C# | Microsoft Docs
description: 첫 번째 Azure SQL Database를 설계하고 ADO.NET을 사용하여 C# 프로그램과 연결하는 방법을 알아봅니다.
services: sql-database
author: MightyPen
manager: craigg-msft
ms.reviewer: CarlRabeler
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.topic: tutorial
ms.date: 03/15/2018
ms.openlocfilehash: 3b6f260983e3c826bf558f0fe6d1a0fa6ae6b3af
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Azure SQL Database 설계 및 C#과 ADO.NET에 연결

Azure SQL Database는 Microsoft Cloud(Azure)의 관계형 DBaaS(Database-As-A-Service)입니다. 이 자습서에서는 Visual Studio에서 Azure Portal 및 ADO.NET을 사용하여 다음과 같은 작업을 수행하는 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Azure Portal에서 데이터베이스 만들기
> * Azure Portal에서 서버 수준 방화벽 규칙 설정
> * ADO.NET 및 Visual Studio를 사용하여 데이터베이스에 연결
> * ADO.NET을 사용하여 테이블 만들기
> * ADO.NET을 사용하여 데이터 삽입, 업데이트 및 삭제 
> * ADO.NET 데이터 쿼리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

설치된 [Visual Studio Community 2017, Visual Studio Professional 2017 또는 Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/)

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 데이터베이스 및 테이블 만들기, 데이터 로드 및 쿼리, 데이터베이스를 이전 시점으로 복원과 같은 기본적인 데이터베이스 작업에 대해 배웁니다. 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * 데이터베이스 만들기
> * 방화벽 규칙 설정
> * [Visual Studio 및 C#](sql-database-connect-query-dotnet-visual-studio.md)을 사용하여 데이터베이스에 연결
> * 테이블 만들기
> * 데이터 삽입, 업데이트 및 삭제
> * 쿼리 데이터

데이터를 마이그레이션하는 방법에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
>[SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션](sql-database-migrate-your-sql-server-database.md)

