---
title: MPI 응용 프로그램을 실행하도록 Windows RDMA 클러스터 설정 | Microsoft Docs
description: Azure RDMA 네트워크를 사용하여 MPI 앱을 실행하기 위해 크기가 H16r, H16mr, A8 또는 A9인 VM으로 Windows HPC Pack 클러스터를 만드는 방법을 알아보세요.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 437c475735ec3823de51c5f9e996a5303fe9cfa7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정
Azure에서 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 및 [RDMA 가능 HPC VM 크기](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances)를 사용하여 MPI(Message Passing Interface) 응용 프로그램을 병렬로 실행하도록 Windows RDMA 클러스터를 설정합니다. HPC 팩 클러스터에서 RDMA 지원, Windows Server 기반 노드를 설정하는 경우 MPI 응용 프로그램은 Azure에서 RDMA(원격 직접 메모리 액세스) 기술을 기반으로 하는 낮은 대기 시간 및 높은 처리량의 네트워크에서 효율적으로 통신합니다.

Azure RDMA 네트워크에 액세스하는 Linux VM에서 MPI 워크로드를 실행하려는 경우 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](../../linux/classic/rdma-cluster.md)을 참조하세요.

## <a name="hpc-pack-cluster-deployment-options"></a>HPC 팩 클러스터 배포 옵션
Microsoft HPC Pack은 Windows 또는 Linux HPC 응용 프로그램을 실행하기 위해 온-프레미스 또는 Azure에서 HPC 클러스터를 만들 수 있도록 추가 비용 없이 제공되는 도구입니다. HPC Pack에 Windows(MS-MPI)용 메시지 전달 인터페이스의 Microsoft 구현에 대한 런타임 환경을 포함합니다. HPC Pack을 지원되는 Windows Server 운영 체제를 실행하는 RDMA 지원 인스턴스와 함께 사용하는 경우 Azure RDMA 네트워크에 액세스하는 Windows MPI 응용 프로그램을 효율적으로 실행할 수 있습니다. 

이 문서에서는 Microsoft HPC Pack 2012 R2를 사용하여 Windows RDMA 클러스터를 설정하기 위한 2가지 시나리오를 소개하고 자세한 지침을 제공하는 링크를 소개합니다. 

