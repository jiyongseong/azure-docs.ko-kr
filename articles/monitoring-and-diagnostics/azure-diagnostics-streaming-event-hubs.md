---
title: "Event Hubs를 사용하여 실행 부하 과다 경로에서 Azure 진단 데이터 스트리밍 | Microsoft 문서"
description: "일반적인 시나리오에 대한 지침을 포함하여 Event Hubs로 Azure 진단을 완벽하게 구성."
services: event-hubs
documentationcenter: na
author: rboucher
manager: carmonm
editor: 
ms.assetid: edeebaac-1c47-4b43-9687-f28e7e1e446a
ms.service: monitoring-and-diagnostics
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: robb
ms.openlocfilehash: ca0dd96389a605ed8bf34af81eb4d75bef581338
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Event Hubs를 사용하여 실행 부하 과다 경로에서 Azure 진단 데이터 스트리밍
Azure 진단에서는 클라우드 서비스 VM(가상 컴퓨터)에서 메트릭 및 로그를 수집하고 결과를 Azure Storage로 전송하는 유연한 방법을 제공합니다. 2016년 3월(SDK 2.9)부터 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)를 사용하여 데이터 원본을 사용자 지정하고 몇 초 만에 실행 부하 과다 경로 데이터를 전송할 수 있는 진단을 보낼 수 있습니다.

지원되는 데이터 유형은 다음과 같습니다.

* Windows (ETW) 이벤트에 대한 이벤트 추적
* 성능 카운터
* Windows 이벤트 로그
* 응용 프로그램 로그
* Azure 진단 인프라 로그

이 문서에서는 Event Hubs에 Azure 진단을 구성하는 방법을 완벽하게 보여 줍니다. 다음과 같은 일반적인 시나리오에 대한 지침도 제공됩니다.

* Event Hubs에 전송된 로그 및 메트릭을 사용자 지정하는 방법
* 각 환경에서 구성을 변경하는 방법
* Event Hubs 스트림 데이터를 보는 방법
* 연결 문제를 해결하는 방법  

## <a name="prerequisites"></a>필수 조건
Azure 진단에서 데이터를 수신하는 Event Hubs는 Azure SDK 2.9 및 해당 Visual Studio용 Azure 도구에서 시작하는 Cloud Services, VM, 가상 컴퓨터 확장 집합 및 Service Fabric에서 지원됩니다.

