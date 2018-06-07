---
title: IPv6를 사용하여 공용 부하 분산 장치 만들기 - Azure CLI | Microsoft Docs
description: Azure CLI를 사용하여 Azure Resource Manager에서 IPv6으로 공용 부하 분산 장치를 만드는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: ipv6, Azure Load Balancer, 이중 스택, 공용 IP, 기본 ipv6, 모바일, iot
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 62f22ccadfabd2f3d6906beb3c241703d4e6383f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure Resource Manager에서 IPv6으로 공용 부하 분산 장치 만들기

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [템플릿](load-balancer-ipv6-internet-template.md)


Azure 부하 분산 장치는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치는 클라우드 서비스의 정상 서비스 인스턴스 또는 부하 분산 장치 집합의 가상 머신 간에 들어오는 트래픽을 배포하여 고가용성을 제공합니다. 부하 분산 장치는 여러 포트, 여러 IP 주소 또는 둘 다에서 이러한 서비스를 제공할 수도 있습니다.

## <a name="example-deployment-scenario"></a>예제 배포 시나리오

다음 다이어그램은 이 문서에서 설명한 예제 템플릿을 사용하여 배포된 부하 분산 솔루션을 보여줍니다.

![부하 분산 장치 시나리오](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

이 시나리오에서는 다음과 같은 Azure 리소스를 만듭니다.

* 2대의 가상 머신(VM)
* 할당된 IPv4 및 IPv6 주소를 사용하는 각 VM에 대한 가상 네트워크 인터페이스
* IPv4 및 IPv6 공용 IP 주소를 사용하는 공용 부하 분산 장치
* 두 개의 VM이 포함된 가용성 집합
* 공용 VIP를 개인 끝점으로 매핑하기 위한 두 개의 부하 분산 규칙

## <a name="deploy-the-solution-by-using-azure-cli"></a>Azure CLI를 사용하여 솔루션 배포

다음 단계에서는 Azure CLI와 함께 Azure Resource Manager를 사용하여 공용 부하 분산 장치를 만드는 방법을 보여줍니다. Azure Resource Manager를 사용하면 각 개체를 개별적으로 만들고 구성한 다음 함께 리소스를 만듭니다.

부하 분산 장치를 배포하려면 다음 개체를 만들고 구성합니다.

* **프런트 엔드 IP 구성**: 들어오는 네트워크 트래픽에 대한 공용 IP 주소를 포함합니다.
* **백 엔드 주소 풀**: 부하 분산 장치의 네트워크 트래픽을 수신하기 위해 가상 머신에 NIC(네트워크 인터페이스)를 포함합니다.
* **부하 분산 규칙**: 백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.
* **인바운드 NAT 규칙**: 백 엔드 주소 풀에 있는 특정 가상 머신에 대한 포트에 부하 분산 장치의 공용 포트를 매핑하는 NAT(Network Address Translation) 규칙을 포함합니다.
* **프로브**: 백 엔드 주소 풀의 가상 머신 인스턴스의 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다.

자세한 내용은 [부하 분산 장치에 대한 Azure Resource Manager 지원](load-balancer-arm.md)을 참조하세요.

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Azure Resource Manager를 사용하도록 Azure CLI 환경 설정

이 예제의 PowerShell 명령 창에서 Azure CLI 도구를 실행합니다. 가독성 및 재사용을 개선하기 위해 Azure PowerShell cmdlet이 아닌 PowerShell의 스크립팅 기능을 사용합니다.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../cli-install-nodejs.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.

2. Resource Manager 모드로 전환하려면 **azure config mode** 명령을 실행합니다.

    ```azurecli
    azure config mode arm
    ```

    예상 출력:

        info:    New mode is arm

3. Azure에 로그인하고 구독 목록을 가져옵니다.

    ```azurecli
    azure login
    ```

4. 프롬프트에서 Azure 자격 증명을 입력합니다.

    ```azurecli
    azure account list
    ```

5. 사용하려는 구독을 선택하고 다음 단계에서 사용할 구독 ID를 적어둡니다.

6. Azure CLI 명령과 함께 사용할 PowerShell 변수를 설정합니다.

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>리소스 그룹, 부하 분산 장치, 가상 네트워크 및 서브넷 만들기

1. 리소스 그룹 만들기:

    ```azurecli
    azure group create $rgName $location
    ```

2. 부하 분산 장치 만들기:

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. 가상 네트워크 만들기:

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. 가상 네트워크에서 두 서브넷을 만듭니다.

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>프런트 엔드 풀에 대한 공용 IP 주소 만들기

1. PowerShell 변수를 설정합니다.

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. 프론트 엔드 IP 풀에 대한 공용 IP 주소를 만듭니다.

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > 부하 분산 장치는 공용 IP의 도메인 레이블을 FQDN(정규화된 도메인 이름)으로 사용합니다. 이는 클래식 배포의 변경으로, 클라우드 서비스 이름을 부하 분산 장치 FQDN으로 사용합니다.
    >
    > 이 예제에서 FQDN은 *contoso09152016.southcentralus.cloudapp.azure.com*입니다.

## <a name="create-front-end-and-back-end-pools"></a>프런트 엔드 및 백 엔드 풀 만들기

이 섹션에서는 다음과 같은 IP 풀을 만듭니다.
* 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
* 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 전송하는 백 엔드 IP 풀

1. PowerShell 변수를 설정합니다.

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. 프런트 엔드 IP 풀을 만들고, 이전 단계에서 만든 공용 IP 및 부하 분산 장치와 연결합니다.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>프로브 부하, NAT 규칙 및 분산 장치 규칙 만들기

이 예제에서는 다음 항목을 만듭니다.

* TCP 포트 80에 대한 연결을 확인하기 위한 프로브 규칙
* RDP에서 포트 3389에 들어오는 모든 트래픽을 포트 3389로 변환하는 NAT 규칙\*
* RDP(원격 데스크톱 프로토콜)에서 포트 3391에 들어오는 모든 트래픽을 포트 3389로 변환하는 NAT 규칙\*
* 포트 80에서 들어오는 모든 트래픽을 백 엔드 풀에 있는 주소의 포트 80으로 분산하는 부하 분산 장치 규칙

\* NAT 규칙은 부하 분산 장치 뒤에 특정 가상 머신 인스턴스와 관련이 있습니다. 포트 3389에 도달하는 네트워크 트래픽은 NAT 규칙과 연결된 특정 가상 머신 및 포트에 보내집니다. NAT 규칙에 대한 프로토콜(UDP 또는 TCP)을 지정해야 합니다. 프로토콜을 모두 동일한 포트를 할당할 수 없습니다.

1. PowerShell 변수를 설정합니다.

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. 프로브를 만듭니다.

    다음 예제는 15초마다 백 엔드 TCP 포트 80에 대한 연결을 확인하는 TCP 프로브를 만듭니다. 두 차례의 연속 실패 후에는 백 엔드 리소스를 사용할 수 없음으로 표시합니다.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. 백 엔드 리소스에 대한 RDP 연결을 허용하는 인바운드 NAT 규칙을 만듭니다.

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. 요청을 수신하는 프런트 엔드에 따라 다른 백 엔드 포트로 트래픽을 전송하는 부하 분산 장치 규칙을 만듭니다.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. 설정을 확인합니다.

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
    ```

    예상 출력:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>NIC 만들기

NIC를 만들어 NAT 규칙, 부하 분산 장치 규칙 및 프로브와 연결합니다.

1. PowerShell 변수를 설정합니다.

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. 각 백 엔드에 대한 NIC를 만들고 IPv6 구성을 추가합니다.

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>백 엔드 VM 리소스를 만들고 각 NIC를 연결합니다.

VM을 만들려면 저장소 계정이 있어야 합니다. 부하 분산을 하려면 VM이 가용성 집합의 구성원이어야 합니다. VM 만들기에 대한 자세한 내용은 [PowerShell을 사용하여 Azure VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)를 참조하세요.

1. PowerShell 변수를 설정합니다.

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > 이 예제에서는 일반 텍스트인 VM의 사용자 이름 및 비밀번호를 사용합니다. 일반 텍스트인 이러한 자격 증명을 사용할 경우 적절한 조치를 취합니다. PowerShell에서 자격 증명을 처리하는 보다 안전한 방법은 [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx) cmdlet을 참조하세요.

2. 저장소 계정 및 가용성 집합을 만듭니다.

    VM을 만들 때 기존 저장소 계정을 사용할 수도 있습니다. 다음 명령을 사용하여 새로운 저장소 계정을 만듭니다.

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. 가용성 집합을 만듭니다.

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. 연결된 NIC로 가상 머신을 만듭니다.

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-cli.md)  
[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)  
[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
