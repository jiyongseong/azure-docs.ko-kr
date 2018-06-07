---
title: Azure 가상 머신을 다른 Azure 지역으로 장애 조치(failover)하는 경우 IP 주소 유지 | Microsoft Docs
description: Azure Site Recovery로 Azure 장애 조치(failover) 시나리오에 Azure의 IP 주소를 유지하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 9a9c18bfe9073e5af94c7bd8f0fbb91651387731
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071667"
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Azure 가상 머신 장애 조치(failover)를 위한 IP 주소 보존

Azure Site Recovery를 통해 Azure VM에 대한 재해 복구가 가능합니다. 한 Azure 지역에서 다른 지역으로 장애 조치(failover)하는 경우 고객이 자체 IP 구성을 보존하도록 요구하는 경우가 많습니다. 기본적으로 Site Recovery는 대상 지역에 해당 리소스를 생성할 때 원본 가상 네트워크와 서브넷 구조를 모방합니다. 고정 개인 IP 주소로 구성된 Azure VM에 대해, Site Recovery는 해당 IP가 Azure 리소스 또는 복제된 VM에서 이미 차단되지 않은 경우, 대상 VM에 동일한 개인 IP를 프로비전하기 위해 최선의 노력을 기울입니다.

간단한 응용 프로그램의 경우 위의 기본 구성만 있으면 됩니다. 복잡한 엔터프라이즈 응용 프로그램의 경우 인프라의 다른 구성 요소와 장애 조치 후 연결을 보장하기 위해 추가 네트워킹 리소스를 프로비전하는 것이 필요할 수 있습니다. 이 문서에서는 VM IP 주소를 유지하면서 한 지역에서 다른 지역으로 Azure VM을 장애 조치(failover)하기 위한 네트워킹 요구 사항을 설명합니다.

## <a name="azure-to-azure-connectivity"></a>Azure 간 연결

첫 번째 시나리오는 모든 응용 프로그램 인프라를 Azure에서 실행 중인 **A사**의 경우입니다. **A사**는 비즈니스 연속성 및 규정 준수를 위해 Azure Site Recovery를 사용하여 자사의 응용 프로그램을 보호하기로 결정했습니다.

IP 보존 요구 사항(예: 응용 프로그램 바인딩)을 고려하여 A사에는 대상 지역과 동일한 가상 네트워크 및 서브넷 구조가 있습니다. RTO(복구 시간 목표)를 감소시키기 위해서 **A사**는 SQL Always ON, 도메인 컨트롤러 등의 복제본 노드를 활용하며, 이러한 복제본 노드는 대상 지역의 다른 가상 네트워크에 배치됩니다. 복제본 노드에 다른 주소 공간을 사용하면 **A사**는 원본과 대상 지역 사이에 VPN 사이트 간 연결을 설정할 수 있습니다. 양쪽에 동일한 주소 공간을 사용하는 경우에는 이런 설정이 불가능합니다.

다음은 장애 조치(failover) 전 네트워크 아키텍처의 모습입니다.
- 응용 프로그램 VM은 주소 공간이 10.1.0.0/16인 Azure 가상 네트워크를 활용하여 Azure 동아시아에서 호스트됩니다. 이 가상 네트워크의 이름은 **Source VNet**입니다.
- 응용 프로그램 워크로드는 서브넷 세 곳(10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24)으로 분할되며 각각의 이름은 **Subnet 1**, **Subnet 2**, **Subnet 3**입니다.
- Azure 동남 아시아는 대상 지역이며 원본의 주소 공간 및 서브넷 구성을 모방한 복구 가상 네트워크가 있습니다. 이 가상 네트워크의 이름은 **Recovery VNet**입니다.
- Always On에 필요한 복제본 노드, 도메인 컨트롤러 등은 주소가 10.2.4.0/24인 Subnet 4 내에 주소 공간이 10.2.0.0/16인 가상 네트워크에 배치됩니다. 가상 네트워크 이름은 **Azure VNet**이며 Azure 동남 아시아에 있습니다.
- **Source VNet**과 **Azure VNet**은 VPN 사이트 간 연결을 통해 연결됩니다.
- **Recovery VNet**은 다른 가상 네트워크와 연결되어 있지 않습니다.
- **A사**는 복제된 항목의 대상 IP 주소를 할당/확인합니다. 이 예에서 대상 IP는 각 VM의 원본 IP와 같습니다.

