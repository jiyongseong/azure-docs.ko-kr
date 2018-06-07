---
title: MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정 | Microsoft Docs
description: Azure RDMA 네트워크를 사용하여 MPI 앱을 실행하기 위해 H16r, H16mr, A8 또는 A9 크기의 VM으로 이루어진 Linux 클러스터를 만듭니다.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: d53305aae3b12c0de983dced85a9626cf98c6309
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정
Azure에서 [고성능 계산 VM 크기](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용하여 MPI(Message Passing Interface) 응용 프로그램을 병렬로 실행하도록 Linux RDMA 클러스터를 설정하는 방법을 알아봅니다. 이 문서는 클러스터에서 Intel MPI를 실행하도록 Linux HPC 이미지를 준비하기 위한 단계를 제공합니다. 준비가 끝나면 이 이미지와 RDMA 지원 Azure VM 크기(현재 H16r, H16mr, A8 또는 A9) 중 하나를 사용하여 VM 클러스터를 배포합니다. RDMA(원격 직접 메모리 액세스) 기술을 기반으로 하는 짧은 대기 시간, 높은 처리량의 네트워크에서 효율적으로 통신하는 MPI 응용 프로그램을 실행하려면 클러스터를 사용합니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 별도의 두 가지 배포 모델, 즉 [Azure Resource Manager](../../../resource-manager-deployment-model.md)와 클래식 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

## <a name="cluster-deployment-options"></a>클러스터 배포 옵션
다음은 작업 스케줄러의 유무에 관계 없이 Linux RDMA 클러스터를 만드는 데 사용할 수 있는 방법입니다.

* **Azure CLI 스크립트** - 이 문서의 뒷부분에서 볼 수 있듯이 [Azure CLI(명령줄 인터페이스)](../../../cli-install-nodejs.md)를 사용하여 RDMA 지원 VM의 클러스터 배포를 스크립팅합니다. 서비스 관리 모드의 CLI는 클래식 배포 모델에서 계산 노드를 순차적으로 배포하므로 많은 계산 노드를 배포하는 경우 몇 분 정도 걸릴 수 있습니다. 클래식 배포 모델을 사용하는 경우 RDMA 네트워크 연결을 사용하도록 설정하려면 동일한 클라우드 서비스의 VM을 배포합니다.
* **Azure Resource Manager 템플릿** - Resource Manager 배포 모델을 사용하여 RDMA 네트워크에 연결하는 RDMA 지원 VM 클러스터를 배포할 수도 있습니다. [사용자 고유의 템플릿을 만들거나](../../../resource-group-authoring-templates.md) [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)에서 Microsoft 또는 커뮤니티가 참여한 템플릿을 확인하여 원하는 솔루션을 배포할 수 있습니다. 리소스 관리자 템플릿을 사용하면 빠르고 안정적으로 Linux 클러스터를 배포할 수 있습니다. Resource Manager 배포 모델을 사용하는 경우 RDMA 네트워크 연결을 사용하도록 설정하려면 동일한 가용성 집합의 VM을 배포합니다.
* **HPC Pack** - Azure에서 Microsoft HPC 팩 클러스터를 만들고, 지원되는 Linux 배포판을 실행하는 RDMA 지원 계산 노드를 추가하여 RDMA 네트워크에 액세스합니다. 자세한 내용은 [Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작](hpcpack-cluster.md)을 참조하세요.

## <a name="sample-deployment-steps-in-the-classic-model"></a>클래식 모델의 샘플 배포 단계
다음 단계에서는 Azure CLI를 사용하여 Azure Marketplace에서 SLES(SUSE Linux Enterprise Server) 12 SP1 HPC VM을 배포하고, 사용자 지정하고, 사용자 지정 VM 이미지를 만드는 방법을 보여 줍니다. 그런 다음 이 이미지를 사용하여 RDMA 지원 VM 클러스터 배포를 스크립팅할 수 있습니다.

> [!TIP]
> 비슷한 단계를 사용하여 Azure Marketplace에서 CentOS 기반 HPC 이미지에 따라 RDMA 호환 VM의 클러스터를 배포합니다. 일부 단계가 약간 다를 수 있습니다. 
>
>

### <a name="prerequisites"></a>필수 조건
* **클라이언트 컴퓨터** - Azure와 통신할 Mac, Linux 또는 Windows 클라이언트 컴퓨터가 필요합니다. 이러한 단계는 Linux 클라이언트를 사용하는 것을 가정합니다.
* **Azure 구독** - 구독이 없는 경우 몇 분 내에 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다. 대규모 클러스터의 경우, 종량제 구독이나 다른 구매 옵션을 고려하세요.
* **VM 크기 가용성** - H16r, H16mr, A8 및 A9 인스턴스 크기가 RDMA를 지원할 수 있습니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 에서 Azure 지역의 가용성을 확인하세요.
* **코어 할당량** - 계산 집약적 VM 클러스터를 배포하려면 코어 할당량을 늘려야 할 수도 있습니다. 예를 들어 이 문서에 설명된 것처럼 8대의 A9 VM을 배포하려는 경우 적어도 128개의 코어가 필요합니다. 구독에 따라서도 H 시리즈를 포함하여 특정 VM 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../../../azure-supportability/how-to-create-azure-support-request.md) 합니다.
* **Azure CLI** - Azure CLI를 [설치](../../../cli-install-nodejs.md)하고 클라이언트 컴퓨터에서 [Azure 구독에 연결](/cli/azure/authenticate-azure-cli)합니다.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>SLES 12 SP1 HPC VM 프로비전
Azure CLI에서 Azure에 로그인한 후 `azure config list`를 실행하여 출력에 서비스 관리 모드가 표시되는지 확인합니다. 그렇지 않으면 다음 명령을 실행하여 모드를 설정합니다.

    azure config mode asm


