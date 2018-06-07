---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0bf55d2353d3524e65602c7e67b7adbf80432043
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
다음 작업을 수행하기 전에 VNet과 게이트웨이 서브넷을 먼저 만들어야 합니다.

> [!NOTE]
> 이러한 예제가 S2S/ExpressRoute 공존 구성에는 적용되지 않습니다.
> 공존 구성의 게이트웨이 사용에 대한 자세한 내용은 [공존 연결 구성](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)을 참조하세요.

## <a name="add-a-gateway"></a>게이트웨이를 추가합니다.

다음 명령을 사용하여 게이트웨이를 만듭니다. 모든 값을 자신의 고유한 값으로 대체해야 합니다.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>게이트웨이가 만들어졌는지 확인합니다.

아래 명령을 사용하여 게이트웨이가 만들어졌는지 확인합니다. 또한 이 명령은 다른 작업에 필요한 게이트웨이 ID를 검색합니다.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>게이트웨이 크기 조정

다양한 [게이트웨이 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)가 있습니다. 다음 명령을 사용하여 언제든지 게이트웨이 SKU를 변경합니다.

> [!IMPORTANT]
> 이 명령은 UltraPerformance 게이트웨이에는 작동하지 않습니다. 게이트웨이를 UltraPerformance 게이트웨이로 변경하려면 먼저 기존 ExpressRoute 게이트웨이를 제거한 다음 새 UltraPerformance 게이트웨이를 만듭니다. 게이트웨이를 UltraPerformance 게이트웨이에서 다운그레이드하려면 먼저 UltraPerformance 게이트웨이를 제거한 후 새 게이트웨이를 만듭니다. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>게이트웨이 제거

다음 명령을 사용하여 게이트웨이를 제거합니다.

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```