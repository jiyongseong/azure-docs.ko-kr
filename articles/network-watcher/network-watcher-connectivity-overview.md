---
title: Azure Network Watcher 연결 문제 해결 소개 | Microsoft Docs
description: 이 페이지는 Network Watcher 연결 문제 해결 기능에 대한 개요를 제공합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 0268c7e54aa82df12243f98fd72de836fbc82070
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Azure Network Watcher의 연결 문제 해결 소개

Network Watcher의 연결 문제 해결 기능은 가상 머신에서 VM(가상 머신), FQDN(정규화된 도메인 이름), URI 또는 IPv4 주소로 직접 TCP 연결을 확인하는 기능을 제공합니다. 네트워크 시나리오는 복잡하며, Azure에서 제공하는 네트워크 보안 그룹, 방화벽, 사용자 정의 경로 및 리소스를 사용하여 구현됩니다. 복잡한 구성은 연결 문제 해결을 어렵게 만듭니다. Network Watcher는 연결 문제를 찾고 감지하는 시간을 줄이는 데 도움이 됩니다. 반환된 결과를 통해 연결 문제가 플랫폼으로 인한 것인지 아니면 사용자 구성 문제인지에 대한 통찰력을 얻을 수 있습니다. 연결은 [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md) 및 [REST API](network-watcher-connectivity-rest.md)로 확인할 수 있습니다.

> [!IMPORTANT]
> 연결 문제 해결은 문제를 해결하는 VM에 `AzureNetworkWatcherExtension` VM 확장이 설치되어 있어야 합니다. Windows VM에서 확장을 설치하려면 [Windows용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)을 방문하고 Linux VM인 경우 [Linux용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)을 방문하세요. 확장은 대상 엔드포인트에서 필요하지 않습니다.

## <a name="response"></a>response

다음 테이블에 연결 문제 해결이 실행 완료되었을 때 반환되는 속성이 나와 있습니다.

|자산  |설명  |
|---------|---------|
|ConnectionStatus     | 연결 확인의 상태입니다. 가능한 결과는 **연결 가능** 및 **연결 불가능**입니다.        |
|AvgLatencyInMs     | 연결 확인 중 평균 대기 시간(밀리초)입니다. (검사 상태가 연결 가능인 경우에만 표시됩니다.)        |
|MinLatencyInMs     | 연결 확인 중 최소 대기 시간(밀리초)입니다. (검사 상태가 연결 가능인 경우에만 표시됩니다.)        |
|MaxLatencyInMs     | 연결 확인 중 최대 대기 시간(밀리초)입니다. (검사 상태가 연결 가능인 경우에만 표시됩니다.)        |
|ProbesSent     | 검사 중 보낸 프로브의 수입니다. 최대값은 100입니다.        |
|ProbesFailed     | 검사 중 실패한 프로브의 수입니다. 최대값은 100입니다.        |
|Hops     | 원본에서 대상까지 홉 단위 경로입니다.        |
|Hops[].Type     | 리소스의 형식입니다. 가능한 값은 **Source**, **VirtualAppliance**, **VnetLocal** 및 **Internet**입니다.        |
|Hops[].Id | 홉의 고유 식별자입니다.|
|Hops[].Address | 홉의 IP 주소입니다.|
|Hops[].ResourceId | 홉이 Azure 리소스인 경우 홉의 ResourceID입니다. 인터넷 리소스인 경우 ResourceID는 **Internet**입니다. |
|Hops[].NextHopIds | 수행된 다음 홉의 고유 식별자입니다.|
|Hops[].Issues | 해당 홉에서 검사 중 발생한 문제의 컬렉션입니다. 문제가 없는 경우 값은 비어 있습니다.|
|Hops[].Issues[].Origin | 현재 홉에서 문제가 발생한 경우입니다. 가능한 값은 다음과 같습니다.<br/> **인바운드** - 문제가 이전 홉부터 현재 홉까지 링크에 있습니다.<br/>**아웃바운드** - 문제가 현재 홉부터 다음 홉까지 링크에 있습니다.<br/>**로컬** - 문제가 현재 홉에 있습니다.|
|Hops[].Issues[].Severity | 감지된 문제의 심각도입니다. 가능한 값은 **Error** 및 **Warning**입니다. |
|Hops[].Issues[].Type |발견된 문제의 형식입니다. 가능한 값은 다음과 같습니다. <br/>**CPU**<br/>**메모리**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |발견된 문제에 관한 세부 정보입니다.|
|Hops[].Issues[].Context[].key |반환된 키 값 쌍의 키입니다.|
|Hops[].Issues[].Context[].value |반환된 키 값 쌍의 값입니다.|

다음은 홉에서 발견된 문제의 예입니다.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>오류 형식

연결 문제 해결은 연결에 대한 오류 형식을 반환합니다. 다음 테이블에 반환된 현재 오류 형식의 목록이 나와 있습니다.

|유형  |설명  |
|---------|---------|
|CPU     | 높은 CPU 사용률       |
|메모리     | 높은 메모리 사용률       |
|GuestFirewall     | 가상 컴퓨터 방화벽 구성으로 인해 트래픽이 차단되었습니다.        |
|DNSResolution     | 대상 주소에 대한 DNS 확인에 실패했습니다.        |
|NetworkSecurityRule    | 트래픽이 NSG 규칙에 의해 차단되었습니다(규칙이 반환됨).        |
|UserDefinedRoute|트래픽이 사용자 정의 또는 시스템 경로로 인해 삭제되었습니다. |

### <a name="next-steps"></a>다음 단계

[Azure Portal](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md) 또는 [REST API](network-watcher-connectivity-rest.md)를 사용하여 연결 문제를 해결하는 방법을 알아봅니다.