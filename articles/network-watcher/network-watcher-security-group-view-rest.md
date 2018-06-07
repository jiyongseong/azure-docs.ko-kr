---
title: "Azure Network Watcher 보안 그룹 보기를 사용하여 네트워크 보안 분석 - REST API | Microsoft Docs"
description: "이 문서에서는 보안 그룹 보기를 사용하여 가상 머신 보안을 분석하기 위해 PowerShell을 사용하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: a2f418fe-f5d2-43ed-8dc3-df0ed2a4d4ac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 0eec45630fe3467db26620787038f6dd5a05cc72
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>REST API를 사용하는 보안 그룹 보기에서 Virtual Machine 보안 분석

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

보안 그룹 보기는 가상 컴퓨터에 적용되는 효과적으로 구성된 네트워크 보안 규칙을 반환합니다. 이 기능은 VM에 구성된 네트워크 보안 그룹 및 규칙을 감사하고 진단하여 트래픽을 올바르게 허용하거나 거부하는 데 유용합니다. 이 문서에서는 REST API를 사용하여 가상 머신에 효과적으로 적용된 보안 규칙을 검색하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 Network Watcher Rest API를 호출하여 가상 머신에 대한 보안 그룹 보기를 가져옵니다. PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 컴퓨터를 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 지정된 가상 머신에 대한 효과적으로 적용된 보안 규칙을 검색합니다.

## <a name="log-in-with-armclient"></a>ARMClient에 로그인

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>가상 머신 검색

다음 스크립트를 실행하여 가상 머신을 반환합니다. 다음 코드에는 변수가 필요합니다.

- **subscriptionId** - **Get-AzureRMSubscription** cmdlet으로 구독 ID도 검색할 수 있습니다.
- **resourceGroupName** - 가상 머신을 포함하는 리소스 그룹의 이름입니다.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

필요한 정보는 다음 예제에 볼 수 있는 것처럼 응답에서 `Microsoft.Compute/virtualMachines` 형식 아래의 **id**입니다.

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>가상 머신에 대한 보안 그룹 보기 가져오기

다음 예제에서는 대상 지정된 가상 컴퓨터의 보안 그룹 보기를 요청합니다. 구성 편차를 검색하기 위해 원본에서 정의한 규칙 및 보안을 비교하는 데 이 예제의 결과를 사용할 수 있습니다.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>응답 보기

다음 샘플은 이전 명령에서 반환한 응답입니다. 결과는 **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** 및 **EffectiveSecurityRules**라는 그룹으로 구분되는 가상 머신에서 효과적으로 적용된 보안 규칙을 모두 표시합니다.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[Network Watcher를 사용하여 NSG(네트워크 보안 그룹) 감사](network-watcher-security-group-view-powershell.md)를 방문하여 네트워크 보안 그룹의 유효성 검사를 자동화하는 방법을 알아봅니다.


