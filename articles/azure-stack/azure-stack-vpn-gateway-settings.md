---
title: Azure 스택에 대 한 VPN 게이트웨이 설정을 | Microsoft Docs
description: Azure 스택 함께 사용 하면 VPN 게이트웨이에 대 한 설정에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2018
ms.author: brenduns
ms.openlocfilehash: d23f5b91e08c169975ac5d0bb8d9f048828c2910
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Azure 스택에 대 한 VPN 게이트웨이 구성 설정

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

VPN 게이트웨이 스택에서 Azure 가상 네트워크와 원격 VPN 게이트웨이 간의 암호화 된 트래픽을 전송 하는 가상 네트워크 게이트웨이 유형입니다. Azure 데이터 센터에서 장치 또는 다른 사이트의 장치에서에서 원격 VPN 게이트웨이 될 수 있습니다.  두 끝점 간의 네트워크 연결이 없는 경우에 두 네트워크 간에 보안 사이트 및 사이트 간 (S2S) VPN 연결을 설정할 수 있습니다.

VPN Gateway 연결은 각각이 구성 가능한 설정을 포함하는 여러 리소스의 구성에 따라 좌우됩니다. 이 문서의 섹션에서는 Resource Manager 배포 모델에서 생성된 가상 네트워크의 VPN Gateway와 관련된 리소스 및 설정에 대해 설명합니다. 각 연결 솔루션에 대 한 설명 및 토폴로지 다이어그램을 찾을 수 있습니다 [Azure 스택에 대 한 코드에 대 한 VPN 게이트웨이](azure-stack-vpn-gateway-about-vpn-gateways.md)합니다.

## <a name="vpn-gateway-settings"></a>VPN 게이트웨이 설정

### <a name="gateway-types"></a>게이트웨이 유형
각 Azure 스택 가상 네트워크는 단일 가상 네트워크 게이트웨이 형식 이어야 합니다를 원하는 **Vpn**합니다.  이 지원을 추가 형식을 지 원하는 Azure에서 다릅니다.  

가상 네트워크 게이트웨이를 만들 때 게이트웨이 유형이 구성에 정확한지 확인해야 합니다. VPN 게이트웨이 필요는 `-GatewayType Vpn`합니다.

예:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>게이트웨이 SKU
가상 네트워크 게이트웨이를 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 작업 부하, 처리량, 기능 및 SLA의 종류를 기반으로 하는 요구 사항을 충족하는 SKU를 선택합니다.

Azure 스택 다음 VPN 게이트웨이 Sku를 제공합니다.

|   | VPN 게이트웨이 처리량 |VPN 게이트웨이 최대 IPsec 터널 |
|-------|-------|-------|
|**기본 SKU**  | 100Mbps  | 10    |
|**표준 SKU**           | 100Mbps  | 10    |
|**고성능 SKU** | 200Mbps    | 5 |

### <a name="resizing-gateway-skus"></a>게이트웨이 Sku 크기 조정
Azure 스택 지원 되는 레거시 Sku 간에 Sku의 크기 조정을 지원 하지 않습니다.

마찬가지로, Azure 스택 (VpnGw1, VpnGw2, 및 VpnGw3) Azure에서 지 원하는 최신 Sku에 지원 되는 레거시 Sku (Basic, Standard 및 HighPerformance)에서 크기 조정을 지원 하지 않습니다.

### <a name="configure-the-gateway-sku"></a>게이트웨이 SKU 구성
#### <a name="azure-stack-portal"></a>Azure 스택 포털
Azure 스택 포털을 사용 하 여 리소스 관리자 가상 네트워크 게이트웨이를 만드는 경우에 드롭다운을 사용 하 여 게이트웨이 SKU를 선택할 수 있습니다. 표시되는 옵션은 선택한 게이트웨이 형식 및 선택한 VPN 형식에 해당합니다.

#### <a name="powershell"></a>PowerShell
다음 PowerShell 예에서는 VpnGw1으로-GatewaySku를 지정합니다.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>연결 유형
Resource Manager 배포 모델에서 각 구성이 작동하려면 특정 가상 네트워크 게이트웨이 연결 유형이 필요합니다. -ConnectionType에 대 한 사용 가능한 리소스 관리자 PowerShell 값은:

- IPsec

