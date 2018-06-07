---
title: Azure SQL Database 서비스 - DTU | Microsoft Docs
description: 성능 수준 및 저장소 크기를 제공하기 위한 단일 및 풀 데이터베이스에 대한 서비스 계층에 대해 알아보세요.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 6e282291a6e6e219bb275dd4da91f3815590adc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196006"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Azure SQL Database에 대한 DTU 기반 구매 모델 


[Azure SQL Database](sql-database-technical-overview.md)는 계산, 저장소 및 IO 리소스에 대한 두 가지 구매 모델, 즉 DTU 기반 구매 모델과 vCore 기반 구매 모델(미리 보기)을 제공합니다. 다음 표와 차트에서는 이 두 가지 구매 모델을 비교하고 대조합니다.

> [!IMPORTANT]
> vCore 기반 구매 모델(미리 보기)의 경우 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 참조하세요.


|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반 모델|계산, 저장소 및 IO 리소스를 번들로 묶은 측정값을 기반으로 합니다. 성능 수준은 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-what-is-a-dtu.md)을 참조하세요.|간단하고 미리 구성된 리소스 옵션을 원하는 고객에게 가장 적합합니다.| 
|vCore 기반 모델|이 모델을 사용하면 계산 및 저장소 리소스를 독립적으로 크기 조정할 수 있습니다. SQL Server에 대한 Azure 하이브리드 혜택을 사용하여 비용을 절약할 수 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객에게 가장 적합합니다.|
||||  

![가격 책정 모델](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU(데이터베이스 처리량 단위)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 응용 프로그램 성능을 구동하기 위해 계산 리소스 및 포함된 저장소를 미리 구성된 번들로 묶은 집합을 제공합니다. 매월 미리 구성된 번들과 고정된 지불 금액의 단순성을 선호하는 고객은 DTU 기반 모델이 자신의 요구 사항에 더 적합하다는 것을 알 수 있습니다. DTU 기반 구매 모델에서 고객은 [단일 데이터베이스](sql-database-single-database-resources.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 모두에 대해 **기본**, **표준** 및 **프리미엄** 서비스 계층 중에서 선택할 수 있습니다. 서비스 계층은 포함된 저장소의 고정된 양, 고정된 백업 보존 기간 및 고정 가격을 갖춘 다양한 성능 수준으로 구분됩니다. 모든 서비스 계층은 가동 중지 시간 없이 성능 수준을 변경할 수 있는 유연성을 제공합니다. 단일 데이터베이스 및 탄력적 풀은 서비스 계층 및 성능 수준에 따라 시간 단위로 청구됩니다.

> [!IMPORTANT]
> 현재 공개 미리 보기로 있는 SQL Database 관리되는 인스턴스는 DTU 기반 구매 모델을 지원하지 않습니다. 자세한 내용은 [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요. 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>DTU 기반 구매 모델의 서비스 계층 선택

서비스 계층을 선택하는 작업은 주로 비즈니스 연속성, 저장소 및 성능 요구 사항에 따라 다릅니다.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|대상 워크로드|개발 및 프로덕션|개발 및 프로덕션|개발 및 프로덕션||
|작동 시간 SLA|99.99%|99.99%|99.99%|미리 보기에 있는 동안 해당 없음|
|Backup 보존|7 일|35일|35일|
|CPU|낮음|낮음, 보통, 높음|보통, 높음|
|IO 처리량(근사치) |DTU당 2.5 IOPS| DTU당 2.5 IOPS | DTU당 48 IOPS|
|IO 대기 시간(근사치)|5ms(읽기), 10ms(쓰기)|5ms(읽기), 10ms(쓰기)|2ms(읽기/쓰기)|
|Columnstore 인덱싱 |해당 없음|S3 이상|지원됨|
|메모리 내 OLTP|해당 없음|해당 없음|지원됨|
|||||

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>DTU 기반 구매 모델의 성능 수준 및 저장소 크기 제한

성능 수준은 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-what-is-a-dtu.md)을 참조하세요.

### <a name="single-databases"></a>단일 데이터베이스

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| 최대 저장소 크기* | 2 GB | 1TB | 4 TB  | 
| 최대 DTU | 5 | 3000 | 4000 | |
||||||

단일 데이터베이스에 사용할 수 있는 특정 성능 수준 및 저장소 크기를 선택하는 방법에 대한 자세한 내용은 [단일 데이터베이스에 대한 SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels)을 참조하세요.

### <a name="elastic-pools"></a>탄력적 풀

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| 데이터베이스당 최대 저장소 크기*  | 2 GB | 1TB | 1TB | 
| 풀당 최대 저장소 크기* | 156GB | 4 TB | 4 TB | 
| 데이터베이스당 최대 eDTU | 5 | 3000 | 4000 | 
| 풀당 최대 eDTU | 1600 | 3000 | 4000 | 
| 풀당 최대 데이터베이스 수 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> -  포함된 저장소보다 큰 저장소 크기는 미리 보기로 있으며 추가 비용이 적용됩니다. 자세한 내용은 [SQL Database 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 
> -  프리미엄 계층의 경우 현재 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 미국 중부, 프랑스 중부, 독일 중부, 일본 동부, 일본 서부, 한국 중부, 미국 중북부, 북유럽, 미국 중남부, 동남 아시아, 영국 남부, 영국 서부, 미국 동부 2, 미국 서부, 미국 버지니아 주 정부 및 유럽 서부에서 1TB 이상의 저장소를 사용할 수 있습니다. [P11-P15 현재 제한 사항](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  

탄력적 풀에 사용할 수 있는 특정 성능 수준 및 저장소 크기를 선택하는 방법에 대한 자세한 내용은 [SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)을 참조하세요.



## <a name="next-steps"></a>다음 단계

- 사용할 수 있는 특정 성능 수준 및 저장소 크기를 선택하는 방법에 대한 자세한 내용은 [SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits.md) 및 [SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits.md)을 참조하세요.
- 자주 묻는 질문에 대한 답변은 [SQL Database FAQ](sql-database-faq.md)를 참조하세요.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에 대해 자세히 알아보세요.
