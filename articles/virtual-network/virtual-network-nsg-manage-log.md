---
title: "NSG에 대한 작업, 이벤트 및 카운터 모니터링 | Microsoft Docs"
description: "NSG에 대한 카운터, 이벤트 및 작업 로깅을 사용하도록 설정하는 방법에 대해 알아보기"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 6beb9ae1b64e27df0a4eefefd592c7850efc7d2d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>NSG(네트워크 보안 그룹)에 대한 로그 분석

NSG에 대한 다음 진단 로그 범주를 활성화할 수 있습니다.

* **이벤트:** VM 및 MAC 주소 기반 인스턴스 역할에 적용된 NSG 규칙에 대한 항목을 포함합니다. 이러한 규칙에 대한 상태는 60초마다 수집됩니다.
* **규칙 카운터:** 트래픽을 허용하거나 거부하기 위해 각 NSG 규칙이 적용된 횟수에 대한 항목을 포함합니다.

> [!NOTE]
> 진단 로그는 Azure Resource Manager 배포 모델을 통해 배포된 NSG에 대해서만 사용할 수 있습니다. 클래식 배포 모델을 통해 배포된 NSG에 대해 진단 로깅을 사용할 수 없습니다. 두 모델의 이해를 돕기 위해 [Azure 배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

활동 로깅(이전의 감사 또는 작업 로그)은 Azure 배포 모델 중 하나를 통해 만든 NSG에 대해 기본적으로 사용됩니다. 활동 로그의 NSG 내에서 완료된 작업을 확인하려면 다음과 같은 리소스 유형을 포함하는 항목을 찾습니다. 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

활동 로그에 대한 자세한 내용을 보려면 [Azure 활동 로그 개요](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) 문서를 확인합니다. 

## <a name="enable-diagnostic-logging"></a>진단 로깅 사용

진단 로깅은 데이터를 수집하려는 *각* NSG에 대해 활성화되어야 합니다. [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서는 진단 로그를 보낼 수 있는 위치를 설명합니다. 기존 NSG가 없는 경우 [네트워크 보안 그룹 만들기](virtual-networks-create-nsg-arm-pportal.md) 문서의 단계를 완료하여 NSG를 만듭니다. 다음 방법 중 하나를 사용하여 NSG 진단 로깅을 활성화할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

로깅을 활성화하는 데 포털을 사용하려면 [포털](https://portal.azure.com)에 로그인합니다. **모든 서비스**를 클릭한 다음, *네트워크 보안 그룹*을 입력합니다. 로깅을 활성화하려는 NSG를 선택합니다. [포털에서 진단 로그 사용](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) 문서의 비 계산 리소스에 대한 지침을 따릅니다. **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter** 또는 두 범주의 로그를 선택합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 로깅을 활성화하려면 [PowerShell을 통해 진단 로그 사용](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) 문서의 지침을 따릅니다. 문서에서 명령을 입력하기 전에 다음 정보를 평가합니다.

- 다음 [텍스트]를 적절하게 대체한 다음 명령 `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`을 입력하여 `-ResourceId` 매개 변수에 사용할 값을 확인할 수 있습니다. 명령의 ID 출력은 */subscriptions/[Subscription Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]*과 유사합니다.
- 로그 범주에서 데이터를 수집하려는 경우 문서에서 명령의 끝에 `-Categories [category]`를 추가합니다. 범주는 *NetworkSecurityGroupEvent* 또는 *NetworkSecurityGroupRuleCounter*입니다. `-Categories` 매개 변수를 사용하지 않는 경우 데이터 컬렉션은 두 로그 범주에 대해 활성화됩니다.

### <a name="azure-command-line-interface-cli"></a>Azure CLI(명령줄 인터페이스)

CLI를 사용하여 로깅을 활성화하려면 [CLI를 통해 진단 로그 사용](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) 문서의 지침을 따릅니다. 문서에서 명령을 입력하기 전에 다음 정보를 평가합니다.

- 다음 [텍스트]를 적절하게 대체한 다음 명령 `azure network nsg show [resource-group-name] [nsg-name]`을 입력하여 `-ResourceId` 매개 변수에 사용할 값을 확인할 수 있습니다. 명령의 ID 출력은 */subscriptions/[Subscription Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]*과 유사합니다.
- 로그 범주에서 데이터를 수집하려는 경우 문서에서 명령의 끝에 `-Categories [category]`를 추가합니다. 범주는 *NetworkSecurityGroupEvent* 또는 *NetworkSecurityGroupRuleCounter*입니다. `-Categories` 매개 변수를 사용하지 않는 경우 데이터 컬렉션은 두 로그 범주에 대해 활성화됩니다.

## <a name="logged-data"></a>기록된 데이터

JSON 형식 데이터는 두 로그에 기록됩니다. 각 로그 형식에 대해 기록된 특정 데이터는 다음 섹션에 나열됩니다.

### <a name="event-log"></a>이벤트 로그
이 로그는 MAC 주소에 따라 VM 및 클라우드 서비스 역할 인스턴스에 적용될 NSG 규칙에 대한 정보를 포함합니다. 다음 예제 데이터는 각 이벤트에 대해 기록됩니다.

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>규칙 카운터 로그

이 로그는 리소스에 적용되는 각 규칙에 대한 정보를 포함합니다. 다음 예제 데이터는 규칙이 적용될 때마다 기록됩니다.

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>로그 보기 및 분석

활동 로그 데이터를 보는 방법을 알아보려면 [Azure 활동 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서를 확인합니다. 진단 로그 데이터를 보는 방법을 알아보려면 [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서를 확인합니다. Log Analytics에 진단 데이터를 보내는 경우 향상된 통찰력을 위해 [Azure 네트워크 보안 그룹 분석](../log-analytics/log-analytics-azure-networking-analytics.md)(미리 보기) 관리 솔루션을 사용할 수 있습니다. 
