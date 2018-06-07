---
title: "Azure Site Recovery를 사용하여 온-프레미스 사이트 간 Hyper-V VM 재해 복구 설정 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 온-프레미스 사이트 간의 Hyper-V VM에 대한 재해 복구를 설정하는 방법을 알아봅니다."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 13dcc0794c1d89bd27c79cbe6636397da4f008f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>보조 온-프레미스 사이트에 Hyper-V VM 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 문서에서는 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 온-프레미스 Hyper-V VM에 대한 재해 복구를 보조 사이트에 설정하는 방법을 보여 줍니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 온-프레미스 VMM 서버 및 Hyper-V 호스트 준비
> * Site Recovery에 대한 Recovery Services 자격 증명 모음 만들기 
> * 원본 및 대상 복제 환경 설정 
> * 네트워크 매핑 설정 
> * 복제 정책 만들기
> * VM에 대한 복제 사용

## <a name="prerequisites"></a>필수 조건

이 시나리오를 완료하려면:

- [시나리오 아키텍처 및 구성 요소](hyper-v-vmm-architecture.md)를 검토합니다.
- VMM 서버 및 Hyper-V 호스트에서 [지원 요구 사항](hyper-v-vmm-secondary-support-matrix.md)을 준수하는지 확인합니다.
- 복제하려는 VM에서 [복제된 컴퓨터 지원](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support)을 준수하는지 확인합니다.
- 네트워크 매핑용 VMM 서버를 준비합니다.

### <a name="prepare-for-network-mapping"></a>네트워크 매핑을 준비

[네트워크 매핑](hyper-v-vmm-network-mapping.md)에서는 원본 및 대상 클라우드의 온-프레미스 VMM VM 네트워크 사이를 매핑합니다. 매핑은 다음을 수행합니다.

- 장애 조치 후 VM을 적절한 대상 VM 네트워크에 연결합니다. 
- 복제본 VM을 대상 Hyper-V 호스트 서버에 최적으로 배치합니다. 
- 네트워크 매핑을 구성하지 않으면 장애 조치 후 복제본 VM이 VM 네트워크에 연결되지 않습니다.

VMM을 다음과 같이 준비합니다.

