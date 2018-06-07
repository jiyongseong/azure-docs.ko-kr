---
title: VM을 Azure Premium Storage로 마이그레이션 | Microsoft Docs
description: 기존 VM을 Azure Premium Storage로 마이그레이션합니다. Premium Storage는 Azure Virtual Machines에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다.
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: 36ff73d36c752fb342dcfff2360b4f6f7013740e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Azure Premium Storage로 마이그레이션(관리되지 않는 디스크)

> [!NOTE]
> 이 문서는 관리되지 않는 표준 디스크를 사용하는 VM을 관리되지 않는 프리미엄 디스크를 사용하는 VM으로 마이그레이션하는 방법을 설명합니다. 새 VM에 Azure Managed Disks를 사용하고 이전의 관리되지 않은 디스크를 Managed Disks로 변환하는 것이 좋습니다. Managed Disks에서 내부 저장소 계정을 처리해 주기 때문에 사용자가 처리할 필요가 없습니다. 자세한 내용은 [Managed Disks 개요](../../virtual-machines/windows/managed-disks-overview.md)를 참조하세요.
>

Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 머신에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 응용 프로그램의 VM 디스크를 Azure Premium Storage로 마이그레이션하여 이러한 디스크의 속도와 성능 혜택을 활용할 수 있습니다.

이 가이드의 목적은 Azure Premium Storage를 처음 사용하는 사용자가 현재 시스템에서 Premium Storage로 원활한 전환이 가능하도록 돕는 것입니다. 이 가이드에서는 이 프로세스의 세 가지 주요 구성 요소를 다룹니다.

