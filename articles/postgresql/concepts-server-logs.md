---
title: "PostgreSQL용 Azure 데이터베이스의 서버 로그"
description: "이 문서에서는 Azure Database for PostgreSQL에서 쿼리 및 오류 로그를 생성하는 방법과 로그 보존을 구성하는 방법을 설명합니다."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: a8d560aa8906e3ba1f65758239b645cd1b1df032
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스의 서버 로그 
PostgreSQL용 Azure 데이터베이스에서는 쿼리 및 오류 로그를 생성합니다. 그러나 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 쿼리 및 오류 로그는 구성 오류 및 최적 상태가 아닌 성능 문제를 식별하고, 문제를 해결하고, 복구하는 데 사용될 수 있습니다. 자세한 내용은 [오류 보고 및 로깅](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)을 참조하세요.

## <a name="access-server-logs"></a>서버 로그 액세스
Azure Portal, [Azure CLI](howto-configure-server-logs-using-cli.md) 및 Azure REST API를 사용하여 Azure PostgreSQL 서버 오류 로그를 나열하고 다운로드할 수 있습니다.

## <a name="log-retention"></a>로그 보존
서버에 연결된 **log\_retention\_period** 매개 변수를 사용하여 시스템 로그의 보존 기간을 설정할 수 있습니다. 이 매개 변수의 단위는 일입니다. 기본값은 3일입니다. 최대값은 7일입니다. 서버에 보존된 로그 파일을 포함할만큼 충분한 저장소가 할당되어야 합니다.
로그 파일은 1시간마다 또는 100MB 크기가 될 때마다(둘 중 먼저 도달할 때) 순환됩니다.

## <a name="configure-logging-for-azure-postgresql-server"></a>Azure PostgreSQL 서버에 대한 로깅 구성
서버에 대한 쿼리 로깅 및 오류 로그를 사용하도록 설정할 수 있습니다. 오류 로그에는 자동 진공, 연결 및 검사점 정보가 포함될 수 있습니다.

두 개의 서버 매개 변수 `log\_statement` 및 `log\_min\_duration\_statement`를 설정하여 PostgreSQL DB 인스턴스에 대한 쿼리 로깅을 활성화할 수 있습니다.

**log\_statement** 매개 변수는 로깅되는 SQL 문을 제어합니다. 모든 문을 로깅하려면 이 매개 변수를 ***all***로 설정하는 것이 좋습니다. 기본값은 none입니다.

**log\_min\_duration\_statement** 매개 변수는 로깅할 문에 대한 제한(밀리초)을 설정합니다. 이 매개 변수 설정보다 오래 실행되는 모든 SQL 문은 로깅됩니다. 이 매개 변수는 사용되지 않도록 설정되어 있으며, 기본적으로 -1로 설정됩니다. 이 매개 변수를 사용하도록 설정하면 응용 프로그램에서 최적화되지 않은 쿼리를 추적하는 데 도움이 될 수 있습니다.

**log\_min\_messages**를 사용하여 서버 로그에 기록되는 메시지 수준을 제어할 수 있습니다. 기본값은 WARNING입니다. 

이러한 설정에 대한 자세한 내용은 [오류 보고 및 로깅](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) 설명서를 참조하세요. Azure Database for PostgreSQL 서버 매개 변수를 특별히 구성하려면 [Azure CLI를 사용하여 서버 구성 매개 변수 사용자 지정](howto-configure-server-parameters-using-cli.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- Azure CLI 명령줄 인터페이스를 사용하여 로그에 액세스하려면 [Azure CLI를 사용하여 서버 로그 구성 및 액세스](howto-configure-server-logs-using-cli.md)를 참조하세요.
- 서버 매개 변수에 대한 자세한 내용은 [Azure CLI를 사용하여 서버 구성 매개 변수 사용자 지정](howto-configure-server-parameters-using-cli.md)을 참조하세요.
