---
title: "Site Recovery를 사용하여 복제된 VMware VM 및 물리적 서버를 Azure로 장애 조치(Failover) 및 장애 복구(Failback) | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 VMware VM 및 물리적 서버를 Azure로 장애 조치(Failover)고, 온-프레미스 사이트로 장애 복구(Failback)하는 방법 알아보기"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/05/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 618e2bb7ef124883af2d73264e01922ad2329d12
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>복제된 VMware VM 및 물리적 서버를 Azure로 장애 조치(Failover) 및 장애 복구(Failback)

이 자습서에서는 VMware VM을 Azure로 장애 조치(Failover)하는 방법을 설명합니다. 장애 조치(failover)한 후에 온-프레미스 사이트가 사용 가능해지면 장애 복구(failback)합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VMware VM 속성을 확인하여 Azure 요구 사항을 준수하는지 검토
> * Azure에 대한 장애 조치(Failover) 실행
> * 장애 복구(Failback)를 위해 프로세스 서버 및 마스터 대상 서버 만들기
> * 온-프레미스 사이트로 Azure VM 다시 보호
> * Azure에서 온-프레미스로 장애 조치(Failover)
> * 온-프레미스 VM을 다시 보호하고 Azure에 대한 복제 다시 시작

시리즈의 다섯 번째 자습서입니다. 이 자습서에서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. [온-프레미스 VMware 준비](vmware-azure-tutorial-prepare-on-premises.md)
3. [재해 복구 설정](vmware-azure-tutorial.md)
4. [재해 복구 드릴 실행](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>장애 조치(Failover) 및 장애 복구(Failback) 준비

VM에 스냅숏이 없는지 확인합니다. 온-프레미스 VM이 다시 보호 중에 꺼집니다.
이렇게 하면 복제 중 데이터 일관성을 보장할 수 있습니다. 다시 보호가 완료된 후 VM을 켜지 마세요. 복제 그룹을 다시 보호하기 위해 동일한 마스터 대상 서버를 사용합니다. 다른 마스터 대상 서버를 사용하여 복제 그룹을 보호하는 경우 서버에서 공통된 시점을 제공할 수 없습니다.

장애 조치(Failover) 및 장애 복구(Failback)는 다음 4단계로 진행됩니다.

1. **Azure로 장애 조치(Failover)**: 온-프레미스 사이트에서 Azure로 컴퓨터를 장애 조치(Failover)합니다.
2. **Azure VM 다시 보호**: Azure VM을 다시 보호하여 온-프레미스 VMware VM으로의 복제를 다시 시작하도록 합니다.
3. **온-프레미스로 장애 조치(Failover)**: Azure에서 장애 복구(Failback)하도록 장애 조치(Failover)를 실행합니다.
4. **온-프레미스 VM 다시 보호**: 데이터가 장애 복구(Failback)된 후에 장애 복구(Failback)한 온-프레미스 VM을 다시 보호하여 Azure로 복제를 시작하도록 합니다.

## <a name="verify-vm-properties"></a>VM 속성 확인

VM 속성을 확인하고 VM이 [Azure 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 준수하는지 확인합니다.

1. **보호된 항목**에서 **복제된 항목** > VM을 클릭합니다.

2. **복제된 항목** 창에 VM 정보, 상태 및 최신 사용 가능한 복구 지점의 요약이 제공됩니다. 자세한 내용을 보려면 **속성**을 클릭합니다.

3. **계산 및 네트워크**에서 Azure 이름, 리소스 그룹, 대상 크기, [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md) 및 [관리되는 디스크 설정](#managed-disk-considerations)을 수정할 수 있습니다.

4. 장애 조치(failover) 후 Azure VM이 배치될 네트워크/서브넷 및 할당되는 IP 주소를 포함한 네트워크 설정을 보고 수정할 수 있습니다.

5. **디스크**에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 볼 수 있습니다.

## <a name="run-a-failover-to-azure"></a>Azure에 대한 장애 조치(Failover) 실행

1. **설정** > **복제된 항목**에서 VM > **장애 조치(Failover)**를 클릭합니다.

2. **장애 조치(Failover)**에서 장애 조치할 **복구 지점**을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
   - **최신**(기본값): 이 옵션은 먼저 Site Recovery로 전송된 모든 데이터를 처리합니다. 이 옵션은 장애 조치(failover) 후에 생성된 Azure VM은 장애 조치(failover)가 트리거되었을 때 Site Recovery로 복제된 모든 데이터를 보유하므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
   - **가장 최근에 처리됨**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치합니다. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO(복구 시간 목표)를 제공합니다.
   - **최신 앱 일치**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 앱 일치 복구 지점으로 장애 조치합니다.
   - **사용자 지정**: 복구 지점을 지정합니다.

3. 장애 조치를 트리거하기 전에 원본 가상 머신을 종료하려고 시도하려면 **장애 조치(Failover)를 시작하기 전에 컴퓨터를 종료합니다**를 선택합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.

4. Azure VM에 연결할 준비가 된 경우 연결하고, 장애 조치(failover) 후에 유효성을 검사합니다.

5. 확인한 후 장애 조치(failover)를 **커밋**합니다. 그러면 사용 가능한 복구 지점이 모두 삭제됩니다.

> [!WARNING]
> **진행 중인 장애 조치(failover) 취소 안 함**: 장애 조치(failover)를 시작하기 전에 VM 복제가 중지됩니다.
> 진행 중인 장애 조치(Failover)를 취소하면 장애 조치(Failover)가 중지되지만 VM은 다시 복제되지 않습니다.

일부 시나리오에서는 장애 조치(Failover)를 위해서는 추가 처리가 필요하며 이러한 작업을 완료하는 데는 약 8~10분이 소요됩니다. 물리적 서버, VMware Linux 컴퓨터, DHCP 서비스가 사용되도록 설정되지 않은 VMware VM과 부팅 드라이버인 storvsc, vmbus, storflt, intelide, atapi가 없는 VMware VM의 경우 테스트 장애 조치(Failover)가 더 오래 걸릴 수 있습니다.

## <a name="create-a-process-server-in-azure"></a>Azure에서 프로세스 서버 만들기

프로세스 서버는 Azure VM에서 데이터를 수신한 후 온-프레미스 사이트로 보냅니다. 프로세스 서버와 보호된 VM 간에 대기 시간이 짧은 네트워크가 필요합니다.

- 테스트를 수행하려는 경우, Azure ExpressRoute 연결이 있으면 구성 서버에 자동으로 설치되는 온-프레미스 프로세스 서버를 사용할 수 있습니다.
- VPN 연결이 있거나 프로덕션 환경에서 장애 복구(Failback)를 실행하는 경우 Azure VM을 장애 복구(Failback)에 대한 Azure 기반 프로세스 서버로 설정해야 합니다.
- Azure에서 프로세스 서버를 설정하려면 [이 문서](vmware-azure-set-up-process-server-azure.md)의 지침을 따릅니다.

## <a name="configure-the-master-target-server"></a>마스터 대상 서버 구성

기본적으로 마스터 대상 서버는 온-프레미스 구성 서버에서 실행됩니다. 이 자습서에서는 기본 마스터를 그대로 사용합니다. 마스터 대상 서버는 장애 복구(Failback) 데이터를 받습니다.

vCenter 서버에 의해 관리되는 ESXi 호스트에 VM이 있는 경우 마스터 대상 서버는 VM 디스크에 복제된 데이터를 쓰기 위해 VM의 데이터 저장소(VMDK)에 액세스할 수 있어야 합니다. 읽기/쓰기 액세스 권한으로 VM 데이터 저장소를 마스터 대상의 호스트에 탑재했는지 확인합니다.

vCenter 서버에 의해 관리되지 않는 ESXi에 VM이 있는 경우 Site Recovery 서비스가 다시 보호하는 동안 새 VM을 만듭니다. 이 VM은 마스터 대상을 만드는 ESX 호스트에서 만들어집니다.
VM의 하드 디스크는 마스터 대상 서버가 실행 중인 호스트에서 액세스할 수 있는 데이터 저장소에 있어야 합니다.

VM에서 vCenter를 사용하지 않는 경우 컴퓨터를 다시 보호하기 전에 마스터 대상 서버가 실행되는 호스트의 검색을 완료해야 합니다. 물리적 서버를 장애 복구(Failback)하는 경우에도 마찬가지입니다. 다른 옵션(온-프레미스 VM이 있는 경우)은 장애 복구(Failback)를 수행하기 전에 삭제하는 것입니다. 그런 다음 장애 복구를 수행하면 마스터 대상 ESX 호스트와 동일한 호스트에 새 VM을 만듭니다. 대체 위치로 장애 복구하는 경우 온-프레미스 마스터 대상 서버에서 사용한 것과 동일한 데이터 저장소와 동일한 ESX 호스트에 데이터를 복구합니다.

마스터 대상 서버에 저장소 vMotion을 사용할 수 없습니다. 이렇게 하면 디스크를 사용할 수 없으므로 장애 복구(Failback)가 작동하지 않습니다. vMotion 목록에서 마스터 대상 서버를 제외합니다.

## <a name="reprotect-azure-vms"></a>Azure VM 다시 보호

이 절차는 온-프레미스 VM을 사용할 수 없으며 대체 위치로 다시 보호한다고 가정합니다.

1. **설정** > **복제된 항목**에서 장애 조치(Failover)된 VM을 마우스 오른쪽 단추로 클릭하고 **다시 보호**를 클릭합니다.
2. **다시 보호**에서 **Azure - 온-프레미스**를 선택합니다.
3. 온-프레미스 마스터 대상 서버 및 프로세스 서버를 지정합니다.

4. **데이터 저장소**에서 온-프레미스에서 디스크를 복구하려는 마스터 대상 데이터 저장소를 선택합니다. 온-프레미스 VM이 삭제되었고 새 디스크를 만들어야 하는 경우에 이 옵션을 사용합니다. 디스크가 이미 있으면 이 설정을 무시합니다. 하지만 값은 지정해야 합니다.
5. 마스터 대상 보존 드라이브를 선택합니다. 장애 복구 정책이 자동으로 선택됩니다.
6. **확인**을 클릭하여 다시 보호를 시작합니다. Azure에서 온-프레미스 사이트로 가상 머신을 복제하는 작업을 시작합니다. **작업** 탭에서 진행률을 추적할 수 있습니다.

> [!NOTE]
> Azure VM을 기존 온-프레미스 VM으로 복구하려면 마스터 대상 서버의 ESXi 호스트에 읽기/쓰기 액세스 권한으로 온-프레미스 VM의 데이터 저장소를 탑재합니다.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Azure에서 온-프레미스로의 장애 조치(Failover) 실행

온-프레미스로 다시 복제하려면 장애 복구(Failback) 정책 정책이 사용됩니다. 이 정책은 Azure로의 복제를 위한 복제 정책을 만들 때 자동으로 생성됩니다.

- 정책은 구성 서버에 자동으로 연결됩니다.
- 정책을 수정할 수는 없습니다.
- 정책 값은 다음과 같습니다.
    - RPO 임계값 = 15분
    - 복구 지점 보존 기간 = 24시간
    - 앱 일치 스냅숏 빈도 = 60분

다음과 같이 장애 조치(Failover)를 실행합니다.

1. **복제된 항목** 페이지에서 컴퓨터를 마우스 오른쪽 단추로 클릭한 다음 **계획되지 않은 장애 조치(Failover)**를 선택합니다.
2. **장애 조치(Failover) 확인**에서 장애 조치(Failover) 방향이 Azure부터인지 확인합니다.

3. 장애 조치(Failover)에 사용할 복구 지점을 선택합니다. 최신 시점 이전에 최신 응용 프로그램 일치 복구 지점이 발생하고 일부 데이터가 손실됩니다. 장애 조치(Failover)를 실행하면 Site Recovery가 Azure VM을 종료하고 온-프레미스 VM을 부팅합니다. 일정 시간 동안 가동 중지되므로 적절한 시간을 선택합니다.
4. 컴퓨터를 마우스 오른쪽 단추로 클릭하고 **커밋**을 클릭합니다. 그러면 Azure VM을 제거하는 작업이 트리거됩니다.
5. Azure VM이 예상대로 종료되었는지 확인합니다.


## <a name="reprotect-on-premises-machines-to-azure"></a>Azure로 온-프레미스 컴퓨터 다시 보호

이제 데이터가 온-프레미스 사이트에서 다시 복구되지만 Azure로 복제되지는 않습니다. 다음과 같이 Azure로의 복제를 다시 시작할 수 있습니다.

1. 자격 증명 모음 > **설정** >**복제된 항목**에서 장애 복구(Failback)된 VM을 선택하고 **다시 보호**를 클릭합니다.
2. Azure로 복제된 데이터를 전송하는 데 사용되는 프로세스 서버를 선택하고 **확인**을 클릭합니다.

다시 보호가 완료되면 VM이 Azure로 다시 복제하고 필요에 따라 장애 조치(Failover)를 수행할 수 있습니다.
