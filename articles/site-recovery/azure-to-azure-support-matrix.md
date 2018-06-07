---
title: Azure 간에 복제하기 위한 Azure Site Recovery 지원 매트릭스 | Microsoft Docs
description: 재해 복구(DR) 요구 사항에 따라 지역 간에 Azure VM(가상 머신)을 Azure Site Recovery 복제하기 위한 구성 및 지원되는 운영 체제를 요약합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: sujayt
ms.openlocfilehash: daaca3c7b389642a288f738eb0954050e168abef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210565"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>한 Azure 지역에서 다른 지역으로 복제를 위한 지원 매트릭스


>[!NOTE]
>
> Azure 가상 머신에 대한 Site Recovery 복제는 현재 미리 보기로 제공됩니다.

이 문서에서는 지역 간에 Azure Virtual Machines를 복제 및 복구할 때 Azure Site Recovery에 대해 지원되는 구성 및 구성 요소를 요약하여 설명합니다.

## <a name="user-interface-options"></a>사용자 인터페이스 옵션

**사용자 인터페이스** |  **지원됨/지원되지 않음**
--- | ---
**Azure Portal** | 지원됨
**클래식 포털** | 지원되지 않음
**PowerShell** | [PowerShell을 사용한 Azure 간 복제](azure-to-azure-powershell.md)
**REST API** | 현재 지원되지 않음
**CLI** | 현재 지원되지 않음


## <a name="resource-move-support"></a>리소스 이동 지원

**리소스 이동 유형** | **지원됨/지원되지 않음** | **설명**  
--- | --- | ---
**리소스 그룹 간 자격 증명 모음 이동** | 지원되지 않음 |리소스 그룹 간에 Recovery Services 자격 증명 모음을 이동할 수 없습니다.
**리소스 그룹 간에 Compute, Storage 및 Network 이동** | 지원되지 않음 |복제를 사용하도록 설정한 후 가상 머신(또는 저장소 및 네트워크와 같은 연결된 구성 요소)를 이동하는 경우 해당 가상 머신에 대해 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.



## <a name="support-for-deployment-models"></a>배포 모델 지원

**배포 모델** | **지원됨/지원되지 않음** | **설명**  
--- | --- | ---
**클래식** | 지원됨 | 클래식 가상 머신은 복제한 후 클래식 가상 머신으로 복구할 수만 있습니다. Resource Manager 가상 컴퓨터로는 복구할 수 없습니다. 클래식 VM을 가상 네트워크 없이 Azure 지역에 직접 배포하는 경우 이는 지원되지 않습니다.
**리소스 관리자** | 지원됨 |

>[!NOTE]
>
> 1. 재해 복구 시나리오의 다른 구독 간에 Azure 가상 머신을 복제하는 것은 지원되지 않습니다.
> 2. Azure 구독에서 가상 머신을 마이그레이션하도록 지원되지 않습니다.
> 3. 동일한 지역 내에서 가상 머신을 마이그레이션하도록 지원되지 않습니다.
> 4. 클래식 배포 모델에서 Resource Manager 배포 모델로 Azure 가상 머신을 마이그레이션하도록 지원되지 않습니다.

## <a name="support-for-replicated-machine-os-versions"></a>복제된 컴퓨터 운영 체제 버전에 대한 지원

아래 지원은 언급된 OS에서 실행되는 모든 워크로드에 적용됩니다.

#### <a name="windows"></a>Windows

- Windows Server 2016(Server Core, 데스크톱 경험이 있는 Server)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1 이상

