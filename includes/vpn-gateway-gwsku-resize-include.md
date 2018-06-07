---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
게이트웨이 SKU의 크기를 조정하여 현재 SKU(VpnGw1, VpnGw2 및 VPNGW3)를 더 강력하게 업그레이드하려는 경우 `Resize-AzureRmVirtualNetworkGateway` PowerShell cmdlet을 사용할 수 있습니다. 또한 이 cmdlet을 사용하여 게이트웨이 SKU 크기를 다운그레이드할 수도 있습니다. 기본 게이트웨이 SKU를 사용하는 경우 [대신 이러한 지침을 사용하여](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) 게이트웨이의 크기를 조정합니다.

다음 PowerShell 예제에서는 VpnGw2로 크기가 조정되는 게이트웨이 SKU를 보여 줍니다.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

가상 네트워크 게이트웨이에 대한 **구성** 페이지로 이동하고 드롭다운 목록에서 다른 SKU를 선택하여 Azure Portal에서 게이트웨이의 크기를 조정할 수도 있습니다.