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
ms.openlocfilehash: b8a785293c4e555b547c4b002f5ef5906a7633c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
이 FAQ는 클래식 배포 모델을 사용하는 P2S 연결에 적용됩니다.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>지점 및 사이트 간 연결에 사용할 수 있는 클라이언트 운영 체제는 무엇입니까?

다음과 같은 클라이언트 운영 체제가 지원됩니다.

* Windows 7(32비트 및 64비트)
* Windows Server 2008 R2(64비트 전용)
* Windows 8(32비트 및 64비트)
* Windows 8.1(32비트 및 64비트)
* Windows Server 2012(64비트 전용)
* Windows Server 2012 R2(64비트 전용)
* 윈도우 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>SSTP를 지원하는 지점 및 사이트 간 연결에 소프트웨어 VPN 클라이언트를 사용할 수 있습니까?

번호 위에 나열된 Windows 운영 체제 버전만 지원됩니다.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>지점 및 사이트 간 구성에서 VPN 클라이언트 끝점을 몇 개까지 지정할 수 있습니까?

최대 128개의 VPN 클라이언트에서 가상 네트워크에 동시에 연결할 수 있도록 지원합니다.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>지점 및 사이트 간 연결에 내부 PKI 루트 CA를 사용할 수 있습니까?

예. 이전에는 자체 서명한 루트 인증서만 사용할 수 있었습니다. 여전히 20개의 루트 인증서를 업로드할 수 있습니다.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>지점 및 사이트 간 기능을 사용하여 프록시와 방화벽을 트래버스할 수 있습니까?

예. SSTP(보안 소켓 터널링 프로토콜)을 사용하여 방화벽을 통해 터널링합니다. 이 터널은 HTTP 연결로 표시됩니다.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>지점 및 사이트 간 연결에 대해 구성된 클라이언트 컴퓨터를 다시 시작하면 VPN이 자동으로 다시 연결됩니까?

기본적으로 클라이언트 컴퓨터에서는 VPN 연결을 자동으로 다시 설정하지 않습니다.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>지점 및 사이트 간 연결은 VPN 클라이언트에서 자동 다시 연결 및 DDNS를 지원합니까?

자동 다시 연결과 DDNS는 현재 지점 및 사이트 간 VPN에서 지원되지 않습니다.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>동일한 가상 네트워크에 대해 사이트 간 구성과 지점 및 사이트 간 구성을 함께 사용할 수 있습니까?

예. 게이트웨이에 경로 기반 VPN 유형이 있는 경우 이 솔루션이 모두 작동합니다. 클래식 배포 모델의 경우 동적 게이트웨이가 필요합니다. 고정 라우팅 VPN 게이트웨이 또는 `-VpnType PolicyBased` cmdlet을 사용하는 게이트웨이의 지점 및 사이트 간 연결은 지원하지 않습니다.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>지점 및 사이트 간 클라이언트를 여러 가상 네트워크에 동시에 연결하도록 구성할 수 있습니까?

예, 구성할 수 있습니다. 하지만 가상 네트워크에서는 겹치는 IP 접두사를 사용할 수 없으며, 지점 및 사이트 간 주소 공간은 가상 네트워크 간에 겹쳐질 수 없습니다.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>사이트 간 연결 또는 지점 및 사이트 간 연결을 통해 어느 정도의 처리량을 제공할 수 있습니까?

VPN 터널의 정확한 처리량을 유지하는 것은 어렵습니다. IPsec과 SSTP는 암호화 중심 VPN 프로토콜입니다. 또한 처리량은 프레미스와 인터넷 간의 대기 시간과 대역폭에 의해 제한됩니다.
