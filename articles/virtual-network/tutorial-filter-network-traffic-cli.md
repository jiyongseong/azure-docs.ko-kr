---
title: 네트워크 트래픽 필터링 - Azure CLI | Microsoft Docs
description: 이 문서에서는 Azure CLI를 사용하 여 네트워크 보안 그룹을 통해 서브넷에 네트워크 트래픽을 필터링하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 11dc0e5f6ee398b2a745ed60cbc166e2a1697c3e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Azure CLI를 사용하여 네트워크 보안 그룹을 통해 네트워크 트래픽 필터링

네트워크 보안 그룹을 통해 가상 네트워크 서브넷에서 네트워크 트래픽을 인바운드 및 아웃바운드로 필터링할 수 있습니다. 네트워크 보안 그룹에는 IP 주소, 포트, 프로토콜에 따라 네트워크 트래픽을 필터링하는 보안 규칙이 포함됩니다. 보안 규칙은 서브넷에 배포된 리소스에 적용됩니다. 이 문서에서는 다음 방법을 설명합니다.

* 네트워크 보안 그룹 및 보안 규칙 만들기
* 가상 네트워크 만들기 및 서브넷에 네트워크 보안 그룹 연결
* 서브넷에 VM(가상 머신) 배포
* 트래픽 필터 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 


## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

네트워크 보안 그룹은 보안 규칙을 포함합니다. 보안 규칙은 원본 및 대상을 지정합니다. 원본 및 대상은 응용 프로그램 보안 그룹이 될 수 있습니다.

### <a name="create-application-security-groups"></a>응용 프로그램 보안 그룹 만들기

