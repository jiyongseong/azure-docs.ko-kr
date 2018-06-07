---
title: "Azure Batch 풀에 대한 VM 크기 선택 | Microsoft Docs"
description: "Azure Batch 풀에서 계산 노드에 사용할 수 있는 VM 크기를 선택하는 방법을 설명합니다."
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch 풀의 계산 노드에 대한 VM 크기 선택

Azure Batch 풀에 대한 노드 크기를 선택하면 Azure에서 사용할 수 있는 거의 모든 VM 크기 중에서 선택할 수 있습니다. Azure는 다양한 작업에 맞게 다양한 크기의 Linux 및 Windows VM을 제공합니다. 

VM 크기를 선택하는 데는 몇 가지 예외 및 제한 사항이 있습니다.
* 일부 VM 제품군 또는 VM 크기는 Batch에서 지원되지 않습니다. 
* 일부 VM 크기는 제한되어 있으므로 할당하기 전에 특별히 사용하도록 설정해야 합니다.


## <a name="supported-vm-families-and-sizes"></a>지원되는 VM 제품군 및 크기

### <a name="pools-in-virtual-machine-configuration"></a>Virtual Machine 구성의 풀

Virtual Machine 구성의 Batch 풀은 다음을 *제외한* 모든 VM 크기([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md))를 지원합니다.

| 패밀리  | 지원되지 않는 크기  |
|---------|---------|
| 기본 A 시리즈 | Basic_A0(A0) |
| A 시리즈 | Standard_A0 |
| B 시리즈 | 모두 |
| Fsv2 시리즈<sup>*</sup> | 모두 |

<sup>*</sup>이 시리즈의 크기는 향후 지원을 위한 로드맵에 나와 있습니다.

### <a name="pools-in-cloud-service-configuration"></a>Cloud Service 구성의 풀

Cloud Service 구성의 Batch 풀은 다음을 *제외한* 모든 [Cloud Services에 적합한 VM 크기](../cloud-services/cloud-services-sizes-specs.md)를 지원합니다.

| 패밀리  | 지원되지 않는 크기  |
|---------|---------|
| A 시리즈 | 매우 작음 |
| Av2 시리즈 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>제한되는 VM 제품군
다음 VM 제품군은 Batch 풀에 할당할 수 있지만 특정 할당량 증가를 요청해야 합니다([이 문서 ](batch-quota-limit.md#increase-a-quota) 참조).
* NCv2 시리즈
* NCv3 시리즈
* ND 시리즈

이러한 크기는 Virtual Machine 구성의 풀에서만 사용할 수 있습니다.

## <a name="size-considerations"></a>크기 고려 사항

* **응용 프로그램 요구 사항** - 노드에서 실행할 응용 프로그램의 특성 및 요구 사항을 고려합니다. 응용 프로그램이 다중 스레드되었는지 여부 및 응용 프로그램에서 소비하는 메모리 양과 같은 측면은 가장 적절하고 비용 효과적인 노드 크기를 결정하는 데 도움을 줄 수 있습니다. 다중 인스턴스 [MPI 작업](batch-mpi.md) 또는 CUDA 응용 프로그램의 경우, 각각 특수화된 [HPC](../virtual-machines/linux/sizes-hpc.md) 또는 [GPU 가능](../virtual-machines/linux/sizes-gpu.md) VM 크기를 고려합니다. [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요. 

* **노드당 태스크 수** - 한 번에 하나의 태스크가 하나의 노드에서 실행된다고 가정하여 노드 크기를 선택하는 것이 일반적입니다. 하지만 작업 실행 중에 계산 노드에서 여러 태스크(따라서 여러 응용 프로그램 인스턴스)를 [병렬로 실행하는](batch-parallel-node-tasks.md) 것이 유리할 수도 있습니다. 이 경우 병렬 태스크 실행에 대해 증가되는 수요를 수용할 수 있도록 멀티 코어 노드 크기를 선택하는 것이 일반적입니다.

* **다른 태스크에 대한 로드 수준** - 풀의 모든 노드는 동일한 크기입니다. 상이한 시스템 요구 사항 및/또는 부하 수준으로 응용 프로그램을 실행하려면 별도의 풀을 사용하는 것이 좋습니다. 

* **지역 가용성** - VM 제품군 또는 크기는 배치 계정을 만든 지역에서 사용하지 못할 수도 있습니다. 크기를 사용할 수 있는지를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

* **할당량** - 배치 계정의 [코어 할당량](batch-quota-limit.md#resource-quotas)은 Batch 풀에 추가할 수 있는 지정된 크기의 노드 수를 제한할 수 있습니다. 할당량 증가를 요청하려면 [이 문서](batch-quota-limit.md#increase-a-quota)를 참조하세요. 

* **풀 구성** - 일반적으로 Cloud Service 구성에 비해 Virtual Machine 구성에서 풀을 만들 때 더 많은 VM 크기 옵션을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.
* 계산 집약적 VM 크기를 사용하는 방법에 대한 자세한 내용은 [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요. 


