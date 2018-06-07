---
title: Azure Cloud Services를 위한 가상 머신 크기 | Microsoft Docs
description: Azure 클라우드 서비스 웹 및 작업자 역할에 대한 다양한 가상 머신 크기 및 ID를 나열합니다.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: bde4347c487be8986c72435172b18c10effa0690
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33940287"
---
# <a name="sizes-for-cloud-services"></a>Cloud Services에 적합한 크기
이 항목에서는 클라우드 서비스 역할 인스턴스(웹 역할 및 작업자 역할)에서 사용 가능한 크기 및 옵션을 설명합니다. 또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다. 각 크기에 따라 [서비스 정의 파일](cloud-services-model-and-package.md#csdef)에 입력할 ID가 있습니다. 각 크기의 가격은 [Cloud Services 가격](https://azure.microsoft.com/pricing/details/cloud-services/) 페이지에서 확인할 수 있습니다.

> [!NOTE]
> 관련 Azure 제한 사항에 대한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>웹 및 작업자 역할 인스턴스에 적합한 크기
Azure에서 여러 표준 크기를 선택할 수 있습니다. 이러한 크기 중 일부에 대한 고려 사항은 다음을 포함합니다.

* D 시리즈 VM은 높은 계산 능력과 임시 디스크 성능이 필요한 응용 프로그램을 실행하도록 설계되었습니다. D 시리즈 VM은 임시 디스크를 위해 빠른 프로세서, 더 높은 메모리-코어 비율 및 SSD(반도체 드라이브)를 제공합니다. 자세한 내용은 Azure 블로그, [새로운 D 시리즈 Virtual Machine 크기](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)의 발표를 참조하세요.
* 원래 D 시리즈의 후속판인 Dv3 시리즈, Dv2 시리즈는 더 강력한 CPU가 특징입니다. Dv2 시리즈 CPU는 D 시리즈 CPU보다 약 35% 빠릅니다. 최근 출시된 2.4GHz Intel Xeon® E5-2673 v3(Haswell) 프로세서를 기반으로 하고 Intel Turbo Boost Technology 2.0을 사용하여 최대 3.1GHz까지 올라갈 수 있습니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.
* G 시리즈 VM은 많은 메모리를 제공하고 Intel Xeon E5 V3 제품군 프로세서가 설치된 호스트에서 실행합니다.
* 다양한 하드웨어 유형 및 프로세서에 A 시리즈 VM을 배포할 수 있습니다. 배포된 하드웨어에 관계없이 인스턴스 실행 시 일관된 프로세서 성능을 제공하기 위해 하드웨어에 따라 크기가 제한됩니다. 이 크기가 배포되는 실제 하드웨어를 확인하려면 Virtual Machine 내에서 가상 하드웨어를 쿼리합니다.
* A0 크기는 실제 하드웨어에서 과도하게 구독됩니다. 이 특정 크기만 다른 고객 배포가 실행 중인 워크로드의 성능에 영향을 줄 수 있습니다. 상대적인 성능은 예상 기준으로 아래에 대략적으로 나와 있으며 약 15%의 변동성이 적용됩니다.

가상 컴퓨터의 크기는 가격 산정에 영향을 줍니다. 크기는 가상 컴퓨터의 처리, 메모리 및 저장소 용량에 영향을 줍니다. 저장소 비용은 Storage 계정에 사용된 페이지에 따라 개별적으로 계산됩니다. 자세한 내용은 [Cloud Services 가격 세부 정보](https://azure.microsoft.com/pricing/details/cloud-services/) 및 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

다음 고려 사항이 크기를 결정하는 데 도움이 될 수 있습니다.

* A8-A11 및 H 시리즈는 *계산 집약적 인스턴스*라고도 합니다. 이러한 크기를 실행하는 하드웨어는 고성능 컴퓨팅(HPC) 클러스터 응용 프로그램, 모델링 및 시뮬레이션을 포함하는 계산 집약적 및 네트워크 집약적 응용 프로그램을 위해 디자인되고 최적화되었습니다. A8-A11 시리즈는 Intel Xeon E5-2670 @ 2.6 GHZ를 사용하고 H 시리즈는 Intel Xeon E5-2667 v3 @ 3.2 GHz를 사용합니다. 이 크기의 사용과 관련된 자세한 내용 및 고려 사항은 [고성능 계산 VM 크기](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* Dv3 시리즈, Dv2 시리즈, D 시리즈, G 시리즈는 더 빠른 CPU와 더 좋은 로컬 디스크 성능을 요구하거나 더 높은 메모리 요구량을 가진 응용 프로그램에 이상적입니다. 이들은 많은 엔터프라이즈급 응용 프로그램을 위한 강력한 조합을 제공합니다.
* Azure 데이터 센터의 일부 물리적 호스트는 A5 – A11과 같은 큰 크기의 가상 머신을 지원하지 않을 수 있습니다. 결과적으로, 기존 가상 컴퓨터의 크기를 새 크기로 조정하거나 2013년 4월 16일 이전에 만들어진 가상 네트워크에서 새 가상 컴퓨터를 만들거나 새 가상 컴퓨터를 기존 클라우드 서비스에 추가하려고 하면 **{machine name} 가상 컴퓨터를 구성하지 못했습니다** 또는 **{machine name} 가상 컴퓨터를 만들지 못했습니다**라는 오류 메시지가 표시될 수 있습니다. 각 배포 시나리오의 해결 방법에 대한 지원 포럼에서 [오류: "가상 머신을 구성하지 못했습니다."](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) 를 참조하세요.
* 구독에 따라서도 특정 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량을 늘리려면 Azure 지원에 문의하세요.

## <a name="performance-considerations"></a>성능 고려 사항
Azure SKU에서 계산(CPU) 성능을 비교하는 방법을 제공하고 성능 요구 사항을 충족시킬 가능성이 가장 높은 SKU를 식별하는 ACU(Azure Compute 단위) 개념을 만들었습니다.  ACU는 현재 100인 작은(Standard_A1) VM에서 표준화되고 다른 SKU는 모두 SKU가 표준 벤치 마크를 얼마나 빨리 실행할 수 있는지를 나타냅니다.

> [!IMPORTANT]
> ACU는 단지 지침일 뿐입니다. 워크로드에 대한 결과가 달라질 수 있습니다.
>
>

<br>

| SKU 제품군 | ACU/코어 |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Small-ExtraLarge](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [A v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

*로 표시된 ACU는 Intel® 터보 기술을 사용하여 CPU 주파수를 증가시키고 성능 향상을 제공합니다. 상승량은 VM 크기, 워크로드 및 동일한 호스트에서 실행 중인 다른 작업에 따라 달라질 수 있습니다.

## <a name="size-tables"></a>테이블 크기 조정
다음 표에서 제공된 크기와 용량을 표시합니다.

* 저장소 용량 단위는 GiB(1024^3바이트) 단위로 표시됩니다. GB(1000^3바이트) 단위로 측정된 디스크를 GiB(1024^3바이트) 단위로 측정된 디스크와 비교할 때는 GiB 단위의 용량 수치가 더 작게 표시될 수 있음에 유의해야 합니다. 예를 들어 1023GiB = 1098.4GB입니다.
* 디스크 처리량은 IOPS(초당 입/출력 작업 수) 및 MBps로 측정되며, MBps = 10^6바이트/초입니다.
* 데이터 디스크는 캐시된 모드 또는 캐시되지 않은 모드에서 작동할 수 있습니다. 캐시된 데이터 디스크 작업의 경우 호스트 캐시 모드가 **ReadOnly** 또는 **ReadWrite**로 설정됩니다. 캐시되지 않은 데이터 디스크 작업의 경우에는 호스트 캐시 모드가 **None**으로 설정됩니다.
* 최대 네트워크 대역폭은 VM 유형별로 할당되고 집계된 최대 집계 대역폭입니다. 최대 대역폭은 적절한 네트워크 용량을 사용할 수 있도록 하는 올바른 VM 유형을 선택하기 위한 지침을 제공합니다. 낮음, 보통, 높음, 매우 높음 간을 이동할 경우 그에 따라 처리량이 증가합니다. 실제 네트워크 성능은 네트워크 및 응용 프로그램 부하, 응용 프로그램 네트워크 설정을 비롯한 여러 요인에 따라 달라집니다.

## <a name="a-series"></a>A 시리즈
| 크기            | CPU 코어 | 메모리: GiB  | 임시 저장소: GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| 매우 작음      | 1         | 0.768        | 20                   | 1/낮음 |
| 작음           | 1         | 1.75         | 70                   | 1/보통 |
| 중간          | 2         | 3.5          | 135                  | 1/보통 |
| 큰           | 4         | 7            | 285                  | 2/높음 |
| 매우 큼      | 8         | 14           | 605                  | 4/높음 |
| A5              | 2         | 14           | 135                  | 1/보통 |
| A6              | 4         | 28           | 285                  | 2/높음 |
| A7              | 8         | 56           | 605                  | 4/높음 |

## <a name="a-series---compute-intensive-instances"></a>A-시리즈 - 계산 집약적 인스턴스
이 크기의 사용과 관련된 자세한 내용 및 고려 사항은 [고성능 계산 VM 크기](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

| 크기            | CPU 코어 | 메모리: GiB  | 임시 저장소: GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2/높음 |
| A9*             |16         | 112          | 1817                 | 4/매우 높음 |
| A10             |8          | 56           | 1817                 | 2/높음 |
| A11             |16         | 112          | 1817                 | 4/매우 높음 |

\*RDMA 지원

## <a name="av2-series"></a>Av2 시리즈

| 크기            | CPU 코어 | 메모리: GiB  | 임시 저장소(SSD): GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1/보통                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/보통                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4/높음                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8/높음                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/보통                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4/높음                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8/높음                     |


## <a name="d-series"></a>D 시리즈
| 크기            | CPU 코어 | 메모리: GiB  | 임시 저장소(SSD): GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1/보통 |
| Standard_D2     | 2         | 7            | 100                  | 2/높음 |
| Standard_D3     | 4         | 14           | 200                  | 4/높음 |
| Standard_D4     | 8         | 28           | 400                  | 8/높음 |
| Standard_D11    | 2         | 14           | 100                  | 2/높음 |
| Standard_D12    | 4         | 28           | 200                  | 4/높음 |
| Standard_D13    | 8         | 56           | 400                  | 8/높음 |
| Standard_D14    | 16        | 112          | 800                  | 8/매우 높음 |

## <a name="dv2-series"></a>Dv2 시리즈
| 크기            | CPU 코어 | 메모리: GiB  | 임시 저장소(SSD): GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1/보통 |
| Standard_D2_v2  | 2         | 7            | 100                  | 2/높음 |
| Standard_D3_v2  | 4         | 14           | 200                  | 4/높음 |
| Standard_D4_v2  | 8         | 28           | 400                  | 8/높음 |
| Standard_D5_v2  | 16        | 56           | 800                  | 8/극히 높음 |
| Standard_D11_v2 | 2         | 14           | 100                  | 2/높음 |
| Standard_D12_v2 | 4         | 28           | 200                  | 4/높음 |
| Standard_D13_v2 | 8         | 56           | 400                  | 8/높음 |
| Standard_D14_v2 | 16        | 112          | 800                  | 8/극히 높음 |
| Standard_D15_v2 | 20        | 140          | 1,000                | 8/극히 높음 |

## <a name="dv3-series"></a>Dv3 시리즈

| 크기            | CPU 코어 | 메모리: GiB   | 임시 저장소(SSD): GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2/보통 |
| Standard_D4_v3  | 4         | 16            | 100                  | 2/높음 |
| Standard_D8_v3  | 8         | 32            | 200                  | 4/높음 |
| Standard_D16_v3 | 16        | 64            | 400                  | 8/극히 높음 |
| Standard_D32_v3 | 32        | 128           | 800                  | 8/극히 높음 |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8/극히 높음 |

## <a name="ev3-series"></a>Ev3 시리즈

| 크기            | CPU 코어 | 메모리: GiB   | 임시 저장소(SSD): GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2/보통 |
| Standard_E4_v3  | 4         | 32            | 100                  | 2/높음 |
| Standard_E8_v3  | 8         | 64            | 200                  | 4/높음 |
| Standard_E16_v3 | 16        | 128           | 400                  | 8/극히 높음 |
| Standard_E32_v3 | 32        | 256           | 800                  | 8/극히 높음 |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8/극히 높음 |


## <a name="g-series"></a>G 시리즈
| 크기            | CPU 코어 | 메모리: GiB  | 임시 저장소(SSD): GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/높음 |
| Standard_G2     | 4         | 56           | 768                  |2/높음 |
| Standard_G3     | 8         | 112          | 1,536                |4/매우 높음 |
| Standard_G4     | 16        | 224          | 3,072                |8/극히 높음 |
| Standard_G5     | 32        | 448          | 6,144                |8/극히 높음 |

## <a name="h-series"></a>H 시리즈
Azure H 시리즈 가상 머신은 분자 모델링 및 컴퓨팅 유체 역학 등의 고급 계산 요구를 충족하기 위한 차세대 고성능 컴퓨팅 VM입니다. 이러한 8코어 및 16코어 VM은 DDR4 메모리 및 로컬 SSD 기반 저장소를 제공하는 Intel Haswell E5-2667 V3 프로세서 기술을 토대로 구축되었습니다.

H 시리즈 제품은 뛰어난 CPU 처리 능력 외에도 FDR InfiniBand 및 여러 메모리 구성을 사용하는 낮은 대기 시간 RDMA 네트워킹을 위한 다양한 옵션을 제공하여 메모리 집약적 계산 요구 사항을 지원합니다.

| 크기            | CPU 코어 | 메모리: GiB  | 임시 저장소(SSD): GiB       | 최대 NIC 수/네트워크 대역폭 |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8/높음 |
| Standard_H16    | 16        | 112          | 2000                 | 8/매우 높음 |
| Standard_H8m    | 8         | 112          | 1000                 | 8/높음 |
| Standard_H16m   | 16        | 224          | 2000                 | 8/매우 높음 |
| Standard_H16r*  | 16        | 112          | 2000                 | 8/매우 높음 |
| Standard_H16mr* | 16        | 224          | 2000                 | 8/매우 높음 |

\*RDMA 지원

## <a name="configure-sizes-for-cloud-services"></a>Cloud Services에 적합한 크기 구성
[서비스 정의 파일](cloud-services-model-and-package.md#csdef)에 설명된 서비스 모델의 일부로서 역할 인스턴스의 Virtual Machine 크기를 지정할 수 있습니다. 역할의 크기에 따라 실행 인스턴스에 할당되는 CPU 코어 수, 메모리 용량 및 로컬 파일 시스템 크기가 결정됩니다. 응용 프로그램의 리소스 요구 사항에 따라 역할 크기를 선택합니다.

웹 역할 인스턴스에 역할 크기를 [Standard_D2](#general-purpose-d)로 설정하는 예는 다음과 같습니다.

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>기존 역할의 크기 변경

워크로드의 특성이 바뀌거나 새 VM 크기를 사용할 수 있게 되면 역할 크기를 변경하는 것이 좋습니다. 이렇게 하려면 위에 표시된 대로 서비스 정의 파일에서 VM 크기를 변경하고 클라우드 서비스를 다시 패키지한 다음 배포해야 합니다. 포털 또는 PowerShell에서 VM 크기를 직접 변경할 수는 없습니다.

>[!TIP]
> 다른 환경에서 역할에 대해 서로 다른 VM 크기를 사용할 수 있습니다(예: 테스트 및 프로덕션). 이 작업을 수행하는 한 가지 방법은 프로젝트에 여러 개의 서비스 정의(.csdef) 파일을 만든 다음 CSPack 도구를 사용하여 자동 빌드하는 동안 환경마다 다른 클라우드 서비스 패키지를 만드는 것입니다. Cloud Services 패키지의 요소 및 만드는 방법에 대한 자세한 내용은 [Cloud Services 모델 정의 및 패키지 방법](cloud-services-model-and-package.md)을 참조하세요.
>
>

## <a name="get-a-list-of-sizes"></a>크기 목록 가져오기
PowerShell 또는 REST API를 사용하여 크기 목록을 가져올 수 있습니다. REST API는 [여기](https://msdn.microsoft.com/library/azure/dn469422.aspx)에 기록되어 있습니다. 다음 코드는 Cloud Services에 사용할 수 있는 모든 크기를 나열하는 PowerShell 명령입니다. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>다음 단계
* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에 대해 자세히 알아보세요.
* 자세한 내용은 HPC 워크로드의 [고성능 계산 VM 크기](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 대해 자세히 알아보세요.
