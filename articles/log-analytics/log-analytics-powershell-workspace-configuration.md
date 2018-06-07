---
title: PowerShell을 사용하여 Log Analytics 작업 영역 만들기 및 구성 | Microsoft Docs
description: Log Analytics는 온-프레미스 또는 클라우드 인프라의 서버에서 데이터를 사용합니다. Azure 진단에 의해 생성된 경우에 Azure 저장소에서 컴퓨터 데이터를 수집할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6a3f91323a017533d2d012f1e81760396c17a643
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="manage-log-analytics-using-powershell"></a>PowerShell을 사용하여 Log Analytics 관리
[Log Analytics PowerShell cmdlet](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx)을 사용하여 명령줄에서 또는 스크립트의 일부로 Log Analytics의 다양한 기능을 수행할 수 있습니다.  PowerShell을 사용하여 수행할 수 있는 작업의 예:

* 작업 영역 만들기
* 솔루션 추가 또는 제거
* 저장된 검색 가져오기 및 내보내기
* 컴퓨터 그룹 만들기
* Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
* Linux 및 Windows 컴퓨터에서 성능 카운터 수집
* Linux 컴퓨터의 syslog에서 이벤트 수집 
* Windows 이벤트 로그에서 이벤트 수집
* 사용자 지정 이벤트 로그 수집
* Azure 가상 머신에 Log Analytics 에이전트 추가
* Azure 진단을 사용하여 수집된 데이터를 인덱싱하도록 Log Analytics 구성

이 문서에서는 PowerShell에서 수행할 수 있는 몇 가지 기능을 보여 주는 두 가지 코드 샘플을 제공합니다.  다른 기능에 대해서는 [Log Analytics PowerShell Cmdlet 참조](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) 를 참조할 수 있습니다.

> [!NOTE]
> Log Analytics는 이전에 Operational Insights라고 했기 때문에 cmdlet에서는 Operational Insights라고 합니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
이 예제는 AzureRm.OperationalInsights 모듈 버전 2.3.0 이상에서 작동합니다.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기 및 구성
다음 스크립트 샘플에서는 다음 작업의 방법을 보여 줍니다.

1. 작업 영역 만들기
2. 사용 가능한 솔루션 나열
3. 작업 영역에 솔루션 추가
4. 저장된 검색 가져오기
5. 저장된 검색 내보내기
6. 컴퓨터 그룹 만들기
7. Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
8. Linux 컴퓨터에서 논리 디스크 성능 카운터 수집(사용된 Inode 비율, 사용 가능한 MB, 사용된 공간 비율, 초당 디스크 전송, 초당 디스크 읽기, 초당 디스크 쓰기)
9. Linux 컴퓨터에서 syslog 이벤트 수집
10. Windows 컴퓨터에서 응용 프로그램 이벤트 로그의 오류 및 경고 이벤트 수집
11. Windows 컴퓨터에서 사용 가능한 메모리(MB) 성능 카운터 수집
12. 사용자 지정 로그 수집 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Azure 진단을 인덱싱하도록 Log Analytics 구성
에이전트 없이 Azure 리소스를 모니터링할 경우 리소스가 Azure 진단을 사용할 수 있어야 하며 Log Analytics 작업 영역에 쓰도록 구성되어야 합니다. 이러한 방식은 데이터를 Log Analytics에 바로 보내며 데이터를 저장소 계정에 쓸 필요가 없습니다. 지원되는 리소스는 다음과 같습니다.

| 리소스 종류 | 로그 | 메트릭 |
| --- | --- | --- |
| Application Gateway    | 예 | 예 |
| Automation 계정     | 예 | |
| Batch 계정          | 예 | 예 |
| Data Lake Analytics     | 예 | | 
| Data Lake Store         | 예 | |
| 탄력적인 SQL 풀        |     | 예 |
| 이벤트 허브 네임스페이스     |     | 예 |
| IoT Hub                |     | 예 |
| Key Vault               | 예 | |
| Load Balancer          | 예 | |
| Logic Apps              | 예 | 예 |
| 네트워크 보안 그룹 | 예 | |
| Redis Cache             |     | 예 |
| Search 서비스         | 예 | 예 |
| Service Bus 네임스페이스   |     | 예 |
| SQL(v12)               |     | 예 |
| 웹 사이트               |     | 예 |
| 웹 서버 팜        |     | 예 |

사용 가능한 메트릭에 대한 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](../monitoring-and-diagnostics/monitoring-supported-metrics.md)을 참조하세요.

사용 가능한 로그에 대한 자세한 내용은 [진단 로그에 지원되는 서비스 및 스키마](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md)를 참조하세요.

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

위의 cmdlet을 사용하여 다른 구독에 속하는 리소스에서 로그를 수집할 수도 있습니다. 리소스 생성 로그의 ID 및 로그를 보내는 작업 영역의 ID를 모두 제공하기 때문에 cmdlet은 구독 간에 작업이 가능합니다.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>저장소에서 Azure 진단을 인덱싱하도록 Log Analytics 구성
클래식 클라우드 서비스 또는 Service Fabric 클러스터의 실행 중인 인스턴스 내에서 로그 데이터를 수집하려면 먼저 Azure Storage에 데이터를 써야 합니다. 그런 다음 저장소 계정에서 로그를 수집하도록 Log Analytics를 구성합니다. 지원되는 리소스는 다음과 같습니다.

* 기존 Cloud Services(웹 및 작업자 역할)
* Service Fabric 클러스터

아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

1. Log Analytics가 다음 출처에서 오는 데이터를 인덱싱하는 기존 저장소 계정 및 위치 나열
2. 저장소 계정에서 읽을 구성 만들기
3. 새로 만든 구성을 추가 위치에서 오는 데이터를 인덱싱하도록 업데이트
4. 새로 만든 구성 삭제

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

위의 스크립트를 사용하여 다른 구독에 속하는 저장소 계정에서 로그를 수집할 수도 있습니다. 저장소 계정 리소스 ID와 해당 액세스 키를 제공하기 때문에 스크립트는 구독 간에 작업이 가능합니다. 액세스 키를 변경할 때 저장소 정보가 새 키를 갖도록 업데이트해야 합니다.


## <a name="next-steps"></a>다음 단계
* [Log Analytics PowerShell Cmdlet 검토](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) 를 참조하세요.

