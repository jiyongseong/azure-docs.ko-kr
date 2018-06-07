---
title: IPv6를 사용하는 Azure 인터넷 연결 부하 분산 장치 만들기 - PowerShell | Microsoft Docs
description: PowerShell을 사용하여 리소스 관리자에 대한 IPv6를 포함한 인터넷 연결 부하 분산 장치를 만드는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: ipv6, Azure Load Balancer, 이중 스택, 공용 IP, 기본 ipv6, 모바일, iot
ms.assetid: d4c649e3-84ad-4343-8b6a-0e89f0b9e518
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 1e369307fba815554b7a34fd430b2e259137c5d6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>PowerShell을 사용하여 리소스 관리자에 대한 IPv6를 포함한 인터넷 연결 부하 분산 장치 만들기 시작

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [템플릿](load-balancer-ipv6-internet-template.md)




Azure 부하 분산 장치는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치는 부하 분산 장치 집합에 있는 클라우드 서비스 또는 가상 머신의 정상 서비스 인스턴스 간에 들어오는 트래픽을 배포하여 고가용성을 제공합니다. Azure Load Balancer는 여러 포트, 여러 IP 주소 또는 둘 다에서 이러한 서비스를 제공할 수도 있습니다.

## <a name="example-deployment-scenario"></a>예제 배포 시나리오

다음 다이어그램은 이 문서에서 배포된 부하 분산 솔루션을 보여 줍니다.

![부하 분산 장치 시나리오](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

이 시나리오에서는 다음과 같은 Azure 리소스를 만듭니다.

* IPv4 및 IPv6 공용 IP 주소를 가진 인터넷 연결 부하 분산 장치
* 공용 VIP를 개인 끝점으로 매핑하기 위한 두 개의 부하 분산 규칙
* 두 개의 VM이 들어 있는 가용성 집합
* 2개의 가상 머신(VM)
* 할당된 IPv4 및 IPv6 주소를 사용하는 각 VM에 대한 가상 네트워크 인터페이스

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Azure PowerShell을 사용하여 솔루션 배포

다음 단계에서는 PowerShell과 함께 Azure Resource Manager를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 보여 줍니다. Azure Resource Manager를 사용하면 각 리소스가 개별적으로 생성되고 구성된 다음, 함께 사용되어 리소스를 만듭니다.

부하 분산 장치를 배포하려면 다음 개체를 만들고 구성해야 합니다.

* 프런트 엔드 IP 구성 - 들어오는 네트워크 트래픽에 대한 공용 IP 주소를 포함합니다.
* 백 엔드 주소 풀 - 부하 분산 장치의 네트워크 트래픽을 받는 가상 머신에 대한 NIC(네트워크 인터페이스)를 포함합니다.
* 부하 분산 규칙 - 백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.
* 인바운드 NAT 규칙 - 백 엔드 주소 풀에 있는 특정 가상 컴퓨터에 대한 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.
* 프로브 - 백 엔드 주소 풀의 가상 머신 인스턴스의 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다.

자세한 내용은 [부하 분산 장치에 대한 Azure Resource Manager 지원](load-balancer-arm.md)을 참조하세요.

## <a name="set-up-powershell-to-use-resource-manager"></a>Resource Manager를 사용하도록 PowerShell 설치

PowerShell에 대한 Azure Resource Manager 모듈의 최신 프로덕션 버전이 있는지 확인합니다.

1. Azure에 로그인

    ```powershell
    Connect-AzureRmAccount
    ```

    메시지가 표시되면 자격 증명을 입력합니다.

2. 계정에 대한 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```

3. 사용할 Azure 구독을 선택합니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>프런트 엔드 IP 풀에 대한 공용 IP 주소 및 가상 네트워크 만들기

1. 서브넷을 사용하는 가상 네트워크를 만듭니다.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. 프런트 엔드 IP 주소 풀에 대한 Azure Public IP address (PIP) 리소스를 만듭니다.

    ```powershell
    $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > 부하 분산 장치는 FQDN에 대한 접두사로 공용 IP의 도메인 레이블을 사용합니다. 이 예제에서 FQDN은 *lbnrpipv4.westus.cloudapp.azure.com*과 *lbnrpipv6.westus.cloudapp.azure.com*입니다.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>프런트 엔드 IP 구성 및 백 엔드 주소 풀 만들기

1. 사용자가 만든 공용 IP 주소를 사용하는 프런트 엔드 주소 구성을 만듭니다.

    ```powershell
    $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. 백 엔드 주소 풀을 만듭니다.

    ```powershell
    $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>LB 규칙, NAT 규칙, 프로브 및 부하 분산 장치 만들기

이 예제에서는 다음 항목을 만듭니다.

* 포트 443~포트 4443에서 들어오는 모든 트래픽을 변환하는 NAT 규칙
* 포트 80~포트 80에서 들어오는 모든 트래픽을 백 엔드 풀에 있는 주소로 분산하는 부하 분산 장치 규칙입니다.
* 포트 3389에서 VM에 RDP 연결을 허용하는 부하 분산 장치 규칙
* *HealthProbe.aspx* 라는 페이지 또는 포트 8080에 대한 상태를 확인할 프로브 규칙
* 이러한 개체를 모두 사용하는 부하 분산 장치

1. NAT 규칙을 만듭니다.

    ```powershell
    $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. 상태 프로브를 만듭니다. 프로브를 구성하는 방법은 두 가지가 있습니다.

    HTTP 프로브

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    또는 TCP 프로브

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    이 예제의 경우 TCP 프로브를 사용할 것입니다.

3. 부하 분산 장치를 만듭니다.

    ```powershell
    $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. 이전에 만든 개체를 사용하여 부하 분산 장치를 만듭니다.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>백 엔드 VM에 대한 NIC 만들기

1. NIC를 만들어야 하는 Virtual Network 및 Virtual Network 서브넷을 가져옵니다.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. VM에 대한 IP 구성 및 NIC를 만듭니다.

    ```powershell
    $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>가상 머신을 만들고 새로 만든 NIC를 할당합니다.

VM 만들기에 대한 자세한 내용은 [리소스 관리자 및 Azure PowerShell을 사용하여 Windows Virtual Machine 만들기 및 미리 구성](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. 가용성 집합 및 Storage 계정 만들기

    ```powershell
    New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. 각 VM을 만들고 이전에 만든 NIC를 할당합니다.

    ```powershell
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```

## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
