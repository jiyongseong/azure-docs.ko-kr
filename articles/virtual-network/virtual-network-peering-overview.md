---
title: Azure Virtual Network 피어링 | Microsoft Docs
description: Azure의 가상 네트워크 피어링에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: jdial
ms.openlocfilehash: f2dddb6e9a933c1dc1eb4ccbf7ace6757d546fc8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367109"
---
# <a name="virtual-network-peering"></a>가상 네트워크 피어링

가상 네트워크 피어링을 사용하면 두 개의 Azure [가상 네트워크](virtual-networks-overview.md)를 원활하게 연결할 수 있습니다. 가상 네트워크가 피어링되면 연결성을 위해 하나로 표시됩니다. 트래픽이 *개인* IP 주소를 통해서만 동일한 가상 네트워크에 있는 가상 네트워크 간에 라우팅되는 것과 마찬가지로 피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. Azure 지원:
* VNet 피어링 - 동일한 Azure 지역 내에 있는 VNet 연결
* 글로벌 VNet 피어링 - Azure 지역 간 VNet 연결

로컬 또는 글로벌 가상 네트워크 피어링을 사용하는 이점은 다음과 같습니다.

* 피어링된 가상 네트워크 간의 네트워크 트래픽이 개인 전용입니다. 가상 네트워크 간의 트래픽이 Microsoft 백본 네트워크에서 유지됩니다. 가상 네트워크 간의 통신에 공용 인터넷, 게이트웨이, 또는 암호화가 필요하지 않습니다.
* 다른 가상 네트워크에 있는 리소스 간에 짧은 대기 시간, 높은 대역폭 연결
* 가상 네트워크가 피어링되면 하나의 가상 네트워크에 있는 리소스가 다른 가상 네트워크에 있는 리소스와 통신할 수 있습니다.
* Azure 구독, 배포 모델 및 Azure 지역에 데이터를 전송할 수 있습니다.
* Azure Resource Manager를 통해 만든 가상 네트워크를 피어링하거나 클래식 배포 모델을 통해 만든 가상 네트워크에 Resource Manager를 통해 만든 다른 가상 네트워크를 피어링할 수 있는 기능입니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
* 피어링을 만들 때 또는 피어링이 생성된 후에 각 가상 네트워크에 있는 리소스에 대한 가동 중지 시간이 없습니다.

## <a name="connectivity"></a>연결

가상 네트워크가 피어링된 후에 가상 네트워크의 리소스는 피어링된 가상 네트워크의 리소스와 직접 연결할 수 있습니다.

동일한 지역에서 피어링된 가상 네트워크에 있는 가상 머신 간의 네트워크 대기 시간은 단일 가상 네트워크 내의 대기 시간과 같습니다. 네트워크 처리량은 해당 크기에 비례하는 가상 머신에 허용되는 대역폭을 기반으로 합니다. 피어링 내에서 대역폭에 대한 추가 제한이 없습니다.

피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 게이트웨이나 공용 인터넷을 통해서가 아니라 Microsoft 백본 인프라를 통해 직접 라우팅됩니다.

원하는 경우 다른 가상 네트워크 또는 서브넷에 대한 액세스를 차단하기 위해 네트워크 보안 그룹을 각 가상 네트워크에 적용할 수 있습니다.
가상 네트워크 피어링을 구성할 때 가상 네트워크 간에 네트워크 보안 그룹 규칙을 열거나 닫을 수 있습니다. 피어링된 가상 네트워크 간의 전체 연결을 여는 경우(기본 옵션임) 특정 서브넷 또는 가상 머신에 네트워크 보안 그룹을 적용하여 특정 액세스를 차단하거나 거부할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md)를 참조하세요.

## <a name="service-chaining"></a>서비스 체이닝

피어링된 가상 네트워크에서 가상 머신을 가리키는 사용자 정의 경로를 *다음 홉* IP 주소나 가상 네트워크 게이트웨이로 구성하여 서비스 체이닝을 사용할 수 있습니다. 서비스 체이닝을 사용하면 사용자 정의 경로를 통해 피어링된 가상 네트워크의 가상 네트워크에서 가상 어플라이언스로, 또는 가상 네트워크 게이트웨이로 트래픽을 전송할 수 있습니다.

허브 가상 네트워크가 네트워크 가상 어플라이언스 또는 VPN 게이트웨이와 같은 인프라 구성 요소를 호스트할 수 있는 허브 및 스포크 네트워크를 배포할 수 있습니다. 모든 스포크 가상 네트워크는 허브 가상 네트워크와 피어링할 수 있습니다. 트래픽은 허브 가상 네트워크의 네트워크 가상 어플라이언스 또는 VPN 게이트웨이를 통해 이동할 수 있습니다. 