1. 원본 및 대상 VMM 서버에 [VMM 논리 네트워크](https://docs.microsoft.com/system-center/vmm/network-logical)가 있는지 확인합니다.
    - 원본 서버의 논리 네트워크는 Hyper-V 호스트가 있는 원본 클라우드와 연결되어 있어야 합니다.
    - 대상 서버의 논리 네트워크는 대상 클라우드와 연결되어 있어야 합니다.
1. 원본 및 대상 VMM 서버에 [VM 네트워크](https://docs.microsoft.com/system-center/vmm/network-virtual)가 있는지 확인합니다. VM 네트워크는 각 위치의 논리적 네트워크에 연결되어야 합니다.
2. 원본 Hyper-V 호스트의 VM을 원본 VM 네트워크에 연결합니다. 


## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.
2. **대상 복구 사이트**를 선택하고 **예, Hyper-V 사용**을 선택합니다.
3. VMM을 사용하여 Hyper-V 호스트를 관리하도록 지정하려면 **예**를 선택합니다.
4. 보조 VMM 서버가 있는 경우 **예**를 선택합니다. 단일 VMM 서버의 클라우드 간에 복제를 배포하는 경우 **아니요**를 클릭합니다. 그런 후 **OK**를 클릭합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

VMM 서버에 Azure Site Recovery 공급자를 설치하고 서버를 검색하여 자격 증명 모음에 등록합니다.

1. **인프라 준비** > **원본**을 클릭합니다.
2. **소스 준비**에서 **+VMM**을 클릭하여 VMM 서버를 추가합니다.
3. **서버 추가**의 **서버 유형**에서 **System Center VMM 서버**가 표시되는지 확인합니다.
4. Azure Site Recovery 공급자 설치 파일을 다운로드합니다.
5. 등록 키를 다운로드합니다. 공급자를 설치할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![원본 설정](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. 각 VMM 서버에 공급자를 설치합니다. Hyper-V 호스트 서버에 명시적으로 설치할 필요는 없습니다.

### <a name="install-the-azure-site-recovery-provider"></a>Azure Site Recovery 공급자 설치

1. 각 VMM 서버에서 공급자 설치 파일을 실행합니다. VMM이 클러스터에 배포되었으면 처음 설치할 때 다음을 수행합니다.
    -  활성 노드에 공급자를 설치하고, 설치를 완료하여 자격 증명 모음에 해당 VMM 서버를 등록합니다.
    - 그런 후에 다른 노드에 공급자를 설치합니다. 모든 클러스터 노드는 동일한 버전의 공급자를 실행해야 합니다.
2. 설치 중에 몇 가지 필수 조건 검사가 실행되며 VMM 서비스를 중지하기 위한 권한이 요청됩니다. 설정이 완료되면 VMM 서비스가 자동으로 다시 시작됩니다. VMM 클러스터에 설치하는 경우 클러스터 역할을 중지하라는 메시지가 표시됩니다.
3. **Microsoft Update**에서 Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 지정할 수 있습니다.
4. **설치**에서 기본 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.
5. 설치가 완료되면 **등록**을 클릭하여 자격 증명 모음에 서버를 등록합니다.

    ![설치 위치](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다. **다음**을 클릭합니다.
7. **프록시 연결**에서 VMM 서버에서 실행되는 공급자를 Azure에 연결하는 방법을 지정합니다.
   - 공급자가 인터넷에 직접 또는 프록시를 통해 연결되도록 지정할 수 있습니다. 필요에 따라 프록시 설정을 지정합니다.
   - 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. 실행 계정 설정은 VMM 콘솔 > **설정** > **보안** > **실행 계정**에서 수정할 수 있습니다.
   - 변경 내용을 업데이트하려면 VMM 서비스를 다시 시작합니다.
8. **등록 키**에서 VMM 서버에 다운로드하여 복사한 키를 선택합니다.
9. 암호화 설정은 이 시나리오와 관련이 없습니다. 
10. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터에서 VMM 클러스터 역할 이름을 지정합니다.
11. **클라우드 메타데이터 동기화**에서 VMM 서버의 모든 클라우드에 대한 메타데이터를 동기화할지 여부를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않으려면 이 설정을 선택하지 않습니다. VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.
12. **다음** 을 클릭하여 프로세스를 완료합니다. 등록 후에 Site Recovery에서 VMM 서버의 메타데이터를 검색합니다. 서버가 자격 증명 모음의 **서버** > **VMM 서버**에 표시됩니다.
13. 서버가 자격 증명 모음에 표시되면 **원본** > **원본 준비**에서 VMM 서버를 선택하고 Hyper-V 호스트가 있는 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.


## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 VMM 서버 및 클라우드를 선택합니다.

1. **인프라 준비** > **대상**을 차례로 클릭하고 대상 VMM 서버를 선택합니다.
2. Site Recovery와 동기화된 VMM 클라우드가 표시됩니다. 대상 클라우드를 선택합니다.

   ![대상](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>복제 정책 설정

시작하기 전에 정책을 사용하는 모든 호스트가 동일한 운영 체제인지 확인합니다. 호스트에서 다른 버전의 Windows Server를 실행하는 경우 여러 복제 정책이 필요합니다.

1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 차례로 클릭합니다.
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다. 소스 및 대상 형식은 **Hyper-V**여야 합니다.
3. **Hyper-V 호스트 버전**에서 호스트에서 실행되는 운영 체제를 선택합니다.
4. **인증 형식** 및 **인증 포트**에서 기본 및 복구 Hyper-V 호스트 서버 간에 트래픽을 인증하는 방법을 지정합니다.
    - 작동하는 Kerberos 환경이 구성되어 있지 않으면 **인증서** 를 선택합니다. Azure Site Recovery가 HTTPS 인증을 위한 인증서를 자동으로 구성합니다. 수동으로 수행할 작업은 없습니다.
    - 기본적으로 Hyper-V 호스트 서버의 Windows 방화벽에서 포트 8083과 8084(인증서용)가 열립니다.
    - **Kerberos**를 선택하면 호스트 서버의 상호 인증에 Kerberos 티켓이 사용됩니다. Kerberos는 Windows Server 2012 R2 이상에서 실행되는 Hyper-V 호스트 서버에만 관련이 있습니다.
1. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초마다, 5분마다 또는 15분마다)를 지정합니다.
2. **복구 지점 보존**에서 각 복구 지점에 대한 보존 기간을 시간 단위로 지정합니다. 복제된 컴퓨터는 이 기간 내의 모든 지점으로 복구할 수 있습니다.
3. **앱 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1~12시간)를 지정합니다. Hyper-V에서는 다음 두 가지 유형의 스냅숏을 사용합니다.
    - **표준 스냅숏**: 전체 가상 머신의 증분 스냅숏을 제공합니다.
    - **앱 일치 스냅숏**: VM 내의 응용 프로그램 데이터에 대한 특정 시점 스냅숏을 만듭니다. VSS(볼륨 섀도 복사본 서비스)는 스냅숏을 만들 때 앱이 일관된 상태를 유지하도록 합니다. 앱 일치 스냅숏을 사용하면 원본 VM에서 앱 성능에 영향을 줍니다. 구성한 추가 복구 지점 수보다 작은 값을 설정합니다.
4. **데이터 전송 압축**에서 전송되는 복제 데이터를 압축해야 하는지 여부를 지정합니다.
5. **복제본 VM 삭제**를 선택하여 소스 VM에 대해 보호를 사용하지 않도록 설정하는 경우 복제본 가상 머신이 삭제되도록 지정합니다. 이 설정을 사용하도록 지정하는 경우 보호가 설정되지 않은 원본 VM이 Site Recovery 콘솔에서 제거되고, VMM에 대한 Site Recovery 설정이 VMM 콘솔에서 제거되고, 복제본이 삭제됩니다.
6. 네트워크를 통해 복제하는 경우 **초기 복제 방법**에서 초기 복제를 시작할지 또는 예약할지를 지정합니다. 네트워크 대역폭을 절약하려면 사용량이 많지 않은 시간에 예약하는 것이 좋습니다. 그런 후 **OK**를 클릭합니다.

     ![복제 정책](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. 새 정책은 VMM 클라우드에 자동으로 연결됩니다. **복제 정책**에서 **확인**을 클릭합니다. 


## <a name="enable-replication"></a>복제 사용

1. **응용 프로그램 복제** > **원본**을 클릭합니다. 
2. **원본**에서 복제하려는 Hyper-V 호스트가 배치되는 VMM 서버 및 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **대상**에서 보조 VMM 서버 및 클라우드를 확인합니다.
4. **가상 머신**의 목록에서 보호하려는 VM을 선택합니다.


**작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료되면 초기 복제가 완료되고 VM에서 장애 조치를 수행할 준비가 됩니다.

## <a name="next-steps"></a>다음 단계

[재해 복구 드릴 실행](hyper-v-vmm-test-failover.md)
