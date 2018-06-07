---
title: 모니터링 및 성능 튜닝 - Azure SQL Database | Microsoft Docs
description: 평가 및 개선을 통한 Azure SQL Database의 성능 튜닝 관련 팁.
services: sql-database
author: v-shysun
manager: craigg
editor: ''
keywords: sql 성능 튜닝, 데이터베이스 성능 튜닝, sql 성능 튜닝 팁, SQL Database 성능 튜닝
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: v-shysun
ms.openlocfilehash: 710d517621cb9d4d9d2e9bf29e4facf7d75ab481
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187835"
---
# <a name="monitoring-and-performance-tuning"></a>모니터링 및 성능 튜닝

Azure SQL Database는 사용량을 쉽게 모니터링하고, 리소스(CPU, 메모리, IO)를 추가 또는 제거하며, 데이터베이스 성능을 향상시킬 수 있는 권장 사항을 찾거나, 데이터베이스가 사용자 워크로드에 맞게 조정되고 성능을 자동으로 최적화할 수 있도록 해주는 자동으로 관리되는 유연한 데이터 서비스입니다.

이 문서에서는 Azure SQL Database에서 제공되는 모니터링 및 성능 튜닝 옵션에 대한 개요를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>모니터링 및 데이터베이스 성능 문제 해결

Azure SQL Database를 사용하면 데이터베이스 사용량을 쉽게 모니터링하고 성능 문제를 일으킬 수 있는 쿼리를 식별할 수 있습니다. Azure Portal 또는 시스템 뷰를 사용gkdu 데이터베이스 성능을 모니터링할 수 있습니다. 데이터베이스 성능 문제 해결 및 모니터링에 대한 다음과 같은 옵션이 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **SQL Database**를 클릭하고 데이터베이스를 선택한 후 모니터링 차트를 사용하여 최대값에 근접한 리소스를 찾습니다. 기본적으로 DTU 사용량이 표시됩니다. **편집** 을 클릭하여 표시된 시간 범위 및 값을 변경합니다.
2. [Query Performance Insight](sql-database-query-performance.md)를 사용하여 가장 많은 리소스를 소비하는 쿼리를 파악합니다.
3. DMV(동적 관리 뷰), 확장 이벤트(`XEvents`) 및 SSMS의 쿼리 저장소를 사용하여 실시간으로 성능 매개 변수를 가져올 수 있습니다.

이러한 보고서 또는 뷰를 사용하여 몇 가지 문제를 파악하는 경우 Azure SQL Database의 성능을 향상시키는 데 사용할 수 있는 기술을 찾으려면 [성능 지침 항목](sql-database-performance-guidance.md)을 참조하세요.

> [!IMPORTANT] 
> Microsoft Azure 및 SQL Database에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>데이터베이스 최적화로 성능 개선

Azure SQL Database를 사용하면 [성능 튜닝 권장 사항](sql-database-advisor.md)을 검토하여 리소스를 변경하지 않고도 쿼리 성능을 개선 및 최적화하는 기회를 찾을 수 있습니다. 인덱스 누락 및 최적화되지 않은 쿼리는 데이터베이스 성능을 저하시키는 일반적인 원인입니다. 이러한 튜닝 권장 사항을 적용하여 워크로드의 성능을 개선할 수 있습니다.
또한 Azure SQL Database를 통해 식별된 모든 권장 사항을 적용하고 이를 통해 데이터베이스 성능이 개선되는지 확인하여 [쿼리의 성능을 자동으로 최적화](sql-database-automatic-tuning.md)할 수 있습니다. 데이터베이스의 성능 향상을 위해 다음 옵션을 사용할 수 있습니다.

1. [SQL Database Advisor](sql-database-advisor-portal.md)를 사용하여 인덱스 만들기 및 삭제, 쿼리 매개 변수화, 스키마 문제 해결에 대한 권장 사항을 확인합니다.
2. [자동 튜닝을 사용](sql-database-automatic-tuning-enable.md)하고 Azure SQL Database에서 식별된 성능 문제를 자동으로 수정하도록 합니다.

## <a name="improving-database-performance-with-more-resources"></a>보다 많은 리소스와 함께 데이터베이스 성능 개선

마지막으로, 데이터베이스의 성능을 향상시킬 수 있는 실행 가능한 항목이 더 없으면 Azure SQL Database에서 사용할 수 있는 리소스의 양을 변경할 수 있습니다. 독립 실행형 데이터베이스의 [DTU 서비스 계층](sql-database-service-tiers-dtu.md)을 변경하여 더 많은 리소스를 할당하거나 탄력적 풀의 eDTU를 언제든지 늘릴 수 있습니다. 또는 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)을 사용하는 경우 서비스 계층을 변경하거나 데이터베이스에 할당된 리소스를 늘릴 수 있습니다. 
1. 독립 실행형 데이터베이스의 경우 필요에 따라 [서비스 계층을 변경](sql-database-service-tiers-dtu.md)하거나 [리소스를 계산](sql-database-service-tiers-vcore.md)하여 데이터베이스 성능을 개선할 수 있습니다.
2. 여러 데이터베이스의 경우 [탄력적 풀](sql-database-elastic-pool-guidance.md)을 사용하여 자동으로 리소스 규모를 조정할 수 있습니다.

## <a name="tune-and-refactor-application-or-database-code"></a>응용 프로그램 또는 데이터베이스 코드 조정 및 리팩터링

데이터베이스를 보다 최적으로 사용하도록 응용 프로그램 코드를 변경하거나 인덱스를 변경하며 계획을 강제 적용하거나 힌트를 사용하여 사용자 워크로드에 맞게 데이터베이스를 수동으로 조정할 수 있습니다. 수동 튜닝 및 코드 다시 작성에 대한 몇 가지 지침 및 팁은 [성능 지침 항목](sql-database-performance-guidance.md) 문서에서 확인합니다.


## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 자동 조정을 사용하도록 설정하고 자동 조정 기능으로 작업을 완벽하게 관리하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.
- 수동 튜닝을 사용하려면 [Azure Portal에서 튜닝 권장 사항](sql-database-advisor-portal.md)을 검토하고 쿼리의 성능을 개선하는 권장 사항을 직접 적용합니다.
- [Azure SQL Database 서비스 계층](sql-database-performance-guidance.md)을 변경하여 데이터베이스에 제공되는 리소스를 변경합니다.
