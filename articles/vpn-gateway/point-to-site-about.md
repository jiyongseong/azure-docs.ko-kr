---
title: "Azure 지점 및 사이트 간 VPN 연결 정보 | Microsoft Docs"
description: "이 문서에서는 지점 및 사이트 간 연결을 이해하고 P2S VPN 게이트웨이 인증 유형을 결정합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2018
ms.author: cherylmc
ms.openlocfilehash: 708027b6cea8ac6a2fe7f713f5c6639fc6f8258a
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="about-point-to-site-vpn"></a>지점 및 사이트 간 VPN 연결 정보

P2S(지점 및 사이트 간) VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 솔루션은 집 또는 회의실과 같은 원격 위치에서 Azure VNet에 연결하려는 재택 근무자에게 유용합니다. 또한 P2S VPN은 VNet에 연결해야 하는 클라이언트가 몇 개만 있는 경우 S2S VPN 대신 사용할 수 있는 유용한 솔루션입니다. 이 문서는 Resource Manager 배포 모델에 적용됩니다.

## <a name="protocol"></a>P2S는 어떤 프로토콜을 사용합니까?

지점 및 사이트 간 VPN에서는 다음 프로토콜 중 하나를 사용할 수 있습니다.

* SSTP(Secure Socket Tunneling Protocol) - 독점적인 SSL 기반 VPN 프로토콜입니다. 대부분의 방화벽에서 SSL이 사용되는 443 TCP 포트를 열기 때문에 SSL VPN 솔루션이 방화벽을 통과할 수 있습니다. SSTP는 Windows 장치에서만 지원됩니다. Azure는 SSTP가 설치된 모든 Windows 버전(Windows 7 이상)을 지원합니다.

* IKEv2 VPN - 표준 기반 IPsec VPN 솔루션입니다. IKEv2 VPN은 Mac 장치(OSX 버전 10.11 이상)에서 연결하는 데 사용할 수 있습니다.

Windows 및 Mac 장치로 구성된 혼합 클라이언트 환경인 경우 SSTP 및 IKEv2를 모두 구성합니다.

>[!NOTE]
>P2S용 IKEv2는 Resource Manager 배포 모델에서만 사용할 수 있습니다. 클래식 배포 모델에서는 사용할 수 없습니다.
>

## <a name="authentication"></a>P2S VPN 클라이언트는 어떻게 인증됩니까?

Azure에서 P2S VPN 연결을 허용하기 전에 먼저 사용자를 인증해야 합니다. 연결하는 사용자를 인증하기 위해 Azure에서 제공하는 두 가지 메커니즘이 있습니다.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Azure 기본 인증서 인증을 사용하여 인증

Azure 기본 인증서 인증을 사용하는 경우 장치에 있는 클라이언트 인증서가 연결하는 사용자를 인증하는 데 사용됩니다. 클라이언트 인증서는 신뢰할 수 있는 루트 인증서에서 생성되어 각 클라이언트 컴퓨터에 설치됩니다. 엔터프라이즈 솔루션을 사용하여 생성된 루트 인증서를 사용하거나 자체 서명된 인증서를 생성할 수 있습니다.

클라이언트 인증서의 유효성 검사는 VPN 게이트웨이에서 수행되며, P2S VPN 연결을 설정하는 동안 발생합니다. 루트 인증서는 유효성 검사에 필요하며 Azure에 업로드해야 합니다. 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Azure AD(Active Directory) 도메인 서버를 사용하여 인증

AD 도메인 인증을 사용하면 사용자가 자신의 조직 도메인 자격 증명을 사용하여 Azure에 연결할 수 있습니다. AD 서버와 통합되는 RADIUS 서버가 필요합니다. 또한 조직에서 기존 RADIUS 배포를 활용할 수도 있습니다.   
  RADIUS 서버를 온-프레미스 또는 Azure VNET에 배포할 수 있습니다. 인증하는 동안 Azure VPN Gateway에서 통과 역할을 수행하여 RADIUS 서버와 연결된 장치 간에 인증 메시지를 전달합니다. 따라서 RADIUS 서버에 대한 게이트웨이 연결 가능성이 중요합니다. RADIUS 서버가 온-프레미스에 있는 경우 연결 가능성을 위해 Azure에서 온-프레미스 사이트로의 VPN S2S 연결이 필요합니다.  
  RADIUS 서버는 AD 인증서 서비스와 통합할 수도 있습니다. 이렇게 하면 Azure 인증서 인증 대신 P2S 인증서 인증용 RADIUS 서버 및 엔터프라이즈 인증서 배포를 사용할 수 있습니다. 장점은 루트 인증서와 해지된 인증서를 Azure에 업로드할 필요가 없다는 것입니다.

또한 RADIUS 서버는 다른 외부 ID 시스템과 통합할 수도 있습니다. 이렇게 하면 다단계 옵션을 포함하여 P2S VPN에 대한 많은 인증 옵션이 제공됩니다.

![지점 및 사이트 간 연결](./media/지점 및 사이트 간-about/p2s.png "지점 및 사이트 간")

### <a name="configuration-requirements-for-client-devices"></a>클라이언트 장치에 대한 구성 요구 사항

사용자는 P2S용 Windows 및 Mac 장치에서 VPN 기본 클라이언트를 사용합니다. Azure는 이러한 기본 클라이언트에서 Azure에 연결하는 데 필요한 설정을 포함하고 있는 VPN 클라이언트 구성 zip 파일을 제공합니다.

* Windows 장치의 경우 VPN 클라이언트 구성은 사용자가 자신의 장치에 설치하는 설치 관리자 패키지로 구성됩니다.
* Mac 장치의 경우 사용자가 장치에 설치하는 mobileconfig 파일로 구성됩니다.

또한 zip 파일에서 중요한 Azure 쪽 설정 일부에 대한 값을 제공하므로 이러한 장치에 대한 사용자 고유의 프로필을 만들 수 있습니다. 일부 값에는 VPN 게이트웨이 주소, 구성된 터널 종류, 경로 및 게이트웨이 유효성 검사용 루트 인증서가 포함됩니다.

>[!NOTE]
>Windows 클라이언트의 경우 클라이언트 장치에서 Azure로 VPN 연결을 시작하려면 클라이언트 장치에 대한 관리자 권한이 있어야 합니다.
>

### <a name="gwsku"></a>어떤 게이트웨이 SKU에서 P2S VPN을 지원하나요?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* 집계 처리량 벤치마크는 단일 게이트웨이를 통해 집계된 여러 터널의 측정값을 기반으로 합니다. 인터넷 트래픽 조건 및 응용 프로그램 동작으로 인해 처리량이 보장되지 않습니다.
* 가격 정보는 [가격 책정] 페이지에 있습니다. 
* Service Level Agreement(서비스 수준 계약) 정보는 SLA 페이지에 있습니다.

>[!NOTE]
>기본 SKU는 IKEv2 또는 RADIUS 인증을 지원하지 않습니다.
>

## <a name="configure"></a>P2S 연결은 어떻게 구성하나요?

P2S 구성에는 몇 가지 특정한 단계가 필요합니다. 다음 문서에는 P2S 구성을 수행하는 단계와 VPN 클라이언트 장치를 구성하기 위한 링크가 포함되어 있습니다.

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="faqcert"></a>네이티브 Azure 인증서 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>RADIUS 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>다음 단계

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)
