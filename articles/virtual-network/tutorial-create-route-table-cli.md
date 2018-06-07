---
title: 네트워크 트래픽 라우팅 - Azure CLI | Microsoft Docs
description: 이 문서에서는 Azure CLI를 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: eb4a28b5a57d7e301e800cd4ad87c56b7c5df6d2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Azure CLI를 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 자동으로 라우팅합니다. 고유의 라우팅을 만들어 Azure의 기본 라우팅을 재정의할 수 있습니다. 사용자 지정 경로를 만드는 기능은 예를 들어 NVA(네트워크 가상 어플라이언스)를 통해 서브넷 간 트래픽을 라우팅하려는 경우에 유용합니다. 이 문서에서는 다음 방법을 설명합니다.

* 경로 테이블 만들기
* 경로 만들기
* 여러 서브넷이 있는 가상 네트워크 만들기
* 서브넷에 경로 테이블 연결
* 트래픽을 라우팅하는 NVA 만들기
* 다른 서브넷에 VM(가상 머신) 배포
* NVA를 통해 한 서브넷에서 다른 서브넷으로 트래픽 라우팅

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-a-route-table"></a>경로 테이블 만들기

이 문서에서 생성된 모든 리소스에 대해 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만들어야 경로 테이블을 만들 수 있습니다. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

