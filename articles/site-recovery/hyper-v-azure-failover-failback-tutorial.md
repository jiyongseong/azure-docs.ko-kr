---
title: "Site Recovery를 사용하여 복제된 Hyper-V VM을 Azure로 장애 조치(failover) 및 장애 복구(failback) | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 VMware VM을 Azure로 장애 조치(failover)하고, 온-프레미스 사이트로 장애 복구(failback)하는 방법을 알아봅니다."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/8/2018
ms.author: raynew
ms.openlocfilehash: 7863feb29fbb04f643aa3b7e1984209f44cdbe9a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>복제된 Hyper-V VM을 Azure로 장애 조치(failover) 및 장애 복구(failback)

이 자습서에서는 Hyper-V VM을 Azure로 장애 조치(failover)하는 방법을 설명합니다. 장애 조치(failover)한 후에 온-프레미스 사이트가 사용 가능해지면 장애 복구(failback)합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Hyper-V VM 속성을 확인하여 Azure 요구 사항을 준수하는지 검토
> * Azure에 대한 장애 조치(Failover) 실행
> * 온-프레미스 사이트로 Azure VM 다시 보호
> * Azure에서 온-프레미스로 장애 복구(failback)
> * 온-프레미스 VM을 다시 보호하고 Azure에 대한 복제 다시 시작

시리즈의 다섯 번째 자습서입니다. 이 자습서에서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. [온-프레미스 VMware 준비](tutorial-prepare-on-premises-hyper-v.md)
3. [Hyper-V VM](tutorial-hyper-v-to-azure.md) 또는 [System Center VMM 클라우드에서 관리되는 Hyper-V VM](tutorial-hyper-v-vmm-to-azure.md)용 재해 복구 설정
4. [재해 복구 드릴 실행](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>장애 조치(failover) 및 장애 복구(failback) 준비

VM에서 스냅숏이 없으며 다시 보호하는 동안 온-프레미스 VM이 꺼져 있는지 확인해야 합니다. 이렇게 하면 복제 중 데이터 일관성을 보장할 수 있습니다. 다시 보호가 완료된 후 VM을 켜지 마세요. 

장애 조치(Failover) 및 장애 복구(Failback)는 다음 4단계로 진행됩니다.

1. **Azure로 장애 조치(failover)**: 온-프레미스 사이트에서 Azure로 컴퓨터를 장애 조치합니다.
2. **Azure VM 다시 보호**: Azure VM을 다시 보호하여 온-프레미스 Hyper-V VM에 다시 복제하게 만듭니다.
3. **온-프레미스로 장애 조치(failover)**: 사용 가능한 경우 Azure에서 온-프레미스 사이트로의 장애 조치(failover)를 실행합니다.
4. **온-프레미스 VM 다시 보호**: 데이터가 장애 복구(failback)된 후에 온-프레미스 VM을 다시 보호하여 Azure로의 복제를 시작합니다.

## <a name="verify-vm-properties"></a>VM 속성 확인

VM 속성을 확인하고 VM이 [Azure 요구 사항](hyper-v-azure-support-matrix.md#replicated-vms)을 준수하는지 확인합니다.

1. **보호 항목**에서 **복제된 항목** > <VM-name>을 클릭합니다.

2. **복제된 항목** 창에서 VM 정보, 상태 및 사용 가능한 최신 복구 지점을 검토합니다. 자세한 내용을 보려면 **속성**을 클릭합니다.
     - **계산 및 네트워크**에서는 장애 조치(failover) 이후 Azure VM이 배치될 네트워크/서브넷과 해당 VM에 할당될 IP 주소를 비롯한 관리 디스크는 Azure에서 Hyper-V로의 장애 복구(failback)용으로 지원되지 않습니다.
   네트워크 설정과 VM 설정을 수정할 수 있습니다.
    - **디스크**에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 볼 수 있습니다.

## <a name="fail-over-to-azure"></a>Azure로 장애 조치(failover)

1. **설정** > **복제된 항목**에서 VM > **장애 조치(Failover)**를 클릭합니다.
2. **장애 조치(failover)**에서 **최신** 복구 지점을 선택합니다. 
3. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.**를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM 종료를 시도합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
4. 장애 조치(failover)를 확인하고 **커밋**을 클릭합니다. 그러면 사용 가능한 복구 지점이 모두 삭제됩니다.

> [!WARNING]
> **진행 중인 장애 조치(failover) 취소 안 함**: 장애 조치(failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 VM은 다시 복제되지 않습니다.

## <a name="reprotect-azure-vms"></a>Azure VM 다시 보호

1. **AzureVMVault** > **복제된 항목**에서 장애 조치(failover)된 VM을 마우스 오른쪽 단추로 클릭한 다음 **다시 보호**를 선택합니다.
2. 보호 방향이 **Azure - 온-프레미스**로 설정되어 있는지 확인합니다.
3. 데이터 일관성을 지키기 위해 온-프레미스 VM은 다시 보호하는 동안 꺼집니다. 다시 보호가 완료된 후에 온-프레미스 VM을 켜지 마세요.
4. 다시 보호 프로세스 후에 VM은 Azure에서 온-프레미스 사이트로 복제하기 시작합니다.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Azure에서 장애 조치(failover)하고 온-프레미스 VM 다시 보호

Azure에서 온-프레미스 사이트로 장애 조치(failover)한 후 온-프레미스 사이트에서 Azure로 VM을 복제하기 시작합니다.

1. **설정** > **복제된 항목**에서 VM > **계획된 장애 조치(failover)**를 클릭합니다.
2. **계획된 장애 조치(Failover) 확인**에서 장애 조치(failover) 방향(Azure에서)을 확인하고 원본 위치와 대상 위치를 선택합니다.
3. **장애 조치(failover) 전 데이터 동기화(델타 변경 내용만 동기화)**를 선택합니다. 이 옵션은 VM을 종료하지 않고 동기화하므로 VM 가동 중지 시간을 최소화합니다.
4. 장애 조치를 시작합니다. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다.
5. 초기 데이터 동기화가 완료되고 Azure VM을 종료할 준비가 되면 **작업** > 계획된 장애 조치(failover) 작업 이름 > **장애 조치(failover) 완료**를 차례로 클릭합니다. 그러면 Azure VM이 종료되고, 온-프레미스로 최신 변경 내용이 전송되며, 온-프레미스 VM이 시작됩니다.
6. 온-프레미스 VM에 로그온하여 예상대로 사용 가능한지 확인합니다.
7. 온-프레미스 VM은 이제 **커밋 보류 중** 상태가 됩니다. **커밋**을 클릭합니다. 그러면 Azure VM 및 해당 디스크가 삭제되고, 온-프레미스 VM이 역방향 복제를 위해 준비됩니다.
온-프레미스 VM을 Azure에 복제하기 시작하려면 **역방향 복제**를 사용합니다. 이렇게 하면 Azure VM을 끈 이후에 수행된 델타 변경 내용의 복제가 트리거됩니다.  
