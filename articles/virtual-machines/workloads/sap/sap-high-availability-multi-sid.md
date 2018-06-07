---
title: "Azure에서 SAP 다중 SID 구성 만들기 | Microsoft Docs"
description: "Windows 가상 컴퓨터의 고가용성 SAP NetWeaver 다중 SID 구성 가이드"
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b48df78df9f53ac7bf0804f55a8d36a2fe2f86b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>SAP NetWeaver 다중 SID 구성 만들기

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

2016년 9월에 Microsoft는 Azure 내부 부하 분산 장치를 사용하여 여러 가상 IP 주소를 관리할 수 있는 기능을 릴리스했습니다. 이 기능은 Azure 외부 부하 분산 장치에 이미 있습니다.

SAP 배포가 있는 경우 [Windows VM에서 고가용성 SAP NetWeaver 가이드][sap-ha-guide]에 설명된 대로 내부 부하 분산 장치를 사용하여 SAP ASCS/SCS에 대한 Windows 클러스터 구성을 만들 수 있습니다.

이 문서에서는 추가 SAP ASCS/SCS 클러스터링된 인스턴스를 기존 WSFC(Windows Server 장애 조치 클러스터링) 클러스터에 설치하여 단일 ASCS/SCS 설치에서 SAP 다중 SID 구성으로 이동하는 방법을 중점적으로 설명합니다. 이 프로세스가 완료되면 SAP 다중 SID 클러스터가 구성됩니다.


## <a name="prerequisites"></a>필수 조건
[Windows VM에서 고가용성 SAP NetWeaver 가이드][sap-ha-guide]에서 설명되고 이 다이어그램에 표시된 대로 하나의 SAP ASCS/SCS 인스턴스에 사용되는 WSFC 클러스터를 구성했습니다.

