---
title: Azure Site Recovery의 VMware와 Azure 간 복제 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 온-프레미스 VMware VM을 Azure로 복제할 때 사용되는 구성 요소 및 아키텍처에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware에서 Azure로 복제 아키텍처

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware 사이트와 Azure 간에 VMware VM(가상 머신)을 복제, 장애 조치 및 복구할 때 사용되는 아키텍처와 프로세스에 대해 설명합니다.


## <a name="architectural-components"></a>아키텍처 구성 요소

다음 표와 그림은 Azure로 VMware를 복제하는 데 사용되는 구성 요소를 개략적으로 보여 줍니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**Azure** | Azure 구독, Azure Storage 계정 및 Azure 네트워크 | 온-프레미스 VM에서 복제된 데이터는 저장소 계정에 저장됩니다. 장애 조치를 온-프레미스에서 Azure로 실행할 때 복제된 데이터를 사용하여 Azure VM을 만듭니다. Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**구성 서버 컴퓨터** | 단일 온-프레미스 컴퓨터입니다. 다운로드한 OVF 템플릿에서 배포할 수 있는 VMware VM으로 실행하는 것이 좋습니다.<br/><br/> 이 컴퓨터는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함하는 모든 온-프레미스 Site Recovery 구성 요소를 실행합니다. | **구성 서버**: 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.<br/><br/> **프로세스 서버**: 기본적으로 구성 서버에 설치됩니다. 복제 데이터를 수신하고, 캐싱, 압축 및 암호화를 사용하여 최적화하며, Azure Storage로 보냅니다. 또한 프로세스 서버는 복제하려는 VM에 Azure Site Recovery 모바일 서비스를 설치하고, 온-프레미스 컴퓨터의 자동 검색을 수행합니다. 배포가 늘어나면 프로세스 서버로 실행하는 별도의 프로세스 서버를 추가하여 더 큰 복제 트래픽을 처리할 수 있습니다.<br/><br/> **마스터 대상 서버**: 기본적으로 구성 서버에 설치됩니다. Azure에서 장애 복구(Failback) 중에 복제 데이터를 처리합니다. 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다.
**VMware 서버** | VMware VM은 온-프레미스 vSphere ESXi 서버에서 호스트됩니다. 호스트를 관리하려면 vCenter 서버를 사용하는 것이 좋습니다. | Site Recovery 배포 중에 VMware 서버를e Recovery Services 자격 증명 모음에 추가합니다.
**복제된 컴퓨터** | 복제한 각 VMware VM에 모바일 서비스가 설치됩니다. | 프로세스 서버에서 자동 설치를 수행할 수 있도록 하는 것이 좋습니다. 또는 서비스를 수동으로 설치하거나 System Center Configuration Manager와 같은 자동화된 배포 방법을 사용할 수 있습니다.

**VMware에서 Azure로 아키텍처**

