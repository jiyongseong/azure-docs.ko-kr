---
title: "Azure VPN Gateway에서의 BGP 개요| Microsoft Azure"
description: "이 문서는 Azure VPN Gateway와 BGP에 대한 개요를 제공합니다."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: f8c3985c-c128-4f34-835c-0e88742bf36e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: yushwang
ms.openlocfilehash: 13a17eb3d78e70a09864bf218f1027d6e98486a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Azure VPN Gateway에서의 BGP 개요
이 문서에서는 Azure VPN Gateway에서의 BGP(경계 게이트웨이 프로토콜) 지원에 대한 개요를 제공합니다.

BGP는 두 개 이상의 네트워크 간에 라우팅 및 연결 정보를 교환하도록 인터넷에서 일반적으로 사용하는 표준 라우팅 프로토콜입니다. Azure 가상 네트워크에서는 BGP를 통해 Azure VPN Gateway 및 온-프레미스 VPN 장치(BGP 피어 또는 인접이라고 함)는 관련된 게이트웨이 또는 라우터를 거치도록 해당 접두사의 가용성 및 연결 가능성에 대한 정보를 두 게이트웨이에 제공하는 "경로"를 교환할 수 있습니다. BGP 게이트웨이가 하나의 BGP 피어에서 파악한 경로를 다른 모든 BGP 피어로 전파하여 BGP를 통해 여러 네트워크 간에 전송 라우팅을 사용할 수도 있습니다. 

## <a name="why"></a>BGP를 사용하는 이유
BGP는 선택적 기능으로, Azure 경로 기반 VPN Gateway와 함께 사용할 수 있습니다. 또한 이 기능을 사용하기 전에 온-프레미스 VPN 장치가 BGP를 지원하는지 확인해야 합니다. BGP 없이도 Azure VPN Gateway 및 온-프레미스 VPN 장치를 계속 사용할 수 있습니다. 네트워크와 Azure 간에 BGP를 통한  동적 라우팅을 사용하는 것과 고정 경로(BGP 없음)를 사용하는 것의 *차이* 입니다.

BGP에는 다음과 같이 몇 가지 장점과 새로운 기능이 있습니다.

### <a name="prefix"></a>자동 및 유연한 접두사 업데이트 지원
BGP를 사용하면 IPsec S2S VPN 터널을 경유하는 특정 BGP 피어에 최소한의 접두어만 선언하면 됩니다. 온-프레미스 VPN 장치의 BGP 피어 IP 주소에 있는 호스트 접두어(/32)처럼 짧을 수 있습니다. Azure 가상 네트워크에 액세스를 허용하기 위해 Azure에 제시할 온-프레미스 네트워크 접두어를 제어할 수 있습니다.

또한 대형 비공개 IP 주소 공간(예: 10.0.0.0/8)과 같은 일부 VNet 주소 접두어를 포함하는 더 큰 접두어를 제시할 수도 있습니다. 그렇지만 접두어는 VNet의 어떤 접두어와도 같을 수 없습니다. VNet 접두어와 동일한 경로는 거부됩니다. 

### <a name="multitunnel"></a>BGP를 기준으로 자동 장애 조치(failover)가 있는 VNet과 온-프레미스 사이트 간 여러 터널 지원
동일한 위치에서 온-프레미스 VPN 장치와 Azure VNet 간에 여러 연결을 수립할 수 있습니다. 이 기능은 활성-활성 구성의 두 네트워크 간에 여러 터널(경로)을 제공합니다. 한 터널의 연결이 끊기면 BGP를 통해 해당 경로가 제거되고 트래픽이 자동으로 다른 터널로 전달됩니다.

다음 다이어그램에서는 이러한 고가용성 설정의 간단한 예제를 보여줍니다.

![여러 개의 활성 경로](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>온-프레미스 네트워크와 여러 Azure VNet 간 전송 라우팅 지원
BGP는 직접 또는 간접 연결 여부에 관계없이 서로 다른 네트워크로부터 접두어를 파악하고 전파하기 위해 여러 게이트웨이를 활성화합니다. 따라서 온-프레미스 사이트 간 또는 여러 Azure 가상 네트워크 간의 Azure VPN Gateway를 통한 전송  라우팅이 가능합니다.

다음 다이어그램에서는 Microsoft 네트워크 내에서 Azure VPN Gateway를 통해 두 온-프레미스 네트워크 간에 트래픽을 전송할 수 있는 다중 홉 토폴로지의 예를 보여줍니다.

![다중 홉 전송](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>BGP FAQ
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>다음 단계
프레미스 간 연결 및 VNet 간 연결에 대해 BGP를 구성하는 단계는 [Azure VPN Gateway에서 BGP 시작하기](vpn-gateway-bgp-resource-manager-ps.md) 를 참조하세요.

