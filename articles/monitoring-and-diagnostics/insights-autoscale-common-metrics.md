---
title: "Azure Monitor 자동 크기 조정 공용 메트릭 | Microsoft Docs"
description: "클라우드 서비스, 가상 컴퓨터 및 웹앱의 자동 크기 조정에 일반적으로 사용되는 메트릭에 대해 알아봅니다."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 189b2a13-01c8-4aca-afd5-90711903ca59
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: ancav
ms.openlocfilehash: 240a230d09680672ccd5316470a87d047fab9fd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor 자동 크기 조정 공용 메트릭
Azure Monitor 자동 크기 조정을 사용하여 원격 분석 데이터(메트릭)에 따라 실행 중인 인스턴트 수를 늘리거나 줄일 수 있습니다. 이 문서에서는 사용하고자 하는 공용 메트릭에 대해 설명합니다. Cloud Services 및 서버 팜용 Azure Portal에서 크기를 조정할 리소스의 메트릭을 선택할 수 있습니다. 그러나 크기를 조정하기 위해 여러 리소스에서 임의 메트릭을 선택할 수도 있습니다.

Azure Monitor 자동 크기 조정은 [가상 컴퓨터 확장 집합](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/) 및 [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)에만 적용됩니다. 다른 Azure 서비스에는 다른 크기 조정 방법이 사용됩니다.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Resource Manager 기반 VM용 메트릭 계산
기본적으로 Resource Manager 기반 Virtual Machines 및 Virtual Machine Scale Sets는 기본(호스트 수준) 메트릭을 내보냅니다. 또한 Azure VM 및 VMSS용 진단 데이터 수집을 구성하면 Azure 진단 확장은 게스트 OS 성능 카운터(일반적으로 "게스트 OS 메트릭"이라고 함)도 내보냅니다.  자동 크기 조정 규칙에서 이러한 모든 메트릭을 사용합니다.

`Get MetricDefinitions` API/PoSH/CLI를 사용하여 VMSS 리소스에 사용할 수 있는 메트릭을 볼 수 있습니다.

VM 규모 집합을 사용 중인데 특정 메트릭이 목록에 표시되지 않는 경우, 이는 진단 확장에서 *사용하지 않도록 설정*되었을 수 있습니다.

특정 메트릭이 원하는 빈도로 샘플링 또는 전송되고 있지 않은 경우 진단 구성을 업데이트할 수 있습니다.

위 경우 중 하나가 해당되면 PowerShell에 대한 [PowerShell을 사용하여 Windows를 실행하는 가상 컴퓨터에서 Azure 진단을 사용하도록 설정](../virtual-machines/windows/ps-extensions-diagnostics.md)을 검토하여 메트릭을 사용하도록 Azure VM 진단 확장을 구성 및 업데이트합니다. 이 문서에는 샘플 진단 구성 파일도 포함되어 있습니다.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Resource Manager 기반 Windows 및 Linux VM용 호스트 메트릭
기본적으로 Windows 및 Linux 인스턴스 모두 Azure VM 및 VMSS용으로 다음 호스트 수준 메트릭을 내보냅니다. 이러한 메트릭은 Azure VM을 설명하지만 게스트 VM에 설치된 에이전트를 통하는 대신 Azure VM 호스트에서 수집됩니다. 자동 크기 조정 규칙에서 이러한 메트릭을 사용할 수 있습니다.

