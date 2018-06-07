---
title: Azure Site Recovery를 사용하여 Azure로 온-프레미스 컴퓨터 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 온-프레미스 컴퓨터를 마이그레이션하는 방법을 설명합니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3e82983ff171e638415d6e7cafe0cb034dd978af
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>Azure로 온-프레미스 컴퓨터 마이그레이션

BCDR(비즈니스 지속성 및 재해 복구)을 위해 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 컴퓨터 및 Azure VM의 재해 복구를 관리하고 오케스트레이션하는 것은 물론, Site Recovery를 사용하여 온-프레미스 컴퓨터를 Azure로 마이그레이션하는 작업을 관리할 수도 있습니다.


이 자습서는 온-프레미스 VM 및 물리적 서버를 Azure로 마이그레이션하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 복제 목표 선택
> * 원본 및 대상 환경 설정
> * 복제 정책 설정
> * 복제 사용
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인
> * Azure에 대한 일회성 장애 조치(failover) 실행

이 문서는 시리즈의 세 번째 자습서입니다. 이 자습서에서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. 온-프레미스 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 또는 [Hyper-V] (hyper-v-prepare-on-premises-tutorial.md) 서버를 준비합니다.

시작하기 전에 재해 복구를 위한 [VMware](vmware-azure-architecture.md) 또는 [Hyper-V](hyper-v-azure-architecture.md) 아키텍처를 검토하는 것이 좋습니다.


## <a name="prerequisites"></a>필수 조건

반가상화 드라이버에서 내보낸 장치는 지원되지 않습니다.


## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **모니터링 및 관리** > **Backup 및 Site Recovery**를 클릭합니다.
3. **이름**에서 **ContosoVMVault**라는 이름을 지정합니다. 구독이 두 개 이상인 경우 적절한 구독을 선택합니다.
4. **ContosoRG** 리소스 그룹을 만듭니다.
5. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
6. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정**을 클릭하고 **만들기**를 클릭합니다.

   ![새 자격 증명 모음](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

**대시보드**의 **모든 리소스** 아래와 주 **Recovery Services 자격 증명 모음** 페이지에 새 자격 증명 모음이 추가됩니다.



## <a name="select-a-replication-goal"></a>복제 목표 선택

복제할 대상과 복제할 위치를 선택합니다.
1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴 >에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 마이그레이션할 항목을 선택합니다.
    - **VMware**: **Azure에** > **예, VMware vSphere 하이퍼바이저 사용**을 차례로 선택합니다.
    - **물리적 컴퓨터**: **Azure에** > **가상화되지 않음/기타**를 선택합니다.
    - **Hyper-V**: **Azure에** > **예, Hyper-V 사용**을 선택합니다. Hyper-V VM이 VMM에서 관리되는 경우 **예**를 선택합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

- VMware VM에 대한 원본 환경을 [설정](vmware-azure-tutorial.md#set-up-the-source-environment)합니다.
- 물리적 서버에 대한 원본 환경을 [설정](physical-azure-disaster-recovery.md#set-up-the-source-environment)합니다.
- Hyper-V VM에 대한 원본 환경을 [설정](hyper-v-azure-tutorial.md#set-up-the-source-environment)합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 리소스 관리자 배포 모델을 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

## <a name="set-up-a-replication-policy"></a>복제 정책 설정

- VMware VM에 대한 [복제 정책을 설정](vmware-azure-tutorial.md#create-a-replication-policy)합니다.
- 물리적 서버에 대한 [복제 정책을 설정](physical-azure-disaster-recovery.md#create-a-replication-policy)합니다.
- Hyper-V VM에 대한 [복제 정책을 설정](hyper-v-azure-tutorial.md#set-up-a-replication-policy)합니다.


## <a name="enable-replication"></a>복제 사용

- VMware VM에 대해 [복제를 사용하도록 설정](vmware-azure-tutorial.md#enable-replication)합니다.
- 물리적 서버에 [복제를 사용하도록 설정](physical-azure-disaster-recovery.md#enable-replication)합니다.
- [VMM이 있거나](hyper-v-vmm-azure-tutorial.md#enable-replication) [없는](hyper-v-azure-tutorial.md#enable-replication) Hyper-V VM에 대해 복제를 사용하도록 설정합니다.


## <a name="run-a-test-migration"></a>테스트 마이그레이션 실행

모든 것이 예상대로 작동하는지 확인할 수 있도록 Azure에 대해 [테스트 장애 조치(Failover)](tutorial-dr-drill-azure.md)를 실행합니다.


## <a name="migrate-to-azure"></a>Azure로 마이그레이션

마이그레이션할 컴퓨터에 대해 장애 조치(Failover)를 실행합니다.

1. **설정** > **복제된 항목**에서 컴퓨터 > **장애 조치(Failover)** 를 클릭합니다.
2. **장애 조치(Failover)** 에서 장애 조치할 **복구 지점**을 선택합니다. 최신 복구 지점을 선택합니다.
3. 암호화 키 설정은 이 시나리오와 관련이 없습니다.
4. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 가상 머신을 종료하려고 시도합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
5. Azure VM이 예상대로 Azure에 표시되는지 확인합니다.
6. **복제된 항목**에서 VM를 마우스 오른쪽 단추로 클릭하고 **마이그레이션 완료**를 클릭합니다. 그러면 마이그레이션 프로세스가 완료되고, VM에 대한 복제가 중지되고, VM에 대한 Site Recovery 청구가 중지됩니다.

    ![마이그레이션 완료](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **진행 중인 장애 조치(failover) 취소 안 함**: 장애 조치(failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 VM은 다시 복제되지 않습니다.

일부 시나리오에서는 장애 조치(Failover)를 위해서는 추가 처리가 필요하며 이러한 작업을 완료하는 데는 약 8~10분이 소요됩니다. 물리적 서버, VMware Linux 컴퓨터, DHCP 서비스가 사용되도록 설정되지 않은 VMware VM과 부팅 드라이버인 storvsc, vmbus, storflt, intelide, atapi가 없는 VMware VM의 경우 테스트 장애 조치(Failover)가 더 오래 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 온-프레미스 VM을 Azure VM으로 마이그레이션했습니다. 이제 Azure VM에 대해 재해 복구를 구성할 수 있습니다.

> [!div class="nextstepaction"]
> 온-프레미스 사이트에서 마이그레이션한 후 Azure VM에 대해 [재해 복구를 설정](azure-to-azure-replicate-after-migration.md)합니다.
