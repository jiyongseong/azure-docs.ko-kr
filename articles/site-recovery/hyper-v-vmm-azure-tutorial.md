---
title: Azure Site Recovery를 통한 VMM 클라우드의 온-프레미스 Hyper-V VM에서 Azure로의 재해 복구 설정 | Microsoft Docs
description: Azure Site Recovery 서비스를 통한 System Center VMM 클라우드의 온-프레미스 Hyper-V VM에서 Azure로의 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8bbbe0a5aca20222ff7385be9d0ecf0a4224d5c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>VMM 클라우드의 온-프레미스 Hyper-V VM에서 Azure로의 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 온-프레미스 Hyper-V VM의 Azure로의 재해 복구를 설정하는 방법을 설명합니다. 이 자습서에는 System Center VMM(Virtual Machine Manager)을 통해 관리되는 Hyper-V VM 관련 내용이 포함되어 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 복제 원본 및 대상을 선택합니다.
> * 온-프레미스 Site Recovery 구성 요소 및 대상 복제 환경을 포함하여 원본 복제 환경을 설정합니다.
> * VMM VM 네트워크와 Azure 가상 네트워크 간의 매핑을 위한 네트워크 매핑을 설정합니다.
> * 복제 정책 만들기
> * VM에 대한 복제 사용

이 문서는 시리즈의 세 번째 자습서입니다. 이 자습서에서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. [온-프레미스 Hyper-V 준비](tutorial-prepare-on-premises-hyper-v.md)

시작하기 전에 이 재해 복구 시나리오용 [아키텍처를 검토](concepts-hyper-v-to-azure-architecture.md)하는 것이 좋습니다.



## <a name="select-a-replication-goal"></a>복제 목표 선택

