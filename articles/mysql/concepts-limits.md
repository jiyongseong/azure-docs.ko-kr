---
title: Azure Database for MySQL의 제한 사항
description: 이 문서에서는 Azure Database for MySQL에 대한 연결 수 및 저장소 엔진 옵션과 같은 제한 사항을 설명합니다.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 2fa69182b4238cfd19fcc9571e4327512e9528c1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL의 제한 사항
다음 섹션에서는 데이터베이스 서비스의 용량, 저장소 엔진 지원, 권한 지원, 데이터 조작 명령문 지원 및 기능 제한 사항에 대해 설명합니다. 또한 MySQL 데이터베이스 엔진에 적용할 수 있는 [일반적인 제한 사항](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)도 참조하세요.

## <a name="service-tier-maximums"></a>서비스 계층 최대값
Azure Database for MySQL에는 서버를 만들 때 선택할 수 있는 여러 서비스 계층이 있습니다. 자세한 내용은 [Azure Database for MySQL 가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.  

각 서비스 계층에는 다음과 같은 최대 연결 수, 계산 단위 및 저장소가 있습니다. 

|**가격 책정 계층**| **계산 세대**|**vCore**| **최대 연결**|
|---|---|---|---|
|Basic| 4세대| 1| 50|
|Basic| 4세대| 2| 100|
|Basic| 5세대| 1| 50|
|Basic| 5세대| 2| 100|
|범용| 4세대| 2| 300|
|범용| 4세대| 4| 625|
|범용| 4세대| 8| 1250|
|범용| 4세대| 16| 2500|
|범용| 4세대| 32| 5,000|
|범용| 5세대| 2| 300|
|범용| 5세대| 4| 625|
|범용| 5세대| 8| 1250|
|범용| 5세대| 16| 2500|
|범용| 5세대| 32| 5,000|
|메모리 최적화| 5세대| 2| 600|
|메모리 최적화| 5세대| 4| 1250|
|메모리 최적화| 5세대| 8| 2500|
|메모리 최적화| 5세대| 16| 5,000|

너무 많은 연결에 도달하면 다음 오류가 나타날 수 있습니다.
> 오류 1040(08004): 너무 많은 연결이 있습니다.

## <a name="storage-engine-support"></a>저장소 엔진 지원

### <a name="supported"></a>지원됨
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>지원되지 않음
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>권한 지원

### <a name="unsupported"></a>지원되지 않음
- DBA 역할: 서버 매개 변수 및 설정이 많으면 실수로 서버 성능이 저하되거나 DBMS의 ACID 속성이 무효화될 수 있습니다. 따라서 제품 수준에서 서비스 무결성 및 SLA를 유지하기 위해 이 서비스에서는 DBA 역할이 노출되지 않습니다. 사용자는 새 데이터베이스 인스턴스를 만들 때 생성되는 기본 사용자 계정을 통해 관리되는 데이터베이스 인스턴스에서 대부분의 DDL 및 DML 문을 수행할 수 있습니다. 
- SUPER 권한: 마찬가지로 [SUPER 권한](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)도 제한됩니다.

## <a name="data-manipulation-statement-support"></a>데이터 조작 명령문 지원

### <a name="supported"></a>지원됨
- LOAD DATA INFILE - 지원되지만, UNC 경로(XSMB를 통해 탑재된 Azure 저장소)로 전달되는 [LOCAL] 매개 변수를 지정해야 합니다.

### <a name="unsupported"></a>지원되지 않음
- SELECT ... INTO OUTFILE

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="scale-operations"></a>크기 조정 작업
- 가격 책정 계층 간 서버의 동적 크기 조정은 현재 지원되지 않습니다. 즉, 기본, 범용 및 메모리 최적화 가격 책정 계층 사이의 전환을 말합니다.
- 서버 저장소 크기를 줄이는 것은 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
- 다른 서비스 계층 및/또는 Compute 단위 및 저장소 크기로 복원할 수 없습니다.
- 삭제된 서버 복원은 지원되지 않습니다.

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="subscription-management"></a>구독 관리
- 구독 및 리소스 그룹에서 미리 생성된 서버를 동적으로 이동하는 것은 현재 지원되지 않습니다.

## <a name="current-known-issues"></a>현재 알려진 문제
- 연결이 설정된 후에 MySQL 서버 인스턴스에서 잘못된 서버 버전을 표시합니다. 올바른 서버 인스턴스 버전 관리를 가져오려면 MySQL 프롬프트에서 select version(); 명령을 입력합니다.

## <a name="next-steps"></a>다음 단계
- [각 서비스 계층에서 사용할 수 있는 기능](concepts-pricing-tiers.md)
- [지원되는 MySQL 데이터베이스 버전](concepts-supported-versions.md)
