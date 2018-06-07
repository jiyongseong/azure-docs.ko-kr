---
title: Azure Network Watcher를 사용하여 패킷 캡처 관리 - Azure CLI 1.0 | Microsoft Docs
description: 이 페이지에서는 Azure CLI 1.0을 사용하여 Network Watcher의 패킷 캡처 기능을 관리하는 방법에 대해 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c74d1a94971495f7cd5f5bed42b33869fa9710d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185388"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-10"></a>Azure CLI 1.0에서 Azure Network Watcher를 사용하여 패킷 캡처 관리

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher 패킷 캡처를 사용하면 가상 컴퓨터 간에 트래픽을 추적하는 캡처 세션을 만들 수 있습니다. 원하는 트래픽만 캡처할 수 있도록 캡처 세션에 대 한 필터가 제공됩니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버깅할 수 있습니다. 이 기능은 원격으로 패킷 캡처를 트리거할 수 있게 하여 원하는 컴퓨터에서 수동으로 패킷 캡처를 실행하는 부담을 줄이고 시간을 단축합니다.

이 문서에서는 Windows, Mac 및 Linux에 사용할 수 있는 플랫폼 간 Azure CLI 1.0을 사용합니다.

이 문서에서는 패킷 캡처를 위해 현재 사용할 수 있는 여러 관리 태스크를 설명합니다.

- [**패킷 캡처 시작**](#start-a-packet-capture)
- [**패킷 캡처 중지**](#stop-a-packet-capture)
- [**패킷 캡처 삭제**](#delete-a-packet-capture)
- [**패킷 캡처 다운로드**](#download-a-packet-capture)

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 사용자에게 다음 리소스가 있는 것으로 가정합니다.

- 패킷 캡처를 만들려는 영역의 Network Watcher 인스턴스
- 패킷 캡처 확장을 사용하는 Virtual Machine

> [!IMPORTANT]
> 패킷 캡처에는 가상 머신에서 실행되는 에이전트가 필요합니다. 에이전트는 확장으로 설치됩니다. VM 확장에 대한 지침은 [Virtual Machine 확장 및 기능](../virtual-machines/windows/extensions-features.md)을 참조하세요.

## <a name="install-vm-extension"></a>VM 확장 설치

### <a name="step-1"></a>1단계

ph x="1" /> cmdlet을 실행하여 게스트 가상 머신에 패킷 캡처 에이전트를 설치합니다.

Windows Virtual Machines의 경우:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

Linux 가상 머신의 경우:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>2단계

에이전트가 설치되어 있는지 확인하려면 `vm extension get` cmdlet을 실행하고 리소스 그룹과 가상 머신 이름을 전달합니다. 결과 목록을 확인하여 에이전트가 설치되어 있는지 확인합니다.

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

다음 샘플은 실행 중인 `azure vm extension get`에서 응답의 예제입니다.

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                        Version  State
data:    ------------------------------  -----------------------     -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentWindows  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>패킷 캡처 시작

이전 단계가 완료되면 패킷 캡처 에이전트는 가상 머신에 설치됩니다.

### <a name="step-1"></a>1단계

다음 단계는 Network Watcher 인스턴스를 검색하는 것입니다. 이 변수는 4단계에서 `network watcher show` cmdlet으로 전달됩니다.

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>2단계

저장소 계정을 검색합니다. 이 저장소 계정은 패킷 캡처 파일을 저장하는 데 사용됩니다.

```azurecli
azure storage account list
```

### <a name="step-3"></a>3단계

패킷 캡처에 의해 저장되는 데이터를 제한하는 데 필터를 사용할 수 있습니다. 다음 예제에서는 몇 가지 필터를 사용하여 패킷 캡처를 설정합니다.  처음 세 개의 필터는 로컬 IP 10.0.0.3에서 대상 포트 20, 80 및 443으로 나가는 TCP 트래픽을 수집합니다.  마지막 필터는 UDP 트래픽만을 수집합니다.

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

패킷 캡처를 위해 여러 필터를 정의할 수 있습니다. 복잡한 필터 구조를 사용하는 경우 필터를 json 파일로 사용하여 구문 오류를 방지하는 것이 좋습니다. 예를 들어, "-f" 대신 "-r" 플래그를 사용하고 다음 필터를 포함하는 json 파일의 위치를 전달합니다.

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


다음 예제는 `network watcher packet-capture create` cmdlet을 실행하는 예상된 출력입니다.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>패킷 캡처 가져오기

`network watcher packet-capture show` cmdlet을 실행하고 현재 실행 중이거나 완료된 패킷 캡처의 상태를 검색합니다.

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

다음 예제는 `network watcher packet-capture show` cmdlet의 출력입니다. 다음 예제는 캡처를 완료한 이후입니다. PacketCaptureStatus 값은 TimeExceeded의 StopReason과 함께 중지됨입니다. 이 값은 패킷 캡처가 성공했으며 해당 시간을 실행했음을 보여 줍니다.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>패킷 캡처 중지

`network watcher packet-capture stop` cmdlet을 실행하여 캡처 세션이 진행 중인 경우 중지됩니다.

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> cmdlet은 현재 실행 중인 캡처 세션 또는 이미 중지된 기존 세션에서 실행되는 경우 응답을 반환하지 않습니다.

## <a name="delete-a-packet-capture"></a>패킷 캡처 삭제

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> 패킷 캡처를 삭제하면 저장소 계정에서 파일을 삭제하지 않습니다.

## <a name="download-a-packet-capture"></a>패킷 캡처 다운로드

패킷 캡처 세션이 완료되면 캡처 파일을 Blob Storage 또는 VM의 로컬 파일에 업로드할 수 있습니다. 패킷 캡처의 저장 위치는 세션 생성 시 정의됩니다. 저장소 계정에 저장되는 이러한 캡처 파일에 액세스하는 편리한 도구는 Microsoft Azure Storage 탐색기이며 http://storageexplorer.com/에서 다운로드할 수 있습니다.

저장소 계정이 지정되어 있으면 패킷 캡처 파일은 다음 위치에서 저장소 계정에 저장됩니다.

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>다음 단계

[경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 확인하여 가상 머신 경고로 패킷 캡처를 자동화하는 방법을 알아봅니다.

[IP 흐름 확인 확인](diagnose-vm-network-traffic-filtering-problem.md)을 방문하여 특정 트래픽이 VM에서 허용되는지 알아봅니다.

<!-- Image references -->
