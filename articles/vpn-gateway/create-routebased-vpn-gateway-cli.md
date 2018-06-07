---
title: '경로 기반 Azure VPN Gateway 만들기: CLI | Microsoft Docs'
description: CLI를 사용하여 VPN 게이트웨이를 만드는 방법을 신속하게 알아보기
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: d0f4e292c6f5a2725b4a9efe91e78c6e634ea64e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>CLI를 사용하여 경로 기반 VPN 게이트웨이 만들기

이 문서는 Azure CLI를 사용하여 경로 기반 Azure VPN Gateway를 빠르게 만드는 데 도움이 됩니다. VPN 게이트웨이는 온-프레미스 네트워크에 대한 VPN 연결을 만들 때 사용됩니다. VPN 게이트웨이를 사용하여 VNet을 연결할 수도 있습니다.

이 문서의 단계에서는 VNet, 서브넷, 게이트웨이 서브넷 및 경로 기반 VPN 게이트웨이(가상 네트워크 게이트웨이)를 만듭니다. 가상 네트워크 게이트웨이를 만드는 데에는 45분 이상이 걸릴 수 있습니다. 게이트웨이 생성이 완료되면 연결을 만들 수 있습니다. 이러한 단계에는 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 명령을 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 **EastUS** 위치에 **VNet1**이라는 가상 네트워크를 만듭니다.

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>게이트웨이 서브넷 추가

게이트웨이 서브넷은 가상 네트워크 게이트웨이 서비스가 사용하는 예약된 IP 주소를 포함합니다. 다음 예제를 사용하여 게이트웨이 서브넷을 추가합니다.

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>공용 IP 주소 요청

VPN 게이트웨이에는 동적으로 할당된 공용 IP 주소가 있어야 합니다. 공용 IP 주소는 가상 네트워크용으로 만들 VPN Gateway에 할당됩니다. 다음 예제를 사용하여 공용 IP 주소를 요청합니다.

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>VPN 게이트웨이 만들기

[az network vnet-gateway create](/cli/azure/group#az_network_vnet_gateway_create) 명령을 사용하여 VPN Gateway를 만듭니다.

`--no-wait` 매개 변수를 사용하여 이 명령을 실행하면 피드백 또는 출력이 보이지 않습니다. `--no-wait` 매개 변수는 백그라운드에서 게이트웨이를 만드는 것을 허용합니다. VPN 게이트웨이가 즉시 만들어진다는 것을 의미하지는 않습니다.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

VPN 게이트웨이를 만드는 데에는 45분 이상이 걸릴 수 있습니다.

## <a name="viewgw"></a>VPN 게이트웨이 보기

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

응답은 다음과 유사합니다.

```
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>공용 IP 주소 보기

게이트웨이에 할당된 공용 IP 주소를 보려면 다음 예제를 사용합니다.

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

**ipAddress** 필드와 연결된 값은 VPN 게이트웨이의 공용 IP 주소입니다.

예제 응답:

```
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```
## <a name="clean-up-resources"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않으면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹을 삭제합니다. 그러면 리소스 그룹 및 포함된 모든 리소스가 삭제됩니다.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>다음 단계

게이트웨이 생성이 완료되면 가상 네트워크와 VNet 간에 연결을 만들 수 있습니다. 또는 가상 네트워크와 온-프레미스 위치 간에 연결을 만듭니다.

> [!div class="nextstepaction"]
> [사이트 간 연결 만들기](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [지점 및 사이트 간 연결 만들기](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [다른 VNet에 대한 연결 만들기](vpn-gateway-vnet-vnet-rm-ps.md)