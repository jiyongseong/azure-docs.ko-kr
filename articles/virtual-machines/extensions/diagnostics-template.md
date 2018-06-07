---
title: Azure Virtual Machines에 모니터링 및 진단 추가 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 새로운 Windows 가상 머신과 Azure 진단 확장을 만드는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 792a3401c483327eb7fb9fcd88039bc09025b3ef
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944955"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Windows VM 및 Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 사용
Azure Diagnostics Extension은 Windows 기반 Azure 가상 머신에 모니터링 및 진단 기능을 제공합니다. 확장을 Azure Resource Manager 템플릿에 속하도록 포함시켜서 가상 머신에서 이러한 기능을 사용하도록 설정할 수 있습니다. 가상 머신 템플릿의 일부로 확장을 포함시키는 것과 관련된 자세한 내용은 [VM 확장을 사용하여 Azure 리소스 관리자 템플릿 작성](../windows/template-description.md#extensions) 을 참조하세요. 이 문서는 Azure 진단 확장을 Windows 가상 머신 템플릿에 추가하는 방법을 설명합니다.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Azure 진단 확장을 가상 컴퓨터 정의에 추가
Windows 가상 머신에서 진단 확장을 사용하도록 설정하려면 진단 확장을 Resource Manager 템플릿에 VM 리소스로 추가해야 합니다.

간단한 리소스 관리자를 기반으로 하는 Virtual Machine의 경우 확장 구성을 가상 머신에 대한 *resources* 배열로 추가합니다. 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

가상 머신의 리소스 노드 아래에 확장을 구성하는 대신 템플릿의 루트 리소스 노드에 확장 구성을 추가하는 것도 일반적인 방법입니다. 이런 방식의 경우 *name* 및 *type* 값을 통해 확장과 가상 머신 간의 계층적인 관계를 명시적으로 지정해야 합니다. 예:  

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

확장은 가상 머신과 항상 연결되므로, 가상 머신의 리소스 노드 아래에 직접적으로 확장을 정의하거나 기본 수준에서 확장을 정의하고 계층적인 명명 규칙을 사용하여 가상 머신과 확장을 연결할 수 있습니다.

Virtual Machine Scale Sets 확장 구성은 *VirtualMachineProfile*의 *extensionProfile* 속성에 지정됩니다.

*publisher* 속성의 값이 **Microsoft.Azure.Diagnostics**이고 *type* 속성의 값이 **IaaSDiagnostics**이면, Azure 진단 확장이 고유하게 식별됩니다.

*name* 속성의 값은 리소스 그룹에서 확장을 참조하는 데 사용될 수 있습니다. 이 값을 구체적으로 **Microsoft.Insights.VMDiagnosticsSettings**로 설정하면 Azure Portal에서 해당 속성을 쉽게 식별하여 모니터링 차트를 올바르게 표시할 수 있습니다.

*typeHandlerVersion* 은 사용할 확장의 버전을 지정합니다. *autoUpgradeMinorVersion* 부 버전을 **true** 로 설정하면 사용 가능한 최신 부 버전 확장이 제공됩니다. 새로운 기능과 버그 수정을 모두 포함하는 최신의 진단 확장을 사용하려면 항상 *autoUpgradeMinorVersion* 을 **true** 로 설정하는 것이 좋습니다. 

*settings* 요소는 설정하고 확장에서 다시 읽어올 수 있는(공용 구성으로 참조되기도 하는) 확장에 대한 구성 속성을 포함합니다. *xmlcfg* 속성은 진단 에이전트에 의해 수집되는 진단 로그, 성능 카운터 등에 대한 XML 기반 구성을 포함합니다. XML 스키마 자체에 대한 자세한 내용은 [진단 구성 스키마](https://msdn.microsoft.com/library/azure/dn782207.aspx) 를 참조하세요. 실제 XML 구성은 Azure 리소스 관리자 템플릿에 변수로 저장한 후 연결하여 base64로 인코딩하여 *xmlcfg*에 대한 값을 설정하는 것이 일반적인 방식입니다. 변수에 xml을 저장하는 방법에 대한 자세한 내용은 [진단 구성 변수](#diagnostics-configuration-variables) 섹션을 참조하세요. *storageAccount* 속성은 진단 데이터가 전송되는 저장소 계정의 이름을 지정합니다. 

*protectedSettings* 의 속성은(개인 구성으로 참조되기도 하는) 설정할 수 있지만 설정된 후에는 다시 읽어올 수 없습니다. *protectedSettings*는 쓰기 전용이므로 진단 데이터를 기록하는 저장소 계정 키와 같은 중요한 비밀을 저장하는 데 유용합니다.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>진단 저장소 계정을 매개 변수로 지정
위의 진단 저장소 JSON 코드 조각은 *existingdiagnosticsStorageAccountName* 및 *existingdiagnosticsStorageResourceGroup*이라는 두 가지 매개 변수를 사용하여 진단 데이터가 저장되는 진단 저장소 계정을 지정합니다. 진단 저장소 계정을 매개 변수로 지정하면 다양한 환경에서 진단 저장소 계정을 간편하게 변경할 수 있습니다. 예를 들어, 테스트와 프로덕션 배포에 서로 다른 진단 저장소 계정을 사용할 수 있습니다.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }        
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
      }
}
```

가상 머신에 대한 리소스 그룹에 진단 저장소 계정을 지정하는 것보다는 리소스 그룹별로 진단 저장소 계정을 지정하는 것이 가장 바람직합니다. 리소스 그룹은 자체적인 수명을 갖는 배포 단위라고 간주할 수 있으며, 가상 머신은 배포되었다가 구성 업데이트가 새로 생성되면 다시 배포될 수 있지만, 이렇게 가상 머신이 배포되더라도 동일한 저장소 계정에 진단 데이터를 계속 저장하기를 바라는 경우가 일반적입니다. 다른 리소스에 저장소 계정을 지정해 두면, 저장소 계정은 다양한 가상 머신 배포로부터 데이터를 받아들이기 때문에 다양한 버전에서 손쉽게 문제를 해결하는 것이 가능해집니다.

> [!NOTE]
> Visual Studio에서 Windows 가상 머신 템플릿을 만드는 경우 기본 저장소 계정이 가상 머신 VHD가 업로드되는 동일한 저장소 계정을 사용하도록 설정할 수 있습니다. 이것은 VM 초기 설정을 간소화하기 위한 것입니다. 매개 변수로 전달될 수 있는 다른 저장소 계정을 사용하려면 템플릿을 리팩터링합니다. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>진단 구성 변수
앞의 진단 확장 JSON 코드 조각은 진단 저장소에 대한 저장소 계정 키 생성을 간소화하기 위해서 *accountid* 변수를 정의합니다.   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

진단 확장의 *xmlcfg* 속성은 함께 연결되는 여러 개의 변수를 사용하여 정의됩니다. 이러한 변수의 값은 XML에 있기 때문에 JSON 변수를 설정할 때 올바르게 이스케이프되어야 합니다.

다음 예제에서는 일부 Windows 이벤트 로그 및 진단 인프라 로그와 함께 표준 시스템 수준 성능 카운터를 수집하는 진단 구성 XML을 설명합니다. 올바르게 이스케이프되고 형식이 지정되었으므로, 템플릿의 변수 섹션으로 구성을 바로 붙여넣을 수 있습니다. 사람이 읽기에 좀 더 편한 구성 XML의 예를 보려면 [진단 구성 스키마](https://msdn.microsoft.com/library/azure/dn782207.aspx) 를 참조하세요.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

위의 구성에서 Metrics 정의 XML 노드는 앞서 XML에서 *PerformanceCounter* 노드에 정의한 성능 카운터가 집계되고 저장되는 방식을 정의하기 때문에 중요한 구성 요소입니다. 

> [!IMPORTANT]
> 이러한 메트릭은 Azure 포털에서 모니터링 차트 및 경고를 실행합니다.  Azure 포털의 VM 모니터링 데이터를 확인하려는 경우 VM에 대한 진단 구성에 *resourceID* 및 **MetricAggregation**이 있는 **메트릭** 노드가 포함되어 있어야 합니다. 
> 
> 

다음 예제에서는 메트릭 정의에 대한 XML을 보여줍니다. 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*resourceID* 속성은 구독 내의 가상 머신을 고유하게 식별합니다. 구독 및 배포하는 리소스 그룹을 기반으로 템플릿에서 자동으로 값을 업데이트할 수 있도록 subscription() 및 resourceGroup() 함수를 사용해야 합니다.

루프에서 여러 가상 머신을 만드는 경우에는 개별 VM을 정확하게 구분할 수 있도록 copyIndex() 함수로 *resourceID* 값을 채워야 합니다. 다음과 같이 *xmlCfg* 값을 업데이트하여 이것을 지원할 수 있습니다.  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

MetricAggregation의 *PT1M* 및 *PT1H* 값은 각각 1분간의 집계와 1시간의 집계를 나타냅니다.

## <a name="wadmetrics-tables-in-storage"></a>저장소의 WADMetrics 테이블
위의 Metrics 구성은 다음과 같은 명명 규칙으로 진단 저장소 계정에 테이블을 생성합니다.

* **WADMetrics**: 모든 WADMetrics 테이블에 대한 표준 접두사입니다.
* **PT1H** 또는 **PT1M**: 1시간 또는 1분 동안 집계된 데이터가 테이블에 포함되어 있음을 나타냅니다.
* **P10D**: 테이블이 데이터 수집을 시작한 시점부터 시작하여 10일 동안의 데이터를 포함할 것임을 나타냅니다.
* **V2S**: 문자열 상수입니다.
* **yyyymmdd**: 테이블이 데이터 수집을 시작한 날짜입니다.

예제: *WADMetricsPT1HP10DV2S20151108*은 2015년 11월 11일부터 10일간 1시간 넘게 수집한 메트릭 데이터를 포함합니다.    

각각의 WADMetrics 테이블은 다음 열을 포함합니다.

* **PartitionKey**: partitionkey는 VM 리소스를 고유하게 식별하기 위해 *resourceID* 값을 기준으로 생성됩니다. 예: 002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: `<Descending time tick>:<Performance Counter Name>` 형식을 따릅니다. 감소하는 시간 틱 계산식은 최대 시간 틱 빼기 집계 기간이 시작된 시간입니다. 예를 들어 샘플 기간이 2015년 11월 10일 00:00시(UTC)에 시작되는 경우의 계산식은 `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`입니다. memory available bytes 성능 카운터의 행 키는 `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`입니다.
* **counterName**: 성능 카운터의 이름입니다. 이것은 XML config에 정의된 *counterSpecifier* 와 일치합니다.
* **Maximum**: 집계 기간 동안 성능 카운터의 최대 값입니다.
* **Minimum**: 집계 기간 동안 성능 카운터의 최소 값입니다.
* **Total**: 집계 기간 동안 보고된 모든 성능 카운터 값의 합계입니다.
* **Count**: 성능 카운터에 대해 보고된 값의 총 개수입니다.
* **Average**: 집계 기간 동안 성능 카운터의 평균(합계/개수) 값입니다.

## <a name="next-steps"></a>다음 단계
* 진단 확장을 포함하는 Windows 가상 머신의 전체 샘플 템플릿은 [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)을 참조하세요.   
* [Azure PowerShell](../windows/ps-template.md) 또는 [Azure 명령줄](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하여 Azure Resource Manager 템플릿 배포
* [Azure 리소스 관리자 템플릿 작성](../../resource-group-authoring-templates.md)
