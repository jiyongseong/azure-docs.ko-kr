---
title: "Azure Site Recovery를 사용하여 보조 사이트에 VMware VM 또는 물리적 서버의 재해 복구 설정 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 보조 사이트에 VMware VM 또는 Windows 및 Linux 물리적 서버의 재해 복구를 설정하는 방법을 알아봅니다."
services: site-recovery
author: nsoneji
manager: gauarvd
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 7d172b89a0846c8ccf19896400e127a3bec0b55e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>보조 사이트에 온-프레미스 VMware 가상 머신 또는 물리적 서버의 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md)의 InMage Scout는 온-프레미스 VMware 사이트 간 실시간 복제를 제공합니다. InMage Scout는 Azure Site Recovery 서비스 구독에 포함되어 있습니다. 


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 모든 구성 요소에 대한 지원 요구 사항을 [검토합니다](vmware-physical-secondary-support-matrix.md).
- 복제하려는 컴퓨터에서 [복제된 컴퓨터 지원](vmware-physical-secondary-support-matrix.md#replicated-vm-support)을 준수하는지 확인합니다.


## <a name="create-a-vault"></a>자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>보호 목표 선택

복제할 항목 및 복제 대상 위치를 선택합니다.

1. **Site Recovery** > **인프라 준비** > **보호 목표**를 차례로 클릭합니다.
2. **To recovery site(대상 복구 사이트)** > **Yes, with VMware vSphere Hypervisor(예, VMware vSphere 하이퍼바이저 포함)**를 차례로 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **Scout 설치**에서 InMage Scout 8.0.1 GA 소프트웨어 및 등록 키를 다운로드합니다. 모든 구성 요소에 대한 설치 파일은 다운로드한 zip 파일에 포함되어 있습니다.

## <a name="download-and-install-component-updates"></a>구성 요소 업데이트 다운로드 및 설치

 최신 [업데이트](#updates)를 검토하고 설치합니다. 업데이트는 다음 순서로 서버에 설치해야 합니다.

1. RX 서버(있는 경우)
2. 서버 구성
3. 프로세스 서버
4. 마스터 대상 서버
5. vContinuum 서버.
6. 원본 서버(Windows 및 Linux 서버 모두)

다음과 같이 업데이트를 설치합니다.

> [!NOTE]
>모든 Scout 구성 요소의 파일 업데이트 버전이 업데이트 .zip 파일에서 같지 않을 수 있습니다. 이전 버전인 경우는 이 업데이트의 이전 업데이트 이후에 구성 요소가 변경되지 않았음을 나타냅니다.

[update](https://aka.ms/asr-scout-update6) zip 파일을 다운로드합니다. 파일에는 다음 구성 요소가 포함됩니다. 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA update4 bits for RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. .zip 파일의 압축을 풉니다.
2. **RX 서버**: **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz**를 RX 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.
3. **구성 서버/프로세스 서버**: **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe**를 구성 서버 및 프로세스 서버에 복사합니다. 실행하려면 두 번 클릭합니다.<br>
4. **Windows 마스터 대상 서버**: 통합 에이전트를 업데이트하려면 **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe**를 서버에 복사합니다. 실행하려면 두 번 클릭합니다. 또한 원본 서버에도 동일한 통합 에이전트 업데이트를 적용할 수 있습니다. 원본 서버가 업데이트 4로 업데이트되어 있지 않은 경우 통합 에이전트를 업데이트해야 합니다.
  **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**로 준비된 마스터 대상에는 이 업데이트를 적용할 필요가 없습니다. 모든 최신 변경 내용을 포함하는 새 GA 설치 관리자이기 때문입니다.
5. **vContinuum 서버**: **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe**를 서버에 복사합니다.  VContinuum 마법사를 닫았는지 확인합니다. 실행하려면 파일을 두 번 클릭합니다.
    **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**로 준비된 마스터 대상에는 이 업데이트를 적용할 필요가 없습니다. 모든 최신 변경 내용을 포함하는 새 GA 설치 관리자이기 때문입니다.
6. **Linux 마스터 대상 서버**: 통합 에이전트를 업데이트하려면 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz**를 마스터 대상 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.
7. **Windows 원본 서버**: 통합 에이전트를 업데이트하려면 **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe**를 원본 서버에 복사합니다. 실행하려면 파일을 두 번 클릭합니다. 
    업데이트 4로 이미 업데이트되었거나 원본 에이전트가 최신 기본 설치 관리자인 **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**를 사용하여 설치되면 원본 서버에서 업데이트 5 에이전트를 설치할 필요가 없습니다.
8. **Linux 원본 서버**: 통합 에이전트를 업데이트하려면 해당 버전의 통합 에이전트 파일을 Linux 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.  예: RHEL 6.7 64비트 서버의 경우 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz**를 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.

## <a name="enable-replication"></a>복제 사용

1. 원본과 대상 VMware 사이트 간 복제를 설정합니다.
2. 설치, 보호 및 복구에 대한 자세한 내용을 보려면 다음 문서를 참조하세요.

   * [릴리스 정보](https://aka.ms/asr-scout-release-notes)
   * [호환성 매트릭스](https://aka.ms/asr-scout-cm)
   * [사용자 가이드](https://aka.ms/asr-scout-user-guide)
   * [RX 사용자 가이드](https://aka.ms/asr-scout-rx-user-guide)
   * [빠른 설치 가이드](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>업데이트

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 업데이트 6 
업데이트 날짜: 2017년 10월 12일

[Scout 업데이트 6](https://aka.ms/asr-scout-update6)를 다운로드합니다.

Scout 업데이트 6는 누적 업데이트입니다. 여기에는 업데이트 1에서 업데이트 5까지의 모든 수정 사항 및 아래에서 설명하는 새 수정 사항 및 개선된 기능이 포함되어 있습니다. 

#### <a name="new-platform-support"></a>새 플랫폼 지원
* 원본 Windows Server 2016에 대한 지원이 추가되었습니다.
* 다음 Linux 운영 체제에 대한 지원이 추가되었습니다.
    - RHEL(Red Hat Enterprise Linux) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* VMware Center 6.5에 대한 지원이 추가되었습니다.

> [!NOTE]
> * Windows용 기본 UA(통합 에이전트) 설치 관리자는 Windows Server 2016을 지원하도록 새로 고쳐졌습니다. 새 설치 관리자 **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**가 기본 Scout GA 패키지에 패키지됩니다(**InMage_Scout_Standard_8.0.1 GA Oct17.zip**). 지원되는 모든 Windows 버전에 동일한 설치 관리자가 사용됩니다. 
> * 기본 Windows vContinuum 및 마스터 대상 설치 관리자는 Windows Server 2016을 지원하도록 새로 고쳐졌습니다. 새 설치 관리자 **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**가 기본 Scout GA 패키지에 패키지됩니다(**InMage_Scout_Standard_8.0.1 GA Oct17.zip**). Windows 2016 마스터 대상 및 Windows 2012R2 마스터 대상을 배포하는 데 동일한 설치 관리자가 사용됩니다.
> * [자격 증명 모음 만들기](#create-a-vault)에서 설명한 대로 포털에서 GA 패키지를 다운로드합니다.
>

#### <a name="bug-fixes-and-enhancements"></a>버그 수정 및 향상된 기능
- 복제할 디스크 목록을 포함하는 Linux VM에 대한 장애 복구(Failback) 보호 실패가 구성 마지막에 비어 있습니다.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 업데이트 5
Scout 업데이트 5는 누적 업데이트입니다. 여기에는 업데이트 1에서 업데이트 4까지의 모든 수정 사항 및 아래에서 설명하는 새 수정 사항이 포함되어 있습니다.
- Site Recovery Scout 업데이트 4에서 업데이트 5로 수정된 사항은 특히 마스터 대상 및 vContinuum 구성 요소에 대한 것입니다.
- 원본, 마스터 대상, 구성, 프로세스 및 RX 서버에서 이미 업데이트 4를 실행하는 경우 마스터 대상 서버에만 적용합니다. 

#### <a name="new-platform-support"></a>새 플랫폼 지원
* SUSE Linux Enterprise Server 11 SP4(서비스 팩 4)
* SLES 11 SP4 64비트(**InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz**)는 기본 Scout GA 패키지(**InMage_Scout_Standard_8.0.1 GA.zip**)와 함께 패키지되어 있습니다. [자격 증명 모음 만들기](#create-a-vault)에서 설명한 대로 포털에서 GA 패키지를 다운로드합니다.


#### <a name="bug-fixes-and-enhancements"></a>버그 수정 및 향상된 기능

* 향상된 Windows 클러스터 지원 안정성을 위한 수정 사항:
    * 수정됨 - P2V MSCS 클러스터 디스크 중 일부가 복구 후 RAW가 됩니다.
    * 수정됨 - P2V MSCS 클러스터 복구가 디스크 순서 불일치로 인해 실패합니다.
    * 수정됨 - 디스크를 추가하는 MSCS 클러스터 작업이 디스크 크기 불일치 오류로 실패합니다.
    * 수정됨 - RDM LUN 매핑이 있는 원본 MSCS 클러스터에 대한 준비 상태를 확인할 때 크기 확인에 실패합니다.
    * 수정됨 - 단일 노드 클러스터 보호가 SCSI 불일치 문제로 인해 실패합니다. 
    * 수정됨 - 대상 클러스터 디스크가 있는 경우 P2V Windows 클러스터 서버의 다시 보호가 실패합니다. 
    
* 수정됨: 장애 복구 보호 중에 선택한 마스터 대상 서버가 보호된 원본 컴퓨터(전달 보호 중)와 동일한 ESXi 서버에 있지 않으면 vContinuum에서 장애 복구 중에 잘못된 마스터 대상 서버를 선택하고 복구 작업이 실패합니다.

> [!NOTE]
> * P2V 클러스터 수정은 Site Recovery Scout 업데이트 5로 새로 보호되는 물리적 MSCS 클러스터에만 적용할 수 있습니다. 이전 업데이트를 사용하여 보호된 P2V MSCS 클러스터에 클러스터 수정 프로그램을 설치하려면 [Site Recovery Scout 릴리스 정보](https://aka.ms/asr-scout-release-notes)의 12섹션에서 설명하는 업그레이드 단계를 수행합니다.
> * 다시 보호할 때 초기에 보호된 경우와 같이 동일한 디스크 집합이 각 클러스터 노드에서 활성 상태인 경우 물리적 MSCS 클러스터를 다시 보호하는 데 기존 대상 디스크만 다시 사용할 수 있습니다. 그렇지 않은 경우 [Site Recovery Scout 릴리스 정보](https://aka.ms/asr-scout-release-notes) 12섹션의 수동 단계에 따라 대상쪽 디스크를 올바른 데이터 저장소 경로로 이동하여 다시 보호 중에 다시 사용할 수 있습니다. 업그레이드 단계를 수행하지 않고 P2V 모드에서 MSCS 클러스터를 다시 보호하는 경우 대상 ESXi 서버에 새 디스크를 만듭니다. 데이터 저장소에서 이전 디스크를 수동으로 삭제해야 합니다.
> * 원본 SLES11 서버 또는 SLES11(모든 서비스 팩 포함) 서버가 정상적으로 다시 부팅되는 경우 다시 동기화에 대한 **루트** 디스크 복제 쌍을 수동으로 표시합니다. CX 인터페이스에는 알림이 없습니다. 다시 동기화에 대한 루트 디스크를 표시하지 않으면 데이터 무결성 문제가 발생할 수 있습니다.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 4
Scout 업데이트 4는 누적 업데이트입니다. 여기에는 업데이트 1에서 업데이트 3까지의 모든 수정 사항 및 아래에서 설명하는 새 수정 사항이 포함되어 있습니다.

#### <a name="new-platform-support"></a>새 플랫폼 지원

* vCenter/vSphere 6.0, 6.1 및 6.2에 대한 지원이 추가되었습니다.
* 다음 Linux 운영 체제에 대한 지원이 추가되었습니다.
  * Red Hat Enterprise Linux(RHEL) 7.0, 7.1 및 7.2
  * CentOS 7.0, 7.1 및 7.2
  * Red Hat Enterprise Linux(RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64비트(**InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz**)가 기본 Scout GA 패키지(**InMage_Scout_Standard_8.0.1 GA.zip**)와 함께 패키지되어 있습니다. [자격 증명 모음 만들기](#create-a-vault)에서 설명한 대로 포털에서 Scout GA 패키지를 다운로드합니다.

#### <a name="bug-fixes-and-enhancements"></a>버그 수정 및 향상된 기능

* 원치 않는 다시 동기화 문제를 방지하기 위해 다음 Linux 운영 체제 및 복제본에 대한 종료 처리가 향상되었습니다.
    * Red Hat Enterprise Linux(RHEL) 6.x
    * Oracle Linux(OL) 6.x
* Linux의 경우 통합 에이전트 설치 디렉터리의 모든 폴더 액세스 권한은 이제 로컬 사용자로만 제한되었습니다.
* Windows의 경우 SQL Server 및 SharePoint 클러스터와 같이 부하가 심한 분산 응용 프로그램에서 일반적인 분산 일관성 북마크를 발급할 때 발생한 시간 제한 문제가 해결되었습니다.
* 구성 서버 기본 설치 관리자와 관련된 로그가 수정되었습니다.
* VMware vCLI 6.0에 대한 다운로드 링크가 Windows 마스터 대상 기본 설치 관리자에 추가되었습니다.
* 장애 조치 및 재해 복구 훈련 중에 네트워크 구성 변경에 대한 추가 검사 및 로그가 추가되었습니다.
* 보존 정보가 구성 서버에 보고되지 않도록 하는 문제가 해결되었습니다.  
* 물리적 클러스터의 경우 원본 볼륨을 줄이면 vContinuum 마법사에서 볼륨 크기 조정을 실패하게 하는 문제가 해결되었습니다.
* 클러스터 디스크가 PRDM 디스크인 경우 "디스크 서명을 찾지 못했습니다" 오류로 인한 클러스터 보호 실패 문제가 해결되었습니다.
* 범위 이탈 예외로 인한 cxps 전송 서버 크래시가 수정되었습니다.
* vContinuum 마법사의 **푸시 설치** 페이지에서 서버 이름 및 IP 주소 열의 크기를 조정할 수 있습니다.
* 향상된 RX API 기능:
  * 사용 가능한 5개의 최신 공통 일관성 지점을 사용할 수 있습니다(보장됨 태그만).
  * 보호된 모든 장치에 대한 용량 및 사용 가능한 공간 세부 정보가 표시됩니다.
  * 원본 서버의 Scout 드라이버 상태가 제공됩니다.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** 기본 패키지에 포함된 항목:
    * 업데이트된 구성 서버 기본 설치 관리자(**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Windows 마스터 대상 기본 설치 관리자(**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * 모든 새 설치에서는 새 구성 서버 및 Windows 마스터 대상 GA 비트를 사용합니다.
> * 업데이트 4는 8.0.1 GA에 직접 적용할 수 있습니다.
> * 구성 서버 및 RX 업데이트는 적용된 후에 롤백할 수 없습니다.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 3

모든 사이트 복구 업데이트는 누적됩니다. 업데이트 3에는 업데이트 1 및 업데이트 2의 모든 수정 사항이 포함되어 있습니다. 업데이트3은 8.0.1 GA에 직접 적용할 수 있습니다. 구성 서버 및 RX 업데이트는 적용된 후에 롤백할 수 없습니다.

#### <a name="bug-fixes-and-enhancements"></a>버그 수정 및 향상된 기능
업데이트 3에서 해결된 문제:

* 구성 서버 및 RX 서버가 Site Recovery 자격 증명 모음에서 프록시 서버 뒤에 있으면 등록되지 않습니다.
* RPO(복구 지점 목표)에 도달하지 못한 시간 수는 상태 보고서에서 업데이트되지 않습니다.
* ESX 하드웨어 세부 정보 또는 네트워크 세부 정보에 UTF-8 문자가 포함되어 있으면 구성 서버가 RX 서버와 동기화되지 않습니다.
* 복구 후 Windows Server 2008 R2 도메인 컨트롤러가 시작되지 않습니다.
* 오프라인 동기화가 예상대로 작동하지 않습니다.
* VM 장애 조치 후에 구성 서버 콘솔에서 오랫동안 복제 쌍 삭제가 진행되지 않습니다. 사용자가 장애 복구를 완료하거나 작업을 다시 시작할 수 없습니다.
* 일관성 작업에 의한 전체 스냅숏 작업이 최적화되어 SQL Server 클라이언트와 같은 응용 프로그램의 연결 해제를 줄일 수 있습니다.
* 일관성 도구(VACP.exe) 성능이 향상되었습니다. Windows에서 스냅숏을 만드는 데 필요한 메모리 사용량이 줄었습니다.
* 암호가 16자를 초과하면 푸시 설치 서비스가 충돌합니다.
* 자격 증명이 수정되면 vContinuum에서 새 vCenter 자격 증명을 확인하고 요청하지 않습니다.
* Linux에서 마스터 대상 캐시 관리자(cachemgr)는 프로세스 서버에서 파일을 다운로드하지 않습니다. 복제 쌍 제한이 발생합니다.
* 물리적 장애 조치 클러스터(MSCS) 디스크 순서가 모든 노드에서 동일하지 않으면 클러스터 볼륨 중 일부에 대해 복제가 설정되지 않습니다. 이 수정 프로그램을 이용하려면 클러스터를 다시 보호해야 합니다.  
* RX 서버가 Scout 7.1에서 Scout 8.0.1로 업그레이드된 후에 SMTP 기능이 예상대로 작동하지 않습니다.
* 롤백 작업 완료에 걸린 시간을 추적하기 위해 롤백 작업에 대한 로그에 더 많은 통계가 추가되었습니다.
* 원본 서버에서 Linux 운영 체제에 대한 지원이 추가되었습니다.
  * RHEL(Red Hat Enterprise Linux) 6 업데이트 7
  * CentOS 6 업데이트 7
* 이제 구성 서버 및 RX 서버 콘솔에 비트맵 모드로 전환되는 쌍에 대한 알림이 표시됩니다.
* 다음 보안 수정이 RX에 추가되었습니다.
    * 매개 변수 변조를 통한 권한 부여 무시: 적용할 수 없는 사용자에 대한 액세스 권한이 제한되었습니다.
    * 교차 사이트 요청 위조: 페이지 토큰 개념이 구현되었으며 모든 페이지에 대해 임의로 생성됩니다. 즉, 동일한 사용자에 대한 단일 로그인 인스턴스만 있고 페이지 새로 고침은 작동하지 않습니다. 대신 대시보드로 리디렉션됩니다.
    * 악의적인 파일 업로드: 파일이 특정 확장자로 제한됩니다(z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, and zip).
    * 영구 사이트 간 스크립팅: 입력 유효성 검사가 추가되었습니다.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 2(2015년 12월 3일 업데이트)

업데이트 2에서 수정 사항은 다음과 같습니다.

* **구성 서버**: 구성 서버가 Site Recovery에 등록되었을 때 31일 체험 계량 기능이 예상대로 작동하지 않는 문제가 해결되었습니다.
* **통합 에이전트**: 버전 8.0에서 8.0.1로 업그레이드하는 동안 업데이트가 마스터 대상 서버에 설치되지 않는 업데이트 1 문제가 해결되었습니다.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 1
업데이트 1에는 다음 버그 수정 및 새로운 기능이 포함됩니다.

* 서버 인스턴스 당 31일간 무료 보호됩니다. 이 경우 기능을 테스트하거나 개념 증명을 설정할 수 있습니다.
* 장애 조치 및 장애 복구를 포함하여 서버의 모든 작업은 처음 31일 동안 추가 비용 없이 사용해 볼 수 있습니다. 이 시간은 Site Recovery Scout를 사용하여 서버를 처음 보호할 때부터 시작됩니다. 32일째부터는 고객 소유 사이트에 대한 Azure Site Recovery 보호에 적용되는 표준 인스턴스 요율로 계산된 대금이 보호된 각 서버에 청구됩니다.
* 현재 청구되는 보호된 서버의 수는 언제든지 자격 증명의 **대시보드** 페이지에서 제공됩니다.
* vSphere 명령줄 인터페이스(vCLI) 5.5 업데이트 2에 대한 지원이 추가되었습니다.
* 원본 서버에서 다음 Linux 운영 체제에 대한 지원이 추가되었습니다.
    * RHEL 6 업데이트 6
    * RHEL 5 업데이트 11
    * CentOS 6 업데이트 6
    * CentOS 5 업데이트 11
* 다음과 같은 문제를 해결하는 버그 수정:
  * 서버 구성 또는 RX 서버에 대한 자격 증명 모음 등록이 실패합니다.
  * 클러스터된 VM을 다시 시작할 때 이 VM을 다시 보호하면 클러스터 볼륨이 예상대로 표시되지 않습니다.
  * 마스터 대상 서버가 온-프레미스 프로덕션 VM과 다른 ESXi 서버에서 호스팅되는 경우 장애 복구가 실패합니다.
  * 구성 파일 권한은 8.0.1로 업그레이드할 때 변경됩니다. 이 변경은 보호 및 운영에 영향을 줍니다.
  * 다시 동기화 임계값이 예상대로 적용되지 않으므로 일치하지 않는 복제 동작이 발생합니다.
  * 구성 서버 콘솔에 RPO 설정이 제대로 표시되지 않습니다. 압축되지 않은 데이터 값은 압축된 값을 잘못 표시합니다.
  * 제거 작업이 vContinuum 마법사에서 예상대로 삭제되지 않고, 복제가 구성 서버 콘솔에서 삭제되지 않습니다.
  * vContinuum 마법사에서 MSCS VM을 보호하는 동안 디스크 보기에서 **세부 정보**를 클릭하면 디스크가 자동으로 선택 취소됩니다.
  * P2V(Physical-to-Virtual) 시나리오에서 CIMnotify 및 CqMgHost와 같은 필수 HP 서비스는 VM 복구에서 수동으로 이동되지 않습니다. 이 문제로 인해 부팅 시간이 늘어납니다.
  * 마스터 대상 서버에 27개 이상의 디스크가 있으면 Linux VM 보호가 실패합니다.

