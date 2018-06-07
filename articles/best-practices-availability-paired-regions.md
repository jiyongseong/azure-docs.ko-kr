---
title: 'BCDR(무중단 업무 방식 및 재해 복구): Azure 쌍을 이루는 지역 | Microsoft Docs'
description: Azure 지역 쌍을 통해 데이터 센터 오류 중 응용 프로그램의 복원성을 유지하는 방법에 대해 알아봅니다.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: multiple
ms.topic: article
ms.date: 05/09/2018
ms.author: raynew
ms.openlocfilehash: e2c288af881fa925c1680efdb0f86deec60b7510
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302681"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>BCDR(무중단 업무 방식 및 재해 복구): Azure 쌍을 이루는 지역

## <a name="what-are-paired-regions"></a>쌍을 이루는 지역이란?

Azure는 전 세계 여러 지역에서 작동합니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함하는 지리적 위치 내의 영역입니다.

각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. 예외는 브라질 남부이며, 지리적 위치 외부에 있는 지역과 쌍을 이루고 있습니다.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

그림 1 - Azure 지역 쌍

| Geography | 쌍을 이루는 지역 |  |
|:--- |:--- |:--- |
| 아시아 |동아시아 |동남아시아 |
| 오스트레일리아 |오스트레일리아 동부 |오스트레일리아 남동부 |
| 오스트레일리아 |오스트레일리아 중부 |오스트레일리아 중부(2) |
| 브라질 |브라질 남부(2) |미국 중남부 |
| 캐나다 |캐나다 중부 |캐나다 동부 |
| 중국 |중국 북부 |중국 동부|
| 유럽 |북유럽 |서유럽 |
| 독일 |독일 중부 |독일 북동부 |
| 인도 |인도 중부 |인도 남부 |
| 인도 |인도 서부(1) |인도 남부 |
| 일본 |일본 동부 |일본 서부 |
| 한국 |한국 중부 |한국 남부 |
| 북아메리카 |미국 동부 |미국 서부 |
| 북아메리카 |미국 동부 2 |미국 중부 |
| 북아메리카 |미국 중북부 |미국 중남부 |
| 북아메리카 |미국 서부 2 |미국 중서부 
| 영국 |영국 서부 |영국 남부 |
| 미국 국방부 |미국 국방부 동부 |미국 국방부 중부 |
| 미국 정부 |미국 정부 애리조나 |미국 정부 텍사스 |
| 미국 정부 |미국 아이오와 주 정부(3) |미국 정부 버지니아 |
| 미국 정부 |미국 버지니아 주 정부(4) |미국 정부 텍사스 |

표 1 - Azure 지역 쌍 매핑

- (1) 인도 서부는 다른 지역과 함께 한 방향으로만 쌍을 이루기 때문에 다릅니다. 인도 서부의 보조 지역은 인도 남부이지만 인도 남부의 보조 지역은 인도 중부입니다.
- (2) 브라질 남부는 해당 지리적 위치 외부에 있는 지역과 쌍을 이루기 때문에 고유합니다. 브라질 남부의 보조 지역은 미국 중남부이지만 미국 중남부의 보조 지역은 브라질 남부가 아닙니다.
- (3) 미국 아이오와 주 정부의 보조 지역은 미국 버지니아 주 정부이지만 미국 버지니아 주 정부의 보조 지역은 미국 아이오와 주 정부가 아닙니다.
- (4) 미국 버지니아 주 정부의 보조 지역은 미국 텍사스 주 정부이지만 미국 텍사스 주 정부의 보조 지역은 미국 버지니아 주 정부가 아닙니다.


Azure의 격리 및 가용성 정책을 활용하려면 지역 쌍 간에 작업을 복제하는 것이 좋습니다. 예를 덜어 계획된 Azure 시스템 업데이트는 쌍을 이루는 지역 간에 순차적으로 배포됩니다. 즉, 흔하지 않은 업데이트 오류가 발생한 경우에도 두 지역이 동시에 영향을 않습니다. 또한 거의 발생할 가능성이 없는 광범위한 중단 시 모든 쌍에서 하나 이상의 지역에 대한 복구 우선 순위가 지정됩니다.

