---
title: Azure Linux VM에 대한 벤치마크 점수 계산 | Microsoft Docs
description: Linux를 실행하는 Azure VM에 대한 CoreMark 계산 벤치마크 점수를 비교합니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 07e313404b1d57fe4c851ab182823bcea454cbec
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Linux VM에 대한 벤치마크 점수 계산
다음 CoreMark 벤치마크 점수는 Ubuntu를 실행하는 Azure의 고성능 VM 라인업에 대한 계산 성능을 보여 줍니다. [Windows Vm](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 대해 Compute 벤치마크 점수를 사용할 수도 있습니다.

## <a name="av2---general-compute"></a>Av2 - 일반 계산
(3/23/2018 7:32:49 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 1 | 1 | 1.9 | 6,514 | 56 | 0.86% | 119 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 6,162 | 195 | 3.17% | 70 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 6,505 | 425 | 6.53% | 63 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 3.9 | 13,061 | 282 | 2.16% | 112 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 12,270 | 390 | 3.18% | 56 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 13,406 | 793 | 5.91% | 35 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 16.0 | 13,148 | 130 | 0.98% | 84 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 16.0 | 12,450 | 563 | 4.52% | 35 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 16.0 | 12,929 | 988 | 7.64% | 56 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 8.0 | 26,037 | 356 | 1.37% | 70 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 8.0 | 24,728 | 594 | 2.40% | 77 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 8.0 | 26,549 | 1,375 | 5.18% | 42 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 32.1 | 25,865 | 672 | 2.60% | 70 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 32.1 | 24,646 | 1,104 | 4.48% | 63 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 32.1 | 25,058 | 1,292 | 5.15% | 35 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 16.0 | 52,963 | 694 | 1.31% | 98 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 16.0 | 49,908 | 970 | 1.94% | 35 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 16.0 | 48,584 | 742 | 1.53% | 77 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 2 | 64.4 | 52,900 | 815 | 1.54% | 133 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 64.4 | 49,733 | 861 | 1.73% | 84 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 64.4 | 48,494 | 501 | 1.03% | 49 |

## <a name="a0-7-standard-general-compute"></a>A0-7 표준 일반 계산
(3/23/2018 9:06:07 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 | AMD Opteron(tm) 프로세서 4171 HE | 1 | 1 | 0.6 | 3,556 | 14 | 0.39% | 21 |
| Standard_A0 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 1 | 1 | 0.6 | 3,137 | 16 | 0.51% | 70 |
| Standard_A0 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 0.6 | 3,146 | 134 | 4.27% | 63 |
| Standard_A1 | AMD Opteron(tm) 프로세서 4171 HE | 1 | 1 | 1.7 | 6,862 | 169 | 2.47% | 42 |
| Standard_A1 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 1 | 1 | 1.7 | 6,471 | 104 | 1.61% | 98 |
| Standard_A1 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.7 | 6,211 | 262 | 4.22% | 98 |
| Standard_A2 | AMD Opteron(tm) 프로세서 4171 HE | 2 | 1 | 3.4 | 13,950 | 415 | 2.98% | 35 |
| Standard_A2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 3.4 | 13,205 | 187 | 1.41% | 112 |
| Standard_A2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.4 | 12,563 | 639 | 5.09% | 84 |
| Standard_A3 | AMD Opteron(tm) 프로세서 4171 HE | 4 | 1 | 6.9 | 28,069 | 679 | 2.42% | 28 |
| Standard_A3 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 6.9 | 26,238 | 236 | 0.90% | 98 |
| Standard_A3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 6.9 | 25,195 | 827 | 3.28% | 77 |
| Standard_A4 | AMD Opteron(tm) 프로세서 4171 HE | 8 | 2 | 14.0 | 56,604 | 305 | 0.54% | 7 |
| Standard_A4 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 14.0 | 53,271 | 577 | 1.08% | 63 |
| Standard_A4 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 14.0 | 49,744 | 1,118 | 2.25% | 147 |
| Standard_A5 | AMD Opteron(tm) 프로세서 4171 HE | 2 | 1 | 14.0 | 14,164 | 273 | 1.93% | 21 |
| Standard_A5 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 14.0 | 13,136 | 151 | 1.15% | 98 |
| Standard_A5 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 14.0 | 12,510 | 615 | 4.92% | 84 |
| Standard_A6 | AMD Opteron(tm) 프로세서 4171 HE | 4 | 1 | 28.1 | 28,272 | 392 | 1.39% | 28 |
| Standard_A6 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 28.1 | 26,165 | 294 | 1.13% | 105 |
| Standard_A6 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 28.1 | 25,178 | 1,009 | 4.01% | 70 |
| Standard_A7 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 56.3 | 52,949 | 1,114 | 2.10% | 112 |
| Standard_A7 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 56.3 | 49,677 | 894 | 1.80% | 133 |

## <a name="dsv3---general-compute--premium-storage"></a>DSv3 - 일반 계산 + Premium Storage
(3/23/2018 7:28:44 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 8.0 | 20,259 | 729 | 3.60% | 140 |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 8.0 | 20,364 | 1,007 | 4.94% | 70 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 16.0 | 39,662 | 1,757 | 4.43% | 182 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 16.0 | 40,632 | 2,422 | 5.96% | 168 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 32.1 | 80,055 | 1,022 | 1.28% | 133 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 32.1 | 80,639 | 2,844 | 3.53% | 56 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 64.4 | 158,407 | 3,267 | 2.06% | 119 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 64.4 | 154,146 | 4,432 | 2.88% | 63 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 32 | 2 | 128.9 | 307,408 | 3,203 | 1.04% | 70 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 1 | 128.9 | 309,183 | 3,132 | 1.01% | 56 |
| Standard_D32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 128.9 | 80,160 | 3,912 | 4.88% | 84 |
| Standard_D32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 128.9 | 84,406 | 1,939 | 2.30% | 70 |
| Standard_D32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 32 | 2 | 128.9 | 157,154 | 2,152 | 1.37% | 84 |
| Standard_D32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 1 | 128.9 | 155,460 | 3,663 | 2.36% | 112 |
| Standard_D64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 257.9 | 615,873 | 7,266 | 1.18% | 140 |
| Standard_D64-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 257.9 | 170,309 | 2,169 | 1.27% | 98 |
| Standard_D64-32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 257.9 | 306,578 | 4,095 | 1.34% | 98 |

## <a name="dv3---general-compute"></a>Dv3 - 일반 계산
(3/23/2018 7:32:37 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 8.0 | 20,791 | 1,531 | 7.36% | 175 |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 8.0 | 21,326 | 1,622 | 7.61% | 84 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 16.0 | 39,978 | 1,853 | 4.63% | 98 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 16.0 | 41,842 | 2,798 | 6.69% | 77 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 32.1 | 80,559 | 1,990 | 2.47% | 154 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 32.1 | 79,711 | 4,368 | 5.48% | 63 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 64.4 | 160,309 | 3,371 | 2.10% | 133 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 64.4 | 155,447 | 3,426 | 2.20% | 56 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 32 | 2 | 128.9 | 309,021 | 4,128 | 1.34% | 105 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 1 | 128.9 | 311,375 | 3,714 | 1.19% | 49 |
| Standard_D64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 257.9 | 613,424 | 19,225 | 3.13% | 84 |

## <a name="esv3---memory-optimized--premium-storage"></a>Esv3 - 메모리 최적화 + Premium Storage
(3/23/2018 7:31:01 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_E2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 16.0 | 21,015 | 1,112 | 5.29% | 210 |
| Standard_E4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 32.1 | 40,691 | 1,928 | 4.74% | 287 |
| Standard_E8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 64.4 | 79,841 | 2,856 | 3.58% | 161 |
| Standard_E16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 128.9 | 155,976 | 2,666 | 1.71% | 161 |
| Standard_E32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 257.9 | 297,695 | 8,535 | 2.87% | 140 |
| Standard_E32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 2 | 257.9 | 86,375 | 7,300 | 8.45% | 140 |
| Standard_E32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 257.9 | 158,842 | 10,809 | 6.81% | 189 |
| Standard_E64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 435.3 | 613,160 | 8,637 | 1.41% | 63 |
| Standard_E64-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 435.3 | 170,343 | 2,052 | 1.20% | 126 |
| Standard_E64-32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 435.3 | 307,110 | 3,759 | 1.22% | 126 |
| Standard_E64is_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 435.3 | 613,892 | 7,763 | 1.26% | 140 |

## <a name="ev3---memory-optimized"></a>Ev3 - 메모리 최적화
(3/23/2018 7:29:35 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_E2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 16.0 | 23,318 | 2,734 | 11.72% | 245 |
| Standard_E4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 32.1 | 42,612 | 3,834 | 9.00% | 154 |
| Standard_E8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 64.4 | 83,488 | 4,888 | 5.85% | 189 |
| Standard_E16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 128.9 | 159,537 | 4,999 | 3.13% | 175 |
| Standard_E32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 257.9 | 306,311 | 8,547 | 2.79% | 196 |
| Standard_E64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 435.3 | 605,599 | 36,245 | 5.98% | 168 |
| Standard_E64i_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 435.3 | 618,198 | 10,022 | 1.62% | 154 |

## <a name="dsv2---storage-optimized"></a>DSv2 - 저장소 최적화
(3/23/2018 7:30:03 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 3.4 | 14,691 | 626 | 4.26% | 182 |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 3.4 | 14,577 | 1,120 | 7.68% | 63 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 6.9 | 29,156 | 1,095 | 3.76% | 91 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 6.9 | 29,157 | 1,945 | 6.67% | 56 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 14.0 | 55,981 | 1,625 | 2.90% | 98 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 14.0 | 57,921 | 2,628 | 4.54% | 77 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 28.1 | 115,636 | 1,963 | 1.70% | 161 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 28.1 | 111,527 | 494 | 0.44% | 14 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 56.3 | 220,333 | 4,856 | 2.20% | 91 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 56.3 | 217,812 | 5,320 | 2.44% | 35 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 56.3 | 222,116 | 4,445 | 2.00% | 56 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 14.0 | 28,364 | 929 | 3.28% | 105 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 14.0 | 28,772 | 1,351 | 4.69% | 70 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 28.1 | 56,130 | 2,174 | 3.87% | 119 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 28.1 | 58,490 | 2,670 | 4.56% | 63 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 56.3 | 115,107 | 2,525 | 2.19% | 126 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 56.3 | 111,429 | 1,111 | 1.00% | 35 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 56.3 | 28,933 | 1,176 | 4.06% | 154 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 56.3 | 30,336 | 1,635 | 5.39% | 98 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 56.3 | 57,644 | 1,893 | 3.28% | 126 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 56.3 | 57,927 | 1,306 | 2.26% | 84 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 112.8 | 218,363 | 3,866 | 1.77% | 154 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 112.8 | 224,791 | 4,363 | 1.94% | 77 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 2 | 112.8 | 56,398 | 2,675 | 4.74% | 126 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 112.8 | 61,709 | 1,468 | 2.38% | 49 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 2 | 112.8 | 112,282 | 4,368 | 3.89% | 147 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 112.8 | 111,469 | 3,747 | 3.36% | 49 |
| Standard_DS15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 2 | 141.0 | 271,529 | 3,749 | 1.38% | 189 |

## <a name="dv2---general-compute"></a>Dv2 - 일반 계산
(3/23/2018 7:27:28 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 3.4 | 15,027 | 963 | 6.41% | 105 |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 3.4 | 15,296 | 1,696 | 11.09% | 77 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 6.9 | 29,060 | 1,405 | 4.83% | 133 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 6.9 | 31,552 | 2,315 | 7.34% | 63 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 14.0 | 57,666 | 1,559 | 2.70% | 168 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 14.0 | 58,312 | 3,640 | 6.24% | 77 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 28.1 | 114,708 | 2,019 | 1.76% | 147 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 28.1 | 114,971 | 5,415 | 4.71% | 42 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 56.3 | 221,546 | 5,093 | 2.30% | 112 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 56.3 | 214,933 | 3,741 | 1.74% | 21 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 56.3 | 225,410 | 5,421 | 2.41% | 77 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 14.0 | 29,125 | 1,267 | 4.35% | 154 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 14.0 | 31,315 | 1,762 | 5.63% | 63 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 28.1 | 57,386 | 1,712 | 2.98% | 168 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 28.1 | 57,903 | 3,322 | 5.74% | 112 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 56.3 | 114,319 | 2,482 | 2.17% | 126 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 56.3 | 114,933 | 3,486 | 3.03% | 49 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 112.8 | 219,594 | 5,752 | 2.62% | 119 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 112.8 | 225,880 | 3,699 | 1.64% | 77 |
| Standard_D15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 2 | 141.0 | 275,417 | 6,457 | 2.34% | 154 |

## <a name="d---general-compute"></a>D - 일반 계산
(3/23/2018 7:28:16 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 1 | 1 | 3.4 | 10,331 | 303 | 2.93% | 112 |
| Standard_D1 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 3.4 | 14,547 | 916 | 6.30% | 14 |
| Standard_D2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 6.9 | 21,112 | 383 | 1.81% | 119 |
| Standard_D2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 6.9 | 27,545 | 173 | 0.63% | 7 |
| Standard_D3 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 14.0 | 41,910 | 974 | 2.32% | 133 |
| Standard_D3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 14.0 | 57,567 | 2,166 | 3.76% | 21 |
| Standard_D4 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 28.1 | 85,255 | 971 | 1.14% | 119 |
| Standard_D4 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 28.1 | 112,324 | 1,367 | 1.22% | 14 |
| Standard_D11 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 14.0 | 21,172 | 479 | 2.26% | 140 |
| Standard_D11 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 14.0 | 29,935 | 1,702 | 5.69% | 14 |
| Standard_D12 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 28.1 | 41,788 | 1,185 | 2.84% | 98 |
| Standard_D12 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 28.1 | 57,105 | 761 | 1.33% | 28 |
| Standard_D13 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 56.3 | 85,180 | 1,395 | 1.64% | 119 |
| Standard_D13 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 56.3 | 113,421 | 1,695 | 1.49% | 28 |
| Standard_D14 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 16 | 2 | 112.8 | 165,497 | 3,376 | 2.04% | 105 |
| Standard_D14 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 112.8 | 217,813 | 4,869 | 2.24% | 35 |

## <a name="ds---storage-optimized"></a>DS - 저장소 최적화
(3/23/2018 7:34:52 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 1 | 1 | 3.4 | 10,666 | 134 | 1.25% | 126 |
| Standard_DS11 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 14.0 | 21,330 | 268 | 1.26% | 49 |
| Standard_DS12 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 28.1 | 42,225 | 555 | 1.31% | 126 |
| Standard_DS12 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 28.1 | 56,288 | 976 | 1.73% | 14 |
| Standard_DS13 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 56.3 | 85,292 | 1,303 | 1.53% | 98 |
| Standard_DS14 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 16 | 2 | 112.8 | 162,996 | 3,911 | 2.40% | 70 |
| Standard_DS14 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 112.8 | 217,038 | 3,540 | 1.63% | 28 |
| Standard_DS2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 6.9 | 21,398 | 193 | 0.90% | 119 |
| Standard_DS3 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 14.0 | 42,470 | 559 | 1.32% | 126 |
| Standard_DS3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 14.0 | 55,664 | 385 | 0.69% | 7 |
| Standard_DS4 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 28.1 | 84,956 | 1,087 | 1.28% | 105 |

## <a name="fsv2---compute--storage-optimized"></a>Fsv2 - 계산 + 저장소 최적화
(3/23/2018 7:33:11 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F2s_v2 | Intel(R) Xeon(R) 플래티넘 8168 CPU @ 2.70GHz | 2 | 1 | 3.9 | 25,392 | 157 | 0.62% | 49 |
| Standard_F4s_v2 | Intel(R) Xeon(R) 플래티넘 8168 CPU @ 2.70GHz | 4 | 1 | 8.0 | 48,065 | 656 | 1.36% | 42 |
| Standard_F8s_v2 | Intel(R) Xeon(R) 플래티넘 8168 CPU @ 2.70GHz | 8 | 1 | 16.0 | 95,026 | 1,205 | 1.27% | 49 |
| Standard_F16s_v2 | Intel(R) Xeon(R) 플래티넘 8168 CPU @ 2.70GHz | 16 | 1 | 32.1 | 187,039 | 2,681 | 1.43% | 42 |
| Standard_F32s_v2 | Intel(R) Xeon(R) 플래티넘 8168 CPU @ 2.70GHz | 32 | 1 | 64.4 | 375,828 | 5,180 | 1.38% | 70 |
| Standard_F64s_v2 | Intel(R) Xeon(R) 플래티넘 8168 CPU @ 2.70GHz | 64 | 2 | 128.9 | 741,859 | 5,954 | 0.80% | 49 |
| Standard_F72s_v2 | Intel(R) Xeon(R) 플래티넘 8168 CPU @ 2.70GHz | 72 | 2 | 145.0 | 831,616 | 4,867 | 0.59% | 42 |

## <a name="f---compute-optimized"></a>F - 계산 최적화
(3/23/2018 7:28:54 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 14,762 | 815 | 5.52% | 175 |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 15,756 | 1,653 | 10.49% | 91 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 29,265 | 1,593 | 5.44% | 182 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 32,105 | 3,072 | 9.57% | 70 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 8.0 | 57,174 | 2,009 | 3.51% | 182 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 8.0 | 57,613 | 3,076 | 5.34% | 91 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 16.0 | 114,851 | 1,983 | 1.73% | 182 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 16.0 | 113,706 | 2,797 | 2.46% | 105 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 32.1 | 220,641 | 5,114 | 2.32% | 140 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 32.1 | 217,174 | 4,952 | 2.28% | 28 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 32.1 | 226,457 | 5,507 | 2.43% | 49 |

## <a name="fs---compute-and-storage-optimized"></a>Fs - 계산 및 저장소 최적화
(3/23/2018 7:30:14 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 14,630 | 678 | 4.64% | 203 |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 15,247 | 801 | 5.25% | 91 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 28,931 | 1,105 | 3.82% | 133 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 29,079 | 1,454 | 5.00% | 77 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 8.0 | 56,674 | 1,518 | 2.68% | 189 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 8.0 | 57,051 | 2,872 | 5.03% | 91 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 16.0 | 113,709 | 11,105 | 9.77% | 196 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 16.0 | 111,477 | 2,531 | 2.27% | 70 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 32.1 | 222,637 | 4,405 | 1.98% | 140 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 32.1 | 218,297 | 4,284 | 1.96% | 14 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 32.1 | 225,001 | 3,033 | 1.35% | 98 |

## <a name="g---compute-optimized"></a>G - 계산 최적화
(3/23/2018 7:27:25 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 2 | 1 | 28.1 | 32,071 | 4,239 | 13.22% | 182 |
| Standard_G2 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 56.3 | 60,598 | 6,048 | 9.98% | 175 |
| Standard_G3 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 112.8 | 111,058 | 6,536 | 5.89% | 161 |
| Standard_G4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 225.7 | 200,516 | 1,833 | 0.91% | 154 |
| Standard_G5 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 451.5 | 395,591 | 5,192 | 1.31% | 154 |

## <a name="gs---storage-optimized"></a>GS - 저장소 최적화
(3/23/2018 7:25:12 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 2 | 1 | 28.1 | 28,771 | 2,006 | 6.97% | 231 |
| Standard_GS2 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 56.3 | 54,947 | 3,699 | 6.73% | 203 |
| Standard_GS3 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 112.8 | 105,054 | 4,441 | 4.23% | 182 |
| Standard_GS4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 225.7 | 199,189 | 12,092 | 6.07% | 168 |
| Standard_GS4-4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 225.7 | 59,066 | 2,935 | 4.97% | 196 |
| Standard_GS4-8 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 225.7 | 107,076 | 3,209 | 3.00% | 168 |
| Standard_GS5 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 451.5 | 386,803 | 9,303 | 2.41% | 161 |
| Standard_GS5-8 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 2 | 451.5 | 116,094 | 1,935 | 1.67% | 42 |
| Standard_GS5-16 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 2 | 451.5 | 209,500 | 2,622 | 1.25% | 63 |

## <a name="h---high-performance-compute-hpc"></a>H - HPC(고성능 계산)
(3/23/2018 7:27:16 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 8 | 1 | 56.3 | 140,445 | 2,840 | 2.02% | 147 |
| Standard_H8m | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 8 | 1 | 112.8 | 141,086 | 2,209 | 1.57% | 147 |
| Standard_H16 | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 112.8 | 270,129 | 6,502 | 2.41% | 56 |
| Standard_H16m | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 225.7 | 272,667 | 6,686 | 2.45% | 112 |
| Standard_H16mr | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 225.7 | 272,683 | 6,484 | 2.38% | 70 |
| Standard_H16r | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 112.8 | 271,822 | 6,126 | 2.25% | 98 |

## <a name="hpc---a8-11"></a>HPC - A8-11
(3/23/2018 7:35:10 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 | Intel(R) Xeon(R) CPU E5-2670 0 @ 2.60GHz | 8 | 1 | 56.3 | 117,148 | 1,877 | 1.60% | 189 |
| Standard_A9 | Intel(R) Xeon(R) CPU E5-2670 0 @ 2.60GHz | 16 | 2 | 112.8 | 225,608 | 7,532 | 3.34% | 147 |
| Standard_A10 | Intel(R) Xeon(R) CPU E5-2670 0 @ 2.60GHz | 8 | 1 | 56.3 | 117,638 | 1,988 | 1.69% | 168 |
| Standard_A11 | Intel(R) Xeon(R) CPU E5-2670 0 @ 2.60GHz | 16 | 2 | 112.8 | 225,980 | 7,067 | 3.13% | 161 |

## <a name="ls---storage-optimized"></a>Ls - 저장소 최적화
(3/23/2018 7:58:51 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_L4s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 32.1 | 55,962 | 3,567 | 6.37% | 154 |
| Standard_L8s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 64.4 | 106,482 | 3,178 | 2.98% | 168 |
| Standard_L16s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 128.9 | 197,906 | 12,605 | 6.37% | 168 |
| Standard_L32s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 257.9 | 388,652 | 6,274 | 1.61% | 126 |

## <a name="m---memory-optimized"></a>M - 메모리 최적화
(3/23/2018 8:57:07 PM pbi 2050259)

| VM 크기 | CPU | vCPU | NUMA 노드 | 메모리(GiB) | Avg 점수 | StdDev | StdDev% | #실행 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_M64-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1,806.2 | 339,412 | 4,655 | 1.37% | 21 |
| Standard_M64ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1,806.2 | 662,070 | 16,539 | 2.50% | 70 |
| Standard_M64s | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1,032.1 | 659,757 | 22,439 | 3.40% | 21 |
| Standard_M128-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 4 | 3,923.0 | 332,861 | 6,061 | 1.82% | 42 |
| Standard_M128-64ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 3,923.0 | 656,788 | 16,341 | 2.49% | 35 |
| Standard_M128ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 3,923.0 | 1,275,328 | 16,544 | 1.30% | 70 |
| Standard_M128s | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 2,064.3 | 1,275,445 | 19,510 | 1.53% | 42 |

## <a name="about-coremark"></a>CoreMark 정보
Linux 숫자는 Ubuntu에서 [CoreMark](http://www.eembc.org/coremark/faq.php) 를 실행하여 계산됩니다. CoreMark는 가상 CPU의 수에 설정된 스레드 수와 PThread에 설정된 동시성 수로 구성 되었습니다. 대상 반복 횟수는 20초 이상(일반적으로 훨씬 더 김)의 런타임을 제공하기 위해 예상되는 성능에 따라 조정되었습니다. 최종 점수는 완료된 반복 횟수를 테스트를 실행하는 데 걸린 시간(초)으로 나누어 나타냅니다. 각 테스트는 각 VM에서 적어도 7번 실행되었습니다. 위에 표시된 테스트 실행 날짜 실행된 날짜에서 VM이 지원된 Azure 공용 지역의 여러 VM에 대한 테스트 실행 기본 A와 B(버스터블) 시리즈는 성능이 변수이므로 표시되지 않습니다. N 시리즈는 GPU 중심이고 Coremark는 GPU 성능을 측정하지 않으므로 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계
* 저장 용량, 디스크 세부 정보 및 VM 크기 선택시 추가적인 고려 사항에 관한 자세한 내용은 [가상 머신의 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* Linux VM에서 CoreMark 스크립트를 실행하려면 [CoreMark 스크립트 팩](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)을 다운로드합니다.

