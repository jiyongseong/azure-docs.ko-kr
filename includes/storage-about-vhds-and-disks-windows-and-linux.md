---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b8b61f2a512cca2a88274b93d04a1fdc8893a88f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34222916"
---
## <a name="about-vhds"></a>VHD에 대하여

Azure에서 사용되는 VHD는 Azure의 표준 또는 프리미엄 저장소 계정에서 페이지 blob으로 저장되는 .vhd 파일입니다. 페이지 Blob에 대한 자세한 내용은 [블록 Blob 및 페이지 Blob 이해하기](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/)를 참조하세요. 프리미엄 저장소에 대한 자세한 내용은 [고성능 프리미엄 저장소와 Azure VM](../articles/virtual-machines/windows/premium-storage.md)을 참조하세요.

Azure는 고정 디스크 VHD 형식을 지원합니다. 고정 형식은 디스크 오프셋 X가 blob 오프셋 X에 저장되도록 파일 내에 선형적으로 논리적 디스크를 배치합니다. blob 끝의 작은 바닥글에서는 VHD의 속성을 설명합니다. 고정 형식은 대부분의 디스크에서 사용되지 않는 공간 범위가 크기 때문에 공간이 낭비되는 경우가 많습니다. 하지만 Azure에서는 .vhd 파일을 스파스 형식으로 저장하기 때문에 고정 및 동적 디스크의 장점을 동시에 얻을 수 있습니다. 자세한 내용은 [가상 하드 디스크 시작](https://technet.microsoft.com/library/dd979539.aspx)을 참조하세요.

사용자가 Azure 저장소로 업로드하거나 복사한 .vhd 파일을 제외하고, Azure에서 디스크 또는 이미지를 만드는 데 원본으로 사용하려는 모든 VHD 파일은 읽기 전용입니다(읽기-쓰기 또는 읽기 전용일 수 있음). 사용자가 디스크나 이미지를 만들 때, Azure는 원본 .vhd 파일의 복사본을 만듭니다. 이러한 복사본들은 읽기 전용이거나 사용자가 VHD를 사용하는 방법에 따라 읽기 또는 쓰기 모두를 할 수 있습니다.

사용자가 이미지에서 가상 컴퓨터를 만들 때, Azure는 원본 .vhd 파일의 복사본인, 가상 머신에 대한 디스크를 만듭니다. 실수로 삭제되지 않도록 보호하기 위해, Azure는 이미지, 운영 체제 디스크 또는 데이터 디스크를 만드는 데 사용되는 모든 원본 .vhd 파일에 임대를 설정합니다.

원본 .vhd 파일을 삭제하기 전에, 디스크 또는 이미지를 삭제하여 임대를 제거해야 합니다. 운영 체제 디스크로 가상 머신이 사용중인 .vhd 파일을 삭제하려면, 사용자는 가상 머신 및 모든 연결된 디스크를 삭제함으로써 가상 머신, 운영 체제 디스크 및 원본 .vhd 파일을 한번에 삭제할 수 있습니다. 그러나 데이터 디스크의 원본인 .vhd 파일을 삭제하려면 설정된 순서대로 몇 가지 단계를 수행해야 합니다. 먼저 가상 머신에서 디스크를 분리한 다음 디스크를 삭제하고 .vhd 파일을 삭제합니다.

> [!WARNING]
> 저장소에서 원본 .vhd 파일을 삭제하거나, 사용자의 저장소 계정을 삭제한 경우, Microsoft는 해당 데이터를 복구할 수 없습니다.

## <a name="types-of-disks"></a>디스크 유형 

Azure 디스크는 99.999% 가용성을 위해 설계되었습니다. Azure 디스크는 업계 최고의 0% 연간 실패율(Annualized Failure Rate)로 엔터프라이즈급 내구성을 일관되게 제공합니다.

디스크(Standard Storage 및 Premium Storage)를 만들 때 선택할 수 있는 저장소에는 두 가지 성능 계층이 있습니다. 또한 디스크에도 두 가지 유형(비관리 및 관리)이 있으며, 두 성능 계층 중 한 계층에 있을 수 있습니다.


### <a name="standard-storage"></a>Standard Storage 

Standard Storage는 HDD에 의해 지원되며 성능은 그대로이면서 비용 효율적인 저장소를 제공합니다. Standard Storage는 하나의 데이터센터에 로컬로 복제되거나 기본 및 보조 데이터센터와 함께 지역 중복 저장소가 될 수 있습니다. 저장소 복제에 대한 자세한 내용은 [Azure Storage 복제](../articles/storage/common/storage-redundancy.md)를 참조하세요. 

VM 디스크로 Standard Storage를 사용하는 방법에 대한 자세한 내용은 [Standard Storage 및 디스크](../articles/virtual-machines/windows/standard-storage.md)를 참조하세요.

### <a name="premium-storage"></a>Premium Storage 

Premium Storage는 SSD에 의해 지원되며 I/O 사용량이 많은 작업을 실행하는 VM을 위해 고성능의 낮은 대기 시간의 디스크를 지원합니다. 일반적으로 시리즈 이름에 “s”가 포함되는 크기에 Premium Storage를 사용할 수 있습니다. 예를 들어 Dv3 및 Dsv3 시리즈가 있다면 Dsv3 시리즈에서 Premium Storage를 사용할 수 있습니다.  자세한 내용은 [Premium Storage](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요.

### <a name="unmanaged-disks"></a>관리되지 않는 디스크

관리되지 않는 디스크는 VM에서 사용되는 전통적인 유형의 디스크입니다. 이 디스크를 통해 자신만의 저장소 계정을 만들어 디스크를 만들 때 해당 저장소 계정을 지정합니다. 동일한 저장소 계정에 너무 많은 디스크를 배치하지 않도록 해야 합니다. 그 이유는 저장소 계정의 [확장성 목표](../articles/storage/common/storage-scalability-targets.md)(예: 20,000개의 IOPS)를 초과하여, VM이 제한됩니다. 관리되지 않는 디스크를 사용하는 경우 VM에서 최상의 성능을 얻을 수 있도록 하나 이상의 저장소 계정 사용을 극대화하는 방법을 파악해야 합니다.

### <a name="managed-disks"></a>관리 디스크 

Managed Disks는 백그라운드에서 저장소 계정 만들기/관리 작업을 처리하기 때문에 저장소 계정의 확장성 제한에 걱정할 필요가 없습니다. 디스크 크기와 성능 계층(Standard/Premium)을 지정하면, Azure가 디스크를 만들고 관리합니다. 디스크를 추가하거나 VM을 확장/축소하더라도 사용 중인 저장소에 대해 걱정할 필요가 없습니다. 

또한 Azure 지역당 하나의 저장소 계정에서 사용자 지정 이미지를 관리하고 동일한 구독에서 수백 개의 VM을 만드는 데 사용할 수도 있습니다. Managed Disks에 대한 자세한 내용은 [Managed Disks 개요](../articles/virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

새 VM에 Azure Managed Disks를 사용하고 이전의 관리되지 않은 디스크를 관리 디스크로 변환하여 Managed Disks에서 사용할 수 있는 많은 기능을 활용하는 것이 좋습니다.

### <a name="disk-comparison"></a>디스크 비교

다음 표는 용도에 맞게 관리되지 않는 디스크를 사용할지 관리 디스크를 사용할지 결정할 수 있도록 Premium과 Standard를 비교한 내용을 제공합니다.

|    | Azure Premium Disk | Azure Standard Disk |
|--- | ------------------ | ------------------- |
| 디스크 유형 | 반도체 드라이브(SSD) | 하드 디스크 드라이브 (HDD)  |
| 개요  | IO 사용량이 많은 작업을 실행하거나 중요 업무용 프로덕션 환경을 호스팅하는 VM을 위해 SSD 기반의 고성능, 낮은 대기 시간 디스크 지원 | 개발/테스트 VM 시나리오를 위한 HDD 기반의 경제적인 디스크 지원 |
| 시나리오  | 프로덕션 및 성능이 중요한 워크로드 | 개발/테스트, 중요하지 않음, <br>액세스를 자주 하지 않음 |
| 디스크 크기 | P4: 32GB(Managed Disks에만 해당)<br>P6: 64GB(Managed Disks에만 해당)<br>P10: 128GB<br>P20: 512GB<br>P30: 1024GB<br>P40: 2048GB<br>P50: 4095GB | 관리되지 않는 디스크: 1GB-4TB(4095GB) <br><br>관리 디스크:<br> S4: 32GB <br>S6: 64GB <br>S10: 128GB <br>S20: 512GB <br>S30: 1024GB <br>S40: 2048GB<br>S50: 4095GB| 
| 디스크당 최대 처리량 | 250MB/초 | 60MB/s | 
| 디스크당 최대 IOPS | 7500IOPS | 500IOPS | 