![고가용성 SAP ASCS/SCS 인스턴스][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>대상 아키텍처

목적은 여기에서 설명한 것처럼 동일한 WSAFC 클러스터에서 여러 SAP ABAP ASCS 또는 SAP Java SCS 클러스터링된 인스턴스를 설치하는 것입니다.

![Azure에서 여러 SAP ASCS/SCS 클러스터링된 인스턴스][sap-ha-guide-figure-6002]

> [!NOTE]
>각 Azure 내부 부하 분산 장치에 대한 개인 프런트 엔드 IP의 수에 제한이 있습니다.
>
>하나의 WSFC 클러스터에서 SAP ASCS/SCS 인스턴스의 최대수는 각 Azure 내부 부하 분산 장치에 대한 개인 프런트 엔드 IP의 최대수와 같습니다.
>

부하 분산 장치 제한에 대한 자세한 내용은 [네트워킹 제한 - Azure Resource Manager][networking-limits-azure-resource-manager]에서 “부하 분산 장치당 개인 프런트 엔드 IP”를 참조하세요.

두 가지 고가용성 SAP 시스템을 포함한 전체 그림은 다음과 같습니다.

![두 개의 SAP 시스템 SID를 포함한 SAP 고가용성 다중 SID 설치 프로그램][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> 설치 프로그램은 다음 조건을 충족해야 합니다.
> - SAP ASCS/SCS 인스턴스는 동일한 WSFC 클러스터를 공유해야 합니다.
> - 각 DBMS SID에는 해당하는 고유한 전용 WSFC 클러스터가 있어야 합니다.
> - 하나의 SAP 시스템 SID에 속하는 SAP 응용 프로그램 서버에는 고유한 전용 VM이 있어야 합니다.


## <a name="prepare-the-infrastructure"></a>인프라 준비
인프라를 준비하기 위해 다음 매개 변수를 사용하여 추가 SAP ASCS/SCS 인스턴스를 설치할 수 있습니다.

| 매개 변수 이름 | 값 |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| SAP DBMS 내부 부하 분산 장치 | PR5 |
| SAP 가상 호스트 이름 | pr5-sap-cl |
| SAP ASCS/SCS 가상 호스트 IP 주소(추가 Azure Load Balancer IP 주소) | 10.0.0.50 |
| SAP ASCS/SCS 인스턴스 번호 | 50 |
| 추가 SAP ASCS/SCS 인스턴스의 ILB 프로브 포트 | 62350 |

> [!NOTE]
> SAP ASCS/SCS 클러스터 인스턴스의 경우 각 IP 주소에는 고유한 프로브 포트가 필요합니다. 예를 들어 Azure 내부 부하 분산 장치에 있는 하나의 IP 주소가 프로브 포트 62300을 사용하는 경우 해당 부하 분산 장치의 다른 IP 주소는 프로브 포트 62300을 사용할 수 없습니다.
>
>프로브 포트 62300이 이미 예약되어 있으므로 여기서는 프로브 포트 62350을 사용하고 있습니다.

두 개의 노드를 포함한 기존 WSFC 클러스터에서 추가 SAP ASCS/SCS 인스턴스를 설치할 수 있습니다.

| 가상 컴퓨터 역할 | 가상 컴퓨터 호스트 이름 | 고정 IP 주소 |
| --- | --- | --- |
| ASCS/SCS 인스턴스의 첫 번째 클러스터 노드 |pr1-ascs-0 |10.0.0.10 |
| ASCS/SCS 인스턴스의 두 번째 클러스터 노드 |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>DNS 서버에서 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기

다음 매개 변수를 사용하여 ASCS/SCS 인스턴스의 가상 호스트 이름에 대한 DNS 항목을 만들 수 있습니다.

| 새 SAP ASCS/SCS 가상 호스트 이름 | 연결된 IP 주소 |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

새 호스트 이름 및 IP 주소는 다음 스크린샷에 표시된 것처럼 DNS 관리자에 표시됩니다.

![새로운 SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 정의된 DNS 항목을 강조 표시는 DNS 관리자 목록][sap-ha-guide-figure-6004]

DNS 항목을 만드는 절차는 기본 [Windows VM에서 고가용성 SAP NetWeaver 가이드][sap-ha-guide-9.1.1]에서 자세히 설명합니다.

> [!NOTE]
> 추가 ASCS/SCS 인스턴스의 가상 호스트 이름에 할당하는 새 IP 주소는 SAP Azure Load Balancer에 할당한 새 IP 주소와 동일해야 합니다.
>
>이 시나리오에서 IP 주소는 10.0.0.50입니다.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>PowerShell을 사용하여 기존 Azure 내부 부하 분산 장치에 IP 주소 추가

동일한 WSFC 클러스터에서 둘 이상의 SAP ASCS/SCS 인스턴스를 만들려면 PowerShell을 사용하여 IP 주소를 기존 Azure 내부 부하 분산 장치에 추가합니다. 각 IP 주소에는 고유한 부하 분산 규칙, 프로브 포트, 프런트 엔드 IP 풀 및 백 엔드 풀이 필요합니다.

다음 스크립트는 기존 부하 분산 장치에 새 IP 주소를 추가합니다. 사용자 환경에 맞게 PowerShell 변수를 업데이트합니다. 스크립트는 모든 SAP ASCS /SCS 포트에 필요한 모든 부하 분산 규칙을 만듭니다.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Succesfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
스크립트를 실행한 후 다음 스크린샷에 표시된 것처럼 결과가 Azure Portal에 표시됩니다.

![Azure Portal에서 새 프런트 엔드 IP 풀][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>클러스터 컴퓨터에 디스크 추가 및 SIOS 클러스터 공유 디스크 구성

각 추가 SAP ASCS/SCS 인스턴스에 새 클러스터 공유 디스크를 추가해야 합니다. Windows Server 2012 R2의 경우 현재 사용 중인 WSFC 클러스터 공유 디스크는 SIOS DataKeeper 소프트웨어 솔루션입니다.

다음을 수행합니다.
1. 각 클러스터 노드에 추가 디스크 또는 동일한 크기의 디스크(스트라이프해야 하는)를 추가하고 서식을 지정합니다.
2. SIOS DataKeeper를 사용하여 저장소 복제를 구성합니다.

이 절차는 WSFC 클러스터 컴퓨터에 SIOS DataKeeper를 이미 설치했다고 가정합니다. 설치한 경우 이제 컴퓨터 간의 복제를 구성해야 합니다. 프로세스는 기본 [Windows VM에서 고가용성 SAP NetWeaver 가이드][sap-ha-guide-8.12.3.3]에서 자세히 설명합니다.  

![새 SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>SAP 응용 프로그램 서버 및 DBMS 클러스터에 대한 VM 배포

두 번째 SAP 시스템에 대한 인프라 준비를 완료하려면 다음을 수행합니다.

1. SAP 응용 프로그램 서버에 대한 전용 VM을 배포하고 고유한 전용 가용성 그룹에 배치합니다.
2. DBMS 클러스터에 대한 전용 VM을 배포하고 고유한 전용 가용성 그룹에 배치합니다.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>두 번째 SAP SID2 NetWeaver 시스템 설치

두 번째 SAP SID2 시스템을 설치하는 전체 프로세스는 기본 [Windows VM에서 고가용성 SAP NetWeaver 가이드][sap-ha-guide-9]에서 설명합니다.

고급 절차는 다음과 같습니다.

1. [SAP 첫 번째 클러스터 노드 설치][sap-ha-guide-9.1.2].  
 이 단계에서는 **기존 WSFC 클러스터 노드 1**에 고가용성 ASCS/SCS 인스턴스를 포함한 SAP를 설치하고 있습니다.

2. [ASCS/SCS 인스턴스의 SAP 프로필 수정][sap-ha-guide-9.1.3].

3. [프로브 포트 구성][sap-ha-guide-9.1.4].  
 이 단계에서는 PowerShell을 사용하여 SAP 클러스터 리소스 SAP-SID2-IP 프로브 포트를 구성하고 있습니다. SAP ASCS/SCS 클러스터 노드 중 하나에서 이 구성을 실행합니다.

4. [데이터베이스 인스턴스 설치][sap-ha-guide-9.2].  
 이 단계에서는 전용 WSFC 클러스터에 DBMS를 설치하고 있습니다.

5. [두 번째 클러스터 노드 설치][sap-ha-guide-9.3].  
 이 단계에서는 기존 WSFC 클러스터 노드 2에 고가용성 ASCS/SCS 인스턴스를 포함한 SAP를 설치하고 있습니다.

6. SAP ASCS /SCS 인스턴스 및 ProbePort의 Windows 방화벽 포트를 엽니다.  
 SAP ASCS/SCS 인스턴스에 사용되는 두 클러스터 노드에서 SAP ASCS/SCS에서 사용하는 모든 Windows 방화벽 포트를 열고 있습니다. 이러한 포트는 [Windows VM에서 고가용성 SAP NetWeaver 가이드][sap-ha-guide-8.8]에 나열되어 있습니다.  
 또한 62350 시나리오에서와 같이 Azure 내부 부하 분산 장치 프로브 포트를 엽니다.

7. [SAP ERS Windows 서비스 인스턴스의 시작 유형 변경][sap-ha-guide-9.4].

8. 새 전용 VM에서 [SAP 기본 응용 프로그램 서버 설치][sap-ha-guide-9.5].

9. 새 전용 VM에서 [SAP 추가 응용 프로그램 서버 설치][sap-ha-guide-9.6].

10. [SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트][sap-ha-guide-10].

## <a name="next-steps"></a>다음 단계

- [네트워킹 제한: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Azure Load Balancer에 대한 다중 VIP][load-balancer-multivip-overview]
- [Windows VM에서 고가용성 SAP NetWeaver 가이드][sap-ha-guide]
