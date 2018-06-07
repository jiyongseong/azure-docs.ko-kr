---
title: 가상 머신 네트워크 라우팅 문제 진단 - Azure CLI | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher의 다음 홉 기능을 사용하여 가상 머신 네트워크 라우팅 문제를 진단하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fcb7ec2e40b5c0e8794d2f4d70395dcbecca019c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182163"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>가상 머신 네트워크 라우팅 문제 진단 - Azure CLI

이 문서에서는 VM(가상 머신)을 배포한 다음, IP 주소와 URL로 전송되는 통신을 확인합니다. 통신 오류의 원인 및 해결 방법을 확인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. CLI 버전을 확인한 후 `az login`을 실행하여 Azure와의 연결을 만듭니다. 이 문서에서 CLI 명령은 Bash 셸에서 실행하도록 형식이 지정됩니다.

## <a name="create-a-vm"></a>VM 만들기

VM을 만들려면 먼저 VM이 포함될 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. 다음 예제에서는 *myVm*이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분이 걸립니다. VM이 만들어지고 CLI에서 출력을 반환할 때까지 나머지 단계를 진행하지 마세요.

## <a name="test-network-communication"></a>네트워크 통신 테스트

Network Watcher와의 네트워크 통신을 테스트하려면 먼저 테스트하려는 VM이 있는 지역에서 Network Watcher를 사용하도록 설정한 다음, Network Watcher의 다음 홉 기능을 사용하여 통신을 테스트해야 합니다.

### <a name="enable-network-watcher"></a>네트워크 감시자 사용

미국 동부 지역에서 이미 Network Watcher를 사용하도록 설정한 경우, [다음 홉 사용](#use-next-hop)으로 건너뜁니다. [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) 명령을 사용하여 미국 동부 지역의 네트워크 감시자를 만듭니다.

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>다음 홉 사용

Azure에서는 기본 대상에 대한 경로를 자동으로 만듭니다. 기본 경로를 재정의하는 사용자 지정 경로를 만들 수 있습니다. 경우에 따라 사용자 지정 경로로 인해 통신이 실패할 수 있습니다. VM에서 라우팅을 테스트하려면 [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop)을 사용하여 트래픽의 대상이 특정 주소로 지정되는 경우 다음 라우팅 홉을 확인합니다.

VM에서 www.bing.com에 대한 IP 주소 중 하나로 아웃바운드 통신을 테스트합니다.

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

몇 초 후에 결과는 **nextHopType**이 **인터넷**이며, **routeTableId**가 **시스템 경로**임을 알려줍니다. 이 결과를 확인하면 대상에 대한 유효한 경로가 있음을 알 수 있습니다.

VM에서 172.31.0.100으로 아웃바운드 통신을 테스트합니다.

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

반환된 결과는 **없음**이 **nextHopType**이며, **routeTableId**가 **시스템 경로**임도 알려줍니다. 이 결과를 사용하면 대상에 대한 유효한 시스템 경로가 있지만, 대상에 트래픽을 라우팅하는 다음 홉이 없음을 알 수 있습니다.

## <a name="view-details-of-a-route"></a>경로의 세부 정보 보기

추가로 라우팅을 분석하려면 [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) 명령으로 네트워크 인터페이스에 대한 유효 경로를 검토합니다.

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

반환되는 출력에 다음 텍스트가 포함됩니다.

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

[다음 홉 사용](#use-next-hop)에서 `az network watcher show-next-hop` 명령을 사용하여 13.107.21.200에 대한 아웃바운드 통신을 테스트하는 경우 출력의 다른 경로가 주소를 포함하지 않으므로 **addressPrefix** 0.0.0.0/0** 포함 경로를 사용하여 주소에 트래픽을 라우팅했습니다. 기본적으로 다른 경로의 주소 접두사 내에서 지정되지 않은 모든 주소는 인터넷으로 라우팅됩니다.

그러나 `az network watcher show-next-hop` 명령을 사용하여 172.31.0.100에 대한 아웃바운드 통신을 테스트하는 경우 결과에서 다음 홉 유형이 없음을 알 수 있습니다. 반환되는 출력에서 다음 텍스트를 볼 수 있습니다.

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

`az network watcher nic show-effective-route-table` 명령의 출력에서 볼 수 있듯이 172.31.0.100 주소를 포함하는 172.16.0.0/12 접두사에 대한 기본 경로가 있더라도 **nextHopType**은 **없음**입니다. Azure에서는 172.16.0.0/12에 대한 기본 경로를 만들지만 필요가 있을 때까지 다음 홉 형식을 지정하지 않습니다. 예를 들어, 가상 네트워크의 주소 공간에 172.16.0.0/12 주소 범위를 추가한 경우, Azure는 **nextHopType**을 경로의 **가상 네트워크**로 변경합니다. 그런 다음, 검사에서는 **가상 네트워크**를 **nextHopType**으로 표시합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 VM을 만들고 VM에서 네트워크 라우팅을 진단했습니다. Azure가 여러 개의 기본 경로를 만들고 두 개의 다른 대상에 대한 라우팅을 테스트했음을 알아보았습니다. [Azure에서 라우팅](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 및 [사용자 지정 경로를 만드는](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route) 방법을 알아봅니다.

아웃바운드 VM 연결의 경우 Network Watcher의 [연결 문제 해결](network-watcher-connectivity-cli.md) 기능을 사용하여 VM과 끝점 간의 네트워크 트래픽을 허용하거나 거부하는 대기 시간을 결정할 수도 있습니다. Network Watcher 연결 모니터 기능을 사용하여 시간에 따라 IP 주소 또는 URL과 같은 VM과 끝점 간의 통신을 모니터링할 수 있습니다. 방법을 알아보려면 [네트워크 연결 모니터링](connection-monitor.md)을 참조하세요.