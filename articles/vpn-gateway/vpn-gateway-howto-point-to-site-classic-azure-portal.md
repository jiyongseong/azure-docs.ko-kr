---
title: "지점 및 사이트 간 연결과 인증서 인증을 사용하여 가상 네트워크에 컴퓨터 연결: Azure Portal 클래식 | Microsoft Docs"
description: "Azure Portal을 사용하여 클래식 지점 및 사이트 간 VPN 게이트웨이 연결을 만듭니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: fe460113441933d655b183e87cceefee4dd24d24
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-classic-azure-portal"></a>인증서 인증(클래식)을 사용하여 VNet에 지점 및 사이트 간 연결 구성: Azure Portal

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

이 문서에서는 클래식 배포 모델에서 Azure Portal을 사용하여 지점 및 사이트 간 연결로 VNet을 만드는 방법을 보여줍니다. 이 구성은 자체 서명된 CA 또는 발급된 CA 중 하나인 인증서를 사용하여 연결 중인 클라이언트를 인증합니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure 포털](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

지점 및 사이트 간(P2S) VPN Gateway를 통해 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. 지점 및 사이트 간 VPN 연결은 집 또는 회의에서 원격 통신하는 경우와 같이 원격 위치에서 VNet에 연결하려는 경우에 유용합니다. VNet에 연결해야 하는 몇 가지 클라이언트만 있는 경우에 사이트 간 VPN 대신 P2S VPN을 사용하는 것도 유용한 솔루션입니다. 클라이언트 컴퓨터에서 시작하여 P2S VPN 연결을 설정합니다.

> [!IMPORTANT]
> 클래식 배포 모델은 Windows VPN 클라이언트만을 지원하고 SSTP(Secure Socket Tunneling Protocol), SSL 기반 VPN 프로토콜을 사용합니다. 비-Windows VPN 클라이언트를 지원하기 위해 VNet은 리소스 관리자 배포 모델을 사용하여 생성되어야 합니다. 리소스 관리자 배포 모델은 SSTP 외에도 IKEv2 VPN을 지원합니다. 자세한 내용은 [P2S 연결 정보](point-to-site-about.md)를 참조하세요.
>
>

![지점 및 사이트 간 다이어그램](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)


지점 및 사이트 간 인증서 인증 연결을 사용하려면 다음 항목이 필요합니다.

* 동적 VPN 게이트웨이
* Azure에 업로드된 루트 인증서에 대한 공개 키(.cer 파일)입니다. 신뢰할 수 있는 인증서로 간주되며 인증에 사용됩니다.
* 루트 인증서에서 생성되고 연결할 각 클라이언트 컴퓨터에 설치된 클라이언트 인증서 - 클라이언트 인증에 사용됩니다.
* 연결하는 모든 클라이언트 컴퓨터에 VPN 클라이언트 구성 패키지를 생성하여 설치해야 합니다. 클라이언트 구성 패키지는 VNet에 연결하는 데 필요한 정보와 함께 운영 체제에 이미 있는 기본 VPN 클라이언트를 구성합니다.

지점 및 사이트 간 연결에는 VPN 장치 또는 온-프레미스 공용 IP 주소가 필요하지 않습니다. VPN 연결은 SSTP(Secure Socket Tunneling Protocol)를 통해 만듭니다. 서버 쪽에서 SSTP 버전 1.0, 1.1 및 1.2를 지원하며, 클라이언트에서 사용할 버전을 결정합니다. Windows 8.1 이상에서는 기본적으로 SSTP 버전 1.2를 사용합니다. 

지점 및 사이트 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [지점 및 사이트 간 FAQ](#faq)를 참조하세요.

### <a name="example-settings"></a>예제 설정

다음 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

* **이름: VNet1**
* **주소 공간: 192.168.0.0/16**<br>이 예제에서는 하나의 주소 공간만 사용합니다. 다이어그램과 같이 VNet에는 둘 이상의 주소 공간이 있을 수 있습니다.
* **서브넷 이름: FrontEnd**
* **서브넷 주소 범위: 192.168.1.0/24**
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹: TestRG**
* **위치: 미국 동부**
* **연결 형식: 지점 및 사이트 간**
* **클라이언트 주소 공간: 172.16.201.0/24**. 이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 지정된 풀에서 IP 주소를 받습니다.
* **GatewaySubnet: 192.168.200.0/24**. 게이트웨이 서브넷의 이름으로 "GatewaySubnet"을 사용해야 합니다.
* **크기:** 사용할 게이트웨이 SKU를 선택합니다.
* **라우팅 유형: 동적**

## <a name="vnetvpn"></a>1. 가상 네트워크 및 VPN Gateway 만들기

시작하기 전에 Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

### <a name="createvnet"></a>1부: 가상 네트워크 만들기

가상 네트워크가 아직 없는 경우 만듭니다. 스크린샷은 예제로 제공됩니다. 사용자 고유의 값으로 대체해야 합니다. Azure 포털을 사용하여 VNet을 만들려면 다음 단계를 사용하세요.

1. 브라우저에서 [Azure Portal](http://portal.azure.com)로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. **리소스 만들기** > **네트워킹** > **가상 머신**을 클릭합니다. 
3. Virtual Network 페이지 아래쪽 근처의 **배포 모델 선택** 목록에서 **클래식**을 선택한 다음 **만들기**를 클릭합니다.

  ![배포 모델 선택](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. **가상 네트워크 만들기** 페이지에서 VNet 설정을 구성합니다. 이 페이지에서 첫 번째 주소 공간과 단일 서브넷 주소 범위를 추가합니다. VNet 만들기를 완료한 후에 다시 돌아와서 추가 서브넷 및 주소 공간을 추가합니다.

  ![가상 네트워크 만들기 페이지](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. **구독**이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
6. **리소스 그룹**을 클릭하고 기존 리소스 그룹을 선택하거나 새 리소스 그룹에 대한 이름을 입력하여 새로 만듭니다. 새 리소스 그룹을 만드는 경우 계획된 구성 값에 따라 리소스 그룹의 이름을 지정합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
7. 다음으로 VNet에 대한 **위치** 설정을 선택합니다. 이 위치는 VNet에 배포하는 리소스가 상주할 곳을 결정합니다.
8. 대시보드에서 VNet을 쉽게 찾을 수 있으려면 **대시보드에 고정**을 선택한 다음 **만들기**를 클릭합니다.

  ![대시보드에 고정](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. [만들기]를 클릭하면 VNet의 진행 상황을 반영하는 타일이 대시보드에 표시됩니다. 타일은 VNet이 생성되면서 변경됩니다.

  ![가상 네트워크 만들기 타일](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. 가상 네트워크가 만들어지면 **만들어짐**이 표시됩니다.
11. DNS 서버를 추가합니다(선택 사항). 가상 네트워크를 만든 후에 이름 확인을 위해 DNS 서버의 IP 주소를 추가할 수 있습니다. 지정한 DNS 서버 IP 주소는 VNet에서 리소스의 이름을 확인할 수 있는 DNS 서버의 주소여야 합니다.<br>DNS 서버를 추가하려면 가상 네트워크에 대한 설정을 열고 DNS 서버를 클릭하고 사용하려는 DNS 서버의 IP 주소를 추가합니다.

### <a name="gateway"></a>2부: 게이트웨이 서브넷 및 동적 라우팅 게이트웨이 만들기

이 단계에서는 게이트웨이 서브넷 및 동적 라우팅 게이트웨이를 만듭니다. 클래식 배포 모델을 위한 Azure Portal에서 게이트웨이 서브넷 및 게이트웨이 만들기는 동일한 구성 페이지를 통해 수행할 수 있습니다. 게이트웨이 서브넷은 게이트웨이 서비스에만 사용됩니다. 게이트웨이 서브넷(예: VM 또는 다른 서비스)에 직접 배포하지 마세요.

1. 포털에서 게이트웨이를 만들려는 가상 네트워크로 이동합니다.
2. 가상 네트워크에 대한 페이지에 대해, **개요** 페이지의 VPN 연결 섹션에서 **게이트웨이**를 클릭합니다.

  ![클릭하여 게이트웨이 서브넷 만들기](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. **새 VPN 연결** 페이지에서 **지점 및 사이트 간**을 선택합니다.

  ![지점 및 사이트 간 연결 형식](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. **클라이언트 주소 공간**에 대해 IP 주소 범위를 추가합니다. 연결할 때 VPN 클라이언트에서 IP 주소를 받는 범위입니다. 연결 원본이 되는 온-프레미스 위치 또는 연결 대상이 되는 VNet과 겹치지 않는 개인 IP 주소 범위를 사용합니다. 자동으로 채워진 범위를 삭제한 다음 사용하려는 개인 IP 주소 범위를 추가할 수 있습니다. 이 예에서는 자동으로 채워진 주소 범위를 보여 줍니다. 이 주소 범위를 삭제하여 원하는 값을 추가합니다.

  ![클라이언트 주소 공간](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. **게이트웨이 즉시 만들기** 확인란을 선택합니다. **선택적 게이트웨이 구성**을 클릭하여 **게이트웨이 구성** 페이지를 엽니다.

  ![선택적 게이트웨이 구성 클릭](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
6. **서브넷 필수 설정 구성**을 클릭하여 **게이트웨이 서브넷**을 클릭합니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 적어도 /28 또는 /27을 선택하여 더 많은 주소를 포함하는 큰 서브넷을 만드는 것이 좋습니다. 이렇게 하면 나중에 필요할 수도 있는 추가 구성에 맞게 충분히 주소를 사용할 수 있습니다. 게이트웨이 서브넷에서 작업할 때는 게이트웨이 서브넷에 NSG(네트워크 보안 그룹)를 연결하지 않습니다. 이 서브넷에 네트워크 보안 그룹을 연결하면 VPN Gateway가 정상적으로 작동하지 않을 수 있습니다.

  ![GatewaySubnet 추가](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. 게이트웨이 **크기**를 선택합니다. 크기는 가상 네트워크 게이트웨이에 대한 게이트웨이 SKU입니다. 포털에서 SKU 기본값은 **기본**입니다. 게이트웨이 SKU에 대한 자세한 내용은 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

  ![게이트웨이 크기](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. 게이트웨이에 대한 **라우팅 유형**을 선택합니다. P2S 구성에는 **동적** 라우팅 유형이 필요합니다. 이 페이지 구성을 완료했으면 **확인**을 클릭합니다.

  ![라우팅 유형 구성](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
9. **새 VPN 연결** 페이지 하단에 **확인**을 클릭하여 가상 네트워크 게이트웨이 만들기를 시작합니다. VPN 게이트웨이는 선택한 게이트웨이 SKU에 따라 완료하는 데 최대 45분이 걸릴 수 있습니다.

## <a name="generatecerts"></a>2. 인증서 만들기

인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하기 위해 Azure에 의해 사용됩니다. Azure에 루트 인증서의 공개 키 정보를 업로드합니다. 그러면 해당 공개 키가 '신뢰할 수 있는 키'로 간주됩니다. 클라이언트 인증서는 신뢰할 수 있는 루트 인증서에서 생성한 다음 각 클라이언트 컴퓨터의 [인증서 - 현재 사용자/개인] 인증서 저장소에 설치해야 합니다. 인증서는 VNet에 대한 연결을 시작할 때 해당 클라이언트를 인증하는 데 사용됩니다. 

자체 서명된 인증서를 사용하는 경우 특정 매개 변수를 사용하여 만들어야 합니다. [PowerShell 및 Windows 10](vpn-gateway-certificates-point-to-site.md) 또는 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)에 대한 지침을 사용하여 자체 서명된 인증서를 만들 수 있습니다. 자체 서명된 루트 인증서로 작동하고 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성할 때 이들 지침에 있는 단계를 따르는 것이 중요합니다. 그렇지 않으면 만든 인증서는 P2S 연결과 호환되지 않으며 연결 오류가 발생하게 됩니다.

### <a name="cer"></a>1부: 루트 인증서용 공개 키(.cer) 가져오기

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>2부: 클라이언트 인증서 생성

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3. 루트 인증서 .cer 파일 업로드

게이트웨이를 만든 후에 신뢰할 수 있는 루트 인증서의 .cer 파일(공개 키 정보 포함)을 Azure로 업로드할 수 있습니다. 루트 인증서에 대한 개인 키를 Azure에 업로드하지 않습니다. a.cer 파일이 업로드되면 Azure는 이.cer 파일을 사용하여 신뢰할 수 있는 루트 인증서에서 생성된 클라이언트 인증서를 설치한 클라이언트를 인증합니다. 필요한 경우 나중에 신뢰할 수 있는 루트 인증서 파일을 최대 20개까지 추가로 업로드할 수 있습니다.  

1. VNet에 대한 페이지의 **VPN 연결** 섹션에서 **클라이언트** 그래픽을 클릭하여 **지점 및 사이트 간 VPN 연결** 페이지를 엽니다.

  ![클라이언트](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **지점 및 사이트 간 연결** 페이지에서 **인증서 관리**를 클릭하여 **인증서** 페이지를 엽니다.<br>

  ![인증서 페이지](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **인증서** 페이지에서 **업로드**를 클릭하여 **인증서 업로드** 페이지를 엽니다.<br>

    ![인증서 업로드 페이지](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. 폴더 그래픽을 클릭하여 .cer 파일을 찾아봅니다. 파일을 선택한 후 **확인**을 클릭합니다. 페이지를 새로 고쳐 **인증서** 페이지에서 업로드된 인증서를 확인합니다.

  ![인증서 업로드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. 클라이언트 구성

지점 및 사이트 간 VPN을 사용하여 VNet에 연결하려면 각 클라이언트에서 기본 Windows VPN 클라이언트를 구성하는 패키지를 설치해야 합니다. 구성 패키지는 가상 네트워크에 연결하는 데 필요한 설정을 사용하여 네이티브 Windows VPN 클라이언트를 구성합니다.

버전이 클라이언트의 아키텍처와 일치하는 한 각 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 이 문서 끝의 [지점 및 사이트 간 연결 FAQ](#faq)를 참조하세요.

### <a name="generateconfigpackage"></a>1부: VPN 클라이언트 구성 패키지 생성 및 설치

1. Azure Portal에서 VNet에 대한 **개요** 페이지의 **VPN 연결**에서 클라이언트 그래픽을 클릭하여 **지점 및 사이트 간 VPN 연결** 페이지를 엽니다.
2. **지점 및 사이트 간 VPN 연결** 페이지 맨 위에서 설치할 클라이언트 운영 체제에 해당하는 다운로드 패키지를 선택합니다.

  * 64비트 클라이언트인 경우 **VPN 클라이언트(64비트)**를 선택합니다.
  * 32비트 클라이언트인 경우 **VPN 클라이언트(32비트)**를 선택합니다.

  ![VPN 클라이언트 구성 패키지 다운로드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. 패키지가 생성되면 다운로드하여 클라이언트 컴퓨터에 설치합니다. SmartScreen 팝업이 표시되면 **자세한 정보**, **실행**을 차례로 클릭합니다. 다른 클라이언트 컴퓨터에 설치하기 위해 패키지를 저장할 수도 있습니다.

### <a name="installclientcert"></a>2부: 클라이언트 인증서 설치

클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치하는 경우 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다. 일반적으로 이 인증서를 두 번 클릭하고 설치하기만 하면 됩니다. 자세한 내용은 [내보낸 클라이언트 인증서 설치](vpn-gateway-certificates-point-to-site.md#install)를 참조하세요.

## <a name="connect"></a>5. Azure에 연결

### <a name="connect-to-your-vnet"></a>VNet에 연결

>[!NOTE]
>연결하는 클라이언트 컴퓨터에서 관리자 권한이 있어야 합니다.
>
>

1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. 이 경우 **계속** 을 클릭하여 상승된 권한을 사용합니다.
2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.

  ![VPN 클라이언트 연결](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. 연결이 설정되었습니다.

  ![설정된 연결](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>P2S 연결 문제 해결

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="verifyvpnconnect"></a>VPN 연결 확인

1. 클라이언트 컴퓨터에서 VPN 연결이 활성화되어 있는지 확인하려면, 관리자 권한으로 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 VNet을 만들 때 지정한 지점 및 사이트 간 연결 주소 범위 내의 주소 중 하나인지 확인합니다. 결과는 다음 예제와 비슷합니다.

  ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>가상 컴퓨터에 연결

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>신뢰할 수 있는 루트 인증서 추가 또는 제거

Azure에서 신뢰할 수 있는 루트 인증서를 추가 및 제거할 수 있습니다. 루트 인증서를 제거하면 해당 루트에서 생성된 인증서가 있는 클라이언트를 인증하지 못하게 됩니다. 따라서 연결할 수도 없습니다. 클라이언트를 인증하고 연결하려는 경우 Azure에 (업로드된)신뢰할 수 있는 루트 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

### <a name="addtrustedroot"></a>신뢰할 수 있는 루트 인증서를 추가하려면

Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 지침은 [섹션 3 - 루트 인증서 .cer 파일 업로드](#upload)를 참조하세요.

### <a name="removetrustedroot"></a>신뢰할 수 있는 루트 인증서를 제거하려면

1. VNet에 대한 페이지의 **VPN 연결** 섹션에서 **클라이언트** 그래픽을 클릭하여 **지점 및 사이트 간 VPN 연결** 페이지를 엽니다.

  ![클라이언트](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **지점 및 사이트 간 연결** 페이지에서 **인증서 관리**를 클릭하여 **인증서** 페이지를 엽니다.<br>

  ![인증서 페이지](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **인증서** 페이지에서 제거할 인증서 옆에 있는 줄임표를 클릭한 다음 **삭제**를 클릭합니다.

  ![루트 인증서 삭제](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>클라이언트 인증서 해지

클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. 이것은 신뢰할 수 있는 루트 인증서를 제거하는 것과 다릅니다. Azure에서 신뢰할 수 있는 루트 인증서 .cer를 제거하면, 해지된 루트 인증서로 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트 인증서가 아닌 클라이언트 인증서를 해지해야 루트 인증서로부터 생성된 다른 인증서를 지점 및 사이트 간 연결을 위한 인증에 계속 사용할 수 있습니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

### <a name="revokeclientcert"></a>클라이언트 인증서를 해지하려면

해지 목록에 지문을 추가하여 클라이언트 인증서를 해지할 수 있습니다.

1. 클라이언트 인증서 지문을 검색합니다. 자세한 내용은 [방법: 인증서의 지문 검색](https://msdn.microsoft.com/library/ms734695.aspx)을 참조하세요.
2. 텍스트 편집기에 정보를 복사하고 연속 문자열이 되도록 공백을 모두 제거합니다.
3. **[클래식 가상 네트워크 이름] > [지점 및 사이트 간 VPN 연결] > [인증서]** 페이지로 이동한 다음 **해지 목록**을 클릭하여 해지 목록 페이지를 엽니다. 
4. **해지 목록** 페이지에서 **+인증서 추가**를 클릭하여 **해지 목록에 인증서 추가** 페이지를 엽니다.
5. **해지 목록에 인증서 추가** 페이지에서 인증서 지문을 공백 없이 연속적인 텍스트 줄로 붙여넣습니다. 페이지 아래쪽에서 **확인**을 클릭합니다.
6. 업데이트가 완료된 후에는 인증서를 더 이상 연결에 사용할 수 없습니다. 이 인증서를 사용하여 연결하려는 클라이언트에서 인증서가 더 이상 유효하지 않다고 하는 메시지를 받습니다.

## <a name="faq"></a>지점 및 사이트 간 FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요. 네트워킹 및 가상 머신에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/linux/azure-vm-network-overview.md)를 참조하세요.

P2S 문제 해결 정보는 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.