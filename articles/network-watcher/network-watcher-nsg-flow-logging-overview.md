---
title: Azure Network Watcher를 사용하여 네트워크 보안 그룹에 대한 흐름 로깅 소개 | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher의 NSG 흐름 로그 기능을 사용하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c6a24fbca37d6aa1d775a70c708a139dfb70b813
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182428"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>네트워크 보안 그룹에 대한 흐름 로깅 소개

NSG(네트워크 보안 그룹) 흐름 로그는 NSG를 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다. 흐름 로그는 json 형식으로 작성되고 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC(네트워크 인터페이스), 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)를 보여 줍니다.

![흐름 로그 개요](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

흐름 로그는 NSG를 대상으로 하지만 다른 로그와 동일하게 표시되지 않습니다. 흐름 로그는 저장소 계정 내에만 저장되며 다음 예제와 같이 로깅 경로를 따릅니다.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

다른 로그에서 보듯이 흐름 로그에 동일한 보존 정책을 적용합니다. 1일에서 365일까지 로그 보존 정책을 설정할 수 있습니다. 보존 정책을 설정하지 않으면 로그는 계속 유지됩니다.

## <a name="log-file"></a>로그 파일

흐름 로그에는 다음 속성이 포함됩니다.

* **시간** - 이벤트가 로그된 시간
* **systemId** - 네트워크 보안 그룹 리소스 ID
* **범주** - 이벤트의 범주입니다. 범주는 항상 **NetworkSecurityGroupFlowEvent**입니다.
* **resourceid** - NSG의 리소스 ID
* **operationName** - 항상 NetworkSecurityGroupFlowEvents
* **속성** - 흐름의 속성 컬렉션
    * **버전** - 흐름 로그 이벤트 스키마의 버전 번호
    * **흐름** - 흐름의 컬렉션입니다. 이 속성에는 서로 다른 규칙에 대한 여러 항목이 있습니다.
        * **규칙** - 흐름이 나열된 규칙
            * **흐름** - 흐름의 컬렉션
                * **mac** - 흐름이 수집된 VM에 대한 NIC의 MAC 주소
                * **flowTuples** - 쉼표로 구분된 형식에서 흐름 튜플에 대한 여러 속성을 포함하는 문자열
                    * **타임스탬프** - 이 값은 흐름이 UNIX EPOCH 형식에서 발생하는 경우의 타임스탬프입니다.
                    * **원본 IP** - 원본 IP
                    * **대상 IP** - 대상 IP
                    * **원본 포트** - 원본 포트
                    * **대상 포트** - 대상 포트
                    * **프로토콜** - 흐름의 프로토콜입니다. 유효한 값은 TCP에 대해서 **T**이며 UDP에 대해서 **U**입니다.
                    * **트래픽 흐름** - 트래픽 흐름의 방향입니다. 유효한 값은 인바운드에 대해서 **I**이며 아웃바운드에 대해서 **O**입니다.
                    * **트래픽** - 트래픽이 허용되었는지 거부되었는지 여부입니다. 유효한 값은 허용에 대해 **A**이며 거부에 대해 **D**입니다.

다음 텍스트는 흐름 로그의 예제입니다. 이전 섹션에 설명된 속성 목록을 따르는 여러 레코드를 볼 수 있습니다.

> [!NOTE]
> **flowTuples* 속성의 값은 쉼표로 구분된 목록입니다.
 
```json
{
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>다음 단계

- 흐름 로그를 사용하도록 설정하는 방법을 알아보려면 [NSG 흐름 로깅을 사용하도록 설정](network-watcher-nsg-flow-logging-portal.md)을 참조하세요.
- NSG 로깅에 대해 알아보려면 [NSG(네트워크 보안 그룹)에 대한 Log Analytics](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 참조하세요.
- 트래픽이 허용되거나 VM에서 거부할지 여부를 결정하려면 [VM 네트워크 트래픽 필터 문제 진단](diagnose-vm-network-traffic-filtering-problem.md)을 참조하세요.