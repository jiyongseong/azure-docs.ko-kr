---
title: PostgreSQL용 Azure 데이터베이스의 제한 사항
description: 이 문서는 Azure Database for PostgreSQL에 대한 연결 수 및 저장소 엔진 옵션과 같은 제한 사항을 설명합니다.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 7e06cdba7c9c9f7e5c1d621e7421a18c342c0fdb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스의 제한 사항
다음 섹션에서는 데이터베이스 서비스의 용량 및 기능 제한에 대해 설명합니다.

## <a name="pricing-tier-maximums"></a>가격 책정 계층 최댓값
Azure Database for PostgreSQL에는 서버를 만들 때 선택할 수 있는 가격 책정 계층이 여러 개 있습니다. 자세한 내용은 [Azure Database for PostgreSQL의 가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.  

각 가격 책정 계층에는 다음과 같은 최대 연결 수, 계산 단위 및 저장소가 있습니다. 

|가격 책정 계층| 계산 세대| vCore| 최대 연결 수 |
|---|---|---|---|
|Basic| 4세대| 1| 50 |
|Basic| 4세대| 2| 100 |
|Basic| 5세대| 1| 50 |
|Basic| 5세대| 2| 100 |
|범용| 4세대| 2| 150|
|범용| 4세대| 4| 250|
|범용| 4세대| 8| 480|
|범용| 4세대| 16| 950|
|범용| 4세대| 32| 1500|
|범용| 5세대| 2| 150|
|범용| 5세대| 4| 250|
|범용| 5세대| 8| 480|
|범용| 5세대| 16| 950|
|범용| 5세대| 32| 1500|
|메모리 최적화| 5세대| 2| 150|
|메모리 최적화| 5세대| 4| 250|
|메모리 최적화| 5세대| 8| 480|
|메모리 최적화| 5세대| 16| 950|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류: 너무 많은 클라이언트가 이미 연결되어 있습니다.

Azure 시스템에는 Azure Database for PostgreSQL 서버를 모니터링하기 위해 5개의 연결이 필요합니다. 

## <a name="functional-limitations"></a>기능 제한 사항
### <a name="scale-operations"></a>크기 조정 작업
1.  가격 책정 계층 간 서버의 동적 크기 조정은 현재 지원되지 않습니다. 즉, 기본, 범용 또는 메모리 최적화 계층 사이의 전환을 말합니다.
2.  서버 저장소 크기를 줄이는 것은 현재 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다.

### <a name="subscription-management"></a>구독 관리
- 구독 및 리소스 그룹에서 서버를 동적으로 이동하는 것은 현재 지원되지 않습니다.

### <a name="point-in-time-restore-pitr"></a>PITR(특정 시점 복원)
1.  PITR 기능을 사용하면 새 서버가 기반으로 하는 서버와 동일한 구성으로 새 서버가 만들어집니다.
2.  삭제된 서버 복원은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [각 가격 책정 계층에서 사용할 수 있는 기능](concepts-pricing-tiers.md) 이해
- [지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md) 알아보기
- [Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법](howto-restore-server-portal.md) 검토
