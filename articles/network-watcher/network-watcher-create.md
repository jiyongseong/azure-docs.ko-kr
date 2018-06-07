---
title: Azure Network Watcher 인스턴스 만들기 | Microsoft Docs
description: Azure 지역에서 Network Watcher를 사용하도록 설정하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9d3e579cd58bc6c7d67b29998ea5a48a65548b0a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher 인스턴스 만들기

Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다.

## <a name="create-a-network-watcher-in-the-portal"></a>포털에서 Network Watcher 만들기

**모든 서비스** > **네트워킹** > **Network Watcher**로 이동합니다. Network Watcher에 대해 사용하도록 설정하려는 모든 구독을 선택할 수 있습니다. 이 작업은 사용할 수 있는 모든 지역에서 Network Watcher를 만듭니다.

![Network Watcher 만들기](./media/network-watcher-create/figure1.png)

포털을 사용하여 Network Watcher를 사용하도록 설정하면 Network Watcher 인스턴스의 이름이 *NetworkWatcher_region_name*으로 자동 설정됩니다. 여기서 *region_name*은 인스턴스가 설정된 Azure 지역에 해당됩니다. 예를 들어 미국 중서부에서 활성화된 Network Watcher의 이름은 *NetworkWatcher_westcentralus*입니다.

Network Watcher 인스턴스가 *NetworkWatcherRG*라는 리소스 그룹에 자동으로 생성됩니다. 리소스 그룹은 아직 존재하지 않는 경우 만들어집니다.

Network Watcher 인스턴스와 이 인스턴스가 배치된 리소스 그룹의 이름을 사용자 지정하려면 다음 섹션에 설명된 Powershell, Azure CLI, REST API 또는 ARMClient 메서드를 사용할 수 있습니다. 각 옵션에서 Network Watcher를 배치하려면 리소스 그룹이 있어야 합니다.  

## <a name="create-a-network-watcher-with-powershell"></a>PowerShell을 사용하여 Network Watcher 만들기

Network Watcher의 인스턴스를 만들려면 다음 예제를 실행합니다.

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Azure CLI를 사용하여 Network Watcher 만들기

Network Watcher의 인스턴스를 만들려면 다음 예제를 실행합니다.

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>REST API를 사용하여 Network Watcher 만들기

PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다.

### <a name="log-in-with-armclient"></a>ARMClient에 로그인

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Network Watcher 만들기

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>다음 단계

이제 Network Watcher의 인스턴스가 있으니 제공되는 기능에 대해 알아봅니다.

* [토폴로지](network-watcher-topology-overview.md)
* [패킷 캡처](network-watcher-packet-capture-overview.md)
* [IP 흐름 확인](network-watcher-ip-flow-verify-overview.md)
* [다음 홉](network-watcher-next-hop-overview.md)
* [보안 그룹 보기](network-watcher-security-group-view-overview.md)
* [NSG 흐름 로깅](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network 게이트웨이 문제 해결](network-watcher-troubleshoot-overview.md)

Network Watcher 인스턴스가 있으면 가상 머신 내에서 패킷 캡처를 사용하도록 설정할 수 있습니다. 방법을 알아보려면 [경고가 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 참조하세요.
