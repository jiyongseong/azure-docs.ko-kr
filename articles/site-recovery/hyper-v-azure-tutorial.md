---
title: "Azure Site Recovery를 통한 온-프레미스 Hyper-V VM(VMM 없음)에서 Azure로의 재해 복구 설정 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 통한 온-프레미스 Hyper-V VM(VMM 없음)에서 Azure로의 재해 복구를 설정하는 방법을 알아봅니다."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: e7ddb3046b0725b3afcea2ed6a533388a89cf306
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>온-프레미스 Hyper-V VM의 Azure로의 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 온-프레미스 Hyper-V VM의 Azure로의 재해 복구를 설정하는 방법을 설명합니다. 이 자습서에는 System Center VMM(Virtual Machine Manager)에서 관리하지 않는 Hyper-V VM 관련 내용이 포함되어 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 복제 원본 및 대상을 선택합니다.
> * 온-프레미스 Site Recovery 구성 요소 및 대상 복제 환경을 포함하여 원본 복제 환경을 설정합니다.
> * 복제 정책을 만듭니다.
> * VM의 복제를 사용하도록 설정합니다.

이 문서는 시리즈의 세 번째 자습서입니다. 이 자습서에서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. [온-프레미스 Hyper-V 준비](tutorial-prepare-on-premises-hyper-v.md)

시작하기 전에 이 재해 복구 시나리오용 [아키텍처를 검토](concepts-hyper-v-to-azure-architecture.md)하는 것이 좋습니다.

## <a name="select-a-replication-goal"></a>복제 목표 선택


1. **모든 서비스** > **Recovery Services 자격 증명 모음**에서 이전 자습서에서 준비한 자격 증명 모음인 **ContosoVMVault**를 선택합니다.
2. **시작**에서**Site Recovery**를 클릭합니다. 그런 다음 **인프라 준비**를 클릭합니다.
3. **보호 목표** > **컴퓨터가 있는 위치**에서 **온-프레미스**를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.**에서 **Azure**를 선택합니다.
5. **컴퓨터가 가상화되어 있습니까?**에서 **아니요**를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 목표](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="set-up-the-source-environment"></a>원본 환경 설정

원본 환경을 설정하려면 Hyper-V 사이트에 Hyper-V 호스트를 추가하고, Azure Site Recovery 공급자와 Azure Recovery Services 에이전트를 다운로드하여 설치하고, 자격 증명 모음에 Hyper-V 사이트를 등록합니다. 

1. **인프라 준비**에서 **원본**을 클릭합니다.
2. **+Hyper-V 사이트**를 클릭하고 이전 자습서에서 만든 사이트의 이름인 **ContosoHyperVSite**를 지정합니다.
3. **+Hyper-V Server**를 클릭합니다.
4. Provider 설치 파일을 다운로드합니다.
5. 자격 증명 모음 등록 키를 다운로드합니다. 공급자 설정을 실행하려면 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![Provider 다운로드](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Provider 설치

**ContosoHyperVSite** 사이트추에 가한 각 Hyper-V 호스트에서 Provider 설치 파일(AzureSiteRecoveryProvider.exe)을 실행합니다. 설치 프로그램을 실행하면 Azure Site Recovery Provider와 Recovery Services 에이전트가 각 Hyper-V 호스트에 설치됩니다.

1. Azure Site Recovery Provider 설치 마법사 > **Microsoft 업데이트**에서 Provider 업데이트를 확인하려면 Microsoft 업데이트를 사용하는 옵션을 선택합니다.
2. **설치**에서 Provider와 에이전트의 기본 설치 위치를 적용하고 **설치**를 클릭합니다.
3. 설치 후에 Microsoft Azure Site Recovery 등록 마법사 > **자격 증명 모음 설정**에서 **찾아보기**를 클릭하고 **키 파일**에서 다운로드한 자격 증명 모음 키 파일을 선택합니다. 
4. Azure Site Recovery 구독, 자격 증명 모음 이름(**ContosoVMVault**) 및 Hyper-V Server가 속한 Hyper-V 사이트(**ContosoHyperVSite**)를 지정합니다.
5. **프록시 설정**에서 **프록시 서버 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
6. **등록**에서 자격 증명 모음에 서버를 등록한 후에 **마침**을 클릭합니다.

Azure Site Recovery에서 Hyper-V 서버의 메타데이터가 검색되며 서버가 **Site Recovery 인프라** > **Hyper-V 호스트**에 표시됩니다. 이 프로세스는 최대 30분 정도 걸릴 수 있습니다.


## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다. 

1. **인프라 준비** > **대상**을 클릭합니다.
2. 구독을 선택하고 장애 조치(failover) 후 Azure VM이 생성될 리소스 그룹 **ContosoRG**를 선택합니다.
3. **Resource Manager"** 배포 모델을 선택합니다.

Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.


## <a name="set-up-a-replication-policy"></a>복제 정책 설정

> [!NOTE]
> Hyper-V에서 Azure로 복제 정책의 경우, 15분 복제 빈도 옵션이 5분 및 30초 복사 빈도 설정을 위해 사용이 중지됩니다. 15분 복사 빈도가 사용되는 복제 정책은 5분 복사 빈도 설정이 사용되도록 자동으로 업데이트됩니다. 5분 및 30초 복사 빈도 옵션은 대역폭 사용 및 데이터 전송 볼륨에 미치는 영향을 최소화하여 15분 복사 빈도에 비해 향상된 복제 성능과 우수한 RPO(복구 지점 목표)를 제공합니다.

1. **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 클릭합니다.
2. **정책 만들기 및 연결**에서 정책 이름을 **ContosoReplicationPolicy**로 지정합니다.
3. 기본 설정을 그대로 적용하고 **확인**을 클릭합니다.
    - **복사 빈도**는 초기 복제 후 5분마다 델타 데이터가 복제됨을 나타냅니다.
    - **복구 지점 보존**은 모든 복구 지점의 보존 기간이 2시간임을 나타냅니다.
    - **앱 일치 스냅숏 빈도**는 앱 일치 스냅숏을 포함하는 복구 지점이 1시간마다 만들어짐을 나타냅니다.
    - **초기 복제 시작 시간**은 초기 복제가 즉시 시작됨을 나타냅니다.
4. 정책이 만들어지고 나면 **확인**을 클릭합니다. 정책을 새로 만들면, 지정된 Hyper-V 사이트(**ContosoHyperVSite**)와 자동으로 연결됩니다.

    ![복제 정책](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>복제 사용


1. **응용 프로그램 복제**에서 **원본**을 클릭합니다. 
2. **원본**에서 **ContosoHyperVSite** 사이트를 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **대상**에서 대상(Azure), 자격 증명 모음 구독, **Resource Manager** 배포 모델을 확인합니다.
4. 이전 자습서에서 복제된 데이터용으로 만든 **contosovmsacct1910171607** 저장소 계정을 선택하고, 장애 조치(failover) 후 Azure VM이 배치될 **ContosoASRnet** 네트워크를 선택합니다.
5. **가상 머신** > **선택**에서 복제할 VM을 선택합니다. 그런 후 **OK**를 클릭합니다.

 **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료되고 나면 초기 복제가 완료되며 가상 머신은 장애 조치(failover)를 수행할 준비가 됩니다.

## <a name="next-steps"></a>다음 단계
[재해 복구 드릴 실행](tutorial-dr-drill-azure.md)
