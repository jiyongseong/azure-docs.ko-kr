---
title: Azure Database for MySQL에서 지원되는 버전
description: MySQL용 Azure 데이터베이스에서 지원되는 버전에 대해 설명합니다.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: 53c8d51ddf9b7465a99b8b0685d7f6ce177fc526
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>지원되는 MySQL용 Azure 데이터베이스 서버 버전
Azure Database for MySQL은 [MySQL Community Edition](https://www.mysql.com/products/community/)에서 InnoDB 엔진을 사용하여 개발되었습니다.  Azure Database for MySQL은 현재 다음 버전을 지원합니다.

## <a name="mysql-version-5638"></a>MySQL 버전 5.6.38
MySQL 5.6.38의 향상된 기능 및 수정 내용에 대한 자세한 내용은 MySQL [설명서](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html)를 참조하세요.

## <a name="mysql-version-5720"></a>MySQL 버전 5.7.20
MySQL 5.7.20의 향상된 기능 및 수정 내용에 대한 자세한 내용은 MySQL [설명서](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.html)를 참조하세요.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다. 

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 부 버전 업데이트에 대한 패치를 자동으로 관리합니다. 주 버전 업그레이드가 지원되지 않습니다(예: MySQL 5.6에서 MySQL 5.7로 업그레이드).

## <a name="next-steps"></a>다음 단계

**서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
