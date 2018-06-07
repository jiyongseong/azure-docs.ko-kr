---
title: Azure Network Watcher 보안 그룹 보기를 사용하여 NSG 감사 자동화 | Microsoft Docs
description: 이 페이지에서는 네트워크 보안 그룹의 감사를 구성하는 방법에 대한 설명을 제공합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 227ea446a75c167be27128b15de1d3c216e6856d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363379"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Azure Network Watcher 보안 그룹 보기를 사용하여 NSG 감사 자동화

고객은 흔히 인프라의 보안 상태를 확인하는 문제에 직면합니다. 이 문제는 Azure의 VM에 대해서도 마찬가지입니다. 적용된 NSG(네트워크 보안 그룹) 규칙에 따라 유사한 보안 프로필을 포함하는 것이 중요합니다. 보안 그룹 보기를 사용하여 이제 NSG 내의 VM에 적용된 규칙 목록을 가져올 수 있습니다. 골든 NSG 보안 프로필을 정의하고 매주 보안 그룹 보기를 시작하며 출력을 골든 프로필과 비교하여 보고서를 만들 수 있습니다. 이 방법으로 미리 정해진 보안 프로필을 준수하지 않는 모든 VM을 쉽게 파악할 수 있습니다.

네트워크 보안 그룹에 대해 잘 모르는 경우 [네트워크 보안 개요](../virtual-network/security-overview.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 가상 머신에서 반환된 보안 그룹 보기 결과를 알려진 적합한 기준선과 비교합니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 컴퓨터를 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 가상 컴퓨터에 대한 보안 그룹 보기를 가져옵니다.

이 시나리오에서는 다음을 수행합니다.

- 알려진 적합한 규칙 집합 검색
- Rest API로 가상 머신 검색
- 가상 머신에 대한 보안 그룹 보기 가져오기
- 응답 평가

## <a name="retrieve-rule-set"></a>규칙 집합 검색

이 예제의 첫 번째 단계는 기존 기준선으로 작업하는 것입니다. 다음 예제는 이 예제에 대한 기준선으로 사용된 `Get-AzureRmNetworkSecurityGroup` cmdlet을 사용하여 기존 네트워크 보안 그룹에서 추출한 json입니다.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>규칙 집합을 PowerShell 개체로 변환

이 단계에서는 이 예제에 대한 네트워크 보안 그룹에 있는 것으로 예상되는 규칙을 사용하여 이전에 만든 json 파일을 읽고 있습니다.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Network Watcher 검색

다음 단계는 Network Watcher 인스턴스를 검색하는 것입니다. `$networkWatcher` 변수는 `AzureRmNetworkWatcherSecurityGroupView` cmdlet으로 전달됩니다.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>VM 확인

`Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet을 실행하려면 가상 머신이 필요합니다. 다음 예제는 VM 개체를 가져옵니다.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>보안 그룹 보기 검색

다음 단계에서는 보안 그룹 보기 결과 검색합니다. 이 결과를 이전에 표시된 "기준선" json과 비교합니다.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>결과 분석

응답은 네트워크 인터페이스별로 그룹화됩니다. 반환된 다양한 형식의 규칙 및 기본 보안 규칙이 적용됩니다. 결과는 서브넷 또는 가상 NIC에서 적용된 방식에 따라 세분화됩니다.

다음 PowerShell 스크립트는 보안 그룹 보기의 결과를 기존 NSG 출력과 비교합니다. 다음 예제는 결과를 `Compare-Object` cmdlet과 비교하는 방법을 보여 주는 간단한 예입니다.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

다음 예제는 결과입니다. 첫 번째 규칙 집합에 있었던 규칙 중 두 가지는 비교에 표시되지 않는 것을 볼 수 있습니다.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>다음 단계

설정이 변경된 경우 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)를 참조하여 문제가 될 수 있는 네트워크 보안 그룹 및 보안 규칙을 추적합니다.