1. **모든 서비스** > **Recovery Services 자격 증명 모음**에서 이러한 자습서에서 사용할 자격 증명 모음 이름인 **ContosoVMVault**를 클릭합니다.
2. **시작**에서**Site Recovery**를 클릭합니다. 그런 다음 **인프라 준비**를 클릭합니다.
3. **보호 목표** > **컴퓨터가 있는 위치**에서 **온-프레미스**를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
5. **컴퓨터가 가상화되어 있습니까?** 에서 **예, Hyper-V 사용**을 선택합니다.
6. **System Center VMM을 사용하고 있습니까?** 에서 **예**를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 목표](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>원본 환경 설정

원본 환경을 설정할 때는 Azure Site Recovery 공급자와 Azure Recovery Services 에이전트를 설치하고 자격 증명 모음에 온-프레미스 서버를 등록합니다. 

1. **인프라 준비**에서 **원본**을 클릭합니다.
2. **소스 준비**에서 **+VMM**을 클릭하여 VMM 서버를 추가합니다. **서버 추가**의 **서버 유형**에서 **System Center VMM 서버**가 표시되는지 확인합니다.
3. Microsoft Azure Site Recovery Provider용 설치 관리자를 다운로드합니다.
4. 자격 증명 모음 등록 키를 다운로드합니다. 공급자 설정을 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.
5. Recovery Services 에이전트를 다운로드합니다.

    ![다운로드](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>VMM 서버에 공급자 설치

1. Azure Site Recovery Provider 설치 마법사 > **Microsoft 업데이트**에서 Provider 업데이트를 확인하려면 Microsoft 업데이트를 사용하는 옵션을 선택합니다.
2. **설치**에서 Provider의 기본 설치 위치를 적용하고 **설치**를 클릭합니다. 
3. 설치 후에 Microsoft Azure Site Recovery 등록 마법사 > **자격 증명 모음 설정**에서 **찾아보기**를 클릭하고 **키 파일**에서 다운로드한 자격 증명 모음 키 파일을 선택합니다.
4. Azure Site Recovery 구독 및 자격 증명 모음 이름(**ContosoVMVault**)을 지정합니다. 자격 증명 모음에서 식별할 수 있도록 VMM 서버의 식별 이름을 지정합니다.
5. **프록시 설정**에서 **프록시 서버 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
6. 데이터를 암호화하는 데 사용되는 인증서의 기본 위치를 적용합니다. 장애 조치(failover) 시에는 암호화된 데이터의 암호가 해독됩니다.
7. **클라우드 메타데이터 동기화**에서 **클라우드 메타데이터를 Site Recovery 포털에 동기화하세요.** 를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 그런 후에 **등록**을 클릭합니다.
8. 서버를 자격 증명 모음에 등록한 후 **마침**을 클릭합니다.

등록이 완료되면 Azure Site Recovery에서 서버의 메타데이터가 검색되며 VMM 서버가 **Site Recovery 인프라**에 표시됩니다.

### <a name="install-the-recovery-services-agent"></a>Recovery Services 에이전트 설치

복제할 VM이 포함된 각 Hyper-V 호스트에 에이전트를 설치합니다.

1. Microsoft Azure Recovery Services 에이전트 설치 마법사 > **필수 구성 요소 확인**에서 **다음**을 클릭합니다. 누락된 필수 구성 요소는 자동으로 설치됩니다.
2. **설치 설정**에서 설치 위치 및 캐시 위치를 적용합니다. 캐시 드라이브에는 5GB 이상의 저장 공간이 필요합니다. 600GB 이상의 여유 공간이 있는 드라이브를 사용하는 것이 좋습니다. **설치**를 클릭합니다.
3. **설치**에서, 설치가 완료되면 **닫기**를 클릭하여 마법사를 마칩니다.

    ![에이전트 설치](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>대상 환경 설정

1. **인프라 준비** > **대상**을 클릭합니다.
2. 구독을 선택하고 장애 조치(failover) 후 Azure VM이 생성될 리소스 그룹 **ContosoRG**를 선택합니다.
3. **Resource Manager"** 배포 모델을 선택합니다.

Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.


## <a name="configure-network-mapping"></a>네트워크 매핑 구성

1. **Site Recovery 인프라** > **네트워크 매핑** > **네트워크 매핑**에서 **+네트워크 매핑** 아이콘을 클릭합니다.
2. **네트워크 매핑 추가**에서 원본 VMM 서버를 선택합니다. 대상으로 **Azure**를 선택합니다.
3. 장애 조치(failover) 후 구독과 배포 모델을 확인합니다.
4. **원본 네트워크**에서 원본 온-프레미스 VM 네트워크를 선택합니다.
5. **대상 네트워크**에서 장애 조치(failover) 후 만들어지는 복제본 Azure VM가 위치할 Azure 네트워크를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![네트워크 매핑](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>복제 정책 설정

1. **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 클릭합니다.
2. **정책 만들기 및 연결**에서 정책 이름을 **ContosoReplicationPolicy**로 지정합니다.
3. 기본 설정을 그대로 적용하고 **확인**을 클릭합니다.
    - **복사 빈도**는 초기 복제 후 5분마다 델타 데이터가 복제됨을 나타냅니다.
    - **복구 지점 보존**은 모든 복구 지점의 보존 기간이 2시간임을 나타냅니다
    - **앱 일치 스냅숏 빈도**는 앱 일치 스냅숏을 포함하는 복구 지점이 1시간마다 만들어짐을 나타냅니다.
    - **초기 복제 시작 시간**은 초기 복제가 즉시 시작됨을 나타냅니다.
    - **Azure에 저장된 데이터 암호화** -기본 설정인 **해제**는 Azure의 미사용 데이터는 암호화되지 않음을 나타냅니다.
4. 정책이 만들어지고 나면 **확인**을 클릭합니다. 새 정책을 만들면 새 정책이 자동으로 VMM 클라우드에 연결됩니다.

## <a name="enable-replication"></a>복제 사용

1. **응용 프로그램 복제**에서 **원본**을 클릭합니다. 
2. **원본**에서 VMM 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **대상**에서 대상(Azure) 및 자격 증명 모음 구독을 확인하고 **Resource Manager** 모델을 선택합니다.
4. **contosovmsacct1910171607** 저장소 계정과 **ContosoASRnet** Azure 네트워크를 선택합니다.
5. **가상 머신** > **선택**에서 복제할 VM을 선택합니다. 그런 후 **OK**를 클릭합니다.

 **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료되면 초기 복제가 완료되고 VM에서 장애 조치를 수행할 준비가 됩니다.


## <a name="next-steps"></a>다음 단계
[재해 복구 드릴 실행](tutorial-dr-drill-azure.md)