![구성 요소](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>구성 단계

VMware-Azure 재해 복구 또는 VMware-마이그레이션을 설정하는 광범위한 단계는 다음과 같습니다.

1. **Azure 구성 요소 설정**. 올바른 권한의 Azure 계정, Azure Storage 계정, Azure VIrtual Network 및 Recovery Services 자격 증명 모음이 필요합니다. [자세히 알아보기](tutorial-prepare-azure.md).
2. **온-프레미스 설정**. 여기에는 Site Recovery에서 복제할 VM을 자동으로 검색할 수 있도록 VMware 서버에서 계정을 설정하고, 복제하려는 VM에 모바일 서비스 구성 요소를 설치하는 데 사용할 수 있는 계정을 설정하고, VMware 서버 및 VM이 필수 조건을 준수하는지 확인하는 작업이 포함됩니다. 또한 필요에 따라 장애 조치(Failover) 후에 이러한 Azure VM에 연결하도록 준비할 수 있습니다. Site Recovery는 Azure Storage 계정에 VM 데이터를 복제하고, Azure에 대한 장애 조치(Failover)를 실행할 때 해당 데이터를 사용하여 Azure VM을 만듭니다. [자세히 알아보기](vmware-azure-tutorial-prepare-on-premises.md).
3. **복제 설정**. 복제할 위치를 선택합니다. 필요한 모든 온-프레미스 Site Recovery 구성 요소를 실행하는 단일 온-프레미스 VMware VM(구성 서버)을 설정하여 원본 복제 환경을 구성합니다. 설정 후에 Recovery Services 자격 증명 모음에서 구성 서버 컴퓨터를 등록합니다. 그런 후 대상 설정을 선택합니다. [자세히 알아보기](vmware-azure-tutorial.md).
4. **복제 정책 만들기**. 복제가 수행되는 방식을 지정하는 복제 정책을 만듭니다. 
    - **RPO 임계값**: 이 모니터링 설정은 지정된 시간 내에 복제가 발생하지 않을 경우 경고(및 필요에 따라 전자 메일)가 실행됨을 나타냅니다. 예를 들어, RPO 임계값을 30분으로 설정했으며, 문제로 인해 30분 동안 복제가 발생하지 않을 경우 이벤트가 생성됩니다. 이 설정은 복제에 영향을 주지 않습니다. 복제는 연속적이며, 복구 지점은 몇 분 간격으로 생성됩니다.
    - **보존**: 복구 지점 보존은 Azure에서 복구 지점이 유지되는 기간을 지정합니다. 프리미엄 저장소에 대해 0~24시간 사이의 값을 지정하거나, 표준 저장소에 대해 최대 72시간을 지정할 수 있습니다. 0보다 큰 값을 설정하는 경우 최신 복구 지점으로 또는 저장된 지점으로 장애 조치(Failover)할 수 있습니다. 보존 기간 후에는 복구 지점이 제거됩니다.
    - **크래시 일치 스냅숏**: 기본적으로 Site Recovery는 크래시 일치 스냅숏을 생성하고, 이 스냅숏을 사용해서 몇 분 간격으로 복구 지점을 만듭니다. 상호 연관된 모든 데이터 구성 요소의 쓰기 순서가 일치하는 경우가 복구 지점이 생성되었을 때에 해당되므로 복구 지점은 크래시 일치 상태입니다. 좀 더 잘 이해하기 위해 정전 또는 비슷한 이벤트 이후의 PC 하드 드라이브 데이터 상태를 가정해보겠습니다. 응용 프로그램이 데이터 불일치 없이 크래시에서 복구되도록 설계된 경우라는 대부분은 크래시 일치 복구 지점이면 충분합니다.
    - **앱 일치 스냅숏**: 이 값이 0이 아닌 경우 VM에서 실행되는 모바일 서비스는 파일 시스템 일치 스냅숏 및 복구 지점을 생성하려고 합니다. 첫 번째 스냅숏은 초기 복제가 완료된 후에 생성됩니다. 그런 후에는 지정한 빈도로 스냅숏이 생성됩니다. 복구 지점은 쓰기 순서 일치 외에도, 실행 중인 응용 프로그램이 모든 작업을 완료하고 해당 버퍼를 디스크에 플러시하는 경우(응용 프로그램 정지) 응용 프로그램 일치 상태가 됩니다. 앱 일치 복구 지점은 SQL, Oracle, Exchange 등의 데이터베이스 응용 프로그램에 권장됩니다. 크래시 일치 스냅숏으로 충분한 경우 이 값을 0으로 설정할 수 있습니다.  
    - **다중 VM 일치**: 필요에 따라 복제 그룹을 만들 수 있습니다. 그런 다음, 복제를 사용하도록 설정하면 해당 그룹으로 VM을 수집할 수 있습니다. 복제 그룹의 VM은 함께 복제되고 장애 조치(Failover) 시 공유 크래시 일치 및 앱 일치 복구 지점을 갖습니다. 스냅숏은 여러 컴퓨터에서 수집되어야 하므로 워크로드 성능에 영향을 줄 수 있습니다. 따라서 이 옵션은 신중하게 사용해야 합니다. VM이 동일한 워크로드를 실행하고 일관되어야 하며 비슷한 변동을 나타내는 경우에만 이 옵션을 사용하세요. 하나의 그룹에 최대 8개의 VM을 추가할 수 있습니다. 
5. **VM 복제를 사용하도록 설정**. 마지막으로, 온-프레미스 VMware VM에 대해 복제를 사용하도록 설정합니다. 모바일 서비스를 설치하기 위한 계정을 만들고 Site Recovery가 강제 설치를 수행하도록 지정한 경우, 모바일 서비스는 복제를 사용하도록 설정한 각 VM에 설치됩니다. [자세히 알아보기](vmware-azure-tutorial.md#enable-replication). 다중 VM 일치를 위해 복제 그룹을 만든 경우 해당 그룹에 VM을 추가할 수 있습니다.
6. **테스트 장애 조치(Failover)**. 모든 항목을 설정한 후 테스트 장애 조치(Failover)를 수행하여 VM이 예상대로 Azure로 장애 조치(Failover)되는지 확인할 수 있습니다. [자세히 알아보기](tutorial-dr-drill-azure.md).
7. **장애 조치(Failover)**. VM을 Azure로 마이그레이션하기만 하려는 경우 이 작업을 위해 전체 장애 조치(Failover)를 실행합니다. 재해 복구를 설정하는 경우에는 필요할 때 전체 장애 조치(Failover)를 실행할 수 있습니다. 전체 재해 복구의 경우 Azure로 장애 조치(Failover)한 후에 가능할 때 온-프레미스 사이트로 다시 장애 복구(Failback)할 수 있습니다. [자세히 알아보기](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>복제 프로세스

1. VM에 대한 복제를 사용하도록 설정하면 복제 정책에 따라 복제되기 시작합니다. 
2. 트래픽은 인터넷을 통해 Azure Storage 공용 끝점에 복제됩니다. Azure ExpressRoute과 [공용 피어링](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)을 함께 사용할 수도 있습니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. VM 데이터의 초기 복사본은 Azure Storage에 복제됩니다.
4. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. .hrl 파일에는 컴퓨터에 대한 추적된 변경 내용이 유지됩니다.
5. 다음과 같이 통신이 발생합니다.

    - VM은 복제 관리를 위해 HTTPS 443 인바운드 포트에 대한 온-프레미스 구성 서버와 통신합니다.
    - 구성 서버는 HTTPS 443 아웃바운드 포트를 통해 Azure를 사용하는 복제를 오케스트레이션합니다.
    - VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.


**VMware에서 Azure로 복제 프로세스**

![복제 프로세스](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

복제가 설정되고 재해 복구 훈련(테스트 장애 조치)을 실행하여 모든 것이 예상대로 작동하는지 확인한 후 필요에 따라 장애 조치와 장애 복구를 실행할 수 있습니다.

1. 단일 컴퓨터에서 장애 조치(Failover)를 수행하거나 여러 VM을 동시에 장애 조치(Failover)할 복구 계획을 만들 수 있습니다. 단일 컴퓨터 장애 조치(Failover) 이외의 복구 계획을 사용할 때의 이점은 다음과 같습니다.
    - 단일 복구 계획에 앱의 모든 VM을 포함하여 앱 종속성을 모델링할 수 있습니다.
    - 스크립트, Azure Runbook 및 수동 작업에 대한 일시 중지를 추가할 수 있습니다.
2. 초기 장애 조치를 트리거한 후 이를 커밋하여 Azure VM에서 워크로드 액세스를 시작합니다.
3. 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 장애 복구(Failback)를 준비할 수 있습니다. 장애 복구(Failback)를 위해서는 다음을 포함한 장애 복구 인프라를 설정해야 합니다.

    * **Azure의 임시 프로세스 서버**: Azure에서 장애 복구하려면 Azure에서 복제를 처리하는 프로세스 서버로 작동하도록 Azure VM을 설정합니다. 장애 복구를 완료한 후 이 VM을 삭제할 수 있습니다.
    * **VPN 연결**: 장애 복구하려면 Azure 네트워크에서 온-프레미스 사이트로의 VPN 연결(또는 ExpressRoute)이 필요합니다.
    * **별도의 마스터 대상 서버**: 기본적으로 구성 서버와 함께 온-프레미스 VMware VM에 설치된 마스터 대상 서버에서 장애 복구를 처리합니다. 대량 트래픽을 장애 복구해야 하는 경우 별도의 온-프레미스 마스터 대상 서버를 이 용도로 설정합니다.
    * **장애 복구 정책**: 온-프레미스 사이트에 다시 복제하려면 장애 복구 정책이 필요합니다. 이 정책은 온-프레미스에서 Azure로의 복제 정책을 만들 때 자동으로 만들어졌습니다.
4. 구성 요소를 배치한 후 다음 3가지 동작에 따라 장애 복구(Failback)가 발생합니다.

    - 1단계: Azure VM을 다시 보호하여 Azure에서 다시 온-프레미스 VMware VM으로의 복제하도록 합니다.
    -  2단계: 온-프레미스 사이트에서 장애 조치를 실행합니다.
    - 3단계: 워크로드가 장애 복구되면 온-프레미스 VM에 대한 복제를 다시 사용하도록 설정합니다.
    
 
**Azure로부터 VMware 장애 복구**

![장애 복구](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>다음 단계

[이 자습서](vmware-azure-tutorial.md)를 따라 VMware와 Azure 간의 복제를 활성화합니다.
