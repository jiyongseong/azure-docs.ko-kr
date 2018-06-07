---
title: Azure SQL Database FAQ | Microsoft Docs
description: 클라우드 데이터베이스 및 Azure SQL Database, Microsoft 관계형 데이터베이스 관리 시스템(RDBMS) 및 클라우드에서 서비스로 데이터베이스에 대해 고객이 궁금해하는 일반적인 질문에 대한 답변입니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 5d77c2dc121d7f291fa755f66d7d9a5d7213bf9f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195433"
---
# <a name="sql-database-faq"></a>SQL Database FAQ

## <a name="what-is-the-current-version-of-sql-database"></a>SQL Database의 최신 버전은 무엇인가요?
SQL Database의 현재 버전은 V12입니다. 버전 V11은 만료되었습니다.

## <a name="what-is-the-sla-for-sql-database"></a>SQL Database에 대한 SLA란 무엇인가요?
99.99% 이상의 시간을 보장하며, 서비스 계층에 관계없이 Microsoft Azure SQL Database와 인터넷 게이트웨이를 연결할 수 있습니다. 자세한 내용은 [SLA](http://azure.microsoft.com/support/legal/sla/)를 참조하세요.

## <a name="whatis-the-new-vcore-based-purchasing-model-preview-for-azure-sql-database"></a>Azure SQL Database에 대한 새 vCore 기반 구매 모델(미리 보기)이란?

새 구매 모델이 기존 DTU 기반 모델에 추가되었습니다. vCore 기반 모델은 고객에게 유연성, 제어, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 변환하는 간단한 방법을 제공하도록 설계되었습니다. 또한 고객이 워크로드 요구 사항에 따라 계산 및 저장소의 크기를 조정할 수 있습니다. vCore 모델을 사용하는 단일 데이터베이스 및 탄력적 풀 옵션도 [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 통해 비용을 최대 30%까지 절약할 수 있습니다. 자세한 내용은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)을 참조하세요. 

## <a name="what-is-a-vcore"></a>vCore란? 
가상 코어는 하드웨어 세대 중에서 선택할 수 있는 옵션과 함께 제공되는 논리 CPU를 나타냅니다. Gen 4 논리 CPU는 Intel E5-2673 v3(Haswell) 2.4GHz 프로세서를 기반으로 하며, Gen 5 논리 CPU는 Intel E5-2673 v4(Broadwell) 2.3GHz 프로세서를 기반으로 합니다.

## <a name="is-moving-to-the-vcore-based-model-required"></a>vCore 기반 모델로 전환해야 하나요?
아니요, 탄력적 풀 및 단일 데이터베이스 배포 옵션에 vCore 기반 모델을 도입한 것은 고객의 선택과 유연성에 대한 약속을 반영합니다. 고객이 DTU 기반 모델을 계속 사용하려면 이 알림과 관련하여 어떤 조치도 취할 필요가 없으며, 해당 환경과 요금 청구는 변경되지 않은 채 유지됩니다. 

대부분의 경우 응용 프로그램은 미리 구성된 리소스 번들을 간단하게 활용할 수 있습니다. 따라서 고객에게 이러한 DTU 기반 선택을 지속적으로 제공하고 지원합니다. 이러한 모델을 사용하고 있고 비즈니스 요구 사항이 충족되는 경우 계속 사용해야 합니다.

DTU 및 vCore 기반 모델은 계속해서 함께 존재할 것입니다. vCore 기반 모델은 데이터베이스 리소스에 대한 투명성 강화와 계산 및 저장소의 크기를 개별적으로 조정하는 기능에 대한 고객의 요청에 부응하여 출시되었습니다. 또한 vCore 기반 모델을 사용하면 활성 Software Assurance를 보유한 고객이 SQL Server에 대한 Azure 하이브리드 혜택을 통해 비용을 추가로 절약할 수도 있습니다.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model-preview"></a>DTU 기반 구매 모델과 vCore 기반 구매 모델(미리 보기) 중 어느 것을 선택해야 할까요? 
DTU(데이터베이스 트랜잭션 단위)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 기반으로 합니다. DTU 기반 성능 수준은 다양한 응용 프로그램 성능 수준을 구동하는 미리 구성된 리소스 번들을 나타냅니다. 기본 리소스에 대해 걱정할 필요 없이 매월 고정 금액을 지불하면서 미리 구성된 번들을 단순히 사용하려는 고객은 DTU 기반 모델이 자신의 요구에 더 적합하다는 것을 알 수 있습니다. 그러나 최적의 성능을 달성하기 위해 기본 리소스에 대한 추가 정보가 필요하거나 크기를 독립적으로 조정해야 하는 고객에게는 vCore 기반 모델이 가장 적합합니다.  또한 고객이 SQL Server에 대한 활성 SA(Software Assurance)를 보유하고 있는 경우, 기존 투자를 활용하고 [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 통해 비용을 최대 30%까지 절약할 수 있습니다.  각 구매 모델의 옵션은 자동 백업, 소프트웨어 업데이트 및 패치와 같이 완전히 관리되는 서비스의 이점을 제공합니다. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택이란? 
[SQL Server에 대한 Azure 하이브리드 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 사용하면 현재 라이선스의 투자 가치를 최대화하고 클라우드로의 마이그레이션을 가속화할 수 있습니다. SQL Server에 대한 Azure 하이브리드 혜택은 Software Assurance가 포함된 SQL Server 라이선스를 사용하여 SQL Database에 대해 할인된 요금("기본 요금")을 지불할 수 있게 하는 Azure 기반 혜택입니다. SQL Server에 대한 Azure 하이브리드 혜택은 SQL Database 단일 데이터베이스 및 탄력적 풀에 대한 vCore 기반 구매 모델(미리 보기)의 공개 미리 보기에서 사용할 수 있습니다. SKU가 활성 중인 경우에도 이 혜택을 적용할 수 있지만, 기본 요금은 Azure Portal에서 해당 요금을 선택한 시점부터 적용됩니다. 크레딧은 소급해서 발급되지 않습니다.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택에 이중 사용 권한이 있나요?
마이그레이션이 원활하게 실행되도록 하려면 180일간의 이중 사용 권한 라이선스가 있어야 합니다. 180일 기간이 지난 후에는 SQL Server 라이선스는 클라우드의 SQL Database에만 사용할 수 있으며, 온-프레미스 및 클라우드에 대한 이중 사용 권한을 갖지 않습니다.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server에 대한 Azure 하이브리드 혜택은 라이선스 이동과 어떻게 다른가요?
현재 SQL Server 고객에게는 타사 공유 서버에 라이선스를 다시 할당할 수 있는 Software Assurance 라이선스 이동 혜택을 제공합니다. 이 혜택은 Azure IaaS 및 AWS EC2에서 사용할 수 있습니다.
SQL Server에 대한 Azure 하이브리드 혜택은 두 가지 주요 영역에서 라이선스 이동과 다릅니다.
- 높은 수준으로 가상화된 워크로드를 Azure로 이동할 수 있는 경제적 이점을 제공합니다. SQL EE 고객은 높은 수준으로 가상화된 응용 프로그램을 위해 온-프레미스에서 소유하는 모든 코어에 대해 Azure의 범용 SKU에서 4개 코어를 얻을 수 있습니다. 라이선스 이동은 가상화된 워크로드를 클라우드로 이동하는 데 특별한 비용상의 혜택을 허용하지 않습니다.
- Azure에서 SQL Server 온-프레미스(SQL Database 관리되는 인스턴스)와 항상 호환되는 PaaS 대상을 제공합니다.

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택의 특정 권한은 무엇인가요?
SQL Server에 대한 Azure 하이브리드 혜택과 관련하여 SQL Database 고객이 보유하는 권한은 다음과 같습니다.

|라이선스 공간|SQL Server에 대한 Azure 하이브리드 혜택의 내용|
|---|---|
|SA가 있는 SQL Server Enterprise Edition 핵심 고객|<li>범용 또는 중요 비즈니스 SKU 중 하나에 대한 기본 요금을 지불할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 4개 코어</li><br><li>온-프레미스의 1개 코어 = 중요 비즈니스 SKU의 1개 코어</li>|
|SA가 있는 SQL Server Standard Edition 핵심 고객|<li>범용 SKU에 대해서만 기본 요금을 지불할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 1개 코어</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>vCore 기반 모델은 SQL Database 관리되는 인스턴스에서 사용할 수 있나요?
[관리되는 인스턴스](sql-database-managed-instance.md)는 vCore 기반 모델에서만 사용할 수 있습니다. 자세한 내용은 [SQL Database 가격 페이지](https://azure.microsoft.com/pricing/details/sql-database/managed/)도 참조하세요. 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>내가 선택한 서비스 계층에 따라 계산 및 저장소 비용이 달라지나요?
계산 비용은 응용 프로그램에 프로비전되는 총 계산 용량을 반영합니다. 중요 비즈니스 서비스 계층에서는 3개 이상의 Always ON 복제본이 자동으로 할당됩니다. 이러한 추가 계산 리소스 할당을 반영하기 위해 중요 비즈니스 계층에서 vCore 가격은 약 2.7배 더 높습니다. 같은 이유로, 중요 비즈니스 계층에서 GB당 저장소 가격이 더 높을수록 SSD 저장소의 높은 IO 및 짧은 대기 시간이 반영됩니다. 이와 동시에 두 경우 모두에서 표준 저장소 클래스를 사용하므로 백업 저장소 비용이 다릅니다.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>선불로 구성하는 항목 또는 데이터베이스에서 사용하는 항목에 따라 저장소 비용을 부과하려면 어떻게 할까요?
다양한 유형의 저장소에 대해 별도의 방식으로 요금이 청구됩니다. 데이터 저장소의 경우 선택한 최대 데이터베이스 또는 풀 크기에 따라 프로비전된 저장소 비용이 청구됩니다. 해당 최댓값을 줄이거나 늘리지 않는 한 비용은 변하지 않습니다. 백업 저장소는 인스턴스의 자동 백업과 관련이 있습니다. 백업 보존 기간을 늘리면 인스턴스에서 사용되는 백업 저장소도 늘어납니다. 프로비전된 총 서버 저장소의 최대 100%까지 백업 저장소에 대한 추가 요금이 청구되지 않습니다. 백업 저장소의 추가 사용량은 GB/월 단위로 요금이 청구됩니다. 예를 들어 데이터베이스 저장소 크기가 100GB인 경우 추가 비용 없이 100GB의 백업을 얻을 수 있습니다. 그러나 백업이 110GB인 경우 추가 10GB에 대한 요금을 지불합니다.

단일 데이터베이스에 대한 백업 저장소의 경우, 데이터베이스 백업에 할당된 저장소에서 데이터베이스 크기를 뺀 값에 대해 비례 배분 방식으로 요금이 청구됩니다. 탄력적 풀에 대한 백업 저장소의 경우, 풀에 있는 모든 데이터베이스의 데이터베이스 백업에 할당된 저장소에서 탄력적 풀의 최대 데이터 크기를 뺀 값에 대해 비례 배분 방식으로 요금이 청구됩니다. 데이터베이스 크기나 탄력적 풀의 증가 또는 트랜잭션 속도의 증가에 따라 더 많은 저장소가 필요하므로 백업 저장소 요금이 증가합니다.  최대 데이터 크기를 늘리면 새로운 이 크기는 청구되는 백업 저장소 크기에서 차감됩니다.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>기존 데이터베이스를 새 서비스 계층으로 변환할 때 SKU를 올바르게 선택하려면 어떻게 할까요? 
DTU 기반 모델을 사용하는 기존 SQL Database 응용 프로그램의 경우, 범용 서비스 계층은 표준 계층과 비슷합니다. 중요 비즈니스 서비스 계층은 프리미엄 계층과 비슷합니다. 두 경우 모두에서 응용 프로그램이 DTU 기반 모델에서 사용하는 각 100DTU당 하나 이상의 vCore를 할당해야 합니다.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>새 vCore 기반 서비스 계층에서 기존의 모든 SLO(서비스 수준 목표)와 호환되는 성능 수준을 제공하나요?
새 vCore 기반 서비스 계층은 100 이상의 DTU를 사용하는 모든 탄력적 풀 및 데이터베이스에 대해 비슷한 성능을 제공합니다.  100 미만의 DTU 워크로드를 수용할 수 있도록 시간이 지남에 따라 더 많은 SLO를 계속 추가할 예정입니다.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>기존 DTU 기반 서비스와 새 vCore 기반 서비스 계층 간에 데이터베이스 기능상의 차이가 있나요? 
새 서비스 계층은 현재 DTU 기반 제품에서 사용할 수 있는 기능의 상위 집합을 지원합니다. 추가 기능에는 일단의 추가 DMV(동적 관리 뷰) 및 추가 리소스 구성 옵션이 포함됩니다. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>내 데이터베이스가 CPU에 바인딩되고 많은 저장소를 사용하지 않는 경우 추가 저장소 비용을 지불하지 않고 계산을 늘릴 수 있나요?
예, 응용 프로그램에 필요한 계산 수준을 독립적으로 선택하고 저장소를 변경하지 않은 상태를 유지할 수 있습니다. 저장소는 32GB까지 낮게 설정할 수 있습니다. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>새 vCore 기반 계층에서는 현재의 35일 기간에 대한 PITR(특정 시점 복원)을 지원하나요? 
PITR에 대한 백업 보존 기간은 7-35일로 구성할 수 있습니다. 백업 저장소는 최대 데이터 크기와 동일한 저장소 용량을 초과하는 경우 실제 저장소 사용량에 따라 별도로 요금이 청구됩니다. 미리 보기에서 PITR 보존 기간은 기본적으로 7일로 설정됩니다. 대부분의 경우 최대 데이터 크기는 7일간의 백업 저장에 충분합니다.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>계산에 대해 하드웨어 세대를 선택하도록 허용하는 이유는 무엇인가요?
응용 프로그램의 요구 사항과 거의 일치하는 성능 구성을 선택할 수 있도록 최대한의 유연성을 제공할 필요가 있기 때문입니다. 위의 표에서는 4세대와 5세대의 차이점을 보여 줍니다. 특히 4세대 하드웨어는 실질적으로 vCore당 더 많은 메모리를 제공합니다. 그러나 5세대 하드웨어를 사용하면 계산을 훨씬 더 강화할 수 있습니다. 이러한 차이를 투명하게 하여 응용 프로그램에 가장 적합한 가격 대비 성능 비율을 달성할 수 있게 하려고 합니다.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>DTU 기반 데이터베이스에서 vCore 기반 서비스 계층으로 변환하려면 응용 프로그램을 오프라인으로 전환해야 하나요? 
새 서비스 계층에서는 데이터베이스를 표준 서비스 계층에서 프리미엄 서비스 계층으로 업그레이드하거나 그 반대로 업그레이드하는 기존 프로세스와 비슷한 간단한 온라인 변환 방법을 제공합니다. 이 변환은 포털, ARM, PowerShell, Azure CLI 또는 T-SQL을 사용하여 시작할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-resources.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>데이터베이스를 vCore 기반 서비스 계층에서 DTU 기반 서비스 계층으로 변환할 수 있나요? 
예, 포털을 사용하거나 프로그래밍 방식으로 포털, ARM, PowerShell, Azure CLI 또는 T-SQL을 사용하여 데이터베이스를 지원하는 성능 목표로 쉽게 변환할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-resources.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>범용 및 중요 비즈니스 서비스 계층 간에 업그레이드하거나 다운그레이드할 수 있나요? 
예, 하지만 몇 가지 제한 사항이 있습니다. 대상 SKU는 기존 배포에 대해 구성한 최대 데이터베이스 또는 탄력적 풀 크기를 충족해야 합니다. [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 사용하는 경우 중요 비즈니스 SKU는 Enterprise Edition 라이선스가 있는 고객만 사용할 수 있습니다. Enterprise Edition 라이선스가 있는 SQL Server에 대한 Azure 하이브리드 혜택을 사용하여 온-프레미스에서 범용 계층으로 마이그레이션한 고객만 중요 비즈니스 계층으로 업그레이드할 수 있습니다. 자세한 내용은 [SQL Server에 대한 Azure 하이브리드 사용 혜택에 대한 특정 권한은 무엇입니까?](../virtual-machines/windows/hybrid-use-benefit-licensing.md)를 참조하세요.

이 변환은 가동 중지 시간을 초래하지 않으며, 포털, ARM, PowerShell, Azure CLI 또는 T-SQL을 사용하여 시작할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-resources.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>일반 공급되지 않는 프리미엄 RS 데이터베이스를 사용하고 있습니다. 새 계층으로 업그레이드하고 비슷한 가격 대비 성능 혜택을 얻을 수 있나요?
vCore 모델은 프로비전된 계산 및 저장소의 용량을 독립적으로 제어할 수 있으므로, 결과 비용을 더 효과적으로 관리하여 프리미엄 RS 데이터베이스에서 매력적인 대상이 됩니다. 또한 [SQL Server에 대한 Azure 하이브리드 사용 혜택](../virtual-machines/windows/hybrid-use-benefit-licensing.md)은 vCore 기반 모델을 사용할 때 상당한 할인 혜택을 제공합니다. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>풀당 리소스는 얼마나 자주 조정할 수 있나요?
원하는 횟수만큼 조정할 수 있습니다. [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>단일 데이터베이스의 서비스 계층 또는 성능 수준을 변경하거나 탄력적 풀 내부 및 외부로 데이터베이스를 이동하는 데는 얼마나 시간이 소요되나요?
데이터베이스의 서비스 계층을 변경하고 풀 내부 및 외부로 이동하려면 플랫폼에서 백그라운드 작업으로 데이터베이스를 복사해야 합니다. 서비스 계층을 변경하는 데는 데이터베이스 크기에 따라 몇 분에서 몇 시간까지 소요될 수 있습니다. 두 경우 모두 데이터베이스는 온라인 상태이고 이동 중에도 사용할 수 있습니다. 단일 데이터베이스 변경에 대한 자세한 내용은 [데이터베이스의 서비스 계층 변경](sql-database-service-tiers-dtu.md)을 참조하세요. 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>탄력적 데이터베이스와 단일 데이터베이스는 언제 사용해야 하나요?
일반적으로 탄력적 풀은 고객 또는 테넌트당 하나의 데이터베이스가 있는 전형적인 [SaaS(Software-as-a-Service) 응용 프로그램 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 위해 설계되었습니다. 개별 데이터베이스를 만들고 각 데이터베이스의 가변적인 최대 수요를 충족하기 위해 과도하게 프로비저닝하는 것은 대체로 비용 효율적이지 않습니다. 풀을 사용하여 사용자는 풀의 집단 성능을 관리하고 데이터베이스는 자동으로 확장 및 축소됩니다. Azure의 지능형 엔진이 사용 패턴이 타당하다고 판단되는 경우 데이터베이스에 대한 풀을 추천합니다. 자세한 내용은 [탄력적 풀 지침](sql-database-elastic-pool.md)을 참조하세요.

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>DTU 기반 구매 모델을 사용하는 SQL Database 사용량은 청구서에서 어떻게 표시되나요?
SQL Database는 [구매 모델](sql-database-service-tiers-dtu.md)에 따라 예측 가능한 시간당 요금으로 청구됩니다. 실제 사용량은 시간별로 계산 및 비례 배분되므로 청구서에 시간의 일부가 표시될 수 있습니다. 예를 들어 데이터베이스가 한 달에 12시간 동안 있었다면 청구서에는 0.5일의 사용량이 표시됩니다. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>단일 데이터베이스가 1시간 미만 동안 활성화되거나 더 높은 서비스 계층을 1시간 미만 사용하는 경우 어떻게 되나요?
사용량이나 데이터베이스가 한 시간 미만 동안 활성화되었는지 여부에 관계없이, 그 시간에 적용된 최고 서비스 계층 + 성능 수준을 사용하여 데이터베이스가 있었던 각 시간에 대해 요금이 청구됩니다. 예를 들어 단일 데이터베이스를 만들고 5분 후 삭제하더라도 청구서에는 데이터베이스 1시간 사용에 대한 요금이 반영됩니다. 

예제:

* Basic 데이터베이스를 만든 후 즉시 Standard S1로 업그레이드한 경우에는 처음 1시간 사용에 대해 Standard S1 요금이 청구됩니다.
* 오후 10시에 Basic에서 Premium으로 데이터베이스를 업그레이드하고 다음 날 오전 1시 35분에 업그레이드가 완료되면 오전 1시 시작 시간부터 Premium 요금이 부과됩니다. 
* 오전 11시에 Premium에서 Basic으로 데이터베이스를 다운그레이드하여 오후 2시 15분에 완료되면 데이터베이스에는 Basic 요금이 부과된 후 오후 3시까지 Premium 요금이 부과됩니다.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>DTU 기반 구매 모델을 사용하는 탄력적 풀 사용량은 청구서에서 어떻게 표시되나요?
청구서에서 탄력적 풀 요금은 eDTU(탄력적 DTU) 또는 vCore 수 + 저장소가 [가격 페이지](https://azure.microsoft.com/pricing/details/sql-database/)에 표시된 증분 단위로 표시됩니다. 탄력적 풀에 대한 데이터베이스별 요금은 없습니다. 사용량에 관계없이 또는 풀이 한 시간 미만 동안 활성 상태였는지 여부에 관계없이 풀이 최고 eDTU 또는 vCore 수에 있었던 시간에 대해 시간당 요금이 청구됩니다. 

DTU 기반 구매 모델 예:

* 오전 11시 18분에 5개의 데이터베이스를 풀에 추가하면서 200 eDTU로 Standard 탄력적 풀을 만든 경우 오전 11시부터 해당 일의 남은 시간 동안 200 eDTU에 대한 요금이 부과됩니다.
* 2일차의 오전 5시 5분에 데이터베이스 1은 50 eDTU 사용을 시작하고 그 날 동안 계속 유지합니다. 데이터베이스 2-5는 0과 80 eDTU 사이를 이동합니다. 이 날에는 하루 종일 다양한 eDTU를 사용하는 5개의 다른 데이터베이스를 추가합니다. 2일차는 200 eDTU에서 전일 요금이 청구됩니다. 
* 3일차 오전 5시에 다른 15개의 데이터베이스를 추가합니다. 데이터베이스 사용량이 종일 증가하여 오후 8시 5분에는 풀에 대한 eDTU를 200에서 400으로 증가하도록 결정하기에 이릅니다. 오후 8시까지 200 eDTU 수준으로 요금이 적용되고 남은 4시간에 대해서는 400 eDTU로 요금이 증가합니다. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>DTU 기반 구매 모델에 대한 탄력적 풀은 어떻게 청구되나요?
탄력적 풀은 다음 특성에 따라 대금이 청구됩니다.

* 탄력적 풀은 풀에 데이터베이스가 없더라도 생성 시부터 대금이 청구됩니다.
* 탄력적 풀은 시간당 대금이 청구됩니다. 이는 단일 데이터베이스의 성능 수준과 같은 계량 빈도입니다.
* 탄력적 풀의 크기를 조정하면 크기 조정 작업이 완료될 때까지 새 리소스 양에 따른 풀에 대한 요금은 청구되지 않습니다. 이는 단일 데이터베이스의 성능 수준을 변경하는 것과 동일한 패턴을 따릅니다.
* 탄력적 풀의 가격은 풀의 리소스를 기반으로 합니다. 탄력적 풀의 가격은 그 안의 탄력적 데이터베이스 사용률 및 수와 무관합니다.

자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/), [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)을 참조하세요.

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>내 청구서에서 vCore 기반 사용량은 어떻게 표시되나요? 
vCore 기반 모델에서 서비스 요금은 서비스 계층, 프로비전된 계산(vCore 수), 프로비전된 저장소(GB/월 단위) 및 사용된 백업 저장소에 따라 예측 가능한 시간당 요금으로 청구됩니다. 백업용 저장소가 전체 데이터베이스 크기(즉, 데이터베이스 크기의 100%)를 초과하는 경우 추가 요금이 청구됩니다. vCore 시간, 구성된 데이터베이스 저장소, 사용된 IO 및 백업 저장소는 청구서에서 항목별로 명확하게 구분되어 사용한 리소스의 세부 정보를 더 쉽게 볼 수 있습니다. 최대 데이터베이스 크기의 100%까지 백업 저장소가 포함되고, 매월 사용된 초과 용량에 대한 요금이 GB/월 단위로 청구됩니다.

예: 
- SQL 데이터베이스가 한 달에 12시간 동안 존재하는 경우 청구서에는 vCore에 대해 12시간 사용량이 표시됩니다. SQL 데이터베이스에서 100GB의 저장소를 추가로 프로비전한 경우, 청구서에는 저장소 사용량이 시간당 비례 배분한 GB/월 및 매월 사용된 IO 수의 단위로 표시됩니다.
- SQL 데이터베이스가 1시간 미만 동안 활성 상태인 경우, 사용량에 관계없이 또는 데이터베이스가 1시간 미만 동안 활성 상태였는지 여부에 관계없이 선택한 가장 높은 서비스 계층, 프로비전된 저장소 및 해당 시간 동안 적용된 IO를 사용하여 존재한 데이터베이스에 대한 시간당 요금이 청구됩니다.
- 관리되는 인스턴스를 만들고 5분 후에 삭제한 경우 데이터베이스 시간당 요금이 청구됩니다.
- 8개 vCore를 사용하여 범용 계층에서 관리되는 인스턴스를 만든 다음, 즉시 16개 vCore로 업그레이드하는 경우 처음 1시간 사용에 대해 16개 vCore 요금이 청구됩니다.

> [!NOTE]
> 2018년 6월 30일까지로 제한된 기간 동안 백업 요금 및 IO 요금은 무료입니다.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>탄력적 풀에서 활성 지역 복제의 사용량은 청구서에 어떻게 표시되나요?
단일 데이터베이스와 달리, 탄력적 데이터베이스의 [활성 지역 복제](sql-database-geo-replication-overview.md)를 사용해도 청구서에 직접적인 영향은 없습니다.  각 풀(주 풀 및 보조 풀)에 프로비전된 리소스에 대해서만 요금이 청구됩니다.

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>감사 기능 사용이 청구서 요금에 반영되는 방식
감사는 추가 비용 없이 SQL Database 서비스에 기본 제공되며, 모든 서비스 계층에서 사용할 수 있습니다. 하지만 감사 로그를 저장할 때 감사 기능은 Azure Storage 계정을 사용하며 감사 로그의 크기에 따라 Azure Storage의 테이블 및 큐에 대한 요금이 부과됩니다.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>서버 관리자에 대한 암호를 재설정하려면 어떻게 하나요?
[Azure Portal](https://portal.azure.com)에서 **SQL Server**를 클릭하고, 목록에서 서버를 선택한 다음, **암호 재설정**을 클릭합니다.

## <a name="how-do-i-manage-databases-and-logins"></a>데이터베이스 및 로그인을 관리하려면 어떻게 해야 하나요?
[데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>권한이 부여된 IP 주소만 서버에 액세스할 수 있도록 허용하려면 어떻게 해야 하나요?
[방법: SQL Database에서 방화벽 설정 구성](sql-database-configure-firewall-settings.md)을 참조하세요.

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>동일한 Azure 지리 내의 두 지역 간에 데이터베이스를 지역 복제하는 경우 예상되는 복제 지연은 무엇인가요?
현재 5초의 RPO를 지원하고 있으며 복제 지연은 지역 보조 데이터베이스가 Azure에서 권장하는 쌍을 이루는 지역에 호스트되고 동일한 서비스 계층인 경우보다 짧았습니다.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>지역 보조 데이터베이스가 주 데이터베이스와 동일한 지역에 만들어지는 경우 예상되는 복제 지연은 무엇인가요?
경험적 데이터를 보면 Azure에서 권장하는 쌍을 이루는 지역을 사용하는 경우 지역 내 및 지역 간 복제 지연 간에 그리 많은 차이는 없습니다. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>두 지역 간에 네트워크 오류가 있는 경우 지역에서 복제가 설정되었을 때 재시도 논리가 어떻게 작동하나요?
연결이 끊어지면 연결을 다시 설정하도록 10초마다 재시도합니다.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>주 데이터베이스의 중요한 변경 내용이 복제되도록 보장하려면 어떻게 해야 하나요?
지역 보조 데이터베이스는 비동기 복제본이며 주 데이터베이스와 전체 동기화를 유지하려고 하지 않습니다. 하지만 중요한 변경 내용(예: 암호 업데이트)이 복제되도록 하기 위해 강제 동기화 방법을 제공하고 있습니다. 강제 동기화는 모든 커밋된 트랜잭션이 복제될 때까지 호출 스레드를 차단하므로 성능에 영향을 주게 됩니다. 자세한 내용은 [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx)를 참조하세요. 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>주 데이터베이스와 지역 보조 데이터베이스 간의 복제 지연을 모니터링할 수 있는 도구는 무엇인가요?
DMV를 통해 주 데이터베이스와 지역 보조 데이터베이스 간의 실시간 복제 지연을 표시합니다. 자세한 내용은 [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx)를 참조하세요.

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>동일한 구독의 다른 서버에 데이터베이스를 이동하려면
[Azure Portal](https://portal.azure.com)에서 **SQL Database**를 클릭하고 목록에서 데이터베이스를 선택한 후 **복사**를 클릭합니다. 자세한 내용은 [Azure SQL Database 복사](sql-database-copy.md) 를 참조하세요.

## <a name="to-move-a-database-between-subscriptions"></a>구독 간에 데이터베이스를 이동하려면
[Azure Portal](https://portal.azure.com)에서 **SQL Server** 를 클릭하고, 목록에서 데이터베이스를 호스팅하는 서버를 선택합니다. **이동**을 클릭한 후 이동할 리소스와 이동 대상인 구독을 선택합니다.
