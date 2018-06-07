---
title: '온-프레미스 네트워크를 Azure Virtual Network에 연결: 사이트 간 VPN: Portal | Microsoft Docs'
description: 공용 인터넷을 통해 온-프레미스 네트워크에서 Azure Virtual Network에 IPsec을 만드는 단계입니다. 이 단계는 포털을 사용하여 크로스-프레미스 사이트 간 VPN Gateway 연결을 만드는 데 도움이 됩니다.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 42aa80013edd7dcd0f78744e03e0f5da64bfeafc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Azure Portal에서 사이트 간 연결 만들기

이 문서에서는 Azure Portal을 사용하여 온-프레미스 네트워크에서 VNet으로 사이트 간 VPN Gateway 연결을 만드는 방법을 보여줍니다. 이 문서의 단계는 Resource Manager 배포 모델에 적용됩니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

사이트 간 VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure Virtual Network에 연결하는 데 사용됩니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 장치를 필요로 합니다. VPN Gateway에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

![사이트 간 VPN Gateway 크로스-프레미스 연결 다이어그램](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 호환되는 VPN 장치 및 이 장치를 구성할 수 있는 사람이 있는지 확인합니다. 호환되는 VPN 장치 및 장치 구성에 대한 자세한 내용은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.
* VPN 장치에 대한 외부 연결 공용 IPv4 주소가 있는지 확인합니다. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
* 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다. 이 구성을 만들 때 Azure가 온-프레미스 위치에 라우팅할 IP 주소 범위 접두사를 지정해야 합니다. 온-프레미스 네트워크의 어떤 서브넷도 사용자가 연결하려는 가상 네트워크 서브넷과 중첩될 수 없습니다. 

### <a name="values"></a>예제 값

이 문서의 예제에서는 다음 값을 사용합니다. 이러한 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다. 특정 게이트웨이 설정에 대한 자세한 내용은 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요.

* **VNet 이름:** TestVNet1
* **주소 공간:** 10.1.0.0/16
* **구독:** 사용할 구독을 선택합니다.
* **리소스 그룹:** TestRG1
* **위치:** 미국 동부
* **서브넷:** 프런트 엔드: 10.1.0.0/24, 백 엔드: 10.1.1.0/24(이 연습의 선택 사항)
* **게이트웨이 서브넷 이름:** GatewaySubnet(포털에서 자동으로 채워짐)
* **게이트웨이 서브넷 주소 범위:** 10.1.255.0/27
* **DNS 서버:** 8.8.8.8 - 선택 사항입니다. DNS 서버의 IP 주소입니다.
* **Virtual Network 게이트웨이 이름:** VNet1GW
* **공용 IP:** VNet1GWIP
* **VPN 유형:** 경로 기반
* **연결 형식:** 사이트 간(IPsec)
* **게이트웨이 유형:** VPN
* **로컬 네트워크 게이트웨이 이름:** Site1
* **연결 이름:** VNet1toSite1
* **공유 키:** 이 예제에서는 abc123을 사용합니다. 그러나 VPN 하드웨어와 호환이 되는 것이면 무엇이든 사용할 수 있습니다. 중요한 점은 값이 연결의 양쪽 모두에 일치합니다.

## <a name="CreatVNet"></a>1. 가상 네트워크 만들기

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="dns"></a>2. DNS 서버 지정

DNS는 사이트 간 연결을 만들지 않아도 됩니다. 하지만 가상 네트워크에 배포된 리소스에 대한 이름을 확인하려는 경우 DNS 서버를 지정해야 합니다. 이 설정을 통해 이 가상 네트워크에 대한 이름을 확인하는 데 사용하려는 DNS 서버를 지정할 수 있습니다. DNS 서버를 만들지 않습니다. 이름 확인에 대한 자세한 내용은 [VM에서 이름 확인 및 역할 인스턴스](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)를 참조하세요.

[!INCLUDE [Specify a dns server - optional](../../includes/vpn-gateway-specify-dns-portal-include.md)]

## <a name="gatewaysubnet"></a>3. 게이트웨이 서브넷 만들기

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [Add a gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="VNetGateway"></a>4. VPN Gateway 만들기

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gateway-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. 로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. Azure가 참조할 수 있는 사이트 이름을 지정한 다음 연결을 만들 온-프레미스 VPN 장치의 IP 주소를 지정합니다. 또한 VPN Gateway를 통해 VPN 장치로 라우팅될 IP 주소 접두사를 지정합니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다. 온-프레미스 네트워크가 변경되거나 VPN 장치에서 공용 IP 주소를 변경해야 하는 경우 나중에 값을 쉽게 업데이트할 수 있습니다.

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="VPNDevice"></a>6. VPN 장치 구성

온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 장치가 필요합니다. 이 단계에서는 VPN 장치를 구성합니다. VPN 장치를 구성할 때 다음이 필요합니다.

- 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.
- 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다. Azure Portal을 사용하여 VPN Gateway의 공용 IP 주소를 찾으려면 **가상 네트워크 게이트웨이**로 이동한 다음 게이트웨이의 이름을 클릭합니다.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="CreateConnection"></a>7. VPN 연결 만들기

가상 네트워크 게이트웨이와 온-프레미스 VPN 장치 사이의 사이트 간 VPN 연결을 만듭니다.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="VerifyConnection"></a>8. VPN 연결 확인

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connectVM"></a>가상 컴퓨터에 연결하려면

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="reset"></a>VPN 게이트웨이를 다시 설정하는 방법

Azure VPN Gateway 재설정은 하나 이상의 사이트 간 VPN 터널에서 크로스-프레미스 VPN 연결이 손실되는 경우에 유용합니다. 이 상황에서 온-프레미스 VPN 장치는 모두 올바르게 작동하지만 Azure VPN 게이트웨이와 IPsec 터널을 설정할 수 없습니다. 자세한 단계는 [VPN 게이트웨이 다시 설정](vpn-gateway-resetgw-classic.md)을 참조하세요.

## <a name="resize"></a>게이트웨이 SKU를 변경하는 방법(게이트웨이 크기 조정)

게이트웨이 SKU를 변경하는 단계는 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

## <a name="addconnect"></a>VPN 게이트웨이에 추가 연결을 추가하는 방법

어떤 주소 공간도 연결 간에 겹치지 않는다면 추가 연결을 추가할 수 있습니다.

1. 추가 연결을 추가하려면 VPN 게이트웨이로 이동한 다음 **연결**을 클릭하여 연결 페이지를 엽니다.
2. 사용자 연결을 추가하려면 **+ 추가**를 클릭합니다. VNet 간(다른 VNet 게이트웨이에 연결하는 경우) 또는 사이트 간을 reflect하려면 연결 유형을 조정합니다.
3. 사이트 간을 이용하여 연결하고 연결하려는 사이트에 대한 로컬 네트워크 게이트웨이를 아직 만들지 않았다면, 새 게이트웨이를 만들 수 있습니다.
4. 사용하려는 공유 키를 지정한 다음 연결을 만들려면 **확인**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

* BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.
* 강제 터널링에 대한 내용은 [강제 터널링 정보](vpn-gateway-forced-tunneling-rm.md)를 참조하세요.
* 항상 사용 가능한 활성/활성 연결에 대한 정보는 [항상 사용 가능한 크로스-프레미스 및 VNet 간 연결](vpn-gateway-highlyavailable.md)을 참조하세요.
* 가상 네트워크에서 리소스에 네트워크 트래픽을 제한하는 방법에 관한 정보는 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 참조하세요.
* Azure에서 트래픽을 Azure, 온-프레미스 및 인터넷 리소스간에 라우팅하는 방법에 대한 정보는 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.
* Azure Resource Manager 템플릿을 사용하여 사이트 간 VPN 연결을 만드는 방법은 [사이트 간 VPN 연결 만들기](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/)를 참조하세요.
* Azure Resource Manager 템플릿을 사용하여 VNet 간 VPN 연결을 만드는 방법은 [HBase 지역 복제 배포](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)를 참조하세요.