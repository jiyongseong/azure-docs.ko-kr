---
title: Azure 가상 머신 장애 복구에서 ExpressRoute 사용 | Microsoft Docs
description: Azure 가상 머신 재해 복구에 대해 Azure Site Recovery에서 Azure ExpressRoute를 사용하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071590"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Azure 가상 머신 장애 복구에서 ExpressRoute 사용

Microsoft Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. 이 아티클에서는 Azure 가상 머신의 재해 복구에 대해 Site Recovery에서 Azure ExpressRoute를 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 이해해야 합니다.
-   ExpressRoute [회로](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [라우팅 도메인](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Azure 가상 머신 [복제 아키텍처](azure-to-azure-architecture.md)
-   Azure 가상 머신의 [복제 설정](azure-to-azure-tutorial-enable-replication.md)
-   Azure 가상 머신 [장애 조치](azure-to-azure-tutorial-failover-failback.md)

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute 및 Azure 가상 머신 복제

Site Recovery에서 Azure 가상 머신을 보호할 때 Azure 가상 머신이 [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md)를 사용하는지 여부에 따라 복제 데이터가 대상 Azure 지역의 Azure Storage 계정 또는 복제본 Managed Disks로 전송됩니다. 복제 엔드포인트가 공용이라고 해도 기본적으로 Azure VM 복제에 대한 복제 트래픽은 원본 가상 네트워크가 존재하는 Azure 지역과 무관하게 인터넷을 통과하지 않습니다.

Azure VM 재해 복구의 경우 복제 데이터가 Azure 경계를 넘지 않으므로 ExpressRoute가 복제에 필요하지 않습니다. 가상 머신이 대상 Azure 지역으로 장애 조치된 후 [개인 피어링](../expressroute/expressroute-circuit-peerings.md#azure-private-peering)을 사용하여 해당 가상 머신에 액세스할 수 있습니다.

## <a name="replicating-azure-deployments"></a>Azure 배포 복제

이전 [아티클](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity)에서는 ExpressRoute를 통해 고객 온-프레미스 데이터 센터에 연결된 하나의 Azure 가상 네트워크를 사용하는 간단한 설치를 설명했습니다. 일반적인 엔터프라이즈 배포에는 여러 Azure 가상 네트워크에 분할된 워크로드가 있고 중앙 연결 허브는 인터넷 및 온-프레미스 배포 모두에 대한 외부 연결을 설정합니다.

이 예제에서는 엔터프라이즈 배포에서 일반적으로 사용되는 허브 및 스포크 토폴로지를 설명합니다.
-   배포는 **Azure 동아시아** 지역에 해당되며 온-프레미스 데이터 센터는 홍콩에 있는 파트너 에지를 통해 ExpressRoute 회로에 연결됩니다.
-   응용 프로그램은 주소 공간 10.1.0.0/24를 포함한 **원본 VNet1** 및 주소 공간 10.2.0.0/24를 포함한 **원본 VNet2**라는 두 개의 스포크 가상 네트워크를 통해 배포됩니다.
-   주소 공간 10.10.10.0/24를 포함한 허브 가상 네트워크인 **원본 허브 VNet**은 게이트키퍼의 역할을 담당합니다. 서브넷 간 통신은 모두 허브를 통해 이루어집니다.
-   허브 가상 네트워크에는 주소 공간 10.10.10.0/25를 포함한 **NVA 서브넷** 및 주소 공간 10.10.10.128/25를 포함한 **게이트웨이 서브넷**이라는 두 서브넷이 있습니다.
-   **NVA 서브넷**에는 10.10.10.10 IP 주소를 포함한 네트워크 가상 어플라이언스가 있습니다.
-   **게이트웨이 서브넷**에는 개인 피어링 라우팅 도메인을 통해 고객 온-프레미스 데이터 센터로 라우팅하는 ExpressRoute 연결에 연결된 ExpressRoute 게이트웨이가 있습니다.
-   각 스포크 가상 네트워크가 허브 가상 네트워크에 연결되고 이 네트워크 토폴로지 내의 모든 라우팅은 Azure 경로 테이블(UDR)을 통해 제어됩니다. VNet 간 또는 VNet에서 온-프레미스 데이터 센터 간의 모든 아웃바운드 트래픽은 NVA를 통해 라우팅됩니다.

![장애 조치(Failover) 전에 ExpressRoute를 포함한 온-프레미스 및 Azure](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>허브 및 스포크 피어링

허브 피어링에 대한 스포크에는 다음과 같은 구성이 있습니다.
-   Virtual Network 주소 공간 허용: 사용
-   전달된 트래픽 허용: 사용
-   게이트웨이 전송 허용: 사용 안 함
-   게이트웨이 제거 사용: 사용

 ![허브 피어링 구성에 대한 스포크](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

스포크 피어링에 대한 허브에는 다음과 같은 구성이 있습니다.
-   Virtual Network 주소 공간 허용: 사용
-   전달된 트래픽 허용: 사용
-   게이트웨이 전송 허용: 사용
-   게이트웨이 제거 사용: 사용 안 함

 ![스포크 피어링 구성에 대한 허브](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>배포에 복제 사용

위의 설정에서 먼저 Site Recovery를 사용하는 모든 가상 머신에 [재해 복구를 설정합니다](azure-to-azure-tutorial-enable-replication.md). Site Recovery는 대상 지역에서 복제본 가상 네트워크(서브넷, 게이트웨이 서브넷 포함)를 만들고, 원본과 대상 가상 네트워크 간에 필수 매핑을 만들 수 있습니다. 대상 쪽 네트워크 및 서브넷을 미리 만들 수 있고 복제를 사용하는 동안 동일한 기능을 사용할 수도 있습니다.

Site Recovery는 경로 테이블, 가상 네트워크 게이트웨이, 가상 네트워크 게이트웨이 연결, 가상 네트워크 피어링 또는 기타 네트워킹 리소스나 연결을 복제하지 않습니다. [복제 프로세스](azure-to-azure-architecture.md#replication-process)의 일부가 아닌 이러한 항목 및 기타 리소스는 장애 조치가 완료되기 전에 생성되고 관련된 리소스에 연결되어야 합니다. Azure Site Recovery의 강력한 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 자동화 스크립트를 사용하는 추가 리소스를 만들고 연결하도록 자동화할 수 있습니다.

기본적으로 복제 트래픽은 Azure 경계에 남지 않습니다. 또한 NVA 배포는 일반적으로 아웃바운드 인터넷 트래픽이 NVA를 통과하도록 강제하는 기본 경로(0.0.0.0/0)를 정의합니다. 이 경우에 모든 복제 트래픽이 NVA를 통과하면 어플라이언스가 제한될 수 있습니다. 온-프레미스 배포에 모든 Azure VM 트래픽을 라우팅하기 위해 기본 경로를 사용하는 경우에 동일한 항목이 적용됩니다. 복제 트래픽이 Azure 경계를 나가지 않도록 "Storage"용 가상 네트워크에서 [가상 네트워크 서비스 엔드포인트](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)를 사용하는 것이 좋습니다.

## <a name="failover-models-with-expressroute"></a>ExpressRoute를 사용하는 장애 조치 모델

Azure 가상 머신이 다른 지역으로 장애 조치되는 경우 원본 가상 네트워크에 대한 기존 ExpressRoute 연결이 자동으로 복구 지역의 대상 가상 네트워크에 전송되지 않습니다. ExpressRoute를 대상 가상 네트워크에 연결하는 데 새 연결이 필요합니다.

[여기](azure-to-azure-support-matrix.md#region-support)에 설명된 대로 동일한 지리적 클러스터 내에서 Azure 가상 머신을 Azure 지역에 복제할 수 있습니다. 원본과 동일한 지리적 지역 내에 선택한 대상 Azure 지역이 없는 경우 원본 및 대상 지역 연결에 단일 ExpressRoute 회로를 사용하려면 ExpressRoute 프리미엄을 활성화해야 합니다. 자세한 내용은 [ExpressRoute 위치](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) 및 [ExpressRoute 가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 확인하세요.

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>다른 두 ExpressRoute 피어링 위치의 두 개의 ExpressRoute 회로
-   이 구성은 ExpressRoute 피어링 위치에 영향을 주고 주 ExpressRoute 회로를 방해할 수 있는 주 ExpressRoute 회로의 오류 및 대규모 지역 재해에 대해 보장하려는 경우 유용합니다.
-   일반적으로 프로덕션 환경에 연결된 회로는 주 회로로 사용되고 보조 회로는 failsafe이며 일반적으로 낮은 대역폭입니다. 보조 회로가 주 회로를 대신해야 하는 경우 재해 이벤트에서 보조 회로의 대역폭을 늘릴 수 있습니다.
-   이 구성을 사용하여 장애 조치 후에 보조 ExpressRoute 회로에서 대상 가상 네트워크로 연결을 설정할 수 있습니다. 또는 연결을 설정하고 재해 선언에 대한 준비를 마쳐서 전체 복구 시간을 줄일 수 있습니다. 주 및 대상 지역 가상 네트워크 모두에 대한 동시 연결을 사용하여 온-프레미스 라우팅이 장애 조치한 후에만 보조 회로 및 연결을 사용하는지 확인합니다.
-   Site Recovery로 보호된 VM의 원본 및 대상 가상 네트워크에는 요구 사항에 따라 장애 조치 시 동일하거나 다른 IP 주소가 있을 수 있습니다. 두 경우 모두에 장애 조치하기 전에 보조 연결을 설정할 수 있습니다.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>동일한 ExpressRoute 피어링 위치의 두 개의 ExpressRoute 회로
-   이 구성에서는 주 ExpressRoute 회로의 오류에 대해 보장할 수 있지만 ExpressRoute 피어링 위치에 영향을 줄 수 있는 대규모 지역 재해에 대해서는 없습니다. 후자의 경우 주 및 보조 회로는 모두 영향을 받을 수 있습니다.
-   IP 주소 및 연결에 대한 기타 조건은 이전 경우와 동일하게 유지합니다. 기본 회로를 사용하여 온-프레미스 데이터 센터에서 원본 가상 네트워크 및 보조 회로를 사용하여 대상 가상 네트워크로 동시에 연결할 수 있습니다. 주 및 대상 지역 가상 네트워크 모두에 대한 동시 연결을 사용하여 온-프레미스 라우팅이 장애 조치한 후에만 보조 회로 및 연결을 사용하는지 확인합니다.
-   회로가 동일한 피어링 위치에 생성되면 동일한 가상 네트워크에 두 회로를 모두 연결할 수 없습니다.

### <a name="single-expressroute-circuit"></a>단일 ExpressRoute 회로
-   이 구성은 ExpressRoute 피어링 위치에 영향을 줄 수 있는 대규모 지역 재해에 대해 보장하지 않습니다.
-   단일 ExpressRoute 회로에서는 동일한 IP 주소 공간을 대상 지역에 사용하는 경우 원본 및 대상 가상 네크워크를 회로에 동시에 연결할 수 없습니다.
-   대상 지역에 동일한 IP 주소 공간을 사용하는 경우 원본 쪽 연결을 끊어야 하고 대상 쪽 연결을 이후에 설정해야 합니다. 연결 변경 내용은 복구 계획의 일환으로 스크립팅될 수 있습니다.
-   지역 오류에서 주 지역에 액세스할 수 없으면 연결 끊기 작업에 실패할 수 있습니다. 이러한 중단은 대상 가상 네트워크에서 동일한 IP 주소 공간을 사용할 때 대상 지역에 연결하는 작업에 영향을 줄 수 있습니다.
-   대상에서 연결에 성공하면 주 지역이 나중에 복구되는 경우 두 개의 동시 연결이 동일한 주소 공간에 연결하려면 패킷이 삭제될 수 있습니다. 패킷 삭제를 방지하려면 주 연결을 즉시 종료해야 합니다. 주 지역에 대한 가상 머신을 장애 복구하면 주 연결은 보조 연결을 끊은 후에 다시 설정할 수 있습니다.
-   대상 가상 네트워크에서 다른 주소 공간을 사용하는 경우 동일한 ExpressRoute 회로에서 동시에 원본 및 대상 가상 네트워크에 연결할 수 있습니다.

## <a name="recovering-azure-deployments"></a>Azure 배포 복구
다른 두 피어링 위치에서 다른 두 개의 ExpressRoute 회로를 사용하는 장애 조치 모델 및 보호된 Azure 가상 머신의 개인 IP 주소 유지를 사용하도록 고려합니다. 대상 복구 지역은 Azure 동남 아시아이며 보조 ExpressRoute 회로 연결은 싱가포르에 있는 파트너 에지를 통해 설정됩니다.

전체 배포 복구를 자동화하려면 가상 머신 및 가상 네트워크를 복제하는 것외에도 다른 관련 네트워킹 리소스 및 연결도 만들어야 합니다. 이전 허브 및 스포크 네트워크 토폴로지의 경우 [장애 조치](azure-to-azure-tutorial-failover-failback.md) 작업이 완료되기 전에 다음 추가 단계가 수행되어야 합니다.
1.  대상 지역 허브 가상 네트워크에서 Azure ExpressRoute 게이트웨이를 만듭니다. 대상 허브 가상 네트워크를 ExpressRoute 회로에 연결하는 데 ExpressRoute 게이트웨이가 필요합니다.
2.  대상 허브 가상 네트워크에서 대상 ExpressRoute 회로에 가상 네트워크 연결을 만듭니다.
3.  대상 지역의 허브와 스포크 가상 네트워크 간에 VNet 피어링을 설정합니다. 대상 지역의 피어링 속성은 원본 지역에 있는 속성과 동일합니다.
4.  허브 VNet의 UDR 및 두 개의 스포크 VNet을 설정합니다. 동일한 IP 주소를 사용할 때 대상 쪽 UDR의 속성이 원본 쪽 속성과 동일합니다. 다른 대상 IP 주소에서 UDR은 적절하게 수정해야 합니다.

위의 단계는 [복구 계획](site-recovery-create-recovery-plans.md)의 일환으로 스크립팅될 수 있습니다. 응용 프로그램 연결 및 복구 시간 요구 사항에 따라 위의 단계는 장애 조치를 시작하기 전에 완료될 수도 있습니다.

가상 머신을 복구하고 기타 연결 단계를 완료한 다음, 복구 환경은 다음과 같습니다: ![장애 조치 후 ExpressRoute를 사용하는 온-프레미스 Azure 간](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

대상 가상 머신에서 동일한 IP 및 단일 ExpressRoute 회로를 사용하는 Azure VM 재해 복구에 대한 간단한 토폴로지 예제는 [여기](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity)에서 자세히 설명합니다.

## <a name="recovery-time-objective-rto-considerations"></a>RTO(복구 시간 목표) 고려 사항
배포에 대한 전체 복구 시간을 줄이려면 미리 가상 네트워크 게이트웨이 등 추가 대상 지역 [네트워킹 구성 요소](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment)를 프로비전하고 배포하는 것이 좋습니다. 짧은 가동 중지 시간은 추가 리소스를 배포하는 작업과 연결되어 있습니다. 이 가동 중지 시간을 계획하는 동안 계산하지 않으면 전체 복구 시간에 영향을 줄 수 있습니다.

보호된 배포에 기본 [재해 복구 훈련](azure-to-azure-tutorial-dr-drill.md)을 실행하는 것이 좋습니다. 훈련은 데이터 손실이나 가동 중지 시간 없이 복제 전략의 유효성을 검사하며 프로덕션 환경에 영향을 미치지 않습니다. 또한 드릴을 실행하면 복구 시간 목표에 부정적인 영향을 줄 수 있는 최신 구성 문제를 방지합니다. 복제를 활성화할 때 설정된 기본 네트워크 대신 테스트 장애 조치에 대한 별도의 Azure VM 네트워크를 사용하는 것이 좋습니다.

단일 ExpressRoute 회로를 사용하는 경우 지역 재해가 발생하는 동안 연결 설정 문제를 방지하기 위해 대상 가상 네트워크에 다른 IP 주소 공간을 사용하는 것이 좋습니다. 다른 IP 주소를 사용하면 복구된 프로덕션 환경에 적절하지 않은 경우 재해 복구 훈련 테스트 장애 조치를 다른 IP 주소를 사용하는 별도 테스트 네트워크에서 수행해야 합니다. 동일한 ExpressRoute 회로에 IP 주소 공간이 겹치는 두 가상 네트워크를 연결할 수 없기 때문입니다.

## <a name="next-steps"></a>다음 단계
- [ExpressRoute 회로](../expressroute/expressroute-circuit-peerings.md)에 대해 자세히 알아봅니다.
- [ExpressRoute 라우팅 도메인](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)에 대해 자세히 알아봅니다.
- [ExpressRoute 위치](../expressroute/expressroute-locations.md)에 대해 자세히 알아봅니다.
- 응용 프로그램 장애 조치(failover)를 자동화하는 [복구 계획](site-recovery-create-recovery-plans.md)에 대해 자세히 알아봅니다.