>[!NOTE]
>
> \* Windows Server 2016 Nano Server는 지원되지 않습니다.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- Ubuntu 14.04 LTS 서버[(지원되는 커널 버전)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS 서버[(지원되는 커널 버전)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7[(지원되는 커널 버전)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8[(지원되는 커널 버전)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

복제 컴퓨터를 SLES 11 SP3에서 SLES 11 SP4로 업그레이드하는 것은 지원되지 않습니다. 복제된 컴퓨터가 SLES 11SP3에서 SLES 11 SP4로 업그레이드된 경우 복제를 비활성화하고 업그레이드 후 컴퓨터를 다시 보호해야 합니다.

>[!NOTE]
>
> 암호 기반 인증 및 로그인을 사용하고, cloud-init 패키지를 사용하여 클라우드 가상 머신을 구성하는 Ubuntu 서버의 암호 기반 로그인은 장애 조치(failover) 시 사용되지 않도록 설정할 수 있습니다(cloudinit 구성에 따라 다름). Azure Portal에서 장애 조치(failover)된 가상 머신의 설정 메뉴(지원 + 문제 해결 섹션 아래)에서 암호를 재설정하여 암호 기반 로그인을 가상 머신에서 다시 사용하도록 설정할 수 있습니다.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Ubuntu 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | 9.12 | 3.13.0-24-generic에서 3.13.0-132-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic에서 3.13.0-137-generic,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-104-generic |
14.04 LTS | 9.14 | 3.13.0-24-generic에서 3.13.0-141-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-112-generic |
14.04 LTS | 9.15 | 3.13.0-24-generic에서 3.13.0-143-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-116-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic에서 4.4.0-96-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic에서 4.4.0-104-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic |
16.04 LTS | 9.14 | 4.4.0-21-generic에서 4.4.0-112-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-32-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1009-azure |
16.04 LTS | 9.15 | 4.4.0-21-generic에서 4.4.0-116-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-37-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Debian 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | 9.14, 9.15 | 3.2.0-4-amd64에서 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14, 9.15 | 3.16.0-4-amd64에서 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.5-amd64 |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Linux OS를 실행하는 Azure Virtual Machines에서 지원되는 파일 시스템 및 게스트 저장소 구성

* 파일 시스템: ext3, ext4, ReiserFS(Suse Linux Enterprise Server만), XFS
* 볼륨 관리자: LVM2
* 다중 경로 소프트웨어: 장치 매퍼

## <a name="region-support"></a>지역 지원

동일한 지리적 클러스터 내의 두 지역 간에 VM을 복제 및 복구할 수 있습니다.

**지리적 클러스터** | **Azure 지역**
-- | --
아메리카 | 캐나다 동부, 캐나다 중부, 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중부, 미국 중북부
유럽 | 영국 서부, 영국 남부, 북유럽, 유럽 서부, 프랑스 중부, 프랑스 남부
아시아 | 인도 남부, 인도 중부, 동남 아시아, 동아시아, 일본 동부, 일본 서부, 한국 중부, 한국 남부
오스트레일리아   | 오스트레일리아 동부, 오스트레일리아 남동부
Azure Government    | 미국 정부 버지니아, 미국 정부 아이오와, 미국 정부 애리조나, 미국 정부 텍사스, 미국 정부 동부, 미국 정부 중부
독일 | 독일 중부, 독일 북동부
중국 | 중국 동부, 중국 북부

>[!NOTE]
>
> 브라질 남부 지역의 경우 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2 및 미국 중북부 지역 중 하나로만 복제 및 장애 조치(failover)하고 장애 복구(failback)할 수 있습니다.


## <a name="support-for-compute-configuration"></a>Compute 구성 지원

**구성** | **지원됨/지원되지 않음** | **설명**
--- | --- | ---
크기 | CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM | [Azure Virtual Machines 크기](../virtual-machines/windows/sizes.md) 참조
가용성 집합 | 지원됨 | 포털에서 '복제 사용' 단계 중에 기본 옵션을 사용하는 경우 원본 지역 구성에 따라 가용성 집합이 자동으로 만들어집니다. 언제든지 '복제된 항목 > 설정 > Compute 및 네트워크 > 가용성 집합'에서 대상 가용성 집합을 변경할 수 있습니다.
HUB(하이브리드 사용 혜택) VM | 지원됨 | 원본 VM에 활성 HUB 라이선스가 있는 경우 테스트 장애 조치 또는 장애 조치 VM에서도 HUB 라이선스를 사용합니다.
가상 머신 크기 집합 | 지원되지 않음 |
Azure 갤러리 이미지 - Microsoft 게시 | 지원됨 | VM이 Site Recovery에서 지원하는 운영 체제에서 실행되는 경우에 지원됨
Azure 갤러리 이미지 - 타사 게시 | 지원됨 | VM이 Site Recovery에서 지원하는 운영 체제에서 실행되는 경우에 지원됨
사용자 지정 이미지 - 타사 게시 | 지원됨 | VM이 Site Recovery에서 지원하는 운영 체제에서 실행되는 경우에 지원됨
Site Recovery를 사용하여 마이그레이션된 VM | 지원됨 | Site Recovery를 사용하여 Azure에 마이그레이션된 VMware/물리적 컴퓨터인 경우 이전 버전의 모바일 서비스를 제거하고 컴퓨터를 다시 시작한 후 다른 Azure 지역에 복제해야 합니다.

## <a name="support-for-storage-configuration"></a>저장소 구성 지원

**구성** | **지원됨/지원되지 않음** | **설명**
--- | --- | ---
최대 OS 디스크 크기 | 2048GB | [VM에서 사용되는 디스크](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)를 참조하세요.
최대 데이터 디스크 크기 | 4,095GB | [VM에서 사용되는 디스크](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)를 참조하세요.
데이터 디스크 수 | Azure VM 크기에 따라 최대 64개 지원 | [Azure Virtual Machines 크기](../virtual-machines/windows/sizes.md) 참조
임시 디스크 | 항상 복제에서 제외됨 | 임시 디스크는 항상 복제에서 제외됩니다. Azure 지침에 따라 임시 디스크에 영구 데이터를 저장해서는 안 됩니다. 자세한 내용은 [Azure VM의 임시 디스크](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk)를 참조하세요.
디스크의 데이터 변경률 | 프리미엄 저장소의 경우 디스크당 최대 10MBps, 표준 저장소의 경우 디스크당 최대 2MBps | 디스크의 평균 데이터 변경률이 지속적으로 10MBps(프리미엄) 및 2MBps(표준)를 초과하는 경우 복제가 처리되지 않습니다. 그러나 데이터 버스트가 간헐적으로 발생하고 데이터 변경률이 일정 시간 동안 10MBps(프리미엄) 및 2MBps(표준)를 초과했다가 낮아지는 경우에는 복제가 처리됩니다. 이 경우 복구 지점이 약간 지연될 수 있습니다.
표준 저장소 계정의 디스크 | 지원됨 |
Premium Storage 계정의 디스크 | 지원됨 | VM의 디스크가 Premium Storage 계정과 표준 저장소 계정에 분산된 경우 대상 지역의 저장소를 동일하게 구성하기 위해 각 디스크에 대해 서로 다른 대상 저장소 계정을 선택할 수 있습니다.
표준 Managed Disks | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. Government 클라우드는 현재 지원되지 않습니다.  |  
프리미엄 Managed Disks | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. Government 클라우드는 현재 지원되지 않습니다. |
저장소 공간 | 지원됨 |         
미사용 암호화(SSE) | 지원됨 | 캐시 및 대상 저장소 계정에 대해 SSE 사용 저장소 계정을 선택할 수 있습니다.     
ADE(Azure Disk Encryption) | 지원되지 않음 |
디스크 핫 추가/제거 | 지원되지 않음 | VM에서 데이터 디스크를 추가하거나 제거하는 경우 해당 VM에 대해 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
디스크 제외 | 지원되지 않음|   임시 디스크는 기본적으로 제외됩니다.
직접 저장소 공간  | 지원되지 않음|
스케일 아웃 파일 서버  | 지원되지 않음|
LRS | 지원됨 |
GRS | 지원됨 |
RA-GRS | 지원됨 |
ZRS | 지원되지 않음 |  
콜드 및 핫 저장소 | 지원되지 않음 | 가상 머신 디스크는 콜드 및 핫 저장소에서 지원되지 않습니다.
가상 네트워크의 Azure Storage 방화벽  | 아니오 | 복제된 데이터를 저장하는 데 사용된 캐시 저장소 계정의 특정 Azure Virtual Network에 대한 액세스 허용은 지원되지 않습니다.
범용 V2 저장소 계정(핫 및 쿨 계층 모두) | 아니오 | 범용 V1 Storage 계정에 비해 상당한 트랜잭션 비용 증가

>[!IMPORTANT]
> [Linux](../virtual-machines/linux/disk-scalability-targets.md) 또는 [Windows](../virtual-machines/windows/disk-scalability-targets.md) 가상 머신에 대한 VM 디스크 확장성 및 성능 목표를 확인하여 성능 문제를 피해야 합니다. 기본 설정을 따르는 경우 Site Recovery가 원본 구성에 따라 필요한 디스크 및 저장소 계정을 만듭니다. 사용자 고유의 설정을 사용자 지정하고 선택하는 경우 소스 VM의 디스크 확장성 및 성능 목표를 준수하는지 확인합니다.

## <a name="support-for-network-configuration"></a>네트워크 구성 지원
**구성** | **지원됨/지원되지 않음** | **설명**
--- | --- | ---
NIC(네트워크 인터페이스) | 특정 Azure VM 크기에서 지원되는 최대 NIC 수 | NIC는 VM이 테스트 장애 조치 또는 장애 조치 작업의 일부로 만들어질 때 생성됩니다. 장애 조치 VM의 NIC 수는 복제를 사용하도록 설정한 당시에 원본 VM의 NIC 수에 따라 다릅니다. 복제를 사용하도록 설정한 후 NIC를 추가/제거하는 경우 이는 장애 조치 VM의 NIC 수에 영향을 주지 않습니다.
인터넷 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결해야 합니다.
내부 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결해야 합니다.
공용 IP| 지원됨 | 기존 공용 IP를 NIC에 연결하거나, 복구 계획에서 Azure Automation 스크립트를 사용하여 공용 IP를 만들어 NIC에 연결해야 합니다.
NIC의 NSG(Resource Manager)| 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 NIC에 연결해야 합니다.  
서브넷의 NSG(Resource Manager 및 클래식)| 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 서브넷에 연결해야 합니다.
VM의 NSG(클래식)| 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 NIC에 연결해야 합니다.
예약된 IP(고정 IP)/원본 IP 유지 | 지원됨 | 원본 VM의 NIC에 고정 IP 구성이 있고 대상 서브넷에서 동일한 IP를 사용할 수 있는 경우 해당 IP가 장애 조치 VM에 할당됩니다. 대상 서브넷에서 동일한 IP를 사용할 수 없는 경우 서브넷의 사용 가능한 IP 중 하나가 이 VM용으로 예약됩니다. '복제된 항목 > 설정 > Compute 및 네트워크 > 네트워크 인터페이스'에서 원하는 고정 IP를 지정할 수 있습니다. NIC를 선택하고 원하는 서브넷 및 IP를 지정할 수 있습니다.
동적 IP| 지원됨 | 원본 VM의 NIC에 동적 IP 구성이 있는 경우 장애 조치 VM의 NIC도 기본적으로 동적으로 설정됩니다. '복제된 항목 > 설정 > Compute 및 네트워크 > 네트워크 인터페이스'에서 원하는 고정 IP를 지정할 수 있습니다. NIC를 선택하고 원하는 서브넷 및 IP를 지정할 수 있습니다.
Traffic Manager 통합 | 지원됨 | 트래픽이 평소에는 원본 지역의 끝점으로 라우팅되고 장애 조치 시에는 대상 지역의 끝점으로 라우팅되도록 Traffic Manager를 미리 구성할 수 있습니다.
Azure 관리 DNS | 지원됨 |
사용자 지정 DNS  | 지원됨 |    
인증되지 않은 프록시 | 지원됨 | [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.    
인증된 프록시 | 지원되지 않음 | VM에서 아웃바운드 연결에 인증된 프록시를 사용하는 경우 Azure Site Recovery를 사용하여 VM을 복제할 수 없습니다.    
온-프레미스를 사용하는 사이트 간 VPN(ExpressRoute 사용 또는 사용 안 함)| 지원됨 | Site Recovery 트래픽이 온-프레미스로 라우팅되지 않도록 UDR 및 NSG가 구성되어 있는지 확인합니다. [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.  
VNet 간 연결 | 지원됨 | [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.  
Virtual Network 서비스 엔드포인트 | 지원됨 | 가상 네트워크의 Azure Storage 방화벽이 지원되지 않습니다. 복제된 데이터를 저장하는 데 사용된 캐시 저장소 계정의 특정 Azure Virtual Network에 대한 액세스 허용은 지원되지 않습니다.
가속 네트워킹 | 지원되지 않음 | 가속 네트워킹을 사용하는 VM은 복제할 수 있지만 장애 조치 VM은 가속 네트워킹을 사용할 수 없습니다. 가속 네트워킹도 장애 복구(failback) 시 원본 VM에 사용되지 않습니다.


## <a name="next-steps"></a>다음 단계
- [Azure VM 복제를 위한 네트워킹 지침](site-recovery-azure-to-azure-networking-guidance.md)에 대해 자세히 알아보기
- [Azure VM을 복제](site-recovery-azure-to-azure.md)하여 워크로드 보호 시작