다음 PowerShell 예에서는 IPsec 연결 형식이 필요한 S2S 연결 생성 됩니다.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN 유형
VPN Gateway 구성에 대한 가상 네트워크 게이트웨이 만들 때 VPN 유형을 지정해야 합니다. 선택하는 VPN 유형은 만들려는 연결 토폴로지에 따라 달라집니다.  또한 VPN 유형은 사용하는 하드웨어에 따라서도 달라질 수 있습니다. S2S 구성에는 VPN 장치가 필요합니다. 일부 VPN 장치는 특정 VPN 유형을 지원합니다.

> [!IMPORTANT]  
> 이때 Azure 스택 경로 기반 VPN 형식만을 지원 합니다. 정책 기반 Vpn 장치 지원, Azure 스택에서 해당 장치에 대 한 연결 지원 되지 않습니다.  또한 Azure 스택 지원 하지 않습니다이 때 경로 기반 게이트웨이 위한 정책 기반 트래픽 선택기를 사용 하 여 사용자 지정 IPSec/IKE 정책 구성이 아직 지원 합니다.

- **PolicyBased**: *(Azure 스택 있지만 Azure에서 지원 됨)* 정책 기반 Vpn 암호화 하 고 패킷을 주소 접두사 조합으로 구성 된 IPsec 정책에 따라 IPsec 터널을 통해 직접 온-프레미스 네트워크와 Azure 스택 VNet입니다. 정책(또는 트래픽 선택기)는 일반적으로 VPN 장치 구성에서 액세스 목록으로 정의됩니다.

- **RouteBased**: RouteBased Vpn IP 전달 또는 해당 터널 인터페이스에 직접 패킷을 라우팅 테이블에서에서 "경로"를 사용 합니다. 그런 다음 터널 인터페이스는 터널로 들어오는 터널에서 나가는 패킷을 암호화하거나 암호 해독합니다. 정책 (또는 트래픽 선택기) RouteBased Vpn에 any-에-any로 구성 된 (또는 와일드 카드) 하 여 기본 및 변경할 수 없습니다. RouteBased VPN 형식에 대 한 값은 RouteBased.

다음 PowerShell 예에서는 RouteBased로-VpnType를 지정합니다. 게이트웨이를 만들 때 -VpnType이 구성에 정확한지 확인해야 합니다.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>게이트웨이 요구 사항
다음 표에서 VPN 게이트웨이에 대 한 요구 사항을 나열합니다.

| |PolicyBased Basic VPN 게이트웨이 | RouteBased Basic VPN 게이트웨이 | RouteBased 표준 VPN 게이트웨이 | RouteBased 높은 성능 VPN 게이트웨이|
|--|--|--|--|--|
| **사이트 간 연결 (S2S 연결)** | 지원되지 않음 | RouteBased VPN 구성 | RouteBased VPN 구성 | RouteBased VPN 구성 |
| **인증 방법**  | 지원되지 않음 | S2S 연결에 대 한 사전 공유 키  | S2S 연결에 대 한 사전 공유 키  | S2S 연결에 대 한 사전 공유 키  |   
| **S2S 연결의 최대 수**  | 지원되지 않음 | 10 | 10| 5|
|**활성 라우팅 지원(BGP)** | 지원되지 않음 | 지원되지 않음 | 지원됨 | 지원됨 |

### <a name="gateway-subnet"></a>게이트웨이 서브넷 
VPN Gateway를 만들기 전에 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은 가상 네트워크 게이트웨이 VM 및 서비스에서 사용하는 IP 주소를 포함합니다. 가상 네트워크 게이트웨이 만들 때 게이트웨이 VM은 게이트웨이 서브넷에 배포되고 필수 VPN Gateway 설정으로 구성됩니다. 다른 작업 (예를 들어 추가 Vm) 게이트웨이 서브넷에 배포 하지 마십시오. 게이트웨이 서브넷이 제대로 작동하려면 이름을 'GatewaySubnet'으로 지정해야 합니다. 게이트웨이 서브넷 이름 지정 'GatewaySubnet을' Azure 가상 네트워크 게이트웨이 Vm 및 서비스를 배포 하는 서브넷을 식별 하는 스택 수 있습니다.

