---
title: Azure Virtual Network 게이트웨이 및 연결 문제 해결 - Azure CLI 1.0 | Microsoft Docs
description: 이 페이지에서는 Azure Network Watcher 문제 해결 Azure CLI 1.0을 사용하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 778b1c52720ad0583c57182d5d09db0d765be52f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-10"></a>Azure Network Watcher Azure CLI 1.0을 사용하여 Virtual Network 게이트웨이 및 연결 문제 해결

> [!div class="op_single_selector"]
> - [포털](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher는 Azure에서 네트워크 리소스를 이해하는 데 관련된 다양한 기능을 제공합니다. 이러한 기능 중 하나는 리소스 문제 해결입니다. 리소스 문제 해결은 포털, PowerShell, CLI 또는 REST API를 통해 호출할 수 있습니다. Network Watcher가 호출되면 Virtual Network 게이트웨이 또는 연결의 상태를 검사하거나 해당 결과를 반환합니다.

이 문서에서는 Windows, Mac 및 Linux에 사용할 수 있는 플랫폼 간 Azure CLI 1.0을 사용합니다. 

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다.

지원되는 게이트웨이 유형 목록을 보려면 [지원되는 게이트웨이 유형](/network-watcher-troubleshoot-overview.md#supported-gateway-types)을 방문하세요.

## <a name="overview"></a>개요

리소스 문제 해결은 Virtual Network 게이트웨이 및 연결에 발생한 문제를 해결하는 기능을 제공합니다. 리소스 문제 해결을 요청하는 경우 로그를 쿼리하고 검사합니다. 검사가 완료되면 결과가 반환됩니다. 리소스 문제 해결 요청은 장기 실행 요청이며 결과를 반환하는 데 몇 분이 걸릴 수 있습니다. 문제를 해결하는 로그는 지정된 저장소 계정의 컨테이너에 저장됩니다.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Virtual Network 게이트웨이 연결 검색

이 예제에서 리소스 문제 해결은 연결에서 실행됩니다. Virtual Network 게이트웨이를 전달할 수도 있습니다. 다음 cmdlet은 리소스 그룹에서 VPN 연결을 나열합니다.

```azurecli
azure network vpn-connection list -g resourceGroupName
```

구독에서 연결을 참조하는 명령을 실행할 수도 있습니다.

```azurecli
azure network vpn-connection list -s subscription
```

연결의 이름이 있으면 해당 리소스 ID를 가져오기 위해 이 명령을 실행할 수 있습니다.

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기

리소스 문제 해결은 리소스의 상태에 대한 데이터를 반환하고 검토할 저장소 계정에 로그를 저장합니다. 이 단계에서는 저장소 계정을 만듭니다. 기존 저장소 계정이 있는 경우 사용할 수 있습니다.

1. 저장소 계정 만들기

    ```azurecli
    azure storage account create -n storageAccountName -l location -g resourceGroupName
    ```

1. 저장소 계정 키 가져오기

    ```azurecli
    azure storage account keys list storageAccountName -g resourcegroupName
    ```

1. 컨테이너 만들기

    ```azurecli
    azure storage container create --account-name storageAccountName -g resourcegroupName --account-key {storageAccountKey} --container logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Network Watcher 리소스 문제 해결 실행

`network watcher troubleshoot` cmdlet을 사용하여 리소스의 문제를 해결합니다. 리소스 그룹, Network Watcher의 이름, 연결의 ID, 저장소 계정의 ID 및 Blob에 대한 경로에 cmdlet을 전달하여 문제 해결 결과를 저장합니다.

```azurecli
azure network watcher troubleshoot -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

cmdlet을 실행하면 Network Watcher는 리소스를 검토하여 상태를 확인합니다. 셸에 결과를 반환하고 지정된 저장소 계정에 결과 로그를 저장합니다.

## <a name="understanding-the-results"></a>결과 이해

작업 텍스트에서는 문제를 해결하는 방법에 대한 일반적인 지침을 제공합니다. 문제에 대한 조치를 취할 수 있는 경우 링크는 추가 설명서와 함께 제공됩니다. 추가 지침이 없는 경우에 응답은 지원 사례를 열 URL을 제공합니다.  응답의 속성 및 포함된 항목에 대한 자세한 내용은 [Network Watcher 문제 해결 개요](network-watcher-troubleshoot-overview.md)를 방문하세요.

Azure Storage 계정에서 파일을 다운로드하는 방법에 대한 지침은 [.NET을 사용하여 Azure Blob Storage 시작](../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요. 사용할 수 있는 다른 도구는 저장소 탐색기입니다. 저장소 탐색기에 대한 자세한 내용은 여기에 있는 [저장소 탐색기](http://storageexplorer.com/) 링크에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

VPN 연결을 중지하도록 설정이 변경된 경우 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)를 참조하여 문제가 될 수 있는 네트워크 보안 그룹 및 보안 규칙을 추적합니다.
