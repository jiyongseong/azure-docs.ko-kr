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
ms.openlocfilehash: ee145e5784ae6da6cce22f1b21f9a5d45335ea8b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
A8-A11 및 H 시리즈는 *계산 집약적 인스턴스*라고도 합니다. 이러한 크기를 실행하는 하드웨어는 고성능 컴퓨팅(HPC) 클러스터 응용 프로그램, 모델링 및 시뮬레이션을 포함하는 계산 집약적 및 네트워크 집약적 응용 프로그램을 위해 디자인되고 최적화되었습니다. A8-A11 시리즈는 Intel Xeon E5-2670 @ 2.6 GHZ를 사용하고 H 시리즈는 Intel Xeon E5-2667 v3 @ 3.2 GHz를 사용합니다.  이 문서에서는 이 그룹화에서 각 크기에 대한 저장소 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다. 

Azure H 시리즈 가상 머신은 분자 모델링 및 컴퓨팅 유체 역학과 같은 고급 계산 요구 사항에 맞춘 최신 고성능 컴퓨팅 VM입니다. 이러한 8 및 16 vCPU VM은 DDR4 메모리 및 SSD 기반 임시 저장소를 제공하는 Intel Haswell E5-2667 V3 프로세서 기술을 토대로 구축되었습니다. 

H 시리즈 제품은 뛰어난 CPU 처리 능력 외에도 FDR InfiniBand 및 여러 메모리 구성을 사용하는 낮은 대기 시간 RDMA 네트워킹을 위한 다양한 옵션을 제공하여 메모리 집약적 계산 요구 사항을 지원합니다.



## <a name="h-series"></a>H 시리즈

ACU: 290-300

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 디스크 처리량: IOPS | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> MPI 응용 프로그램의 경우 초단기 대기 시간 및 고대역폭을 제공하는 FDR InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

<br>



## <a name="a-series---compute-intensive-instances"></a>A-시리즈 - 계산 집약적 인스턴스

ACU: 225

| 크기 | vCPU | 메모리: GiB | 임시 저장소(HDD) GiB | 최대 데이터 디스크 수 | 최대 데이터 디스크 처리량: IOPS | 최대 NIC 수|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>MPI 응용 프로그램의 경우 초단기 대기 시간 및 고대역폭을 제공하는 FDR InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

<br>



