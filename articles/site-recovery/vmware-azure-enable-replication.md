---
title: Azure Site Recovery를 사용하여 VMware VM에 대해 Azure로의 복제 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure에 대한 VMware VM 복제를 설정하는 방법을 설명합니다.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: asgang
ms.openlocfilehash: 48d84f59a80ea14c2b40821d886fc410c7e3f2c3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>VMware VM에 대해 Azure로의 복제를 사용하도록 설정


이 문서에서는 Azure로의 온-프레미스 VMware VM 복제를 사용하도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

1.  [온-프레미스 원본 환경 설정](vmware-azure-set-up-source.md)
2.  [Azure에서 대상 환경 설정](vmware-azure-set-up-target.md)


## <a name="before-you-start"></a>시작하기 전에
VMware 가상 머신을 복제하는 경우:

* Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 Azure에 새 가상 머신을 복제할 수 있습니다.
* VMware VM은 15분마다 검색됩니다. 검색 후 Azure Portal에 나타나려면 15분 이상 걸릴 수 있습니다. 마찬가지로 vCenter Server 또는 vSphere 호스트를 추가할 때 검색이 15분 이상 걸릴 수 있습니다.
* 가상 컴퓨터의 환경 변경 사항(예: VMware 도구 설치)이 포털에서 업데이트되는 데 15분 이상 소요될 수도 있습니다.
* **구성 서버** 페이지에서 vCenter Server/vSphere 호스트에 대한 **마지막 연락** 필드에서 VMware VM이 마지막으로 검색된 시간을 확인할 수 있습니다.
* 예약된 검색을 기다리지 않고 복제에 대해 컴퓨터를 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침** 단추를 클릭합니다.
* 복제를 사용하도록 설정하는 경우 컴퓨터가 준비되면 프로세스 서버가 자동으로 해당 컴퓨터에 모바일 서비스를 설치합니다.


## <a name="enable-replication"></a>복제 사용

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다. 처음으로 복제를 활성화한 후 자격 증명 모음에서 **+복제**를 클릭하여 추가 컴퓨터에 대해 복제를 활성화합니다.
2. **원본** 페이지 > **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **Virtual Machines** 또는 **물리적 컴퓨터**를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다. 이 설정은 물리적 컴퓨터를 복제하는 경우에는 관련이 없습니다.
5. 프로세스 서버를 선택합니다. 그러면 추가 프로세스 서버를 만들지 않은 경우 구성 서버의 이름이 됩니다. 그런 후 **OK**를 클릭합니다.

    ![복제 원본 사용](./media/vmware-azure-enable-replication/enable-replication2.png)

6. **대상**에서 장애 조치 가상 머신을 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 가상 머신에 대해 Azure에서 사용하려는 배포 모델을 선택합니다.

7. 데이터 복제에 사용할 Azure Storage 계정을 선택합니다. 

    > [!NOTE]

    >   * 프리미엄 또는 표준 저장소 계정을 선택할 수 있습니다. 프리미엄 계정을 선택하는 경우 지속적인 복제 로그를 위한 추가 표준 저장소 계정을 지정해야 합니다. 계정은 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.
    >   * 다른 저장소 계정을 사용하려면 [새로 만들](../storage/common/storage-create-storage-account.md)면 됩니다. Resource Manager 모델을 사용하여 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 

