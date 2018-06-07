---
title: Linux용 Azure N 시리즈 드라이버 설치 | Microsoft Docs
description: Azure에서 Linux를 실행하는 N 시리즈 VM의 NVIDIA GPU 드라이버를 설정하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 20bcb822ff39b9587a479fd6cc43b7daa9b83627
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190682"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Linux를 실행하는 N 시리즈 VM의 NVIDIA GPU 드라이버 설치

Linux를 실행하는 Azure N 시리즈 VM의 GPU 기능을 최대한 활용하려면 NVIDIA 그래픽 드라이버를 설치해야 합니다. 이 문서에서는 N 시리즈 VM을 배포한 후의 드라이버 설치 단계를 제공합니다. [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 대한 드라이버 설치 정보도 사용할 수 있습니다.

N 시리즈 VM 사양, 저장소 용량 및 디스크 세부 정보는 [GPU Linux VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-ncv3-and-nd-series-vms"></a>NC, NCv2, NCv3 및 ND VM용 CUDA 드라이버 설치

N 시리즈 VM의 NVIDIA CUDA 도구 키트에서 CUDA 드라이버를 설치하는 단계는 다음과 같습니다. 


경우에 따라 C 및 C++ 개발자는 GPU 가속 응용 프로그램을 빌드하기 위해 전체 도구 키트를 설치할 수도 있습니다. 자세한 내용은 [CUDA 설치 가이드](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)를 참조하세요.

CUDA 드라이버를 설치하려면 각 VM에 SSH 연결을 설정합니다. 시스템에 CUDA 호환 GPU가 있는지 확인하려면 다음 명령을 실행합니다.

```bash
lspci | grep -i NVIDIA
```
다음 예제와 비슷한 출력이 표시됩니다(NVIDIA Tesla K80 카드 표시).

![lspci 명령 출력](./media/n-series-driver-setup/lspci.png)

그런 다음 배포 관련 특정 설치 명령을 실행합니다.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. CUDA 드라이버를 다운로드하여 설치합니다.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  설치에는 몇 분 정도 걸릴 수 있습니다.

2. 필요에 따라 전체 CUDA 도구 키트를 설치하려면 다음을 입력합니다.

  ```bash
  sudo apt-get install cuda
  ```

3. VM 다시 부팅하고 계속해서 설치를 확인합니다.

#### <a name="cuda-driver-updates"></a>CUDA 드라이버 업데이트

배포 후에 CUDA 드라이버를 정기적으로 업데이트하는 것이 좋습니다.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS 또는 Red Hat Enterprise Linux 7.3/7.4

1. 커널을 업데이트합니다.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

  ```bash
  wget https://aka.ms/lis
 
  tar xvzf lis
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. VM에 다시 연결하고 다음 명령을 사용하여 설치를 계속합니다.

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  설치에는 몇 분 정도 걸릴 수 있습니다. 

4. 필요에 따라 전체 CUDA 도구 키트를 설치하려면 다음을 입력합니다.

  ```bash
  sudo yum install cuda
  ```

5. VM 다시 부팅하고 계속해서 설치를 확인합니다.

### <a name="verify-driver-installation"></a>드라이버 설치 확인

GPU 장치 상태를 쿼리하려면 VM에 대해 SSH를 실행하고 드라이버와 설치된 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 명령줄 유틸리티를 실행합니다. 

드라이버가 설치된 경우 다음과 유사한 출력이 표시됩니다. 현재 VM에서 GPU 워크로드를 실행 중이지 않으면 **GPU-Util**에 0%가 표시됩니다. 드라이버 버전 및 GPU 세부 정보는 표시된 것과 다를 수 있습니다.

![NVIDIA 장치 상태](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA 네트워크 연결

동일한 가용성 집합 또는 VM 확장 집합에 배포된 NC24r처럼 RDMA 지원 N 시리즈 VM에서 RDMA 네트워크 연결을 사용할 수 있습니다. RDMA 네트워크는 Intel MPI 5.x 이상 버전을 사용하여 실행되는 응용 프로그램에 대한 MPI(Message Passing Interface) 트래픽을 지원합니다. 추가 요구 사항은 다음과 같습니다.

### <a name="distributions"></a>배포

N 시리즈 VM에서 RDMA 연결을 지원하는 Azure Marketplace의 이미지 중 하나에서 RDMA 지원 N 시리즈 VM을 배포합니다.
  
* **Ubuntu 16.04 LTS** - Intel MPI를 다운로드하도록 VM에서 RDMA 드라이버를 구성하고 Intel에 등록합니다.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS 기반 7.4 HPC** - RDMA 드라이버 및 Intel MPI 5.1은 VM에 설치됩니다.

## <a name="install-grid-drivers-for-nv-series-vms"></a>NV 시리즈 VM용 GRID 드라이버 설치

NVIDIA GRID 드라이버를 NV 시리즈 VM에 설치하려면 각 VM에 대한 SSH 연결을 확인하고 Linux 배포에 필요한 단계를 수행합니다. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. `lspci` 명령을 실행합니다. NVIDIA M60 카드가 PCI 장치로 표시되는지 확인합니다.

2. 업데이트를 설치합니다.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. NVIDIA 드라이버와 호환되지 않는 Nouveau 커널 드라이버를 사용하지 않도록 설정합니다. (NV VM에서 NVIDIA 드라이버만 사용합니다.) 이를 수행하려면 다음 콘텐츠가 포함된 `nouveau.conf`라고 하는 `/etc/modprobe.d `에 파일을 만듭니다.

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. VM을 다시 부팅하고 다시 연결합니다. X 서버를 종료합니다.

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. GRID 드라이버를 다운로드하여 설치합니다.

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 

6. X 구성 파일을 업데이트할 nvidia-xconfig 유틸리티를 실행할 것인지 여부를 묻는 메시지가 표시되면 **예**를 선택합니다.

7. 설치가 완료되면 /etc/nvidia/gridd.conf.template을 /etc/nvidia/ 위치의 새 파일 gridd.conf에 복사합니다.

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. 다음 줄을 `/etc/nvidia/gridd.conf`에 추가합니다.
 
  ```
  IgnoreSP=TRUE
  ```
9. VM 다시 부팅하고 계속해서 설치를 확인합니다.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS 또는 Red Hat Enterprise Linux 

1. 커널 및 DKMS를 업데이트합니다.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. NVIDIA 드라이버와 호환되지 않는 Nouveau 커널 드라이버를 사용하지 않도록 설정합니다. (NV VM에서 NVIDIA 드라이버만 사용합니다.) 이를 수행하려면 다음 콘텐츠가 포함된 `nouveau.conf`라고 하는 `/etc/modprobe.d `에 파일을 만듭니다.

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. VM을 다시 부팅하고 다시 연결한 후 [Hyper-V 및 Azure에 대한 최신 Linux 통합 서비스](https://www.microsoft.com/download/details.aspx?id=55106)를 설치합니다.
 
  ```bash
  wget https://aka.ms/lis

  tar xvzf lis

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. VM에 다시 연결하고 `lspci` 명령을 실행합니다. NVIDIA M60 카드가 PCI 장치로 표시되는지 확인합니다.
 
5. GRID 드라이버를 다운로드하여 설치합니다.

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 
6. X 구성 파일을 업데이트할 nvidia-xconfig 유틸리티를 실행할 것인지 여부를 묻는 메시지가 표시되면 **예**를 선택합니다.

7. 설치가 완료되면 /etc/nvidia/gridd.conf.template을 /etc/nvidia/ 위치의 새 파일 gridd.conf에 복사합니다.
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. 다음 줄을 `/etc/nvidia/gridd.conf`에 추가합니다.
 
  ```
  IgnoreSP=TRUE
  ```
9. VM 다시 부팅하고 계속해서 설치를 확인합니다.

### <a name="verify-driver-installation"></a>드라이버 설치 확인


GPU 장치 상태를 쿼리하려면 VM에 대해 SSH를 실행하고 드라이버와 설치된 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 명령줄 유틸리티를 실행합니다. 

드라이버가 설치된 경우 다음과 유사한 출력이 표시됩니다. 현재 VM에서 GPU 워크로드를 실행 중이지 않으면 **GPU-Util**에 0%가 표시됩니다. 드라이버 버전 및 GPU 세부 정보는 표시된 것과 다를 수 있습니다.

![NVIDIA 장치 상태](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 서버
NV VM에 대한 원격 연결을 위해 X11 서버가 필요한 경우 그래픽의 하드웨어 가속화가 가능하기 때문에 [x11vnc](http://www.karlrunge.com/x11vnc/)가 권장됩니다. M60 장치의 BusID는 xconfig 파일에 수동으로 추가해야 합니다(Ubuntu 16.04 LTS의 `etc/X11/xorg.conf`, CentOS 7.3의 `/etc/X11/XF86config` 또는 Red Hat Enterprise Server 7.3). 다음과 유사한 `"Device"` 섹션을 추가합니다.
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
또한 `"Screen"` 섹션을 업데이트하여 이 장치를 사용합니다.
 
실행하여 소수점 BusID를 찾을 수 있음

```bash
echo $((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))
```
 
BusID는 VM이 다시 할당되거나 다시 부팅될 때를 변경할 수 있습니다. 따라서 스크립트를 만들어서 VM이 다시 부팅될 때 X11 구성에서 BusID를 업데이트할 수도 있습니다. 예를 들어, 다음과 같은 콘텐츠의 `busidupdate.sh`(또는 사용자가 선택한 다른 이름)라는 스크립트를 만듭니다.

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

그런 다음, 부팅 시 루트로 스크립트가 호출되도록 `/etc/rc.d/rc3.d`에서 업데이트 스크립트에 대한 항목을 만듭니다.

## <a name="troubleshooting"></a>문제 해결

* `nvidia-smi`를 사용하여 지속성 모드를 설정할 수 있으므로 카드를 쿼리해야 할 때 명령 출력이 더 빠릅니다. 지속성 모드를 설정하려면 `nvidia-smi -pm 1`을 실행합니다. VM을 다시 시작하면 모드 설정이 사라집니다. 모드 설정은 시작할 때 실행되도록 항상 스크립팅할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 설치한 NVIDIA 드라이버와 함께 Linux VM 이미지를 캡처하려면 [Linux 가상 머신을 일반화하고 캡처하는 방법](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
