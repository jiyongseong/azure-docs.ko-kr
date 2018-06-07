---
title: Azure Advisor 성능 권장 사항 | Microsoft Docs
description: Advisor를 사용하여 Azure 배포 성능을 최적화합니다.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 00abb5aafc6f3aec2e2dd7326a307bee74d97cc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="advisor-performance-recommendations"></a>Advisor 성능 권장 사항

Azure Advisor 성능 권장 사항은 업무에 중요한 응용 프로그램의 속도 및 응답성을 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **성능** 탭에서 Advisor를 사용하여 성능 권장 사항을 볼 수 있습니다.

## <a name="improve-database-performance-with-sql-db-advisor"></a>SQL DB Advisor로 데이터베이스 성능 개선

Advisor는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다. SQL Database Azure와 통합되므로 SQL Azure Database의 성능을 향상시키기 위한 권장 사항이 표시됩니다. SQL Database Advisor는 사용 기록을 분석하여 SQL Azure Database의 성능을 평가합니다. 그런 후 데이터베이스의 일반적인 워크로드를 실행하는 데 가장 적합한 권장 사항을 제공합니다. 

> [!NOTE]
> 권장 사항을 가져오려면 데이터베이스에 일주일의 사용 기간이 필요하고, 그 기간 내에 일관된 활동이 필요합니다. SQL Database Advisor는 일관성 있는 쿼리 패턴을 임의 활동 버스트보다 더욱 쉽게 최적화할 수 있습니다.

SQL Database Advisor에 대한 자세한 내용은 [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)를 참조하세요.

## <a name="improve-redis-cache-performance-and-reliability"></a>Redis Cache 성능 및 안정성 향상

Advisor는 높은 메모리 사용량, 서버 부하, 네트워크 대역폭 또는 많은 수의 클라이언트 연결이 성능이 부정적인 영향을 미칠 수 있는 Redis Cache 인스턴스를 식별합니다. 또한 Advisor는 잠재적인 문제를 방지하는 데 도움이 되는 모범 사례 권장 사항도 제공합니다. Redis Cache 권장 사항에 대한 자세한 내용은 [r](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor)를 참조하세요.


## <a name="improve-app-service-performance-and-reliability"></a>App Service 성능 및 안정성 향상

Azure Advisor는 App Services 환경을 개선하고 관련 플랫폼 기능을 검색하기 위한 모범 사례 권장 사항을 통합합니다. App Services 권장 사항 예제:
* 완화 옵션을 사용하여 메모리나 CPU 리소스가 앱 런타임에서 소모되는 인스턴스 검색
* Web Apps 및 데이터베이스를 함께 배치할 때 성능을 향상시키고 비용을 절감할 수 있는 인스턴스 검색 

App Services 권장 사항에 대한 자세한 내용은 [Azure App Service에 대한 모범 사례](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)를 참조하세요.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor에서 성능 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **성능** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)