## <a name="an-example-of-paired-regions"></a>쌍을 이루는 지역의 예
아래 그림 2는 재해 복구에 지역 쌍을 사용하는 가상 응용 프로그램을 보여줍니다. 녹색 숫자는 세 개의 Azure 서비스(Azure 계산, 저장소 및 데이터베이스)의 지역 간 활동 및 지역 간에 복제하도록 구성된 방식을 강조합니다. 쌍을 이루는 지역에 걸친 배포의 고유한 이점은 주황색 숫자로 강조 표시되어 있습니다.

![쌍을 이루는 지역의 이점 개요](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

그림 2 – 가상의 Azure 지역 쌍

## <a name="cross-region-activities"></a>지역 간 활동
그림 2 참조

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute(PaaS)** – 재해 중에 다른 지역의 리소스를 사용할 수 있도록 사전에 추가 계산 리소스를 프로비전해야 합니다. 자세한 내용은 [Azure 복원력 기술 지침](resiliency/resiliency-technical-guidance.md)을 참조하세요.

![저장소](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** - GRS(지역 중복 저장소)는 기본적으로 Azure Storage 계정을 만들 때 구성됩니다. GRS를 사용하면 주 지역 및 쌍을 이루는 지역에서 각각 세 번씩 데이터가 자동으로 복제됩니다. 자세한 내용은 [Azure Storage 중복 옵션](storage/common/storage-redundancy.md)을 참조하세요.

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – Azure SQL 표준 지리적 복제를 사용하면 쌍을 이루는 지역으로의 비동기 트랜잭션 복제를 구성할 수 있습니다. Premium 지리적 복제를 사용하면 전 세계 모든 지역으로의 복제를 구성할 수 있습니다. 그러나 대부분의 재해 복구 시나리오에서 이러한 리소스를 쌍을 이루는 지역에 배포하는 것이 좋습니다. 자세한 내용은 [Azure SQL Database의 지역에서 복제](sql-database/sql-database-geo-replication-overview.md)를 참조하세요.

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** Resource Manager는 본질적으로 지역 간 서비스 관리 구성 요소의 논리적 격리를 제공합니다. 따라서 하나의 지역에서 발생한 논리적 오류가 다른 지역에 영향을 줄 가능성이 거의 없습니다.

## <a name="benefits-of-paired-regions"></a>쌍을 이루는 지역의 이점
그림 2 참조  

![격리](./media/best-practices-availability-paired-regions/5Orange.png)
**물리적 격리** – 가능한 경우 Azure는 모든 지리적 위치에서 실용적이거나 가능한 것은 아니지만 지역 쌍 내 데이터 센터 간에 약 483Km 이상 격리하는 것이 좋습니다. 물리적 데이터 센터 격리는 자연 재해, 내전, 정전 또는 물리적 네트워크 중단으로 인해 두 지역 모두가 동시에 영향을 받을 수 있는 가능성을 줄여 줍니다. 격리는 지리적 위치 내의 제약 조건(지리적 크기, 전력/네트워크 인프라 가용성, 규정 등)에 따라 달라집니다.  

![복제](./media/best-practices-availability-paired-regions/6Orange.png)
**플랫폼 제공 복제** - 지역 중복 저장소와 같은 일부 서비스는 쌍을 이루는 지역에 자동 복제를 제공합니다.

![복구](./media/best-practices-availability-paired-regions/7Orange.png)
**지역 복구 순서** – 광범위한 중단 시 한 지역의 복구가 모든 쌍 중에서 우선적으로 수행됩니다. 쌍을 이루는 지역에 배포된 응용 프로그램은 지역 중 하나가 우선 순위에 따라 복구되도록 해줍니다. 쌍을 이루지 않는 지역에 응용 프로그램이 배포된 경우에는 복구가 지연될 수 있으며, 최악의 경우 선택한 지역이 마지막에 복구되는 두 지역이 될 수 있습니다.

![업데이트](./media/best-practices-availability-paired-regions/8Orange.png)
**순차적 업데이트** – 계획된 Azure 시스템 업데이트는 가동 중지 시간, 버그의 영향 및 흔치 않은 불량 업데이트 시의 논리적 오류를 최소화하기 위해 쌍을 이루는 지역으로 순차적으로(동시가 아님) 롤아웃됩니다.

![데이터](./media/best-practices-availability-paired-regions/9Orange.png)
**데이터 상주** – 지역은 세금 및 법률 집행 관할 구역의 데이터 상주 요구 사항을 충족하기 위해 동일한 지리적 위치 내에 쌍으로 상주합니다(브라질 남부 제외).
