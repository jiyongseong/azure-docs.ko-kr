---
title: "Azure Database for MySQL의 서버 로그"
description: "Azure Database for MySQL에서 사용할 수 있는 로그와, 다양한 로깅 수준을 활성화하는 데 사용할 수 있는 매개 변수에 대해 설명합니다."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ce6b6208b74063ea5d6e9868ca414f833b1a2045
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL의 서버 로그
Azure Database for MySQL에서는 사용자에게 느린 쿼리 로그를 제공합니다. 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 느린 쿼리 로그를 사용하여 문제 해결을 위한 성능 병목을 파악할 수 있습니다. 

MySQL 느린 쿼리 로그에 대한 자세한 내용은 MySQL 참조 설명서의 [느린 쿼리 로그 섹션](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)을 참조하세요.

## <a name="access-server-logs"></a>서버 로그 액세스
Azure Portal 및 Azure CLI를 사용하여 Azure Database for MySQL 서버 로그를 나열 및 다운로드할 수 있습니다.

Azure Portal에서 Azure Database for MySQL Server를 찾습니다. **모니터링** 머리글 아레에서 **서버 로그** 페이지를 선택합니다.

Azure CLI에 대한 자세한 내용은 [Azure CLI를 사용한 서버 로그 구성 및 액세스](howto-configure-server-logs-in-cli.md)를 참조하세요.

## <a name="log-retention"></a>로그 보존
로그는 생성 시점에서 최대 7일까지 사용할 수 있습니다. 사용 가능한 로그의 전체 크기가 7.5GB를 초과하면 여유 공간이 생길 때까지 가장 오래된 파일을 삭제합니다. 

로그는 24시간마다 또는 7.5GB마다 회전됩니다(먼저 해당되는 쪽).


## <a name="configure-logging"></a>로깅 구성 
기본적으로 느린 쿼리 로그는 비활성화됩니다. 활성화하려면 slow_query_log를 ON으로 설정합니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time**: 쿼리가 기록되는 long_query_time(초)보다 쿼리가 오래 걸릴 경우 기본값은 10초입니다.
- **log_slow_admin_statements**: ON에 slow_query_log에 쓰여진 문에서 ALTER_TABLE 및 ANALYZE_TABLE 등과 같은 관리 문이 포함된 경우
- **log_queries_not_using_indexes**: 인덱스를 사용하지 않는 쿼리가 slow_query_log에 기록되는지 여부를 결정합니다.
- **log_throttle_queries_not_using_indexes**:이 매개 변수는 느린 쿼리 로그에 쓸 수 있는 비 인덱스 쿼리의 수 한도를 결정합니다. 이 매개 변수는 log_queries_not_using_indexes가 ON으로 설정된 경우 적용됩니다.

느린 쿼리 로그 매개 변수의 전체 설명은 MySQL [느린 쿼리 로그 설명서](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
- [Azure CLI에서 서버 로그 구성 및 액세스](howto-configure-server-logs-in-cli.md)