[az network route-table create](/cli/azure/network/route#az_network_route_table_create)를 사용하여 경로 테이블을 만듭니다. 다음 예제에서는 *myRouteTablePublic*이라는 경로 테이블을 만듭니다. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>경로 만들기

[az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)를 사용하여 경로 테이블에 경로를 만듭니다. 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

서브넷에 경로 테이블을 연결하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다. [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 하나의 서브넷이 있는 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 2개의 추가 서브넷을 만듭니다.

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 *myRouteTablePublic* 경로 테이블을 *공용* 서브넷에 연결합니다.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA 만들기

NVA는 라우팅, 방화벽 또는 WAN 최적화와 같은 네트워크 기능을 수행하는 VM입니다.

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 *DMZ* 서브넷에서 NVA를 만듭니다. VM을 만들 때 Azure는 기본적으로 VM에 공용 IP 주소를 만들고 할당합니다. VM이 인터넷에서 연결될 필요가 없으므로 `--public-ip-address ""` 매개 변수는 Azure에서 VM에 공용 IP 주소를 만들고 할당하지 못하도록 지시합니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분이 걸립니다. Azure가 VM 만들기를 완료하고 VM에 대한 출력을 반환할 때까지 다음 단계로 이동하지 마십시오. 

네트워크 인터페이스가 자체 IP 주소를 향하지 않고 수신된 네트워크 트래픽을 전달할 수 있으려면 네트워크 인터페이스에 대해 IP 전달을 사용 가능으로 설정해야 합니다. [az network nic update](/cli/azure/network/nic#az_network_nic_update)를 사용하여 네트워크 인터페이스에 대한 IP 전달을 사용하도록 설정합니다.

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

VM 내 운영 체제 또는 VM 내에서 실행 중인 응용 프로그램도 네트워크 트래픽을 전달할 수 있어야 합니다. [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set)를 사용하여 VM의 운영 체제 내에서 IP 전달을 활성화합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
명령은 실행되기까지 최대 1분이 걸릴 수 있습니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 *공용* 서브넷의 해당 트래픽이 NVA를 통해 *개인* 서브넷에 라우팅되는지 유효성을 검사할 수 있도록 가상 네트워크에 두 개의 VM을 만듭니다. 

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 *공용* 서브넷에 VM을 만듭니다. `--no-wait` 매개 변수를 사용하면 Azure가 백그라운드에서 명령을 실행할 수 있으므로 다음 명령을 계속 진행할 수 있습니다. 이 문서를 간소화하기 위해 암호가 사용됩니다. 일반적으로 키는 프로덕션 배포에 사용됩니다. 키를 사용하는 경우 SSH 에이전트 전달도 구성해야 합니다. 자세한 내용은 SSH 클라이언트를 위한 설명서를 참조하세요. 다음 명령에서 `<replace-with-your-password>`를 원하는 암호로 바꿉니다.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

*개인* 서브넷에 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

VM을 만드는 데 몇 분이 걸립니다. VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
**publicIpAddress**를 기록해 둡니다. 이 주소는 이후 단계에서 인터넷을 통해 VM에 액세스하는 데 사용됩니다.

## <a name="route-traffic-through-an-nva"></a>NVA를 통한 트래픽 라우팅

다음 명령을 사용하여 *myVmPrivate* VM으로 SSH 세션을 만듭니다. *<publicIpAddress>*를 VM의 공용 IP 주소로 바꿉니다. 위의 예제에서 IP 주소는 *13.90.242.231*입니다.

```bash 
ssh azureuser@<publicIpAddress>
```

암호를 입력하라는 메시지가 표시되면 [가상 머신 만들기](#create-virtual-machines)에서 선택한 암호를 입력합니다.

다음 명령을 사용하여 *myVmPrivate* VM에 경로 추적을 설치합니다.

```bash 
sudo apt-get install traceroute
```

*myVmPrivate* VM에서 *myVmPublic* VM으로의 네트워크 트래픽 라우팅을 테스트하려면 다음 명령을 사용합니다.

```bash
traceroute myVmPublic
```

응답은 다음 예제와 유사합니다.

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

트래픽이 *myVmPrivate* VM에서 *myVmPublic* VM으로 직접 라우팅되는지 볼 수 있습니다. Azure의 기본 경로는 트래픽을 서브넷 간에 직접 라우팅합니다. 

*myVmPrivate* VM에서 *myVmPublic* VM으로 SSH하려면 다음 명령을 사용합니다.

```bash 
ssh azureuser@myVmPublic
```

다음 명령을 사용하여 *myVmPublic* VM에 경로 추적을 설치합니다.

```bash 
sudo apt-get install traceroute
```

*myVmPublic* VM에서 *myVmPrivate* VM으로의 네트워크 트래픽 라우팅을 테스트하려면 다음 명령을 사용합니다.

```bash
traceroute myVmPrivate
```

응답은 다음 예제와 유사합니다.

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
첫 번째 홉이 NVA의 개인 IP 주소인 10.0.2.4인 것을 확인할 수 있습니다. 두 번째 홉은 *myVmPrivate* VM의 개인 IP 주소인 10.0.1.4입니다. 경로가 *myRouteTablePublic* 경로 테이블에 추가되고 *공용* 서브넷에 연결되었으므로 Azure는 트래픽을 직접 *개인* 서브넷에 라우팅하는 대신 NVA를 통해 트래픽을 라우팅합니다.

*myVmPublic* 및 *myVmPrivate* VM 모두에 대한 SSH 세션을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 경로 테이블을 만들고 서브넷에 연결했습니다. 공용 서브넷에서 개인 서브넷으로 트래픽을 라우팅하는 간단한 NVA를 만들었습니다. 이제 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)에서 방화벽 및 WAN 최적화 같은 네트워크 기능을 수행하는 다양한 미리 구성된 NVA를 배포합니다. 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md) 및 [경로 테이블 관리](manage-route-table.md)를 참조하세요.

가상 네트워크 내에서 여러 Azure 리소스를 배포할 수 있는 반면, 일부 Azure PaaS 서비스에 대한 리소스는 가상 네트워크에 배포할 수 없습니다. 하지만 일부 Azure PaaS 서비스의 리소스에 대한 액세스를 가상 네트워크 서브넷의 트래픽만으로 제한할 수 있습니다. 방법을 알아보려면 [PaaS 리소스에 대한 네트워크 액세스 제한](tutorial-restrict-network-access-to-resources-cli.md)을 참조하세요.