- [Resource Manager 기반 Windows 및 Linux VM용 호스트 메트릭](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Resource Manager 기반 Windows 및 Linux VM Scale Sets용 호스트 메트릭](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>게스트 OS 메트릭 Resource Manager 기반 Windows VM
Azure에서 VM을 만들 때 진단 확장을 사용하여 진단을 사용하도록 설정합니다. 진단 확장을 사용하여 VM 내에서 가져온 메트릭 집합을 내보냅니다. 즉, 기본적으로 내보내지 않도록 메트릭의 자동 크기 조정을 해제할 수 있습니다.

PowerShell에서 다음 명령을 사용하여 메트릭 목록을 생성할 수 있습니다.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

다음 메트릭에 대한 경고를 만들 수 있습니다.

| 메트릭 이름 | 단위 |
| --- | --- |
| \Processor(_Total)\% 프로세서 시간 |백분율 |
| \Processor(_Total)\% 시스템 시간 |백분율 |
| \Processor(_Total)\% 사용자 시간 |백분율 |
| \Processor Information(_Total)\Processor Frequency |개수 |
| \System\Processes |개수 |
| \Process(_Total)\Thread Count |개수 |
| \Process(_Total)\Handle Count |개수 |
| \Memory\% 사용 중인 커밋된 바이트 |백분율 |
| \Memory\Available Bytes |바이트 |
| \Memory\Committed Bytes |바이트 |
| \Memory\Commit Limit |바이트 |
| \Memory\Pool Paged Bytes |바이트 |
| \Memory\Pool Nonpaged Bytes |바이트 |
| \PhysicalDisk(_Total)\% 디스크 시간 |백분율 |
| \PhysicalDisk(_Total)\% 디스크 읽기 시간 |백분율 |
| \PhysicalDisk(_Total)\% 디스크 쓰기 시간 |백분율 |
| \PhysicalDisk(_Total)\디스크 전송/초 |초당 개수 |
| \PhysicalDisk(_Total)\Disk Reads/sec |초당 개수 |
| \PhysicalDisk(_Total)\Disk Writes/sec |초당 개수 |
| \PhysicalDisk(_Total)\Disk Bytes/sec |초당 바이트 수 |
| \PhysicalDisk(_Total)\Disk Read Bytes/sec |초당 바이트 수 |
| \PhysicalDisk(_Total)\Disk Write Bytes/sec |초당 바이트 수 |
| \PhysicalDisk(_Total)\Avg. 디스크 큐 길이 |개수 |
| \PhysicalDisk(_Total)\Avg. 디스크 읽기 큐 길이 |개수 |
| \PhysicalDisk(_Total)\Avg. 디스크 쓰기 큐 길이 |개수 |
| \LogicalDisk(_Total)\% 사용 가능한 공간 |백분율 |
| \LogicalDisk(_Total)\Free Megabytes |개수 |

### <a name="guest-os-metrics-linux-vms"></a>게스트 OS 메트릭 Linux VM
Azure에서 VM을 만들 때 진단 확장을 사용하여 기본적으로 진단을 사용하도록 설정합니다.

PowerShell에서 다음 명령을 사용하여 메트릭 목록을 생성할 수 있습니다.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 다음 메트릭에 대한 경고를 만들 수 있습니다.

| 메트릭 이름 | 단위 |
| --- | --- |
| \Memory\AvailableMemory |바이트 |
| \Memory\PercentAvailableMemory |백분율 |
| \Memory\UsedMemory |바이트 |
| \Memory\PercentUsedMemory |백분율 |
| \Memory\PercentUsedByCache |백분율 |
| \Memory\PagesPerSec |초당 개수 |
| \Memory\PagesReadPerSec |초당 개수 |
| \Memory\PagesWrittenPerSec |초당 개수 |
| \Memory\AvailableSwap |바이트 |
| \Memory\PercentAvailableSwap |백분율 |
| \Memory\UsedSwap |바이트 |
| \Memory\PercentUsedSwap |백분율 |
| \Processor\PercentIdleTime |백분율 |
| \Processor\PercentUserTime |백분율 |
| \Processor\PercentNiceTime |백분율 |
| \Processor\PercentPrivilegedTime |백분율 |
| \Processor\PercentInterruptTime |백분율 |
| \Processor\PercentDPCTime |백분율 |
| \Processor\PercentProcessorTime |백분율 |
| \Processor\PercentIOWaitTime |백분율 |
| \PhysicalDisk\BytesPerSecond |초당 바이트 수 |
| \PhysicalDisk\ReadBytesPerSecond |초당 바이트 수 |
| \PhysicalDisk\WriteBytesPerSecond |초당 바이트 수 |
| \PhysicalDisk\TransfersPerSecond |초당 개수 |
| \PhysicalDisk\ReadsPerSecond |초당 개수 |
| \PhysicalDisk\WritesPerSecond |초당 개수 |
| \PhysicalDisk\AverageReadTime |초 |
| \PhysicalDisk\AverageWriteTime |초 |
| \PhysicalDisk\AverageTransferTime |초 |
| \PhysicalDisk\AverageDiskQueueLength |개수 |
| \NetworkInterface\BytesTransmitted |바이트 |
| \NetworkInterface\BytesReceived |바이트 |
| \NetworkInterface\PacketsTransmitted |개수 |
| \NetworkInterface\PacketsReceived |개수 |
| \NetworkInterface\BytesTotal |바이트 |
| \NetworkInterface\TotalRxErrors |개수 |
| \NetworkInterface\TotalTxErrors |개수 |
| \NetworkInterface\TotalCollisions |개수 |

## <a name="commonly-used-web-server-farm-metrics"></a>일반적으로 사용되는 웹(서버 팜) 메트릭
Http 큐 길이와 같이 공용 웹 서버 메트릭을 기반으로 자동 크기 조정을 수행할 수도 있습니다. 메트릭 이름은 **HttpQueueLength**입니다.  다음 섹션에는 사용 가능한 서버 팜(웹앱) 메트릭이 나열되어 있습니다.

### <a name="web-apps-metrics"></a>웹앱 메트릭
PowerShell에서 다음 명령을 사용하여 웹앱 메트릭 목록을 생성할 수 있습니다.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

다음 메트릭에 대한 경고를 만들거나 크기를 조정할 수 있습니다.

| 메트릭 이름 | 단위 |
| --- | --- |
| CpuPercentage |백분율 |
| MemoryPercentage |백분율 |
| DiskQueueLength |개수 |
| HttpQueueLength |개수 |
| BytesReceived |바이트 |
| BytesSent |바이트 |

## <a name="commonly-used-storage-metrics"></a>일반적으로 사용되는 저장소 메트릭
저장소 큐의 메시지 수인 저장소 큐 길이의 크기를 조정할 수 있습니다. 저장소 큐 길이는 특수한 메트릭이고 임계값은 인스턴스당 메시지 수입니다. 예를 들어 인스턴스가 두 개이고 임계값이 100으로 설정된 경우 큐에서 총 메시지 수가 200일 때 크기가 조정됨을 의미합니다. 인스턴스당 메시지는 100개, 120개 및 80개, 또는 최대 200개 이상을 추가하는 임의의 기타 조합이 될 수 있습니다.

Azure Portal의 **설정** 블레이드에서 이 설정을 구성합니다. VM Scale Sets의 경우 *metricName*을 *ApproximateMessageCount*로 사용하고 저장소 큐 ID를 *metricResourceUri*로 전달하도록 Resource Manager 템플릿에서 자동 크기 조정 설정을 업데이트할 수 있습니다.

예를 들어 클래식 저장소 계정을 사용하면 자동 크기 조정 설정 metricTrigger는 다음을 포함합니다.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

(클래식이 아닌) 저장소 계정의 경우 metricTrigger는 다음을 포함합니다.

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>자주 사용되는 서비스 버스 메트릭
서비스 버스 큐의 메시지 수인 서비스 버스 큐 길이의 크기를 조정할 수 있습니다. Service Bus 큐 길이는 특수한 메트릭이고 임계값은 인스턴스당 메시지 수입니다. 예를 들어 인스턴스가 두 개이고 임계값이 100으로 설정된 경우 큐에서 총 메시지 수가 200일 때 크기가 조정됨을 의미합니다. 인스턴스당 메시지는 100개, 120개 및 80개, 또는 최대 200개 이상을 추가하는 임의의 기타 조합이 될 수 있습니다.

VM Scale Sets의 경우 *metricName*을 *ApproximateMessageCount*로 사용하고 저장소 큐 ID를 *metricResourceUri*로 전달하도록 Resource Manager 템플릿에서 자동 크기 조정 설정을 업데이트할 수 있습니다.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> 서비스 버스의 경우 리소스 그룹 개념이 없지만 Azure Resource Manager가 지역마다 기본 리소스 그룹을 만듭니다. 리소스 그룹은 일반적으로 'Default-ServiceBus-[region]' 형식입니다. 예를 들어 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast' 등입니다.
>
>
