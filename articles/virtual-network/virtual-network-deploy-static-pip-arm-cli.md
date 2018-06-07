---
title: 고정 공용 IP가 있는 VM 만들기 - Azure CLI | Microsoft Docs
description: Azure CLI(명령줄 인터페이스)를 사용하여 고정 공용 IP 주소가 있는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd44971162a79e53b731c5c89316f14e8bb0a1a6
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Azure CLI를 사용하여 고정 공용 IP 주소가 있는 VM 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell(클래식)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 클래식 배포 모델 대신 이 모델을 사용하도록 권장합니다.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>VM 만들기

다음 단계에서 변수에 대한 ""의 값은 시나리오의 설정을 사용하여 리소스를 만듭니다. 사용자 환경에 적절한 값으로 변경합니다.

1. [Azure CLI 2.0](/cli/azure/install-az-cli2)을 아직 설치하지 않은 경우 설치합니다.
2. [Linux VM에 SSH 공용 및 개인 키 쌍 만들기](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 단계를 완료하여 Linux VM에 SSH 공용 및 개인 키 쌍을 만듭니다.
3. 명령 셸에서 `az login` 명령을 사용하여 로그인합니다.
4. Linux 또는 Mac 컴퓨터에서 다음에 나오는 스크립트를 실행하여 VM을 만듭니다. Azure 공용 IP 주소, 가상 네트워크, 네트워크 인터페이스 및 VM 리소스는 모두 동일한 위치에 있어야 합니다. 리소스가 모두 동일한 리소스 그룹에 위치할 필요는 없습니다. 하지만 다음 스크립트에서는 모두 동일한 리소스 그룹에 위치합니다.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

스크립트는 VM 외에도 다음 항목을 만듭니다.
- 기본적으로 단일 프리미엄이 디스크를 관리했지만 만들 수 있는 디스크 유형에 대한 다른 옵션을 사용할 수 있습니다. 자세한 내용은 [Azure CLI 2.0을 사용하여 Linux VM 만들기](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- 가상 네트워크, 서브넷, NIC 및 공용 IP 주소 리소스입니다. 또는 *기존* 가상 네트워크, 서브넷, NIC 또는 공용 IP 주소 리소스를 사용할 수 있습니다. 추가 리소스를 만드는 것이 아니라 기존 네트워크 리소스를 사용하는 방법을 알아보려면 `az vm create -h`을 입력합니다.

## <a name = "validate"></a>VM 생성 및 공용 IP 주소의 유효성 검사

1. `az resource list --resouce-group IaaSStory --output table` 명령을 입력하여 스크립트로 만든 리소스의 목록을 볼 수 있습니다. 반환된 출력에 네트워크 인터페이스, 디스크, 공용 IP 주소, 가상 네트워크 및 가상 머신과 같은 5개의 리소스가 있어야 합니다.
2. `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table` 명령을 입력합니다. 반환된 출력에서 **IpAddress** 값을 확인하고 **PublicIpAllocationMethod** 값이 *고정*인지 확인합니다.
3. 다음 명령을 실행하기 전에 <>를 제거하고 *사용자 이름*을 스크립트의 **사용자 이름** 변수에 사용된 이름으로 바꾸고 *ipAddress*를 이전 단계의 **ipAddress**로 바꿉니다. `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>` 명령을 실행하여 VM에 연결합니다. 

## <a name= "clean-up"></a>VM 및 관련된 리소스 제거

프로덕션에서 VM을 사용하지 않는 경우 이 연습에서 만든 리소스를 삭제하는 것이 좋습니다. VM, 공용 IP 주소 및 디스크 리소스를 프로비전하는 경우 요금이 발생합니다. 이 연습에서 만든 리소스를 제거하려면 다음 단계를 완료합니다.

1. 리소스 그룹의 리소스를 보려면 `az resource list --resource-group IaaSStory` 명령을 실행합니다.
2. 리소스 그룹에 이 문서의 스크립트에서 만든 리소스 외에 다른 리소스가 없는지 확인합니다. 
3. 이 연습에서 만든 모든 리소스를 삭제하려면 `az group delete -n IaaSStory` 명령을 실행합니다. 이 명령은 리소스 그룹과 그 속에 포함된 모든 리소스를 삭제합니다.
 
## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다. [Windows VM에 여러 IP 주소를 할당](virtual-network-multiple-ip-addresses-cli.md)할 때처럼 반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 만든 VM 간에 네트워크 트래픽을 전달할 수 있습니다. 네트워크 인터페이스, 서브넷 또는 둘 간에 전달할 수 있는 트래픽을 제한하는 네트워크 보안 그룹 내에서 인바운드 및 아웃바운드 보안 규칙을 정의할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md)를 참조하세요.