---
title: Azure Monitor 지원 리소스의 최신 메트릭 경고 | Microsoft Docs
description: 최신 Azure 근 실시간 메트릭 경고에서 지원 메트릭 및 로그에 대한 참조입니다.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: c4a4a82eedc41b7690af005faecc1505257183ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778116"
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Azure Portal에서 Azure 서비스에 대한 최신 메트릭 경고
Azure Monitor는 이제 새로운 메트릭 경고 형식을 지원합니다. 최신 경고는 몇 가지 점에서 [클래식 메트릭 경고](insights-alerts-portal.md)와 다릅니다.

- **개선된 대기 시간**: 최신 메트릭 경고는 1분 간격으로 자주 실행할 수 있습니다. 이전 메트릭 경고는 항상 5분 주기로 실행됩니다. 로그를 수집 하는 데 걸리는 시간으로 인해 경고 로그는 1분 이상 지연됩니다. 
- **다차원 메트릭에 대한 지원**: 메트릭의 흥미로운 세그먼트만 모니터링할 수 있도록 차원이 포함된 메트릭에서 경고를 수행할 수 있습니다. 
- **메트릭 조건 세부 제어**: 경고 규칙을 다양하게 정의할 수 있습니다. 최신 경고는 메트릭의 최대, 최소, 평균 및 전체 값을 모니터링하도록 지원합니다. 
- **여러 가지 메트릭의 결합 모니터링**: 단일 규칙을 사용하여 다중 메트릭(현재 최대 2개)을 모니터링할 수 있습니다. 두 메트릭이 지정된 기간 동안 해당 임계값을 위반하면 경고가 트리거됩니다. 
- **향상된 알림 시스템**: 모든 최신 경고는 여러 경고에서 다시 사용할 수 있는 알림 및 작업으로 명명된 그룹인 [작업 그룹](monitoring-action-groups.md)을 사용합니다. 클래식 메트릭 경고 및 이전 Log Analytics 경고는 작업 그룹을 사용하지 않습니다. 
- **로그의 메트릭**(제한된 공용 미리 보기): 이제 Log Analytics로 이동하는 로그 데이터를 추출하고 Azure Monitor 메트릭으로 변환한 다음, 다른 메트릭과 마찬가지로 경고할 수 있습니다. 

Azure Portal에서 새로운 메트릭 경고를 만드는 방법은 [Azure Portal에서 경고 규칙 만들기](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)를 참조하세요. 생성 후에 [Azure Portal에서 경고 관리](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)에 설명된 단계에 따라 경고를 관리할 수 있습니다.


## <a name="portal-powershell-cli-rest-support"></a>포털, PowerShell, CLI, REST 지원
현재는 Azure Portal, [REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) 또는 [Resource Manager 템플릿](monitoring-create-metric-alerts-with-templates.md)에서만 최신 메트릭 경고를 만들 수 있습니다. PowerShell을 사용한 최신 경고 구성이 지원되며, Azure CLI(Azure 명령줄 인터페이스) 2.0이 지원될 예정입니다.

## <a name="metrics-and-dimensions-supported"></a>지원되는 메트릭 및 차원
최신 메트릭 경고는 차원을 사용하는 메트릭에 대한 경고를 지원합니다. 차원을 사용하여 메트릭을 적절한 수준으로 필터링할 수 있습니다. 해당 차원과 함께 지원되는 모든 메트릭을 [Azure Monitor - 메트릭 탐색기(미리 보기)](monitoring-metric-charts.md)에서 탐색하고 시각화할 수 있습니다.

최신 경고에서 지원하는 Azure Monitor 메트릭 원본의 전체 목록은 다음과 같습니다.