게이트웨이 서브넷을 만드는 경우 서브넷이 포함하는 IP 주소의 수를 지정합니다. 게이트웨이 서브넷의 IP 주소는 게이트웨이 VM 및 게이트웨이 서비스에 할당됩니다. 일부 구성에는 다른 구성보다 더 많은 IP 주소가 필요합니다. 만들려는 구성에 대한 지침을 검토하고 가지고 있는 만들려는 게이트웨이 서브넷이 해당 요구 사항을 충족하는지 확인합니다. 또한 이후 추가 구성이 추가될 가능성에 대비하여 게이트웨이 서브넷에 IP 주소가 충분히 포함되어 있는지 확인하려고 할 수 있습니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 게이트웨이 서브넷을 /28 이상으로 만드는 것이 좋습니다(/28, /27, /26 등). 이런 방식으로 나중에 기능을 추가할 경우 않아도 게이트웨이 종료 한 다음 삭제 하 고 더 많은 IP 주소에 대 한 허용 하려면 게이트웨이 서브넷을 다시 만듭니다.

다음 Resource Manager PowerShell 예제에서는 이름이 GatewaySubnet인 게이트웨이 서브넷을 보여 줍니다. CIDR 표기법이 /27을 지정하는 것을 확인할 수 있으며 이는 이번에 존재하는 대부분의 구성에 대한 충분한 IP 주소를 허용합니다.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> 게이트웨이 서브넷에서 작업할 때는 게이트웨이 서브넷에 NSG(네트워크 보안 그룹)를 연결하지 않습니다. 이 서브넷에 네트워크 보안 그룹을 연결하면 VPN Gateway가 정상적으로 작동하지 않을 수 있습니다. 네트워크 보안 그룹에 대 한 자세한 내용은 참조 [네트워크 보안 그룹 이란?](/azure/virtual-network/virtual-networks-nsg)합니다.

### <a name="local-network-gateways"></a>로컬 네트워크 게이트웨이
Azure에서 VPN 게이트웨이 구성을 만들 때 로컬 네트워크 게이트웨이 종종 온-프레미스 위치를 나타냅니다. Azure 스택에서는 Azure 스택 외부에 있는 모든 원격 VPN 장치를 나타냅니다.  데이터 센터, 원격 데이터 센터 또는 Azure에서 VPN 게이트웨이 VPN 장치 수 있습니다.

VPN 장치의 공용 IP 주소에 이름을 로컬 네트워크 게이트웨이 지정 하 고이 정보를 온-프레미스 위치에 있는 주소 접두사를 지정 합니다. Azure는 네트워크 트래픽에 대상 주소 접두사를 보고 로컬 네트워크 게이트웨이에 대해 지정한 구성을 참조하며 그에 따라 패킷을 라우팅합니다.

다음 PowerShell 예제에서는 새 로컬 네트워크 게이트웨이 만듭니다.

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
로컬 네트워크 게이트웨이 설정을 수정해야 하는 경우도 있습니다. 예를 들어 주소 범위를 추가 또는 수정할 경우 또는 VPN 장치의 IP 주소가 변경될 때가 여기에 해당합니다. [PowerShell을 사용하여 로컬 네트워크 게이트웨이 설정 수정](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway)을 참조하세요.

## <a name="ipsecike-parameters"></a>IPsec/IKE 매개 변수
Azure 스택에서 VPN 연결을 설정 하면 양쪽 끝에서 연결을 구성 해야 합니다.  Azure 스택와 같은 스위치 또는 라우터, VPN 게이트웨이로 작동 하는 하드웨어 장치를 VPN 연결을 구성 하는 경우 해당 장치에 대 한 추가 설정을 요청할 수도 있습니다.

를 지 원하는 여러 행사 개시자와 응답자 모두로 Azure와 달리 Azure 스택 제품 하나에 지원 합니다.

###  <a name="ike-phase-1-main-mode-parameters"></a>IKE 1단계(주 모드) 매개 변수
| 자산              | 값|
|-|-|
| IKE 버전           | IKEv2 |
|Diffie-Hellman 그룹   | 그룹 2(1024비트) |
| 인증 방법 | 미리 공유한 키 |
|암호화 및 해싱 알고리즘 | AES256, SHA256 |
|SA 수명(시간)     | 28,800초|

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 2단계(빠른 모드) 매개 변수
| 자산| 값|
|-|-|
|IKE 버전 |IKEv2 |
|암호화 및 해시 알고리즘 (암호화)     | GCMAES256|
|암호화 및 해시 알고리즘 (인증) | GCMAES256|
|SA 수명(시간)  | 27,000초 |
|SA 수명(바이트) | 819,200       |
|PFS(Perfect Forward Secrecy) |PFS2048 |
|작동하지 않는 피어 검색 | 지원됨|  