* 시나리오 1. 계산 집약적 작업자 역할 인스턴스 배포(PaaS)
* 시나리오 2. 계산 집약적 VM에 계산 노드 배포(IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>시나리오 1: 계산 집약적 작업자 역할 인스턴스 배포(PaaS)
기존 HPC 팩 클러스터에서 클라우드 서비스(PaaS)에서 실행 중인 Azure 작업자 역할 인스턴스(Azure 노드)에 추가 계산 리소스를 추가합니다. 이 기능은 HPC 팩에서 "Azure로 버스트"라고도 하며 작업자 역할 인스턴스에 다양한 크기를 지원합니다. Azure 노드를 추가할 때는 RDMA 지원 크기 중 하나를 지정합니다.

다음은 기존 (일반적으로 온-프레미스) 클러스터에서 RDMA 지원 Azure 인스턴스로 버스트하는 단계 및 고려 사항입니다. 유사한 절차를 사용하여 Azure VM에 배포된 HPC 팩 헤드 노드에 작업자 역할 인스턴스를 추가합니다.

> [!NOTE]
> HPC 팩을 사용하는 Azure로 버스트에 대한 자습서는 [HPC 팩을 사용하여 하이브리드 클러스터 설정](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)을 참조하세요. 아래 단계에서 특히 RDMA 지원 Azure 노드에 적용되는 고려 사항을 확인하세요.
> 
> 

![Azure로 버스트][burst]

### <a name="steps"></a>단계
1. **HPC 팩 2012 R2 헤드 노드 배포 및 구성**
   
    [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=49922)에서 HPC 팩 설치 패키지를 다운로드합니다. Azure 버스트 배포를 준비하기 위한 요구 사항 및 지침은 [Microsoft HPC Pack을 사용하여 Azure 작업자 인스턴스로 버스트](https://technet.microsoft.com/library/gg481749.aspx)를 참조하세요.
2. **Azure 구독에서 관리 인증서 구성**
   
    헤드 노드와 Azure 간 연결을 보호하기 위한 인증서를 구성합니다. 옵션 및 절차에 대한 자세한 내용은 [HPC 팩용 Azure 관리 인증서 구성 시나리오](http://technet.microsoft.com/library/gg481759.aspx)를 참조하세요. 테스트 배포의 경우 HPC 팩은 Azure 구독에 신속하게 업로드할 수 있는 기본 Microsoft HPC Azure 관리 인증서를 설치합니다.
3. **새 클라우드 서비스 및 저장소 계정 만들기**
   
    Azure Portal을 사용하여 배포를 위한 클라우드 서비스(클래식) 및 저장소 계정(클래식)을 만듭니다. 사용하려는 H 시리즈, A8 또는 A9 크기를 사용할 수 있는 지역에서 이러한 리소스를 만듭니다. [지역별 Azure 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

4. **Azure 노드 템플릿 만들기**
   
    HPC 클러스터 관리자에서 노드 템플릿 마법사를 사용합니다. 단계를 보려면 "Microsoft HPC 팩을 사용하여 Azure 노드를 배포하는 단계"에서 [Azure 노드 템플릿 만들기](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) 를 참조하세요.
   
    초기 테스트에서 템플릿에 수동 가용성 정책을 구성하는 것이 좋습니다.
5. **클러스터에 노드 추가**
   
    HPC 클러스터 관리자에서 노드 추가 마법사를 사용합니다. 자세한 내용은 참조 [Windows HPC 클러스터에 Azure 노드 추가](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)를 참조하세요.
   
    노드 크기를 지정하는 경우 RDMA 지원 인스턴스 크기 중 하나를 선택합니다.
   
   > [!NOTE]
   > 계산 집약적인 인스턴스를 사용하는 각 Azure로 버스트 배포에서 HPC 팩은 사용자가 지정하는 Azure 작업자 역할 인스턴스 이외에 최소 2개 이상의 RDMA 지원 인스턴스(예: A8)를 프록시 노드로 자동 배포합니다. 프록시 노드는 구독에 할당된 코어를 사용하고 Azure 작업자 역할 인스턴스와 함께 요금이 청구됩니다.
   > 
   > 
6. **노드를 시작(프로비전)하고 온라인 상태로 전환하여 작업 실행**
   
    노드를 선택하고 HPC 클러스터 관리자에서 **시작** 작업을 사용합니다. 프로비전 이 완료되면 노드를 선택하고 HPC 클러스터 관리자에서 **온라인 상태로 전환** 작업을 사용합니다. 노드에서 작업을 실행할 준비가 되었습니다.
7. **클러스터에 작업 제출**
   
   HPC 팩 작업 제출 도구를 사용하여 클러스터 작업을 실행합니다. [Microsoft HPC 팩: 작업 관리](http://technet.microsoft.com/library/jj899585.aspx)를 참조하세요.
8. **노드 중지(프로 비전 해제)**
   
   작업 실행을 마쳤으면 노드를 오프라인으로 전환하고 HPC 클러스터 관리자에서 **중지** 작업을 사용합니다.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>시나리오 2: 계산 집약적 VM에 계산 노드 배포(IaaS)
이 시나리오에서는 Azure 가상 네트워크의 VM에 HPC 팩 헤드 노드와 클러스터 계산 노드를 배포합니다. HPC Pack은 자동 배포 스크립트 및 Azure 빠른 시작 템플릿을 포함하여 다양한 [Azure VM의 배포 옵션](../../linux/hpcpack-cluster-options.md)을 제공합니다. 예를 들어, 다음 고려 사항 및 단계는 [HPC 팩 IaaS 배포 스크립트](hpcpack-cluster-powershell-script.md)를 사용하여 Azure에서 HPC Pack 2012 R2 클러스터의 배포를 자동화하는 방법을 안내합니다.

![Azure VM의 클러스터][iaas]

### <a name="steps"></a>단계
1. **클라이언트 컴퓨터에서 HPC 팩 IaaS 배포 스크립트를 실행하여 클러스터 헤드 노드 및 계산 노드 VM 만들기**
   
    [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 HPC 팩 IaaS 배포 스크립트 패키지를 다운로드합니다.
   
    클라이언트 컴퓨터를 준비하고 스크립트 구성 파일을 만들어 스크립트를 실행하려면 [HPC 팩 IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](hpcpack-cluster-powershell-script.md)를 참조하세요. 
   
    RDMA 가능 계산 노드 배포에 대한 고려 사항을 보려면 [고성능 계산 VM 크기](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances)를 참조하고 다음에 유의합니다.
   
   * **가상 네트워크**: 사용하려는 H 시리즈, A8 또는 A9 크기를 사용할 수 있는 지역에서 새 가상 네트워크를 지정합니다. [지역별 Azure 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

   * **Windows Server 운영 체제**: RDMA 연결을 지원하려면 계산 노드 VM에 대해 Windows Server 2012 R2와 같은 호환되는 Windows 서버 운영 체제를 지정합니다.
   * **클라우드 서비스**: 스크립트는 클래식 배포 모델을 사용하므로 클러스터 VM은 Azure 클라우드 서비스(구성 파일의 `ServiceName` 설정)를 사용하여 배포됩니다. 헤드 노드를 한 클라우드 서비스에 배포하고 계산 노드를 다른 클라우드 서비스에 배포하는 것이 좋습니다. 
   * **헤드 노드 크기**: 이 시나리오의 경우 헤드 노드를 위해 최소한 A4 크기(매우 큼)를 고려합니다.
   * **HpcVmDrivers 확장**: Windows Server 운영 체제로 크기가 A8 또는 A9인 계산 노드를 배포할 경우 배포 스크립트는 Azure VM 에이전트와 HpcVmDrivers 확장을 자동으로 설치합니다. HpcVmDrivers는 계산 노드 VM이 RDMA 네트워크에 연결할 수 있도록 이 VM에 드라이버를 설치합니다. RDMA 지원 H 시리즈 VM에서는 HpcVmDrivers 확장을 수동으로 설치해야 합니다. [고성능 계산 VM 크기](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
   * **클러스터 네트워크 구성**: 배포 스크립트는 토폴로지 5(엔터프라이즈 네트워크에 있는 모든 노드)에 HPC Pack 클러스터를 자동으로 설정합니다. 이 토폴로지는 VM에서의 모든 HPC Pack 클러스터 배포에 필요합니다. 나중에 클러스터 네트워크 토폴로지를 변경하지 마십시오.
1. **계산 노드를 온라인 상태로 전환하여 작업 실행**
   
    노드를 선택하고 HPC 클러스터 관리자에서 **온라인 상태로 전환** 작업을 사용합니다. 노드에서 작업을 실행할 준비가 되었습니다.
3. **클러스터에 작업 제출**
   
    작업을 제출하려면 헤드 노드에 연결하거나 온-프레미스 컴퓨터를 설정합니다. 자세한 내용은 [Azure에서 HPC 클러스터에 작업 제출](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
4. **노드를 오프라인 상태로 전환 및 중지(할당 취소)**
   
    작업 실행을 마쳤으면 HPC 클러스터 관리자에서 노드를 오프라인 상태로 전환합니다. 그런 다음 Azure 관리 도구를 사용하여 종료합니다.

## <a name="run-mpi-applications-on-the-cluster"></a>클러스터에서 MPI 응용 프로그램 실행
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>예: HPC 팩 클러스터에서 mpipingpong 실행
RDMA 지원 인스턴스의 HPC 팩 배포를 확인하려면 클러스터에서 HPC 팩 **mpipingpong** 명령을 실행합니다. **mpipingpong** 은 쌍으로 연결된 노드 사이에서 데이터 패킷을 반복적으로 전송하여 RDMA를 사용하는 응용 프로그램 네트워크의 대기 시간, 처리량 측정값 및 통계를 계산합니다. 이 예제에서는 클러스터 **mpiexec** 명령을 사용하여 MPI 작업(이 경우 **mpipingpong**)을 실행하는 일반적 패턴을 보여줍니다.

이 예제에서는 "Azure로 버스트" 구성에 Azure 노드를 추가했다고 가정합니다([시나리오 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article)). Azure VM의 클러스터에 HPC 팩을 배포한 경우 명령 구문을 수정하여 다른 노드 그룹을 지정하고 추가 변수를 설정하여 네트워크 트래픽이 RDMA 네트워크로 가도록 해야 합니다.

클러스터에서 mpipingpong을 실행하려면

1. 헤드 노드 또는 올바르게 구성된 클라이언트 컴퓨터에서 명령 프롬프트를 엽니다.
2. 4개 노드로 구성된 Azure 버스트 배포에서 쌍으로 연결된 노드 사이의 대기 시간을 추정하려면 다음 명령을 입력하여 작업을 제출하고 작은 패킷 크기와 많은 반복을 사용하여 mpipingpong을 실행합니다.
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    이 명령은 제출된 작업의 ID를 반환합니다.
   
    Azure VM에 배포된 HPC 팩 클러스터를 배포한 경우 단일 클라우드 서비스에 배포된 계산 노드 VM이 포함된 노드 그룹을 지정하고 **mpiexec** 명령을 다음과 같이 수정합니다.
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. 작업이 완료된 다음 출력(이 경우 작업의 작업 1 출력)을 보려면 다음과 같이 입력합니다.
   
    ```Command
    task view <JobID>.1
    ```
   
    여기서 &lt;*JobID*&gt;는 제출된 작업의 ID입니다.
   
    출력에는 다음과 유사한 대기 시간 결과가 포함됩니다.
   
    ![핑퐁 대기 시간][pingpong1]
4. 쌍으로 연결된 Azure 버스트 노드 사이의 처리량을 추정하려면 다음 명령을 입력하여 작업을 제출하고 작은 패킷 크기와 적은 반복을 사용하여 **mpipingpong**을 실행합니다.
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    이 명령은 제출된 작업의 ID를 반환합니다.
   
    Azure VM에 배포된 HPC 팩 클러스터에서 2단계의 설명과 같이 명령을 수정합니다.
5. 작업이 완료된 다음 출력(이 경우 작업의 작업 1 출력)을 보려면 다음과 같이 입력합니다.
   
    ```Command
    task view <JobID>.1
    ```
   
   출력에는 다음과 유사한 처리량 결과가 포함됩니다.
   
   ![핑퐁 처리량][pingpong2]

### <a name="mpi-application-considerations"></a>MPI 응용 프로그램 고려 사항
다음은 Azure에서 HPC Pack을 사용하여 MPI 응용 프로그램을 실행하기 위한 고려 사항입니다. 일부는 Azure 노드 배포에만 적용됩니다(작업자 역할 인스턴스가 "Azure로 버스트" 구성에 추가됨).

* 클라우드 서비스의 작업자 역할 인스턴스는 Azure에서 미리 알리지 않고 정기적으로 다시 프로비전됩니다(예: 시스템 유지 관리에서 또는 인스턴스가 실패할 경우). MPI 작업을 실행하는 동안 인스턴스를 다시 프로비전한 경우 인스턴스의 데이터가 손실되고 인스턴스가 처음 배포될 당시의 상태로 돌아가며, 이로 인해 MPI 작업이 실패합니다. 단일 MPI 작업에 사용하는 노드가 많고 작업이 더 오래 실행될수록 작업이 실행되는 동안 인스턴스 중 하나가 다시 프로비전될 가능성이 높아집니다. 배포에서 단일 노드를 파일 서버로 지정하는 경우에도 이를 고려해야 합니다.
* Azure에서 MPI 작업을 실행하기 위해 RDMA 지원 인스턴스를 사용할 필요는 없습니다. HPC 팩에서 지원되는 모든 인스턴스 크기를 사용할 수 있습니다. 하지만 RDMA 지원 인스턴스는 대기 시간 및 노드에 연결되는 네트워크 대역폭에 민감하며 상대적으로 큰 규모의 MPI 작업을 실행하는 데 사용하는 것이 좋습니다. 대기 시간 및 대역폭에 민감한 MPI 작업을 실행하는 데 다른 크기를 사용하는 경우 단일 작업이 단 몇 개 노드에서만 실행되는 작은 작업을 실행하는 것이 좋습니다.
* Azure 인스턴스에 배포되는 응용 프로그램은 응용 프로그램과 관련된 라이선스 조건이 적용됩니다. 클라우드에서 실행하기 위해 라이선스나 기타 제한 사항에 대한 상용 응용 프로그램을 공급 업체에 확인합니다. 일부 공급 업체는 종량제 라이선스를 제공합니다.
* 온-프레미스 노드, 공유, 라이선스 서버에 액세스하려면 Azure 인스턴스에 추가 설정이 필요합니다. 예를 들어 Azure 노드가 온-프레미스 라이선스 서버에 액세스하도록 하려면 사이트 대 사이트 Azure 가상 네트워크를 구성할 수 있습니다.
* Azure 인스턴스에 MPI 응용 프로그램을 실행하려면 **hpcfwutil** 명령을 실행하여 인스턴스의 Windows 방화벽에 각 MPI 응용 프로그램을 등록합니다. 그러면 방화벽에서 동적으로 할당한 포트에서 MPI 통신이 이루어집니다.
  
  > [!NOTE]
  > Azure로 버스트 배포의 경우 방화벽 제외 명령을 구성하여 클러스터에 추가된 모든 새 Azure 노드에서 자동으로 실행할 수도 있습니다. **hpcfwutil** 명령을 실행하고 응용 프로그램이 작동하는지 확인한 다음 Azure 노드의 시작 스크립트에 명령을 추가합니다. 자세한 내용은 [Azure 노드에 시작 스크립트 사용](https://technet.microsoft.com/library/jj899632.aspx)을 참조하세요.
  > 
  > 
* HPC 팩은 CCP_MPI_NETMASK 클러스터 환경 변수를 사용하여 MPI 통신에 허용 가능한 다양한 주소를 지정합니다. HPC 팩 2012 R2부터 CCP_MPI_NETMASK 클러스터 환경 면수는 도메인에 연결된 클러스터 계산 노드(온-프레미스 또는 Azure VM) 간 MPI 통신에만 영향을 미칩니다. Azure로 버스트 구성에 추가된 노드는 변수를 무시합니다.
* MPI 작업은 다른 클라우드 서비스에 배포된 Azure 인스턴스에서 실행할 수 없습니다(예: 다른 노드 템플릿을 사용하는 Azure로 버스트 배포 또는 여러 클라우드 서비스에 배포된 Azure VM 계산 노드). 다른 노드 템플릿으로 시작된 여러 Azure 노드 배포가 있는 경우 MPI 작업은 하나의 Azure 노드에서만 실행해야 합니다.
* 클러스터에 Azure 노드를 추가하고 온라인 상태로 전환할 경우 HPC 작업 Scheduler 서비스는 노드에서 즉시 작업을 시작하려고 합니다. 워크로드 중 일부만 Azure에서 실행할 수 있을 경우 작업 템플릿을 업데이트하거나 만들어 Azure에서 실행 가능한 작업 유형을 정의해야 합니다. 예를 들어 작업 템플릿과 함께 제출된 작업이 Azure 노드에서만 실행되도록 하려면 작업 템플릿에 노드 그룹 속성을 추가하고 필수 값으로 AzureNodes를 선택합니다. Azure 노드에 대해 사용자 지정 그룹을 만들려면 Add-HpcGroup HPC PowerShell cmdlet을 사용합니다.

## <a name="next-steps"></a>다음 단계
* HPC Pack을 사용하는 대신 Azure의 관리되는 계산 노드 풀에서 MPI 응용 프로그램을 실행하기 위해 Azure Batch 서비스를 사용하여 개발합니다. [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 응용 프로그램 실행](../../../batch/batch-mpi.md)을 참조하세요.
* Azure RDMA 네트워크에 액세스하는 Linux MPI 응용 프로그램을 실행하려는 경우 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](../../linux/classic/rdma-cluster.md)을 참조하세요.

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
