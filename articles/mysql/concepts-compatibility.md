---
title: "MySQL 드라이버 및 관리 도구 호환성"
description: "이 문서에서는 Azure Database for MySQL과 호환되는 MySQL 드라이버 및 관리 도구를 설명합니다."
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 5fc13ef07b61feb9e9fdd73123a09daa61f6aaf3
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Azure Database for MySQL과 호환되는 MySQL 드라이버 및 관리 도구
이 문서에서는 Azure Database for MySQL과 호환되는 드라이버 및 관리 도구를 설명합니다.

## <a name="mysql-drivers"></a>MySQL 드라이버
Azure Database for MySQL은 전 세계에서 가장 인기 있는 커뮤니티 버전의 MySQL 데이터베이스를 사용합니다. 따라서 다양한 프로그래밍 언어 및 드라이버와 호환됩니다. 세 가지 최신 버전의 MySQL 드라이버를 지원하는 것이 목표이며, MySQL 드라이버의 기능 및 사용 편의를 개선하기 위해 지속적으로 오픈 소스 커뮤니티의 작성자와 협력하고 있습니다. 다음 표에는 테스트를 통해 Azure Database for MySQL 5.6 및 5.7과 호환되는 것으로 확인된 드라이버 목록이 정리되어 있습니다.

| **드라이버** | **연결** | **호환되는 버전** | **호환되지 않는 버전** | **참고 사항** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5.3 | PHP 7.0과 SSL MySQLi 연결의 경우 연결 문자열에 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT를 추가합니다. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 집합: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 옵션을 false로 설정합니다.|
| .Net | [GitHub의 MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [Nuget의 설치 패키지](https://www.nuget.org/packages/MySqlConnector/) | 0.27 이상 | 0.26.5 이하 | |
| Nodejs |  [Github의 MySQLjs](https://github.com/mysqljs/mysql/releases) <br> NPM의 설치 패키지:<br> NPM에서 `npm install mysql` 실행 | 2.15 | 2.14.1 이하 | |
| 이동 | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 이하 | 연결 문자열에 allowNativePasswords=true 사용 |
| 파이썬 | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 이하 | |
| 자바 | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 이하 | |

## <a name="management-tools"></a>관리 도구
호환성 장점은 데이터베이스 관리 도구까지 확장됩니다. 데이터베이스 조작이 사용자 권한의 범위 내에서 이루어지는 한, 기존 도구가 Azure Database for MySQL과 계속 호환됩니다. 다음 표에는 테스트를 통해 Azure Database for MySQL 5.6 및 5.7과 호환되는 것으로 확인된 세 가지 일반적인 데이터베이스 관리 도구가 정리되어 있습니다.

|                                     | **MySQL Workbench 6.x 이상** | **Navicat 12** | **PHPMyAdmin 4.x 이상** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| 만들기, 업데이트, 읽기, 쓰기, 삭제 | X | X | X |
| SSL 연결 | X | X | X |
| SQL 쿼리 자동 완성 | X | X |  |
| 데이터 가져오기 및 내보내기 | X | X | X |
| 여러 형식으로 내보내기 | X | X | X |
| Backup 및 복원 |  | X |  |
| 서버 매개 변수 표시 | X | X | X |
| 클라이언트 연결 표시 | X | X | X |
