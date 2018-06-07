---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e484dac645ff2e5867d2e652c389a9950e8bac12
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "30196443"
---
Azure에서는 가상 머신에 대한 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 주기적으로 업데이트를 수행합니다. 이러한 업데이트는 호스팅 환경의 소프트웨어 구성 요소(운영 체제, 하이퍼바이저 및 호스트에 배포되는 다양한 에이전트) 패치, 네트워킹 구성 요소 업그레이드, 하드웨어 서비스 해제와 같은 범위를 포함합니다. 이러한 업데이트 중 대다수는 호스트된 가상 머신에 영향을 미치지 않고 수행됩니다. 그러나 업데이트가 다음 항목에 영향을 미치는 경우가 있습니다.

- 다시 부팅이 필요 없는 업데이트가 가능한 경우 호스트가 업데이트되거나 VM이 이미 업데이트된 호스트에 완전히 이동하는 동안 Azure에서는 메모리 보존 유지 관리를 사용하여 VM을 일시 중지합니다.

- 유지 관리를 다시 부팅해야 하는 경우 유지 관리가 계획된 시기에 대해 알림을 받을 수 있습니다. 이러한 경우에 사용자가 원하는 시점에 스스로 유지 관리를 시작할 수 있는 기간도 지정됩니다.

이 페이지에서는 Microsoft Azure에서 모든 종류의 유지 관리를 수행하는 방법을 설명합니다. 계획되지 않은 이벤트(중단)에 대한 자세한 내용은 [Windows](... / articles/virtual-machines/windows/manage-availability.md) 또는 [Linux](../articles/virtual-machines/linux/manage-availability.md)에서 가상 머신의 가용성 관리를 참조하세요.

가상 머신에서 실행 중인 응용 프로그램은 [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) 또는 [Linux](... / articles/virtual-machines/linux/instance-metadata-service.md)에서 Azure 메타데이터 서비스를 사용하여 예정된 업데이트에 대한 정보를 수집할 수 있습니다.

계획된 유지 보수의 관리에 대한 "방법" 정보는 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 또는 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)용 "계획된 유지 관리 알림 처리"를 참조하세요.

## <a name="memory-preserving-maintenance"></a>메모리 보존 유지 관리

업데이트에 전체 다시 부팅이 필요하지 않는 경우 메모리 보존 유지 관리 메커니즘을 사용하여 가상 머신에 대한 영향을 제한합니다. 호스팅 환경에서 필요한 업데이트 및 패치를 적용하거나 VM을 이미 업데이트된 호스트에 이동시키는 동안 가상 머신은 최대 30초 동안 일시 중지되어 RAM의 메모리를 보존합니다. 그러면 가상 머신이 다시 시작되고 가상 머신의 시계가 자동으로 동기화됩니다. 

가용성 집합에서 VM의 경우 업데이트 도메인은 한 번에 하나씩 업데이트됩니다. 계획된 유지 관리가 다음 UD로 이동하기 전에 하나의 UD(업데이트 도메인)에 있는 모든 VM이 일시 중지되고 업데이트된 다음 다시 시작됩니다.

일부 응용 프로그램은 이러한 종류의 업데이트에 영향을 받을 수 있습니다. 미디어 스트리밍이나 코드 변환과 같은 실시간 이벤트 처리 또는 처리량이 높은 네트워킹 시나리오를 수행하는 응용 프로그램은 30초 일시 중지를 허용할 수 있도록 설계되지 않았을 수 있습니다. <!-- sooooo, what should they do? --> VM을 다른 호스트로 이동하는 경우 일부 중요한 워크로드는 가상 머신에 일시 중지를 발생시켜서 몇 분 동안 성능이 약간 저하될 수 있습니다. 


## <a name="maintenance-requiring-a-reboot"></a>다시 부팅이 필요한 유지 관리

계획된 유지 관리를 위해 VM을 다시 부팅해야 하는 경우 미리 알림을 받습니다. 계획된 유지 관리에는 셀프 서비스 기간과 예약된 유지 관리 기간이라는 두 단계가 있습니다.

**셀프 서비스 기간**을 통해 VM에 대한 유지 관리를 시작할 수 있습니다. 이 기간 동안 각 VM을 쿼리하여 해당 상태를 보고 최신 유지 관리 요청의 결과를 확인할 수 있습니다.

셀프 서비스 유지 관리를 시작하면 이미 업데이트된 노드로 VM을 이동시키고 전원을 다시 켭니다. VM이 다시 부팅되기 때문에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다.

셀프 서비스 유지 관리를 시작하고 프로세스 중 오류가 발생한 경우 작업이 중지됩니다. 또한 VM이 업데이트 되지 않고 계획된 유지 관리 반복에서도 제거됩니다. 이후에 사용자에게 연락하여 새로운 일정을 알려주고 셀프 서비스 유지 관리 작업을 수행할 수 있는 새로운 기회를 제공합니다. 

셀프 서비스 기간이 지난 경우 **예약된 유지 관리 기간**이 시작됩니다. 이 기간 동안 유지 관리 기간을 계속 쿼리할 수 있지만, 더 이상 스스로 유지 관리를 시작할 수 없습니다.

다시 부팅이 필요한 유지 관리의 관리에 대한 자세한 내용은 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 또는 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)용 "계획된 유지 관리 알림 처리"를 참조하세요. 

## <a name="availability-considerations-during-planned-maintenance"></a>계획된 유지 관리 동안의 가용성 고려 사항 

계획된 유지 관리 기간까지 기다리기로 결정한 경우 VM에서 고가용성을 유지 관리하기 위해 고려해야 할 몇 가지 사항이 있습니다. 

### <a name="paired-regions"></a>쌍을 이루는 지역

각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. 계획된 유지 관리 중에 Azure에서는 지역 쌍의 단일 지역에서만 VM을 업데이트합니다. 예를 들어 미국 중북부에 있는 Virtual Machines를 업데이트할 때 Azure는 미국 중남부의 Virtual Machines를 동시에 업데이트하지 않습니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다. 지역 쌍의 작동 방식을 이해하면 지역에 VM을 더 잘 배포할 수 있습니다. 자세한 내용은 [Azure 지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

### <a name="availability-sets-and-scale-sets"></a>가용성 집합 및 확장 집합

Azure VM에서 워크로드를 배포할 때 응용 프로그램에 고가용성을 제공하기 위해 가용성 집합 내에 VM을 만들 수 있습니다. 이를 통해 가동 중단 또는 유지 관리 이벤트 중에도 하나 이상의 가상 머신을 사용할 수 있습니다.

가용성 집합 내에서 개별 VM은 최대 20개의 UD(업데이트 도메인)에 걸쳐 분산됩니다. 계획된 유지 관리 동안에는 단일 업데이트 도메인만 지정된 시간에 영향을 받습니다. 영향을 받는 업데이트 도메인의 순서가 반드시 순차적으로 나열되지는 않습니다. 

가상 머신 확장 집합은 동일한 VM 집합을 단일 리소스로 배포하고 관리할 수 있게 하는 Azure 계산 리소스입니다. 확장 집합은 가용성 집합의 VM과 같은 업데이트 도메인에 걸쳐 자동으로 배포됩니다. 지정된 시점에서는 가용성 집합과 마찬가지로 확장 집합에서 단일 업데이트 도메인만 영향을 받습니다.

고가용성을 위해 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 [Windows](../articles/virtual-machines/windows/manage-availability.md) 또는 [Linux](../articles/virtual-machines/linux/manage-availability.md) 가상 머신의 가용성 관리를 참조하세요.
