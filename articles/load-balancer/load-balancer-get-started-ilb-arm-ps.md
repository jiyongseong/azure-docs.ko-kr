---
title: PowerShell을 사용하여 Azure 내부 부하 분산 장치 만들기 | Microsoft Docs
description: Azure Resource Manager로 Azure PowerShell 모듈을 사용하여 내부 부하 분산 장치를 만드는 방법 알아보기
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 1b599e5b88026c06a6912ede9952497c489b0269
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Azure PowerShell 모듈을 사용하여 내부 부하 분산 장치 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [템플릿](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>구성 시작

이 문서는 Azure Resource Manager로 Azure PowerShell 모듈을 사용하여 내부 부하 분산 장치를 만드는 방법을 설명합니다. 리소스 관리자 배포 모델에서 내부 부하 분산 장치를 만드는 데 필요한 개체는 개별적으로 구성됩니다. 개체가 만들어지고 구성된 후 부하 분산 장치를 만들기 위해 결합됩니다.

부하 분산 장치를 배포하려면 다음 개체를 만들어야 합니다.

* 프런트 엔드 IP 구성 – 들어오는 모든 네트워크 트래픽에 대한 개인 IP 주소.
* 백 엔드 주소 풀: 프런트 엔드 IP 주소의 부하가 분산된 트래픽을 수신하기 위한 네트워크 인터페이스.
* 부하 분산 규칙: 부하 분산 장치의 포트(원본 및 로컬) 구성.
* 프로브 구성: 가상 머신에 대한 상태 프로브.
* 인바운드 NAT 규칙: 가상 머신에 직접 액세스를 위한 포트 규칙.

부하 분산 장치 구성 요소에 대한 자세한 내용은 [부하 분산 장치에 대한 Azure Resource Manager 지원](load-balancer-arm.md)을 참조하세요.

다음 단계에서는 두 개의 가상 머신 간에 부하 분산 장치를 구성하는 방법을 설명합니다.

## <a name="set-up-powershell-to-use-resource-manager"></a>Resource Manager를 사용하도록 PowerShell 설치

Azure PowerShell 모듈의 최신 프로덕션 버전이 있는지 확인합니다. Azure 구독에 액세스하려면 PowerShell을 올바르게 구성해야 합니다.

### <a name="step-1-start-powershell"></a>1단계: PowerShell 시작

Azure Resource Manager용 PowerShell 모듈을 시작합니다.

```powershell
Connect-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>2단계: 구독 보기

사용 가능한 Azure 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

인증을 위한 메시지가 표시되면 자격 증명을 입력합니다.

### <a name="step-3-select-the-subscription-to-use"></a>3단계: 사용할 구독 선택

부하 분산 장치를 배포하는 데 사용할 Azure 구독을 선택합니다.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>4단계: 부하 분산 장치에 대한 리소스 그룹 선택

부하 분산 장치에 대한 새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뜁니다.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 위치는 리소스 그룹의 모든 리소스에 대한 기본 위치로 사용됩니다. 부하 분산 장치 생성과 관련된 모든 명령에 항상 동일한 리소스 그룹을 사용하십시오.

위 예제에서는 "미국 서부" 위치에 **NRP-RG**라는 리소스 그룹을 만들었습니다.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>프런트 엔드 IP 풀에 대한 IP 주소 및 가상 네트워크 만들기

가상 네트워크의 서브넷을 만들어서 **$backendSubnet** 변수에 할당합니다.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

가상 네트워크를 만듭니다.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

가상 네트워크가 생성됩니다. **LB-Subnet-BE** 서브넷이 **NRPVNet** 가상 네트워크에 추가됩니다. 이러한 값은 **$vnet** 변수에 할당됩니다.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>프런트 엔드 IP 풀 및 백 엔드 주소 풀 만들기

들어오는 트래픽에 대한 프런트 엔드 IP 풀을 만들고 부하가 분산된 트래픽을 수신할 백 엔드 주소 풀을 만듭니다.

### <a name="step-1-create-a-front-end-ip-pool"></a>1단계: 프런트 엔드 IP 풀 만들기

서브넷 10.0.2.0/24에 대한 개인 IP 주소 10.0.2.5로 프런트 엔드 IP 풀을 만듭니다. 이 주소는 들어오는 네트워크 트래픽 끝점입니다.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>2단계: 백 엔드 주소 풀 만들기

프런트 엔드 IP 풀에서 들어오는 트래픽을 수신할 백 엔드 주소 풀을 만듭니다.

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>구성 규칙, 프로브 및 부하 분산 장치 만들기

프런트 엔드 IP 풀 및 백 엔드 주소 풀이 만들어진 후 부하 분산 장치 리소스에 대한 규칙을 지정합니다.

### <a name="step-1-create-the-configuration-rules"></a>1단계: 구성 규칙 만들기

이 예제에서는 다음 네 가지 규칙 개체를 만듭니다.

* RDP(원격 데스크톱 프로토콜)에 대한 인바운드 NAT 규칙: 포트 3441의 모든 들어오는 트래픽을 포트 3389로 리디렉션합니다.
* RDP에 대한 두 번째 인바운드 NAT 규칙: 포트 3442의 모든 들어오는 트래픽을 포트 3389로 리디렉션합니다.
* 상태 프로브 규칙: HealthProbe.aspx 경로의 상태를 확인합니다.
* 부하 분산 장치 규칙: 공용 포트 80의 들어오는 모든 트래픽을 백 엔드 주소 풀의 로컬 포트 80으로 부하 분산합니다.

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>2단계: 부하 분산 장치 만들기

부하 분산 장치를 만들고 개체 규칙(RDP, 부하 분산 장치 및 상태 프로브에 대한 인바운드 NAT)을 결합합니다.

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>네트워크 인터페이스 만들기

내부 부하 분산 장치를 만든 후에는 들어오는 부하가 분산된 네트워크 트래픽, NAT 규칙 및 프로브를 수신할 수 있는 NIC(네트워크 인터페이스)를 정의합니다. 각 네트워크 인터페이스는 개별적으로 구성되며 나중에 가상 컴퓨터에 할당됩니다.

### <a name="step-1-create-the-first-network-interface"></a>1단계: 첫 번째 네트워크 인터페이스 만들기

리소스 가상 네트워크 및 서브넷을 가져옵니다. 이 값은 네트워크 인터페이스를 만드는 데 사용됩니다.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

첫 번째 네트워크 인터페이스를 **lb-nic1-be**라는 이름으로 만듭니다. 부하 분산 장치 백 엔드 풀에 인터페이스를 할당합니다. RDP에 대한 첫 번째 NAT 규칙을 다음 NIC와 연결합니다.

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>2단계: 두 번째 네트워크 인터페이스 만들기

두 번째 네트워크 인터페이스를 **lb-nic2-be**라는 이름으로 만듭니다. 두 번째 인터페이스를 첫 번째 인터페이스와 동일한 부하 분산 장치 백 엔드 풀에 할당합니다. 두 번째 NIC를 RDP의 두 번째 NAT 규칙에 연결합니다.

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

구성을 검토합니다.

    $backendnic1

설정은 다음과 같이 표시되어야 합니다.

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>3단계: NIC를 VM에 할당

`Add-AzureRmVMNetworkInterface` 명령을 사용하여 NIC를 가상 머신에 할당합니다.

가상 컴퓨터를 만들고 NIC를 할당하는 단계별 지침은 [PowerShell을 사용하여 Azure VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)를 참조하세요.

## <a name="add-the-network-interface"></a>네트워크 인터페이스 추가

가상 머신을 만든 후 네트워크 인터페이스를 추가합니다.

### <a name="step-1-store-the-load-balancer-resource"></a>1단계: 부하 분산 장치 리소스 저장

부하 분산 장치 리소스를 변수로 저장(아직 수행하지 않은 경우)합니다. **$lb**를 변수 이름으로 사용합니다. 스크립트의 특성 값에 대해 이전 단계에서 만든 부하 분산 장치 리소스의 이름을 사용합니다.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>2단계: 백 엔드 구성 저장

백 엔드 구성을 **$backend** 변수에 저장합니다.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>3단계: 네트워크 인터페이스 저장

네트워크 인터페이스를 다른 변수에 저장합니다. 이 인터페이스는 "1단계, 네트워크 인터페이스 만들기"에서 만들었습니다. **$nic1**을 변수 이름으로 사용합니다. 이전 예제와 동일한 네트워크 인터페이스 이름을 사용합니다.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>4단계: 백 엔드 구성 변경

네트워크 인터페이스에서 백 엔드 구성을 변경합니다.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>5단계: 네트워크 인터페이스 개체 저장

네트워크 인터페이스 개체를 저장합니다.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

인터페이스가 백 엔드 풀에 추가된 후 네트워크 트래픽은 규칙에 따라 부하가 분산됩니다. 이 규칙은 “구성 규칙, 프로브 및 부하 분산 장치 만들기”에서 구성했습니다.

## <a name="update-an-existing-load-balancer"></a>기존 부하 분산 장치 업데이트

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>1단계: 부하 분산 장치 개체를 변수에 할당

`Get-AzureRmLoadBalancer` 명령을 사용하여 부하 분산 장치 개체(이전 예제의)를 **$slb** 변수에 할당합니다.

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>2단계: NAT 규칙 추가

새 인바운드 NAT 규칙을 기존 부하 분산 장치에 추가합니다. 프런트 엔드 풀에는 포트 81을 사용하고 백 엔드 풀에는 포트 8181을 사용합니다.

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>3단계: 구성 저장

`Set-AzureLoadBalancer` 명령을 사용하여 새 구성을 저장합니다.

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>기존 부하 분산 장치 제거

`Remove-AzureRmLoadBalancer` 명령을 사용하여 **NRP-RG** 리소스 그룹에서 **NRP-LB** 부하 분산 장치를 삭제합니다.

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> 삭제 확인 메시지가 나타나지 않도록 하려면 선택적인 **-Force** 스위치를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)
* [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
