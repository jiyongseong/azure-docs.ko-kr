---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 24d89b617c347bc9443b437c92cb034acb3e05cb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
VM(가상 머신)을 만들거나 중지된(할당이 취소된) VM을 재시작하거나 VM의 크기를 조정하는 경우 Microsoft Azure에서 구독에 계산 리소스를 할당합니다. 당사는 고객의 요구를 지원할 수 있는 모든 VM 유형을 항상 제공할 수 있도록 추가 인프라와 기능에 지속적으로 투자하고 있습니다. 하지만 Azure 서비스에 대한 수요가 특정 지역에서 전례 없이 증가하여 리소스 할당 오류가 때때로 발생할 수 있습니다. VM이 다음 오류 코드 및 메시지를 표시하는 동안 해당 지역에서 VM을 만들거나 시작하려고 하면 이런 문제가 발생할 수 있습니다.

**오류 코드**: AllocationFailed 또는 ZonalAllocationFailed

**오류 메시지**: "할당하지 못했습니다. 이 지역에 요청된 VM 크기에 대해 충분한 용량이 없습니다. http://aka.ms/allocation-guidance에서 할당 성공 가능성을 높이는 방법을 참조하세요."

이 문서는 일부 일반적인 할당 오류의 이유를 설명하고 가능한 해결 방법을 제안합니다.

Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 스택 오버플로에서 Azure 포럼](https://azure.microsoft.com/support/forums/)을 방문합니다. 이러한 포럼이나 Twitter의 @AzureSupport에 문제를 게시할 수 있습니다. 또한 [Azure 지원](https://azure.microsoft.com/support/options/) 사이트에서 지원 받기를 선택하여 Azure 지원 요청을 제출할 수 있습니다.

배포 문제가 있는 고객은 원하는 지역에서 원하는 VM 유형을 사용할 수 있게 될 때까지 임시 해결 방법으로 다음 표의 지침을 고려하는 것이 좋습니다. 

사례에 가장 적합한 시나리오를 확인한 다음, 해당하는 제안된 해결 방법으로 할당 요청을 다시 시도하여 할당 성공 가능성을 높입니다. 또는 나중에 언제든지 다시 시도할 수 있습니다. 요청을 수용하기에 충분한 리소스가 클러스터, 지역 또는 영역에서 해제될 수 있기 때문입니다. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>VM 크기 조정 또는 기존 가용성 집합에 VM 추가

### <a name="cause"></a>원인

VM 크기를 조정하거나 기존 가용성 집합에 VM을 추가하는 요청은 기존 가용성 집합을 호스트하는 원래 클러스터에서 시도되어야 합니다. 요청한 VM 크기가 클러스터에서 지원되지만 현재 클러스터의 용량이 충분하지 않을 수 있습니다. 

### <a name="workaround"></a>해결 방법

VM이 다른 가용성 집합에 속할 수 있는 경우에는 동일한 지역의 다른 가용성 집합에 VM을 만듭니다. 그런 다음 새 VM을 동일한 가상 네트워크에 추가할 수 있습니다.

동일한 가용성 집합의 모든 VM을 중지(할당 취소)하고 각 VM을 재시작합니다.
중지하려면 리소스 그룹 > [사용자의 리소스 그룹] > 리소스 > [사용자의 가용성 집합] > Virtual Machines > [사용자의 가상 머신] > 중지를 차례로 클릭합니다.
모든 VM이 중지된 후 첫 번째 VM을 선택한 다음, 시작을 클릭합니다.
이 단계에서는 새 할당 시도가 실행되고 용량이 충분히 있는 새 클러스터를 선택할 수 있습니다.

## <a name="restart-partially-stopped-deallocated-vms"></a>부분적으로 중지(할당 취소)된 VM 다시 시작

### <a name="cause"></a>원인

일부 할당 취소란 가용성 집합에서 하나 이상을 중지(할당 취소)했지만 모든 VM을 중지한 것은 아니라는 의미입니다. VM 할당을 취소하면 연결된 리소스가 해제됩니다. 부분적으로 할당이 취소된 가용성 집합에서 VM을 다시 시작하는 것은 기존 가용성 집합에 VM을 추가하는 것과 같습니다. 따라서 용량이 충분하지 않을 수 있는 기존 가용성 집합을 호스트하는 원래 클러스터에서 할당 요청을 시도해야 합니다.

### <a name="workaround"></a>해결 방법

동일한 가용성 집합의 모든 VM을 중지(할당 취소)하고 각 VM을 재시작합니다.
중지하려면 리소스 그룹 > [사용자의 리소스 그룹] > 리소스 > [사용자의 가용성 집합] > Virtual Machines > [사용자의 가상 머신] > 중지를 차례로 클릭합니다.
모든 VM이 중지된 후 첫 번째 VM을 선택한 다음, 시작을 클릭합니다.
그러면 새 할당 시도가 실행되고 용량이 충분히 있는 새 클러스터를 선택할 수 있습니다.

## <a name="restart-fully-stopped-deallocated-vms"></a>완전히 중지(할당 취소)된 VM 다시 시작

### <a name="cause"></a>원인

전체 할당 취소란 가용성 집합의 모든 VM을 중지(할당 취소)했다는 의미입니다. VM 재시작을 위한 할당 요청은 해당 지역 또는 영역 내에서 원하는 크기를 지원하는 모든 클러스터를 대상으로 합니다. 이 문서의 제안에 따라 할당 요청을 변경하고 요청을 다시 시도하여 할당 성공 가능성을 높일 수 있습니다. 

### <a name="workaround"></a>해결 방법

Dv1, DSv1, Av1, D15v2, DS15v2와 같은 이전 VM 시리즈 또는 크기를 사용하는 경우 새 버전으로 이동하는 것이 좋습니다. 특정 VM 크기에 대한 권장 사항을 참조하십시오.
다른 VM 크기를 사용할 수 있는 옵션이 없으면 동일한 지역 내의 다른 지역에 배포합니다. 각 지역에서 사용 가능한 VM 크기에 대한 자세한 내용은 https://aka.ms/azure-regions를 참조하세요.

가용성 영역을 사용하는 경우 요청한 VM 크기에 사용 가능한 용량이 있을 수 있는 지역 내의 다른 지역을 시도합니다.

할당 요청이 큰 경우(500개가 넘는 코어) 다음 섹션의 지침을 참조하여 소규모 배포로 요청을 분할합니다.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>이전 VM 크기의 할당 오류(Av1, Dv1, DSv1, D15v2, DS15v2 등)

Azure 인프라가 확장되면서 최신 세대 가상 머신 유형을 지원하도록 설계된 차세대 하드웨어가 배포됩니다. 이전 시리즈 VM 중 일부는 최신 세대 인프라에서 실행되지 않습니다. 이러한 이유 때문에 레거시 SKU에 대해 때때로 할당 오류가 발생할 수 있습니다. 이 문제를 방지하려면 레거시 시리즈 가상 머신을 사용하는 경우 다음 권장 사항에 따라 신규 VM으로 이동을 고려하는 것이 좋습니다. 이러한 VM은 최신 하드웨어에 맞게 최적화되어 보다 나은 가격 및 성능의 이점을 활용할 수 있습니다. 

|레거시 VM 시리즈/크기|권장되는 신규 VM 시리즈/크기|자세한 정보|
|----------------------|----------------------------|--------------------|
|Av1 시리즈|[Av2 시리즈](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 또는 DSv1 시리즈(D1 - D5)|[Dv3 또는 DSv3 시리즈](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 또는 DSv1 시리즈(D11 - D14)|[Ev3 또는 ESv3 시리즈](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)|
|D15v2 또는 DS15v2|대규모 VM 크기 활용하기 위해 Resource Manager 배포 모델을 사용하는 경우 D16v3/DS16v3 또는 D32v3/DS32v3으로 이동하는 것이 좋습니다. 이러한 항목은 최신 세대 하드웨어에서 실행되도록 설계되었습니다. VM 인스턴스가 단일 고객 전용 하드웨어로 격리되도록 Resource Manager 배포 모델을 사용하는 경우, 최신 세대 하드웨어에서 실행되도록 설계된 신규 격리형 VM 크기인 E64i_v3 또는 E64is_v3으로 이동하는 것이 좋습니다. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>대량 배포(코어 500개 초과)에 대한 할당 실패

요청한 VM 크기의 인스턴스 수를 줄인 다음, 배포 작업을 다시 시도합니다. 또한 대량 배포의 경우 [Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)을 평가하는 것이 좋습니다. VM 인스턴스 수가 수요 또는 정의된 일정에 따라 자동으로 증가하거나 감소할 수 있으며 배포가 여러 클러스터에 걸쳐 분산될 수 있어서 할당이 성공할 가능성이 높아집니다. 

## <a name="background-information"></a>배경 정보
### <a name="how-allocation-works"></a>할당의 작동 원리
Azure 데이터 센터의 서버는 클러스터로 분할되어 있습니다. 일반적으로 할당 요청은 여러 클러스터에서 시도되지만 할당 요청의 특정한 제약 조건으로 인해 Azure 플랫폼이 하나의 클러스터에만 요청을 시도하게 될 수 있습니다. 이 문서에서는 이를 “클러스터에 고정”이라고 합니다. 아래 다이어그램 1은 여러 클러스터에 시도되는 정상적인 할당의 사례를 보여 줍니다.  다이어그램 2는 기존 클라우드 서비스 CS_1 또는 가용성 집합이 호스트되는 클러스터 2에 고정된 할당의 사례를 보여 줍니다.
![할당 다이어그램](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>할당 오류가 발생하는 이유
할당 요청이 클러스터에 고정되면 사용 가능한 리소스 풀이 작아지기 때문에 유휴 리소스를 찾는데 실패할 가능성이 높습니다. 할당 요청이 클러스터에 고정되어 있지만 요청하는 리소스 유형이 이 클러스터에서 지원되지 않으면, 클러스터에 유휴 리소스가 있어도 사용자의 요청은 실패합니다. 아래 다이어그램 3은 유일한 후보 클러스터에 유휴 리소스가 없어서 고정된 할당이 실패하는 경우를 보여줍니다. 다이어그램 4는 클러스터에 유휴 리소스가 있지만 유일한 후보 클러스터가 요청한 VM 크기를 지원하지 않기 때문에 고정된 할당이 실패하는 경우를 보여 줍니다.

![고정된 할당 오류](./media/virtual-machines-common-allocation-failure/Allocation2.png)