다음을 입력하여 사용 권한이 부여된 모든 구독을 나열합니다.

    azure account list

현재 활성 구독은 `Current`가 `true`로 설정되어 있습니다. 클러스터를 만드는 데 사용하려는 구독이 아닌 경우 다음과 같이 해당 구독 ID를 활성 구독으로 설정합니다.

    azure account set <subscription-Id>

Azure에서 공개적으로 사용할 수 있는 SLES 12 SP1 HPC 이미지를 보려면 셸 환경에서 **grep**를 지원한다고 가정하고 다음과 같은 명령을 실행합니다.

    azure vm image list | grep "suse.*hpc"

다음과 같은 명령을 실행하여 SLES 12 SP1 HPC 이미지로 RDMA 지원 VM을 프로비전합니다.

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

위치:

* 해당 크기(이 예제의 경우 A9)는 RDMA 지원 VM 크기 중 하나입니다.
* 외부 SSH 포트 번호(이 예제의 경우 SSH 기본인 22)는 유효한 모든 포트 번호입니다. 내부 SSH 포트 번호는 22로 설정됩니다.
* 새 클라우드 서비스는 해당 위치로 지정된 Azure 지역에 만들어집니다. 선택한 VM 크기를 사용할 수 있는 위치를 지정합니다.
* SUSE 우선 순위 지원(추가 요금 발생)의 경우 SLES 12 SP1 이미지 이름은 현재 다음 두 옵션 중 하나일 수 있습니다. 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>VM 사용자 지정
VM 프로비전이 완료되면 VM의 외부 IP 주소(또는 DNS 이름) 및 구성한 외부 포트 번호를 사용하여 VM에 SSH로 연결한 후 VM을 사용자 지정합니다. 연결에 대한 자세한 내용은 [Linux를 실행하는 가상 머신에 로그온하는 방법](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 루트 액세스가 단계를 완료하기 위해 필요하지 않은 경우 VM에서 구성된 사용자로 명령을 수행합니다.

> [!IMPORTANT]
> Microsoft Azure에서는 Linux VM에 대한 루트 액세스를 제공하지 않습니다. VM에 사용자로 연결할 때 관리 액세스 권한을 얻으려면 `sudo`를 사용하여 명령을 실행합니다.
>
>

* **업데이트** - zypper를 사용하여 업데이트를 설치합니다. NFS 유틸리티를 설치하려고 할 수도 있습니다.

  > [!IMPORTANT]
  > SLES 12 SP1 HPC VM에서 Linux RDMA 드라이버에 문제가 발생할 수 있으므로 커널 업데이트를 적용하지 않는 것이 좋습니다.
  >
  >
* **Intel MPI** - 다음 명령을 실행하여 SLES 12 SP1 HPC VM에서 Intel MPI 설치를 완료합니다.

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **메모리 잠금** - MPI 코드로 RDMA에 사용 가능한 메모리를 잠그려면 /etc/security/limits.conf 파일에서 다음 설정을 추가하거나 변경합니다. 이 파일을 편집하려면 루트 액세스가 필요합니다.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > 테스트를 위해 memlock을 무제한으로 설정할 수도 있습니다. 예: `<User or group name>    hard    memlock unlimited` 자세한 내용은 [가장 잘 알려진 잠금 메모리 크기 설정 방법](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size)(영문)을 참조하세요.
  >
  >
* **SLES VM용 SSH 키** - SSH 키를 생성하여 MPI 작업을 실행할 때 SLES 클러스터의 계산 노드 간에 사용자 계정에 대한 신뢰를 설정합니다. CentOS 기반 HPC VM을 배포한 경우 이 단계를 수행하지 않습니다. 이미지를 캡처하고 클러스터를 배포한 후에 클러스터 노드 간에 암호 없는 SSH 트러스트를 설정하려면 이 문서의 뒷부분에 나오는 지침을 참조하세요.

    SSH 키를 만들려면 다음 명령을 실행합니다. 입력하라는 메시지가 표시되면 **Enter** 키를 선택하여 암호를 설정하지 않고 기본 위치에 키를 생성합니다.

        ssh-keygen

    알려진 공용 키에 대한 authorized_keys 파일에 공개키를 추가합니다.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    ~/.ssh 디렉터리에서 구성 파일을 편집하거나 만듭니다. Azure에서 사용할 개인 네트워크의 IP 주소 범위를 제공합니다(이 예에서는 10.32.0.0/16).

        host 10.32.0.*
        StrictHostKeyChecking no

    또는 다음과 같이 클러스터의 각 VM에 대한 개인 네트워크 IP 주소를 나열합니다.

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > `StrictHostKeyChecking no`를 구성하면 특정 IP 주소 또는 범위를 지정하지 않은 경우 잠재적인 보안 위험이 발생할 수 있습니다.
  >
  >
* **응용 프로그램** - 이미지를 캡처하기 전에 필요한 모든 응용 프로그램을 설치하거나 다른 사용자 지정을 수행합니다.

### <a name="capture-the-image"></a>이미지 캡처
이미지를 캡처하려면 먼저 Linux VM에서 다음 명령을 실행합니다. 이 명령은 VM을 프로비전 취소하지만 설정한 사용자 계정 및 SSH 키를 유지 관리합니다.

```
sudo waagent -deprovision
```

클라이언트 컴퓨터에서 다음 Azure CLI 명령을 실행하여 이미지를 캡처합니다. 자세한 내용은 [클래식 Linux 가상 머신을 이미지로 캡처하는 방법](capture-image-classic.md)을 참조하세요.  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

이러한 명령을 실행하면 VM 이미지가 캡처되고 VM이 삭제됩니다. 이제 사용자 지정 이미지를 사용하여 클러스터를 배포할 수 있습니다.

### <a name="deploy-a-cluster-with-the-image"></a>이미지를 사용하여 클러스터 배포
다음 Bash 스크립트를 환경에 적절한 값으로 수정하고 클라이언트 컴퓨터에서 실행합니다. Azure는 클래식 배포 모델에서 VM을 순차적으로 배포하기 때문에 이 스크립트에 제안된 8개의 A9 VM을 배포하는 데 몇 분 정도 걸립니다.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>CentOS HPC 클러스터에 대한 고려 사항
HPC용 SLES 12 대신 Azure Marketplace의 CentOS 기반 HPC 이미지 중 하나를 기준으로 클러스터를 설정하려는 경우 이전 섹션의 일반 단계를 따릅니다. VM을 프로비전하고 구성하는 경우 다음과 같은 차이점을 알아둡니다.

- Intel MPI가 CentOS 기반 HPC 이미지에서 프로비전된 VM에 이미 설치되어 있습니다.
- 잠금 메모리 설정이 VM의 /etc/security/limits.conf 파일에 이미 추가되어 있습니다.
- 캡처를 위해 프로비전하는 VM에 대해서는 SSH 키를 생성하지 않습니다. 대신 클러스터를 배포한 후에 사용자 기반 인증을 설정하는 것이 좋습니다. 자세한 내용은 다음 섹션을 참조하세요.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>클러스터에 대해 암호 없는 SSH 트러스트 설정
CentOS 기반 HPC 클러스터에서 계산 노드 간에 트러스트를 설정하는 방법에는 호스트 기반 인증 및 사용자 기반 인증의 두 가지가 있습니다. 호스트 기반 인증은 이 기사의 범위를 벗어나고 일반적으로 배포하는 동안 확장 스크립트를 통해 수행되어야 합니다. 사용자 기반 인증은 배포 후에 트러스트를 설정하는 데 유용하며 클러스터의 계산 노드 간에 SSH 키를 생성하고 공유해야 합니다. 이 메서드는 일반적으로 암호 없는 SSH 로그인이라고 알려졌으며 MPI 작업을 실행하는 경우에 필요합니다.

커뮤니티에서 제공하는 샘플 스크립트는 [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) 에서 사용할 수 있으며 CentOS 기반 HPC 클러스터에서 쉬운 사용자 인증이 가능합니다. 다음 단계를 사용하여 이 스크립트를 다운로드하고 사용합니다. 이 스크립트를 수정하거나 다른 메서드를 사용하여 클러스터 계산 노드 간에 암호 없는 SSH 인증을 설정할 수 있습니다.

    wget https://raw.githubusercontent.com/tanewill/utils/master/user_authentication.sh

스크립트를 실행하려면 서브넷 IP 주소에 대한 접두사를 확인해야 합니다. 클러스터 노드 중 하나에서 다음 명령을 실행하여 접두사를 가져옵니다. 출력은 10.1.3.5와 비슷해야 하며 접두사는 10.1.3 부분입니다.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

이제 계산 노드의 일반 사용자 이름, 계산 노드의 해당 사용자에 대한 일반적인 암호 및 이전 명령에서 반환된 서브넷 접두사 등 세 개의 매개 변수를 사용하여 스크립트를 실행합니다.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

이 스크립트는 다음을 수행합니다.

* .ssh라는 호스트 노드에 디렉터리를 만듭니다. 이것은 암호 없는 로그인에 필요합니다.
* 클러스터의 노드에서 로그인할 수 있도록 암호 없는 로그인을 지시하는 .ssh 디렉터리에 구성 파일을 만듭니다.
* 클러스터의 모든 노드에 대한 노드 이름 및 노드 IP 주소를 포함하는 파일을 만듭니다. 이러한 파일은 나중에 참조할 수 있도록 스크립트를 실행한 후에도 남아 있습니다.
* 호스트 노드를 포함하여 각 클러스터 노드에 대한 개인 및 공개 키 쌍을 만들고 authorized_keys 파일에 항목을 만듭니다.

> [!WARNING]
> 이 스크립트를 실행하면 잠재적인 보안 위협이 생길 수 있습니다. ~/.ssh의 공개 키 정보가 분산되지 않도록 확인하세요.
>
>

## <a name="configure-intel-mpi"></a>Intel MPI 구성
Azure Linux RDMA에서 MPI 응용 프로그램을 실행하려면 Intel MPI에 특정한 특정 환경 변수를 구성해야 합니다. 응용 프로그램을 실행하는 데 필요한 변수를 구성하는 샘플 Bash 스크립트는 다음과 같습니다. Intel MPI 설치를 위해 필요에 따라 경로를 mpivars.sh로 변경합니다.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

호스트 파일의 형식은 다음과 같습니다. 클러스터의 각 노드에 대해 한 줄을 추가합니다. 앞에서 정의한 가상 네트워크에서 DNS 이름이 아니라 개인 IP 주소를 지정합니다. 예를 들어 IP 주소가 10.32.0.1 및 10.32.0.2인 두 호스트의 경우 파일에 다음이 포함됩니다.

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>기본 2 노드 클러스터에서 MPI 실행
아직 그렇게 하지 않은 경우 먼저 Intel MPI에 대한 환경을 설정합니다.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>MPI 명령 실행
계산 노드 중 하나에서 MPI 명령을 실행하여 MPI가 제대로 설치되어 있고 적어도 두 개의 계산 노드 간에 통신할 수 있는지 확인합니다. 다음 **mpirun** 명령은 두 개의 노드에서 **hostname** 명령을 실행합니다.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
출력은 `-hosts`에 대한 입력으로 전달한 모든 노드의 이름을 나열해야 합니다. 예를 들어 두 노드가 포함된 **mpirun** 명령은 다음과 같은 출력을 반환합니다.

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>MPI 벤치마크 실행
다음 Intel MPI 명령은 pingpong 벤치마크를 실행하여 클러스터 구성 및 RDMA 네트워크 연결을 확인합니다.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

두 개의 노드로 작동하는 클러스터에서 다음과 같은 출력이 표시되어야 합니다. Azure의 RDMA 네트워크에서 최대 512바이트의 메시지 크기에 대해 3 마이크로초 이하의 대기 시간이 예상됩니다.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>다음 단계
* Linux 클러스터에 Linux MPI 응용 프로그램을 배포하고 실행합니다.
* Intel MPI에 대한 지침은 [Intel MPI Library 설명서](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) 를 참조하세요.
* CentOS 기반 HPC 이미지를 사용하여 Intel Lustre 클러스터를 만들기 위해 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) 을 사용해 봅니다. 자세한 내용은 [Microsoft Azure에서 Intel Cloud Edition for Lustre 배포](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/)(영문)를 참조하세요.
