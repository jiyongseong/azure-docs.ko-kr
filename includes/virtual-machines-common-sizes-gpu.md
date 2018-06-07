---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 03/30/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 5457ac8bd229889ed2b96354c44066959c00c64f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2018
---
GPU 최적화 VM 크기는 단일 또는 여러 NVIDIA GPU에서 사용 가능한 특수한 가상 머신입니다. 이러한 크기는 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 저장소 처리량 및 네트워크 대역폭뿐만 아니라 GPU, vCPU, 데이터 디스크 및 NIC의 수 및 형식에 대한 정보를 제공합니다. 

* **NC, NCv2, NCv3 및 ND** 크기는 CUDA 기반 및 OpenCL 기반 응용 프로그램 및 시뮬레이션, AI, 딥 러닝을 포함하여 계산 집약적이고 네트워크 집약적인 응용 프로그램과 알고리즘에 최적화되어 있습니다. 
* **NV** 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 활용하는 원격 시각화, 스트리밍, 게임, 인코딩 및 VDI 시나리오에 맞게 최적화되고 설계되었습니다.  


## <a name="nc-series"></a>NC 시리즈

NC 시리즈 VM은 [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) 카드로 구동됩니다. 사용자는 에너지 탐색 응용 프로그램, 충돌 시뮬레이션, 광선 추적 렌더링, 심층 학습 등에 CUDA를 활용하여 데이터를 더 빠르게 처리할 수 있습니다. NC24r 구성은, 긴밀하게 결합된 병렬 컴퓨팅 워크로드용으로 최적화된 대기 시간이 짧고 처리량이 높은 네트워크 인터페이스를 제공합니다.


| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1,440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1,440 | 4 | 64 | 4 |

1 GPU = K80 카드의 절반.

*RDMA 지원

## <a name="ncv2-series"></a>NCv2 시리즈

NCv2 시리즈 VM은 [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU로 구동됩니다. 이러한 GPU는 NC 시리즈보다 2배를 초과하는 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. NC24rs v2 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 vCPU(코어) 할당량은 각 지역에서 처음에 0으로 설정됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](../articles/azure-supportability/resource-manager-core-quotas-request.md)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 32 | 8 |

한 개의 GPU = 한 개의 P100 카드

*RDMA 지원

## <a name="ncv3-series"></a>NCv3 시리즈

NCv3 시리즈 VM은 [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPU로 구동됩니다. 이러한 GPU는 NCv2 시리즈보다 1.5배의 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. NC24rs v3 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 vCPU(코어) 할당량은 각 지역에서 처음에 0으로 설정됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](../articles/azure-supportability/resource-manager-core-quotas-request.md)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 32 | 8 |

하나의 GPU = 하나의 V100 카드

*RDMA 지원

## <a name="nd-series"></a>ND 시리즈

ND 시리즈 가상 머신은 AI 및 심층 학습 워크로드용으로 설계된 GPU 제품군에 새로 추가됩니다. 이 가상 머신은 교육 및 유추에 우수한 성능을 제공합니다. ND 인스턴스는 [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU에 의해 구동됩니다. 이 인스턴스는 단정밀도 부동 소수점 작업, Microsoft Cognitive Toolkit, TensorFlow, Caffe 및 기타 프레임워크를 활용하는 AI 워크로드에 우수한 성능을 제공합니다. ND 시리즈는 훨씬 큰 GPU 메모리 크기(24GB)도 제공하므로 더 큰 규모의 신경망 모델에도 적합합니다. NC 시리즈와 마찬가지로 ND 시리즈는 RDMA를 통한 대기 시간이 낮고 처리량이 높은 보조 네트워크 및 InfiniBand 연결과 함께 구성할 수 있으므로 여러 GPU를 사용한 대규모 교육 작업을 실행할 수 있습니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 지역당 vCPU(코어) 할당량은 처음에 0으로 설정됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](../articles/azure-supportability/resource-manager-core-quotas-request.md)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 32 | 8 |

한 개의 GPU = 한 개의 P40 카드

*RDMA 지원

## <a name="nv-series"></a>NV 시리즈

NV 시리즈 가상 머신은 고객이 해당 데이터 또는 시뮬레이션을 시각화할 수 있는 데스크톱 가속화 응용 프로그램 및 가상 데스크톱용 [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 NVIDIA GRID 기술로 구동됩니다. 사용자는 NV 인스턴스에서 그래픽 집약적인 워크플로를 시각화하여 뛰어난 그래픽 기능을 가져오고 인코딩 및 렌더링 등의 단정밀도 작업을 추가적으로 실행할 수 있습니다. 

NV 인스턴스의 각 GPU에는 GRID 라이선스가 함께 제공됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 응용 프로그램 시나리오를 위해 VM에 연결할 수 있습니다.

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | 최대 데이터 디스크 수 | 최대 NIC 수 | 가상 워크스테이션 | 가상 응용 프로그램 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1,440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = M60 카드 절반.

 
