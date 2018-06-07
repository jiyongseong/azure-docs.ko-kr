---
title: "Azure Database for MySQL로 최소 가동 중지 시간 마이그레이션"
description: "이 문서에서는 Azure Database for MySQL로 MySQL 데이터베이스의 최소 가동 중지 시간을 마이그레이션하는 방법 및 Microsoft 마이그레이션에서 Attunity Replicate를 사용하여 데이터베이스의 초기 로드 및 원본 데이터베이스에서 대상 데이터베이스로 지속적인 데이터 동기화를 설정하는 방법을 설명합니다."
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e1be72d97570643cc8a7c6eb05d3d363e96357b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL로 최소 가동 중지 시간 마이그레이션
Microsoft 마이그레이션에서 Attunity Replicate를 사용하여 기존 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션할 수 있습니다. Attunity Replicate는 Attunity 및 Microsoft에서 제공하는 결합입니다. Azure Database Migration Service와 함께 Microsoft 고객에게 추가 비용 없이 제공됩니다. 

Attunity Replicate를 통해 데이터베이스를 마이그레이션하는 동안 가동 중지 시간을 최소화하고 프로세스 과정에서 원본 데이터베이스를 작동시킬 수 있습니다.

Attunity Replicate는 다양한 원본과 대상 간에 데이터 동기화를 사용하도록 설정하는 데이터 복제 도구입니다. 스키마 생성 스크립트와 각 데이터베이스 테이블과 연결된 데이터를 전파합니다. Attunity Replicate는 다른 아티팩트(예: SP, 트리거, 함수 등)를 전파하거나 이러한 아티팩트에 호스트되는 PL/SQL 코드를 T-SQL로 변환하지 않습니다.

> [!NOTE]
> Attunity Replicate는 광범위한 마이그레이션 시나리오를 지원하지만 원본/대상 쌍의 특정 하위 집합에 대한 지원에 초점을 두고 있습니다.

최소 가동 중지 시간 마이그레이션을 수행하기 위한 대략적인 프로세스는 다음과 같습니다.

* [MySQL Workbench](https://www.mysql.com/products/workbench/)를 사용하여 Azure Database for MySQL 관리 데이터베이스 서비스로 **MySQL 원본 스키마를 마이그레이션**합니다.

* Attunity Replicate for Microsoft Migrations를 사용하여 **초기 부하 및 원본 데이터베이스에서 대상 데이터베이스로의 지속적 데이터 동기화를 설정**합니다. 이렇게 하면 Azure에서 응용 프로그램을 대상 MySQL 데이터베이스로 전환하기 위한 준비를 할 때 원본 데이터베이스를 읽기 전용으로 설정해야 하는 시간이 최소화됩니다.

Attunity Replicate for Microsoft Migrations에 대한 자세한 내용은 다음 리소스를 참조하세요.
 - [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate) 웹 페이지로 이동합니다.
 - [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)를 다운로드합니다.
 - 빠른 시작 가이드, 자습서 및 지원은 [Attunity Replicate 커뮤니티](https://aka.ms/attunity-community)로 이동합니다.
 - Attunity Replicate를 사용하여 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션하는 방법에 대한 단계별 지침은 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql)를 참조하세요.