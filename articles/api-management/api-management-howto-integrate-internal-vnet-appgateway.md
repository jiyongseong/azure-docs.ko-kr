---
title: Virtual Network에서 Application Gateway와 함께 Azure API Management를 사용하는 방법 | Microsoft Docs
description: 내부 Virtual Network에서 프론트 엔드로 Application Gateway(WAF)와 함께 Azure API Management를 설정하고 구성하는 방법 알아보기
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: 595abcaafdea5cde3f868567bac7fb9cf0ee424b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936108"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>내부 VNET에서 Application Gateway와 API Management 통합 

##<a name="overview"> </a> 개요
 
Virtual Network 내에서만 액세스할 수 있도록 내부 모드의 Virtual Network에서 API Management 서비스를 구성할 수 있습니다. Azure Application Gateway는 계층 7 부하 분산 장치를 제공하는 PAAS 서비스입니다. 역방향 프록시 서비스 역할을 하고 제품에 WAF(웹 응용 프로그램 방화벽)를 제공합니다.

내부 VNET에서 프로비전된 API Management와 Application Gateway 프런트 엔드를 결합하면 다음 시나리오가 가능합니다.

* 내부 소비자 및 외부 소비자의 소비에 대해 동일한 API Management 리소스를 사용합니다.
* 단일 API Management 리소스를 사용하며 외부 소비자가 사용할 수 있는 API Management에서 정의된 API의 하위 집합을 갖습니다.
* 공용 인터넷에서 API Management에 대한 액세스를 켜기 및 끄기로 전환하는 턴키 방법을 제공합니다. 

## <a name="prerequisites"></a>필수 조건

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

+ 활성 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

##<a name="scenario"> </a> 시나리오
이 문서에서는 내부 및 외부 소비자가 단일 API Management 서비스를 사용하여 온-프레미스 및 클라우드 API에서 단일 프런트 엔드 역할을 하도록 만드는 방법을 다룹니다. Application Gateway에서 사용 가능한 PathBasedRouting 기능을 사용하여 외부 소비에 대해 API(예제에서 녹색으로 강조 표시됨)의 하위 집합만을 노출하는 방법을 확인할 수도 있습니다.

첫 번째 설정 예제에서 모든 API는 Virtual Network 내에서만 관리됩니다. 내부 소비자(주황색으로 강조 표시됨)는 모든 내부 및 외부 API에 액세스할 수 있습니다. 트래픽은 ExpressRoute 회로를 통해 고성능을 제공하는 인터넷으로 가지 않습니다.

