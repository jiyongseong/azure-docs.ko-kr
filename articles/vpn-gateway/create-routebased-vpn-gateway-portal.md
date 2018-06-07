---
title: 경로 기반 VPN 게이트웨이 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 빠르게 경로 기반 VPN 게이트웨이 만들기
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 550f655f6eac5a114636978255578eb3753e0d4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기

이 문서는 Azure Portal을 사용하여 경로 기반 Azure VPN Gateway를 빠르게 만드는 데 도움이 됩니다.  VPN 게이트웨이는 온-프레미스 네트워크에 대한 VPN 연결을 만들 때 사용됩니다. VPN 게이트웨이를 사용하여 VNet을 연결할 수도 있습니다. 

이 문서의 단계에서는 VNet, 서브넷, 게이트웨이 서브넷 및 경로 기반 VPN 게이트웨이(가상 네트워크 게이트웨이)를 만듭니다. 게이트웨이 생성이 완료되면 연결을 만들 수 있습니다. 이러한 단계에는 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="vnet"></a>가상 네트워크 만들기

1. 브라우저에서 [Azure 포털](http://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.
2. **리소스 만들기**를 클릭합니다. **마켓플레이스 검색** 필드에 ‘Virtual Network’를 입력합니다. 반환된 목록에서 **Virtual Network**를 찾아서 클릭하여 **Virtual Network** 페이지를 엽니다.
3. Virtual Network 페이지 아래쪽의 **배포 모델 선택** 목록에서 **리소스 관리자**가 선택되었는지 확인한 다음, **만들기**를 클릭합니다. 그러면 **가상 네트워크 만들기** 페이지가 열립니다.
4. **가상 네트워크 만들기** 페이지에서 VNet 설정을 구성합니다. 필드를 채울 때 필드에 입력한 문자가 유효하면 빨간색 느낌표가 녹색 확인 표시가 됩니다. 다음 값을 사용합니다.

  - **이름**: TestVNet1
  - **주소 공간**: 10.1.0.0/16
  - **구독**: 나열된 구독이 사용하려는 구독인지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
  - **리소스 그룹**: TestRG1
  - **위치**: 미국 동부
  - **서브넷**: Frontend
  - **주소 범위**: 10.1.0.0/24

  ![가상 네트워크 만들기 페이지](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "가상 네트워크 만들기 페이지")
5. 값을 입력한 후 대시보드에서 VNet을 찾기 쉽도록 **대시보드에 고정**을 선택하고 **만들기**를 클릭합니다. **만들기**를 클릭하면 VNet의 진행 상황을 반영하는 타일이 대시보드에 표시됩니다. 타일은 VNet이 생성되면서 변경됩니다.

## <a name="gwsubnet"></a>게이트웨이 서브넷 추가

게이트웨이 서브넷은 가상 네트워크 게이트웨이 서비스가 사용하는 예약된 IP 주소를 포함합니다. 게이트웨이 서브넷을 만듭니다.

1. 포털에서 가상 네트워크 게이트웨이를 만들려는 가상 네트워크로 이동합니다.
2. 가상 네트워크 페이지에서 **서브넷**을 클릭하여 **VNet1 - 서브넷** 페이지를 확장합니다.
3. 맨 위에 있는 **+게이트웨이 서브넷**을 클릭하여 **서브넷 추가** 페이지를 엽니다.

  ![게이트웨이 서브넷 추가](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "게이트웨이 서브넷 추가")
4. 서브넷의 **이름**에 필수 값 ‘GatewaySubnet’이 자동으로 채워집니다. 자동으로 채워진 **주소 범위** 값을 다음 값과 일치하도록 조정합니다.

  **주소 범위(CIDR 블록)**: 10.1.255.0/27

  ![게이트웨이 서브넷 추가](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "게이트웨이 서브넷 추가")
5. 게이트웨이 서브넷을 만들려면 페이지 맨 아래에서 **확인**을 클릭합니다.

## <a name="gwvalues"></a>게이트웨이 설정 구성

1. 포털 페이지의 왼쪽에서 **+ 리소스 만들기**를 클릭하고 검색 상자에 '가상 네트워크 게이트웨이'를 입력한 다음, **Enter** 키를 누릅니다. **결과**에서 **가상 네트워크 게이트웨이**를 찾아 클릭합니다.
2. ‘가상 네트워크 게이트웨이’ 페이지의 맨 아래에서 **만들기**를 클릭하여 **가상 네트워크 게이트웨이 만들기** 페이지를 엽니다.
3. **가상 네트워크 게이트웨이 만들기** 페이지에서 가상 네트워크 게이트웨이의 값을 지정합니다.

  - **이름**: Vnet1GW
  - **게이트웨이 유형**: VPN 
  - **VPN 유형**: 경로 기반
  - **SKU**: VpnGw1
  - **위치**: 미국 동부
  - **가상 네트워크**: **가상 네트워크/가상 네트워크 선택**을 클릭하여 **가상 네트워크 선택** 페이지를 엽니다. **VNet1**을 선택합니다.

  ![게이트웨이 설정 구성](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "게이트웨이 설정 구성")

## <a name="pip"></a>공용 IP 주소 만들기

VPN 게이트웨이에는 동적으로 할당된 공용 IP 주소가 있어야 합니다. VPN 게이트웨이에 대한 연결을 만들 때 온-프레미스 장치가 연결하는 IP 주소입니다.

1. 공용 IP 주소를 요청하려면 **첫 번째 IP 구성 게이트웨이 IP 구성 만들기**를 선택합니다.

  ![첫 번째 IP 구성](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "첫 번째 IP 구성")
2. **공용 IP 선택 페이지**에서 **+ 새로 만들기**를 클릭하여 **공용 IP 주소 만들기** 페이지를 엽니다.
3. 다음 값을 사용하여 설정을 구성합니다.

  - **이름**: **VNet1GWIP**
  - **SKU**: **기본**

  ![공용 IP 만들기](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "PIP 만들기")
4. 이 페이지의 맨 아래에서 **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="creategw"></a>VPN 게이트웨이 만들기

1. **가상 네트워크 게이트웨이 만들기** 페이지의 설정을 확인합니다. 필요한 경우, 값을 조정합니다.

  ![VPN 게이트웨이 만들기](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "VPN 게이트웨이 만들기")
2. 페이지의 맨 아래에서 **만들기**를 클릭합니다.

**만들기**를 클릭하면 설정의 유효성이 검사되고 **가상 네트워크 게이트웨이 배포** 타일이 대시보드에 나타납니다. VPN 게이트웨이를 만드는 데 최대 45분이 걸릴 수 있습니다. 완료 상태를 확인하기 위해 포털 페이지를 새로 고쳐야 할 수 있습니다.

## <a name="viewgw"></a>VPN 게이트웨이 보기

1. 게이트웨이가 생성된 후 포털에서 VNet1로 이동합니다. VPN 게이트웨이가 개요 페이지에 연결된 장치로 나타납니다.

  ![연결된 장치](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "연결된 장치")

2. 장치 목록에서 **VNet1GW**를 클릭하여 자세한 정보를 확인합니다.

  ![VPN 게이트웨이 보기](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN 게이트웨이 보기")

## <a name="next-steps"></a>다음 단계

게이트웨이 생성이 완료되면 가상 네트워크와 VNet 간에 연결을 만들 수 있습니다. 또는 가상 네트워크와 온-프레미스 위치 간에 연결을 만듭니다.

> [!div class="nextstepaction"]
> [사이트 간 연결 만들기](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [지점 및 사이트 간 연결 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [다른 VNet에 대한 연결 만들기](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)