가상 네트워크 피어링을 사용하면 사용자 정의 경로의 다음 홉이 피어링된 가상 네트워크 또는 VPN 게이트웨이에 있는 가상 머신의 IP 주소가 될 수 있습니다. 단 ExpressRoute 게이트웨이를 다음 홉 형식으로 지정하는 사용자 정의 경로를 사용하여 가상 네트워크 사이에 라우팅할 수 없습니다. 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요](virtual-networks-udr-overview.md#user-defined)를 참조하세요. 허브 및 스포크 네트워크 토폴로지를 만드는 방법에 대한 자세한 내용은 [허브 및 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)를 참조하세요.

## <a name="gateways-and-on-premises-connectivity"></a>게이트웨이 및 온-프레미스 연결

각 가상 네트워크는 다른 가상 네트워크와 피어링되었는지 여부와 상관없이 고유한 게이트웨이를 가지고 있으며 이를 온-프레미스 네트워크에 연결하는 데 사용할 수 있습니다. 가상 네트워크가 피어링된 경우에도 게이트웨이를 사용하여 [가상 네트워크 간 연결](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 구성할 수도 있습니다.

가상 네트워크 연결을 위한 옵션을 모두 구성한 경우 가상 네트워크 간의 트래픽은 피어링 구성(즉, Azure 백본)을 통해 전달됩니다.

동일한 지역에서 가상 네트워크가 피어링되면 피어링된 가상 네트워크에 있는 게이트웨이를 온-프레미스 네트워크의 전송 지점으로 구성할 수 있습니다. 이 경우 원격 게이트웨이를 사용하는 가상 네트워크는 고유한 게이트웨이를 사용할 수 없습니다. 가상 네트워크는 하나의 게이트웨이만을 가질 수 있습니다. 게이트웨이는 다음 그림에서 보여준 대로 피어링된 가상 네트워크의 로컬 게이트웨이 또는 원격 게이트웨이일 수 있습니다.

![가상 네트워크 피어링 전송](./media/virtual-networks-peering-overview/figure04.png)

게이트웨이 전송은 다른 지역에서 만든 가상 네트워크 간의 피어링 관계에서 지원되지 않습니다. 게이트웨이 전송이 작동하려면 피어링 관계의 두 가상 네트워크가 동일한 지역에 있어야 합니다. 다양한 배포 모델(Resource Manager 및 클래식)을 통해 만든 가상 네트워크 간에 게이트웨이 전송은 게이트웨이가 가상 네트워크(Resource Manager)에 있는 경우에만 지원됩니다. 전송을 위한 게이트웨이 사용에 관해 자세히 알아보려면 [가상 네트워크 피어링에서 전송을 위한 VPN 게이트웨이 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

단일 Azure ExpressRoute 연결을 공유하는 가상 네트워크가 피어링된 경우 둘 사이의 트래픽은 피어링 관계(즉, Azure 백본 네트워크)를 거치게 됩니다. 로컬 게이트웨이 각각에서 가상 네트워크를 사용하여 온-프레미스 회로에 연결할 수 있습니다. 또는 공유 게이트웨이를 사용하고 온-프레미스 연결에 대한 전송을 구성할 수 있습니다.

## <a name="troubleshoot"></a>문제 해결

가상 네트워크 피어링을 확인하려면 가상 네트워크의 서브넷에서 네트워크 인터페이스의 [유효 경로를 확인](virtual-network-routes-troubleshoot-portal.md)합니다. 가상 네트워크 피어링이 존재하는 경우에는 가상 네트워크 내의 모든 서브넷에는 피어링된 각 가상 네트워크의 각 주소 공간에 대해 다음 홉 형식의 *VNet 피어링*을 사용하는 경로가 있습니다.

Network Watcher의 [연결 확인](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 사용하여 피어링된 가상 네트워크에서 가상 머신에 대한 연결 문제를 해결할 수 있습니다. 연결 확인을 통해 원본 가상 머신의 네트워크 인터페이스에서 대상 가상 머신의 네트워크 인터페이스로 트래픽이 라우팅되는 방식을 확인할 수 있습니다.

## <a name="requirements-and-constraints"></a>요구 사항 및 제약 조건

요구 사항 및 제약 조건에 대한 자세한 내용은 [가상 네트워크 피어링 요구 사항 및 제약 조건](virtual-network-manage-peering.md#requirements-and-constraints)을 참조하세요. 가상 네트워크에 만들 수 있는 피어링 수 제한에 대한 자세한 내용은 [Azure 네트워킹 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요. 

## <a name="permissions"></a>권한

가상 네트워크 피어링을 만드는 데 필요한 사용 권한에 대한 자세한 내용은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.

## <a name="pricing"></a>가격

가상 네트워크 피어링 연결을 활용하는 수신 및 송신 트래픽에 대한 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-network)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크 피어링은 동일하거나 다른 구독에 존재하는 동일하거나 다른 배포 모델을 통해 만든 가상 네트워크 간에 만들어집니다. 다음 시나리오 중 하나에 대한 자습서를 완료합니다.

    |Azure 배포 모델             | 구독  |
    |---------                          |---------|
    |둘 다 Resource Manager              |[동일](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[다름](create-peering-different-subscriptions.md)|
    |하나는 Resource Manager, 다른 하나는 클래식  |[동일](create-peering-different-deployment-models.md)|
    |                                   |[다름](create-peering-different-deployment-models-subscriptions.md)|

* [허브 및 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)를 만드는 방법을 알아봅니다.
* 모든 [가상 네트워크 피어링 설정 및 변경 방법](virtual-network-manage-peering.md)에 대해 자세히 알아봅니다.
