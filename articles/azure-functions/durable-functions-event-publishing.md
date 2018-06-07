---
title: Azure Event Grid에 게시하는 Durable Functions(미리 보기)
description: Durable Functions에 대한 자동 Azure Event Grid 게시를 구성하는 방법을 알아봅니다.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/20/2018
ms.author: tdykstra
ms.openlocfilehash: 50e517e5719fb102fd91072abe59d3908176278e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Azure Event Grid에 게시하는 Durable Functions(미리 보기)

이 문서에서는 오케스트레이션 수명 주기 이벤트(예: 만들어짐, 완료됨 및 실패)를 사용자 지정 [Azure Event Grid 토픽](https://docs.microsoft.com/en-us/azure/event-grid/overview)에 게시하도록 Azure Durable Functions를 설정하는 방법을 설명합니다. 

이 기능이 유용한 몇 가지 시나리오는 다음과 같습니다.

* **DevOps 시나리오(예: 파란색/녹색 배포)**: [병렬 배포 전략](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments)을 구현하기 전에 실행 중인 작업이 있는지 여부를 알아야 할 수 있습니다.

* **고급 모니터링 및 진단 지원**: 쿼리에 최적화된 외부 저장소(예: SQL 데이터베이스 또는 CosmosDB)에서 오케스트레이션 상태 정보를 추적할 수 있습니다.

* **장기 실행 백그라운드 작업**: 장기 실행 백그라운드 작업에 Durable Functions를 사용하는 경우 이 기능을 사용하면 현재 상태를 알 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Durable Functions 프로젝트에서 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc 이상을 설치합니다.
* [Azure Storage 에뮬레이터](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator)를 설치합니다.
* [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest)을 설치하거나 [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)을 사용합니다.

## <a name="create-a-custom-event-grid-topic"></a>사용자 지정 Event Grid 토픽 만들기

Durable Functions에서 이벤트를 보내기 위한 Event Grid 토픽을 만듭니다. 다음 지침에서는 Azure CLI를 사용하여 토픽을 만드는 방법을 보여 줍니다. PowerShell 또는 Azure Portal을 사용하여 수행하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Event Grid 빠른 시작: 사용자 지정 이벤트 만들기 - PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [Event Grid 빠른 시작: 사용자 지정 이벤트 만들기 - Azure Portal](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

`az group create` 명령을 사용하여 리소스 그룹을 만듭니다. 현재 Event Grid는 일부 지역을 지원하지 않습니다. 지원되는 지역에 대한 정보는 [Event Grid 개요](https://docs.microsoft.com/en-us/azure/event-grid/overview)를 참조하세요. 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

Event Grid 토픽은 이벤트를 게시하는 사용자 정의 엔드포인트를 제공합니다. `<topic_name>`을 토픽의 고유한 이름으로 바꿉니다. 토픽 이름은 DNS 항목이 되므로 고유해야 합니다.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>엔드포인트 및 키 가져오기

토픽의 엔드포인트를 가져옵니다. `<topic_name>`을 선택한 이름으로 바꿉니다.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

토픽 키를 가져옵니다. `<topic_name>`을 선택한 이름으로 바꿉니다.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

이제 토픽에 이벤트를 보낼 수 있습니다.

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid 게시 구성

Durable Functions 프로젝트에서 `host.json` 파일을 찾습니다.

`durableTask` 속성에 `EventGridTopicEndpoint` 및 `EventGridKeySettingName`을 추가합니다.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

* **EventGridTopicEndpoint** - Event Grid 토픽의 엔드포인트입니다.
* **EventGridKeySettingName** - Durable Functions의 응용 프로그램 설정 키입니다. Durable Functions는 값에서 Event Grid 토픽 키를 가져옵니다.

`host.json` 파일이 구성되면 Durable Functions 프로젝트에서 수명 주기 이벤트를 Event Grid 토픽에 보내기 시작합니다. 이는 함수 앱에서 실행하고 로컬로 실행할 때 작동합니다.

함수 앱 및 `local.setting.json`에서 토픽 키에 대한 앱 설정을 지정합니다. 다음 JSON은 로컬 디버깅에 대한 `local.settings.json` 샘플입니다. `<topic_key>`를 토픽 키로 바꿉니다.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

[저장소 에뮬레이터](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator)가 작동하는지 확인합니다. 실행하기 전에 `AzureStorageEmulator.exe clear all` 명령을 실행하는 것이 좋습니다.

## <a name="create-functions-that-listen-for-events"></a>이벤트를 수신 대기하는 함수 만들기

함수 앱을 만듭니다. Event Grid 토픽과 동일한 지역에서 찾는 것이 가장 좋습니다.

### <a name="create-an-event-grid-trigger-function"></a>Event Grid 트리거 함수 만들기

수명 주기 이벤트를 받는 함수를 만듭니다. **사용자 지정 함수**를 선택합니다. 

![사용자 지정 함수 만들기 선택](media/durable-functions-event-publishing/functions-portal.png)

Event Grid 트리거를 선택하고, `C#`을 선택합니다.

![Event Grid 트리거 선택](media/durable-functions-event-publishing/eventgrid-trigger.png)

함수 이름을 입력한 다음, `Create`를 선택합니다.

![Event Grid 트리거 만들기](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

다음 코드가 있는 함수가 만들어집니다. 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

`Add Event Grid Subscription`를 선택합니다. 이 작업은 사용자가 만든 Event Grid 토픽에 대한 Event Grid 구독을 추가합니다. 자세한 내용은 [Azure Event Grid의 개념](https://docs.microsoft.com/en-us/azure/event-grid/concepts)을 참조하세요.

![Event Grid 트리거 링크 선택](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

**토픽 종류**에 대해 `Event Grid Topics`를 선택합니다. Event Grid 토픽에 대해 만든 리소스 그룹을 선택합니다. 그런 다음, Event Grid 토픽의 인스턴스를 선택합니다. `Create`를 누릅니다.

![Event Grid 구독을 만듭니다.](media/durable-functions-event-publishing/eventsubscription.png)

이제 수명 주기 이벤트를 받을 준비가 되었습니다. 

## <a name="create-durable-functions-to-send-the-events"></a>이벤트를 보내는 Durable Functions 만들기

Durable Functions 프로젝트에서 로컬 컴퓨터에 대한 디버깅을 시작합니다.  다음 코드는 Durable Functions에 대한 템플릿 코드와 동일합니다. 이미 로컬 컴퓨터에 `host.json` 및 `local.settings.json`을 구성했습니다. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Postman 또는 브라우저를 통해 `Sample_HttpStart`를 호출하면 Durable Functions에서 수명 주기 이벤트를 보내기 시작합니다. 로컬 디버깅에 대한 엔드포인트는 일반적으로 `http://localhost:7071/api/Sample_HttpStart`입니다.

Azure Portal에서 만든 함수의 로그를 봅니다.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",    
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>이벤트 스키마

다음 목록에서는 수명 주기 이벤트 스키마를 설명합니다.

* **id**: Event Grid 이벤트에 대한 고유 식별자
* **subject**: 이벤트 주체에 대한 경로입니다. `durable/orchestrator/{orchestrationRuntimeStatus}` `{orchestrationRuntimeStatus}`는 `Running`, `Completed`, `Failed` 및 `Terminated`입니다.  
* **data**: Durable Functions 특정 매개 변수
    * **hubName**: [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs) 이름
    * **functionName**: 오케스트레이터 함수 이름
    * **instanceId**: Durable Functions instanceId
    * **reason**: 추적 이벤트와 관련된 추가 데이터입니다. 자세한 내용은 [Durable Functions의 진단(Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)을 참조하세요.
    * **runtimeStatus**: 오케스트레이션 런타임 상태입니다. 실행 중, 완료됨, 실패, 취소됨입니다. 
* **eventType**: "orchestratorEvent"
* **eventTime**: 이벤트 시간(UTC)
* **dataVersion**: 수명 주기 이벤트 스키마의 버전
* **metadataVersion**: 메타데이터의 버전
* **토픽**: Event Grid 토픽 리소스

## <a name="how-to-test-locally"></a>로컬로 테스트하는 방법

로컬로 테스트하려면 [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok)를 사용합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Durable Functions의 인스턴스 관리 알아보기](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Durable Functions의 버전 관리 알아보기](durable-functions-versioning.md)