* Azure 진단 확장 1.6(기본적으로[Azure SDK for .NET 2.9 이상](https://azure.microsoft.com/downloads/) 대상)
* [Visual Studio 2013 이상](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* *.wadcfgx* 파일과 다음 방법 중 하나를 사용하는 응용 프로그램에서 Azure 진단의 기존 구성은 다음과 같습니다.
  * Visual Studio: [Azure Cloud Services 및 Virtual Machines에서 진단 구성](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Windows PowerShell: [PowerShell을 사용하여 Azure Cloud Services에서 진단 사용](../cloud-services/cloud-services-diagnostics-powershell.md)
* 항목별로 프로비전되는 Event Hubs 네임스페이스([Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) 참조)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Event Hubs 싱크에 Azure 진단 연결
기본적으로 Azure 진단은 항상 Azure Storage 계정에 로그 및 메트릭을 전송합니다. 응용 프로그램은 *.wadcfgx* 파일의 **PublicConfig** / **WadCfg** 요소에 새로운 **Sinks** 섹션을 추가하여 Event Hubs에 데이터를 전송할 수도 있습니다. Visual Studio에서 *.wadcfgx* 파일은 **클라우드 서비스 프로젝트** > **역할** > **(RoleName)** > **diagnostics.wadcfgx** 파일이라는 경로에 저장됩니다.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

이 예제에서 이벤트 허브 URL은 이벤트 허브의 정규화된 네임스페이스(Event Hubs 네임스페이스 + “/” + 이벤트 허브 이름)로 설정됩니다.  

이벤트 허브 URL은 Event Hubs 대시보드의 [Azure Portal](http://go.microsoft.com/fwlink/?LinkID=213885)에 표시됩니다.  

**싱크** 이름의 경우 같은 값이 구성 파일 전체에서 일관되게 사용되고 있다면 유효한 문자열로 설정할 수 있습니다.

> [!NOTE]
> 이 섹션에서 구성된 *applicationInsights* 와 같은 추가 싱크가 있을 수 있습니다. 각 싱크가 **PrivateConfig** 섹션에서도 선언되어 있는 경우 Azure 진단을 통해 하나 이상의 싱크를 정의할 수 있습니다.  
>
>

또한, Event Hubs 싱크도 **.wadcfgx** 구성 파일의 *PrivateConfig* 섹션에서 선언되고 정의되어야 합니다.

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

`SharedAccessKeyName` 값은 **Event Hubs** 네임스페이스에 정의된 SAS(공유 액세스 서명) 키 및 정책과 일치해야 합니다. [Azure Portal](https://portal.azure.com)에서 Event Hubs 대시보드로 이동하여 **구성** 탭을 클릭하고 *보내기* 권한이 있는 명명된 정책(예: "SendRule")을 설정합니다. 또한 **StorageAccount**도 **PrivateConfig**에서 선언되어 있습니다. 값이 작동 중인 경우 변경할 필요가 없습니다. 이 예제에서는 값을 비워 둡니다. 이는 다운스트림 자산이 값을 설정한다는 의미입니다. 예를 들어 *ServiceConfiguration.Cloud.cscfg* 환경 구성 파일은 환경에 적절한 이름 및 키를 설정합니다.  

> [!WARNING]
> Event Hubs SAS 키는 *.wadcfgx* 파일에 일반 텍스트로 저장되어 있습니다. 이 키는 때로 소스 코드 제어에서 발견되거나 빌드 서버에서 자산으로 사용할 수 있는 경우가 있으므로 적절하게 보호해야 합니다. 악의적인 사용자가 SAS 키를 이벤트 허브에 작성하기만 하고 수신하거나 관리하지 않을 수 있으므로 여기서 *보내기 전용* 권한으로 사용하는 것이 좋습니다.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Azure 진단을 구성하여 Event Hubs에 로그 및 메트릭 전송
이전에 설명한 대로 모든 기본 및 사용자 지정 진단 데이터(예: 메트릭 및 로그)는 구성된 간격으로 Azure Storage로 자동 전송됩니다. Event Hubs 및 추가 싱크를 사용하여 이벤트 허브로 전송될 계층 구조에서 루트 또는 리프 노드를 지정할 수 있습니다. 여기에는 ETW 이벤트, 성능 카운터, Windows 이벤트 로그 및 응용 프로그램 로그가 포함되어 있습니다.   

얼마나 많은 데이터 요소를 실제로 Event Hubs로 전송해야 하는지 고려하는 것이 중요합니다. 일반적으로 개발자는 신속하게 사용하고 해석해야 하는 대기 시간이 짧은 실행 부하 과다 경로 데이터를 전송합니다. 예는 경고를 모니터링하고 규칙을 자동 크기 조정하는 시스템입니다. 개발자는 대체 분석 또는 검색 저장소(예: Azure Stream Analytics, ElasticSearch, 사용자 지정 모니터링 시스템 또는 즐겨찾는 타사 모니터링 시스템)를 구성할 수도 있습니다.

다음은 몇 가지 구성 예입니다.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

위의 예제에서는 싱크가 계층 구조에서 부모 **PerformanceCounters** 노드에 적용됩니다. 즉 모든 자식 **PerformanceCounters**를 Event Hubs로 보냅니다.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

이전 예제에서 싱크는 단 세 개의 카운터(**대기 중인 요청**, **거부된 요청** 및 **% 프로세서 시간**)에만 적용되었습니다.  

다음 예제에서는 개발자가 전송될 데이터 양을 이 서비스의 상태에 사용되는 중요 메트릭으로 제한하는 방법을 보여 줍니다.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

이 예제에서 싱크는 로그에 적용되며 오류 수준 추적으로만 필터링됩니다.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Cloud Services 응용 프로그램과 진단 구성 배포 및 업데이트
Visual Studio에서는 응용 프로그램 및 Event Hubs 싱크 구성을 배포하는 가장 쉬운 방법을 제공합니다. 파일을 보고 편집하려면 Visual Studio에서 *.wadcfgx* 파일을 열고 편집하고 저장합니다. 경로는 **클라우드 서비스 프로젝트** > **역할** > **(RoleName)** > **diagnostics.wadcfgx**입니다.  

이 시점에서 Visual Studio의 모든 배포 및 배포 업데이트 작업, Visual Studio Team System 및 MSBuild에 기반하고 **/t:publish** 대상을 사용하는 모든 명령 또는 스크립트에는 패키징 프로세스에 있는 *.wadcfgx* 가 포함됩니다. 또한 배포 및 업데이트는 VM에서 적절한 Azure 진단 에이전트 확장을 사용하여 파일을 Azure에 배포합니다.

응용 프로그램 및 Azure 진단 구성을 배포한 후에 이벤트 허브의 대시보드에서 즉시 작업을 확인하게 됩니다. 이것은 사용자가 선택한 분석 도구 또는 수신기 클라이언트에서 실행 부하 과다 경로 데이터를 볼 준비가 되었다는 것을 나타냅니다.  

다음 그림에서 Event Hubs 대시보드는 오후 11시 이후에 시작하는 이벤트 허브에 진단 데이터를 정상적으로 보내는 작업을 보여 줍니다. 이 때 응용 프로그램이 업데이트된 *.wadcfgx* 파일과 함께 배포되고 싱크가 올바르게 구성됩니다.

![][0]  

> [!NOTE]
> Azure 진단 구성 파일(.wadcfgx)을 업데이트할 경우 Visual Studio 게시 또는 Windows PowerShell 스크립트를 사용하여 구성 뿐만 아니라 전체 응용 프로그램에 대한 업데이트를 푸시하는 것이 좋습니다.  
>
>

## <a name="view-hot-path-data"></a>실행 부하 과다 경로 데이터 보기
이전에 설명한 대로 Event Hubs 데이터를 수신하고 처리하는 많은 사용 사례가 있습니다.

한 가지 간단한 접근 방법은 이벤트 허브를 수신하고 출력 스트림을 인쇄하기 위한 작은 테스트 콘솔 응용 프로그램을 만드는 것입니다. [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) 문서에서 자세히 설명하는 다음 코드를 콘솔 응용 프로그램에 배치할 수 있습니다.  

콘솔 응용 프로그램에는 [이벤트 프로세서 호스트 NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)가 포함되어 있어야 합니다.  

**기본** 함수에서 꺾쇠 괄호로 묶인 값을 리소스에 사용할 값으로 바꾸십시오.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs 싱크 문제 해결
* 이벤트 허브가 들어오거나 나가는 이벤트 활동을 예상대로 표시하지 않습니다.

    이벤트 허브가 성공적으로 프로비저닝되었는지 확인합니다. **.wadcfgx** 의 *PrivateConfig* 섹션에 있는 모든 연결 정보는 포털에서 보이는 리소스의 값과 일치해야 합니다. 포털에 정의된 SAS 정책(예에서는 "SendRule")이 있고 *보내기* 권한이 부여되도록 해야 합니다.  
* 업데이트 이후에 이벤트 허브는 들어오거나 나가는 이벤트 작업을 더 이상 표시하지 않습니다.

    우선 이벤트 허브 및 구성 정보가 이전에 설명한 대로 정확한지를 확인합니다. 때로는 배포 업데이트에서 **PrivateConfig** 가 다시 설정됩니다. 권장되는 해결 방법은 프로젝트에서 *.wadcfgx* 에 모든 변경 사항을 적용한 다음 전체 응용 프로그램 업데이트를 푸시하는 것입니다. 불가능한 경우 진단 업데이트가 SAS 키를 포함하여 전체 **PrivateConfig** 를 푸시하도록 해야 합니다.  
* 제안된 방법을 시도했지만 이벤트 허브가 여전히 작동하지 않습니다.

    Azure 진단 자체에 대한 로그 및 오류가 포함된 Azure Storage 테이블( **WADDiagnosticInfrastructureLogsTable**)을 살펴봅니다. 한 가지 옵션은 [Azure Storage 탐색기](http://www.storageexplorer.com) 등의 도구를 사용하여 이 Storage 계정에 연결하고 이 테이블을 본 후 지난 24시간 동안의 타임스탬프에 대한 쿼리를 추가하는 것입니다. 이 도구를 사용하여 .csv 파일을 내보내고 Microsoft Excel과 같은 응용 프로그램에서 열 수 있습니다. Excel를 통해 어떤 오류가 보고되는지 확인하는 **EventHubs**와 같은 전화 카드 문자열을 쉽게 검색할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
• [Event Hubs에 대해 자세히 알아보기](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>부록: Azure 진단 구성 파일(.wadcfgx) 예제 완료
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

이 예제에 대한 보조 *ServiceConfiguration.Cloud.cscfg* 는 다음과 같습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

가상 컴퓨터에 대한 Json 기반 설정은 다음과 같습니다.
```JSON
"settings": {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}


"protectedSettings": {
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](../event-hubs/event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](../event-hubs/event-hubs-create.md)
* [Event Hubs FAQ](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
