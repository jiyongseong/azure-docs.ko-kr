---
title: Always On 가용성 그룹 수신기 구성 - Microsoft Azure | Microsoft 문서
description: 하나 이상의 IP 주소를 갖는 내부 부하 분산 장치를 사용하여 Azure Resource Manager 모델에서 가용성 그룹 수신기를 구성합니다.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/22/2017
ms.author: mikeray
ms.openlocfilehash: 11aecd9b2bc1bc1521a0e27fc3cd06fe7426a26d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>하나 이상의 Always On 가용성 그룹 수신기 구성 - Resource Manager
이 문서에서는 다음을 수행하는 방법을 보여 줍니다.

* PowerShell cmdlet을 사용하여 SQL Server 가용성 그룹에 대한 내부 부하 분산 장치를 만듭니다.
* 둘 이상의 가용성 그룹에 대한 추가 IP 주소를 부하 분산 장치에 추가합니다. 

가용성 그룹 수신기는 데이터베이스 액세스를 위해 클라이언트에서 연결하는 가상 네트워크 이름입니다. Azure 가상 머신에서 부하 분산 장치는 수신기에 대한 IP 주소를 보유합니다. 부하 분산 장치는 프로브 포트에서 수신 대기하는 SQL Server의 인스턴스로 트래픽을 라우팅합니다. 일반적으로 가용성 그룹은 내부 부하 분산 장치를 사용합니다. Azure 내부 부하 분산 장치는 하나 이상의 IP 주소를 호스트할 수 있습니다. 각 IP 주소는 특정 프로브 포트를 사용합니다. 이 문서에서는 PowerShell을 사용하여 부하 분산 장치를 만들거나 SQL Server 가용성 그룹에 대한 기존 부하 분산 장치에 IP 주소를 추가하는 방법을 보여 줍니다. 

내부 부하 분산 장치에 여러 IP 주소를 할당하는 기능은 Azure에 새로 추가되었으며 Resource Manager 모델에서만 사용할 수 있습니다. 이 작업을 완료하려면 Resource Manager 모델의 Azure 가상 머신에 SQL Server 가용성 그룹이 배포되어야 합니다. 두 SQL Server 가상 머신은 동일한 가용성 집합에 속해야 합니다. [Microsoft 템플릿](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)을 사용하여 Azure Resource Manager에서 가용성 그룹을 자동으로 만들 수 있습니다. 이 템플릿은 내부 부하 분산 장치를 포함하는 가용성 그룹을 자동으로 만듭니다. 원하는 경우 [수동으로 Always On 가용성 그룹을 구성](virtual-machines-windows-portal-sql-availability-group-tutorial.md)할 수 있습니다.

이 항목을 수행하려면 가용성 그룹이 이미 구성되어 있어야 합니다.  

관련 항목은 다음과 같습니다.

