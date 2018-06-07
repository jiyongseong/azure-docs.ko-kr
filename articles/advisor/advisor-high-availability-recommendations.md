---
title: "Azure Advisor 고가용성 권장 사항 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포의 가용성을 향상시킵니다."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: e1cd7948e1969cd4ddb926e428c09b559190a805
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-high-availability-recommendations"></a>Advisor 고가용성 권장 사항

Azure Advisor는 업무상 중요한 응용 프로그램의 연속성을 보장하고 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **고가용성** 탭에서 Advisor의 고가용성 권장 사항을 가져올 수 있습니다.

## <a name="ensure-virtual-machine-fault-tolerance"></a>가상 컴퓨터 내결함성 보장

응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 컴퓨터를 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 가용성 집합에 속하지 않는 가상 컴퓨터를 식별하고 이러한 가상 컴퓨터를 가용성 집합으로 이동할 것을 권장합니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 Azure 가상 컴퓨터 SLA가 충족되도록 합니다. 가상 컴퓨터에 대한 가용성 집합을 만들거나 기존 가용성 집합에 가상 컴퓨터를 추가할 수도 있습니다.

> [!NOTE]
> 가용성 집합을 만들도록 선택한 경우 해당 가용성 집합에 적어도 하나 이상의 가상 컴퓨터를 추가해야 합니다. 중단 시간 동안 하나 이상의 컴퓨터를 사용할 수 있도록 하기 위해 가용성 집합에 둘 이상의 가상 컴퓨터를 그룹화하는 것이 좋습니다.

## <a name="ensure-availability-set-fault-tolerance"></a>가용성 집합 내결함성 보장 

응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 컴퓨터를 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 단일 가상 컴퓨터를 포함하는 가용성 집합을 식별하고 여기에 하나 이상의 가상 컴퓨터를 추가할 것을 권장합니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 Azure 가상 컴퓨터 SLA가 충족되도록 합니다. 가상 컴퓨터를 만들거나 기존 가상 컴퓨터를 가용성 집합에 추가할 수 있습니다.  

## <a name="ensure-application-gateway-fault-tolerance"></a>응용 프로그램 게이트웨이 내결함성 보장
응용 프로그램 게이트웨이에서 구동되는 중요 업무용 응용 프로그램의 비즈니스 연속성을 위해 Advisor는 내결함성에 대해 구성되지 않은 응용 프로그램 게이트웨이 인스턴스를 식별하고 수행할 수 있는 수정 작업을 제안합니다. Advisor는 중간 규모 또는 대규모 단일 인스턴스 응용 프로그램 게이트웨이를 식별하고 하나 이상의 인스턴스 추가를 권장합니다. 또한 단일 또는 다중 인스턴스 소규모 응용 프로그램 게이트웨이를 식별하고 중간 규모 또는 대규모 SKU로 마이그레이션을 권장합니다. Advisor는 응용 프로그램 게이트웨이 인스턴스가 이러한 리소스에 대한 현재 SLA 요구 사항을 만족하도록 구성되어 있는지 확인하는 데 이러한 작업을 권장합니다.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>가상 컴퓨터 디스크의 성능 및 안정성 향상

Advisor는 표준 디스크를 포함하는 가상 컴퓨터를 식별하고 프리미엄 디스크로 업그레이드할 것을 권장합니다.
 
Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 컴퓨터에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 프리미엄 저장소 계정을 사용하는 가상 컴퓨터 디스크는 SSD(솔리드 스테이트 드라이브)에 데이터를 저장합니다. 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 가상 컴퓨터 디스크를 프리미엄 저장소로 마이그레이션하는 것이 좋습니다. 

디스크에 높은 IOPS가 필요하지 않으면 표준 저장소에 유지 관리하여 비용을 제한할 수 있습니다. 표준 저장소는 SSD 대신 HDD(하드 디스크 드라이브)에 가상 컴퓨터 디스크 데이터를 저장합니다. 가상 컴퓨터 디스크를 프리미엄 디스크로 마이그레이션하도록 선택할 수 있습니다. 프리미엄 디스크는 대부분의 가상 컴퓨터 SKU에서 지원됩니다. 그러나 일부 경우에 프리미엄 디스크를 사용하려는 경우 가상 컴퓨터 SKU도 업그레이드해야 할 수 있습니다.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>가상 컴퓨터 데이터를 실수로 삭제되지 않도록 보호
가상 컴퓨터 백업 설정은 비즈니스에 중요한 데이터의 가용성을 보장하고 실수로 인한 삭제 또는 손상에 대한 보호를 제공합니다.  Advisor는 백업이 활성화되지 않은 가상 컴퓨터를 식별하고 백업을 활성화할 것을 권장합니다. 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor의 고가용성 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **고가용성** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)