* [Premium Storage로 마이그레이션 계획 수립](#plan-the-migration-to-premium-storage)
* [VHD(가상 하드 디스크)를 준비하여 Premium Storage로 복사](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Premium Storage를 사용하여 Azure Virtual Machine 만들기](#create-azure-virtual-machine-using-premium-storage)

VM을 다른 플랫폼에서 Azure Premium Storage로 마이그레이션할 수도 있고 기존 Azure VM을 표준 저장소에서 Premium Storage로 마이그레이션할 수도 있습니다. 이 가이드에서는 두 시나리오의 단계를 다룹니다. 시나리오에 따라 관련 섹션에 지정된 단계를 따릅니다.

> [!NOTE]
> Premium Storage의 기능 개요 및 가격 책정은 Premium Storage: [Azure Virtual Machine 워크로드를 위한 고성능 저장소](../../virtual-machines/windows/premium-storage.md)를 참조하세요. 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 가상 머신 디스크를 Azure Premium Storage로 마이그레이션하는 것이 좋습니다. 디스크에 높은 IOPS가 필요하지 않은 경우, 가상 머신 디스크 데이터를 SSD가 아닌 하드 디스크 드라이브(HDD)에 저자하는 Standard Storage를 사용하여 비용을 절약할 수 있습니다.
>

전체 마이그레이션 프로세스를 완료하기 위해서는 이 가이드에 제공된 단계 전과 후에 추가 작업이 필요할 수 있습니다. 이러한 작업의 예로는 가상 네트워크 또는 끝점을 구성하거나 응용 프로그램 자체 내에서 코드를 변경하는 것이 포함되며 이러한 작업은 응용 프로그램에서 약간의 가동 중지 시간이 필요할 수 있습니다. 이러한 작업은 각 응용 프로그램에 대해 고유하며 Premium Storage로 가능한 한 원활하게 완전히 전환하기 위해서는 이 가이드에 제공된 단계에 따라 완료해야 합니다.

## <a name="plan-the-migration-to-premium-storage"></a>Premium Storage로 마이그레이션 계획 수립
이 섹션은 이 문서의 마이그레이션 단계를 수행할 준비를 하고 가장 적합한 VM 및 디스크 유형을 선택할 수 있도록 도와주기 위한 섹션입니다.

### <a name="prerequisites"></a>필수 조건
* Azure 구독이 필요합니다. 구독이 없다면, 한 달의 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 구독하거나 [Azure 가격 책정](https://azure.microsoft.com/pricing/)을 방문하여 추가 옵션을 참고합니다.
* PowerShell cmdlet을 실행하려면 Microsoft Azure PowerShell 모듈이 필요합니다. 설치 지점 및 설치 지침에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 을 참조하세요.
* Premium Storage에서 실행되는 Azure VM을 사용하려는 경우 Premium Storage 지원 VM을 사용해야 합니다. Premium Storage 지원 VM에서 표준 저장소 디스크와 Premium Storage 디스크를 모두 사용할 수 있습니다. 프리미엄 저장소 디스크를 나중에 더 많은 VM 형식으로 사용할 수 있습니다. 사용 가능한 Azure VM 디스크 유형 및 크기에 대한 자세한 내용은 [가상 머신 크기](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [Cloud Services 크기](../../cloud-services/cloud-services-sizes-specs.md)를 참조하세요.

### <a name="considerations"></a>고려 사항
Azure VM은 여러 Premium Storage 디스크의 연결을 지원하므로 응용 프로그램이 VM당 최대 256TB의 저장소를 지원할 수 있습니다. Premium Storage를 사용할 경우 읽기 작업의 대기 시간이 매우 짧은 상태로 VM당 80,000 IOPS(초당 입/출력 작업 수) 및 VM당 디스크 처리량 초당 2000MB를 얻을 수 있습니다. 다양한 VM 및 디스크 옵션이 있습니다. 이 섹션은 워크로드에 가장 적합한 옵션을 찾을 수 있도록 도와주기 위해 준비되었습니다.

#### <a name="vm-sizes"></a>VM 크기
Azure VM 크기 사양은 [가상 머신의 크기](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 나열되어 있습니다. Premium Storage와 작동하는 가상 머신의 성능 특징을 검토하고 워크로드에 가장 적합한 VM 크기를 선택합니다. VM에서 디스크 트래픽을 제어하기에 충분한 대역폭을 사용할 수 있는지 확인합니다.

#### <a name="disk-sizes"></a>디스크 크기
VM에서 사용할 수 있는 디스크에는 다섯 종류가 있으며 각 종류에는 특정 IOP 및 처리량 제한이 있습니다. 용량, 성능, 확장성 및 최대 로드 측면에서 응용 프로그램의 필요에 따라 VM에 대한 디스크 유형을 선택할 때 이 제한을 고려해야 합니다.

| 프리미엄 디스크 유형  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| 디스크 크기           | 128GB| 512 GB| 1,024GB(1TB) | 2,048GB(2TB) | 4,095GB(4TB) | 
| 디스크당 IOPS       | 500   | 2,300  | 5,000           | 7,500           | 7,500           | 
| 디스크당 처리량 | 초당 100MB | 초당 150MB | 초당 200MB | 초당 250MB | 초당 250MB |

사용자 워크로드에 따라 추가 데이터 디스크가 VM에 필요한 경우를 결정합니다. VM에 여러 영구 데이터 디스크를 연결할 수 있습니다. 필요한 경우, 볼륨의 성능과 용량을 늘리도록 디스크에 걸쳐 스트라이핑할 수 있습니다. (디스크 스트라이프란 무엇인지 [여기서](../../virtual-machines/windows/premium-storage-performance.md#disk-striping) 확인하세요.) [저장소 공간][4]을 사용하여 Premium Storage 데이터 디스크를 스트라이프하는 경우, 사용되는 각 디스크에 대해 하나의 열로 구성해야 합니다. 그렇지 않으면 디스크에 트래픽이 고르게 분배되지 않아 스트라이프 볼륨의 전반적인 성능이 예상보다 저하될 수 있습니다. Linux VM의 경우 *mdadm* 유틸리티를 사용하여 동일한 작업을 수행할 수 있습니다. 자세한 내용은 [Linux에서 소프트웨어 RAID 구성](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서를 참조하세요.

#### <a name="storage-account-scalability-targets"></a>Storage 계정의 확장성 목표
Premium Storage 계정에는 [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md) 이외에 다음 확장성 목표가 있습니다. 응용 프로그램의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우, 여러 저장소 계정을 사용하도록 응용 프로그램을 빌드하고 데이터를 이러한 저장소 계정에 분할합니다.

| 총 계정 용량 | 로컬 중복 Storage 계정의 총 대역폭 |
|:--- |:--- |
| 디스크 용량: 35TB<br />스냅숏 용량: 10TB |인바운드+아웃바운드에 대해 초당 최대 50기가비트 |

프리미엄 저장소 사양에 대한 자세한 내용은 [Premium Storage를 사용하는 경우 확장성 및 성능 목표](../../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)를 참조하세요.

#### <a name="disk-caching-policy"></a>디스크 캐싱 정책
기본적으로 디스크 캐싱 정책은 VM에 연결된 프리미엄 운영 체제 디스크에 대한 *읽기 / 쓰기* 및 모든 프리미엄 데이터 디스크에 대한 *읽기 전용*입니다. 응용 프로그램의 IO에 대한 최적의 성능을 얻으려면 이 구성 설정이 좋습니다. 쓰기가 많거나 쓰기 전용인 디스크의 경우(예: SQL Server 로그 파일) 더 나은 응용 프로그램 성능을 얻기 위해 디스크 캐싱을 사용하지 않도록 설정합니다. [Azure Portal](https://portal.azure.com) 또는 *Set-AzureDataDisk* cmdlet의 *-HostCaching* 매개 변수를 사용하여 기존 데이터 디스크에 대한 캐시 설정을 업데이트할 수 있습니다.

#### <a name="location"></a>위치
Azure Premium Storage를 사용할 수 있는 위치를 선택합니다. 사용 가능한 위치에 대한 최신 정보는 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요. VM에 대한 디스크를 저장하는 Storage 계정과 동일한 지역에 있는 VM은 별도 영역에 있는 경우보다 훨씬 우수한 성능을 제공합니다.

#### <a name="other-azure-vm-configuration-settings"></a>기타 Azure VM 구성 설정
Azure VM을 만들 때 특정 VM 설정을 구성해야 합니다. 나중에 다른 설정을 수정하거나 추가할 수 있지만 몇 가지 설정은 VM의 수명 동안 고정됩니다. 이러한 Azure VM 구성 설정을 검토 하고 워크로드 부하 요구 사항과 일치하도록 적절하게 구성되었는지 확인해야 합니다.

### <a name="optimization"></a>최적화
[Azure Premium Storage: 고성능을 위한 설계](../../virtual-machines/windows/premium-storage-performance.md) Azure Premium Storage를 사용하여 고성능 응용 프로그램을 구축하기 위한 지침을 제공합니다. 응용 프로그램에서 사용되는 기술에 적용 가능한 성능 모범 사례가 결합된 지침을 사용할 수 있습니다.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>VHD(가상 하드 디스크)를 준비하여 Premium Storage로 복사
다음 섹션에서는 VM에서 VHD를 준비하여 Azure Storage에 VHD를 복사하기 위한 지침을 제공합니다.

* [시나리오 1: "기존 Azure VM을 Azure Premium Storage로 마이그레이션하려 합니다."](#scenario1)
* [시나리오 2: "다른 플랫폼에서 Azure Premium Storage로 VM을 마이그레이션하려 합니다."](#scenario2)

### <a name="prerequisites"></a>필수 조건
마이그레이션에 사용할 VHD를 준비하려면 다음이 필요합니다.

* Azure 구독, 저장소 계정 및 VHD를 복사할 수 있는 저장소 계정의 컨테이너. 요구 사항에 따라 대상 Storage 계정은 표준 또는 Premium Storage 계정일 수 있습니다.
* 여러 VM 인스턴스를 만들 계획인 경우 VHD를 일반화하는 도구입니다. 예를 들어 Ubuntu용 virt-sysprep 또는 Windows용 sysprep입니다.
* VHD 파일을 Storage 계정에 업로드하는 도구입니다. [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)을 참조하거나 [Azure Storage 탐색기](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)를 사용하세요. 이 가이드는 AzCopy 도구를 사용하여 VHD 복사를 설명합니다.

> [!NOTE]
> AzCopy를 사용한 동기 복사 옵션을 선택하는 경우 최적의 성능을 위해 대상 저장소 계정과 동일한 지역에 있는 Azure VM에서 이러한 도구 중 하나를 실행하여 VHD를 복사합니다. 다른 지역에는 Azure VM에서 VHD를 복사 하는 경우 성능이 느려질 수 있습니다.
>
> 대역폭이 제한된 많은 양의 데이터를 복사하는 경우 [Azure Import/Export 서비스를 사용하여 Blob Storage로 데이터 전송](../storage-import-export-service.md)을 사용하는 것이 좋습니다. 이렇게 하면 디스크 드라이브를 Azure 데이터 센터에 전달하여 데이터를 전송할 수 있습니다. Import/Export 서비스를 사용하여 표준 저장소 계정에만 데이터를 복사할 수 있습니다. 데이터가 표준 저장소 계정에 있는 경우 [Blob API 복사](https://msdn.microsoft.com/library/azure/dd894037.aspx) 또는 AzCopy를 사용하여 Premium Storage 계정에 데이터를 전송할 수 있습니다.
>
> Microsoft Azure는 고정된 크기의 VHD 파일만을 지원합니다. 동적 VHD 또는 VHDX 파일은 지원되지 않습니다. 동적 VHD를 설정한 경우 [CONVERT-VHD](http://technet.microsoft.com/library/hh848454.aspx) cmdlet을 사용하여 고정된 크기를 변환할 수 있습니다.
>
>

### <a name="scenario1"></a>시나리오 1: "기존 Azure VM을 Azure Premium Storage로 마이그레이션하려 합니다."
기존 Azure VM을 마이그레이션하는 경우 VM을 중지하고, 원하는 VHD 유형마다 VHD를 준비하고, AzCopy 또는 PowerShell을 사용하여 VHD를 복사합니다.

깨끗한 상태로 마이그레이션하려면 VM을 완전히 중지해야 합니다. 마이그레이션이 완료될 때까지 가동 중지 상태입니다.

#### <a name="step-1-prepare-vhds-for-migration"></a>1단계. 마이그레이션할 VHD를 준비합니다.
기존 Azure VM을 Premium Storage로 마이그레이션하려는 경우 VHD가 다음일 수 있습니다.

* 일반화된 운영 체제 이미지
* 고유의 운영 체제 디스크
* 데이터 디스크

아래에서는 VHD를 준비하기 위한 3가지 시나리오를 살펴봅니다.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>일반화된 운영 체제 VHD를 사용하여 여러 VM 인스턴스 만들기
여러 일반 Azure VM 인스턴스를 만드는데 사용할 VHD를 업로드하는 경우 , sysprep 유틸리티를 사용하여 VHD를 먼저 일반화해야 합니다. 온-프레미스 또는 클라우드에 있는 VHD에 적용됩니다. Sysprep는 VHD에서 모든 컴퓨터의 특정 정보를 제거합니다.

> [!IMPORTANT]
> 스냅숏을 찍거나 VM을 백업하기 전에 일반화합니다. 실행 중인 sysprep가 중지되고 VM 인스턴스의 할당이 취소됩니다. Windows 운영 체제 VHD를 sysprep 하려면 아래 단계를 따르세요. Sysprep 명령을 가상 머신 종료를 해야한다는 것을 참고하세요. Sysprep에 대한 자세한 내용은 [Sysprep 개요](http://technet.microsoft.com/library/hh825209.aspx) 또는 [Sysprep 기술 참조](http://technet.microsoft.com/library/cc766049.aspx)를 참조하세요.
>
>

1. 관리자로 명령 프롬프트 창을 엽니다.
2. 다음 명령을 입력하여 Sysprep을 엽니다.

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. 아래 이미지와 같이 시스템 준비 도구에서 시스템 OOBE(첫 실행 경험) 시작을 선택하고 일반화 확인란을 선택한 다음 **종료**를 선택하고 **확인**을 클릭합니다. Sysprep는 이 운영 체제를 일반화하고 시스템을 종료합니다.

    ![][1]

Ubuntu VM에 대해 동일한 작업을 수행할 virt sysprep를 사용할 수 있습니다. 자세한 내용은 [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) 를 참조하세요. 다른 Linux 운영 체제는 공개 소스 [Linux 서버 프로비전 소프트웨어](http://www.cyberciti.biz/tips/server-provisioning-software.html) 의 일부도 참조하세요.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>고유의 운영 체제 VHD를 사용하여 단일 VM 인스턴스 만들기
컴퓨터 특정 데이터를 필요로 하는 VM에서 실행 중인 응용 프로그램이 있는 경우 VHD를 일반화하지 않습니다. 일반화되지 않은 VHD는 고유한 Azure VM 인스턴스를 만드는 데 사용될 수 있습니다. 예를들어 VHD에 도메인 컨트롤러가 있는 경우, sysprep를 실행하면 도메인 컨트롤러가 비효율적이게 됩니다. VHD를 일반화하기 전에 VM에서 실행 중인 응용 프로그램을 검토하고 이러한 응용 프로그램에서 sysprep를 실행할 때의 영향을 확인합니다.

##### <a name="register-data-disk-vhd"></a>데이터 디스크 VHD 등록
Azure에 마이그레이션할 데이터 디스크가 있는 경우 이 데이터 디스크를 사용하는 VM을 종료해야 합니다.

아래에 설명된 단계에 따라 Azure Premium Storage에 VHD를 복사하고 프로비전된 데이터 디스크로 등록합니다.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2단계. VHD에 대한 대상 만들기
VHD를 유지 관리하기 위한 저장소 계정을 만듭니다. VHD를 저장할 위치를 계획할 때 다음 사항을 고려해야 합니다.

* 대상 Premium Storage 계정.
* 저장소 계정 위치는 최종 단계에서 만들 Premium Storage 지원 Azure VM과 동일해야 합니다. 새 저장소 계정으로 복사하거나 필요에 따라 동일한 저장소 계정을 사용할 수 있습니다.
* 다음 단계는 대상 저장소 계정의 저장소 계정 키를 복사하고 저장합니다.

데이터 디스크의 경우 표준 저장소 계정에 일부 데이터 디스크(예: 냉각 저장소가 있는 디스크)를 보관할 수 있지만, 프로덕션 워크로드에서 Premium Storage를 사용하도록 모든 데이터를 이동할 것을 권장합니다.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>3단계. AzCopy 또는 PowerShell을 사용하여 VHD 복사
컨테이너 경로 및 저장소 계정 키를 찾아서 이러한 두 옵션 중 하나를 처리해야 합니다. 컨테이너 경로 및 저장소 계정 키는 **Azure Portal** > **저장소**에서 찾을 수 있습니다. 컨테이너 URL은 "https://myaccount.blob.core.windows.net/mycontainer/"와 유사합니다.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>옵션 1: AzCopy를 사용하여 VHD 복사(비동기 복사)
AzCopy를 사용하여 인터넷을 통해 VHD를 쉽게 업로드할 수 있습니다. 소요되는 시간은 VHD의 크기에 따라 다를 수 있습니다. 이 옵션을 사용하는 경우 저장소 계정 송/수신 제한을 확인해야 합니다. 자세한 내용은 [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

1. [최신 버전의 AzCopy](http://aka.ms/downloadazcopy)
2. Azure PowerShell을 열고 AzCopy를 설치한 폴더로 이동합니다.
3. "원본"에서 "대상"으로 VHD 파일을 복사하려면 다음 명령을 사용합니다.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    예:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    AzCopy 명령을 사용 하는 매개 변수에 대한 설명은 다음과 같습니다.

   * **/Source: *&lt;source&gt;:*** VHD를 포함하는 폴더 또는 저장소 컨테이너 URL의 위치입니다.
   * **/SourceKey: *&lt;source-account-key&gt;:*** 원본 Storage 계정의 Storage 계정 키입니다.
   * **/Dest: *&lt;destination&gt;:*** VHD를 복사할 저장소 컨테이너 URL입니다.
   * **/DestKey: *&lt;dest-account-key&gt;:*** 대상 Storage 계정의 Storage 계정 키입니다.
   * **/Pattern: *&lt;file-name&gt;:*** 복사할 VHD의 파일 이름을 지정합니다.

AzCopy 도구 사용에 대한 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)을 참조하세요.

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>옵션 2: PowerShell을 사용하여 VHD 복사(동기 복사)
또한 PowerShell cmdlet Start-AzureStorageBlobCopy를 사용하여 VHD 파일을 복사할 수도 있습니다. Azure PowerShell에서 다음 명령을 사용하여 VHD를 복사합니다. 원본 및 대상 저장소 계정에서 해당 값으로 <>의 값을 대체합니다. 이 명령을 사용하려면 대상 저장소 계정에 vhd라는 컨테이너가 있어야 합니다. 컨테이너가 존재하지 않으면 명령을 실행하기 전에 하나를 만듭니다.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

예:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>시나리오 2: "다른 플랫폼에서 Azure Premium Storage로 VM을 마이그레이션하려 합니다."
비-Azure 클라우드 저장소에서 Azure로 VHD를 마이그레이션하는 경우, 먼저 VHD를 로컬 디렉터리로 내보내야 합니다. VHD가 저장된 로컬 디렉터리의 전체 소스 경로를 찾은 다음 AzCopy를 사용하여 Azure Storage에 업로드합니다.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>1단계. VHD를 로컬 디렉터리로 내보냅니다.
##### <a name="copy-a-vhd-from-aws"></a>AWS에서 VHD 복사
1. AWS를 사용하는 경우, EC2 인스턴스를 Amazon S3 버킷의 VHD로 내보냅니다. Amazon 설명서에 제공된 Amazon EC2 인스턴스 내보내기 단계에 따라 Amazon EC2 CLI(명령줄 인터페이스) 도구를 설치하고 create-instance-export-task 명령을 실행하여 EC2 인스턴스를 VHD 파일로 내보냅니다. **create-instance-export-task** 명령 실행 시 DISK&#95;IMAGE&#95;FORMAT 변수에 **VHD**를 사용해야 합니다. 내보낸 VHD 파일은 해당 프로세스 중 지정된 Amazon S3 버킷에 저장됩니다.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. S3 버킷에서 VHD 파일을 다운로드 합니다. VHD 파일을 선택한 다음 **작업** > **다운로드**를 선택합니다.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>다른 비-Azure 클라우드에서 VHD 복사
비-Azure 클라우드 저장소에서 Azure로 VHD를 마이그레이션하는 경우, 먼저 VHD를 로컬 디렉터리로 내보내야 합니다. VHD가 저장된 로컬 디렉터리의 전체 소스 경로를 복사합니다.

##### <a name="copy-a-vhd-from-on-premises"></a>온-프레미스에서 VHD 복사
온-프레미스 환경에서 VHD를 마이그레이션하는 경우 VHD가 저장된 전체 소스 경로가 필요합니다. 이 소스 경로는 서버 위치 또는 파일 공유일 수 있습니다.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2단계. VHD에 대한 대상 만들기
VHD를 유지 관리하기 위한 저장소 계정을 만듭니다. VHD를 저장할 위치를 계획할 때 다음 사항을 고려해야 합니다.

* 응용 프로그램 요구 사항에 따라 대상 저장소 계정은 표준 또는 프리미엄 저장소가 될 수 있습니다.
* 저장소 계정 지역은 최종 단계에서 만들 Premium Storage 지원 Azure VM과 동일해야 합니다. 새 저장소 계정으로 복사하거나 필요에 따라 동일한 저장소 계정을 사용할 수 있습니다.
* 다음 단계는 대상 저장소 계정의 저장소 계정 키를 복사하고 저장합니다.

프로덕션 워크로드에서 Premium Storage를 사용하도록 모든 데이터를 이동할 것을 권장합니다.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>3단계. Azure Storage에 VHD 업로드
이제 VHD가 로컬 디렉터리에 있으니, AzCopy 또는 Azure PowerShell을 사용하여 .vhd 파일을 Azure Storage에 업로드할 수 있습니다. 두 가지 업로드 옵션이 제공됩니다.

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>옵션 1: Azure PowerShell Add-AzureVhd를 사용하여 .vhd 파일 업로드

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

예제 <Uri>도 ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***일 수 있습니다. <FileInfo>의 예로 ***"C:\path\to\upload.vhd"***를 들 수 있습니다.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>옵션 2: AzCopy를 사용하여 .vhd 파일 업로드
AzCopy를 사용하여 인터넷을 통해 VHD를 쉽게 업로드할 수 있습니다. 소요되는 시간은 VHD의 크기에 따라 다를 수 있습니다. 이 옵션을 사용하는 경우 저장소 계정 송/수신 제한을 확인해야 합니다. 자세한 내용은 [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

1. [최신 버전의 AzCopy](http://aka.ms/downloadazcopy)
2. Azure PowerShell을 열고 AzCopy를 설치한 폴더로 이동합니다.
3. "원본"에서 "대상"으로 VHD 파일을 복사하려면 다음 명령을 사용합니다.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    예:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    AzCopy 명령을 사용 하는 매개 변수에 대한 설명은 다음과 같습니다.

   * **/Source: *&lt;source&gt;:*** VHD를 포함하는 폴더 또는 저장소 컨테이너 URL의 위치입니다.
   * **/SourceKey: *&lt;source-account-key&gt;:*** 원본 Storage 계정의 Storage 계정 키입니다.
   * **/Dest: *&lt;destination&gt;:*** VHD를 복사할 저장소 컨테이너 URL입니다.
   * **/DestKey: *&lt;dest-account-key&gt;:*** 대상 Storage 계정의 Storage 계정 키입니다.
   * **/BlobType: page:** 대상을 페이지 Blob으로 지정합니다.
   * **/Pattern: *&lt;file-name&gt;:*** 복사할 VHD의 파일 이름을 지정합니다.

AzCopy 도구 사용에 대한 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)을 참조하세요.

##### <a name="other-options-for-uploading-a-vhd"></a>VHD를 업로드하기 위한 기타 옵션
또한 다음과 같은 방법 중 하나를 사용하여 저장소 계정에 VHD를 업로드할 수 있습니다.

* [Azure Storage Blob 복사 API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage 탐색기 Blob 업로드](https://azurestorageexplorer.codeplex.com/)
* [저장소 Import/Export 서비스 REST API 참조](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> 예상 업로드 시간이 7일보다 긴 경우 Import/Export 서비스를 사용하는 것이 좋습니다. [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html)를 사용하여 데이터 크기 및 전송 단위로 시간을 예측할 수 있습니다.
>
> Import/Export를 사용하여 표준 저장소 계정을 복사할 수 있습니다. AzCopy와 같은 도구를 사용하여 표준 저장소에서 프리미엄 저장소 계정으로 복사해야 합니다.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Premium Storage를 사용하여 Azure VM 만들기
VHD를 원하는 저장소 계정에 업로드 또는 복사한 후에는 이 섹션의 설명에 따라 VHD를 OS 이미지로 등록하거나 사용자 시나리오에 따라 OS 디스크를 등록한 다음, 해당 이미지나 디스크에서 VM 인스턴스를 만듭니다. 생성되면 데이터 디스크 VHD를 VM에 연결할 수 있습니다.
이 섹션의 끝부분에 마이그레이션 스크립트 예제가 제공됩니다. 이 간단한 스크립트가 모든 시나리오에 적합한 것은 아닙니다. 특정 시나리오에 맞게 스크립트를 업데이트해야 할 수 있습니다. 이 스크립트가 내 시나리오에 적용되는지 알아보려면 아래의 [샘플 마이그레이션 스크립트](#a-sample-migration-script)를 참조하세요.

### <a name="checklist"></a>검사 목록
1. VHD 디스크 복사가 완료될 때까지 기다립니다.
2. Premium Storage를 마이그레이션하는 지역에서 사용할 수 있는지 확인합니다.
3. 사용할 새 VM 시리즈를 결정합니다. Premium Storage를 지원해야 하며, 크기는 해당 지역의 제품 제공 여부와 사용자 요구 사항에 따라 결정해야 합니다.
4. 사용할 정확한 VM 크기를 결정합니다. VM 크기는 현재 포함하고 있는 데이터 디스크 수를 지원할 만큼 충분히 커야 합니다. 예: 데이터 디스크가 4개 있는 경우 VM은 2개 이상의 코어를 포함해야 합니다. 또한 처리 능력, 메모리 및 네트워크 대역폭 요구 사항을 고려합니다.
5. 대상 지역에 Premium Storage 계정을 만듭니다. 새 VM에 사용할 계정입니다.
6. 디스크 및 해당 VHD Blob의 목록을 포함하여 도움이 될 현재 VM 세부 정보를 포함합니다.

응용 프로그램의 가동 중지 시간을 준비합니다. 원활한 마이그레이션 작업을 수행하려면 현재 시스템에서 모든 처리를 중지해야 합니다. 그런 다음 새 플랫폼으로 마이그레이션할 수 있는 일관된 상태로 가져올 수 있습니다. 가동 중지 시간은 디스크에서 마이그레이션할 데이터 양에 따라 달라집니다.

> [!NOTE]
> 특수한 VHD 디스크에서 Azure Resource Manager VM을 만드는 경우 기존 디스크를 사용하여 Resource Manager VM을 배포하는 데 사용되는 [이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)을 참조하세요.
>
>

### <a name="register-your-vhd"></a>장치를 등록합니다.
OS VHD에서 VM을 만들거나 새 VM에 데이터 디스크를 연결하려면 먼저 등록해야 합니다. VHD 시나리오에 따라 아래 단계를 따르세요.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>여러 Azure VM 인스턴스를 만드는 일반화된 운영 체제 VHD
일반화된 OS 이미지를 저장소 계정에 업로드한 후에는 이 이미지에서 하나 이상의 VM 인스턴스를 만들 수 있도록 이미지를 **Azure VM 이미지**로 등록합니다. Azure VM OS 이미지로 VHD를 등록 하려면 다음 PowerShell cmdlet을 사용합니다. VHD가 복사된 완전한 컨테이너 URL을 제공합니다.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

새 Azure VM 이미지의 이름을 복사하고 저장합니다. 위의 예제에서는 *OSImageName*입니다.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>단일 Azure VM 인스턴스를 만드는 고유 운영 체제 VHD
고유 OS VHD를 저장소 계정에 업로드 한 후에는 이 디스크에서 VM 인스턴스를 만들 수 있도록 디스크를 **Azure OS 디스크**로 등록합니다. 이 PowerShell cmdlet을 사용하여 Azure OS 이미지로 VHD를 등록합니다. VHD가 복사된 완전한 컨테이너 URL을 제공합니다.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

새 Azure OS 이미지의 이름을 복사하고 저장합니다. 위의 예제에서는 *OSDisk*입니다.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Azure VM 인스턴스에 연결될 데이터 디스크 VHD
데이터 디스크 VHD를 저장소 계정에 업로드한 후 Azure 데이터 디스크로 등록되면 새 DS 시리즈, DSv2 시리즈 또는 GS 시리즈 Azure VM 인스턴스에 연결할 수 있습니다.

이 PowerShell cmdlet을 사용하여 Azure 데이터 디스크로 VHD를 등록합니다. VHD가 복사된 완전한 컨테이너 URL을 제공합니다.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

새 Azure 데이터 디스크의 이름을 복사하고 저장합니다. 위의 예제에서는 *DataDisk*입니다.

### <a name="create-a-premium-storage-capable-vm"></a>Premium Storage 지원 VM 만들기
OS 이미지나 OS 디스크가 등록되면 새 DS 시리즈, DSv2 시리즈 또는 GS 시리즈 VM을 만듭니다. 등록된 운영 체제 이미지 또는 운영 체제 디스크 이름을 사용합니다. Premium Storage 계층에서 VM 종류를 선택합니다. 아래 예제에서는 *Standard_DS2* VM 크기를 사용 중입니다.

> [!NOTE]
> 디스크 크기를 업데이트하여 용량, 성능 요구 사항 및 사용 가능한 Azure 디스크 크기가 일치하는지 확인합니다.
>
>

아래 PowerShell cmdlet을 단계별로 수행하여 새 VM을 만듭니다. 먼저, 공통 매개 변수를 설정합니다.

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

먼저, 클라우드 서비스를 호스트할 새 Vm을 만듭니다.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

다음, 시나리오에 따라 등록된 OS 이미지 또는 OS 디스크에서 Azure VM 인스턴스를 만듭니다.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>여러 Azure VM 인스턴스를 만드는 일반화된 운영 체제 VHD
등록된 **Azure OS 이미지** 를 사용하여 하나 이상의 새 DS 시리즈 Azure VM 인스턴스를 만듭니다. 아래와 같이 새 VM을 만들 때 VM 구성에서 이 OS 이미지 이름을 지정합니다.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>단일 Azure VM 인스턴스를 만드는 고유 운영 체제 VHD
등록된 **Azure OS 디스크** 를 사용하여 새 DS 시리즈 Azure VM 인스턴스를 만듭니다. 아래와 같이 새 VM을 만들 때 VM 구성에서 이 OS 디스크 이름을 지정합니다.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

클라우드 서비스, 지역, 저장소 계정, 가용성 집합 및 캐싱 정책 등의 기타 Azure VM 정보를 지정합니다. VM 인스턴스는 연관된 운영 체제 또는 데이터 디스크와 배치되므로 선택된 클라우드 서비스, 지역 및 저장소 계정은 모두 해당 디스크의 기본 VHD와 동일한 위치에 있어야 합니다.

### <a name="attach-data-disk"></a>데이터 디스크 연결
마지막으로, 데이터 디스크 VHD를 등록한 경우 새 Premium Storage 지원 Azure VM에 연결합니다.

새 VM에 데이터 디스크를 연결하고 캐싱 정책을 지정하려면 다음 PowerShell cmdlet을 사용합니다. 아래 예제에서는 캐싱 정책이 *ReadOnly*로 설정됩니다.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> 이 가이드에서 다루지 않은 응용 프로그램을 지원하기 위해 추가 단계가 필요할 수 있습니다.
>
>

### <a name="checking-and-plan-backup"></a>확인 및 백업 계획
새 VM이 준비되고 실행 중이면 원본 VM과 동일한 로그인 ID 및 암호를 사용하여 액세스하고 모든 항목이 예상대로 작동하는지 확인합니다. 스트라이프 볼륨을 포함한 모든 설정이 새 VM에 제공됩니다.

마지막 단계는 응용 프로그램 요구 사항에 따라 새 VM의 백업 및 유지 관리 일정을 계획하는 것입니다.

### <a name="a-sample-migration-script"></a>샘플 마이그레이션 스크립트
마이그레이션할 여러 VM이 있는 경우 PowerShell 스크립트를 통한 자동화가 도움이 됩니다. 다음은 VM의 마이그레이션을 자동화하는 예제 스크립트입니다. 아래의 스크립트는 현재 VM 디스크에 대해 몇 가지 조건을 가정하고 만든 예제일 뿐입니다. 특정 시나리오에 맞게 스크립트를 업데이트해야 할 수 있습니다.

가정한 조건은 다음과 같습니다.

* 클래식 Azure VM을 만듭니다.
* 원본 OS 디스크와 원본 데이터 디스크가 동일한 저장소 계정 및 동일한 컨테이너에 있습니다. OS 디스크와 데이터 디스크가 같은 위치에 있지 않으면 AzCopy 또는 Azure PowerShell을 사용하여 저장소 계정 및 컨테이너를 통해 VHD를 복사할 수 있습니다. 이전 단계 [AzCopy 또는 PowerShell을 사용하여 VHD 복사](#copy-vhd-with-azcopy-or-powershell)를 참조하세요. 시나리오를 충족하도록 이 스크립트를 편집하는 방법도 있지만 더 간편하고 빠른 AzCopy 또는 PowerShell을 사용하는 것이 좋습니다.

자동화 스크립트는 아래에 제공됩니다. 텍스트를 사용자의 정보로 바꾸고 사용자의 특정 시나리오와 일치하도록 스크립트를 업데이트하세요.

> [!NOTE]
> 기존 스크립트를 사용하면 원본 VM의 네트워크 구성이 유지되지 않습니다. 마이그레이션된 VM의 네트워킹 설정을 다시 구성해야 합니다.
>
>

```
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>최적화
표준 디스크와 잘 작동하도록 현재 VM 구성을 사용자 지정할 수 있습니다. 예를 들어, 스트라이프 볼륨의 많은 디스크를 사용하여 성능을 향상시킵니다. 예를 들어 Premium Storage에서 4개의 디스크를 개별적으로 사용하는 대신 단일 디스크를 사용하여 비용을 최적화할 수 있습니다. 이와 같은 최적화는 상황별로 처리해야 하며 마이그레이션 후 사용자 지정 단계가 필요합니다. 이 프로세스는 설치 시 정의된 디스크 레이아웃에 종속된 응용 프로그램 및 데이터베이스에 대해서는 제대로 작동하지 않을 수 있습니다.

##### <a name="preparation"></a>준비
1. 이전 섹션에서 설명한 대로 간단한 마이그레이션을 완료합니다. 마이그레이션 후 새 VM에서 최적화가 수행됩니다.
2. 최적화된 구성에 필요한 새 디스크 크기를 정의합니다.
3. 새 디스크 사양에 대해 현재 디스크/볼륨의 매핑을 결정합니다.

##### <a name="execution-steps"></a>실행 단계
1. Premium Storage VM에 올바른 크기의 새 디스크를 만듭니다.
2. VM에 로그인하고 현재 볼륨의 데이터를 해당 볼륨에 매핑되는 새 디스크로 복사합니다. 새 디스크에 매핑해야 하는 모든 현재 볼륨에 대해 이 작업을 수행합니다.
3. 다음으로, 새 디스크로 전환하도록 응용 프로그램 설정을 변경하고 이전 볼륨을 분리합니다.

디스크 성능 향상을 위해 응용 프로그램을 튜닝하는 방법은 [응용 프로그램 성능 최적화](../../virtual-machines/windows/premium-storage-performance.md#optimizing-application-performance)를 참조하세요.

### <a name="application-migrations"></a>응용 프로그램 마이그레이션
데이터베이스 및 기타 복잡한 응용 프로그램에는 응용 프로그램 공급자가 마이그레이션에 대해 정의한 특별한 단계가 필요할 수 있습니다. 각각의 응용 프로그램 설명서를 참조하세요. 예: 일반적으로 백업 및 복원을 통해 데이터베이스를 마이그레이션할 수 있습니다.

## <a name="next-steps"></a>다음 단계
가상 머신 마이그레이션에 대한 특정 시나리오에 대한 다음 리소스를 확인합니다.

* [Storage 계정 간에 Azure Virtual Machines 마이그레이션](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Windows Server VHD를 만들고 Azure에 업로드합니다.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure에 Linux VHD 만들기 및 업로드](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Amazon AWS에서 Microsoft Azure로 Virtual Machines 마이그레이션](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Azure Storage 및 Azure Virtual Machines에 대한 자세한 내용을 보려면 다음 리소스도 확인하세요.

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Azure Virtual Machine 워크로드를 위한 고성능 저장소](../../virtual-machines/windows/premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