![url 경로](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. **다운로드 페이지** 의 [Windows PowerShell](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. Virtual Network를 만들고 API Management 및 Application Gateway에 대한 별도 서브넷을 만듭니다. 
3. Virtual Network에 대한 사용자 지정 DNS 서버를 만들려면 배포를 시작하기 전에 수행합니다. Virtual Network의 새 서브넷에서 만든 가상 머신이 모든 Azure 서비스 끝점을 확인하고 액세스할 수 있도록 하여 작동을 이중으로 확인합니다.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>API Management 및 Application Gateway 간에 통합을 만드는 데 무엇이 필요한가요?

* **백 엔드 서버 풀:** API Management 서비스의 내부 가상 IP 주소입니다.
* **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀 내의 모든 서버에 적용됩니다.
* **프런트 엔드 포트:** Application Gateway에 열려 있는 공용 포트입니다. 이 포트에 도달한 트래픽은 백 엔드 서버 중의 하나로 리디렉션됩니다.
* **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙:** 규칙은 수신기를 백 엔드 서버 풀에 바인딩합니다.
* **사용자 정의 상태 프로브:** 기본적으로 Application Gateway는 IP 주소 기반 프로브를 사용하여 BackendAddressPool의 어떤 서버가 활성 상태인지 파악합니다. API Management 서비스는 올바른 호스트 헤더가 있는 요청에만 응답하므로 기본 프로브는 실패합니다. 서비스가 활성 상태이고 요청을 전달해야 한다는 것을 Application Gateway가 결정할 수 있도록 사용자 지정 상태 프로브를 정의해야 합니다.
* **사용자 지정 도메인 인증서:** 인터넷에서 API Management에 액세스하려면 Application Gateway 프런트 엔드 DNS 이름에 대한 해당 호스트 이름의 CNAME을 매핑해야 합니다. 이렇게 하면 API Management에 전달되는 Application Gateway에 전송되는 호스트 이름 헤더 및 인증서를 APIM에서 유효한 것으로 인식할 수 있습니다.

## <a name="overview-steps"> </a> API Management 및 Application Gateway 통합에 필요한 단계 

1. Resource Manager에 대한 리소스 그룹을 만듭니다.
2. Application Gateway에 대한 Virtual Network, 서브넷 및 공용 IP를 만듭니다. API Management에 대한 다른 서브넷을 만듭니다.
3. 위에서 만든 VNET 서브넷 내에서 API Management 서비스를 만들고 내부 모드로 사용해야 합니다.
4. API Management 서비스에서 사용자 지정 도메인 이름을 설정합니다.
5. Application Gateway 구성 개체를 만듭니다.
6. Application Gateway 리소스를 만듭니다.
7. CNAME을 Application Gateway 리소스의 공용 DNS 이름에서 API Management 프록시 호스트 이름으로 만듭니다.

## <a name="create-a-resource-group-for-resource-manager"></a>Resource Manager에 대한 리소스 그룹 만들기

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager)을 참조하세요.

### <a name="step-1"></a>1단계

Azure에 로그인

```powershell
Connect-AzureRmAccount
```

자격 증명을 사용하여 인증합니다.<BR>

### <a name="step-2"></a>2단계

계정에 대한 구독을 확인하고 선택합니다.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>3단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Application Gateway에 대한 Virtual Network 및 서브넷 만들기

다음 예제에서는 Resource Manager를 사용하여 Virtual Network를 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계

주소 범위 10.0.0.0/24를 Virtual Network를 만드는 동안 Application Gateway에 사용할 서브넷 변수에 할당합니다.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2단계

Virtual Network를 만드는 동안 주소 범위 10.0.1.0/24를 API Management에 사용할 서브넷 변수에 할당합니다.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3단계

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24 및 10.0.1.0/24를 사용하여 미국 서부 지역에 리소스 그룹 **apim-appGw-RG**에서 **appgwvnet**이라는 Virtual Network를 만듭니다.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4단계:

다음 단계에 대한 서브넷 변수 할당

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>내부 모드로 구성된 VNET 내에서 API Management 서비스 만들기

다음 예제에서는 VNET에서 내부 액세스 전용으로 구성된 API Management 서비스를 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계
위에서 만든 $apimsubnetdata 서브넷을 사용하여 API Management Virtual Network 개체를 만듭니다.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>2단계
Virtual Network 내부에 API Management 서비스를 만듭니다.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
위의 명령이 성공한 후에 액세스하려면 [내부 VNET API Management 서비스에 액세스하는 데 필요한 DNS 구성](api-management-using-with-internal-vnet.md#apim-dns-configuration)을 참조하세요.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>API Management에서 사용자 지정 도메인 이름 설정

### <a name="step-1"></a>1단계
도메인에 개인 키로 인증서를 업로드합니다. 이 예에서는 `*.contoso.net`입니다. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>2단계
인증서가 업로드되면 예제 인증서가 도메인 `*.contoso.net`에 대한 권한을 제공하는 대로 호스트 이름 `api.contoso.net`을 사용하여 프록시에 대한 호스트 이름 구성 개체를 만듭니다. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

미국 서부 지역에 리소스 그룹 **apim-appGw-RG**에서 공용 IP 리소스 **publicIP01**을 만듭니다.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

서비스를 시작할 때 응용 프로그램 게이트웨이에 IP 주소가 할당됩니다.

## <a name="create-application-gateway-configuration"></a>응용 프로그램 게이트웨이 구성 만들기

응용 프로그램 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### <a name="step-1"></a>1단계

**gatewayIP01**이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. Application Gateway는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2단계

공용 IP 끝점에 대한 프런트 엔드 IP 포트를 구성합니다. 이 포트는 최종 사용자가 연결하는 포트입니다.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>3단계

공용 IP 끝점으로 프런트 엔드 IP를 구성합니다.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4단계:

Application Gateway의 인증서가 전달되는 트래픽을 암호화하고 해독하는 데 사용되도록 구성합니다.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>5단계

Application Gateway에 대한 HTTP 수신기를 만듭니다. 여기에 프런트 엔드 IP 구성, 포트 및 SSL 인증서를 할당합니다.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>6단계

API Management 서비스 `ContosoApi` 프록시 도메인 끝점에 사용자 지정 프로브를 만듭니다. 경로 `/status-0123456789abcdef`는 모든 API Management 서비스에서 호스트되는 기본 상태 끝점입니다. `api.contoso.net`을 사용자 지정 프로브 호스트 이름으로 지정하여 SSL 인증서로 보호합니다.

> [!NOTE]
> 호스트 이름 `contosoapi.azure-api.net`은 서비스 `contosoapi`가 공용 Azure에서 생성될 때 구성된 기본 프록시 호스트 이름입니다. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7단계

SSL이 활성화된 백 엔드 풀 리소스에 사용할 인증서를 업로드합니다. 위의 4단계에서 제공하는 동일한 인증서입니다.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>8단계

Application Gateway에 HTTP 백 엔드 설정을 구성합니다. 해당 설정이 취소된 후에 백 엔드 요청에 대한 제한 시간을 설정합니다. 이 값은 프로브 시간 초과와 다릅니다.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9단계

위에서 만든 API Management 서비스의 내부 가상 IP 주소로 **apimbackend**라는 백 엔드 IP 주소 풀을 구성합니다.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>10단계

더미(존재하지 않는) 백 엔드에 대한 설정을 만듭니다. Application Gateway를 통한 API Management에서 노출하고 싶지 않은 API 경로에 대한 요청이 이 백 엔드에 도달하여 404를 반환합니다.

더미 백 엔드에 대한 HTTP 설정을 구성합니다.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

FQDN 주소 **dummybackend.com**을 가리키는 더미 백 엔드 **dummyBackendPool**을 구성합니다. 이 FQDN 주소는 가상 네트워크에 존재하지 않습니다.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Application Gateway가 기본적으로 사용하고 Virtual Network에서 존재하지 않는 백 엔드 **dummybackend.com**을 가리키는 규칙 설정을 만듭니다.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>11단계

백 엔드 풀에 대한 URL 규칙 경로를 구성합니다. 이를 통해 공개적으로 노출되는 API Management에서 일부 API만을 선택합니다. 예를 들어 `Echo API` (/echo/), `Calculator API` (/calc/) 등이 있으면 인터넷에서 `Echo API`에만 액세스할 수 있도록 합니다. 

다음 예제에서는 백 엔드 "apimProxyBackendPool"에 트래픽을 라우팅하는 "/echo/" 경로에 대한 간단한 규칙을 만듭니다.

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

경로가 API Management에서 사용하려는 경로 규칙과 일치하지 않으면 규칙 경로 맵 구성이 **dummyBackendPool**이라는 기본 백 엔드 주소 풀도 구성합니다. 예를 들어 http://api.contoso.net/calc/sum은 일치하지 않는 트래픽의 기본 풀로 정의되어 있으므로 **dummyBackendPool**로 이동합니다.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

위의 단계를 수행하면 Application Gateway를 통한 경로 "/echo"에 대한 요청만이 허용됩니다. API Management에서 구성된 다른 API에 대한 요청은 인터넷에서 액세스될 때 Application Gateway에서 404 오류를 throw합니다. 

### <a name="step-12"></a>12단계

URL 경로 기반 라우팅을 사용하도록 Application Gateway의 규칙 설정을 만듭니다.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>13단계

Application Gateway의 크기 및 인스턴스 수를 구성합니다. 여기에서 [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md)를 사용하여 API Management 리소스의 보안을 강화합니다.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>14단계

WAF를 "방지" 모드로 구성합니다.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Application Gateway 만들기

이전 단계의 모든 구성 개체를 사용하여 Application Gateway를 만듭니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>API Management 프록시 호스트 이름을 Application Gateway 리소스의 공용 DNS 이름으로 CNAME합니다.

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 쉽게 사용할 수 없는 동적으로 할당된 DNS 이름이 필요합니다. 

Application Gateway의 DNS 이름은 APIM 프록시 호스트 이름(예: 위의 예제에서 `api.contoso.net`)을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용되어야 합니다. 프런트 엔드 IP CNAME 레코드를 구성하려면 PublicIPAddress 요소를 사용하여 Application Gateway 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. A 레코드를 사용할 경우 게이트웨이를 다시 시작할 때 VIP가 변경될 수 있으므로 권장되지 않습니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> 요약
VNET에서 구성된 Azure API Management는 온-프레미스 또는 클라우드에서 호스트되었는지 여부에 상관 없이 구성된 모든 API에 대한 단일 게이트웨이 인터페이스를 제공합니다. Application Gateway와 API Management의 통합을 통해 특정 API를 인터넷에 액세스할 수 있도록 선택적으로 유연성을 향상시키고 API Management 인스턴스에 대한 프런트 엔드로 웹 응용 프로그램 방화벽을 제공합니다.

##<a name="next-steps"> </a> 다음 단계
* Azure Application Gateway에 대한 자세한 정보
  * [Application Gateway 개요](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway 웹 응용 프로그램 방화벽](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [경로 기반 라우팅을 사용하는 Application Gateway](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* API Management 및 VNET에 대한 자세한 정보
  * [VNET 내에서만 사용할 수 있는 API Management 사용](api-management-using-with-internal-vnet.md)
  * [VNET에서 API Management 사용](api-management-using-with-vnet.md)
