---
title: Azure Database for MySQL에서 서버 매개 변수를 구성하는 방법
description: 이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL에서 사용 가능한 서버 매개 변수를 구성하는 방법을 설명합니다.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6865663bebc84df288f4c7e2564ddb4870667c6f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성 방법

MySQL용 Azure Database는 일부 서버 매개 변수 구성을 지원합니다. 이 문서에서는 Azure Portal을 사용하여 이러한 매개 변수를 구성하는 방법을 설명합니다. 일부 서버 매개 변수를 조정할 수 있습니다. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Azure Portal의 서버 매개 변수로 이동
1. Azure Portal에 로그인한 다음 Azure Database for MySQL 서버를 찾습니다.
2. **설정** 섹션에서 **서버 매개 변수**를 클릭하여 Azure Database for MySQL에 대한 서버 매개 변수 페이지를 엽니다.
![Azure Portal 서버 매개 변수 페이지](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 조정해야 하는 설정을 모두 찾습니다. **설명** 열을 검토하여 용도 및 허용되는 값을 이해합니다. 
![드롭다운 열거](./media/howto-server-parameters/3-toggle_parameter.png)
4. **저장**을 클릭하여 변경 내용을 저장합니다.
![변경 내용 저장 또는 제거](./media/howto-server-parameters/4-save_parameters.png)
5. 매개 변수에 새 값을 저장한 경우 언제든지 **모두 기본값으로 다시 설정**을 선택하여 모든 항목을 기본값으로 되돌릴 수있습니다.
![모두 기본값으로 다시 설정](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>구성 가능한 서버 매개 변수 목록

지원되는 서버 매개 변수 목록은 계속 확장됩니다. Azure Portal의 서버 매개 변수 탭을 사용하여 정의를 가져오고 응용 프로그램 요구 사항에 따라 서버 매개 변수를 구성합니다. 

## <a name="nonconfigurable-server-parameters"></a>구성 불가능한 서버 매개 변수
InnoDB 버퍼 풀 및 최대 연결은 구성할 수 없으며 [가격 책정 계층](concepts-service-tiers.md)에 연결됩니다. 

|**가격 책정 계층**| **계산 세대**|**vCore**|**InnoDB 버퍼 풀(MB)**| **최대 연결**|
|---|---|---|---|--|
|Basic| 4세대| 1| 1024| 50|
|Basic| 4세대| 2| 2560| 100|
|Basic| 5세대| 1| 1024| 50|
|Basic| 5세대| 2| 2560| 100|
|범용| 4세대| 2| 3584| 300|
|범용| 4세대| 4| 7680| 625|
|범용| 4세대| 8| 15360| 1250|
|범용| 4세대| 16| 31232| 2500|
|범용| 4세대| 32| 62976| 5,000|
|범용| 5세대| 2| 3584| 300|
|범용| 5세대| 4| 7680| 625|
|범용| 5세대| 8| 15360| 1250|
|범용| 5세대| 16| 31232| 2500|
|범용| 5세대| 32| 62976| 5,000|
|메모리 최적화| 5세대| 2| 7168| 600|
|메모리 최적화| 5세대| 4| 15360| 1250|
|메모리 최적화| 5세대| 8| 30720| 2500|
|메모리 최적화| 5세대| 16| 62464| 5,000|

이러한 추가 서버 매개 변수는 시스템에서 구성할 수 없습니다.

|**매개 변수**|**고정 값**|
| :------------------------ | :-------- |
|기본 계층의 innodb_file_per_table|끄기|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

여기에 나열되지 않은 다른 서버 매개 변수는 버전 [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 및 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) 버전의 MySQL 기본값으로 설정됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL에 대한 연결 라이브러리](concepts-connection-libraries.md).
