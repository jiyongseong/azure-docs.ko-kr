---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6efec75884857d93f2e128104136bf59a1114594
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
다음 테이블에서는 게이트웨이 형식과 게이트웨이 SKU에 의한 예상 총 처리량을 보여 줍니다. 이 표는 리소스 관리자 배포 모델과 클래식 배포 모델에 적용됩니다. 

가격 책정은 게이트웨이 SKU마다 다릅니다. 자세한 내용은 [VPN Gateway 가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway)을 참조하세요.

UltraPerformance 게이트웨이 SKU는 이 테이블에 표시되지 않습니다. UltraPerformance SKU에 대한 내용은 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 설명서를 참조하세요.

|  | **VPN Gateway 처리량(1)** | **VPN Gateway 최대 IPsec 터널(2)** | **ExpressRoute 게이트웨이 처리량** | **VPN Gateway 및 ExpressRoute 공존** |
| --- | --- | --- | --- | --- |
| **기본 SKU(3)(5)(6)** |100Mbps |10 |500Mbps(6) |아니오 |
| **표준 SKU(4)(5)** |100Mbps |10 |1000Mbps |예 |
| **고성능 SKU(4)** |200Mbps |30 |2000Mbps |예 |


(1) VPN 처리량은 동일한 Azure 지역 내의 VNet 간 측정값을 기반으로 하는 대략적인 예상 값입니다. 인터넷을 통해 프레미스간 연결에 대한 처리량을 보장하지 않습니다. 가능한 최대 처리량 측정값입니다.

(2) 터널의 수는 경로 기반 VPN을 참조합니다. 정책 기반 VPN는 하나의 사이트 간 VPN 터널을 지원할 수 있습니다.

(3) BGP는 기본 SKU에 지원되지 않습니다.

(4) 정책 기반 VPN은 이 SKU에 지원되지 않습니다. 기본 SKU에서만 지원됩니다.

(5) 이 SKU에 대한 활성-활성 S2S VPN Gateway 연결은 지원되지 않습니다. 활성-활성은 HighPerformance SKU에서만 지원됩니다.

(6) 기본 SKU는 ExpressRoute와 함께 사용되지 않습니다.
