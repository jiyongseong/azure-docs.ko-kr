---
title: 가상 네트워크 만들기 - 빠른 시작 - Azure CLI | Microsoft Docs
description: 이 빠른 시작에서 Azure Portal을 사용하여 가상 네트워크를 만드는 방법에 대해 알아봅니다. 가상 네트워크를 사용하면 가상 머신과 같은 Azure 리소스에서 서로 인터넷을 통해 개별적으로 통신할 수 있습니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: bb45b2b4ecd89187e94066bc81782174738fe3a9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 가상 네트워크 만들기

가상 네트워크를 사용하면 VM(가상 머신)과 같은 Azure 리소스에서 서로 인터넷을 통해 개별적으로 통신할 수 있습니다. 이 빠른 시작에서는 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크를 만든 후에 두 개의 VM을 가상 네트워크에 배포합니다. 그런 다음, 인터넷에서 하나의 VM에 연결하고 다른 VM과 개별적으로 통신합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 


## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들려면 먼저 가상 네트워크가 포함될 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *default*라는 하나의 서브넷이 있는 *myVirtualNetwork*라는 기본 가상 네트워크를 만듭니다.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. `--no-wait` 옵션은 백그라운드에서 VM을 만들므로 다음 단계를 계속 진행할 수 있습니다. 다음 예제에서는 *myVm1*이라는 VM을 만듭니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분이 걸립니다. VM이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 반환합니다. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress**를 기록해 둡니다. 이 주소는 다음 단계에서 인터넷에서 VM에 연결하는 데 사용됩니다.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음 명령에서 `<publicIpAddress>`를 *myVm2* VM의 공용 IP 주소로 바꾸고 다음 명령을 입력합니다.

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>VM 간 통신

*myVm2* 및 *myVm1* VM 간의 개별적 통신을 확인하려면 다음 명령을 입력합니다.

```bash
ping myVm1 -c 4
```

*10.0.0.4*에서 4개의 응답을 받게 됩니다.

*myVm2* VM을 사용하여 SSH 세션을 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 가상 네트워크와 두 개의 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결하고 VM 및 다른 VM 간에 개별적으로 통신했습니다. 가상 네트워크 설정에 대한 자세한 내용은 [가상 네트워크 관리](manage-virtual-network.md)를 참조하세요. 

기본적으로 Azure는 가상 머신 간에 무제한 개별 통신을 허용하지만, 인터넷에서 Linux VM에 대한 인바운드 원격 데스크톱 연결만 허용합니다. VM 간에 다양한 유형의 네트워크 통신을 허용하거나 제한하는 방법을 알아보려면 [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)을 참조하세요.