먼저 [az group create](/cli/azure/group#az_group_create)로 이 문서에서 만든 모든 리소스에 대한 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 리소스 그룹을 만듭니다. 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[az network asg create](/cli/azure/network/asg#az_network_asg_create)로 응용 프로그램 보안 그룹을 만듭니다. 응용 프로그램 보안 그룹을 사용하면 유사한 포트 필터링 요구 사항을 갖는 서버를 그룹화할 수 있습니다. 다음 예제에서는 두 응용 프로그램 보안 그룹을 만듭니다.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNsg*라는 네트워크 보안 그룹을 만듭니다. 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>보안 규칙 만들기

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 보안 규칙을 만듭니다. 다음 예제에서는 포트 80 및 443을 통해 인터넷에서 *myWebServers* 응용 프로그램 보안 그룹으로 가는 트래픽을 허용하는 규칙을 만듭니다.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

다음 예제에서는 포트 22를 통해 인터넷에서 *myMgmtServers* 응용 프로그램 보안 그룹으로 가는 트래픽을 허용하는 규칙을 만듭니다.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

이 문서에서 SSH(포트 22)는 *myAsgMgmtServers* VM에 대해 인터넷에 노출되어 있습니다. 프로덕션 환경에서는 포트 22를 인터넷에 노출하는 대신 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [개인](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 네트워크 연결을 통해 관리하려는 Azure 리소스에 연결하는 것이 좋습니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVirtualNetwork*라는 가상 네트워크를 만듭니다.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 가상 네트워크에 서브넷을 추가합니다. 다음 예에서는 *mySubnet*이라는 서브넷을 가상 네트워크에 추가하고 *myNsg* 네트워크 보안 그룹을 연결합니다.

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 트래픽 필터링의 유효성을 검사할 수 있도록 가상 네트워크에 두 VM을 만듭니다. 

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 다음 예제에서는 웹 서버 역할을 수행 하는 VM을 만듭니다. `--asgs myAsgWebServers` 옵션을 사용하면 Azure는 VM에 대해 만든 네트워크 인터페이스를*myAsgWebServers* 응용 프로그램 보안 그룹의 멤버로 지정할 수 있습니다.

Azure에서 VM을 만들 때 만든 네트워크 인터페이스에 대해 Azure가 기본 네트워크 보안 그룹을 만들지 않도록 하기 위해 `--nsg ""` 옵션을 지정합니다. 이 문서를 간소화하기 위해 암호가 사용됩니다. 일반적으로 키는 프로덕션 배포에 사용됩니다. 키를 사용하는 경우 나머지 단계에 대해 SSH 에이전트 전달도 구성해야 합니다. 자세한 내용은 SSH 클라이언트를 위한 설명서를 참조하세요. 다음 명령에서 `<replace-with-your-password>`를 원하는 암호로 바꿉니다.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

VM을 만드는 데 몇 분이 걸립니다. VM을 만들면 다음 예제 출력과 유사한 출력이 반환됩니다. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress**를 기록해 둡니다. 이 주소는 이후 단계에서 인터넷을 통해 VM에 액세스하는 데 사용됩니다.  관리 서버 역할을 하는 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

VM을 만드는 데 몇 분이 걸립니다. VM을 만든 후에는 반환된 출력에서 **publicIpAddress**를 적어둡니다. 이 주소는 다음 단계에서 VM에 액세스하는 데 사용됩니다. Azure에서 VM 만들기가 완료될 때까지 다음 단계를 진행하지 않습니다.

## <a name="test-traffic-filters"></a>트래픽 필터 테스트

다음 명령을 사용하여 *myVmMgmt* VM으로 SSH 세션을 만듭니다. *<publicIpAddress>*를 VM의 공용 IP 주소로 바꿉니다. 위의 예제에서 IP 주소는 *13.90.242.231*입니다.

```bash 
ssh azureuser@<publicIpAddress>
```

암호를 입력하라는 메시지가 표시되면 [VM 만들기](#create-virtual-machines)에서 입력한 암호를 입력합니다.

포트 22는 *myAsgMgmtServers* VM에 연결된 네트워크 인터페이스가 In인 *myVmMgmt* 응용 프로그램 보안 그룹으로 가는 인터넷의 인바운드 트래픽을 허용하기 때문에 연결에 성공합니다.

*myVmMgmt* VM에서 *myVmWeb* VM으로 SSH하려면 다음 명령을 사용합니다.

```bash 
ssh azureuser@myVmWeb
```

각 네트워크 보안 그룹의 기본 보안 규칙에서 가상 네트워크 내 모든 IP 주소 간에 모든 포트를 경유한 트래픽을 허용하기 때문에 연결이 성공합니다. *myAsgWebServers*에 대한 보안 규칙에서는 인터넷으로부터의 포트 22 인바운드 트래픽을 허용하지 않으므로 인터넷으로부터 *myVmWeb* VM에 대한 SSH가 불가능합니다.

다음 명령을 사용하여 *myVmWeb* VM에 nginx 웹 서버를 설치합니다.

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

기본 보안 규칙에서는 인터넷에 대한 모든 아웃바운드 트래픽을 허용하므로 *myVmWeb* VM은 nginx를 검색하기 위해 인터넷에 아웃바운드로 연결할 수 있습니다. *myVmWeb* SSH 세션을 종료합니다. 그러면 *myVmMgmt* VM의 `username@myVmMgmt:~$` 프롬프트에 남게 됩니다. *myVmWeb* VM에서 nginx 시작 화면을 검색하기 위해 다음 명령을 입력합니다.

```bash
curl myVmWeb
```

*myVmMgmt* VM에서 로그아웃합니다. Azure 외부에서 *myVmWeb* 웹 서버에 액세스할 수 있는지 확인하기 위해 자신의 컴퓨터에서 `curl <publicIpAddress>`를 입력합니다. 포트 80은 *myVmWeb* VM에 연결된 네트워크 인터페이스가 In인 *myAsgWebServers* 응용 프로그램 보안 그룹으로 가는 인터넷의 인바운드 트래픽을 허용하기 때문에 연결에 성공합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 네트워크 보안 그룹을 만들어 가상 네트워크 서브넷에 연결했습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md) 및 [네트워크 보안 그룹 관리](manage-network-security-group.md)를 참조하세요.

기본적으로 Azure는 서브넷 간에 트래픽을 라우팅합니다. 그 대신 방화벽 역할을 하는 VM 등을 통해 서브넷 간에 트래픽을 라우팅하도록 선택할 수 있습니다. 자세한 내용은 [경로 테이블 만들기](tutorial-create-route-table-cli.md)를 참조하세요.