![장애 조치(failover) 전 Azure 간 연결](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>전체 지역 장애 조치(failover)

지역에 중단이 발생하는 경우 **A사**는 Azure Site Recovery의 강력한 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 쉽고 빠르게 전체 배포를 복구할 수 있습니다. 장애 조치 전에 각 VM의 대상 IP 주소를 미리 설정했기 때문에 **A사**는 아래 그림과 같이 장애 조치를 오케스트레이션하고 Recovery VNet과 Azure Vnet 간의 연결 설정을 자동화할 수 있습니다.

![Azure 간 연결 전체 지역 장애 조치(failover)](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

응용 프로그램 요구 사항에 따라 대상 영역의 두 VNet 간 연결은 장애 조치 전, 중(중간 단계로) 또는 후에 설정할 수 있습니다. [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 스크립트를 추가하고 장애 조치(failover) 순서를 정의합니다.

A사는 VNet 피어링 또는 사이트 간 VPN을 사용하여 Recovery VNet과 Azure VNet 사이의 연결을 설정할 수도 있습니다. VNet 피어링은 VPN 게이트웨이를 사용하지 않으며 다른 제약 조건이 있습니다. 또한 [VNet 피어링 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)은 [VNet-VNet VPN Gateway 가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway)과 다르게 계산됩니다. 장애 조치(failover)의 경우 네트워크 변경으로 인한 예기치 않은 인시던트를 최소화하기 위해 연결 유형을 비롯한 원본 연결을 모방하는 것이 좋습니다.

### <a name="isolated-application-failover"></a>격리된 응용 프로그램 장애 조치(failover)

특정 조건에서 사용자는 응용 프로그램 인프라의 일부를 장애 조치(failover)해야 할 수도 있습니다. 한 가지 예는 전용 서브넷 내에 있는 특정 응용 프로그램이나 계층을 장애 조치(failover)하는 경우입니다. IP 보존을 통한 서브넷 장애 조치(failover)가 가능하지만 대부분의 상황에서는 연결 불일치가 상당히 증가하므로 권장하지 않습니다. 또한 동일한 Azure 가상 네트워크 내에 있는 다른 서브넷에 대한 서브넷 연결이 끊어집니다.

서브넷 수준의 응용 프로그램 장애 조치(failover) 요구 사항을 처리하는 더 좋은 방법은 장애 조치에 서로 다른 대상 IP 주소를 사용하거나(원본 가상 네트워크의 다른 서브넷에 연결이 필요한 경우) 원본의 전용 가상 네트워크에 있는 각각의 응용 프로그램을 격리하는 것입니다. 후자의 방식을 사용하면 원본에서 네트워크 간 연결을 설정하고 대상 지역으로 장애 조치할 때 동일한 연결을 에뮬레이션할 수 있습니다.

복원력을 위해 개별 응용 프로그램을 설계하려면 전용 가상 네트워크에 응용 프로그램을 배치하고 필요에 따라 이러한 가상 네트워크 간의 연결을 설정하는 것이 좋습니다. 이렇게 하면 원래 개인 IP 주소를 유지하면서 격리된 응용 프로그램 장애 조치(failover)가 가능합니다.

사후 장애 조치(failover) 구성은 다음과 같습니다.
- 응용 프로그램 VM은 첫 번째 응용 프로그램의 주소 공간이 10.1.0.0/16이고 두 번째 응용 프로그램의 주소 공간이 10.2.0.0/16인 Azure 가상 네트워크를 활용하여 Azure 동아시아에서 호스트됩니다. 가상 네트워크의 첫 번째 및 두 번째 응용 프로그램 이름은 각각 **Source VNet1** 및 **Source VNet2**입니다.
- 각 VNet은 두 개의 서브넷으로 각각 분할됩니다.
- Azure 동남 아시아는 대상 지역이며 복구 가상 네트워크인 Recovery VNet1 및 Recovery VNet2가 있습니다.
- Always On에 필요한 복제본 노드, 도메인 컨트롤러 등은 주소가 10.3.4.0/24인 **Subnet 4** 내에 주소 공간이 10.3.0.0/16인 가상 네트워크에 배치됩니다. 가상 네트워크는 Azure VNet이라고 하며 Azure 동남 아시아에 있습니다.
- **Source VNet1**과 **Azure VNet**은 VPN 사이트 간 연결을 통해 연결됩니다. 유사하게 **Source VNet2**와 **Azure VNet**도 VPN 사이트 간 연결을 통해 연결됩니다.
- **Source VNet1** 및 **Source VNet2** 역시 이 예제에서 S2S VPN을 통해 연결됩니다. 두 VNet이 같은 지역에 있기 때문에 S2S VPN 대신 VNet 피어링을 사용할 수도 있습니다.
- **Recovery VNet1**과 **Recovery VNet2**는 다른 가상 네트워크와 연결되어 있지 않습니다.
- RTO(복구 시간 목표)를 감소시키기 위해 장애 조치 전에 **Recovery VNet1**과 **Recovery VNet2**에 VPN 게이트웨이가 구성됩니다.

![장애 조치(failover) 전 Azure 간 연결 격리된 응용 프로그램](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

하나의(이 예제의 경우 Source VNet2에 배치된) 응용 프로그램에만 영향을 미치는 재해 상황이 발생하는 경우 A사는 다음과 같이 해당 응용 프로그램을 복구할 수 있습니다.
- **Source VNet1**과 **Source VNet2** 및 **Source VNet2**와 **Azure VNet** 사이의 VPN 연결이 해제됩니다.
- **Source VNet1**과 **Recovery VNet2** 및 **Recovery VNet2**와 **Azure VNet** 사이에 VPN 연결이 설정됩니다.
- **Source VNet2**의 VM이 **Recovery VNet2**로 장애 조치(failover)됩니다.

![장애 조치(failover) 후 Azure 간 연결 격리된 응용 프로그램](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

위의 격리된 장애 조치(failover) 예제는 더 많은 응용 프로그램과 네트워크 연결을 포함하도록 확장할 수 있습니다. 권장 사항은 원본에서 대상으로 장애 조치(failover)할 때 가능하면 유사한 연결 모델을 따르는 것입니다.

### <a name="further-considerations"></a>추가 고려 사항

VPN 게이트웨이는 공용 IP 주소와 게이트웨이 홉을 사용하여 연결을 설정합니다. 공용 IP를 사용하지 않으려는 경우 및/또는 추가 홉을 방지하려는 경우 Azure [Virtual Network 피어 링](../virtual-network/virtual-network-peering-overview.md)을 사용하여 [지원되는 Azure 지역](../virtual-network/virtual-network-manage-peering.md#cross-region)에서 가상 네트워크를 피어링할 수 있습니다.

## <a name="on-premises-to-azure-connectivity"></a>온-프레미스에서 Azure 연결

두 번째 시나리오는 응용 프로그램 인프라의 일부를 Azure에서 실행 중이고 나머지는 온-프레미스에서 실행 중인 **B사**의 경우입니다. **B사**는 비즈니스 연속성 및 규정 준수를 위해 Azure Site Recovery를 사용하여 Azure에서 실행 중인 자사의 응용 프로그램을 보호하기로 결정했습니다.

다음은 장애 조치(failover) 전 네트워크 아키텍처의 모습입니다.
- 응용 프로그램 VM은 주소 공간이 10.1.0.0/16인 Azure 가상 네트워크를 활용하여 Azure 동아시아에서 호스트됩니다. 이 가상 네트워크의 이름은 **Source VNet**입니다.
- 응용 프로그램 워크로드는 서브넷 세 곳(10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24)으로 분할되며 각각의 이름은 **Subnet 1**, **Subnet 2**, **Subnet 3**입니다.
- Azure 동남 아시아는 대상 지역이며 원본의 주소 공간 및 서브넷 구성을 모방한 복구 가상 네트워크가 있습니다. 이 가상 네트워크의 이름은 **Recovery VNet**입니다.
- Azure 동아시아의 VM은 ExpressRoute 또는 사이트 간 VPN을 통해 온-프레미스 데이터 센터에 연결되어 있습니다.
- RTO(복구 시간 목표)를 줄이기 위해 B사는 장애 조치 전에 Azure 동남 아시아의 Recovery VNet에 게이트웨이를 프로비전합니다.
- **B사**는 복제된 항목의 대상 IP 주소를 할당/확인합니다. 이 예에서 대상 IP는 각 VM의 원본 IP와 같습니다.

![장애 조치(failover) 전 온-프레미스에서 Azure 연결](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>전체 지역 장애 조치(failover)

지역에 중단이 발생하는 경우 **B사**는 Azure Site Recovery의 강력한 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 쉽고 빠르게 전체 배포를 복구할 수 있습니다. 장애 조치 전에 각 VM의 대상 IP 주소를 미리 설정했기 때문에 **B사**는 아래 그림과 같이 장애 조치를 오케스트레이션하고 Recovery VNet과 온-프레미스 데이터 센터 간의 연결 설정을 자동화할 수 있습니다.

Azure 동남 아시아와 온-프레미스 데이터 센터 간에 연결을 설정하기 전에 Azure 동아시아와 온-프레미스 데이터 센터 간의 원래 연결을 끊어야 합니다. 온-프레미스 라우팅은 대상 지역 및 게이트웨이 사후 장애 조치(failover)를 가리키도록 다시 구성됩니다.

![장애 조치(failover) 후 온-프레미스에서 Azure 연결](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>서브넷 장애 조치(failover)

**A사**에 대해 설명한 Azure 간 시나리오와 달리 **B사**의 경우에 대해서는 서브넷 수준 장애 조치가 불가능합니다. 이것은 원본 및 복구 가상 네트워크의 주소 공간이 동일하고 원래 원본과 온-프레미스 연결이 활성 상태이기 때문입니다.

응용 프로그램 복원력을 위해서는 각 응용 프로그램을 전용 Azure 가상 네트워크에 배치하는 것이 좋습니다. 그러면 응용 프로그램이 격리된 상태로 장애 조치(failover)될 수 있고 위의 설명처럼 필요한 온-프레미스에서 원본 연결을 대상 지역으로 라우팅할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [복구 계획](site-recovery-create-recovery-plans.md)에 대해 자세히 알아봅니다.
