---
title: SQL Database 응용 프로그램 개발 개요 | Microsoft 문서
description: 사용 가능한 연결 라이브러리 및 SQL Database에 연결하는 응용 프로그램에 대한 모범 사례를 알아봅니다.
services: sql-database
author: stevestein
manager: craigg
ms.reviewer: genemi
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 37f143ce9dabdc2d0eaf6c60dd551c682a141c17
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187677"
---
# <a name="sql-database-application-development-overview"></a>SQL Database 응용 프로그램 개발 개요
이 문서는 Azure SQL Database에 연결하기 위한 코드를 작성하면서 개발자가 알고 있어야 하는 기본적인 사항을 안내합니다.

> [!TIP]
> 서버 만들기, 서버 기반 방화벽 만들기, 서버 속성 보기, SQL Server Management Studio로 연결, master 데이터베이스 쿼리, 샘플 데이터베이스 및 빈 데이터베이스 만들기, 데이터베이스 속성 쿼리, SQL Server Management Studio로 샘플 데이터베이스 연결 및 쿼리를 수행하는 방법을 보여 주는 자습서에 대해서는 [시작 자습서](sql-database-get-started-portal.md)를 참조하세요.
>

## <a name="language-and-platform"></a>언어 및 플랫폼
다양한 프로그래밍 언어 및 플랫폼에 대한 코드 샘플을 사용할 수 있습니다. 다음에서 코드 샘플에 대한 링크를 찾을 수 있습니다. 

* 추가 정보: [SQL Database 및 SQL Server용 연결 라이브러리](sql-database-libraries.md).

## <a name="tools"></a>도구 
[cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/)와 같은 오픈 소스 도구를 활용할 수 있습니다. 또한 Azure SQL Database는 [Visual Studio](https://www.visualstudio.com/downloads/) 및 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)와 같은 Microsoft 도구로 작동합니다.  Azure 관리 포털, PowerShell 및 REST API를 사용하면 추가 생산성을 얻을 수도 있습니다.

## <a name="resource-limitations"></a>리소스 제한
Azure SQL Database는 리소스 관리와 제한 적용이라는 서로 다른 두 메커니즘을 사용하는 데이터베이스에서 사용 가능한 리소스를 관리합니다.

* 자세한 내용은 [Azure SQL Database DTU 기반 리소스 모델 제한](sql-database-dtu-resource-limits.md) 및 [Azure SQL Database vCore 기반 리소스 모델 제한(미리 보기)](sql-database-vcore-resource-limits.md)을 참조하세요.

## <a name="security"></a>보안
Azure SQL Database는 액세스를 제한하고, 데이터를 보호하고, SQL Database의 활동을 모니터링하는 리소스를 제공합니다.

* 추가 정보: [SQL Database 보안 설정](sql-database-security-overview.md).

## <a name="authentication"></a>인증
* Azure SQL Database는 SQL Server 인증 사용자 및 로그인과 [Azure Active Directory 인증](sql-database-aad-authentication.md) 사용자 및 로그인을 둘 다 지원합니다.
* *master* 데이터베이스를 기본 데이터베이스로 지정하지 말고 특정 데이터베이스를 지정해야 합니다.
* SQL Database에서는 Transact-SQL **USE myDatabaseName;** 문장을 사용하여 다른 데이터베이스로 전환할 수 없습니다.
* 추가 정보: [SQL Database 보안: 데이터베이스 액세스 및 로그인 보안 관리](sql-database-manage-logins.md).

## <a name="resiliency"></a>복원력
SQL Database에 연결하는 동안 일시적인 오류가 발생하면, 코드는 호출을 다시 시도해야 합니다.  여러 클라이언트가 재시도를 동시에 수행하여 SQL Database가 채워지지 않도록 재시도 논리에 백오프 논리를 사용하는 것이 좋습니다.

* 코드 샘플: 재시도 논리를 설명하는 코드 샘플을 보려면 [SQL Database 및 SQL Server용 연결 라이브러리](sql-database-libraries.md) 참조.
* 추가 정보: [SQL Database 클라이언트 프로그램에 대한 오류 메시지](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>연결 관리
* 클라이언트 연결 논리에서 기본 시간 제한을 30초로 재정의합니다.  기본값 15초는 인터넷에 종속된 연결 시간으로 너무 짧습니다.
* [연결 풀](http://msdn.microsoft.com/library/8xx3tyca.aspx)을 사용하는 경우 프로그램에서 활발하게 사용하지 않고 다시 사용할 준비를 하지 않으면 연결을 즉시 닫아야 합니다.

## <a name="network-considerations"></a>네트워크 고려 사항
* 클라이언트 프로그램을 호스팅하는 컴퓨터에서 방화벽이 포트 1433에서 발신 TCP 통신을 허용하는지 확인합니다.  추가 정보: [Azure SQL Database 방화벽 구성](sql-database-configure-firewall-settings.md).
* Azure VM(가상 머신)에서 클라이언트가 실행되는 동안 클라이언트 프로그램이 SQL Database에 연결하는 경우에는, VM의 특정 포트 범위를 열어야 합니다. 추가 정보: [ADO.NET 4.5 및 SQL Database에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md).
* Azure SQL Database에 대한 클라이언트 연결이 프록시를 바이패스하고 데이터베이스와 직접 상호 작용하는 경우가 있습니다. 1433 이외의 포트가 중요해집니다. 자세한 내용은 [Azure SQL Database 연결 아키텍처](sql-database-connectivity-architecture.md) 및 [ADO.NET 4.5 및 SQL Database에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.

## <a name="data-sharding-with-elastic-scale"></a>탄력적인 확장을 사용한 데이터 분할
탄력적 확장은 확장(및 축소) 프로세스를 간소화합니다. 

* [Azure SQL Database를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md).
* [Azure SQL Database 탄력적인 확장 미리 보기 시작](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>다음 단계
모든 [SQL Database의 기능](sql-database-technical-overview.md)을 탐색합니다.