|리소스 종류  |지원되는 차원  | 사용 가능한 메트릭|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | 예        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     예   | [Automation 계정](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 해당 없음| [Batch 계정](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    해당 없음     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    해당 없음     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   해당 없음      |[가상 머신 확장 집합](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | 예| [컨테이너 그룹](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| 예| [데이터 팩터리 V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   예     |[데이터 팩터리 V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   해당 없음      |[MySQL용 DB](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    해당 없음     | [PostgreSQL용 DB](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  예      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| 아니오 | [VAULTS](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     해당 없음    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    해당 없음     | [Application Gateway](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | 해당 없음| [DNS 영역](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (표준 SKU 전용)| 예| [부하 분산 장치](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  해당 없음       |[공용 IP 주소](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | 해당 없음 | [Capacities](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   해당 없음      |[Search 서비스](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  예       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    예     | [Storage 계정](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     예    | [Blob 서비스](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [파일 서비스](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [큐 서비스](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) 및 [테이블 서비스](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  해당 없음       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    해당 없음     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces(미리 보기) | 예|[Log Analytics 작업 영역](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>경고에 대한 메트릭인 Log Analytics 로그 

또한 메트릭으로 추출된 인기 있는 Log Analytics 로그에서 최신 메트릭 경고를 로그 미리 보기에서 메트릭의 일부로 사용할 수 있습니다.  
- Windows 및 Linux 컴퓨터에 대한 [성능 카운터](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [에이전트 상태에 대한 하트비트 레코드](../operations-management-suite/oms-solution-agenthealth.md)
- [업데이트 관리](../operations-management-suite/oms-solution-update-management.md) 레코드
 
> [!NOTE]
> 특정 메트릭 및/또는 차원은 선택한 기간에 해당 데이터가 존재할 때만 표시됩니다. 이러한 메트릭은 미국 동부, 미국 중서부 및 유럽 서부의 작업 영역에서 미리 보기에 옵트인한 고객의 경우 사용할 수 있습니다. 이 미리 보기에 참여하려면 [설문 조사](https://aka.ms/MetricLogPreview)를 사용하여 등록하세요.

다음과 같은 Log Analytics 로그 기반 메트릭 원본 목록이 지원됩니다.

메트릭 이름/세부 정보  |지원되는 차원  | 로그 유형  |
|---------|---------|---------|
|Average_Avg. 디스크 초/읽기     |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Avg. 디스크 초/쓰기     |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Current Disk Queue Length   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Disk Reads/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Disk Transfers/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|   Average_% Free Space    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Available MBytes     |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_% Committed Bytes In Use    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Bytes Received/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|  Average_Bytes Sent/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|  Average_Bytes Total/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|  Average_% Processor Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|   Average_Processor Queue Length    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|   Average_% Free Inodes   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Free Space   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Inodes  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Space   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Read Bytes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Reads/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Transfers/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Write Bytes/sec   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Writes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Megabytes |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Logical Disk Bytes/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Available Memory |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Available Swap Space |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Memory  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Swap Space  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Available MBytes Memory    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Available MBytes Swap  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Page Reads/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Page Writes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Pages/sec  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Used MBytes Swap Space |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Used Memory MBytes |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Bytes Transmitted    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Bytes Received   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Bytes    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Packets Transmitted  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Packets Received |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Rx Errors    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Tx Errors    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Collisions   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Avg. 디스크 초/읽기 |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Avg. 디스크 초/전송 |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Avg. 디스크 초/쓰기    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Physical Disk Bytes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Pct Privileged Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Pct User Time  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Used Memory kBytes |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Virtual Shared Memory  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% DPC Time |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Idle Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Interrupt Time   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% IO Wait Time |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Nice Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Privileged Time  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Processor Time   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% User Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Physical Memory   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Space in Paging Files |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Virtual Memory    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Processes  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Size Stored In Paging Files    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Uptime |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Users  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Heartbeat  |     예 - Computer, OSType, Version 및 SourceComputerId    |   하트비트 레코드 |
|    주 지역에서 |     예 - Computer, Product, Classification, UpdateState, Optional 및 Approved    |   업데이트 관리 |



## <a name="payload-schema"></a>페이로드 스키마

POST 작업에는 적절히 구성된 [작업 그룹](monitoring-action-groups.md)이 사용될 때 모든 최신 메트릭 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다.

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

* 새 [경고 환경](monitoring-overview-unified-alerts.md)에 대해 자세히 알아봅니다.
* [Azure의 로그 경고](monitor-alerts-unified-log.md)에 대해 알아봅니다.
* [Azure의 경고](monitoring-overview-alerts.md)에 대해 알아봅니다.
