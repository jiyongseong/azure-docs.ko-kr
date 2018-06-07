---
title: 내부 부하 분산 장치에서 Azure Application Gateway 사용 - PowerShell | Microsoft Docs
description: 이 페이지에서는 Azure Resource Manager용 ILB(내부 부하 분산 장치)를 사용하여 Azure 응용 프로그램 게이트웨이를 만들고, 구성하고, 시작하고, 삭제하기 위한 지침을 제공합니다.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: a558a5f4f1e19be7b15721c8f99dc5a2d7b396bc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Azure 리소스 관리자를 사용하여 ILB(내부 부하 분산 장치)에서 응용 프로그램 게이트웨이 만들기

> [!div class="op_single_selector"]
> * [Azure 클래식 PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager PowerShell](application-gateway-ilb-arm.md)

Azure Application Gateway는 인터넷 연결 VIP 또는 ILB(내부 부하 분산 장치) 끝점이라고 알려진 인터넷에 노출되지 않은 내부 끝점을 사용하여 구성할 수 있습니다. ILB를 사용하여 게이트웨이를 구성하는 것은 인터넷에 노출되지 않은 비즈니스 응용 프로그램의 내부 라인에 대해 유용합니다. 또한 인터넷에 노출되지 않은 보안 경계에 앉아 있는 다중 계층 응용 프로그램 내에 포함된 서비스 및 계층에 유용하지만 여전히 라운드 로빈 부하 분산, 세션 인력 또는 SSL(Secure Sockets Layer) 종료가 필요합니다.

이 문서는 ILB와 응용 프로그램 게이트웨이 구성 단계를 안내합니다.

## <a name="before-you-begin"></a>시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하여 Azure PowerShell cmdlet의 최신 버전을 설치합니다. **다운로드 페이지** 의 [Windows PowerShell](https://azure.microsoft.com/downloads/)섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. Application Gateway에 대한 가상 네트워크 및 서브넷을 만듭니다. 서브넷을 사용 중인 가상 머신 또는 클라우드 배포가 없는지 확인합니다. Application Gateway는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 구성된 서버가 존재하거나 가상 네트워크나 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.

## <a name="what-is-required-to-create-an-application-gateway"></a>응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?

* **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 응용 프로그램 게이트웨이에 대한 다른 서브넷의 가상 네트워크에 속하거나 공용 IP/VIP이어야 합니다.
* **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
* **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
* **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 경우 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 이동되는 백 엔드 서버 풀을 정의합니다. 현재는 *기본* 규칙만 지원 됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.

## <a name="create-an-application-gateway"></a>응용 프로그램 게이트웨이 만들기

Azure Classic 및 Azure Resource Manager 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다.
Resource Manager를 사용하면 응용 프로그램 게이트웨이를 만드는 모든 항목이 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.

다음은 응용 프로그램 게이트웨이를 만드는 데 필요한 단계입니다.

1. Resource Manager에 대한 리소스 그룹 만들기
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기
3. 응용 프로그램 게이트웨이 구성 개체 만들기
4. 응용 프로그램 게이트웨이 리소스 만들기

## <a name="create-a-resource-group-for-resource-manager"></a>Resource Manager에 대한 리소스 그룹 만들기

Azure 리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell](../powershell-azure-resource-manager.md)사용을 참조하세요.

### <a name="step-1"></a>1단계

```powershell
Connect-AzureRmAccount
```

### <a name="step-2"></a>2단계

계정에 대한 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

### <a name="step-3"></a>3단계

사용할 Azure 구독을 선택합니다.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4단계:

새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

이전 예제에서는 "appgw-rg"라는 리소스 그룹과 "미국 서부"라는 위치를 만들었습니다.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 Resource Manager를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

이 단계에서는 주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

### <a name="step-2"></a>2단계

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

이 단계에서는 접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 "appgw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.

### <a name="step-3"></a>3단계

```powershell
$subnet = $vnet.subnets[0]
```

이 단계에서는 다음 단계를 위해 $subnet 변수에 서브넷 개체를 할당합니다.

## <a name="create-an-application-gateway-configuration-object"></a>응용 프로그램 게이트웨이 구성 개체 만들기

### <a name="step-1"></a>1단계

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

이 단계에서는 "gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. Application Gateway는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

### <a name="step-2"></a>2단계

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

이 단계에서는 IP 주소가 "10.1.1.8, 10.1.1.9, 10.1.1.10"인 "pool01"이라는 백 엔드 IP 주소 풀을 구성합니다. 이러한 IP 주소는 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 이전 IP 주소를 바꿉니다.

### <a name="step-3"></a>3단계

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

이 단계에서는 백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 Application Gateway 설정 "poolsetting01"을 구성합니다.

### <a name="step-4"></a>4단계:

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

이 단계에서는 ILB에 대해 "frontendport01"이라는 프런트 엔드 IP 포트를 구성합니다.

### <a name="step-5"></a>5단계

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

이 단계에서는 "fipconfig01"라는 프런트 엔드 IP 구성을 만들고 현재 가상 네트워크 서브넷의 개인 IP와 연결합니다.

### <a name="step-6"></a>6단계

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

이 단계에서는 "listener01"라는 수신기를 만들고 프런트 엔드 IP 구성에 프런트 엔드 포트를 연결합니다.

### <a name="step-7"></a>7단계

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

이 단계에서는 "rule01"라는 부하 분산 장치 라우팅 규칙을 만들고 부하 분산 장치 동작을 구성합니다.

### <a name="step-8"></a>8단계

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

이 단계에서는 Application Gateway의 인스턴스 크기를 구성합니다.

> [!NOTE]
> *InstanceCount* 의 기본값은 2이고, 최대값은 10입니다. *GatewaySize* 에 대한 기본값은 보통입니다. Standard_Small, Standard_Medium 및 Standard_Large 간에 선택할 수 있습니다.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>New-AzureApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

이전 단계의 모든 구성 항목으로 Application Gateway를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

이 단계에서는 이전 단계의 모든 구성 항목으로 Application Gateway를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.

## <a name="delete-an-application-gateway"></a>응용 프로그램 게이트웨이 삭제

Application Gateway를 삭제하려면 다음 단계를 순서대로 수행해야 합니다.

1. `Stop-AzureRmApplicationGateway` cmdlet을 사용하여 게이트웨이를 중지합니다.
2. `Remove-AzureRmApplicationGateway` cmdlet을 사용하여 게이트웨이를 제거합니다.
3. `Get-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이가 제거되었는지 확인합니다.

### <a name="step-1"></a>1단계

응용 프로그램 게이트웨이 개체를 가져오고 "$getgw" 변수에 연결합니다.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2단계

`Stop-AzureRmApplicationGateway`를 사용하여 응용 프로그램 게이트웨이를 중지합니다. 이 샘플의 첫째 줄에는 `Stop-AzureRmApplicationGateway` cmdlet이 표시되고 그 다음에 출력이 표시됩니다.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

응용 프로그램 게이트웨이가 중지됨 상태가 되면 `Remove-AzureRmApplicationGateway` cmdlet을 사용하여 서비스를 제거합니다.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> **-force** 스위치를 사용하여 제거 확인 메시지가 표시되지 않도록 할 수 있습니다.

서비스가 제거되었는지 확인하려면 `Get-AzureRmApplicationGateway` cmdlet을 사용합니다. 이 단계는 필요 하지 않습니다.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>다음 단계

SSL 오프로드를 구성하려는 경우 [SSL 오프로드에 대해 응용 프로그램 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

ILB에서 사용되도록 응용 프로그램 게이트웨이를 구성하려면 [ILB(내부 부하 분산 장치)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

* [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

