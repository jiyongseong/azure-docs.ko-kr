---
title: Azure Site Recovery를 사용하여 보조 사이트에 VMM 클라우드의 Hyper-V VM 복제에 대한 지원 매트릭스 | Microsoft Docs
description: Azure Site Recovery를 사용하여 VMM 클라우드의 Hyper-V VM을 보조 사이트에 복제하는 기능 지원에 대해 요약합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 767b0e76b73c082ddb75374f51700b85272f713e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-hyper-v-vms-to-a-secondary-site"></a>Hyper-V VM을 보조 사이트에 복제하기 위한 지원 매트릭스

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 Hyper-V VM을 보조 사이트에 복제할 때 지원되는 기능을 요약합니다. Hyper-V VM을 Azure에 복제하려면 [이 지원 매트릭스](hyper-v-azure-support-matrix.md)를 검토하세요.

> [!NOTE]
> Hyper-V 호스트를 VMM 클라우드에서 관리하는 경우에만 보조 사이트에 복제할 수 있습니다.

  

## <a name="host-servers"></a>호스트 서버

**운영 체제** | **세부 정보**
--- | ---
Windows Server 2012 R2 | 서버가 최신 업데이트를 실행해야 합니다.
Windows Server 2016 |  Windows Server 2016 및 2012 R2 호스트가 혼합된 VMM 2016 클라우드는 현재 지원되지 않습니다.<br/><br/> System Center 2012 R2 VMM 2012 R2에서 System Center 2016으로 업그레이드된 배포는 현재 지원되지 않습니다.


## <a name="replicated-vm-support"></a>복제된 VM 지원

다음 표에서는 Site Recovery를 사용하여 복제된 컴퓨터에 대한 운영 체제 지원을 요약합니다. 워크로드를 지원되는 운영 체제에서 실행할 수 있습니다.

**Windows 버전** | **Hyper-V(VMM 포함)**
--- | ---
Windows Server 2016 | Windows Server 2016에서 [Hyper-V가 지원하는](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) 게스트 운영 체제 
Windows Server 2012 R2 | Windows Server 2012 R2에서 [Hyper-V가 지원하는](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) 게스트 운영 체제

## <a name="linux-machine-storage"></a>Linux 컴퓨터 저장소

다음 저장소를 포함한 Linux 컴퓨터만을 복제할 수 있습니다.

- 파일 시스템(EXT3, ETX4, ReiserFS, XFS)
- 다중 경로 소프트웨어 장치 매퍼
- 볼륨 관리자(LVM2)
- HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다.
- ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.

## <a name="network-configuration---hostguest-vm"></a>네트워크 구성 - 호스트/게스트 VM

**구성** | **지원됨**  
--- | --- 
호스트 - NIC 팀 | 예 
호스트 - VLAN | 예 
호스트 - IPv4 | 예 
호스트 - IPv6 | 아니요 
게스트 VM - NIC 팀 | 아니요
게스트 VM - IPv4 | 예
게스트 VM - IPv6 | 아니요
게스트 VM - Windows/Linux - 고정 IP 주소 | 예
게스트 VM - 다중 NIC | 예


## <a name="storage"></a>Storage

### <a name="host-storage"></a>호스트 저장소

**Storage(호스트)** | **지원됨**
--- | --- 
NFS | 해당 없음
SMB 3.0 |  예
SAN(ISCSI) | 예
다중 경로(MPIO) | 예

### <a name="guest-or-physical-server-storage"></a>게스트 또는 물리적 서버 저장소

**구성** | **지원됨**
--- | --- | 
VMDK |  해당 없음
VHD/VHDX | 예(최대 16개 디스크)
2세대 VM | 예
공유 클러스터 디스크 | 아니요
암호화된 디스크 | 아니요
UEFI| 해당 없음
NFS | 아니요
SMB 3.0 | 아니오
RDM | 해당 없음
디스크 > 1TB | 예
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM | 예
저장소 공간 | 예
디스크 핫 추가/제거 | 아니오
디스크 제외 | 예
다중 경로(MPIO) | 예

## <a name="vaults"></a>자격 증명 모음

**작업** | **지원됨**
--- | --- 
리소스 그룹 간에 자격 증명 모음 이동(동일 구독 내 또는 구독 간에) |  아니요
리소스 그룹 간에 저장소, 네트워크, Azure VM 이동(동일 구독 내 또는 구독 간에) | 아니요

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery 공급자

공급자는 VMM 서버 간의 통신을 조정합니다. 

**최신** | **업데이트**
--- | --- | --- | --- | ---
5.1.19([포털에서 사용 가능](http://aka.ms/downloaddra)) | [최신 기능 및 수정](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>다음 단계

[VMM 클라우드의 Hyper-V VM에서 보조 사이트로 복제](tutorial-vmm-to-vmm.md)

