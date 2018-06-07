---
title: 계획된 유지 관리 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 계획된 유지 관리 이벤트를 준비하는 방법을 알아봅니다.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대한 유지 관리 계획 수집

Azure SQL Data Warehouse에 대한 계획된 유지 관리 이벤트를 준비하는 방법을 알아봅니다.

## <a name="what-is-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트란?
계획된 유지 관리 이벤트란 유지 관리 작업을 위해 데이터 웨어하우스를 오프라인 상태로 만들어야 하는 시간 범위를 말합니다. 이러한 이벤트는 서비스 업그레이드, 새 기능 또는 패치를 적용할 수 있는 기회입니다. 

## <a name="frequency"></a>Frequency(빈도)
평균적으로 매달 한 건 이상의 계획된 유지 관리 이벤트가 발생합니다. 

## <a name="notifications-and-downtime"></a>알림 및 가동 중지 시간
각 계획된 유지 관리 이벤트가 발생하기 전에 알림이 제공됩니다. 유지 관리 이벤트는 실행 중인 모든 쿼리를 취소하고 데이터 웨어하우스를 오프라인 상태로 만듭니다. 각 데이터 웨어하우스에 예상되는 가동 중지 시간은 약 30분입니다. 유지 관리가 완료되면 알림을 예상할 수 있습니다. 

## <a name="setting-up-alerts"></a>경고 설정

[Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)를 사용하여 계획된 유지 관리 로그 경고를 설정하는 것이 좋습니다. 경고는 필수 유지 관리를 계획하여 비즈니스에 미치는 영향을 최소화하는 데 도움이 될 수 있습니다. 

알림을 설정하려면 이러한 [로그 경고 지침](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)을 사용하세요. 

## <a name="next-steps"></a>다음 단계
모니터링에 대한 자세한 내용은 [워크로드 모니터링](sql-data-warehouse-manage-monitor.md)을 참조하세요.
