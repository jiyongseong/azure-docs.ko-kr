---
title: '여러 온-프레미스 정책 기반 VPN 장치에 Azure VPN Gateway 연결: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Azure Resource Manager 및 PowerShell을 사용하여 여러 정책 기반 VPN 장치에 대해 Azure 경로 기반 VPN 게이트웨이를 구성합니다.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: dc2dc660262cec892270f8d6e70691fdd169a5c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>PowerShell을 사용하여 여러 온-프레미스 정책 기반 VPN 장치에 Azure VPN Gateway 연결

이 문서에서는 S2S VPN 연결에서 사용자 지정 IPsec/IKE 정책을 활용하여 여러 온-프레미스 정책 기반 VPN 장치에 연결하도록 Azure 경로 기반 VPN Gateway를 구성하는 방법을 설명합니다.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a>정책 기반 및 경로 기반 VPN Gateway 정보

정책 기반 *대* 경로 기반 VPN 장치는 연결에서 IPsec 트래픽 선택기가 설정되는 방식이 서로 다릅니다.

* **정책 기반** VPN 장치는 두 네트워크의 접두사 조합을 사용하여 트래픽이 IPsec 터널을 통해 암호화/암호 해독되는 방법을 정의합니다. 이는 일반적으로 패킷 필터링을 수행하는 방화벽 장치에 구축됩니다. IPsec 터널 암호화 및 암호 해독이 패킷 필터링 및 처리 엔진에 추가됩니다.
* **경로 기반** VPN 장치는 임의(와일드 카드) 트래픽 선택기를 사용하며, 라우팅/전달 테이블이 서로 다른 IPsec 터널로 트래픽을 전달하도록 합니다. 이는 일반적으로 각 IPsec 터널이 네트워크 인터페이스 또는 VTI(가상 터널 인터페이스)로 모델링되는 라우터 플랫폼에 구축됩니다.

다음 다이어그램은 두 모델을 강조 표시합니다.

