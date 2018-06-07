---
title: Windows 용 Azure N 시리즈 드라이버 설정 | Microsoft Docs
description: Azure에서 Windows Server 또는 Windows를 실행하는 N 시리즈 VM용 NVIDIA GPU 드라이버를 설정하는 방법입니다.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: efa8c2603d6ff4493656cda41306a5dad46bc5f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows"></a>Windows가 실행되는 N 시리즈 VM용 GPU 드라이버 설정 
지원되는 Windows Server 또는 Windows 버전을 실행하는 Azure N 시리즈 VM의 GPU 기능을 활용하려면 NVIDIA 그래픽 드라이버를 설치해야 합니다. 이 문서에서는 N 시리즈 VM을 배포한 후의 드라이버 설치 단계를 제공합니다. [Linux VM](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대한 드라이버 설치 정보도 사용할 수 있습니다.

기본 사양, 저장소 용량 및 디스크 세부 정보는 [GPU Windows VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>드라이버 설치

1. 각 N 시리즈 VM에 원격 데스크톱으로 연결합니다.

2. Windows 운영 체제에 지원되는 드라이버를 다운로드, 추출 및 설치합니다.

Azure NV VM에서는 드라이버 설치 후 다시 시작해야 합니다. NC Vm에서는 다시 시작 필요하지 않습니다.

## <a name="verify-driver-installation"></a>드라이버 설치 확인

장치 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NC VM에서 Tesla K80 카드의 성공적인 구성을 보여 줍니다.

![GPU 드라이버 속성](./media/n-series-driver-setup/GPU_driver_properties.png)

GPU 장치 상태를 쿼리하려면 드라이버와 설치된 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 명령줄 유틸리티를 실행합니다.

1. 명령 프롬프트를 열고 **C:\Program Files\NVIDIA Corporation\NVSMI** 디렉터리로 변경합니다.

2. `nvidia-smi`을 실행합니다. 드라이버가 설치된 경우 다음과 유사한 출력이 표시됩니다. 현재 VM에서 GPU 워크로드를 실행 중이지 않으면 **GPU-Util**에 **0%**가 표시됩니다. 드라이버 버전 및 GPU 세부 정보는 표시된 것과 다를 수 있습니다.

![NVIDIA 장치 상태](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA 네트워크 연결

동일한 가용성 집합 또는 VM 확장 집합에 배포된 NC24r처럼 RDMA 지원 N 시리즈 VM에서 RDMA 네트워크 연결을 사용할 수 있습니다. RDMA 연결에 필요한 Windows 네트워크 장치 드라이버를 설치하는 HpcVmDrivers 확장을 추가해야 합니다. RDMA 지원 N 시리즈에 VM 확장을 추가하려면 Azure Resource Manager용 [Azure PowerShell](/powershell/azure/overview) cmdlet을 사용합니다.

미국 서부 하위 지역에서 이름이 myVM인 기존 RDMA 지원 VM에 최신 버전 1.1 HpcVMDrivers 확장을 설치하려면
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  자세한 내용은 [Windows용 가상 머신 확장 및 기능](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

RDMA 네트워크는 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) 또는 Intel MPI 5.x를 사용하여 실행되는 응용 프로그램에 대한 MPI(Message Passing Interface) 트래픽을 지원합니다. 


## <a name="next-steps"></a>다음 단계

* NVIDIA Tesla GPU에 대한 GPU 가속 응용 프로그램을 빌드하는 개발자는 [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads)을 다운로드하여 설치할 수도 있습니다. 자세한 내용은 [CUDA 설치 가이드](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)를 참조하세요.


