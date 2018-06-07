---
title: Azure IoT Hub의 상태 모니터링 | Microsoft Docs
description: Azure Monitor 및 Azure Resource Health를 사용하여 IoT Hub를 모니터링하고 신속하게 문제 진단
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: bf6202b002aaf6d89a30c7c653fdcee00cb50290
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202223"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Azure IoT Hub 상태 모니터링 및 신속한 문제 진단

Azure IoT Hub를 구현하는 기업은 리소스에서 안정적인 성능을 기대합니다. 고객이 계속해서 작업을 면밀하게 검토할 수 있도록 IoT Hub는 [Azure Monitor][lnk-AM] 및 [Azure Resource Health][lnk-ARH]와 완벽하게 통합되어 있습니다. 이러한 두 서비스는 동시에 작동하여 IoT 솔루션을 정상 상태로 유지하는 데 필요한 데이터를 제공합니다. 

Azure Monitor는 모든 Azure 서비스를 모니터링하고 로깅하는 단일 원본입니다. Azure Monitor가 생성하는 로그를 고객이 처리하도록 Log Analytics, Event Hubs 또는 Azure Storage로 보낼 수 있습니다. Azure Monitor의 메트릭 및 진단 설정은 리소스 성능에 대한 실시간 가시성을 제공합니다. 이 문서를 계속 읽고 IoT Hub에 [Azure Monitor를 사용](#use-azure-monitor)하는 방법에 대해 알아보세요. 

Azure Resource Health는 Azure 문제가 리소스에 영향을 줄 때 문제를 진단하고 지원을 받는 데 도움이 됩니다. 개인 설정된 대시보드는 IoT Hub의 현재 및 과거 성능 상태를 제공합니다. 이 문서를 계속 읽고 IoT Hub에 [Azure Resource Health를 사용](#use-azure-resource-health)하는 방법에 대해 알아보세요. 

이러한 두 서비스와의 통합 외에도 Azure IoT Hub는 IoT 리소스의 상태를 이해하는 데 사용할 수 있는 자체적인 메트릭을 제공합니다. 자세한 내용은 [IoT Hub 메트릭 이해][lnk-metrics]를 참조하세요.

## <a name="use-azure-monitor"></a>Azure Monitor 사용

Azure Monitor는 리소스 수준의 진단 정보를 제공합니다. 즉, IoT Hub 내에서 발생하는 작업을 모니터링할 수 있습니다. 

Azure Monitor의 진단 설정은 IoT Hub 작업 모니터를 대체합니다. 현재 작업 모니터링을 사용하는 경우 워크플로를 마이그레이션해야 합니다. 자세한 내용은 [작업 모니터링에서 진단 설정으로 마이그레이션][lnk-migrate]을 참조하세요.

Azure Monitor에서 감시하는 특정 메트릭 및 이벤트에 대해 자세히 알아보려면 [Azure Monitor에서 지원되는 메트릭][lnk-AM-metrics] 및 [Azure 진단 로그에 대해 지원되는 서비스, 스키마 및 범주][lnk-AM-schemas]를 참조하세요.

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>로그 이해

Azure Monitor는 IoT Hub에서 발생하는 여러 작업을 추적합니다. 각 범주에는 해당 범주의 이벤트를 보고하는 방식을 정의하는 스키마가 있습니다. 

#### <a name="connections"></a>연결

연결 범주는 장치가 IoT Hub에 연결되거나 연결이 해제될 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 무단 연결 시도를 식별하고 연결 상태가 좋지 않은 영역에서 장치의 연결이 끊어졌을 때 추적하는 데 유용합니다.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>클라우드-장치 명령

클라우드-장치 명령 범주는 IoT Hub에서 발생하고 클라우드-장치 메시지 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 클라우드-장치 메시지를 보낼 때(예: 권한이 없는 보낸 사람), 클라우드-장치 메시지를 받을 때(예: 전달 수 초과), 그리고 클라우드-장치 메시지 피드백을 받을 때(예: 피드백 만료) 발생하는 오류가 포함됩니다. 이 범주는 클라우드-장치 메시지가 성공적으로 전달된 경우 클라우드-장치 메시지가 부적절하게 처리하는 장치로부터 오류를 포착하지 않습니다.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>장치 ID 작업

장치 ID 작업 범주는 IoT Hub ID 레지스트리의 항목을 만들거나, 업데이트하거나 삭제하려고 할 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 프로비전 시나리오에 유용합니다.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>경로

메시지 라우팅 범주는 IoT Hub에서 감지하는 대로 메시지 경로 평가 및 끝점 상태 중에 발생한 오류를 추적합니다. 이 범주에는 규칙이 "undefined"로 평가되는 경우, IoT Hub가 끝점을 데드로 표시한 경우 및 다른 오류가 끝점에서 수신되는 경우와 같은 이벤트가 포함됩니다. 이 범주는 메시지 자체에 대한 특정 오류(예: 장치 제한 오류)를 포함하지 않습니다. 해당 오류는 "장치 원격 분석" 범주 아래에서 보고됩니다.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>장치 원격 분석

장치 원격 분석 범주는 IoT Hub에서 발생하고 원격 분석 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 원격 분석 이벤트를 보내고(예: 제한) 원격 분석 이벤트를 수신할 때(예: 무단된 판독기) 발생하는 오류가 포함됩니다. 이 범주는 장치 자체에서 실행되는 코드에 의해 발생한 오류를 포착할 수 없습니다.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>파일 업로드 작업

파일 업로드 범주는 IoT Hub에서 발생하고 파일 업로드 기능과 관련된 오류를 추적합니다. 이 범주에는 다음이 포함됩니다.

* SAS URI에서 발생하는 오류(예: 장치가 허브에 완료된 업로드를 알리기 전에 만료되는 경우).
* 장치에 의해 보고된 실패한 업로드.
* IoT Hub 알림 메시지 생성 중 저장소에서 파일을 찾을 수 없는 경우 발생하는 오류.

이 범주는 장치가 저장소로 파일을 업로드하는 동안 직접 발생하는 오류를 검색할 수 없습니다.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>클라우드-장치 쌍 작업

클라우드-장치 쌍 작업 범주는 장치 쌍에서 서비스가 시작한 이벤트를 추적합니다. 이러한 작업에는 쌍 가져오기, 보고된 속성 업데이트, 원하는 속성 구독이 포함될 수 있습니다.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>장치-클라우드 쌍 작업

클라우드-장치 쌍 작업 범주는 장치 쌍에서 장치가 시작한 이벤트를 추적합니다. 이러한 작업에는 쌍 가져오기, 태그 업데이트 또는 대체, 원하는 속성 업데이트 또는 대체가 포함될 수 있습니다. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>쌍 쿼리

쌍 쿼리 범주는 클라우드에서 시작된 장치 쌍에 대한 쿼리 요청을 보고합니다. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>작업 연산

작업 연산 범주는 장치 쌍을 업데이트하는 작업 요청 또는 여러 장치에 대한 직접 메서드를 호출하는 작업 요청에 대해 보고합니다. 이러한 요청은 클라우드에서 시작됩니다. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>직접 메서드

직접 메서드 범주는 개별 장치로 전송되는 요청-응답 상호 작용을 추적합니다. 이러한 요청은 클라우드에서 시작됩니다. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Azure Event Hubs의 로그 읽기

진단 설정을 통해 이벤트 로깅을 설정한 후에는 로그의 정보를 기반으로 조치를 취할 수 있도록 로그를 읽는 응용 프로그램을 만들 수 있습니다. 다음 샘플 코드는 이벤트 허브에서 로그를 검색합니다.

```csharp
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
    static void Main(string[] args) 
    { 
        Console.WriteLine("Monitoring. Press Enter key to exit.\n"); 
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName); 
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds; 
        CancellationTokenSource cts = new CancellationTokenSource(); 
        var tasks = new List<Task>(); 
        foreach (string partition in d2cPartitions) 
        { 
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token)); 
        } 
        Console.ReadLine(); 
        Console.WriteLine("Exiting..."); 
        cts.Cancel(); 
        Task.WaitAll(tasks.ToArray()); 
    } 
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct) 
    { 
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow); 
        while (true) 
        { 
            if (ct.IsCancellationRequested) 
            { 
                await eventHubReceiver.CloseAsync(); 
                break; 
            } 
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10)); 
            if (eventData != null) 
            { 
                string data = Encoding.UTF8.GetString(eventData.GetBytes()); 
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data); 
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Azure Resource Health 사용

Azure Resource Health를 사용하여 IoT Hub가 실행 중인지 모니터링할 수 있습니다. 지역 가동 중단이 IoT Hub의 상태에 영향을 주는지도 알아볼 수 있습니다. Azure IoT Hub의 상태에 대해 구체적으로 이해하려면 [Azure Monitor 사용](#use-azure-monitor)을 읽어 보시기 바랍니다. 

Azure IoT Hub는 지역 수준에서 상태를 표시합니다. IoT Hub에 영향을 주는 지역 가동 중단이 발생하면 상태가 **알 수 없음**으로 표시됩니다. Azure Resource Health가 수행하는 구체적인 상태 검사에 대해 자세히 알아보려면 [Azure Resource Health에서 리소스 유형 및 상태 검사][lnk-ARH-checks]를 참조하세요.

IoT Hub의 상태를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **서비스 상태** > **리소스 상태**로 이동합니다.
1. 드롭다운 상자에서 해당 구독과 **IoT Hub**를 선택합니다.

상태 데이터를 해석하는 방법에 대한 자세한 내용은 [Azure 리소스 상태 개요][lnk-ARH]를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [IoT Hub 메트릭 이해][lnk-metrics]
- [Azure Logic Apps으로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