### <a name="policy-based-vpn-example"></a>정책 기반 VPN 예제
![정책 기반](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>경로 기반 VPN 예제
![경로 기반](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>정책 기반 VPN에 대한 Azure 지원
현재 Azure에서는 VPN Gateway의 두 가지 모드(경로 기반 VPN Gateway 및 정책 기반 VPN Gateway)를 모두 지원합니다. 이러한 게이트웨이는 서로 다른 플랫폼에 구축되므로 사양이 서로 다릅니다.

|                          | **PolicyBased VPN Gateway** | **RouteBased VPN Gateway**               |
| ---                      | ---                         | ---                                      |
| **Azure Gateway SKU**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **IKE 버전**          | IKEv1                       | IKEv2                                    |
| **최대 S2S 연결** | **1**                       | Basic/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

이제 사용자 지정 IPsec/IKE 정책을 사용하여 "**PolicyBasedTrafficSelectors**" 옵션과 함께 접두사 기반 트래픽 선택기를 사용하여 온-프레미스 정책 기반 VPN 장치에 연결하도록 Azure 경로 기반 VPN Gateway를 구성할 수 있습니다. 이 기능을 사용하면 Azure Virtual Network 및 VPN Gateway에서 여러 온-프레미스 정책 기반 VPN/방화벽 장치에 연결할 수 있으므로 현재 Azure 정책 기반 VPN Gateway에서 단일 연결 제한이 제거됩니다.

> [!IMPORTANT]
> 1. 이 연결을 사용하려면 온-프레미스 정책 기반 VPN 장치가 Azure 경로 기반 VPN Gateway에 연결하도록 **IKEv2**를 지원해야 합니다. VPN 장치 사양을 확인하세요.
> 2. 이 메커니즘을 사용하여 정책 기반 VPN 장치를 통해 연결되는 온-프레미스 네트워크는 Azure Virtual Network에 연결할 수 있습니다. **동일한 Azure VPN Gateway를 통해 다른 온-프레미스 네트워크 또는 가상 네트워크로 전송할 수는 없습니다**.
> 3. 구성 옵션은 사용자 지정 IPsec/IKE 연결 정책의 일부입니다. 정책 기반 트래픽 선택기 옵션을 사용하는 경우 전체 정책(IPsec/IKE 암호화 및 무결성 알고리즘, 키 수준 및 SA 수명)을 지정해야 합니다.

아래 다이어그램에서는 Azure VPN Gateway를 통한 전송 라우팅이 정책 기반 옵션에서 작동하지 않는 이유를 보여 줍니다.

![정책 기반 전송](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

다이어그램에 나와 있는 것처럼 Azure VPN Gateway의 트래픽 선택기는 가상 네트워크에서 각각의 온-프레미스 네트워크 접두사로는 연결되지만 교차 연결 접두사로는 연결되지 않습니다. 예를 들어 온-프레미스 사이트 2, 3 및 4는 각각 VNet1과 통신할 수 있지만 Azure VPN Gateway를 통해 서로 연결할 수는 없습니다. 다이어그램에서는 이 구성을 사용할 경우 Azure VPN Gateway에서 사용할 수 없는 상호 연결 트래픽 선택기를 보여줍니다.

## <a name="configure-policy-based-traffic-selectors-on-a-connection"></a>연결에서 정책 기반 트래픽 선택기 구성

이 문서의 지침은 [S2S 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)에 설명된 동일한 예제에 따라 S2S VPN 연결을 설정합니다. 다음 다이어그램에 이러한 연결이 나와 있습니다.

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

이 연결을 사용하도록 설정하는 워크플로는 다음과 같습니다.
1. 크로스-프레미스 연결에 대한 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기
2. IPsec/IKE 정책 만들기
3. S2S 또는 VNet 간 연결을 만들 때 정책을 적용하고 연결에서 **정책 기반 트래픽 선택기를 사용하도록 설정**
4. 연결이 이미 생성된 경우 기존 연결에 정책을 적용하거나 업데이트할 수 있음

## <a name="enable-policy-based-traffic-selectors-on-a-connection"></a>연결에서 정책 기반 트래픽 선택기를 사용하도록 설정

이 섹션에 대해 [IPsec/IKE 정책 구성 문서의 3부](vpn-gateway-ipsecikepolicy-rm-powershell.md)를 완료했는지 확인하세요. 아래 예제에서는 동일한 매개 변수 및 단계를 사용합니다.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1단계 - 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

#### <a name="1-declare-your-variables--connect-to-your-subscription"></a>1. 변수를 선언하고 구독에 연결
이 연습에서는 먼저 변수를 선언합니다. 생산을 위해 구성하는 경우 값을 사용자의 값으로 바꾸어야 합니다.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```
Resource Manager cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기
아래 예제는 세 개의 서브넷과 VPN Gateway가 있는 가상 네트워크 TestVNet1을 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 'GatewaySubnet'이라고 구체적으로 지정해야 합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>2단계 - IPsec/IKE 정책을 사용하여 S2S VPN 연결 만들기

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE 정책 만들기

> [!IMPORTANT]
> 연결에서 "UsePolicyBasedTrafficSelectors" 옵션을 사용하기 위해 IPsec/IKE 정책을 만들어야 합니다.

아래 예제에서는 다음 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책을 만듭니다.
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, SA 수명 3600초 및 2,048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. 정책 기반 트래픽 선택기 및 IPsec/IKE 정책을 사용하여 S2S VPN 연결 만들기
S2S VPN 연결을 만들고 이전 단계에서 만든 IPsec/IKE 정책을 적용합니다. 연결에서 정책 기반 트래픽 선택기를 사용도록 설정하는 추가 매개 변수 "-UsePolicyBasedTrafficSelectors $True"가 필요합니다.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

단계를 완료하면 S2S VPN 연결에서 정의된 IPsec/IKE 정책을 사용하며 연결에서 정책 기반 트래픽 선택기를 활성화합니다. 동일한 단계를 반복하여 동일한 Azure VPN Gateway에서 추가 온-프레미스 정책 기반 VPN 장치에 연결을 더 추가할 수 있습니다.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>연결에 대한 정책 기반 트래픽 선택기 업데이트
마지막 섹션에서는 기존 S2S VPN 연결에 대한 정책 기반 트래픽 선택기 옵션을 업데이트하는 방법을 보여 줍니다.

### <a name="1-get-the-connection"></a>1. 연결 가져오기
연결 리소스를 가져옵니다.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. 정책 기반 트래픽 선택기 옵션 확인
다음 줄은 정책 기반 트래픽 선택기가 연결에 사용되는지 여부를 표시합니다.

```powershell
$connection6.UsePolicyBasedTrafficSelectors
```

이 줄에서 "**True**"가 반환되면 연결에 정책 기반 트래픽 선택기가 구성되어 있는 것이고, 그렇지 않으면 "**False**"가 반환됩니다.

### <a name="3-update-the-policy-based-traffic-selectors-on-a-connection"></a>3. 연결에서 정책 기반 트래픽 선택기 업데이트
연결 리소스를 가져온 후에는 옵션을 사용하거나 사용하지 않도록 설정할 수 있습니다.

#### <a name="disable-usepolicybasedtrafficselectors"></a>UsePolicyBasedTrafficSelectors 사용 안 함
아래 예제에서는 IPsec/IKE 정책을 변경되지 않은 상태로 두고 정책 기반 트래픽 선택기 옵션을 사용하지 않도록 설정합니다.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

#### <a name="enable-usepolicybasedtrafficselectors"></a>UsePolicyBasedTrafficSelectors 사용
아래 예제에서는 IPsec/IKE 정책을 변경되지 않은 상태로 두고 정책 기반 트래픽 선택기 옵션을 사용하도록 설정합니다.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.

사용자 지정 IPsec/IKE 정책에 대한 자세한 내용은 [S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)을 참조하세요.