8. 장애 조치(Failover) 후 Azure VM이 회전하면 연결될 Azure 네트워크 및 서브넷을 선택합니다 네트워크는 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다. 네트워크는 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성** 을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다. 네트워크가 없는 경우 [만들어야](#set-up-an-azure-network) 합니다. Resource Manager 모델을 사용하여 네트워크를 만들려면 **새로 만들기**를 클릭합니다. 해당하는 경우 서브넷을 선택하고 **확인**을 클릭합니다.

    ![복제 대상 설정 사용](./media/vmware-azure-enable-replication/enable-rep3.png)
9. **Virtual Machines** > **Virtual Machines 선택**에서 복제하려는 각 컴퓨터를 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.

    ![복제 사용 가상 머신 선택](./media/vmware-azure-enable-replication/enable-replication5.png)
10. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용되는 계정을 선택합니다.  
11. 기본적으로 모든 디스크가 복제됩니다. 디스크를 복제에서 제외하려면 **모든 디스크**를 클릭하고 복제하지 않으려는 디스크를 지웁니다.  그런 후 **OK**를 클릭합니다. 나중에 추가 속성을 설정할 수 있습니다. 디스크 제외에 대해 [자세히 알아보세요](vmware-azure-exclude-disk.md).

    ![복제 사용 속성 구성](./media/vmware-azure-enable-replication/enable-replication6.png)

12. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. **설정** > **복제 정책** > 정책 이름 > **설정 편집**에서 복제 정책 설정을 수정할 수 있습니다. 정책에 적용하는 변경 내용은 복제 및 새 컴퓨터에도 적용됩니다.
13. 컴퓨터를 복제 그룹으로 수집하려는 경우 **다중 VM 일관성**을 사용합니다. 그룹에 대한 이름을 지정하고 **확인**을 클릭합니다. 

    > [!NOTE]

    >    * 복제 그룹의 컴퓨터는 함께 복제되고 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일관성 복구 지점을 갖습니다.
    >    * 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집합니다. 다중 VM 일관성을 사용하면 워크로드 성능에 영향을 줄 수 있습니다. 컴퓨터가 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 사용합니다.

    ![복제 사용](./media/vmware-azure-enable-replication/enable-replication7.png)
14. **복제 사용**을 클릭합니다. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

> [!NOTE]
> 컴퓨터가 푸시 설치에 대해 준비된 경우 보호를 사용하도록 설정하면 모바일 서비스 구성 요소가 설치됩니다. 컴퓨터에 구성 요소가 설치된 후 보호 작업이 시작되고 실패합니다. 실패 후 각 컴퓨터를 수동으로 다시 시작해야 합니다. 다시 시작한 후 보호 작업이 다시 시작되고 초기 복제가 발생합니다.
>
>

## <a name="view-and-manage-vm-properties"></a>VM 속성 보기 및 관리

다음으로 원본 컴퓨터의 속성을 확인합니다. Azure VM 이름이 [Azure 가상 머신 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 준수해야 합니다.

1. **설정** > **복제된 항목**을 클릭하고 컴퓨터를 선택합니다. **Essentials** 페이지는 컴퓨터 설정 및 상태에 대한 정보를 표시합니다.
2. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.
3. **Compute 및 네트워크** > **Compute 속성**에서 Azure VM 이름 및 대상 크기를 지정할 수 있습니다. 필요한 경우 Azure 요구 사항을 준수하도록 이름을 수정합니다.

    ![계산 및 네트워크 속성](./media/vmware-azure-enable-replication/vmproperties.png)

4.  컴퓨터가 사후 장애 조치(Failover)의 일부인 [리소스 그룹](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)을 선택할 수 있습니다. 장애 조치(Failover) 전에 언제든지 이 설정을 변경할 수 있습니다. 사후 장애 조치(Failover) 시 컴퓨터를 다른 리소스 그룹으로 마이그레이션하는 경우 해당 컴퓨터의 보호 설정이 중단됩니다.
5. 컴퓨터가 사후 장애 조치(Failover)의 일부여야 하는 경우 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)을 선택할 수 있습니다. 가용성 집합을 선택할 때는 다음에 유의하세요.

    * 지정된 리소스 그룹에 속하는 가용성 집합만 나열됩니다.  
    * 다른 가상 네트워크를 사용하는 컴퓨터는 동일한 가용성 집합에 속할 수 없습니다.
    * 동일한 크기의 가상 머신만 가용성 집합의 일부가 될 수 있습니다.
5. 또한 대상 네트워크, 서브넷 및 Azure VM에 할당된 IP 주소에 대한 정보를 보고 추가할 수 있습니다.
6. **디스크**에서 복제될 VM의 운영 체제 및 데이터 디스크를 볼 수 있습니다.

### <a name="configure-networks-and-ip-addresses"></a>네트워크 및 IP 주소 구성

- 대상 IP 주소를 설정할 수 있습니다. 주소를 입력하지 않으면 장애 조치(Failover)된 컴퓨터가 DHCP를 사용합니다. 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다. 주소를 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치(failover)에 동일한 대상 IP 주소를 사용할 수 있습니다.
- 네트워크 어댑터 수는 다음과 같이 대상 가상 머신에 대해 지정하는 크기에 따라 결정됩니다.
    - 원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우 대상의 어댑터 수는 소스와 동일해야 합니다.
    - 원본 가상 머신의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
    예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가 4를 지원하는 경우, 대상 컴퓨터에는 2개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기에서 하나만 지원하는 경우 대상 컴퓨터에는 하나의 어댑터만 있습니다.
    - 가상 머신에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다. 또한 목록에서 표시되는 첫 번째 어댑터는 Azure 가상 머신에서 *기본* 네트워크 어댑터가 됩니다.

### <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

Microsoft Software Assurance 고객은 Azure 하이브리드 혜택을 사용하여 Azure로 마이그레이션되거나 재해 복구를 위해 Azure를 사용하는 Windows Server 컴퓨터에 대한 라이선스 비용을 절감할 수 있습니다. Azure 하이브리드 혜택을 사용할 자격이 있는 경우 장애 조치(Failover) 시 여기에 할당된 가상 머신이 Azure Site Recovery에서 만든 가상 머신임을 지정할 수 있습니다. 다음을 수행합니다.
- 복제된 가상 머신의 계산 및 네트워크 속성 섹션으로 이동합니다.
- Azure 하이브리드 혜택을 받을 수 있는 Windows Server 라이선스가 있는지 묻는 질문에 답변합니다.
- Software Assurance가 포함된 적절한 Windows Server 라이선스가 있는지 확인하려면 이 확인란을 선택합니다. 이 항목은 장애 조치(Failover) 시 생성될 컴퓨터에 Azure 하이브리드 혜택을 적용하는 데 사용할 수 있습니다.
- 복제된 컴퓨터에 대한 설정을 저장합니다.

[Azure 하이브리드 혜택](https://aka.ms/azure-hybrid-benefit-pricing)에 대해 자세히 알아봅니다.

## <a name="common-issues"></a>일반적인 문제

* 각 디스크의 크기는 1TB 미만이어야 합니다.
* OS 디스크는 동적 디스크가 아닌 기본 디스크여야 함
* 2세대/UEFI 지원 가상 머신의 경우 운영 체제 제품군이 Windows이고 부팅 디스크는 300GB 미만이어야 합니다.

## <a name="next-steps"></a>다음 단계

보호가 완료되고 컴퓨터가 보호된 상태에 도달한 후에 [장애 조치(Failover)](site-recovery-failover.md)를 시도하여 응용 프로그램이 Azure에서 온 것인지 여부를 확인할 수 있습니다.

보호를 사용하지 않도록 설정하려는 경우 [등록 및 보호 설정을 정리](site-recovery-manage-registration-and-protection.md)하는 방법을 알아봅니다.