* [Azure VM의 Always On 가용성 그룹 구성(GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Windows 방화벽 구성
SQL Server 액세스를 허용하도록 Windows 방화벽을 구성합니다. 방화벽 규칙에서 SQL Server 인스턴스 및 수신기 프로브에서 사용하는 포트에 TCP 연결을 허용합니다. 자세한 지침은 [데이터베이스 엔진 액세스를 위해 Windows 방화벽 구성](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1)을 참조하세요. SQL Server 포트 및 프로브 포트에 대한 인바운드 규칙을 만듭니다.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>예제 스크립트: PowerShell을 사용하여 내부 부하 분산 장치 만들기
> [!NOTE]
> [Microsoft 템플릿](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)으로 가용성 그룹을 만든 경우 내부 부하 분산 장치는 이미 만들어져 있습니다. 
> 
> 

다음 PowerShell 스크립트는 내부 부하 분산 장치를 만들고, 부하 분산 규칙을 구성하고, 부하 분산 장치에 대한 IP 주소를 설정합니다. 이 스크립트를 실행하려면 Windows PowerShell ISE를 열고 스크립트 창으로 스크립트를 붙여 넣습니다. `Connect-AzureRmAccount`를 사용하여 PowerShell에 로그인합니다. 여러 Azure 구독이 있는 경우 `Select-AzureRmSubscription ` 사용하여 구독을 설정합니다. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> 예제 스크립트: PowerShell을 사용하여 기존 부하 분산 장치에 IP 주소 추가
둘 이상의 가용성 그룹을 사용하려면 추가 IP 주소를 부하 분산 장치에 추가합니다. 각 IP 주소에는 자체 부하 분산 규칙, 프로브 포트 및 프런트 엔드 포트가 필요합니다.

프런트 엔드 포트는 응용 프로그램이 SQL Server 인스턴스에 연결하는 데 사용하는 포트입니다. 다른 가용성 그룹에 대한 IP 주소는 동일한 프런트 엔드 포트를 사용할 수 있습니다.

> [!NOTE]
> SQL Server 가용성 그룹의 경우 각 IP 주소에 특정 프로브 포트가 필요합니다. 예를 들어 부하 분산 장치에 있는 하나의 IP 주소가 프로브 포트 59999를 사용하는 경우 해당 부하 분산 장치의 다른 IP 주소는 프로브 포트 59999를 사용할 수 없습니다.

* 부하 분산 장치 제한에 대한 자세한 내용은 **네트워킹 제한 - Azure Resource Manager**에서 [부하 분산 장치당 개인 프런트 엔드 IP](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)를 참조하세요.
* 가용성 그룹 제한에 대한 자세한 내용은 [제한 사항(가용성 그룹)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)을 참조하세요.

다음 스크립트는 기존 부하 분산 장치에 새 IP 주소를 추가합니다. ILB는 부하 분산 프런트 엔드 포트에 대해 수신기 포트를 사용합니다. 이 포트는 SQL Server에서 수신 대기 중인 포트일 수 있습니다. SQL Server의 기본 인스턴스의 경우 포트는 1433입니다. 가용성 그룹에 대한 부하 분산 규칙에는 부동 IP(Direct Server Return)가 필요하므로 백 엔드 포트는 프런트 엔드 포트와 동일합니다. 사용자 환경에 맞게 변수를 업데이트합니다. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>수신기 구성

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>SQL Server Management Studio에서 수신기 포트 설정

1. SQL Server Management Studio를 시작하고 주 복제본에 연결합니다.

1. **AlwaysOn 고가용성** | **가용성 그룹** | **가용성 그룹 수신기**로 이동합니다. 

1. 이제 장애 조치(Failover) 클러스터 관리자에서 만든 수신기 이름이 표시됩니다. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

1. **포트** 상자에서 이전에 사용한 $EndpointPort(1433이 기본값임)를 사용하여 가용성 그룹 수신기에 대한 포트 번호를 지정한 다음 **확인**을 클릭합니다.

## <a name="test-the-connection-to-the-listener"></a>수신기에 대한 연결 테스트

연결을 테스트하려면

1. 동일한 가상 네트워크에 있지만 복제본을 소유하지 않는 SQL Server로 RDP합니다. 클러스터의 다른 SQL Server가 될 수 있습니다.

1. **sqlcmd** 유틸리티를 사용하여 연결을 테스트합니다. 예를 들어 다음 스크립트는 Windows 인증을 사용하는 수신기를 통해 주 복제본에 대한 **sqlcmd** 연결을 설정합니다.
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    수신기가 기본 포트(1433) 이외의 포트를 사용하는 경우 연결 문자열에서 포트를 지정합니다. 예를 들어 다음 sqlcmd 명령은 포트 1435에서 수신기에 연결합니다. 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD 연결은 주 복제본을 호스트하는 SQL Server 인스턴스에 자동으로 연결합니다. 

> [!NOTE]
> 지정한 포트가 두 SQL Server의 방화벽에서 열려 있는지 확인합니다. 두 서버 모두 사용하는 TCP 포트에 대한 인바운드 규칙이 필요합니다. 자세한 내용은 [방화벽 규칙 추가 또는 편집](http://technet.microsoft.com/library/cc753558.aspx)을 참조하세요. 
> 
> 

## <a name="guidelines-and-limitations"></a>지침 및 제한 사항
내부 부하 분산 장치를 사용하는 Azure에서는 가용성 그룹 수신기에 다음과 같은 지침이 적용됩니다.

* 내부 부하 분산 장치를 사용할 경우 동일한 가상 네트워크 내에서만 수신기에 액세스합니다.


## <a name="for-more-information"></a>Blob에 대한 자세한 내용은
자세한 내용은 [수동으로 Azure VM의 Always On 가용성 그룹 구성](virtual-machines-windows-portal-sql-availability-group-tutorial.md)을 참조하세요.

## <a name="powershell-cmdlets"></a>PowerShell cmdlet
다음 PowerShell cmdlet을 사용하여 Azure 가상 머신에 대한 내부 부하 분산 장치를 만듭니다.

* [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx)는 부하 분산 장치를 만듭니다. 
* [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx)는 부하 분산 장치에 대한 프런트 엔드 IP 구성을 만듭니다. 
* [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx)는 부하 분산 장치에 대한 규칙 구성을 만듭니다. 
* [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx)는 부하 분산 장치에 대한 백 엔드 주소 풀 구성을 만듭니다. 
* [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx)는 부하 분산 장치에 대한 프로브 구성을 만듭니다.
* [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx)는 Azure 리소스 그룹에서 부하 분산 장치를 제거합니다.
