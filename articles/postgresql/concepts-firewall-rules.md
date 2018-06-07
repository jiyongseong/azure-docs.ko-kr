---
title: "PostgreSQL용 Azure 데이터베이스 서버 방화벽 규칙"
description: "이 문서에서는 Azure Database for PostgreSQL 서버의 방화벽 규칙에 대해 설명합니다."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8a3f5d9fa8f1c36d8468c38f7dda803d3ca1d832
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>PostgreSQL용 Azure 데이터베이스 서버 방화벽 규칙
Azure Database for PostgreSQL 서버 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 금지합니다. 방화벽은 각 요청이 시작된 IP 주소의 서버에 대한 액세스를 허용합니다.
방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 수준의 방화벽 규칙을 만들 수 있습니다.

**방화벽 규칙:** 이 규칙은 모든 PostgreSQL용 Azure 데이터베이스 서버, 즉, 동일한 논리 서버 내의 모든 데이터베이스에 클라이언트가 액세스할 수 있도록 합니다. Azure Portal 또는 Azure CLI 명령을 사용하여 서버 수준 방화벽 규칙을 구성할 수 있습니다. 서버 수준 방화벽 규칙을 만들려면 구독 소유자 또는 구독 참가자여야 합니다.

## <a name="firewall-overview"></a>방화벽 개요
PostgreSQL용 Azure 데이터베이스 서버에 대한 모든 데이터베이스 액세스는 기본적으로 방화벽에 의해 차단됩니다. 다른 컴퓨터에서 서버를 사용하려면 해당 서버에 대한 액세스를 허용하는 하나 이상의 서버 수준 방화벽 규칙을 지정해야 합니다. 방화벽 규칙을 사용하여 허용할 인터넷에서의 IP 주소 범위를 지정합니다. Azure Portal 웹 사이트 자체에 대한 액세스는 이 방화벽 규칙의 영향을 받지 않습니다.
인터넷과 Azure로부터의 연결 시도는 다음 다이어그램과 같이 PostgreSQL Database에 연결하기 전에 먼저 방화벽을 통과해야 합니다.

![방화벽 작동 방식을 보여 주는 예제 흐름](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>인터넷에서 연결하기
서버 수준 방화벽 규칙은 동일한 Azure Database for PostgreSQL 서버에 있는 모든 데이터베이스에 적용됩니다. 요청된 IP 주소가 서버 수준 방화벽 규칙의 지정된 범위 안에 있을 경우, 연결이 허용됩니다.
요청된 IP 주소가 서버 수준 방화벽 규칙의 지정된 범위 안에 없을 경우, 연결 요청이 실패합니다.
예를 들어 응용 프로그램에서 PostgreSQL용 JDBC 드라이버에 연결하는 경우 방화벽이 연결을 차단할 때 연결하려고 하면 다음 오류가 발생할 수 있습니다.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Azure에서 연결
Azure의 응용 프로그램에서 Azure Database for PostgreSQL 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 예를 들어 Azure Web Apps 응용 프로그램 또는 Azure VM에서 실행되는 응용 프로그램을 호스팅하거나 Azure Data Factory 데이터 관리 게이트웨이에서 연결하는 경우가 있습니다. 이러한 연결을 사용하도록 설정하려면 리소스가 방화벽 규칙에 대해 동일한 VNet(Virtual Network) 또는 리소스 그룹에 있을 필요가 없습니다. Azure의 응용 프로그램이 데이터베이스 서버로 연결을 시도할 때, 방화벽은 Azure 연결이 허용되는지 확인합니다. 이러한 유형의 연결을 사용하도록 설정하는 몇 가지 방법이 있습니다. 0.0.0.0으로 된 시작 및 끝 주소가 포함된 방화벽 설정은 연결이 허용됨을 나타냅니다. 또는 포털의 **연결 보안** 창에서 **Azure 서비스 방문 허용** 옵션을 **설정**으로 지정하고 **저장**을 누릅니다. 연결 시도가 허용되지 않으면 해당 요청이 Azure Database for PostgreSQL 서버에 도달하지 않습니다.

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
> 

![포털에서 Azure 서비스 방문 허용 구성](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>방화벽 규칙을 프로그래밍 방식으로 관리
Azure Portal 외에도 Azure CLI를 사용하여 방화벽 규칙을 프로그래밍 방식으로 관리할 수 있습니다.
[Azure CLI를 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-cli.md)도 참조하세요.

## <a name="troubleshooting-the-database-server-firewall"></a>데이터베이스 서버 방화벽 문제 해결
PostgreSQL용 Microsoft Azure 데이터베이스 서버 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **허용 목록의 변경사항이 아직 적용되지 않았습니다.** PostgreSQL용 Azure 데이터베이스 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **로그인이 올바르지 않거나 암호가 올바르지 않습니다.** 로그인에 PostgreSQL용 Azure 데이터베이스 서버에 대한 권한이 없거나 사용한 암호가 틀렸을 경우 PostgreSQL용 Azure 데이터베이스 서버에 대한 연결이 거부됩니다. 방화벽 설정을 생성하면 클라이언트에게 서버 연결을 시도할 수 있는 기회만 제공되며 각 클라이언트는 필요한 보안 자격 증명을 제공해야 합니다.

예를 들어 JDBC 클라이언트를 사용하는 경우 다음 오류가 나타날 수 있습니다.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **동적 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

* ISP(인터넷 서비스 공급자)는 PostgreSQL용 Azure 데이터베이스 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.

* 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

## <a name="next-steps"></a>다음 단계
서버 수준 및 데이터베이스 수준 방화벽 규칙 만들기에 대한 문서를 보려면 다음을 참조하세요.
* [Azure Portal을 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-portal.md)
* [Azure CLI를 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-cli.md)
