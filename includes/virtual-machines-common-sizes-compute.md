---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 638b52edb554b6bddb206943fca42b03bdc69060
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "30845134"
---
<!-- F-series, Fs-series* -->

Compute 최적화된 VM 크기는 높은 CPU 대 메모리 비율을 가지며 중간 규모의 트래픽 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 응용 프로그램 서버에 적합합니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 저장소 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.

Fsv2 시리즈는 Intel® Xeon® Platinum 8168 프로세서를 기반으로 하며 2.7GHz라는 기본 코어 및 최대 3.7GHz의 단일 코어 터보를 제공합니다. Intel 확장 가능한 프로세서에서 새로 도입된 Intel® AVX-512 지침은 단일 및 이중 정밀도 부동 소수점 연산에서 벡터 처리 워크로드에 대한 성능을 최대 2배로 향상시킵니다. 즉, 계산 워크로드를 빠르게 처리합니다. 

시간당 가격이 더 낮은 Fsv2 시리즈는 vCPU당 ACU(Azure Compute 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다. 

F 시리즈는 2.4GHz Intel Xeon® E5-2673 v3(Haswell) 프로세서를 기반으로 하고, Intel Turbo Boost Technology 2.0을 채택하여 최대 3.1GHz의 클럭 속도를 달성할 수 있습니다. Dv2 시리즈의 VM과 동일한 CPU 성능입니다.  

F 시리즈 VM은 더 빠른 CPU를 요구하지만 많은 메모리나 vCPU당 임시 저장소가 필요하지 않은 워크로드에 적합합니다.  분석, 게임 서버, 웹 서버 및 배치 처리 등의 워크로드는 F 시리즈 값을 사용하면 도움이 됩니다.

Fs 시리즈는 Premium Storage 외에도 F 시리즈의 모든 기능을 제공합니다.

## <a name="fsv2-series-sup1sup"></a>Fsv2 시리즈 <sup>1</sup>

ACU: 195~210

| 크기             | vCPU 수 | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 저장소 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | 2 / 875                                        |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | 2 / 1,750                                      |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | 4 / 3,500                                      |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | 4 / 7,000                                      |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | 8 / 14,000                                     |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | 8 / 28,000                                     |
| Standard_F72s_v2<sup>2, 3</sup> | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | 8 / 30,000                                     |

<sup>1</sup> Fsv2 시리즈 VM은 Intel® 하이퍼 스레딩 기술 제공

<sup>2</sup> 64개를 초과하는 vCPU에는 지원되는 게스트 OS인 Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 및 Red Hat Enterprise Linux, LIS 4.2.1을 사용하는 CentOS 7.3 또는 Oracle Linux 7.3 중 하나가 필요합니다.

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

## <a name="fs-series-sup1sup"></a>Fs 시리즈 <sup>1</sup>

ACU: 210-250

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 저장소 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000/32(12) |3,200/48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8,000/64(24) |6,400/96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16,000/128 (48) |12,800/192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32,000/256(96) |25,600/384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64,000/512(192) |51,200/768 |8 / 12000 |

MBps = 초당 10^6바이트, GiB = 1024^3바이트

<sup>1</sup> Fs 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다.  자세한 내용은 [Premium Storage: Azure 가상 머신 작업을 위한 고성능 저장소](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요.


<br>

## <a name="f-series"></a>F 시리즈

ACU: 210-250

| 크기         | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |


<br>


