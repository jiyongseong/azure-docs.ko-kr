---
title: Azure SQL Database에 대한 일반적인 연결 문제 해결
description: Azure SQL Database에 대한 일반적인 연결 오류를 확인 및 해결하는 단계
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: daleche
ms.openlocfilehash: 2737b641559b04d661db6ede0e487af30b36737a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Azure SQL Database에 대한 연결 문제 해결
Azure SQL Database에 대한 연결이 실패하면 [오류 메시지](sql-database-develop-error-messages.md)가 표시됩니다. 이 문서는 Azure SQL Database 연결 문제를 해결하는 데 도움이 되는 중앙 집중식 항목입니다. 여기서는 연결 문제의 [일반적인 원인](#cause)을 소개하고, 문제 식별에 도움이 되는 [문제 해결 도구](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues)를 추천하며, [일시적인 오류](#troubleshoot-transient-errors) 및 [영구적이거나 일시적이지 않은 오류](#troubleshoot-persistent-errors)를 해결하는 문제 해결 단계를 제공합니다. 

연결 문제가 발생하는 경우 이 문서에 설명된 문제 해결 단계를 수행합니다.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>원인
연결 문제는 다음 중 하나로 인해 발생할 수 있습니다.

* 응용 프로그램 디자인 프로세스 동안 모범 사례 및 디자인 지침을 적용하지 못함.  시작하려면 [SQL Database 개발 개요](sql-database-develop-overview.md) 를 참조하세요.
* Azure SQL Database 재구성
* 방화벽 설정
* 연결 제한 시간
* 잘못된 로그인 정보
* 일부 Azure SQL Database 리소스에서 최대 한도 도달

일반적으로 Azure SQL Database에 대한 연결 문제는 다음과 같이 분류할 수 있습니다.

* [일시적인 오류(단기 또는 일시적)](#troubleshoot-transient-errors)
* [영구적 또는 일시적이지 않은 오류(정기적으로 되풀이되는 오류)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Azure SQL Database 연결 문제에 대한 문제 해결사 시도
특정 연결 오류가 발생하면 문제를 빠르게 식별하고 해결하는 데 도움이 되는 [이 도구](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)를 사용해보세요.

## <a name="troubleshoot-transient-errors"></a>일시적인 오류 문제 해결

응용 프로그램이 Azure SQL Database에 연결할 때 다음 오류 메시지가 표시됩니다.

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> 이 오류 메시지는 대개 일시적으로 나타납니다.
> 
> 

Azure 데이터베이스를 이동하거나 다시 구성하는 중이어서 SQL데이터베이스에 대한 응용 프로그램의 연결이 끊기면 이 오류가 발생합니다. SQL Database 재구성 이벤트는 계획된 이벤트(예: 소프트웨어 업그레이드) 또는 계획되지 않은 이벤트(예: 프로세스 충돌 또는 부하 분산) 때문에 발생합니다. 대부분의 다시 구성 이벤트는 보통 일시적으로 발생하며 최대 60초 이내에 완료됩니다. 그러나 큰 트랜잭션으로 인해 복구가 오래 실행되는 등 이러한 이벤트가 완료되는 데 시간이 더 오래 걸리는 경우도 있습니다.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>일시적인 연결 문제를 해결하는 단계

1. [Microsoft Azure 서비스 대시보드](https://azure.microsoft.com/status) 에서 응용 프로그램이 오류를 보고한 시간 동안 발생한 알려진 서비스 중단을 확인합니다.
2. Azure SQL Database와 같이 클라우드 서비스에 연결하는 응용 프로그램에서는 주기적으로 다시 구성 이벤트가 발생하므로, 이러한 이벤트를 사용자에게 응용 프로그램 오류로 표시하는 대신 해당 오류를 처리하는 다시 시도 논리를 구현해야 합니다. 자세한 내용과 일반적인 다시 시도 전략을 확인하려면 [일시적인 오류](sql-database-connectivity-issues.md) 섹션과 [SQL Database 개발 개요](sql-database-develop-overview.md)에서 모범 사례 및 설계 지침을 검토하세요. 그런 다음 세부 사항은 [SQL Database 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md) 에서 코드 샘플을 참조하세요.
3. 데이터베이스가 리소스 한계에 도달하면 일시적인 연결 문제가 발생한 것처럼 보일 수 있습니다. [성능 문제 해결](sql-database-troubleshoot-performance.md)을 참조하세요.
4. 연결 문제가 계속 발생하거나 응용 프로그램에서 오류가 발생하는 기간이 60초를 초과하는 경우 또는 특정일에 오류가 여러 번 발생하는 경우에는 **Azure 지원** 사이트에서 [지원 받기](https://azure.microsoft.com/support/options) 를 선택하여 Azure 지원 요청을 접수합니다.

## <a name="troubleshoot-persistent-errors"></a>영구 오류 문제 해결
응용 프로그램이 Azure SQL Database 연결에 계속 실패하는 경우 일반적으로 다음 문제 중 하나를 나타낼 수 있습니다.

* 방화벽 구성. Azure SQL Database 또는 클라이언트 쪽 방화벽이 Azure SQL Database에 대한 연결을 차단하고 있습니다.
* 클라이언트 쪽 네트워크 재구성: 예를 들어 새 IP 주소 또는 프록시 서버.
* 사용자 오류: 예를 들어 연결 문자열에서 서버 이름과 같이 연결 매개 변수를 잘못 입력했습니다.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>영구적인 연결 문제를 해결하는 단계
1. 클라이언트 IP 주소를 허용하도록 [방화벽 규칙](sql-database-configure-firewall-settings.md) 을 설정합니다. 임시 테스트 목적으로 시작 IP 주소 범위로 0.0.0.0을 사용하고 끝 IP 주소 범위로 255.255.255.255를 사용하여 방화벽 규칙을 설정합니다. 이렇게 하면 서버가 모든 IP 주소로 열립니다. 이렇게 해서 연결 문제가 해결되면 이 규칙을 제거하고 적절하게 제한된 IP 주소 또는 주소 범위에 대해 방화벽 규칙을 만듭니다. 
2. 클라이언트와 인터넷 간의 모든 방화벽에서 포트 1433이 아웃바운드 연결에 대해 열려 있는지 확인합니다. [SQL Server 액세스를 허용하도록 Windows 방화벽 구성](https://msdn.microsoft.com/library/cc646023.aspx) 및 [포트 및 프로토콜이 필요한 하이브리드 ID](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports)에서 Azure Active Directory 인증을 위해 열어야 하는 추가 포트와 관련된 추가 포인터를 검토하세요.
3. 연결 문자열 및 기타 연결 설정을 확인합니다. [연결 문제 항목](sql-database-connectivity-issues.md#connections-to-sql-database)의 연결 문자열 섹션을 참조하세요.
4. 대시보드에서 서비스 상태를 확인합니다. 지역별 가동 중단이 있다고 생각되는 경우 [가동 중단에서 복구](sql-database-disaster-recovery.md) 를 참조하여 새 지역으로 복구하는 단계를 따르세요.

## <a name="next-steps"></a>다음 단계
* [Azure SQL Database 성능 문제 해결](sql-database-troubleshoot-performance.md)
* [Microsoft Azure 설명서 검색](http://azure.microsoft.com/search/documentation/)
* [Azure SQL Database 서비스에 대한 최신 업데이트 보기](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>추가 리소스
* [SQL Database 개발 개요](sql-database-develop-overview.md)
* [일반적인 일시적 오류 처리 지침](../best-practices-retry-general.md)
* [SQL Database 및 SQL Server용 연결 라이브러리](sql-database-libraries.md)

